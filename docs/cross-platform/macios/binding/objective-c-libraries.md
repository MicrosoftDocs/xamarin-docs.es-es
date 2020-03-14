---
title: Enlace de bibliotecas de Objective-C
description: En este documento se proporciona información general de alto nivel sobre cómo C# crear enlaces a código de Objective-C, donde se describe cómo enlazar eventos, métodos, controles personalizados, etc.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306030"
---
# <a name="binding-objective-c-libraries"></a>Enlace de bibliotecas de Objective-C

Al trabajar con Xamarin. iOS o Xamarin. Mac, es posible que se produzcan casos en los que desee usar una biblioteca de Objective-C de terceros. En esas situaciones, puede usar proyectos de enlace de Xamarin para crear C# un enlace a las bibliotecas nativas de Objective-C. El proyecto usa las mismas herramientas que usamos para incorporar las API de iOS y Mac C#.

En este documento se describe cómo enlazar API de Objective-C, si solo enlaza las API de C, debe usar el mecanismo estándar de .NET para esto, [el marco de trabajo P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Los detalles sobre cómo vincular estáticamente una biblioteca de C están disponibles en la página [vinculación de bibliotecas nativas](~/ios/platform/native-interop.md) .

Consulte nuestra [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)complementarios.
Además, si desea obtener más información sobre lo que sucede en el capó, consulte nuestra página de [información general](~/cross-platform/macios/binding/overview.md) sobre el enlace.

Los enlaces se pueden compilar para las bibliotecas de iOS y Mac.
En esta página se describe cómo trabajar en un enlace de iOS; sin embargo, los enlaces de Mac son muy similares.

**Código de ejemplo para iOS**

Puede usar el proyecto de [ejemplo enlace de iOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) para experimentar con enlaces.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introducción

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

La forma más fácil de crear un enlace es crear un proyecto de enlace de Xamarin. iOS.
Puede hacerlo desde Visual Studio para Mac seleccionando el tipo de proyecto, la biblioteca de **> de iOS > biblioteca de enlaces**:

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

La forma más fácil de crear un enlace es crear un proyecto de enlace de Xamarin. iOS.
Puede hacerlo desde Visual Studio en Windows seleccionando el tipo de proyecto, **visual C# > iOS > biblioteca de enlaces (iOS)** :

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: los proyectos de enlace para **Xamarin. Mac** solo se admiten en Visual Studio para Mac.

-----

El proyecto generado contiene una plantilla pequeña que se puede editar, que contiene dos archivos: `ApiDefinition.cs` y `StructsAndEnums.cs`.

En el `ApiDefinition.cs` es donde definirá el contrato de API, este es el archivo que describe cómo se proyecta la API de Objective-C C#subyacente en. La sintaxis y el contenido de este archivo son el tema principal de la explicación de este documento y el contenido de este es C# limitado a C# las interfaces y a las declaraciones de delegado. El archivo `StructsAndEnums.cs` es el archivo en el que se especificarán las definiciones que necesiten las interfaces y los delegados. Esto incluye los valores de enumeración y las estructuras que puede usar el código.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Enlazar una API

Para realizar un enlace completo, querrá comprender la definición de la API de Objective-C y familiarícese con las directrices de diseño de .NET Framework.

Para enlazar la biblioteca, normalmente se inicia con un archivo de definición de API. Un archivo de definición de API es C# simplemente un archivo de C# código fuente que contiene interfaces anotadas con una serie de atributos que ayudan a impulsar el enlace.  Este archivo es lo que define el contrato entre C# y Objective-C.

Por ejemplo, se trata de un archivo de API trivial para una biblioteca:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

En el ejemplo anterior se define una clase denominada `Cocos2D.Camera` que se deriva del tipo base `NSObject` (este tipo procede de `Foundation.NSObject`) y que define una propiedad estática (`ZEye`), dos métodos que no toman argumentos y un método que toma tres argumentos.

En la sección [archivo de definición](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) de la API siguiente, se describe detalladamente el formato del archivo de API y los atributos que puede usar.

Para generar un enlace completo, normalmente se tratan cuatro componentes:

- El archivo de definición de la API (`ApiDefinition.cs` en la plantilla).
- Opcional: cualquier enumeración, tipo, Structs que requiera el archivo de definición de la API (`StructsAndEnums.cs` en la plantilla).
- Opcional: orígenes adicionales que pueden expandir el enlace generado o proporcionar una API más C# descriptiva (cualquier C# archivo que se agregue al proyecto).
- Biblioteca nativa que se va a enlazar.

En este gráfico se muestra la relación entre los archivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

El archivo de definición de API solo contendrá espacios de nombres y definiciones de interfaz (con cualquier miembro que una interfaz pueda contener) y no debe contener clases, enumeraciones, delegados o Structs. El archivo de definición de la API es simplemente el contrato que se usará para generar la API.

Cualquier código adicional que se necesite como enumeraciones o clases auxiliares se debe hospedar en un archivo independiente, en el ejemplo anterior, "CameraMode" es un valor de enumeración que no existe en el archivo CS y se debe hospedar en un archivo independiente, por ejemplo `StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

El archivo `APIDefinition.cs` se combina con la clase `StructsAndEnum` y se utiliza para generar el enlace básico de la biblioteca. Puede usar la biblioteca resultante tal cual, pero por lo general, querrá optimizar la biblioteca resultante para agregar algunas C# características para las ventajas de los usuarios. Entre los ejemplos se incluye la implementación de un C# método `ToString()`, la inclusión de indexadores, la adición de conversiones implícitas a y desde algunos tipos nativos o la entrega de versiones fuertemente tipadas de algunos métodos. Estas mejoras se almacenan en C# archivos adicionales. Simplemente agregue los C# archivos al proyecto y se incluirán en este proceso de compilación.

Esto muestra cómo se implementaría el código en el archivo de `Extra.cs`. Tenga en cuenta que usará clases parciales a medida que aumentan las clases parciales que se generan a partir de la combinación de los `ApiDefinition.cs` y el enlace de `StructsAndEnums.cs` Core:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Al compilar la biblioteca se generará el enlace nativo.

Para completar este enlace, debe agregar la biblioteca nativa al proyecto.  Puede hacerlo agregando la biblioteca nativa al proyecto, arrastrando y colocando la biblioteca nativa desde Finder en el proyecto en el explorador de soluciones, o haciendo clic con el botón derecho en el proyecto y eligiendo **agregar** > **Agregar archivos** para seleccionar la biblioteca nativa.
Las bibliotecas nativas por Convención comienzan con la palabra "lib" y terminan con la extensión ". a". Al hacerlo, Visual Studio para Mac agregará dos archivos: el archivo. a y un archivo rellenado C# automáticamente que contiene información acerca de lo que contiene la biblioteca nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

El contenido del archivo de `libMagicChord.linkwith.cs` contiene información sobre cómo se puede usar esta biblioteca e indica al IDE que empaquete este binario en el archivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Detalles completos sobre cómo usar el [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
el atributo se documenta en la [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md).

Ahora, al compilar el proyecto, terminará con un archivo `MagicChords.dll` que contiene el enlace y la biblioteca nativa. Puede distribuir este proyecto o el archivo DLL resultante a otros desarrolladores para su propio uso.

En ocasiones, es posible que necesite algunos valores de enumeración, definiciones de delegado u otros tipos. No los coloque en el archivo de definiciones de la API, ya que se trata simplemente de un contrato

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>El archivo de definición de la API

El archivo de definición de la API consta de varias interfaces. Las interfaces de la definición de la API se convertirán en una declaración de clase y se deben decorar con el atributo [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) para especificar la clase base para la clase.

Es posible que se pregunte por qué no usamos clases en lugar de interfaces para la definición del contrato. Hemos seleccionado interfaces porque nos permitió escribir el contrato para un método sin tener que proporcionar un cuerpo de método en el archivo de definición de la API, o tener que proporcionar un cuerpo que tuviera que producir una excepción o devolver un valor significativo.

Sin embargo, puesto que usamos la interfaz como esqueleto para generar una clase, tuvimos que recurrir para decorar varias partes del contrato con atributos para impulsar el enlace.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de enlace

El enlace más sencillo que puede hacer es enlazar un método. Basta con declarar un método en la interfaz con C# las convenciones de nomenclatura y decorar el método con la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
. El atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) es lo que vincula C# su nombre con el nombre de Objective-C en el tiempo de ejecución de Xamarin. iOS. Parámetro del [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
Attribute es el nombre del selector Objective-C. Algunos ejemplos:

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

En los ejemplos anteriores se muestra cómo se pueden enlazar métodos de instancia. Para enlazar métodos estáticos, debe usar el atributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) , de la siguiente manera:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Esto es necesario porque el contrato forma parte de una interfaz, y las interfaces no tienen ninguna noción de declaraciones de instancia de vs estáticas, por lo que es necesario volver a recurrir a los atributos. Si desea ocultar un método determinado del enlace, puede decorar el método con el [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

El comando `btouch-native` introducirá comprobaciones para que los parámetros de referencia no sean null. Si desea permitir valores NULL para un parámetro determinado, use el [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
en el parámetro, de la siguiente manera:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Al exportar un tipo de referencia, con la palabra clave [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) también puede especificar la semántica de asignación. Esto es necesario para garantizar que no se pierde ningún dato.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propiedades de enlace

Al igual que los métodos, las propiedades de Objective-C se enlazan mediante el [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
Attribute y se asignan C# directamente a propiedades. Al igual que los métodos, las propiedades se pueden decorar con el [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
y el [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Cuando se usa el atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) en una propiedad en el btouch-Native, en realidad enlaza dos métodos: el captador y el establecedor. El nombre que se proporciona para exportar es el **basename** y el establecedor se calcula anteponiendo la palabra "SET", desactivando la primera **letra del nombre de grupo en** mayúsculas y haciendo que el selector tome un argumento. Esto significa que `[Export ("label")]` aplicado en una propiedad enlaza realmente los métodos "Label" y "setLabel:" Objective-C.

A veces, las propiedades de Objective-C no siguen el patrón descrito anteriormente y el nombre se sobrescribe manualmente. En estos casos, puede controlar la manera en que se genera el enlace mediante el [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
atributo del captador o establecedor, por ejemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

A continuación, enlaza "isMenuVisible" y "setMenuVisible:". Opcionalmente, se puede enlazar una propiedad con la sintaxis siguiente:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

Donde el captador y el establecedor se definen explícitamente como en los enlaces de `name` y `setName` anteriores.

Además de admitir las propiedades estáticas mediante [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), puede decorar las propiedades estáticas de los subprocesos con [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), por ejemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Al igual que los métodos permiten marcar algunos parámetros con [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), puede aplicar [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
en una propiedad para indicar que NULL es un valor válido para la propiedad, por ejemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

También se puede especificar el parámetro [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) directamente en el establecedor:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Advertencias de los controles personalizados de enlace

Se deben tener en cuenta las siguientes advertencias al configurar el enlace de un control personalizado:

1. **Las propiedades de enlace deben ser estáticas** : al definir el enlace de propiedades, se debe usar el atributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) .
2. Los **nombres de propiedad deben coincidir exactamente** ; el nombre utilizado para enlazar la propiedad debe coincidir exactamente con el nombre de la propiedad del control personalizado.
3. Los **tipos de propiedad deben coincidir exactamente** : el tipo de variable que se usa para enlazar la propiedad debe coincidir exactamente con el tipo de propiedad del control personalizado.
4. Nunca se alcanzarán **los puntos de interrupción y los** puntos de interrupción del captador o establecedor colocados en los métodos captador o establecedor de la propiedad.
5. **Observar devoluciones de llamada** : deberá utilizar las devoluciones de llamada de observación para recibir notificaciones de los cambios en los valores de propiedad de los controles personalizados.

Si no se observa ninguna de las advertencias enumeradas anteriormente, se puede producir un error en el enlace en tiempo de ejecución.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Patrón y propiedades mutable de Objective-C

Los marcos de trabajo de Objective-C usan una expresión en la que algunas clases son inmutables con una subclase mutable. Por ejemplo `NSString` es la versión inmutable, mientras que `NSMutableString` es la subclase que permite la mutación.

En esas clases es habitual ver que la clase base inmutable contiene propiedades con un captador, pero ningún establecedor. Y para que la versión mutable Introduzca el establecedor. Dado que esto no es realmente posible C#con, teníamos que asignar esta expresión a una expresión que funcionaría con C#.

La forma en que se asigna a C# esto es agregando el captador y el establecedor en la clase base, pero marcando el establecedor con una [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
.

A continuación, en la subclase mutable, use el [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
atributo de la propiedad para asegurarse de que la propiedad está invalidando realmente el comportamiento del elemento primario.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Constructores de enlace

La herramienta de `btouch-native` generará automáticamente cuatro constructores en la clase, para una clase determinada `Foo`, genera:

- `Foo ()`: el constructor predeterminado (se asigna al constructor "init" de Objective-C)
- `Foo (NSCoder)`: el constructor que se usa durante la deserialización de archivos NIB (se asigna al constructor "initWithCoder:" de Objective-C).
- `Foo (IntPtr handle)`: el constructor para la creación basada en identificadores, que el Runtime invoca cuando el tiempo de ejecución necesita exponer un objeto administrado desde un objeto no administrado.
- `Foo (NSEmptyFlag)`: se usa en las clases derivadas para evitar la inicialización doble.

En el caso de los constructores que defina, se deben declarar con la siguiente firma dentro de la definición de interfaz: deben devolver un valor `IntPtr` y el nombre del método debe ser constructor. Por ejemplo, para enlazar el constructor de `initWithFrame:`, esto es lo que usaría:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de enlace

Como se describe en el documento de diseño de la API, en la sección sobre [modelos y protocolos](~/ios/internals/api-design/index.md#models), Xamarin. iOS asigna los protocolos de Objective-C a las clases que se han marcado con el [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
. Normalmente se usa al implementar clases de delegado de Objective-C.

La gran diferencia entre una clase enlazada normal y una clase delegada es que la clase delegada podría tener uno o más métodos opcionales.

Por ejemplo, considere la `UIAccelerometerDelegate`de `UIKit` clase, así es como se enlaza en Xamarin. iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Dado que se trata de un método opcional en la definición de `UIAccelerometerDelegate` no hay nada más que hacer. Pero si hubiera un método necesario en el protocolo, debería agregar el [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
atributo del método. Esto obligará al usuario de la implementación a proporcionar realmente un cuerpo para el método.

En general, los protocolos se usan en las clases que responden a los mensajes. Esto se hace normalmente en Objective-C asignando a la propiedad "Delegate" una instancia de un objeto que responde a los métodos del protocolo.

La Convención en Xamarin. iOS es admitir el estilo de acoplamiento flexible de Objective-C en el que se puede asignar cualquier instancia de un `NSObject` al delegado y también exponer una versión fuertemente tipada. Por esta razón, normalmente proporcionamos una `Delegate` propiedad fuertemente tipada y una `WeakDelegate` que tiene un tipo flexible. Normalmente enlazamos la versión con tipo flexible con [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)y usamos el atributo [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) para proporcionar la versión fuertemente tipada.

Esto muestra cómo se enlaza la clase `UIAccelerometer`:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Novedades de MonoTouch 7,0**

A partir de MonoTouch 7,0 se ha incorporado una funcionalidad de enlace de protocolo nueva y mejorada.  Esta nueva compatibilidad facilita el uso de expresiones de Objective-C para adoptar uno o más protocolos en una clase determinada.

Para cada definición de protocolo `MyProtocol` en Objective-C, ahora hay una interfaz `IMyProtocol` que enumera todos los métodos necesarios del Protocolo, así como una clase de extensión que proporciona todos los métodos opcionales.  Lo anterior, combinado con la nueva compatibilidad en el editor de Xamarin Studio permite a los desarrolladores implementar métodos de protocolo sin tener que usar las subclases independientes de las clases de modelo abstracto anteriores.

Cualquier definición que contenga el atributo [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) generará tres clases compatibles que mejoran en gran medida la forma en que se consumen los protocolos:

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

La **implementación** de la clase proporciona una clase abstracta completa que puede invalidar métodos individuales de y obtener seguridad de tipos completa.  Pero debido a C# que no se admite la herencia múltiple, hay escenarios en los que es posible que necesite tener una clase base diferente, pero que aún desea implementar una interfaz, es decir, donde

La **definición de interfaz** generada entra en.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite a los desarrolladores que desean implementar el protocolo implementar simplemente la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como la adopción del protocolo.

Observe que la interfaz solo enumera los métodos necesarios y expone los métodos opcionales.  Esto significa que las clases que adopten el protocolo obtendrán una comprobación completa de tipos para los métodos necesarios, pero tendrán que recurrir a tipos débiles (con [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributos y coincidencia de la firma) para los métodos de protocolo opcionales.

Para que sea conveniente usar una API que use protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.  Esto significa que, siempre que esté consumiendo una API, podrá tratar los protocolos como si tuvieran todos los métodos.

Si desea usar las definiciones de protocolo en la API, deberá escribir las interfaces vacías del esqueleto en la definición de la API.  Si desea utilizar el protocolo de la API, deberá hacer lo siguiente:

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

Lo anterior es necesario porque en el momento del enlace el `IMyProtocol` no existía, por eso es necesario proporcionar una interfaz vacía.

#### <a name="adopting-protocol-generated-interfaces"></a>Adopción de interfaces generadas por el protocolo

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

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Enlazar extensiones de clase

En Objective-C es posible extender clases con nuevos métodos, similares en el espíritu de los C#métodos de extensión de. Cuando uno de estos métodos esté presente, puede usar la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
atributo para marcar el método como receptor del mensaje de Objective-C.

Por ejemplo, en Xamarin. iOS enlazamos los métodos de extensión que se definen en `NSString` cuando `UIKit` se importa como métodos en el `NSStringDrawingExtensions`, como se indica a continuación:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Enlazar listas de argumentos de Objective-C

Objective-C admite argumentos de variádicas. Por ejemplo:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para invocar este método C# desde, querrá crear una firma similar a la siguiente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Esto declara el método como interno y oculta la API anterior de los usuarios, pero la expone a la biblioteca. Después, puede escribir un método similar al siguiente:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>Campos de enlace

A veces, querrá acceder a los campos públicos declarados en una biblioteca.

Normalmente, estos campos contienen cadenas o valores enteros a los que se debe hacer referencia. Normalmente se usan como una cadena que representa una notificación específica y como claves en diccionarios.

Para enlazar un campo, agregue una propiedad al archivo de definición de interfaz y decore la propiedad con el [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo. Este atributo toma un parámetro: el nombre de C del símbolo que se va a buscar. Por ejemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si desea ajustar varios campos en una clase estática que no se deriva de `NSObject`, puede usar el [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
en la clase, de la siguiente manera:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Lo anterior generará un `LonelyClass` que no se deriva de `NSObject` y contendrá un enlace al `NSSomeEventNotification`
`NSString` expuesto como `NSString`.

El atributo [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) se puede aplicar a los siguientes tipos de datos:

- referencias de `NSString` (solo propiedades de solo lectura)
- referencias de `NSArray` (solo propiedades de solo lectura)
- int de 32 bits (`System.Int32`)
- int de 64 bits (`System.Int64`)
- floats de 32 bits (`System.Single`)
- floats de 64 bits (`System.Double`)
- `System.Drawing.SizeF`
- `CGSize`

Además del nombre de campo nativo, puede especificar el nombre de la biblioteca donde se encuentra el campo, pasando el nombre de la biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Si vincula estáticamente, no hay ninguna biblioteca a la que enlazar, por lo que debe usar el nombre del `__Internal`:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumeración de enlaces

Puede Agregar `enum` directamente en los archivos de enlace para facilitar su uso en las definiciones de API: sin usar un archivo de origen diferente (que debe compilarse en los enlaces y en el proyecto final).

Ejemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

También es posible crear sus propias enumeraciones para reemplazar `NSString` constantes. En este caso, el generador creará **automáticamente** los métodos para convertir los valores de enumeraciones y las constantes NSString.

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

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

En el ejemplo anterior podría decidir decorar `void Perform (NSString mode);` con un atributo de [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) . Esto **ocultará** la API basada en constantes de los consumidores de enlace.

Sin embargo, esto limitaría la subclase del tipo, ya que la alternativa de API más agradable utiliza un atributo de [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) . Los métodos generados no se `virtual`, es decir, no podrá invalidarlos, lo que puede ser, o no, ser una buena elección.

Una alternativa consiste en marcar la definición original, basada en `NSString`, como `[Protected]`. Esto permitirá que las subclases funcionen, cuando sea necesario, y la versión de wrap'ed seguirá funcionando y llamará al método invalidado.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Enlazar `NSValue`, `NSNumber`y `NSString` a un tipo mejor

El atributo [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) permite enlazar `NSNumber`, `NSValue` y `NSString`(enumeraciones) en tipos C# más precisos. El atributo se puede usar para crear una API de .NET mejor y precisa a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y propiedades con [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). La única restricción es que el miembro **no debe** estar dentro de un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
o [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaz.

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

Internamente, se realizarán las conversiones `bool?` <-> `NSNumber` y `CGRect` <-> .`NSValue`

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) también admite matrices de `NSNumber` `NSValue` y `NSString`(enumeraciones).

Por ejemplo:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Generaría:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` es una enumeración respaldada por `NSString`, se capturará el valor de `NSString` derecho y se controlará la conversión de tipos.

Consulte la documentación de [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) para ver los tipos de conversión admitidos.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificaciones de enlace

Las notificaciones son mensajes que se publican en el `NSNotificationCenter.DefaultCenter` y se usan como mecanismo para difundir mensajes de una parte de la aplicación a otra. Los desarrolladores se suscriben a las notificaciones normalmente mediante el método [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) de [NSNotificationCenter](xref:Foundation.NSNotificationCenter). Cuando una aplicación envía un mensaje al centro de notificaciones, normalmente contiene una carga almacenada en el diccionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) . Este diccionario está débilmente tipado y la obtención de información es propensa a errores, ya que los usuarios normalmente necesitan leer en la documentación qué claves están disponibles en el Diccionario y los tipos de valores que se pueden almacenar en el diccionario. La presencia de claves a veces se usa también como booleano.

El generador de enlaces de Xamarin. iOS proporciona compatibilidad para que los desarrolladores enlacen notificaciones. Para ello, establezca el [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
atributo de una propiedad que también se ha etiquetado con un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
propiedad (puede ser pública o privada).

Este atributo se puede usar sin argumentos para las notificaciones que no tienen ninguna carga útil, o puede especificar una `System.Type` que haga referencia a otra interfaz en la definición de la API, normalmente con el nombre que termina con "EventArgs". El generador convertirá la interfaz en una clase que subclase `EventArgs` e incluirá todas las propiedades que se enumeran allí. El atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) debe usarse en la clase EventArgs para mostrar el nombre de la clave utilizada para buscar el Diccionario de Objective-C para capturar el valor.

Por ejemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

En el código anterior se generará una clase anidada `MyClass.Notifications` con los siguientes métodos:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Los usuarios del código pueden suscribirse fácilmente a las notificaciones publicadas en [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) mediante código como el siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

El valor devuelto de `ObserveDidStart` se puede usar para dejar de recibir notificaciones fácilmente, de la siguiente manera:

```csharp
token.Dispose ();
```

O bien, puede llamar a [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) y pasar el token. Si la notificación contiene parámetros, debe especificar un ayudante `EventArgs` interfaz, como se indica a continuación:

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

Lo anterior generará una clase de `MyScreenChangedEventArgs` con las propiedades `ScreenX` y `ScreenY` que capturarán los datos del diccionario [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando las claves "ScreenXKey" y "ScreenYKey", respectivamente, y aplicarán las conversiones adecuadas. El atributo `[ProbePresence]` se usa para que el generador sondee si la clave está establecida en el `UserInfo`, en lugar de intentar extraer el valor. Se utiliza para los casos en los que la presencia de la clave es el valor (normalmente para los valores booleanos).

Esto le permite escribir código como el siguiente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorías de enlace

Las categorías son un mecanismo de Objective-C que se usa para ampliar el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se usan para extender la funcionalidad de una clase base (por ejemplo `NSObject`) cuando se vincula un marco de trabajo específico (por ejemplo, `UIKit`), haciendo que sus métodos estén disponibles, pero solo si el nuevo marco de trabajo está vinculado en.   En algunos otros casos, se usan para organizar características en una clase por funcionalidad.   Son similares en lo que se C# refiere a los métodos de extensión. Este es el aspecto que tendría una categoría en Objective-C:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

En el ejemplo anterior, si se encuentra en una biblioteca, se extienden las instancias de `UIView` con el método `makeBackgroundRed`.

Para enlazarlos, puede usar el atributo [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) en una definición de interfaz.  Al utilizar el [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
atributo, el significado del [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
los cambios de atributo de se usan para especificar la clase base que se va a extender, para ser el tipo que se va a extender.

A continuación se muestra cómo se enlazan las extensiones de `UIView` C# y se convierten en métodos de extensión:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Lo anterior creará un `MyUIViewExtension` una clase que contiene el método de extensión `MakeBackgroundRed`.  Esto significa que ahora puede llamar a "MakeBackgroundRed" en cualquier subclase `UIView`, lo que le proporciona la misma funcionalidad que obtendría en Objective-C. En algunos otros casos, las categorías se utilizan para no extender una clase de sistema, sino para organizar la funcionalidad exclusivamente con fines de decoración.  Así:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Aunque puede usar el [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
atributo también para este estilo de decoración de declaraciones, es posible que solo se agreguen todos a la definición de clase.  Ambos lograría lo mismo:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

En estos casos, es más breve fusionar mediante combinación las categorías:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Bloques de enlace

Los bloques son una nueva construcción presentada por Apple para poner el equivalente funcional C# de métodos anónimos a Objective-C. Por ejemplo, la clase `NSSet` ahora expone este método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descripción anterior declara un método denominado `enumerateObjectsUsingBlock:` que toma un argumento denominado `block`. Este bloque es similar a un C# método anónimo, ya que admite la captura del entorno actual (el puntero "this", el acceso a las variables locales y los parámetros). El método anterior de `NSSet` invoca el bloque con dos parámetros `NSObject` (la parte `id obj`) y un puntero a un elemento booleano (la `BOOL *stop`).

Para enlazar este tipo de API con btouch, primero debe declarar la signatura de tipo de bloque como C# un delegado y luego hacer referencia a ella desde un punto de entrada de la API, de la siguiente manera:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Y ahora el código puede llamar a la función C#desde:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

También puede usar expresiones lambda si lo prefiere:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos asincrónicos

El generador de enlaces puede convertir una determinada clase de métodos en métodos compatibles con Async (métodos que devuelven una tarea o tarea&lt;T&gt;).

Puede usar el [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
atributo en métodos que devuelven void y cuyo último argumento es una devolución de llamada.  Cuando se aplica a un método, el generador de enlaces generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor devuelto será un `Task`, si la devolución de llamada toma un parámetro, el resultado será un `Task<T>`.  Si la devolución de llamada toma varios parámetros, debe establecer el `ResultType` o `ResultTypeName` para especificar el nombre deseado del tipo generado que contendrá todas las propiedades.

Ejemplo:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

El código anterior generará el método LoadFile, así como:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Exposición de tipos seguros para parámetros NSDictionary débiles

En muchos lugares de la API de Objective-C, los parámetros se pasan como API de `NSDictionary` débilmente tipados con claves y valores específicos, pero son propensos a errores (puede pasar claves no válidas y no obtener ninguna advertencia; puede pasar valores no válidos y no obtener advertencias) y frustrar el uso, ya que requieren varios viajes a la documentación para buscar los nombres y valores de

La solución consiste en proporcionar una versión fuertemente tipada que proporciona la versión fuertemente tipada de la API y, en segundo plano, asigna las distintas claves y valores subyacentes.

Por ejemplo, si la API de Objective-C aceptó una `NSDictionary` y se documenta como la toma de la clave `XyzVolumeKey` que toma un `NSNumber` con un valor de volumen de 0,0 a 1,0 y un `XyzCaptionKey` que toma una cadena, querrá que los usuarios tengan una agradable API que tenga el aspecto siguiente:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

La propiedad `Volume` se define como Nullable Float, ya que la Convención en Objective-C no requiere que estos diccionarios tengan el valor, por lo que hay escenarios en los que no se puede establecer el valor.

Para ello, debe realizar algunas acciones:

- Cree una clase fuertemente tipada que subclase [DictionaryContainer](xref:Foundation.DictionaryContainer) y proporcione los distintos captadores y establecedores para cada propiedad.
- Declare las sobrecargas de los métodos que toman `NSDictionary` para tomar la nueva versión fuertemente tipada.

Puede crear la clase fuertemente tipada manualmente, o bien usar el generador para realizar el trabajo por usted.  En primer lugar, exploraremos cómo hacerlo manualmente para que entienda lo que está ocurriendo y, después, el enfoque automático.

Debe crear un archivo auxiliar para esto, no entra en la API de contrato.  Esto es lo que tendría que escribir para crear la clase XyzOptions:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

A continuación, debe proporcionar un método contenedor que surfacee la API de alto nivel, además de la API de bajo nivel.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Si no es necesario sobrescribir la API, puede ocultar de forma segura la API basada en NSDictionary mediante el [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Como puede ver, usamos el [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
atributo para mostrar un nuevo punto de entrada de API y lo encontramos con nuestra clase de `XyzOptions` fuertemente tipada.  El método contenedor también permite pasar valores NULL.

Ahora, lo que no hemos mencionado es de dónde proceden los valores de `XyzOptionsKeys`.  Normalmente, se agrupan las claves que una API expone en una clase estática como `XyzOptionsKeys`, como se muestra a continuación:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Echemos un vistazo al soporte automático para crear estos diccionarios fuertemente tipados.  Esto evita una gran cantidad de texto reutilizable y puede definir el Diccionario directamente en el contrato de la API, en lugar de usar un archivo externo.

Para crear un diccionario fuertemente tipado, introduzca una interfaz en la API y decorarla con el atributo [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) .  Esto indica al generador que debe crear una clase con el mismo nombre que la interfaz que se derivará de `DictionaryContainer` y proporcionará descriptores de acceso fuertemente tipados para ella.

El atributo [`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) toma un parámetro, que es el nombre de la clase estática que contiene las claves del diccionario.  Después, cada propiedad de la interfaz se convertirá en un descriptor de acceso fuertemente tipado.  De forma predeterminada, el código utilizará el nombre de la propiedad con el sufijo "Key" en la clase estática para crear el descriptor de acceso.

Esto significa que la creación del descriptor de acceso fuertemente tipado ya no requiere un archivo externo, ni tiene que crear manualmente captadores y establecedores para todas las propiedades, ni tener que buscar las claves manualmente.

Este es el aspecto que tendría el enlace completo:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

En caso de que tenga que hacer referencia a los miembros del `XyzOption` en un campo diferente (que no es el nombre de la propiedad con el sufijo `Key`), puede decorar la propiedad con una [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributo con el nombre que desea utilizar.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Asignaciones de tipos

En esta sección se explica cómo se asignan los tipos C# de Objective-C a los tipos.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

En la tabla siguiente se muestra cómo asignar los tipos del mundo Objective-C y CocoaTouch al mundo de Xamarin. iOS:

|Nombre de tipo de Objective-C|Tipo de Unified API de Xamarin. iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([más información sobre el enlace NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (vea también: [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipos CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipos de base (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Matrices

El tiempo de ejecución de Xamarin. iOS se encarga C# automáticamente de convertir las matrices en `NSArrays` y de volver a realizar la conversión, por ejemplo, el método imaginario de Objective-C que devuelve un `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Se enlaza como se indica a continuación:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

La idea es usar una C# matriz fuertemente tipada, ya que esto permitirá que el IDE proporcione la finalización correcta del código con el tipo real sin forzar al usuario a adivinar o buscar la documentación para averiguar el tipo real de los objetos contenidos en la matriz.

En los casos en los que no se puede realizar un seguimiento del tipo más derivado real incluido en la matriz, se puede utilizar `NSObject []` como valor devuelto.

<a name="Selectors" />

### <a name="selectors"></a>Selectores

Los selectores aparecen en la API de Objective-C como el tipo especial `SEL`. Al enlazar un selector, asignaría el tipo a `ObjCRuntime.Selector`.  Normalmente, los selectores se exponen en una API con un objeto, el objeto de destino y un selector que se va a invocar en el objeto de destino. Proporcionar ambos se corresponde básicamente con el C# delegado: algo que encapsula el método que se va a invocar, así como el objeto en el que se va a invocar el método.

Este es el aspecto del enlace:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Y así es como se utilizaría normalmente el método en una aplicación:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Para que el enlace sea más agradable C# a los desarrolladores, normalmente proporcionará un método que toma un parámetro `NSAction`, lo C# que permite usar delegados y expresiones lambda en lugar de la `Target+Selector`. Para ello, normalmente ocultaría el método `SetTarget` marcándolos con un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
y, a continuación, expondría un nuevo método auxiliar, similar al siguiente:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Por lo tanto, el código de usuario se puede escribir de la siguiente manera:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>Cadenas

Al enlazar un método que toma un `NSString`, puede reemplazarlo por un C# tipo de cadena, tanto en tipos de valor devuelto como en parámetros.

El único caso en el que podría querer usar un `NSString` directamente es cuando se usa la cadena como un token. Para obtener más información sobre las cadenas y `NSString`, lea el documento [sobre el diseño de la API en NSString](~/ios/internals/api-design/nsstring.md) .

En algunos casos raros, una API podría exponer una cadena similar a C (`char *`) en lugar de una cadena de Objective-C (`NSString *`). En esos casos, puede anotar el parámetro con el [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>parámetros out/Ref

Algunas API devuelven valores en sus parámetros o pasan parámetros por referencia.

Normalmente, la firma tiene el siguiente aspecto:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

En el primer ejemplo se muestra una expresión común de Objective-C para devolver los códigos de error, se pasa un puntero a un puntero de `NSError` y, cuando se devuelve, se establece el valor.   El segundo método muestra cómo un método de Objective-C puede tomar un objeto y modificar su contenido.   Esto sería un paso por referencia, en lugar de un valor de salida puro.

El enlace sería similar al siguiente:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de administración de memoria

Si usa el atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) y está pasando datos que se retendrán mediante el método llamado, puede especificar la semántica de los argumentos pasándolo como segundo parámetro, por ejemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Lo anterior marcaría el valor como si tuviera la semántica "retain". La semántica disponible es:

- Assign
- Copiar
- Tain

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Directrices de estilo

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Usar [Internal]

Puede usar el [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributo para ocultar un método de la API pública. Puede que desee hacer esto en los casos en los que la API expuesta es demasiado baja y desea proporcionar una implementación de alto nivel en un archivo independiente basado en este método.

También puede utilizarlo cuando tenga limitaciones en el generador de enlaces; por ejemplo, algunos escenarios avanzados podrían exponer tipos que no están enlazados y que desea enlazar de forma propia, y desea incluir esos tipos en su propia forma.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Controladores de eventos y devoluciones de llamada

Las clases de Objective-C suelen difundir notificaciones o solicitar información mediante el envío de un mensaje en una clase delegada (delegado Objective-C).

Este modelo, aunque totalmente compatible con Xamarin. iOS, puede resultar complicado. Xamarin. iOS expone el C# patrón de eventos y un sistema de devolución de llamada de método en la clase que se puede usar en estas situaciones. Esto permite ejecutar código como este:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

El generador de enlaces es capaz de reducir la cantidad de tipos necesarios para asignar el patrón Objective-C al C# patrón.

A partir de Xamarin. iOS 1,4, también se puede indicar al generador que genere enlaces para un delegado de Objective-C específico y que exponga el delegado como C# eventos y propiedades en el tipo de host.

Hay dos clases implicadas en este proceso, la clase host que será la que actualmente emite eventos y los envía al `Delegate` o `WeakDelegate` y la clase de delegado real.

Teniendo en cuenta la siguiente configuración:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Para encapsular la clase debe:

- En la clase de host, agregue a su [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   declaración el tipo que actúa como delegado y el C# nombre que expuso. En el ejemplo anterior, se `typeof (MyClassDelegate)` y `WeakDelegate` respectivamente.
- En la clase Delegate, en cada método que tiene más de dos parámetros, debe especificar el tipo que desea utilizar para la clase EventArgs generada automáticamente.

El generador de enlaces no se limita a contener solo un destino de evento único, es posible que algunas clases de Objective-C emitan mensajes a más de un delegado, por lo que tendrá que proporcionar matrices para admitir esta configuración. La mayoría de las configuraciones no lo necesitarán, pero el generador está listo para admitir esos casos.

El código resultante será:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

El `EventArgs` se utiliza para especificar el nombre de la clase `EventArgs` que se va a generar. Debe usar una por firma (en este ejemplo, el `EventArgs` contendrá una propiedad `With` de tipo NINT).

Con las definiciones anteriores, el generador generará el evento siguiente en la clase generada:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Por lo tanto, ahora puede usar el código como el siguiente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Las devoluciones de llamada son similares a las invocaciones de eventos, la diferencia es que, en lugar de tener varios suscriptores potenciales (por ejemplo, varios métodos se pueden enlazar a un evento de `Clicked` o un evento de `DownloadFinished`) las devoluciones de llamada solo pueden tener un único suscriptor.

El proceso es idéntico, la única diferencia es que en lugar de exponer el nombre de la clase `EventArgs` que se va a generar, el EventArgs se usa realmente para asignar un nombre C# al nombre del delegado resultante.

Si el método de la clase delegada devuelve un valor, el generador de enlaces lo asignará a un método delegado en la clase primaria en lugar de un evento. En estos casos, debe proporcionar el valor predeterminado que debe devolver el método si el usuario no se enlaza al delegado. Para ello, use el [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
o [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) atributos.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) codificarán un valor devuelto, mientras [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
se utiliza para especificar qué argumento de entrada se devolverá.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumeraciones y tipos base

También puede hacer referencia a enumeraciones o tipos base que no son directamente compatibles con el sistema de definición de la interfaz btouch. Para ello, coloque las enumeraciones y los tipos principales en un archivo independiente e inclúyalo como parte de uno de los archivos adicionales que proporcione a btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Vinculación de las dependencias

Si va a enlazar API que no forman parte de la aplicación, debe asegurarse de que el ejecutable esté vinculado a estas bibliotecas.

Debe informar a Xamarin. iOS sobre cómo vincular las bibliotecas; esto puede hacerse modificando la configuración de compilación para invocar el comando `mtouch` con algunos argumentos de compilación adicionales que especifican cómo se vinculan las nuevas bibliotecas con la opción "-gcc_flags", seguida de una cadena entrecomillada que contiene todas las bibliotecas adicionales que se necesitan para el programa, de la siguiente manera:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

En el ejemplo anterior se vinculará `libMyLibrary.a`, `libSystemLibrary.dylib` y la biblioteca de `CFNetwork` Framework en el ejecutable final.

También puede aprovechar el [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)de nivel de ensamblado, que puede insertar en los archivos de contrato (por ejemplo, `AssemblyInfo.cs`).
Cuando use el [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), deberá tener la biblioteca nativa disponible en el momento en que realice el enlace, ya que esto incrustará la biblioteca nativa con su aplicación. Por ejemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Es posible que se pregunte por qué necesita `-force_load` comando y el motivo es que la marca-ObjC a pesar de que compila el código en, no conserva los metadatos necesarios para admitir las categorías (la eliminación del enlazador o del compilador no responde) que necesita en tiempo de ejecución para Xamarin. iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Referencias asistidas

Algunos objetos transitorios como las hojas de acción y los cuadros de alerta son engorrosos para realizar un seguimiento de los desarrolladores y el generador de enlaces puede ayudarle un poco más.

Por ejemplo, si tuviera una clase que mostrase un mensaje y después generara un evento `Done`, la manera tradicional de controlar esto sería:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

En el escenario anterior, el desarrollador debe mantener la referencia al propio objeto y filtrar o borrar de forma activa la referencia para Box.  Mientras se enlaza el código, el generador admite el seguimiento de la referencia y la borra cuando se invoca un método especial, el código anterior se convertiría en:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Observe cómo ya no es necesario conservar la variable en una instancia, que funciona con una variable local y que no es necesario borrar la referencia cuando el objeto se elimina.

Para aprovechar esto, la clase debe tener una propiedad Events establecida en la declaración de [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) y también la variable `KeepUntilRef` establecida en el nombre del método que se invoca cuando el objeto ha completado su trabajo, de la siguiente manera:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Heredar protocolos

A partir de Xamarin. iOS v 3.2, se admite la herencia de los protocolos que se han marcado con la propiedad [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) . Esto resulta útil en ciertos patrones de API, como en `MapKit` donde el protocolo de `MKOverlay` hereda del protocolo `MKAnnotation` y lo adoptan varias clases que heredan de `NSObject`.

Históricamente, se necesitaba copiar el protocolo en cada implementación, pero en estos casos podemos hacer que la clase `MKShape` herede del protocolo `MKOverlay` y que genere automáticamente todos los métodos necesarios.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de enlace](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
