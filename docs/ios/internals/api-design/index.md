---
title: Diseño de la API de Xamarin. iOS
description: Principios de GUID que se usaron para diseñar las API de Xamarin. iOS y cómo se relacionan con Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724438"
---
# <a name="xamarinios-api-design"></a>Diseño de la API de Xamarin. iOS

Además de las bibliotecas de clases base básicas que forman parte de mono, [Xamarin. iOS](~/ios/index.yml) se distribuye con enlaces para varias API de iOS para que los desarrolladores puedan crear aplicaciones de iOS nativas con mono.

En el núcleo de Xamarin. iOS, hay un motor de interoperabilidad que C# une el mundo al mundo de Objective-C, así como enlaces para las API basadas en C de iOS como CoreGraphics y [OpenGL es](#opengles).

El motor en tiempo de ejecución de bajo nivel para comunicarse con código de Objective-C es [MonoTouch. ObjCRuntime](#objcruntime). Además de esto, se proporcionan enlaces para [Foundation](#foundation), CoreFoundation y [UIKit](#uikit) .

## <a name="design-principles"></a>Principios de diseño

Estos son algunos de los principios de diseño de los enlaces de Xamarin. iOS (también se aplican a Xamarin. Mac, los enlaces mono para Objective-C en macOS):

- Siga las [directrices de diseño de .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir a los desarrolladores subclases de clases de Objective-C:

  - Derivar de una clase existente
  - Llamar al constructor base para encadenar
  - Los métodos de reemplazo deben realizarse C#con el sistema de invalidación de
  - La creación de subclases C# debe funcionar con construcciones estándar

- No exponga a los desarrolladores a los selectores de Objective-C
- Proporcionar un mecanismo para llamar a bibliotecas arbitrarias de Objective-C
- Haga que las tareas comunes de Objective-C resulten sencillas y objetivas, que son posibles.
- Exponer propiedades de Objective-C C# como propiedades
- Exponga una API fuertemente tipada:

  - Aumentar la seguridad de tipos
  - Minimizar los errores en tiempo de ejecución
  - Obtener IntelliSense del IDE en los tipos de valor devuelto
  - Permite la documentación del menú emergente IDE

- Anime la exploración en el IDE de las API:

  - Por ejemplo, en lugar de exponer una matriz débilmente tipada de la manera siguiente:

    ```objc
    NSArray *getViews
    ```

    Exponga un tipo seguro, de la siguiente manera:

    ```csharp
    NSView [] Views { get; set; }
    ```

    Esto proporciona a Visual Studio para Mac la capacidad de realizar una finalización automática mientras se examina la API, hace que todas las operaciones de `System.Array` estén disponibles en el valor devuelto y permite que el valor devuelto participe en LINQ.

- Tipos C# nativos:

  - [`NSString` se convierte en `string`](~/ios/internals/api-design/nsstring.md)
  - Convertir `int` y `uint` parámetros que deben haberse enumerado en C# enumeraciones y C# enumeraciones con atributos `[Flags]`
  - En lugar de objetos `NSArray` neutros para el tipo, exponga matrices como matrices fuertemente tipadas.
  - En el caso de eventos y notificaciones, proporcione a los usuarios una opción entre:

    - Una versión fuertemente tipada de forma predeterminada
    - Una versión débilmente tipada para casos de uso avanzados

- Compatibilidad con el patrón de delegado de Objective-C:

  - C#sistema de eventos
  - Exponga C# los delegados (expresiones lambda, métodos anónimos y `System.Delegate`) a las API de Objective-C como bloques

### <a name="assemblies"></a>Ensamblados

Xamarin. iOS incluye una serie de ensamblados que constituyen el *Perfil de Xamarin. iOS*. La página [ensamblados](~/cross-platform/internals/available-assemblies.md) tiene más información.

### <a name="major-namespaces"></a>Espacios de nombres principales

#### <a name="objcruntime"></a>ObjCRuntime

El espacio de nombres [ObjCRuntime](xref:ObjCRuntime) permite a los desarrolladores enlazar los mundos entre C# y Objective-C.
Se trata de un nuevo enlace, diseñado específicamente para iOS, en función de la experiencia de coco # y gtk #.

#### <a name="foundation"></a>Foundation

El espacio de nombres [Foundation](xref:Foundation) proporciona los tipos de datos básicos diseñados para interoperar con el marco de trabajo de Objective-c Foundation que forma parte de iOS y es la base para la programación orientada a objetos en Objective-c.

Los reflejos de Xamarin C# . iOS en la jerarquía de clases de Objective-C. Por ejemplo, la clase base de Objective-C NSObject se puede C# usar desde [Foundation. NSObject](xref:Foundation.NSObject).

Aunque este espacio de nombres proporciona enlaces para los tipos subyacentes de Objective-C Foundation, en algunos casos hemos asignado los tipos subyacentes a los tipos de .NET. Por ejemplo:

- En lugar de tratar con NSString y [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), el tiempo de ejecución los expone C#como [cadenas](xref:System.String)y [matrices](xref:System.Array)fuertemente tipadas a través de la API.

- Aquí se muestran varias API auxiliares que permiten a los desarrolladores enlazar API de Objective-C de terceros, otras API de iOS o API que actualmente no están enlazadas con Xamarin. iOS.

Para más información sobre las API de enlace, consulte la sección [generador de enlaces de Xamarin. iOS](~/cross-platform/macios/binding/binding-types-reference.md) .

##### <a name="nsobject"></a>NSObject

El tipo [NSObject](xref:Foundation.NSObject) es la base para todos los enlaces de Objective-C. Los tipos de Xamarin. iOS reflejan dos clases de tipos de las API de CocoaTouch de iOS: los tipos de C (que normalmente se denominan tipos de CoreFoundation) y los tipos de Objective-C (todos ellos derivan de la clase NSObject).

Para cada tipo que refleje un tipo no administrado, es posible obtener el objeto nativo a través de la propiedad [Handle](xref:Foundation.NSObject.Handle) .

Aunque mono proporcionará la recolección de elementos no utilizados para todos los objetos, el `Foundation.NSObject` implementa la interfaz [System. IDisposable](xref:System.IDisposable) . Esto significa que puede liberar explícitamente los recursos de cualquier NSObject determinado sin tener que esperar a que se inicie el recolector de elementos no utilizados. Esto es importante cuando se usa una NSObjects pesada, por ejemplo, UIImages que puede contener punteros a grandes bloques de datos.

Si el tipo necesita realizar una finalización determinista, invalide el [método NSObject. Dispose (bool)](xref:Foundation.NSObject.Dispose(System.Boolean)) el parámetro para Dispose es "bool disposing" y, si se establece en true, significa que se llama al método Dispose porque el usuario llamó explícitamente a Dispose () en el objeto. Si el valor es false, significa que se llama al método Dispose (bool disposing) desde el finalizador en el subproceso finalizador.

##### <a name="categories"></a>Categorías

A partir de Xamarin. iOS 8,10 es posible crear categorías de Objective-C desde C#.

Esto se hace mediante el atributo `Category`, especificando el tipo que se va a extender como argumento al atributo. En el ejemplo siguiente, para la extensión de instancia NSString.

```csharp
[Category (typeof (NSString))]
```

Cada método de categoría usa el mecanismo normal para exportar métodos a Objective-C mediante el atributo `Export`:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia de Objective-C con la sintaxis estándar C#para los métodos de extensión de:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

y el primer argumento del método de extensión será la instancia en la que se invocó el método.

Ejemplo completo:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

En este ejemplo se agregará un método de instancia toUpper nativo a la clase NSString, que se puede invocar desde Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Un escenario en el que esto resulta útil es agregar un método a un conjunto completo de clases en el código base, por ejemplo, esto haría que todas las instancias de `UIViewController` informen que pueden girar:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute es un atributo personalizado que se usa para indicar a Mtouch (la herramienta de implementación de Xamarin. iOS) que se debe conservar un tipo, o un miembro de un tipo, durante la fase en la que se procesa la aplicación para reducir su tamaño.

Todos los miembros no vinculados de forma estática por la aplicación podrían eliminarse. Por lo tanto, este atributo se usa para marcar los miembros a los que no se hace referencia estáticamente, pero que siguen siendo necesarios para la aplicación.

Por ejemplo, si crea instancias de tipos de forma dinámica, puede que desee conservar el constructor predeterminado de sus tipos. Si usa la serialización XML, puede que desee conservar las propiedades de los tipos.

Este atributo se puede aplicar en todos los miembros de un tipo o en el propio tipo. Si desea conservar todo el tipo, puede usar la sintaxis [preserve (AllMembers = true)] en el tipo.

#### <a name="uikit"></a>UIKit

El espacio de nombres [UIKit](xref:UIKit) contiene una asignación uno a uno a todos los componentes de la interfaz de usuario que componen CocoaTouch C# en forma de clases. La API se ha modificado para seguir las convenciones usadas en el C# lenguaje.

C#los delegados se proporcionan para las operaciones comunes. Vea la sección [delegados](#delegates) para obtener más información.

#### <a name="opengles"></a>OpenGLES

En el caso de OpenGL, se distribuye una [versión modificada](xref:OpenTK) de la API de [OpenTK](https://opentk.net/) , un enlace orientado a objetos a OpenGL que se ha modificado para usar estructuras y tipos de datos CoreGraphics, además de exponer solo la funcionalidad que está disponible en iOS.

La funcionalidad OpenGL 1,1 está disponible a través del [tipo ES11.GL](xref:OpenTK.Graphics.ES11.GL).

La funcionalidad OpenGL 2,0 está disponible a través del [tipo ES20.GL](xref:OpenTK.Graphics.ES20.GL).

La funcionalidad OpenGL 3,0 está disponible a través del [tipo ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Diseño de enlace

Xamarin. iOS no es simplemente un enlace a la plataforma de Objective-C subyacente. Extiende el sistema de tipos de .NET y el sistema de distribución C# para una mejor fusión y un objetivo de C.

Del mismo modo que P/Invoke es una herramienta útil para invocar bibliotecas nativas en Windows y Linux, o como la compatibilidad de IJW se puede usar para la interoperabilidad COM en Windows, C# Xamarin. iOS amplía el tiempo de ejecución para admitir el enlace de objetos a objetos de Objective-C.

La explicación de las siguientes secciones no es necesaria para los usuarios que crean aplicaciones de Xamarin. iOS, sino que ayudarán a los desarrolladores a comprender cómo se realizan las cosas y le ayudarán a crear aplicaciones más complicadas.

#### <a name="types"></a>Tipos

En los casos en los C# que tenía sentido, los tipos se exponen en lugar de los C# tipos de base de bajo nivel al universo.  Esto significa que [la API usa el C# tipo "String" en lugar de NSString](~/ios/internals/api-design/nsstring.md) y usa C# matrices fuertemente tipadas en lugar de exponer NSArray.

En general, en el diseño de Xamarin. iOS y Xamarin. Mac, no se expone el objeto de `NSArray` subyacente. En su lugar, el tiempo de ejecución convierte automáticamente `NSArray`s en matrices fuertemente tipadas de alguna `NSObject` clase. Por lo tanto, Xamarin. iOS no expone un método débilmente tipado como GetViews para devolver un NSArray:

```csharp
NSArray GetViews ();
```

En su lugar, el enlace expone un valor devuelto fuertemente tipado, como se muestra a continuación:

```csharp
UIView [] GetViews ();
```

Hay algunos métodos que se exponen en `NSArray`, en los casos en los que podría querer usar una `NSArray` directamente, pero no se recomienda su uso en el enlace de API.

Además, en el **Classic API** en lugar de exponer `CGRect`, `CGPoint` y `CGSize` de la API de CoreGraphics, los reemplazamos por las implementaciones `System.Drawing` `RectangleF`, `PointF` y `SizeF`, ya que ayudarían a los desarrolladores a conservar el código OpenGL existente que usa OpenTK. Al usar el nuevo **Unified API**de 64 bits, se debe usar la API CoreGraphics.

#### <a name="inheritance"></a>Herencia

El diseño de la API de Xamarin. iOS permite a los desarrolladores ampliar los tipos nativos de Objective-C de la misma C# manera que extienden un tipo mediante la palabra clave "override" en una clase derivada, así como el encadenamiento hasta la implementación base C# mediante la palabra clave "base".

Este diseño permite a los desarrolladores evitar trabajar con los selectores de Objective-C como parte de su proceso de desarrollo, ya que todo el sistema Objective-C ya está encapsulado dentro de las bibliotecas de Xamarin. iOS.

#### <a name="types-and-interface-builder"></a>Tipos y Interface Builder

Al crear clases .NET que son instancias de tipos creados por Interface Builder, debe proporcionar un constructor que tome un solo parámetro `IntPtr`.
Esto es necesario para enlazar la instancia de objeto administrado con el objeto no administrado.
El código consta de una sola línea, como la siguiente:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Delegados

Objective-C y C# tienen significados diferentes para el delegado de palabra en cada idioma.

En el mundo Objective-C, y en la documentación que encontrará en línea sobre CocoaTouch, un delegado es normalmente una instancia de una clase que responderá a un conjunto de métodos. Esto es muy similar a una C# interfaz, con la diferencia de que los métodos no siempre son obligatorios.

Estos delegados desempeñan un papel importante en UIKit y otras API de CocoaTouch. Se usan para realizar varias tareas:

- Para proporcionar notificaciones al código (similar a la entrega de eventos C# en o GTK +).
- Para implementar los modelos para los controles de visualización de datos.
- Para controlar el comportamiento de un control.

El patrón de programación se diseñó para minimizar la creación de clases derivadas para modificar el comportamiento de un control. Esta solución es similar en cuanto a lo que han hecho otros kits de soluciones de GUI a lo largo de los años: señales de GTK, ranuras de QT, eventos de WinForms, eventos de WPF/Silverlight, etc. Para evitar tener cientos de interfaces (una para cada acción) o requerir a los desarrolladores que implementen demasiados métodos que no necesitan, Objective-C admite definiciones de método opcionales. Esto es diferente de C# las interfaces que requieren que se implementen todos los métodos.

En las clases de Objective-C, verá que las clases que usan este patrón de programación exponen una propiedad, normalmente denominada `delegate`, que es necesaria para implementar las partes obligatorias de la interfaz y cero, o más, de los elementos opcionales.

En Xamarin. iOS se ofrecen tres mecanismos mutuamente excluyentes para enlazar a estos delegados:

1. [Eventos Via](#via-events).
2. [Con establecimiento inflexible de tipos a través de una propiedad `Delegate`](#strongly-typed-via-a-delegate-property)
3. [Con tipo flexible a través de una propiedad `WeakDelegate`](#loosely-typed-via-the-weakdelegate-property)

Por ejemplo, considere la clase UIWebView. Esto se envía a una instancia de UIWebViewDelegate, que se asigna a la propiedad del delegado.

##### <a name="via-events"></a>Eventos Via

Para muchos tipos, Xamarin. iOS creará automáticamente un delegado adecuado que reenviará las llamadas `UIWebViewDelegate` C# a los eventos. Para `UIWebView`:

- El método webViewDidStartLoad se asigna al evento [UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted) .
- El método webViewDidFinishLoad se asigna al evento [UIWebView. LoadFinished](xref:UIKit.UIWebView.LoadFinished) .
- El método WebView: didFailLoadWithError se asigna al evento [UIWebView. LoadError](xref:UIKit.UIWebView.LoadError) .

Por ejemplo, este sencillo programa registra las horas de inicio y finalización al cargar una vista Web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Propiedades Via

Los eventos son útiles cuando puede haber más de un suscriptor al evento. Además, los eventos se limitan a los casos en los que no hay ningún valor devuelto del código.

En los casos en los que se espera que el código devuelva un valor, hemos optado por propiedades. Esto significa que solo se puede establecer un método en un momento dado en un objeto.

Por ejemplo, puede usar este mecanismo para descartar el teclado en la pantalla del controlador de un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

En este caso, la propiedad `ShouldReturn` del `UITextField`toma como argumento un delegado que devuelve un valor booleano y determina si el TextField debe hacer algo con el botón de retorno que se presiona. En nuestro método, se devuelve *true* al llamador, pero también se quita el teclado de la pantalla (esto sucede cuando el TextField llama a `ResignFirstResponder`).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fuertemente tipado a través de una propiedad de delegado

Si prefiere no usar eventos, puede proporcionar su propia subclase [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) y asignarla a la propiedad [UIWebView. Delegate](xref:UIKit.UIWebView.Delegate) . Una vez que se ha asignado UIWebView. Delegate, el mecanismo de envío de eventos UIWebView dejará de funcionar y los métodos UIWebViewDelegate se invocarán cuando se produzcan los eventos correspondientes.

Por ejemplo, este tipo simple registra el tiempo que se tarda en cargar una vista Web:

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

Lo anterior se usa en código como el siguiente:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Lo anterior creará un UIWebViewer y le indicará que envíe mensajes a una instancia del notificador, una clase que hemos creado para responder a los mensajes.

Este patrón también se usa para controlar el comportamiento de ciertos controles, por ejemplo, en el caso de UIWebView, la propiedad [UIWebView. ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) permite a la instancia de `UIWebView` controlar si el `UIWebView` cargará o no una página.

El patrón también se usa para proporcionar los datos a petición para algunos controles. Por ejemplo, el control [UITableView](xref:UIKit.UITableView) es un control de representación de tabla eficaz, y el aspecto y el contenido están controlados por una instancia de [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Con tipo flexible a través de la propiedad WeakDelegate

Además de la propiedad fuertemente tipada, también hay un delegado con tipo débil que permite al desarrollador enlazar cosas de manera diferente si se desea.
En todas partes, una propiedad fuertemente tipada `Delegate` se expone en el enlace de Xamarin. iOS, también se expone una propiedad `WeakDelegate` correspondiente.

Al utilizar el `WeakDelegate`, usted es responsable de decorar correctamente la clase mediante el atributo de [exportación](xref:Foundation.ExportAttribute) para especificar el selector. Por ejemplo:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Tenga en cuenta que una vez que se ha asignado la propiedad `WeakDelegate`, no se utilizará la propiedad `Delegate`. Además, si implementa el método en una clase base heredada que desea [exportar], debe convertirlo en un método público.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Asignación del patrón de delegado de Objective-C a C\#

Cuando vea ejemplos de Objective-C que tienen el siguiente aspecto:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Esto indica al lenguaje que cree y construya una instancia de la clase "SomethingDelegate" y asigne el valor a la propiedad de delegado en la variable foo. Este mecanismo es compatible con Xamarin. iOS y C# la sintaxis es:

```csharp
foo.Delegate = new SomethingDelegate ();
```

En Xamarin. iOS hemos proporcionado clases fuertemente tipadas que se asignan a las clases de delegado de Objective-C. Para usarlos, creará subclases e invalidará los métodos definidos por la implementación de Xamarin. iOS. Para obtener más información sobre cómo funcionan, vea la sección "modelos" a continuación.

### <a name="mapping-delegates-to-c"></a>Asignar delegados a C\#

UIKit en general utiliza delegados de Objective-C de dos formas.

El primer formulario proporciona una interfaz al modelo de un componente. Por ejemplo, como mecanismo para proporcionar datos a petición para una vista, como la instalación de almacenamiento de datos para una vista de lista.  En estos casos, siempre debe crear una instancia de la clase adecuada y asignar la variable.

En el ejemplo siguiente, se proporciona la `UIPickerView` con una implementación para un modelo que utiliza cadenas:

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

La segunda forma es proporcionar una notificación para los eventos. En esos casos, aunque todavía exponemos la API en el formato descrito anteriormente, también proporcionamos C# eventos, que deben ser más fáciles de usar para las operaciones rápidas e integrarse con delegados anónimos C#y expresiones lambda en.

Por ejemplo, puede suscribirse a eventos de `UIAccelerometer`:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Las dos opciones están disponibles cuando tienen sentido, pero como programador debe elegir una u otra. Si crea su propia instancia de un respondedor o delegado fuertemente tipado y la asigna, los C# eventos no funcionarán. Si usa los C# eventos, nunca se llamará a los métodos de la clase de respuesta/delegado.

El ejemplo anterior que usaba `UIWebView` se puede escribir con C# las expresiones lambda 3,0 como esta:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Respuesta a eventos

En el código de Objective-C, a veces los controladores de eventos para varios controles y proveedores de información para varios controles, se hospedarán en la misma clase. Esto es posible porque las clases responden a los mensajes, mientras que las clases responden a los mensajes, es posible vincular los objetos entre sí.

Como se detailó anteriormente, Xamarin. iOS C# admite tanto el modelo de programación basado en eventos como el patrón de delegado de Objective-C, donde puede crear una nueva clase que implemente el delegado e invalide los métodos deseados.

También es posible admitir el patrón de Objective-C, donde todos los respondedores de varias operaciones diferentes se hospedan en la misma instancia de una clase. Si no es así, tendrá que usar características de bajo nivel del enlace de Xamarin. iOS.

Por ejemplo, si desea que la clase responda al mensaje `UITextFieldDelegate.textFieldShouldClear`: y al `UIWebViewDelegate.webViewDidStartLoad`: en la misma instancia de una clase, tendría que usar la declaración de atributo [Export]:

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

Los C# nombres de los métodos no son importantes; lo único que importa son las cadenas que se pasan al atributo [Export].

Al usar este estilo de programación, asegúrese de que C# los parámetros coincidan con los tipos reales que el motor de tiempo de ejecución pasará.

#### <a name="models"></a>Modelos

En las instalaciones de almacenamiento de UIKit, o en los servicios de respuesta que se implementan mediante clases auxiliares, normalmente se hace referencia a ellos en el código de Objective-C como delegados y se implementan como protocolos.

Los protocolos Objective-C son similares a las interfaces, pero admiten métodos opcionales, es decir, no es necesario implementar todos los métodos para que el protocolo funcione.

Hay dos formas de implementar un modelo. Puede implementarlo manualmente o usar las definiciones fuertemente tipadas existentes.

El mecanismo manual es necesario cuando se intenta implementar una clase que no está enlazada con Xamarin. iOS. Es muy fácil de hacer:

- Marcar la clase para el registro en tiempo de ejecución
- Aplique el atributo [Export] con el nombre de selector real en cada método que desee invalidar.
- Cree una instancia de la clase y pásela.

Por ejemplo, lo siguiente implementa solo uno de los métodos opcionales en la definición del protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

El nombre del selector Objective-C ("applicationDidFinishLaunching:") se declara con el atributo export y la clase se registra con el atributo `[Register]`.

Xamarin. iOS proporciona declaraciones fuertemente tipadas, listas para usar, que no requieren enlace manual. Para admitir este modelo de programación, el tiempo de ejecución de Xamarin. iOS admite el atributo [Model] en una declaración de clase. Esto informa al tiempo de ejecución de que no debe conectar todos los métodos de la clase, a menos que se implementen explícitamente los métodos.

Esto significa que en UIKit, las clases que representan un protocolo con métodos opcionales se escriben de la siguiente manera:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Cuando desee implementar un modelo que solo implemente algunos de los métodos, lo único que tiene que hacer es invalidar los métodos que le interesen y omitir los demás métodos. El Runtime solo enlazará los métodos sobrescritos, no los métodos originales al mundo Objective-C.

El equivalente al ejemplo manual anterior es:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Las ventajas son que no es necesario profundizar en los archivos de encabezado de Objective-C para buscar el selector, los tipos de los argumentos o la asignación a C#y que se obtiene intellisense de Visual Studio para Mac, junto con tipos seguros.

#### <a name="xib-outlets-and-c"></a>Salidas de XIB y C\#

> [!IMPORTANT]
> En esta sección se explica la integración del IDE con las salidas al usar archivos XIB. Al usar el Xamarin Designer para iOS, todo esto se reemplazará escribiendo un nombre en **identidad > nombre** en la sección Propiedades del IDE, como se muestra a continuación:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Para más información sobre el diseñador de iOS, consulte la [Introducción al documento de iOS Designer](~/ios/user-interface/designer/introduction.md#how-it-works) .

Se trata de una descripción de bajo nivel de cómo las salidas se C# integran con y se proporcionan para los usuarios avanzados de Xamarin. iOS. Cuando se usa Visual Studio para Mac la asignación se realiza automáticamente en segundo plano mediante el código generado en el vuelo.

Al diseñar la interfaz de usuario con Interface Builder, solo tendrá que diseñar la apariencia de la aplicación y establecerá algunas conexiones predeterminadas. Si desea obtener información mediante programación, modificar el comportamiento de un control en tiempo de ejecución o modificar el control en tiempo de ejecución, es necesario enlazar algunos de los controles al código administrado.

Esto se realiza en algunos pasos:

1. Agregue la **declaración** de la salida al **propietario del archivo**.
1. Conecte el control al **propietario del archivo**.
1. Almacene la interfaz de usuario más las conexiones en el archivo XIB/NIB.
1. Cargue el archivo NIB en tiempo de ejecución.
1. Acceda a la variable de salida.

Los pasos (1) a (3) se describen en la documentación de Apple para la creación de interfaces con Interface Builder.

Al usar Xamarin. iOS, la aplicación necesitará crear una clase que se derive de UIViewController. Se implementa como esta:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Después, para cargar el ViewController desde un archivo NIB, haga lo siguiente:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Esto carga la interfaz de usuario desde NIB. Ahora, para tener acceso a las salidas, es necesario informar al tiempo de ejecución de que queremos acceder a ellas. Para ello, la subclase `UIViewController` debe declarar las propiedades y anotarlas con el atributo [Connect]. Por ejemplo:

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

La implementación de la propiedad es aquella que realmente captura y almacena el valor para el tipo nativo real.

No es necesario preocuparse de esto al usar Visual Studio para Mac y InterfaceBuilder. Visual Studio para Mac refleja automáticamente todas las salidas declaradas con código en una clase parcial que se compila como parte del proyecto.

#### <a name="selectors"></a>Selectores

Un concepto básico de la programación de Objective-C es selectores. A menudo, se incluirán las API que requieren que se pase un selector o se espera que el código responda a un selector.

Crear nuevos selectores C# en es muy fácil: solo tiene que crear una nueva instancia de la clase `ObjCRuntime.Selector` y usar el resultado en cualquier lugar de la API que lo requiera. Por ejemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para que C# un método responda a una llamada de selector, debe heredar del tipo `NSObject` y C# el método se debe decorar con el nombre del selector mediante el atributo `[Export]`. Por ejemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Tenga en cuenta que los nombres de los selectores **deben** coincidir exactamente, incluidos todos los dos puntos entre el medio y el final (":"), si están presentes.

#### <a name="nsobject-constructors"></a>Constructores NSObject

La mayoría de las clases de Xamarin. iOS que se derivan de `NSObject` expondrán constructores específicos de la funcionalidad del objeto, pero también expondrán varios constructores que no son obvios de inmediato.

Los constructores se usan de la siguiente manera:

```csharp
public Foo (IntPtr handle)
```

Este constructor se usa para crear una instancia de la clase cuando el tiempo de ejecución necesita asignar la clase a una clase no administrada. Esto sucede cuando se carga un archivo XIB/NIB.  En este momento, el tiempo de ejecución de Objective-C habrá creado un objeto en el mundo no administrado y se llamará a este constructor para inicializar el lado administrado.

Normalmente, todo lo que necesita hacer es llamar al constructor base con el parámetro Handle y, en el cuerpo, realizar cualquier inicialización necesaria.

```csharp
public Foo ()
```

Este es el constructor predeterminado para una clase y en las clases proporcionadas por Xamarin. iOS, esto inicializa la clase Foundation. NSObject y todas las clases entre y, al final, encadena este al método `init` de Objective-C en la clase.

```csharp
public Foo (NSObjectFlag x)
```

Este constructor se usa para inicializar la instancia, pero evitar que el código llame al método Objective-C "init" al final. Normalmente se utiliza cuando ya se ha registrado para la inicialización (cuando se usa `[Export]` en el constructor) o cuando ya se ha realizado la inicialización a través de otro medio.

```csharp
public Foo (NSCoder coder)
```

Este constructor se proporciona para los casos en los que el objeto se inicializa desde una instancia de NSCoding.

#### <a name="exceptions"></a>Excepciones

El diseño de la API de Xamarin. iOS no genera excepciones de Objective C# -C como excepciones. El diseño exige que no se envíen elementos no utilizados al mundo de Objective-C en primer lugar y que cualquier excepción que se deba producir se produzca por el propio enlace antes de que se pasen datos no válidos al mundo de Objective-C.

#### <a name="notifications"></a>Notificaciones

Tanto en iOS como en OS X, los desarrolladores pueden suscribirse a las notificaciones que emite la plataforma subyacente. Esto se hace mediante el método `NSNotificationCenter.DefaultCenter.AddObserver`. El método `AddObserver` toma dos parámetros: una es la notificación a la que desea suscribirse; el otro es el método que se va a invocar cuando se produzca la notificación.

En Xamarin. iOS y Xamarin. Mac, las claves para las distintas notificaciones se hospedan en la clase que desencadena las notificaciones. Por ejemplo, las notificaciones generadas por el `UIMenuController` se hospedan como `static NSString` propiedades en las clases `UIMenuController` que terminan con el nombre "Notification".

### <a name="memory-management"></a>Administración de la memoria

Xamarin. iOS tiene un recolector de elementos no utilizados que se encargará de liberar recursos cuando ya no estén en uso. Además del recolector de elementos no utilizados, todos los objetos que derivan de `NSObject` implementan la interfaz `System.IDisposable`.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Exponer la interfaz de `IDisposable` es una forma cómoda de ayudar a los desarrolladores a liberar objetos que podrían encapsular grandes bloques de memoria (por ejemplo, un `UIImage` podría parecer simplemente un puntero inocente, pero podría apuntar a una imagen de 2 megabytes) y otros recursos importantes y finitos (como un búfer de descodificación de vídeo).

NSObject implementa la interfaz IDisposable y también el [patrón de Dispose de .net](https://msdn.microsoft.com/library/fs2xkftw.aspx). Esto permite a los desarrolladores que subclases NSObject invalidar el comportamiento de Dispose y liberar sus propios recursos a petición. Por ejemplo, considere este controlador de vistas que se mantiene en torno a un montón de imágenes:

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Cuando se elimina un objeto administrado, ya no es útil. Es posible que todavía tenga una referencia a los objetos, pero el objeto es para todos los intentos y propósitos no válidos en este momento. Algunas API de .NET lo comprueban iniciando una excepción ObjectDisposedException si intenta obtener acceso a cualquier método en un objeto desechado, por ejemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Incluso si todavía puede acceder a la variable "Image", es realmente una referencia no válida y ya no apunta al objeto de Objective-C que contenía la imagen.

Pero desechar un objeto en C# no significa que el objeto se destruya necesariamente. Lo único que tiene que hacer es liberar C# la referencia que tenía en el objeto. Es posible que el entorno de Cocoa haya mantenido una referencia en torno a su propio uso. Por ejemplo, si establece la propiedad de imagen de UIImageView en una imagen y, a continuación, desecha la imagen, el UIImageView subyacente habría tomado su propia referencia y mantendrá una referencia a este objeto hasta que termine de usarlo.

#### <a name="when-to-call-dispose"></a>Cuándo se debe llamar a Dispose

Debe llamar a Dispose cuando necesite mono para deshacerse del objeto. Un caso de uso posible es cuando mono no tiene conocimiento de que el NSObject realmente contiene una referencia a un recurso importante, como la memoria o un grupo de información. En esos casos, debe llamar a Dispose para liberar inmediatamente la referencia a la memoria, en lugar de esperar a que mono realice un ciclo de recolección de elementos no utilizados.

Internamente, cuando mono crea [referencias de NSString C# a partir de cadenas, las](~/ios/internals/api-design/nsstring.md)eliminará inmediatamente para reducir la cantidad de trabajo que debe realizar el recolector de elementos no utilizados. Cuantos menos objetos haya que tratar, más rápido se ejecutará el GC.

#### <a name="when-to-keep-references-to-objects"></a>Cuándo mantener las referencias a los objetos

Uno de los efectos secundarios de la administración automática de la memoria es que el GC se deshará de los objetos no utilizados siempre y cuando no haya referencias a ellos. En ocasiones, esto puede tener efectos secundarios sorprendentes, por ejemplo, si crea una variable local para que contenga el controlador de vista de nivel superior, o la ventana de nivel superior, y después vuelva a dejarlos por detrás.

Si no se mantiene una referencia en las variables estáticas o de instancia a los objetos, mono llamará al método Dispose () y liberará la referencia al objeto. Como esto podría ser la única referencia pendiente, el entorno de tiempo de ejecución de Objective-C destruirá el objeto automáticamente.

## <a name="related-links"></a>Vínculos relacionados

- [Campos de enlace](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
