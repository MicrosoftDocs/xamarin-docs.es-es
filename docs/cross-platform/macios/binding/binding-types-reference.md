---
title: Guía de referencia de tipos de enlace
description: En esta guía de referencia se describen los distintos atributos y conceptos necesarios para entender C# cuándo crear enlaces a las bibliotecas de Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: ef94c90cec11c374b24ddfb159674adb468e72de
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765788"
---
# <a name="binding-types-reference-guide"></a>Guía de referencia de tipos de enlace

En este documento se describe la lista de atributos que puede usar para anotar los archivos de contrato de API para controlar el enlace y el código generado.

Los contratos de la API de Xamarin. iOS y Xamarin. C# Mac se escriben principalmente como definiciones de interfaz que definen la forma en C#que se encuentra el código de Objective-C. El proceso implica una combinación de declaraciones de interfaz y algunas definiciones de tipos básicas que el contrato de API puede necesitar. Para ver una introducción a los tipos de enlace, consulte nuestra guía complementaria [de enlace de las bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definiciones de tipo

Sintaxis:

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Cada interfaz de la definición del contrato que tiene [`[BaseType]`](#BaseTypeAttribute) el atributo que declara el tipo base para el objeto generado. En la declaración anterior, `MyType` se C# generará un tipo de clase que se enlaza a un tipo de Objective `MyType`-C denominado.

Si especifica tipos después del TypeName (en el ejemplo anterior `Protocol1` y `Protocol2`) mediante la sintaxis de herencia de interfaz, el contenido de esas interfaces se insertará como si hubieran sido `MyType`parte del contrato de.
La forma en que Xamarin. iOS muestra que un tipo adopta un protocolo es alineando todos los métodos y propiedades que se han declarado en el protocolo en el propio tipo.

A continuación se muestra cómo se definiría la declaración `UITextField` de Objective-C para en un contrato de Xamarin. iOS:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Se escribiría como este como un C# contrato de API:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Puede controlar muchos otros aspectos de la generación de código aplicando otros atributos a la interfaz y configurando el [`[BaseType]`](#BaseTypeAttribute) atributo.

### <a name="generating-events"></a>Generar eventos

Una característica del diseño de la API de Xamarin. iOS y Xamarin. Mac es que asignamos clases de delegado de C# Objective-C como eventos y devoluciones de llamada. Los usuarios pueden elegir en cada instancia si desean adoptar el patrón de programación de Objective-c, asignando a propiedades como `Delegate` una instancia de una clase que implementa los distintos métodos a los que llamaría el tiempo de ejecución de Objective-c, o bien elección de C#los eventos y las propiedades de estilo.

Vamos a ver un ejemplo de cómo usar el modelo Objective-C:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

En el ejemplo anterior, puede ver que hemos elegido sobrescribir dos métodos, una notificación de que se ha producido un evento de desplazamiento y el segundo que es una devolución de llamada que debe devolver un valor booleano que indica `scrollView` si debe desplazarse al Top o not.

El C# modelo permite al usuario de la biblioteca escuchar notificaciones mediante la C# sintaxis de eventos o la sintaxis de la propiedad para enlazar las devoluciones de llamada que se espera que devuelvan valores.

Este es el aspecto C# que tiene el código de la misma característica al usar lambdas:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Dado que los eventos no devuelven valores (tienen un tipo de valor devuelto nulo) puede conectar varias copias. No es un evento, sino que es una propiedad con el tipo `UIScrollViewCondition` que tiene esta firma: `ShouldScrollToTop`

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Devuelve un `bool` valor; en este caso, la sintaxis lambda nos permite devolver simplemente el valor de la `MakeDecision` función.

El generador de enlaces admite la generación de eventos y propiedades que vinculan una `UIScrollViewDelegate` clase como `UIScrollView` con su (bien, llamarlos a la clase de modelo), [`[BaseType]`](#BaseTypeAttribute) esto se hace `Events` anotando la definición con y `Delegates`los parámetros (se describen a continuación). Además de anotar [`[BaseType]`](#BaseTypeAttribute) con esos parámetros, es necesario informar al generador de algunos componentes más.

En el caso de los eventos que toman más de un parámetro (en Objective-C, la Convención es que el primer parámetro de una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que desea para `EventArgs` la clase generada. Esto se hace con el [`[EventArgs]`](#EventArgsAttribute) atributo en la declaración del método en la clase de modelo. Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará una `UIImagePickerImagePickedEventArgs` clase que deriva de `EventArgs` y `UIImage` empaqueta ambos `NSDictionary`parámetros, y. El generador produce lo siguiente:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, expone lo siguiente en la `UIImagePickerController` clase:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Los métodos de modelo que devuelven un valor se enlazan de forma diferente. Estos requieren un nombre para el delegado generado C# (la firma del método) y también un valor predeterminado que se devolverá en caso de que el usuario no proporcione una implementación por sí misma. Por ejemplo, la `ShouldScrollToTop` definición es la siguiente:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Lo anterior creará un `UIScrollViewCondition` delegado con la firma que se mostró anteriormente y, si el usuario no proporciona una implementación, el valor devuelto será true.

Además del [`[DefaultValue]`](#DefaultValueAttribute) atributo, también puede usar el [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) atributo que dirige el generador para devolver el valor del parámetro especificado en la llamada o el [`[NoDefaultValue]`](#NoDefaultValueAttribute) parámetro que indica al generador que no hay ningún valor predeterminado. valor.

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

Sintaxis:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Use la `Name` propiedad para controlar el nombre con el que se enlazará este tipo en el mundo de Objective-C. Normalmente se usa para dar al C# tipo un nombre que es compatible con las directrices de diseño de .NET Framework, pero que se asigna a un nombre en Objective-C que no sigue esa Convención.

Por ejemplo, en el siguiente caso, asignamos el tipo `NSURLConnection` de Objective `NSUrlConnection`-C a, como las directrices de diseño de .NET Framework usan "URL" en lugar de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

El nombre especificado se utiliza como valor del atributo generado `[Register]` en el enlace. Si `Name` no se especifica, el nombre corto del tipo se usa como el valor `[Register]` del atributo en la salida generada.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. Events y BaseType. Delegates

Estas propiedades se utilizan para controlar la generación de C#eventos de estilo en las clases generadas. Se usan para vincular una clase determinada a su clase de delegado de Objective-C. Encontrará muchos casos en los que una clase usa una clase delegada para enviar notificaciones y eventos. Por ejemplo, `BarcodeScanner` un tendría una clase `BardodeScannerDelegate` complementaria. La `BarcodeScanner` clase normalmente tendría una `Delegate` propiedad a la que asignaría una instancia de `BarcodeScannerDelegate` , mientras que esto funciona, quizás desee exponer a la interfaz de eventos C#de estilo a los usuarios y, en esos casos, usaría el `Events` propiedades y`Delegates` [del`[BaseType]`](#BaseTypeAttribute) atributo.

Estas propiedades siempre se establecen juntas y deben tener el mismo número de elementos y mantenerse sincronizados. La `Delegates` matriz contiene una cadena para cada delegado débilmente tipado que se desea ajustar y la `Events` matriz contiene un tipo para cada tipo que se desea asociar a él.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```

#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Si aplica este atributo cuando se crean nuevas instancias de esta clase, la instancia de ese objeto se conservará hasta que se haya invocado al método al `KeepRefUntil` que hace referencia. Esto resulta útil para mejorar la facilidad de uso de las API, cuando no se desea que el usuario mantenga una referencia a un objeto para usar el código. El valor de esta propiedad es el nombre de un método en la `Delegate` clase, por lo que debe usarse en combinación con `Events` las `Delegates` propiedades y también.

`UIActionSheet` En el ejemplo siguiente se muestra cómo lo usa en Xamarin. iOS:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```

<a name="DesignatedDefaultCtorAttribute" />

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz, generará `[DesignatedInitializer]` un atributo en el constructor predeterminado (generado), que se asigna `init` al selector.

<a name="DisableDefaultCtorAttribute" />

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz, impedirá que el generador genere el constructor predeterminado.

Utilice este atributo cuando necesite que el objeto se inicialice con uno de los otros constructores de la clase.

<a name="PrivateDefaultCtorAttribute" />

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Cuando este atributo se aplica a la definición de interfaz, marcará el constructor predeterminado como privado. Esto significa que todavía puede crear instancias de un objeto de esta clase internamente desde el archivo de extensión, pero simplemente no es accesible para los usuarios de su clase.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Utilice este atributo en una definición de tipo para enlazar categorías de Objective-C y exponerlas como C# métodos de extensión para reflejar la manera en que Objective-c expone la funcionalidad.

Las categorías son un mecanismo de Objective-C que se usa para ampliar el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se usan para extender la funcionalidad de una clase base (por ejemplo `NSObject`,) cuando se vincula un marco de trabajo específico en (por ejemplo `UIKit`,), lo que permite que sus métodos estén disponibles, pero solo si el nuevo marco de trabajo está vinculado en.   En algunos otros casos, se usan para organizar características en una clase por funcionalidad.   Son similares en lo que se C# refiere a los métodos de extensión.

Este es el aspecto que tendría una categoría en Objective-C:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

El ejemplo anterior se encuentra en una biblioteca que extienden las instancias `UIView` de con el `makeBackgroundRed`método.

Para enlazarlos, puede usar el [`[Category]`](#CategoryAttribute) atributo en una definición de interfaz.   Al usar el [`[Category]`](#CategoryAttribute) atributo, el significado [`[BaseType]`](#BaseTypeAttribute) del atributo cambia de se utiliza para especificar la clase base que se va a extender, hasta ser el tipo que se va a extender.

A continuación se muestra cómo `UIView` se enlazan las extensiones y C# se convierten en métodos de extensión:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Lo anterior creará una `MyUIViewExtension` clase que contiene el método `MakeBackgroundRed` de extensión.   Esto significa que ahora puede llamar `MakeBackgroundRed` a en cualquier `UIView` subclase, lo que le proporciona la misma funcionalidad que obtendría en Objective-C.

En algunos casos, encontrará miembros **estáticos** dentro de categorías como en el ejemplo siguiente:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Esto dará lugar a una definición de C# interfaz de categoría incorrecta:

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Esto es incorrecto porque para usar la `BoolMethod` extensión necesita una instancia de `FooObject` pero está enlazando una extensión **estática** ObjC, este es un efecto secundario debido al hecho de que se implementan los métodos de C# extensión.

La única manera de usar las definiciones anteriores es mediante el siguiente código feo:

```csharp
(null as FooObject).BoolMethod (range);
```

La recomendación para evitar esto es alinear la `BoolMethod` definición dentro de la `FooObject` propia definición de interfaz, lo que le permitirá llamar a esta extensión como está previsto. `FooObject.BoolMethod (range)`

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Se emitirá una advertencia (BI1117) siempre que se encuentre un [`[Static]`](#StaticAttribute) miembro dentro de [`[Category]`](#CategoryAttribute) una definición. Si realmente desea tener [`[Static]`](#StaticAttribute) miembros dentro de las [`[Category]`](#CategoryAttribute) definiciones, puede `[Category (allowStaticMembers: true)]` silenciar la advertencia mediante o decorando su definición de miembro o [`[Category]`](#CategoryAttribute) interfaz con [`[Internal]`](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Cuando este atributo se aplica a una clase, simplemente genera una clase estática, una que no se deriva de `NSObject`, por lo que se omite el [`[BaseType]`](#BaseTypeAttribute) atributo. Las clases estáticas se usan para hospedar las variables públicas de C que desea exponer.

Por ejemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Generará una C# clase con la siguiente API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Definiciones de protocolo/modelo

Los modelos se utilizan normalmente en la implementación del protocolo.
Difieren en que el tiempo de ejecución solo se registrará con Objective-C los métodos que realmente se han sobrescrito.
De lo contrario, no se registrará el método.

En general `ModelAttribute`, esto significa que, cuando se subclase de una clase que se ha marcado con, no se debe llamar al método base.   Llamar a ese método producirá una excepción; se supone que se implementa todo el comportamiento en la subclase para los métodos que reemplace.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

De forma predeterminada, los miembros que forman parte de un protocolo no son obligatorios. Esto permite a los usuarios crear una subclase del `Model` objeto simplemente derivando de la clase en C# e invalidando solo los métodos que le interesan. En ocasiones, el contrato de Objective-C requiere que el usuario proporcione una implementación para este método (que se marcan con la `@required` Directiva en Objective-C). En esos casos, debe marcar esos métodos con el `[Abstract]` atributo.

El `[Abstract]` atributo se puede aplicar a métodos o propiedades y hace que el generador Marque el miembro generado como abstracto y la clase sea una clase abstracta.

Lo siguiente se toma de Xamarin. iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Especifica el valor predeterminado que un método de modelo devolverá si el usuario no proporciona un método para este método determinado en el objeto de modelo.

Sintaxis:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Por ejemplo, en la siguiente clase de delegado imaginario `Camera` para una clase, se `ShouldUploadToServer` proporciona un que se exponería como una propiedad `Camera` en la clase. Si el usuario de la `Camera` clase no establece explícitamente el valor en una expresión lambda que puede responder a true o false, el valor predeterminado devuelto en este caso sería false, el valor que se especifica en `DefaultValue` el atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Si el usuario establece un controlador en la clase imaginario, se omitirá este valor:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Vea también: [`[NoDefaultValue]`](#NoDefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintaxis:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Este atributo, cuando se proporciona en un método que devuelve un valor en una clase de modelo, indicará al generador que devuelva el valor del parámetro especificado si el usuario no proporcionó su propio método o expresión lambda.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

En el caso anterior, si el usuario de `NSAnimation` la clase decidiera usar cualquiera de los C# eventos o propiedades y no se estableció `NSAnimation.ComputeAnimationCurve` en un método o una expresión lambda, el valor devuelto sería el valor pasado en el parámetro Progress.

Vea también: [`[NoDefaultValue]`](#NoDefaultValueAttribute),[`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

A veces, tiene sentido no exponer una propiedad de evento o delegado de una clase de modelo en la clase host, por lo que agregar este atributo indicará al generador que evite la generación de cualquier método decorado con él.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Este atributo se utiliza en métodos de modelo que devuelven valores para establecer el nombre de la firma de delegado que se va a usar.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador generará la siguiente declaración pública:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Este atributo se utiliza para permitir que el generador cambie el nombre de la propiedad generada en la clase de host. A veces resulta útil cuando el nombre del método de la clase FooDelegate tiene sentido para la clase Delegate, pero tendría un aspecto extraño en la clase host como una propiedad.

También es realmente útil (y necesario) cuando tiene dos o más métodos de sobrecarga que tienen sentido para mantenerlos denominados como están en la clase FooDelegate, pero desea exponerlos en la clase host con un nombre mejor proporcionado.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Con la definición anterior, el generador generará la siguiente declaración pública en la clase host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

En el caso de los eventos que toman más de un parámetro (en Objective-C, la Convención es que el primer parámetro de una clase de delegado es la instancia del objeto de remitente) debe proporcionar el nombre que desea para la clase EventArgs generada. Esto se hace con el `[EventArgs]` atributo en la declaración del método en `Model` la clase.

Por ejemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

La declaración anterior generará una `UIImagePickerImagePickedEventArgs` clase que deriva de EventArgs y empaqueta ambos parámetros `UIImage` , y `NSDictionary`. El generador produce lo siguiente:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

A continuación, expone lo siguiente en la `UIImagePickerController` clase:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

Este atributo se utiliza para permitir que el generador cambie el nombre de un evento o una propiedad generados en la clase. A veces resulta útil cuando el nombre del método de clase de modelo tiene sentido para la clase de modelo, pero tendría un aspecto extraño en la clase de origen como un evento o propiedad.

Por ejemplo, `UIWebView` utiliza el siguiente bit `UIWebViewDelegate`de:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

El anterior expone `LoadingFinished` como el método `UIWebViewDelegate`en, pero `LoadFinished` como `UIWebView`el evento que se va a enlazar a en:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Al aplicar el `[Model]` atributo a una definición de tipo en la API de contrato, el tiempo de ejecución generará código especial que solo mostrará las invocaciones a los métodos de la clase si el usuario ha sobrescrito un método en la clase. Este atributo se aplica normalmente a todas las API que encapsulan una clase de delegado de Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Especifica que el método del modelo no proporciona un valor devuelto predeterminado.

Esto funciona con el tiempo de ejecución de Objective- `false` c respondiendo a la solicitud en tiempo de ejecución de Objective-c para determinar si el selector especificado está implementado en esta clase.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Vea también: [`[DefaultValue]`](#DefaultValueAttribute),[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protocolos

El concepto de protocolo Objective-C no existe realmente en C#. Los protocolos son similares C# a las interfaces, pero difieren en que no todos los métodos y propiedades declarados en un protocolo deben ser implementados por la clase que los adopte. En su lugar, algunos métodos y propiedades son opcionales.

Algunos protocolos se usan generalmente como clases de modelo, que se deben enlazar [`[Model]`](#ModelAttribute) mediante el atributo.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

A partir de Xamarin. iOS 7,0 se ha incorporado una funcionalidad de enlace de protocolo nueva y mejorada.  Cualquier definición que contenga `[Protocol]` el atributo generará realmente tres clases compatibles que mejoran en gran medida la forma en que se consumen los protocolos:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

La **implementación** de la clase proporciona una clase abstracta completa que puede invalidar métodos individuales de y obtener seguridad de tipos completa. Pero debido a C# que no se admite la herencia múltiple, hay escenarios en los que es posible que necesite una clase base diferente, pero que aún desea implementar una interfaz.

Aquí es donde entra en la definición de la **interfaz** generada.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite a los desarrolladores que desean implementar el protocolo implementar simplemente la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como la adopción del protocolo.

Observe que la interfaz solo enumera los métodos necesarios y expone los métodos opcionales.   Esto significa que las clases que adopten el protocolo obtendrán una comprobación completa de tipos para los métodos necesarios, pero tendrán que recurrir a tipos débiles (usando los atributos de exportación y coincidir la firma) para los métodos de protocolo opcionales.

Para que sea conveniente usar una API que use protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.   Esto significa que, siempre que esté consumiendo una API, podrá tratar los protocolos como si tuvieran todos los métodos.

Si desea usar las definiciones de protocolo en la API, deberá escribir las interfaces vacías del esqueleto en la definición de la API.   Si desea utilizar el protocolo de la API, deberá hacer lo siguiente:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Lo anterior es necesario porque, en el momento `IMyProtocol` del enlace, no existía, por eso es necesario proporcionar una interfaz vacía.

### <a name="adopting-protocol-generated-interfaces"></a>Adopción de interfaces generadas por el protocolo

Siempre que implemente una de las interfaces generadas para los protocolos, de la siguiente manera:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

La implementación de los métodos de interfaz se exporta automáticamente con el nombre correcto, por lo que es equivalente a esto:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

No importa si la interfaz se implementa de forma implícita o explícita.

### <a name="protocol-inlining"></a>Inclusión de protocolo

Mientras enlaza tipos de Objective-C existentes que se han declarado como adoptando un protocolo, querrá alinear el protocolo directamente. Para ello, simplemente declare el protocolo como una interfaz sin ningún [`[BaseType]`](#BaseTypeAttribute) atributo y enumere el protocolo en la lista de interfaces base de la interfaz.

Ejemplo:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```

## <a name="member-definitions"></a>Definiciones de miembro

Los atributos de esta sección se aplican a los miembros individuales de un tipo: propiedades y declaraciones de método.

### <a name="alignattribute"></a>AlignAttribute

Se utiliza para especificar el valor de alineación de los tipos de valor devuelto de propiedad. Algunas propiedades toman punteros a direcciones que se deben alinear en ciertos límites (en Xamarin. iOS esto sucede, por ejemplo `GLKBaseEffect` , con algunas propiedades que deben tener una alineación de 16 bytes). Puede usar esta propiedad para decorar el captador y usar el valor de alineación. Normalmente se usa con los tipos `OpenTK.Vector4` y `OpenTK.Matrix4` cuando se integran con las API de Objective-C.

Ejemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

El `[Appearance]` atributo está limitado a iOS 5, donde se presentó el administrador de apariencia.

El `[Appearance]` atributo se puede aplicar a cualquier método o propiedad que participe en `UIAppearance` el marco. Cuando este atributo se aplica a un método o una propiedad en una clase, dirigirá el generador de enlaces para crear una clase de aspecto fuertemente tipada que se utiliza para aplicar estilo a todas las instancias de esta clase o las instancias que coinciden con determinados criterios.

Ejemplo:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

Lo anterior generaría el código siguiente en UIToolbar:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin. iOS 5,4)

Utilice los `[AutoReleaseAttribute]` métodos y las propiedades para ajustar la invocación del método al método `NSAutoReleasePool`en.

En Objective-C hay algunos métodos que devuelven valores que se agregan al `NSAutoReleasePool`valor predeterminado. De forma predeterminada, estos iría al subproceso `NSAutoReleasePool`, pero como Xamarin. iOS también mantiene una referencia a los objetos mientras el objeto administrado reside, es posible que no desee conservar una referencia adicional en el `NSAutoReleasePool` , que solo se purgará hasta el subproceso. Devuelve el control al subproceso siguiente o vuelve al bucle principal.

Este atributo se aplica, por ejemplo, en propiedades pesadas `UIImage.FromFile`(por ejemplo,) que devuelve objetos que se han `NSAutoReleasePool`agregado al valor predeterminado. Sin este atributo, las imágenes se conservarán siempre que el subproceso no devuelva el control al bucle principal. La conexión del subproceso era algún tipo de descargador en segundo plano que siempre está activo y en espera de trabajo. las imágenes nunca se liberan.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]` Se utiliza para exigir la creación de un tipo administrado incluso si el objeto no administrado devuelto no coincide con el tipo descrito en la definición de enlace.

Esto resulta útil cuando el tipo descrito en un encabezado no coincide con el tipo devuelto del método nativo, por ejemplo, tome la siguiente definición de Objective- `NSURLSession`C de:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

`NSURLSessionDownloadTask` Indica claramente que devolverá una instancia de, pero que **devuelve** `NSURLSessionTask`, que es una superclase y, por lo tanto, no se pueden convertir en. `NSURLSessionDownloadTask` Dado que estamos en un contexto con seguridad de tipos `InvalidCastException` , se producirá una.

Para cumplir con la descripción del encabezado y evitar `InvalidCastException`el `[ForcedTypeAttribute]` , se usa.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

También acepta un valor booleano denominado `Owns` que es `false` de forma predeterminada `[ForcedType (owns: true)]`. `[ForcedTypeAttribute]` El parámetro conpropiedads se usa para seguir la [Directiva de propiedad](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) de los objetos de **Core Foundation** .

Solo `[ForcedTypeAttribute]` es válido en parámetros, propiedades y valores devueltos.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`NSNumber` C# Permite enlazar y`NSString`(enumeraciones) en tipos más precisos. `NSValue` `[BindAsAttribute]` El atributo se puede usar para crear una API de .NET mejor y precisa a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y `BindAs`propiedades con. La única restricción es que el miembro **no debe** estar dentro de `[Protocol]` una [`[Model]`](#ModelAttribute) interfaz o.

Por ejemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Generaría:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente, se realizarán `bool?` las `CGRect`  <->  `NSNumber` conversiones y  <-> . `NSValue`

Los tipos de encapsulación admitidos actualmente son:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Se admite C# la encapsulación de los siguientes tipos de datos de/ `NSValue`en:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* CGRect/RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Se admite C# la encapsulación de los siguientes tipos de datos de/ `NSNumber`en:

* booleano
* byte
* Doble
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* Enumeraciones

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute)funciona en conjuntion con [enumeraciones respaldadas por una constante NSString](#enum-attributes) para que pueda crear una mejor API de .net, por ejemplo:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Generaría:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Administraremos la `enum`  <->  [`[BindAs]`conversiónsolo](#BindAsAttribute) si el tipo de enumeración proporcionado en está [respaldado por una constante NSString.](#enum-attributes) `NSString`

#### <a name="arrays"></a>Matrices

[`[BindAs]`](#BindAsAttribute)también admite matrices de cualquiera de los tipos admitidos, por ejemplo, puede tener la siguiente definición de API:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Generaría:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

El `rects` parámetro se encapsulará en una que `NSArray` contenga un `NSValue` para cada `CGRect` y, en la devolución, obtendrá una matriz `CAScroll?` de que se ha creado con los valores del devuelto `NSArray` . que `NSStrings`contiene.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

El `[Bind]` atributo tiene dos usos cuando se aplica a una declaración de método o propiedad y otro cuando se aplica al captador o establecedor individual en una propiedad.

Cuando se usa para un método o una propiedad, el efecto `[Bind]` del atributo es generar un método que invoca el selector especificado. Pero el método generado resultante no se decora con el [`[Export]`](#ExportAttribute) atributo, lo que significa que no puede participar en el reemplazo del método. Normalmente se usa en combinación con el `[Target]` atributo para implementar métodos de extensión de Objective-C.

Por ejemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Cuando se usa en un captador o un `[Bind]` establecedor, el atributo se usa para modificar los valores predeterminados que el generador de código infiere al generar los nombres de selector de Objective-C del captador y establecedor para una propiedad. De forma predeterminada, cuando se marca una propiedad con `fooBar`el nombre, el generador genera `fooBar` una exportación para el captador y `setFooBar:` para el establecedor. En algunos casos, Objective-C no sigue esta Convención, normalmente cambian el nombre del captador como `isFooBar`.
Usaría este atributo para informar al generador de este.

Por ejemplo:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

Solo está disponible en Xamarin. iOS 6,3 y versiones más recientes.

Este atributo se puede aplicar a métodos que toman un controlador de finalización como el último argumento.

Puede usar el `[Async]` atributo en métodos cuyo último argumento sea una devolución de llamada.  Cuando se aplica a un método, el generador de enlaces generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor `Task`devuelto será, si la devolución de llamada toma un parámetro, el resultado `Task<T>`será.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

Lo siguiente generará este método asincrónico:

```csharp
Task LoadFileAsync (string file);
```

Si la devolución de llamada toma varios parámetros, debe establecer `ResultType` o `ResultTypeName` para especificar el nombre deseado del tipo generado que contendrá todas las propiedades.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

Lo siguiente generará este método asincrónico, donde `FileLoading` contiene las propiedades para `files` tener acceso `byteCount`a y:

```csharp
Task<FileLoading> LoadFile (string file);
```

Si el último parámetro de la devolución de llamada `NSError`es, el método `Async` generado comprobará si el valor no es NULL y, en ese caso, el método asincrónico generado establecerá la excepción de la tarea.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

Lo anterior genera el siguiente método asincrónico:

```csharp
Task<string> UploadAsync (string file);
```

Y, si se produce un `NSErrorException` error, la tarea resultante tendrá la excepción establecida en que contiene el resultado. `NSError`

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Utilice esta propiedad para especificar el valor del objeto que `Task` se devuelve.   Este parámetro toma un tipo existente, por lo que debe definirse en una de las definiciones de API principales.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Utilice esta propiedad para especificar el valor del objeto que `Task` se devuelve.   Este parámetro toma el nombre del tipo deseado, el generador generará una serie de propiedades, una para cada parámetro que la devolución de llamada toma.

#### <a name="asyncattributemethodname"></a>AsyncAttribute. MethodName

Utilice esta propiedad para personalizar el nombre de los métodos asincrónicos generados.   El valor predeterminado es usar el nombre del método y anexar el texto "Async"; puede utilizarlo para cambiar este valor predeterminado.

<a name="DesignatedInitializerAttribute" />

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

Cuando este atributo se aplica a un constructor, se generará el `[DesignatedInitializer]` mismo en el ensamblado de plataforma final. Esto sirve para ayudar al IDE a indicar qué constructor se debe utilizar en las subclases.

Se debe asignar al uso de Objective-C/Clang `__attribute__((objc_designated_initializer))`de.

<a name="DisableZeroCopyAttribute" />

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Este atributo se aplica a los parámetros de cadena o a las propiedades de cadena e indica al generador de código que no use el cálculo de referencias de cadenas de copia cero para este parámetro y, en su C# lugar, crea una nueva instancia de NSString a partir de la cadena.
Este atributo solo es necesario en las cadenas si se indica al generador que use el cálculo de referencias de cadenas de copia `--zero-copy` cero mediante la opción de línea de comandos o estableciendo `ZeroCopyStringsAttribute`el atributo de nivel de ensamblado.

Esto es necesario en los casos en los que la propiedad se declara en Objective-C `retain` para `assign` ser una propiedad o en `copy` lugar de una propiedad. Normalmente se producen en bibliotecas de terceros que los desarrolladores han "optimizado" erróneamente. En general, `retain` las `assign` propiedades o `NSString` son incorrectas, ya que `NSMutableString` las `NSString` clases derivadas por el usuario de pueden modificar el contenido de las cadenas sin el conocimiento del código de la biblioteca, con lo que se interrumpe sutilmente el aplicación. Normalmente esto sucede debido a una optimización prematura.

A continuación se muestran dos propiedades de este tipo en Objective-C:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute" />

### <a name="disposeattribute"></a>DisposeAttribute

Cuando se aplica `[DisposeAttribute]` a una clase, se proporciona un fragmento de código que se agregará a la `Dispose()` implementación del método de la clase.

Dado que `Dispose` las herramientas `bmac-native` y `btouch-native` generan automáticamente el método, debe usar el `[Dispose]` atributo para insertar código en la implementación del método generado `Dispose` .

Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

El `[Export]` atributo se usa para marcar un método o una propiedad que se expondrán al tiempo de ejecución de Objective-C. Este atributo se comparte entre la herramienta de enlace y los tiempos de ejecución reales de Xamarin. iOS y Xamarin. Mac. En el caso de los métodos, el parámetro se pasa literalmente al código generado, para las propiedades, las exportaciones de captador y establecedor se generan basándose en la [`[BindAttribute]`](#BindAttribute) declaración base (consulte la sección en el para obtener información sobre cómo modificar el comportamiento de la herramienta de enlace).

Sintaxis:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

El [selector](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) representa el nombre del método o propiedad de Objective-C subyacente que se está enlazando.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Este atributo se usa para exponer una variable global de C como un campo que se carga a petición y se C# expone al código. Normalmente, esto es necesario para obtener los valores de las constantes que se definen en C o Objective-C y que podrían ser tokens usados en algunas API o cuyos valores son opacos y deben usarse tal cual por el código de usuario.

Sintaxis:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName` Es el símbolo de C con el que se va a vincular. De forma predeterminada, se cargará desde una biblioteca cuyo nombre se deduce del espacio de nombres en el que se define el tipo. Si no es la biblioteca en la que se busca el símbolo, debe pasar el `libraryName` parámetro. Si está vinculando una biblioteca estática, use `__Internal` `libraryName` como parámetro.

Las propiedades generadas siempre son estáticas.

Las propiedades marcadas con el atributo de campo pueden ser de los siguientes tipos:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enumeraciones

Los establecedores no se admiten en las [enumeraciones respaldadas por constantes NSString](#enum-attributes), pero se pueden enlazar manualmente si es necesario.

Ejemplo:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

El `[Internal]` atributo se puede aplicar a métodos o propiedades y tiene el efecto de marcar el código generado con la `internal` C# palabra clave para que el código solo sea accesible para el código del ensamblado generado. Esto se usa normalmente para ocultar las API que son demasiado bajas o proporcionar una API pública no óptima que desee mejorar o para las API que no son compatibles con el generador y requieren algún código de mano.

Al diseñar el enlace, normalmente ocultaría el método o la propiedad mediante este atributo y proporcionaría un nombre diferente para el método o la propiedad y, después, C# en el archivo de soporte complementario, agregaría un contenedor fuertemente tipado que exponga el funcionalidad subyacente.

Por ejemplo:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Después, en el archivo auxiliar, podría tener código similar al siguiente:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Este atributo marca el campo de respaldo de una propiedad que se va a anotar `[ThreadStatic]` con el atributo de .net. Esto resulta útil si el campo es una variable estática de subproceso.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin. iOS 6.0.6)

Este atributo hará que un método admita excepciones nativas (Objective-C).
En lugar de llamar `objc_msgSend` directamente a, la invocación pasará a través de un Trampoline personalizado que detecta las excepciones de ObjectiveC y las serializa en excepciones administradas.

Actualmente solo se admiten algunas `objc_msgSend` firmas (se averigua si no se admite una firma cuando la vinculación nativa de una aplicación que usa el enlace produce un error con un símbolo *_objc_msgSend* de monotouch_ que falta), pero se puede agregar más en la solicitud.

### <a name="newattribute"></a>NewAttribute

Este atributo se aplica a los métodos y propiedades para que el generador genere `new` la palabra clave delante de la declaración.

Se utiliza para evitar las advertencias del compilador cuando se introduce el mismo nombre de método o propiedad en una subclase que ya existía en una clase base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

Puede aplicar este atributo a los campos para que el generador genere una clase de notificaciones de aplicación auxiliar fuertemente tipadas.

Este atributo se puede usar sin argumentos para las notificaciones que no tienen ninguna carga útil, o puede `System.Type` especificar un que haga referencia a otra interfaz en la definición de la API, normalmente con el nombre que termina con "EventArgs". El generador convertirá la interfaz en una clase que crea subclases `EventArgs` e incluirá todas las propiedades que aparecen en la lista. El [`[Export]`](#ExportAttribute) atributo debe usarse en la `EventArgs` clase para mostrar el nombre de la clave utilizada para buscar el Diccionario de Objective-C para capturar el valor.

Por ejemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

En el código anterior se generará una clase `MyClass.Notifications` anidada con los siguientes métodos:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Los usuarios del código pueden suscribirse fácilmente a las notificaciones publicadas en [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) mediante código como el siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O para establecer un objeto específico que se va a observar. Si se pasa `null` a `objectToObserve` este método, se comportará igual que el otro elemento del mismo nivel.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

El valor devuelto `ObserveDidStart` de se puede usar para dejar de recibir notificaciones fácilmente, de la siguiente manera:

```csharp
token.Dispose ();
```

O bien, puede llamar a [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) y pasar el token. Si la notificación contiene parámetros, debe especificar una `EventArgs` interfaz auxiliar, similar a la siguiente:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Lo anterior generará una `MyScreenChangedEventArgs` clase `ScreenX` con las propiedades `ScreenY` y que capturarán los datos del diccionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando las claves `ScreenXKey` y `ScreenYKey` , respectivamente, y aplicarán el conversiones adecuadas. El `[ProbePresence]` atributo se usa para que el generador sondee si la clave se establece `UserInfo`en, en lugar de intentar extraer el valor. Se utiliza para los casos en los que la presencia de la clave es el valor (normalmente para los valores booleanos).

Esto le permite escribir código como el siguiente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

En algunos casos, no hay ninguna constante asociada al valor pasado en el diccionario.  A veces Apple utiliza constantes de símbolos públicos y a veces utiliza constantes de cadena.  De forma predeterminada [`[Export]`](#ExportAttribute) , el atributo de `EventArgs` la clase proporcionada usará el nombre especificado como símbolo público que se va a buscar en tiempo de ejecución.  Si no es así, se supone que se debe buscar como una constante de cadena y, a continuación, pasar el `ArgumentSemantic.Assign` valor al atributo export.

**Novedades de Xamarin. iOS 8,4**

A veces, las notificaciones comenzarán a vivir sin argumentos, por lo [`[Notification]`](#NotificationAttribute) que el uso de sin argumentos es aceptable.  Pero a veces, se introducirán parámetros para la notificación.  Para admitir este escenario, el atributo se puede aplicar más de una vez.

Si va a desarrollar un enlace y desea evitar la interrupción del código de usuario existente, debe convertir una notificación existente de:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

En una versión que enumera el atributo de notificación dos veces, como se indica a continuación:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Cuando se aplica a una propiedad, marca la propiedad para que se le asigne `null` el valor. Esto solo es válido para los tipos de referencia.

Cuando se aplica a un parámetro en una firma de método, indica que el parámetro especificado puede ser NULL y que no se debe realizar ninguna comprobación para `null` pasar valores.

Si el tipo de referencia no tiene este atributo, la herramienta de enlace generará una comprobación del valor que se va a asignar antes de pasarlo a Objective-C y generará una comprobación `ArgumentNullException` que producirá una excepción `null`si el valor asignado es.

Por ejemplo:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

Utilice este atributo para indicar al generador de enlaces que el enlace para este método concreto se debe marcar con una `override` palabra clave.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Puede usar este atributo para insertar código que se va a insertar una vez validados los parámetros de entrada, pero antes de que el código llame a Objective-C.

Ejemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Puede usar este atributo para insertar código que se va a insertar antes de que cualquiera de los parámetros se valide en el método generado.

Ejemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Indica al generador de enlaces que invoque la propiedad especificada desde esta clase para capturar un valor de ella.

Esta propiedad se utiliza normalmente para actualizar la memoria caché que apunta a los objetos de referencia que mantienen el gráfico de objetos al que se hace referencia. Normalmente, se muestra en el código que tiene operaciones como agregar o quitar. Este método se usa para que se agreguen o quiten elementos después de que se actualice la memoria caché interna para asegurarse de que se mantienen las referencias administradas a los objetos que realmente se están usando. Esto es posible porque la herramienta de enlace genera un campo de respaldo para todos los objetos de referencia en un enlace determinado.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

En este caso, la `Dependencies` propiedad se invocará después `NSOperation` de agregar o quitar las dependencias del objeto, asegurándose de que tenemos un gráfico que representa los objetos cargados reales, evitando pérdidas de memoria, así como daños en la memoria.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Puede usar este atributo para insertar código C# fuente que se va a insertar después de que el código haya invocado el método de Objective-C subyacente.

Ejemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Este atributo se aplica a los valores devueltos para marcarlos como objetos proxy. Algunas API de Objective-C devuelven objetos proxy que no se pueden diferenciar de los enlaces de usuario. El efecto de este atributo es marcar el objeto como un `DirectBinding` objeto. Para un escenario de Xamarin. Mac, puede ver la [explicación sobre este error](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Indica al generador que mantenga una referencia administrada al parámetro o quite una referencia interna al parámetro. Se usa para mantener los objetos a los que se hace referencia.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de `doAdd` es true, el parámetro se agrega `__mt_{0}_var List<NSObject>;`a. Donde `{0}` se reemplaza por el especificado `listName`. Debe declarar este campo de respaldo en la clase parcial complementaria a la API.

Para ver un ejemplo, vea [Foundation.CS](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.CS](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin. iOS 6,0)

Se puede aplicar a los tipos de valor devuelto para indicar que el `Release` generador debe llamar a en el objeto antes de devolverlo. Esto solo es necesario cuando un método proporciona un objeto retenido (en lugar de un objeto autolanzado, que es el escenario más común).

Ejemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Además, este atributo se propaga al código generado, de modo que el tiempo de ejecución de Xamarin. iOS sabe que debe conservar el objeto al volver a Objective-C desde una función de este tipo.

### <a name="sealedattribute"></a>SealedAttribute

Indica al generador que marque el método generado como sellado. Si no se especifica este atributo, el valor predeterminado es generar un método virtual (ya sea un método virtual, un método abstracto o una invalidación en función de cómo se utilicen otros atributos).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Cuando el `[Static]` atributo se aplica a un método o propiedad, genera una propiedad o un método estático. Si no se especifica este atributo, el generador genera una propiedad o un método de instancia.

### <a name="transientattribute"></a>TransientAttribute

Use este atributo para marcar las propiedades cuyos valores son transitorios, es decir, los objetos creados temporalmente por iOS pero que no son de larga duración. Cuando este atributo se aplica a una propiedad, el generador no crea un campo de respaldo para esta propiedad, lo que significa que la clase administrada no mantiene una referencia al objeto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

En el diseño de los enlaces de Xamarin. iOS/Xamarin. Mac, el `[Wrap]` atributo se usa para encapsular un objeto débilmente tipado con un objeto fuertemente tipado. Esto entra en juego principalmente con objetos de delegado de Objective-C que se declaran normalmente `id` como `NSObject`de tipo o. La Convención usada por Xamarin. iOS y Xamarin. Mac es exponer esos delegados u orígenes de datos como de `NSObject` tipo y se denominan mediante la Convención "Weak" + el nombre que se está exponiendo. Una `id delegate` propiedad de Objective-C se expondría como una `NSObject WeakDelegate { get; set; }` propiedad en el archivo de contrato de la API.

Pero normalmente, el valor que se asigna a este delegado es de un tipo seguro, por lo que se crea el tipo seguro `[Wrap]` y se aplica el atributo, lo que significa que los usuarios pueden optar por usar tipos débiles si necesitan un control preciso o si necesitan recurrir a tric de bajo nivel. KS, o bien pueden utilizar la propiedad fuertemente tipada para la mayor parte de su trabajo.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Así es como el usuario usaría la versión débilmente tipada del delegado:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

Y este es el modo en que el usuario usaría la versión fuertemente tipada, tenga en cuenta que el usuario C#aprovecha el sistema de tipos de y usa la palabra clave override para declarar su intención y que no tiene que decorar manualmente el `[Export]`método con, ya que lo hacía en el enlace del usuario:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Otro uso del atributo `[Wrap]` es admitir la versión fuertemente tipada de los métodos.  Por ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Cuando el `[Wrap]` atributo se aplica a un método dentro de un tipo decorado `[Category]` con un atributo, debe incluir `This` como primer argumento desde que se genera un método de extensión. Por ejemplo:

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

Los miembros generados `[Wrap]` por no `virtual` son de forma predeterminada; si necesita `virtual` un miembro, puede establecer `true` en el `isVirtual` parámetro opcional.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]`también se puede usar directamente en captadores y establecedores de propiedad.
Esto permite tener control total sobre ellos y ajustar el código según sea necesario.
Por ejemplo, considere la siguiente definición de API que usa enumeraciones inteligentes:

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

Definición de interfaz:

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>Atributos de parámetro

En esta sección se describen los atributos que se pueden aplicar a los parámetros en una definición de método, `[NullAttribute]` así como el que se aplica a una propiedad en su totalidad.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Este atributo se aplica a los tipos de C# parámetros en las declaraciones de delegado para notificar al enlazador que el parámetro en cuestión se ajusta a la Convención de llamada a bloque de Objective-C y debe serializarlo de esta manera.

Normalmente se usa para las devoluciones de llamada definidas como esta en Objective-C:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vea también: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Este atributo se aplica a los tipos de C# parámetros en las declaraciones de delegado para notificar al enlazador que el parámetro en cuestión se ajusta a la Convención de llamada al puntero de función Abi de C y debe serializarlo de esta manera.

Normalmente se usa para las devoluciones de llamada definidas como esta en Objective-C:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Vea también: [BlockCallback](#BlockCallback).

### <a name="params"></a>Params

Puede usar el `[Params]` atributo en el último parámetro de matriz de una definición de método para que el generador Inserte un "PARAM" en la definición.   Esto permite que el enlace permita fácilmente parámetros opcionales.

Por ejemplo, la siguiente definición:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Permite escribir el código siguiente:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Esto tiene la ventaja adicional de que no requiere que los usuarios creen una matriz únicamente para pasar elementos.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

Puede usar el `[PlainString]` atributo delante de los parámetros de cadena para indicar al generador de enlaces que pase la cadena como una cadena de C, en lugar de pasar el parámetro `NSString`como.

La mayoría de las API de `NSString` Objective-C consumen parámetros, pero `char *` un puñado de API expone una API para pasar `NSString` cadenas, en lugar de la variación.
Use `[PlainString]` en esos casos.

Por ejemplo, las siguientes declaraciones de Objective-C:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Se debe enlazar como se indica a continuación:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Indica al generador que mantenga una referencia al parámetro especificado. El generador proporcionará la memoria auxiliar para este campo o puede especificar un nombre (el `WrapName`) en el que almacenar el valor. Esto resulta útil para contener una referencia a un objeto administrado que se pasa como parámetro a Objective-C y cuando se sabe que Objective-C solo conservará esta copia del objeto. Por ejemplo, una API como `SetDisplay (SomeObject)` usaría este atributo, ya que es probable que SetDisplay solo muestre un objeto cada vez. Si necesita realizar un seguimiento de más de un objeto (por ejemplo, para una API de tipo pila), usaría el `[RetainList]` atributo.

Sintaxis:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

Indica al generador que mantenga una referencia administrada al parámetro o quite una referencia interna al parámetro. Se usa para mantener los objetos a los que se hace referencia.

Sintaxis:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Si el valor de `doAdd` es true, el parámetro se agrega `__mt_{0}_var List<NSObject>`a. Donde `{0}` se reemplaza por el especificado `listName`. Debe declarar este campo de respaldo en la clase parcial complementaria a la API.

Para ver un ejemplo, vea [Foundation.CS](https://github.com/mono/maccore/blob/master/src/foundation.cs) y [NSNotificationCenter.CS](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

Este atributo se aplica a los parámetros y solo se usa cuando se realiza la transición de Objective C#-C a.  Durante esas transiciones, los distintos parámetros de `NSObject` Objective-C se ajustan en una representación administrada del objeto.

El tiempo de ejecución tomará una referencia al objeto nativo y mantendrá la referencia hasta que la última referencia administrada al objeto haya desaparecido, y el GC tendrá la oportunidad de ejecutarse.

En algunos casos, es importante que el C# Runtime no mantenga una referencia al objeto nativo.  En ocasiones, esto sucede cuando el código nativo subyacente ha adjuntado un comportamiento especial al ciclo de vida del parámetro.  Por ejemplo: el destructor del parámetro realizará alguna acción de limpieza o eliminará un recurso precioso.

Este atributo informa al tiempo de ejecución de que desea que el objeto se elimine si es posible al devolver a Objective-C desde el método sobrescrito.

La regla es sencilla: Si el tiempo de ejecución tenía que crear una nueva representación administrada a partir del objeto nativo, al final de la función se quitará el recuento de retenciones del objeto nativo y se borrará la propiedad Handle del objeto administrado.   Esto significa que si se mantiene una referencia al objeto administrado, esa referencia se volverá inútil (al invocar métodos en ella se producirá una excepción).

Si no se ha creado el objeto que se ha pasado, o si ya había una representación administrada pendiente del objeto, la eliminación forzada no tiene lugar. 

## <a name="property-attributes"></a>Atributos de propiedad

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Este atributo se usa para admitir una expresión de Objective-C donde una propiedad con un captador se introduce en una clase base y una subclase mutable introduce un establecedor.

Puesto C# que no admite este modelo, la clase base debe tener el establecedor y el captador, y una subclase puede utilizar [OverrideAttribute](#OverrideAttribute).

Este atributo solo se usa en los establecedores de propiedad y se usa para admitir la expresión mutable en Objective-C.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enumerar atributos

Asignar `NSString` constantes a valores de enumeración es una manera fácil de crear una API de .net. Instalar

* permite que la finalización del código sea más útil, mostrando **solo** los valores correctos de la API;
* agrega seguridad de tipos, no se puede `NSString` utilizar otra constante en un contexto incorrecto.
* permite ocultar algunas constantes y hacer que la finalización del código muestre una lista de API más corta sin perder la funcionalidad.

Ejemplo:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

En la definición de enlace anterior, el generador creará el `enum` propio y creará también un `*Extensions` tipo estático que incluye métodos de conversión de dos maneras entre los valores `NSString` de enumeración y las constantes. Esto significa que las constantes siguen estando disponibles para los desarrolladores aunque no formen parte de la API.

Ejemplos:

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Puede decorar **un** valor de enumeración con este atributo. Se convertirá en la constante que se devuelve si no se conoce el valor de enumeración.

En el ejemplo anterior:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Si no se decora ningún valor de enumeración, se producirá una `NotSupportedException` excepción.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Los códigos de error se enlazan como valores de enumeración. Por lo general, hay un dominio de error para ellos y no siempre es fácil encontrar cuál es el que se aplica (o si existe uno incluso).

Puede usar este atributo para asociar el dominio de error a la propia enumeración.

Ejemplo:

```csharp
[Native]
[ErrorDomain ("AVKitErrorDomain")]
public enum AVKitError : nint {
    None = 0,
    Unknown = -1000,
    PictureInPictureStartFailed = -1001
}
```

Después, puede llamar al método `GetDomain` de extensión para obtener la constante de dominio de cualquier error.

### <a name="fieldattribute"></a>FieldAttribute

Este es el mismo `[Field]` atributo que se usa para las constantes dentro del tipo. También se puede utilizar dentro de las enumeraciones para asignar un valor a una constante específica.

Se `null` puede utilizar un valor para especificar el valor de enumeración que se debe `null` devolver si se especifica una `NSString` constante.

En el ejemplo anterior:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Si no `null` hay ningún valor presente, `ArgumentNullException` se producirá una excepción.

## <a name="global-attributes"></a>Atributos globales

Los atributos globales se aplican mediante `[assembly:]` el modificador de atributo [`[LinkWithAttribute]`](#LinkWithAttribute) , como o se pueden usar en cualquier [`[Lion]`](#SinceAndLionAttributes) parte [`[Since]`](#SinceAndLionAttributes) , como los atributos y.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Se trata de un atributo de nivel de ensamblado que permite a los desarrolladores especificar las marcas de vinculación necesarias para reutilizar una biblioteca enlazada sin forzar al consumidor de la biblioteca a configurar manualmente los argumentos gcc_flags y Mtouch adicionales pasados a una biblioteca.

Sintaxis:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Este atributo se aplica en el nivel de ensamblado, por ejemplo, esto es lo que usan los [enlaces de CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) :

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Cuando se usa el `[LinkWith]` atributo, el especificado `libraryName` se incrusta en el ensamblado resultante, lo que permite a los usuarios enviar un único archivo DLL que contiene las dependencias no administradas, así como las marcas de línea de comandos necesarias para consumir correctamente el Biblioteca de Xamarin. iOS.

También es posible no proporcionar un `libraryName`, en cuyo caso el `LinkWith` atributo se puede usar para especificar solo marcas de enlazador adicionales:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Constructores LinkWithAttribute

Estos constructores permiten especificar la biblioteca con la que se va a vincular e insertar en el ensamblado resultante, los destinos admitidos que admite la biblioteca y cualquier marcador de biblioteca opcional que sea necesario vincular a la biblioteca.

Tenga en cuenta `LinkTarget` que Xamarin. iOS infiere el argumento y no es necesario establecerlo.

Ejemplos:

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

La `ForceLoad` propiedad se utiliza para decidir si se usa o `-force_load` no la marca de vínculo para vincular la biblioteca nativa. Por ahora, siempre debe ser true.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute. frameworks

Si la biblioteca que se está enlazando tiene un requisito estricto en cualquier marco de `Foundation` trabajo `UIKit`(distinto de y), `Frameworks` debe establecer la propiedad en una cadena que contiene una lista delimitada por espacios de los marcos de plataforma necesarios. Por ejemplo, si va a enlazar una biblioteca que `CoreGraphics` requiere `CoreText`y, debe establecer la `Frameworks` propiedad en `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Establezca esta propiedad en true si el ejecutable resultante debe compilarse con un C++ compilador en lugar del valor predeterminado, que es un compilador de C. Úselo si la biblioteca en C++la que está enlazando se escribió.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

Nombre de la biblioteca no administrada que se va a agrupar. Se trata de un archivo con la extensión ". a" y puede contener código de objeto para varias plataformas (por ejemplo, ARM y x86 para el simulador).

Las versiones anteriores de Xamarin. iOS comprobó la `LinkTarget` propiedad para determinar la plataforma admitida por la biblioteca, pero ahora se detecta automáticamente y se omite la `LinkTarget` propiedad.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

La `LinkerFlags` cadena proporciona una manera de enlazar a los autores de especificar cualquier marcador de enlazador adicional necesario al vincular la biblioteca nativa en la aplicación.

Por ejemplo, si la biblioteca nativa requiere libxml2 y zlib, establecería la `LinkerFlags` cadena en. `"-lxml2 -lz"`

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

Las versiones anteriores de Xamarin. iOS comprobó la `LinkTarget` propiedad para determinar la plataforma admitida por la biblioteca, pero ahora se detecta automáticamente y se omite la `LinkTarget` propiedad.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Establezca esta propiedad en true si la biblioteca que se va a vincular requiere la biblioteca de control de excepciones GCC (gcc_eh).

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

La `SmartLink` propiedad se debe establecer en true para permitir que Xamarin. iOS determine si `ForceLoad` es necesario o no.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

La `WeakFrameworks` propiedad funciona de la misma forma que `Frameworks` la propiedad, excepto en el momento de la vinculación, el `-weak_framework` especificador se pasa a GCC para cada uno de los marcos de trabajo de la lista.

`WeakFrameworks`permite que las bibliotecas y aplicaciones se vinculen de forma débil con los marcos de trabajo de la plataforma para que puedan usarlos opcionalmente si están disponibles, pero no tienen una dependencia fuerte en ellos, lo que resulta útil si la biblioteca está pensada para agregar características adicionales en una versión más reciente. versiones de iOS. Para obtener más información sobre la vinculación débil, consulte la documentación de Apple sobre la [vinculación débil](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Los buenos candidatos para la vinculación `Frameworks` débil serían `CoreBluetooth`las `CoreImage`cuentas `GLKit`,, `Twitter` , y, `NewsstandKit` ya que solo están disponibles en iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) y LionAttribute (macOS)

El atributo se `[Since]` usa para marcar las API como si se hubieran introducido en un momento determinado. El atributo solo se debe usar para marcar tipos y métodos que podrían provocar un problema en tiempo de ejecución si la clase, el método o la propiedad subyacentes no están disponibles.

Sintaxis:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

En general, no se debe aplicar a las enumeraciones, restricciones o estructuras nuevas, ya que no provocarían un error en tiempo de ejecución si se ejecutan en un dispositivo con una versión anterior del sistema operativo.

Ejemplo cuando se aplica a un tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Ejemplo cuando se aplica a un nuevo miembro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

El `[Lion]` atributo se aplica de la misma manera pero en el caso de los tipos introducidos con Lion. La razón para usar `[Lion]` frente al número de versión más específico que se usa en iOS es que Ios se revisa con mucha frecuencia, mientras que las versiones principales de OS X se producen con poca frecuencia y es más fácil recordar el sistema operativo por su nombre de código que por su número de versión

### <a name="adviceattribute"></a>AdviceAttribute

Utilice este atributo para ofrecer a los desarrolladores una sugerencia sobre otras API que pueden ser más cómodas de usar.   Por ejemplo, si proporciona una versión fuertemente tipada de una API, puede usar este atributo en el atributo débilmente tipado para dirigir al desarrollador a la mejor API.

La información de este atributo se muestra en la documentación y se pueden desarrollar herramientas para proporcionar sugerencias de usuario sobre cómo mejorar

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

Se trata de una subclase especializada del `[Advice]` atributo que se puede usar para sugerir al desarrollador que invalide un método **requiere** una llamada al método base (invalidado).

Esto corresponde a `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Solo está disponible en Xamarin. iOS 5,4 y versiones más recientes.

Este atributo indica al generador que el enlace para esta biblioteca específica (si se aplica con `[assembly:]`) o el tipo debe utilizar el cálculo de referencias de cadenas de copia rápida de cero. Este atributo es equivalente a pasar la opción `--zero-copy` de la línea de comandos al generador.

Cuando se usa una copia de cero para las cadenas, el generador utiliza C# de forma eficaz la misma cadena que la cadena que Objective-C consume sin incurrir en `NSString` la creación de un nuevo objeto y evitar C# copiar los datos de las cadenas en el Cadena de Objective-C. El único inconveniente de usar cero cadenas de copia es que debe asegurarse de que todas las propiedades de cadena que se ajustan se marcan `copy` como `retain` o `[DisableZeroCopy]` tienen el atributo establecido. Esto es necesario porque el identificador de las cadenas de copia cero se asigna en la pila y no es válido en la función que devuelve.

Ejemplo:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

También puede aplicar el atributo en el nivel de ensamblado y se aplicará a todos los tipos del ensamblado:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Diccionarios fuertemente tipados

Con Xamarin. iOS 8,0 hemos incorporado compatibilidad para crear fácilmente clases fuertemente tipadas que se `NSDictionaries`ajustan.

Aunque siempre ha sido posible usar el tipo de datos [DictionaryContainer](xref:Foundation.DictionaryContainer) junto con una API manual, ahora es mucho más sencillo hacerlo.  Para obtener más información, consulte [exposición de tipos seguros](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Cuando este atributo se aplica a una interfaz, el generador genera una clase con el mismo nombre que la interfaz que se deriva de [DictionaryContainer](xref:Foundation.DictionaryContainer) y convierte cada propiedad definida en la interfaz en un captador y establecedor fuertemente tipados para el diccionarios.

Esto genera automáticamente una clase de la que se pueden crear instancias desde `NSDictionary` un existente o que se ha creado.

Este atributo toma un parámetro, el nombre de la clase que contiene las claves que se usan para obtener acceso a los elementos del diccionario.   De forma predeterminada, cada propiedad de la interfaz con el atributo buscará un miembro en el tipo especificado para un nombre con el sufijo "Key".

Por ejemplo:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

En el caso anterior, la `MyOption` clase generará una propiedad de cadena `Name` para que utilizará `MyOptionKeys.NameKey` como la clave en el diccionario para recuperar una cadena.   Y usará `NSNumber` como la clave en el diccionario para recuperar un que contiene un int. `MyOptionKeys.AgeKey`

Si desea usar una clave diferente, puede usar el atributo de exportación en la propiedad, por ejemplo:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Tipos de diccionario seguros

En la `StrongDictionary` definición se admiten los siguientes tipos de datos:

|C#Tipo de interfaz|`NSDictionary`Tipo de almacenamiento|
|---|---|
|`bool`|`Boolean`almacenado en un`NSNumber`|
|Valores de enumeración|entero almacenado en un`NSNumber`|
|`int`|entero de 32 bits almacenado en un`NSNumber`|
|`uint`|entero de 32 bits sin signo almacenado en un`NSNumber`|
|`nint`|`NSInteger`almacenado en un`NSNumber`|
|`nuint`|`NSUInteger`almacenado en un`NSNumber`|
|`long`|entero de 64 bits almacenado en un`NSNumber`|
|`float`|entero de 32 bits almacenado como`NSNumber`|
|`double`|entero de 64 bits almacenado como`NSNumber`|
|`NSObject`y subclases|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` de`NSObject`|`NSArray`|
|C#`Array` de enumeraciones|`NSArray`valores `NSNumber` que contienen|
