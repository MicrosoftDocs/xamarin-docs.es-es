---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Usar bibliotecas deC++ C/con Xamarin
description: Visual Studio para Mac puede usarse para compilar e integrar código C/C++ multiplataforma en aplicaciones móviles para Android e iOS, con Xamarin C#y. En este artículo se explica cómo configurar y depurar un C++ proyecto en una aplicación de Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 12/17/2018
ms.openlocfilehash: a6c5e172fa9fe41e210f332d351adc307d0c7df3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648193"
---
# <a name="use-cc-libraries-with-xamarin"></a>Usar bibliotecas deC++ C/con Xamarin

## <a name="overview"></a>Información general

Xamarin permite a los desarrolladores crear aplicaciones móviles nativas multiplataforma con Visual Studio. Por lo C# general, los enlaces se utilizan para exponer los componentes de la plataforma existentes a los desarrolladores. Sin embargo, hay ocasiones en las que las aplicaciones de Xamarin necesitan trabajar con códigos base existentes. A veces, los equipos simplemente no tienen el tiempo, el presupuesto o los recursos para migrar un código base grande, bien probado C#y muy optimizado a.

[Visual C++ para el desarrollo móvil multiplataforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite que el códigoC++ C C# /y se compile como parte de la misma solución, ofreciendo muchas ventajas, incluida una experiencia de depuración unificada. Microsoft ha usado C/C++ y Xamarin de esta manera para ofrecer aplicaciones como hyperlapse [móvil](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) y [cámara PIX](https://www.microsoft.com/microsoftpix).

Sin embargo, en algunos casos es necesario (o requisito) mantener los procesos y las herramientasC++ existentes de C/y mantener el código de la biblioteca desacoplado de la aplicación, tratando la biblioteca como si fuera similar a un componente de terceros. En estas situaciones, el desafío no solo expone los miembros relevantes a sino que C# administra la biblioteca como una dependencia. Y, por supuesto, automatizar todo lo posible de este proceso.  

En esta publicación se describe un enfoque de alto nivel para este escenario y se describe un ejemplo sencillo.

## <a name="background"></a>Fondo

C/C++ se considera un lenguaje multiplataforma, pero se debe prestar mucha atención para asegurarse de que el código fuente es realmente multiplataforma, usando solo C/C++ admitido por todos los compiladores de destino y que contenga poca o ninguna plataforma incluida condicionalmente o código específico del compilador.

En última instancia, el código debe compilarse y ejecutarse correctamente en todas las plataformas de destino, por lo tanto, se reduce la similitud entre las plataformas (y los compiladores) de destino. Los problemas pueden seguir surgiendo de pequeñas diferencias entre los compiladores y, por lo tanto, las pruebas exhaustivas (preferiblemente automatizadas) en cada plataforma de destino se vuelven cada vez más importantes.  

## <a name="high-level-approach"></a>Enfoque de alto nivel

La ilustración siguiente representa el enfoque de cuatro fases que se usa para transformarC++ código C/source en una biblioteca Xamarin multiplataforma que se comparte a través de NuGet y, a continuación, se consume en una aplicación de Xamarin. Forms.
 
![Enfoque de alto nivel para el uso deC++ C/con Xamarin](images/cpp-steps.jpg)

Las cuatro fases son:

1. Compilar elC++ código de C/source en bibliotecas nativas específicas de la plataforma.
2. Encapsular las bibliotecas nativas con una solución de Visual Studio.
3. Empaquetar e insertar un paquete de NuGet para el contenedor de .NET.
4. Consumir el paquete NuGet desde una aplicación Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1: Compilar elC++ código de C/source en bibliotecas nativas específicas de la plataforma

El objetivo de esta fase es crear bibliotecas nativas a las que puede llamar el C# contenedor. Esto puede ser o no relevante en función de su situación. Las muchas herramientas y procesos que se pueden incluir en este escenario común quedan fuera del ámbito de este artículo. Las consideraciones clave son mantener la sincronizaciónC++ de C/codebase con cualquier código de contenedor nativo, suficientes pruebas unitarias y automatización de la compilación. 

Las bibliotecas en el tutorial se crearon con Visual Studio Code con un script de Shell acompañante. Una versión ampliada de este tutorial se puede encontrar en el [repositorio de github de Mobile CAT](https://github.com/xamarin/mobcat/blob/dev/samples/cpp_with_xamarin/) que describe esta parte del ejemplo con mayor profundidad. Las bibliotecas nativas se tratan como una dependencia de terceros en este caso, sin embargo, esta fase se ilustra para el contexto.

Para simplificar, el tutorial solo tiene como destino un subconjunto de arquitecturas. Para iOS, usa la utilidad Lipo para crear un único binario de Fat a partir de archivos binarios específicos de la arquitectura. Android usará archivos binarios dinámicos con. por tanto, la extensión e iOS utilizarán un binario FAT estático con una extensión. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: Encapsular las bibliotecas nativas con una solución de Visual Studio

La siguiente fase consiste en encapsular las bibliotecas nativas para que se puedan usar fácilmente desde .NET. Esto se hace con una solución de Visual Studio con cuatro proyectos. Un proyecto compartido contiene el código común. Los proyectos que tienen como destino cada uno de Xamarin. Android, Xamarin. iOS y .NET Standard permiten que se haga referencia a la biblioteca de manera independiente de la plataforma.

El contenedor usa '[The anzuelo and switch engañe](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/), ' descrito por Paul Betts. Esta no es la única manera, pero facilita la referencia a la biblioteca y evita la necesidad de administrar explícitamente implementaciones específicas de la plataforma dentro de la propia aplicación de consumo. El truco consiste esencialmente en garantizar que los destinos (.NET Standard, Android, iOS) comparten el mismo espacio de nombres, nombre de ensamblado y estructura de clase. Dado que NuGet siempre preferirá una biblioteca específica de la plataforma, la versión de .NET Standard nunca se utiliza en tiempo de ejecución.

La mayor parte del trabajo de este paso se centrará en el uso de P/Invoke para llamar a los métodos de la biblioteca nativa y administrar las referencias a los objetos subyacentes. El objetivo es exponer la funcionalidad de la biblioteca al consumidor a la vez que se abstrae cualquier complejidad. Los desarrolladores de Xamarin. Forms no necesitan tener conocimientos prácticos sobre el funcionamiento interno de la biblioteca no administrada. Debería parecerse a que están usando cualquier otra biblioteca C# administrada.

En última instancia, la salida de esta fase es un conjunto de bibliotecas de .NET, una por destino, junto con un documento nuspec que contiene la información necesaria para compilar el paquete en el paso siguiente.

**Fase 3: Empaquetar e insertar un paquete de NuGet para el contenedor de .NET**

La tercera fase es la creación de un paquete NuGet mediante los artefactos de compilación del paso anterior. El resultado de este paso es un paquete de NuGet que se puede usar desde una aplicación de Xamarin. El tutorial usa un directorio local para servir como la fuente de NuGet. En producción, este paso debe publicar un paquete en una fuente NuGet pública o privada y debe estar totalmente automatizado.

**Fase 4: Consumir el paquete de NuGet desde una aplicación de Xamarin. Forms**

El paso final consiste en hacer referencia y usar el paquete NuGet desde una aplicación de Xamarin. Forms. Esto requiere configurar la fuente de NuGet en Visual Studio para usar la fuente definida en el paso anterior.

Una vez configurada la fuente, se debe hacer referencia al paquete desde cada proyecto en la aplicación de Xamarin. Forms multiplataforma. "El truco de anzuelo y switch" proporciona interfaces idénticas, por lo que se puede llamar a la funcionalidad de la biblioteca nativa mediante el código definido en una sola ubicación.

El repositorio de código fuente contiene una [lista de lecturas adicionales](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up) que incluye artículos sobre cómo configurar una fuente de NuGet privada en Azure DevOps y cómo insertar el paquete en esa fuente. Aunque requiere un poco más de tiempo de configuración que un directorio local, este tipo de fuente es mejor en un entorno de desarrollo en equipo.

## <a name="walk-through"></a>Tutorial

Los pasos proporcionados son específicos de **Visual Studio para Mac**, pero la estructura funciona también en **Visual Studio 2017** .

### <a name="prerequisites"></a>Requisitos previos

Para seguir adelante, el desarrollador necesitará lo siguiente:

- [Línea de comandos de NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Se necesita una [**cuenta de desarrollador de Apple**](https://developer.apple.com/) activa para implementar aplicaciones en un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Crear las bibliotecas nativas (fase 1)

La funcionalidad de la biblioteca nativa se basa en el [ejemplo de tutorial: Crear y usar una biblioteca estática (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

En este tutorial se omite la primera fase, compilando las bibliotecas nativas, ya que la biblioteca se proporciona como una dependencia de terceros en este escenario. Las bibliotecas nativas precompiladas se incluyen junto con el [código de ejemplo](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin) o se pueden [Descargar](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) directamente.

### <a name="working-with-the-native-library"></a>Trabajar con la biblioteca nativa

El ejemplo de *MathFuncsLib* original incluye una clase única `MyMathFuncs` denominada con la siguiente definición:

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Una clase adicional define las funciones de contenedor que permiten a un consumidor .net crear, Dispose e interactuar con la `MyMathFuncs` clase nativa subyacente.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Serán estas funciones contenedoras que se usan en el lado de [Xamarin](https://visualstudio.microsoft.com/xamarin/) .

## <a name="wrapping-the-native-library-stage-2"></a>Encapsular la biblioteca nativa (fase 2)

Esta fase requiere las [bibliotecas](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) precompiladas que se describen en la [sección anterior](##creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Crear la solución de Visual Studio

1. En **Visual Studio para Mac**, haga clic en **nuevo proyecto** (desde la *Página principal*) o en **nueva solución** (en el menú *archivo* ).
2. En la ventana **nuevo proyecto** , elija **proyecto compartido** (desde la *biblioteca de > multiplataforma*) y, a continuación, haga clic en **siguiente**.
3. Actualice los campos siguientes y haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncs.Shared  
    - **Nombre de la solución:** MathFuncs  
    - **Cód** Usar la ubicación de almacenamiento predeterminada (o elegir una alternativa)   
    - **Cree un proyecto en el directorio de la solución:** Establezca esta opción en activado.
4. En **Explorador de soluciones**, haga doble clic en el proyecto **MathFuncs. Shared** y vaya a **configuración principal**.
5. Quitar **. Compartido** desde el **espacio de nombres predeterminado** para que se establezca en solo **MathFuncs** y, a continuación, haga clic en **Aceptar**.
6. Abra **MyClass.CS** (creado por la plantilla), cambie el nombre de la clase y el nombre de archivo a **MyMathFuncsWrapper** y cambie el espacio de nombres a **MathFuncs**.
7. **Control + clic** en la solución **MathFuncs**y, a continuación, elija **Agregar nuevo proyecto...** en el menú **Agregar** .
8. En la ventana **nuevo proyecto** , elija **biblioteca de .net Standard** (desde la *biblioteca de > multiplataforma*) y, a continuación, haga clic en **siguiente**.
9. Elija **.NET Standard 2,0** y, a continuación, haga clic en **siguiente**.
10. Actualice los campos siguientes y haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncs.Standard  
    - **Cód** Usar la misma ubicación de almacenamiento que el proyecto compartido   

11. En **Explorador de soluciones**, haga doble clic en el proyecto **MathFuncs. Standard** .
12. Vaya a **configuración principal**y, a continuación, actualice el **espacio de nombres predeterminado** a **MathFuncs**.
13. Vaya a la configuración de **salida** y, a continuación, actualice **el nombre** del ensamblado a **MathFuncs**.
14. Vaya a la configuración **del** compilador, cambie la **configuración** a **versión**, establezca la **información** de depuración en **símbolos solo** y haga clic en **Aceptar**.
15. Elimine **Class1.CS/Getting iniciada** en el proyecto (si se ha incluido uno de ellos como parte de la plantilla).
16. **Control + clic** en la carpeta **dependencias/referencias** del proyecto y, a continuación, elija **Editar referencias**.
17. Seleccione **MathFuncs. Shared** en la pestaña **proyectos** y haga clic en **Aceptar**.
18. Repita los pasos del 7-17 (omitiendo el paso 9) con las siguientes configuraciones:

    | **NOMBRE DEL PROYECTO**  | **NOMBRE DE PLANTILLA**   | **MENÚ NUEVO PROYECTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Biblioteca de clases       | Biblioteca de > de Android      |
    | MathFuncs. iOS     | Biblioteca de enlace     | Biblioteca de > de iOS          |

19. En **Explorador de soluciones**, haga doble clic en el proyecto **MathFuncs. Android** y, a continuación, vaya a la configuración **del** compilador.

20. Con la **configuración** establecida endepurar, edite **definir símbolos** para incluir **Android;** .

21. Cambie la **configuración** a **versión**y, a continuación, edite los **símbolos de definición** para incluir también **Android;** .

22. Repita los pasos 19-20 para **MathFuncs. iOS**y edite los **símbolos de definición** para incluir **iOS** , en lugar de **Android** , en ambos casos.

23. Compile la solución en configuración de **lanzamiento** (**control + comando + B**) y compruebe que los tres ensamblados de salida (Android, iOS, .net Standard) (en las carpetas de la papelera de proyecto correspondientes) comparten el mismo nombre **MathFuncs. dll**.

En esta fase, la solución debe tener tres destinos, uno para Android, iOS y .NET Standard, y un proyecto compartido al que hagan referencia cada uno de los tres destinos. Deben configurarse para usar el mismo espacio de nombres y ensamblados de salida predeterminados con el mismo nombre. Esto es necesario para el enfoque "cebo and switch" mencionado anteriormente.

### <a name="adding-the-native-libraries"></a>Agregar las bibliotecas nativas

El proceso de agregar las bibliotecas nativas a la solución de contenedor varía ligeramente entre iOS y Android.

#### <a name="native-references-for-mathfuncsandroid"></a>Referencias nativas para MathFuncs. Android

1. **Control + haga clic** en el proyecto **MathFuncs. Android** y, a continuación, elija **nueva carpeta** en el menú **Agregar** nombre de **bibliotecas**.

2. Para cada **Abi** (interfaz binaria de aplicación), **control + haga clic** en la carpeta **bibliotecas** y, a continuación, elija **nueva carpeta** en el menú **Agregar** y asígnele un nombre después de la **Abi**correspondiente. En este caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obtener información general más detallada, consulte el tema [arquitecturas y CPU](https://developer.android.com/ndk/guides/arch) de la [Guía para desarrolladores de NDK](https://developer.android.com/ndk/guides/), específicamente la sección sobre cómo direccionar [código nativo en paquetes de aplicaciones](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Compruebe la estructura de carpetas:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Agregue **las bibliotecas correspondientes** a cada una de las carpetas **Abi** basadas en la siguiente asignación:

    **arm64-v8a:** bibliotecas/Android/arm64

    **armeabi-v7a:** bibliotecas/Android/ARM  

    **x86:** bibliotecas/Android/x86

    **x86_64:** bibliotecas/Android/x86_64

    > [!NOTE]
    > Para agregar archivos, **control + haga clic** en la carpeta que representa la **Abi**correspondiente y, a continuación, elija **Agregar archivos...** en el menú **Agregar** . Elija la biblioteca adecuada (en el directorio **PrecompiledLibs** ), haga clic en **abrir** y, a continuación, haga clic en **Aceptar** y, a continuación, desactive la opción predeterminada para *copiar el archivo en el directorio*.

5. Para cada uno de los archivos **. so** , **control + clic** y, a continuación, elija la opción **EmbeddedNativeLibrary** en el menú **acción de compilación** .

Ahora la carpeta **bibliotecas** debe aparecer de la siguiente manera:

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Referencias nativas para MathFuncs. iOS

1. **Control + clic** en el proyecto **MathFuncs. iOS** y, a continuación, elija **Agregar referencia nativa** en el menú **Agregar** . 
2. Elija la biblioteca **libMathFuncs. a** (desde bibliotecas/iOS en el directorio **PrecompiledLibs** ) y, a continuación, haga clic en **abrir** . 
3. **Control + clic** en el archivo **libMathFuncs** (dentro de la carpeta **referencias nativas** y, después, elija la opción **propiedades** en el menú.  
4. Configure las propiedades de **referencia nativa** para que se comprueben (mostrando un icono de marca) en el panel de **propiedades** :

    - Forzar carga
    - EncuentraC++
    - Conexión inteligente

    > [!NOTE]
    > El uso de un tipo de proyecto de biblioteca de enlace junto con una [referencia nativa](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) inserta la biblioteca estática y permite que se vincule automáticamente a la aplicación de Xamarin. iOS que hace referencia a ella (incluso cuando se incluye a través de un paquete NuGet).

5. Abra **ApiDefinition.CS**, eliminando el código comentado con plantilla (saliendo solo `MathFuncs` del espacio de nombres) y, a continuación, realice el mismo paso para **Structs.CS** 

    > [!NOTE]
    > Un proyecto de biblioteca de enlaces requiere estos archivos (con las acciones de compilación *ObjCBindingApiDefinition* y *ObjCBindingCoreSource* ) para poder compilar. Sin embargo, escribiremos el código para llamar a nuestra biblioteca nativa, fuera de estos archivos de una manera que se pueda compartir entre ambos destinos de la biblioteca de iOS y Android mediante P/Invoke estándar.

### <a name="writing-the-managed-library-code"></a>Escribir el código de biblioteca administrada

Ahora, escriba el C# código para llamar a la biblioteca nativa. El objetivo es ocultar cualquier complejidad subyacente. El consumidor no debería necesitar ningún conocimiento práctico de los conceptos internos de la biblioteca nativa o de los conceptos de P/Invoke.  

#### <a name="creating-a-safehandle"></a>Crear un SafeHandle

1. **Control + clic** en el proyecto **MathFuncs. Shared** y elija **Agregar archivo...** en el menú **Agregar** . 
2. Elija **clase vacía** en la ventana **nuevo archivo** , asígnele el nombre **MyMathFuncsSafeHandle** y, a continuación, haga clic en **nuevo** .
3. Implemente la clase **MyMathFuncsSafeHandle** :

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) es la manera preferida de trabajar con recursos no administrados en código administrado. Esto abstrae una gran cantidad de código reutilizable relacionado con la finalización crítica y el ciclo de vida de los objetos. El propietario de este identificador puede tratarlo posteriormente como cualquier otro recurso administrado y no tendrá que implementar el [patrón](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)descartable completo. 

#### <a name="creating-the-internal-wrapper-class"></a>Crear la clase interna contenedora

1. Abra **MyMathFuncsWrapper.CS**y cambie a una clase estática interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. En el mismo archivo, agregue la siguiente instrucción condicional a la clase:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Esto establece el valor constante **DllName** en función de si la biblioteca se compila para **Android** o **iOS**. Esto sirve para abordar las distintas convenciones de nomenclatura utilizadas por cada plataforma respectiva, pero también el tipo de biblioteca que se usa en este caso. Android usa una biblioteca dinámica, por lo que espera un nombre de archivo que incluya la extensión. Para iOS, se requiere ' *__Internal*' porque usamos una biblioteca estática.

3. Agregue una referencia a **System. Runtime. InteropServices** en la parte superior del archivo **MyMathFuncsWrapper.CS**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Agregue los métodos de contenedor para controlar la creación y eliminación de la clase **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Pasamos la constante **DllName** al atributo **DllImport** junto con el **punto** de entrada, que indica explícitamente al tiempo de ejecución de .net el nombre de la función a la que se llamará dentro de esa biblioteca. Técnicamente, no es necesario proporcionar el valor de **punto** de entrada si nuestros nombres de método administrado eran idénticos a los no administrados. Si no se proporciona ninguno, el nombre del método administrado se utilizaría como **punto** de entrada en su lugar. Sin embargo, es mejor ser explícito.  

5. Agregue los métodos de contenedor para que podamos trabajar con la clase **MyMathFuncs** con el código siguiente:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Estamos usando tipos simples para los parámetros de este ejemplo. Como la serialización es una copia bit a bit en este caso, no es necesario ningún trabajo adicional en nuestra parte. Observe también el uso de la clase **MyMathFuncsSafeHandle** en lugar del **IntPtr**estándar. **IntPtr** se asigna automáticamente al **SafeHandle** como parte del proceso de serialización.

6. Compruebe que la clase **MyMathFuncsWrapper** finalizada aparece como se muestra a continuación:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Finalización de la clase MyMathFuncsSafeHandle

1. Abra la clase **MyMathFuncsSafeHandle** , navegue hasta el comentario del marcador de posición **todo** dentro del método **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Reemplace la línea **todo** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escribir la clase MyMathFuncs

Ahora que el contenedor está completo, cree una clase MyMathFuncs que administre la referencia al objeto C++ MyMathFuncs no administrado.  

1. **Control + clic** en el proyecto **MathFuncs. Shared** y elija **Agregar archivo...** en el menú **Agregar** . 
2. Elija **clase vacía** en la ventana **nuevo archivo** , asígnele el nombre **MyMathFuncs** y, a continuación, haga clic en **nuevo** .
3. Agregue los siguientes miembros a la clase **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implemente el constructor para la clase de modo que cree y almacene un identificador para el objeto **MyMathFuncs** nativo cuando se cree una instancia de la clase:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implemente la interfaz **IDisposable** mediante el código siguiente:

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Implemente los métodos de **MyMathFuncs** con la clase **MyMathFuncsWrapper** para realizar el trabajo real en el capó pasando el puntero que hemos almacenado al objeto no administrado subyacente. El código debe ser el siguiente:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>Crear el archivo nuspec

Para que la biblioteca esté empaquetada y distribuida a través de NuGet, la solución necesita un archivo **nuspec** . Esto identificará los ensamblados resultantes que se incluirán en cada plataforma compatible.

1. **Control + haga clic** en la solución **MathFuncs**y, a continuación, elija **Agregar carpeta de soluciones** en el menú **Agregar** y asígnele el nombre **SolutionItems**.
2. **Control + haga clic** en la carpeta **SolutionItems** y, a continuación, elija **nuevo archivo...** en el menú **Agregar** .
3. Elija **archivo XML vacío** en la ventana **nuevo archivo** , asígnele el nombre **MathFuncs. nuspec** y, a continuación, haga clic en **nuevo**.
4. Actualice **MathFuncs. nuspec** con los metadatos de paquete básicos que se mostrarán al consumidor de **NuGet** . Por ejemplo:

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > Vea el documento de [referencia de nuspec](https://docs.microsoft.com/nuget/reference/nuspec) para obtener más detalles sobre el esquema que se usa para este manifiesto.

5. Agregue un `<files>` elemento como elemento secundario `<package>` del elemento (justo debajo `<metadata>`), identificando cada archivo con un elemento `<file>` independiente:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Cuando se instala un paquete en un proyecto y se especifican varios ensamblados con el mismo nombre, NuGet elige el ensamblado más específico para la plataforma dada.

6. Agregue los `<file>` elementos de los ensamblados **Android** :

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Agregue los `<file>` elementos de los ensamblados de **iOS** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Agregue los `<file>` elementos de los ensamblados de **netstandard 2.0** :

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Compruebe el manifiesto de **nuspec** :

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Este archivo especifica las rutas de acceso de salida del ensamblado de una compilación de **versión** , por lo que debe asegurarse de compilar la solución con esa configuración.

En este punto, la solución contiene tres ensamblados .NET y un manifiesto de **nuspec** compatible.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribución del contenedor .NET con NuGet

El paso siguiente consiste en empaquetar y distribuir el paquete NuGet para que la aplicación pueda usarlo fácilmente y administrarse como una dependencia. El ajuste y el consumo se pueden realizar en una única solución, pero la distribución de la biblioteca a través de NuGet ayuda a desacoplar y nos permite administrar estos códigos base de forma independiente.

### <a name="preparing-a-local-packages-directory"></a>Preparar un directorio de paquetes locales

La forma más sencilla de la fuente de NuGet es un directorio local:

1. En el **buscador**, navegue a un directorio adecuado. Por ejemplo, **/users**.
2. Elija **nueva carpeta** en el menú **archivo** y proporcione un nombre descriptivo como **local-Nuget-feed**.

### <a name="creating-the-package"></a>Crear el paquete

1. Establezca la **configuración de compilación** en **Release**y ejecute una compilación con el **comando + B**.
2. Abra **terminal** y cambie el directorio a la carpeta que contiene el archivo **nuspec** .
3. En **terminal**, ejecute el comando del **paquete de Nuget** especificando el archivo **nuspec** , la **versión** (por ejemplo, 1.0.0) y el directorio de **archivos con la** carpeta creada en el [paso anterior](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), es decir,  **local-Nuget-feed**. Por ejemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirme** que **MathFuncs. 1.0.0. nupkg** se ha creado en el directorio **local-Nuget-feed** .

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>OPTA Uso de una fuente de NuGet privada con Azure DevOps

Una técnica más sólida se describe en [Introducción a los paquetes de NuGet en Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), que muestra cómo crear una fuente privada e insertar el paquete (generado en el paso anterior) en esa fuente.

Es ideal que este flujo de trabajo esté totalmente automatizado, por ejemplo, mediante el uso de [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Para obtener más información, consulte Introducción [a Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumir el contenedor de .NET desde una aplicación de Xamarin. Forms

Para completar el tutorial, cree una aplicación de **Xamarin. Forms** para consumir el paquete que acaba de publicar en la fuente de **NuGet** local.

### <a name="creating-the-xamarinforms-project"></a>Crear el proyecto de **Xamarin. Forms**

1. Abra una nueva instancia de **Visual Studio para Mac**. Esto se puede hacer desde el **terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. En **Visual Studio para Mac**, haga clic en **nuevo proyecto** (desde la *Página principal*) o en **nueva solución** (en el menú *archivo* ).
3. En la ventana **nuevo proyecto** , elija **aplicación de formularios en blanco** (desde la *aplicación de > multiplataforma*) y, a continuación, haga clic en **siguiente**.
4. Actualice los campos siguientes y haga clic en **siguiente**:

    - **Nombre de la aplicación:** MathFuncsApp.
    - **Identificador de la organización:** Use un espacio de nombres inverso, por ejemplo, _com. {your_org}_ .
    - **Plataformas de destino:** Use el valor predeterminado (ambos destinos iOS y Android).
    - **Código compartido:** Establézcalo en .NET Standard (una solución de "biblioteca compartida" es posible, pero más allá del ámbito de este tutorial).

5. Actualice los campos siguientes y haga clic en **crear**:

    - **Nombre del proyecto:** MathFuncsApp.
    - **Nombre de la solución:** MathFuncsApp.  
    - **Cód** Use la ubicación de almacenamiento predeterminada (o seleccione una alternativa).

6. En **Explorador de soluciones**, **control + clic** en el destino (**MathFuncsApp. Android** o **MathFuncs. iOS**) para las pruebas iniciales y, a continuación, elija **establecer como proyecto de inicio**.
7. Elija el **dispositivo** preferido o/el**emulador**del simulador. 
8. Ejecute la solución (**comando + retorno**) para validar que el proyecto de **Xamarin. Forms** con plantilla se compila y se ejecuta de forma correcta. 

    > [!NOTE]
    > **iOS** (en concreto, el simulador) tiende a tener el tiempo de compilación e implementación más rápido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Adición de la fuente de NuGet local a la configuración de NuGet

1. En **Visual Studio**, elija **preferencias** (en el menú de **Visual Studio** ).
2. Elija **orígenes** en la sección **NuGet** y, a continuación, haga clic en **Agregar**.
3. Actualice los campos siguientes y haga clic en **Agregar origen**:

    - **Name:** Proporcione un nombre descriptivo, por ejemplo, local-packages.  
    - **Cód** Especifique la carpeta **local-Nuget-feed** creada en el [paso anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > En este caso, no es necesario especificar un **nombre de usuario** y una **contraseña**. 

4. Haga clic en **OK**.

### <a name="referencing-the-package"></a>Hacer referencia al paquete

Repita los pasos siguientes para cada proyecto (**MathFuncsApp**, **MathFuncsApp. Android**y **MathFuncsApp. iOS**).

1. **Control + haga clic** en el proyecto y, a continuación, elija **agregar paquetes NuGet...** en el menú **Agregar** .
2. Busque **MathFuncs**. 
3. Compruebe que la **versión** del paquete es **1.0.0** y que los demás detalles aparecen como se esperaba, como el **título** y la **Descripción**, es decir, *MathFuncs* y la biblioteca de *contenedores de ejemplo C++* . 
4. Seleccione el paquete **MathFuncs** y, a continuación, haga clic en **Agregar paquete**.

### <a name="using-the-library-functions"></a>Usar las funciones de la biblioteca

Ahora, con una referencia al paquete **MathFuncs** en cada uno de los proyectos, las funciones están disponibles para el C# código.

1. Abra **mainpage.Xaml.CS** desde dentro del proyecto común **de Xamarin. Forms** **MathFuncsApp** (al que hacen referencia **MathFuncsApp. Android** y **MathFuncsApp. iOS**).
2. Agregue instrucciones **using** para **System. Diagnostics** y **MathFuncs** en la parte superior del archivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare una instancia de `MyMathFuncs` la clase en la parte superior `MainPage` de la clase:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. `OnAppearing` Invalide `OnDisappearing` los métodos y `ContentPage` de la clase base:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Actualice el `OnAppearing` método para inicializar `myMathFuncs` la variable declarada previamente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Actualice el `OnDisappearing` método para `Dispose` llamar al método en `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implemente un método privado denominado **TestMathFuncs** como se indica a continuación:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Por último, `TestMathFuncs` llame al final `OnAppearing` del método:

    ```csharp
    TestMathFuncs();
    ```

9. Ejecute la aplicación en cada plataforma de destino y compruebe que la salida en el panel de salida de la **aplicación** aparece de la manera siguiente:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Si encuentra un "*DLLNotFoundException*" al realizar pruebas en Android o un error de compilación en iOS, asegúrese de comprobar que la arquitectura de la CPU del dispositivo, el emulador o el simulador que está usando es compatible con el subconjunto que elegimos admitir. 

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear una aplicación de Xamarin. Forms que usa bibliotecas nativas a través de un contenedor .NET común distribuido a través de un paquete NuGet. El ejemplo que se proporciona en este tutorial es intencionalmente simplista para demostrar más fácilmente el enfoque. Una aplicación real tendrá que tratar con complejidades como el control de excepciones, las devoluciones de llamada, la serialización de tipos más complejos y la vinculación con otras bibliotecas de dependencias. Una consideración clave es el proceso por el que la evolución del C++ código se coordina y se sincroniza con el contenedor y las aplicaciones cliente. Este proceso puede variar en función de si uno o ambos aspectos son responsabilidad de un solo equipo. En cualquier caso, la automatización es una ventaja real. A continuación se muestran algunos recursos que proporcionan más información sobre algunos de los conceptos clave junto con las descargas correspondientes. 

### <a name="downloads"></a>Descargas

- [Herramientas de línea de comandos (CLI) de NuGet](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Ejemplos

- [Hyperlapse desarrollo móvil multiplataforma conC++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft PIX (C++ y Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Puerto de ejemplo mono San Ángeles](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Información adicional

[Artículos relacionados con el contenido de esta publicación](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
