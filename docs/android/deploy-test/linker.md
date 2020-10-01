---
title: Vincular en Android
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2018
ms.openlocfilehash: 3c42743a26ab4ec5388c1d3458d88ef4d161a1f3
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454850"
---
# <a name="linking-on-android"></a>Vincular en Android

Las aplicaciones de Xamarin.Android usan un *enlazador* para reducir el tamaño de la aplicación. El enlazador emplea análisis estáticos de la aplicación para determinar qué ensamblados, tipos y miembros se usan realmente. El enlazador se comporta entonces como un *recolector de elementos no utilizados*, que busca continuamente los ensamblados, tipos y miembros a los que se hace referencia hasta que se encuentra la clausura completa de los ensamblados, los tipos y los miembros a los que se hace referencia. Todo el contenido fuera de esta clausura se *descarta*.

Por ejemplo, el ejemplo [Hello, Android](/samples/xamarin/monodroid-samples/hellom4a):

|Configuración|Tamaño 1.2.0|Tamaño 4.0.1|
|---|---|---|
|Lanzar sin vincular:|14,0 MB|16,0 MB|
|Lanzar con vinculación:|4,2 MB|2,9 MB|

Enlace de resultados de un paquete con un tamaño un 30 % inferior al del paquete original (sin vincular) en 1.2.0 y un 18 % inferior al del paquete sin vincular en 4.0.1.

## <a name="control"></a>Control

La vinculación se basa en *análisis estáticos*. Por lo tanto, todo lo que depende del entorno en tiempo de ejecución no se detectará:

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```

### <a name="linker-behavior"></a>Comportamiento del enlazador

El mecanismo principal para controlar el enlazador es el menú desplegable **Comportamiento del enlazador** (*Vinculación* en Visual Studio) del cuadro de diálogo **Opciones del proyecto**. Hay tres opciones:

1. **No vincular** (*Ninguno* en Visual Studio)
1. **Vincular ensamblados de SDK** (*Solo ensamblados de SDK*)
1. **Vincular todos los ensamblados** (*Ensamblados de SDK y usuario*)

La opción **No vincular** desactiva el enlazador; en el ejemplo de tamaño de aplicación anterior "Lanzar sin vincular" se usó este comportamiento. Es útil para solucionar errores en tiempo de ejecución, para ver si es responsabilidad del enlazador. Normalmente, no se recomienda esta configuración para las compilaciones de producción.

La opción **Vincular ensamblados de SDK** solo vincula los [ensamblados que se proporcionan con Xamarin.Android](~/cross-platform/internals/available-assemblies.md).
Todos los demás ensamblados (como el código) no se vinculan.

La opción **Vincular todos los ensamblados** vincula todos los ensamblados, lo que significa que el código también puede eliminarse si no hay referencias estáticas.

El ejemplo anterior funcionará con las opciones *No vincular* y *Vincular ensamblados de SDK* y presentará errores con la opción *Vincular todos los ensamblados*, en cuyo caso se generará el siguiente error:

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```

### <a name="preserving-code"></a>Conservar el código

A veces, el enlazador eliminará código que desea conservar. Por ejemplo:

- Es posible que tenga código al que llamar de forma dinámica mediante `System.Reflection.MemberInfo.Invoke`.

- Si crea instancias de tipos de forma dinámica, puede que quiera conservar el constructor predeterminado de los tipos.

- Si usa la serialización XML, puede que quiera conservar las propiedades de los tipos.

En estos casos, puede usar el atributo [Android.Runtime.Preserve](xref:Android.Runtime.PreserveAttribute). Cada miembro no vinculado de forma estática por la aplicación es susceptible de ser eliminado, por lo que se puede usar este atributo para marcar los miembros a los que no se hace referencia de forma estática pero que aún son necesarios para la aplicación. Este atributo se puede aplicar en cada miembro de un tipo o en el propio tipo.

En el ejemplo siguiente, este atributo se utiliza para conservar el constructor de la clase `Example`:

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

Si desea conservar todo el tipo, puede usar la siguiente sintaxis de atributo:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

Por ejemplo, en el siguiente fragmento de código, toda la clase `Example` se conserva para la serialización XML:

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

A veces se desean conservar determinados tipos, pero solo si se mantiene el tipo contenedor. En tales casos, use la siguiente sintaxis de atributo:

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

Si no desea recurrir a una dependencia en las bibliotecas de Xamarin, por ejemplo, como en el supuesto caso de que compile una biblioteca de clases portátil multiplataforma, también puede usar el atributo `Android.Runtime.Preserve`. Para ello, declare una clase `PreserveAttribute` en el espacio de nombres `Android.Runtime` como este:

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```

En los ejemplos anteriores, el atributo `Preserve` se declara en el espacio de nombres `Android.Runtime`; sin embargo, puede usar el atributo `Preserve` porque el vinculador busca este atributo por nombre de tipo de atributo en cualquier espacio de nombres.

### <a name="falseflag"></a>falseflag

Si no se puede usar el atributo [Preserve], a menudo resulta útil proporcionar un bloque de código para que el enlazador crea que se usa el tipo, al tiempo que se impide la ejecución del bloque de código en tiempo de ejecución. Para usar esta técnica, podríamos hacer lo siguiente:

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```

### <a name="linkskip"></a>linkskip

Es posible especificar que un conjunto de ensamblados proporcionado por el usuario no debe vincularse en absoluto, mientras que se permite omitir otros ensamblados de usuario con la opción *Vincular ensamblados de SDK* mediante el uso de la [propiedad AndroidLinkSkip MSBuild](~/android/deploy-test/building-apps/build-process.md):

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```

### <a name="linkdescription"></a>LinkDescription

La [`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
**acción de compilación** puede usarse en archivos que pueden contener un [archivo de configuración de enlazador personalizado](~/cross-platform/deploy-test/linker.md).
.edmx. Puede que sea necesario que los archivos de configuración de enlazador personalizados conserven los miembros `internal` o `private`.

### <a name="custom-attributes"></a>Atributos personalizados

Cuando se vincula un ensamblado, se quitarán los siguientes tipos de atributo personalizado de todos los miembros:

- System.ObsoleteAttribute
- System.MonoDocumentationNoteAttribute
- System.MonoExtensionAttribute
- System.MonoInternalNoteAttribute
- System.MonoLimitationAttribute
- System.MonoNotSupportedAttribute
- System.MonoTODOAttribute
- System.Xml.MonoFIXAttribute

Cuando se vincula un ensamblado, se quitarán los siguientes tipos de atributo personalizado de todos los miembros de las compilaciones de versiones:

- System.Diagnostics.DebuggableAttribute
- System.Diagnostics.DebuggerBrowsableAttribute
- System.Diagnostics.DebuggerDisplayAttribute
- System.Diagnostics.DebuggerHiddenAttribute
- System.Diagnostics.DebuggerNonUserCodeAttribute
- System.Diagnostics.DebuggerStepperBoundaryAttribute
- System.Diagnostics.DebuggerStepThroughAttribute
- System.Diagnostics.DebuggerTypeProxyAttribute
- System.Diagnostics.DebuggerVisualizerAttribute

## <a name="related-links"></a>Vínculos relacionados

- [Configuración personalizada del enlazador](~/cross-platform/deploy-test/linker.md)
- [Vincular en iOS](~/ios/deploy-test/linker.md)