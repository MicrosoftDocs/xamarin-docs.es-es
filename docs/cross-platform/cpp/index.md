---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Uso de bibliotecas de C/C++ con Xamarin
description: Visual Studio para Mac puede usarse para compilar e integrar código de C/C++ multiplataforma en aplicaciones móviles para Android y iOS, mediante Xamarin y C#. En este artículo se explica cómo configurar y depurar un proyecto de C++ proyecto en una aplicación de Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 8422affab86ea176cad4e57833188dcd5738a99a
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928508"
---
# <a name="use-cc-libraries-with-xamarin"></a>Uso de bibliotecas de C/C++ con Xamarin

## <a name="overview"></a>Información general

Xamarin permite a los desarrolladores crear aplicaciones móviles nativas multiplataforma con Visual Studio. Por lo general, los enlaces de C# se utilizan para exponer los componentes de la plataforma a los desarrolladores. Sin embargo, hay ocasiones en las que las aplicaciones de Xamarin necesitan trabajar con códigos base. A veces, los equipos simplemente no tienen el tiempo, el presupuesto o los recursos necesarios para portar a C# un código base grande, probado correctamente y muy optimizado.

[Visual C++ para desarrollo móvil multiplataforma](/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) permite que los códigos de C/C++ y C# se compile como parte de la misma solución, lo que ofrece muchas ventajas, por ejemplo, una experiencia de depuración unificada. Microsoft ha usado C/C++ y Xamarin de esta manera para ofrecer aplicaciones como [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) y [Pix Camera](https://www.microsoft.com/microsoftpix).

Sin embargo, en algunos casos es recomendable (u obligatorio) tener activos los procesos y las herramientas de C/C++, además de mantener el código de la biblioteca desacoplado de la aplicación, tratando la biblioteca como algo similar a un componente de terceros. En estas situaciones, el desafío no solo consiste en exponer los miembros pertinentes a C#, sino en administrar la biblioteca como una dependencia. Y, por supuesto, automatizar todo lo posible este proceso.  

En esta publicación se describe un enfoque general para este escenario y se describe un ejemplo sencillo.

## <a name="background"></a>Segundo plano

C/C++ se considera un lenguaje multiplataforma, pero se debe prestar mucha atención para asegurarse de que el código fuente sea realmente multiplataforma. Se debe usar solamente el código de C/C++ que admitan todos los compiladores de destino, y no debe haber ningún código específico del compilador o de la plataforma incluido condicionalmente (o, en su defecto, que haya poco).

En última instancia, el código debe compilarse y ejecutarse correctamente en todas las plataformas de destino, por lo tanto, se reduce la similitud entre las plataformas (y los compiladores) de destino. Los problemas pueden seguir surgiendo por pequeñas diferencias entre los compiladores y, por lo tanto, cada vez es más importante realizar pruebas exhaustivas (preferiblemente automatizadas) en cada plataforma de destino.  

## <a name="high-level-approach"></a>Enfoque de alto nivel

La ilustración siguiente representa el enfoque de cuatro fases que se usa para transformar el código fuente de C/C++ en una biblioteca de Xamarin multiplataforma que se comparte a través de NuGet y que, a continuación, se consume en una aplicación de Xamarin.Forms.

![Enfoque general para usar C/C++ con Xamarin](images/cpp-steps.jpg)

Las cuatro fases son:

1. Compilar el código fuente de C/C++ en bibliotecas nativas específicas de la plataforma
2. Encapsular las bibliotecas nativas con una solución de Visual Studio
3. Empaquetar e insertar un paquete de NuGet para el contenedor de .NET
4. Consumir el paquete de NuGet desde una aplicación de Xamarin

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1 Compilar el código fuente de C/C++ en bibliotecas nativas específicas de la plataforma

El objetivo de esta fase es crear bibliotecas nativas a las que pueda llamar el contenedor de C#. En función de su situación, puede que le resulte relevante o que no. Los numerosos procesos y herramientas que se pueden tener en cuenta en este escenario común quedan fuera del ámbito de este artículo. Las consideraciones clave son mantener sincronizado el código base de C/C++ con cualquier código de contenedor nativo, realizar las suficientes pruebas unitarias y automatizar la compilación.

Las bibliotecas del tutorial se crearon con Visual Studio Code con un script de Shell complementario. Puede encontrar una versión ampliada de este tutorial en el repositorio [Mobile CAT GitHub](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) que trata esta parte del ejemplo con mayor profundidad. Las bibliotecas nativas se tratan como una dependencia de terceros en este caso, sin embargo, esta fase se ilustra para que disponga de contexto.

Con el fin de simplificar, el tutorial solo tiene como destino un subconjunto de arquitecturas. En iOS, usa la utilidad lipo para crear un único archivo binario FAT a partir de archivos binarios específicos de la arquitectura. Android usará archivos binarios dinámicos con una extensión .so y iOS utilizará un archivo binario FAT estático con una extensión .a.

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: Encapsular las bibliotecas nativas con una solución de Visual Studio

La siguiente fase consiste en encapsular las bibliotecas nativas para que se puedan usar fácilmente desde .NET. Esto se hace con una solución de Visual Studio con cuatro proyectos. Un proyecto compartido contiene el código común. Los proyectos que tienen como destino Xamarin.Android, Xamarin.iOS y .NET Standard permiten que se haga referencia a la biblioteca de manera independiente de la plataforma.

El contenedor usa el [truco del señuelo y cambio](https://github.com/JFMG/Bait-and-Switch-PCL-example). Esta no es la única manera, pero facilita el proceso de hacer referencia a la biblioteca y evita la necesidad de administrar explícitamente implementaciones específicas de la plataforma dentro de la propia aplicación de consumo. El truco consiste esencialmente en garantizar que los destinos (.NET Standard, Android y iOS) compartan el mismo espacio de nombres, nombre de ensamblado y estructura de clase. Dado que NuGet siempre preferirá una biblioteca específica de la plataforma, la versión de .NET Standard nunca se utiliza en el runtime.

La mayor parte del trabajo de este paso se centrará en el uso de P/Invoke para llamar a los métodos de la biblioteca nativa y administrar las referencias a los objetos subyacentes. El objetivo es exponer la funcionalidad de la biblioteca al consumidor a la vez que se abstrae cualquier complejidad. Los desarrolladores de Xamarin.Forms no necesitan tener conocimientos prácticos sobre el funcionamiento interno de la biblioteca no administrada. El comportamiento debería ser similar al de cualquier otra biblioteca de C# administrada.

En última instancia, la salida de esta fase es un conjunto de bibliotecas de .NET, una por destino, junto con un documento nuspec que contiene la información necesaria para compilar el paquete en el paso siguiente.

**Fase 3: Empaquetar e insertar un paquete de NuGet para el contenedor de .NET**

La tercera fase consiste en la creación de un paquete de NuGet mediante los artefactos de compilación del paso anterior. El resultado de este paso es un paquete de NuGet que se puede usar desde una aplicación de Xamarin. El tutorial usa un directorio local como fuente de NuGet. En producción, este paso debe publicar un paquete en una fuente de NuGet pública o privada, y estar totalmente automatizado.

**Fase 4: Consumir el paquete de NuGet desde una aplicación de Xamarin.Forms**

El paso final consiste en crear la referencia y usar el paquete de NuGet desde una aplicación de Xamarin.Forms. Para ello, hay que configurar la fuente de NuGet en Visual Studio para usar la fuente definida en el paso anterior.

Una vez configurada la fuente, se debe hacer referencia al paquete desde cada proyecto en la aplicación multiplataforma de Xamarin.Forms. El truco del señuelo y cambio proporciona interfaces idénticas, por lo que se puede llamar a la funcionalidad de la biblioteca nativa mediante el código definido en una sola ubicación.

El repositorio del código fuente contiene una [lista de lecturas de ampliación](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) que incluye artículos sobre cómo configurar una fuente de NuGet privada en Azure DevOps y cómo insertar el paquete en esa fuente. Aunque requiere un poco más de tiempo de configuración que con un directorio local, este tipo de fuente es mejor en un entorno de desarrollo en equipo.

## <a name="walk-through"></a>Ejemplo paso a paso

Los pasos proporcionados son específicos de **Visual Studio para Mac** , pero la estructura también funciona en **Visual Studio 2017**.

### <a name="prerequisites"></a>Requisitos previos

Para seguir adelante, el desarrollador necesitará lo siguiente:

- [Línea de comandos de NuGet (CLI)](/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *para Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Se requiere una [**cuenta de desarrollador de Apple**](https://developer.apple.com/) para implementar aplicaciones en un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Creación de las bibliotecas nativas (fase 1)

La funcionalidad de la biblioteca nativa se basa en el ejemplo de [Tutorial: Crear y utilizar una biblioteca estática (C++)](/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

En este tutorial se pasa por alto la primera fase, donde se compilan las bibliotecas nativas, ya que la biblioteca se proporciona como una dependencia de terceros en este escenario. Las bibliotecas nativas precompiladas se incluyen junto con el [código de ejemplo](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) o [se pueden descargar](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) directamente.

### <a name="working-with-the-native-library"></a>Uso de la biblioteca nativa

El ejemplo original *MathFuncsLib* incluye una sola clase denominada `MyMathFuncs` con la siguiente definición:

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

Una clase adicional define las funciones de contenedor que permiten a un consumidor de .NET para crear, eliminar e interactuar con la clase `MyMathFuncs` nativa subyacente.

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

Serán estas funciones de contenedor las que se usen en [Xamarin](https://visualstudio.microsoft.com/xamarin/).

## <a name="wrapping-the-native-library-stage-2"></a>Encapsulamiento de la biblioteca nativa (fase 2)

Esta fase requiere que las [bibliotecas precompiladas](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) se describen en la sección [anterior](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Creación de la solución de Visual Studio

1. En **Visual Studio para Mac** , haga clic en **Nuevo proyecto** (en la *página de bienvenida* ) o en **Nueva solución** (en el menú *Archivo* ).
2. En la ventana **Nuevo proyecto** , elija **Proyecto compartido** (en *Multiplataforma > Biblioteca* ) y, a continuación, haga clic en **Siguiente**.
3. Actualice los campos siguientes y haga clic en **Crear** :

    - **Nombre del proyecto:** MathFuncs.Shared  
    - **Nombre de la solución:** MathFuncs  
    - **Ubicación:** use la ubicación de almacenamiento predeterminada (o elija una alternativa)   
    - **Crear un directorio de proyecto dentro del directorio de la solución:** active esta opción
4. En el **Explorador de soluciones** , haga doble clic en el proyecto **MathFuncs.Shared** y navegue hasta **Configuración principal**.
5. Quite **.Shared** del **espacio de nombres predeterminado** de modo que solo se establezca en **MathFuncs** y, a continuación, haga clic en **Aceptar**.
6. Abra **MyClass.cs** (creado por la plantilla) y, a continuación, cambie el nombre de la clase y del archivo a **MyMathFuncsWrapper** , y cambie el espacio de nombres a **MathFuncs**.
7. **Presione Ctrl y haga clic** en la solución **MathFuncs** , a continuación, elija **Agregar nuevo proyecto...** en el menú **Agregar**.
8. En la ventana **Nuevo proyecto** , elija **Biblioteca de .NET Standard** (en *Multiplataforma > Biblioteca* ) y, a continuación, haga clic en **Siguiente**.
9. Elija **.NET Standard 2.0** y seleccione **Siguiente**.
10. Actualice los campos siguientes y haga clic en **Crear** :

    - **Nombre del proyecto:** MathFuncs.Standard  
    - **Ubicación:** use la misma ubicación de almacenamiento que el proyecto compartido   

11. En el **Explorador de soluciones** , haga doble clic en el proyecto **MathFuncs.Standard**.
12. Vaya a **Configuración principal** y, después, actualice el **espacio de nombres predeterminado** a **MathFuncs**.
13. Navegue a la configuración de **Salida** y, a continuación, actualice el valor de **Nombre del ensamblado** a **MathFuncs**.
14. Desplácese a la configuración de **Compilador** , cambie la **configuración** a **Versión** , estableciendo **Información de depuración** en **Solo símbolos** ; a continuación, haga clic en **Aceptar**.
15. Elimine **Class1.cs/Getting Started** del proyecto (si una de ellas se ha incluido como parte de la plantilla).
16. **Presione Ctrl y haga clic** en el proyecto **Dependencies/References** y, a continuación, elija **Editar referencias**.
17. Seleccione **MathFuncs.Shared** en la pestaña **Proyectos** y, a continuación, haga clic en **Aceptar**.
18. Repita los pasos del 7 al 17 (saltándose el 9) con las siguientes configuraciones:

    | **NOMBRE DEL PROYECTO**  | **NOMBRE DE LA PLANTILLA**   | **MENÚ NUEVO PROYECTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Biblioteca de clases       | Android > Biblioteca      |
    | MathFuncs.iOS     | Biblioteca de enlaces     | iOS > Biblioteca          |

19. En el **Explorador de soluciones** , haga doble clic en el proyecto **MathFuncs.Android** y navegue hasta la configuración **Compilador**.

20. Con la **configuración** establecida en **Depuración** , edite **Definir símbolos** para incluir **Android;** .

21. Cambie la **configuración** a **Versión** , edite **Definir símbolos** para incluir también **Android;** .

22. Repita los pasos del 19 al 20 para **MathFuncs.iOS** , editando **Definir símbolos** para incluir **iOS;** en lugar de **Android;** en ambos casos.

23. Compile la solución en la configuración de **Versión** ( **CONTROL + COMANDO + B** ) y compruebe que los tres ensamblados de salida (Android, iOS y .NET Standard) (en las carpetas correspondientes del proyecto) comparten el mismo nombre **MathFuncs.dll**.

En esta fase, la solución debe tener tres destinos, uno para Android, iOS y .NET Standard, y un proyecto compartido al que hagan referencia cada uno de los tres destinos. Deben configurarse para usar el mismo espacio de nombres y ensamblados de salida predeterminados con el mismo nombre. Esto es necesario para el enfoque de señuelo y cambio mencionado anteriormente.

### <a name="adding-the-native-libraries"></a>Incorporación de las bibliotecas nativas

El proceso de agregar las bibliotecas nativas a la solución de contenedor varía ligeramente entre iOS y Android.

#### <a name="native-references-for-mathfuncsandroid"></a>Referencias nativas de MathFuncs.Android

1. **Presione Ctrl y haga clic** en el proyecto **MathFuncs.Android** y, a continuación, elija **Nueva carpeta** en el menú **Agregar** con el nombre **libs**.

2. En cada **ABI** (interfaz binaria de aplicación), **presione Ctrl y haga clic** en la carpeta **libs** y, a continuación, elija **Nueva carpeta** en el menú **Agregar** y asígnele un nombre después de su respectiva **ABI**. En este caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Para obtener información general más detallada, consulte el tema [Arquitecturas y CPU](https://developer.android.com/ndk/guides/arch) de la [guía para desarrolladores de NDK](https://developer.android.com/ndk/guides/), en concreto, la sección sobre cómo tratar el [código nativo en los paquetes de aplicaciones](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Compruebe la estructura de carpetas:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Agregue las bibliotecas **.so** correspondientes a cada una de las carpetas **ABI** basadas en la siguiente asignación:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Para agregar archivos, **presione Ctrl y haga clic** en la carpeta que representa la **ABI** correspondiente y, a continuación, elija **Agregar archivos...** en el menú **Agregar**. Elija la biblioteca adecuada (en el directorio **PrecompiledLibs** ), haga clic en **Abrir** y, a continuación, haga clic en **Aceptar** , dejando la opción predeterminada en *Copiar el archivo en el directorio*.

5. Para cada uno de los archivos **.so** , **presione Ctrl y haga clic** , a continuación, elija la opción **EmbeddedNativeLibrary** en el menú **Acción de compilación**.

Ahora la carpeta **libs** debe aparecer de la siguiente manera:

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

#### <a name="native-references-for-mathfuncsios"></a>Referencias nativas de MathFuncs.iOS

1. **Presione Ctrl y haga clic** en el proyecto **MathFuncs.iOS** y, a continuación, elija **Agregar referencia nativa** en el menú **Agregar**.
2. Elija la biblioteca **libMathFuncs.a** (en libs/iOS, en el directorio **PrecompiledLibs** ) y, a continuación, haga clic en **Abrir**
3. **Presione Ctrl y haga clic** en el archivo **libMathFuncs** (en la carpeta **Referencias nativas** y, a continuación, elija la opción **Propiedades** en el menú.  
4. Configure las propiedades de **Referencia nativa** para que se comprueben (mostrando un icono de marca) en el panel de **propiedades** :

    - Forzar carga
    - Es C++
    - Conexión inteligente

    > [!NOTE]
    > El uso de un tipo de proyecto de biblioteca de enlace junto con una [referencia nativa](../macios/native-references.md) incrusta la biblioteca estática y permite que se vincule automáticamente a la aplicación de Xamarin.iOS que hace referencia a ella (incluso cuando se incluye a través de un paquete de NuGet).

5. Abra **ApiDefinition.cs** , eliminando el código comentado con plantilla (dejando solo el espacio de nombres `MathFuncs`) y, a continuación, realice el mismo paso para **Structs.cs**.

    > [!NOTE]
    > Un proyecto de biblioteca de enlaces requiere estos archivos (con las acciones de compilación *ObjCBindingApiDefinition* y *ObjCBindingCoreSource* ) para poder compilar. Sin embargo, escribiremos el código para llamar a nuestra biblioteca nativa, fuera de estos archivos, de una manera que se pueda compartir entre ambos destinos de la biblioteca de iOS y Android mediante P/Invoke estándar.

### <a name="writing-the-managed-library-code"></a>Escritura del código de biblioteca administrada

Ahora, escriba el código de C# para llamar a la biblioteca nativa. El objetivo es ocultar cualquier complejidad subyacente. El consumidor no debería necesitar ningún conocimiento práctico de los conceptos internos de la biblioteca nativa o de P/Invoke.  

#### <a name="creating-a-safehandle"></a>Creación de un objeto SafeHandle

1. **Presione Ctrl y haga clic** en el proyecto **MathFuncs.Shared** y, a continuación, elija **Agregar archivo...** en el menú **Agregar**.
2. Elija **Clase vacía** en la ventana **Nuevo archivo** , asígnele el nombre **MyMathFuncsSafeHandle** y, a continuación, haga clic en **Nuevo**.
3. Implemente la clase **MyMathFuncsSafeHandle** :

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Un objeto [SafeHandle](/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) es la manera preferida de trabajar con recursos no administrados en código administrado. Esto abstrae una gran cantidad de código reutilizable relacionado con la finalización crítica y el ciclo de vida de los objetos. Después, el propietario de este identificador puede tratarlo como cualquier otro recurso administrado y no tendrá que implementar el [patrón desechable](/dotnet/standard/garbage-collection/implementing-dispose) completo.

#### <a name="creating-the-internal-wrapper-class"></a>Creación de la clase interna contenedora

1. Abra **MyMathFuncsWrapper.cs** y cambie a una clase estática interna.

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. En el mismo archivo, agregue la siguiente instrucción condiciones a la clase:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Esto establece la constante **DllName** en función de si la biblioteca se está generando para **Android** o **iOS**. De este modo, se pueden abordar las distintas convenciones de nomenclatura que emplea cada plataforma respectiva, pero también el tipo de biblioteca que se usa en este caso. Android usa una biblioteca dinámica, por lo que espera un nombre de archivo que incluya la extensión. En iOS, se requiere *__Internal* porque usamos una biblioteca estática.

3. Agregue una referencia a **System.Runtime.InteropServices** en la parte superior del archivo **MyMathFuncsWrapper.cs**.

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Agregue los métodos contenedores para controlar la creación y eliminación de la clase **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Pasamos la constante **DllName** al atributo **DllImport** junto con el valor de **EntryPoint** , que indica explícitamente al runtime de .NET el nombre de la función a la que se llamará dentro de esa biblioteca. Técnicamente, no es necesario proporcionar el valor de **EntryPoint** si nuestros nombres de método administrado eran idénticos a los no administrados. Si no se proporciona ninguno, el nombre del método administrado se utilizaría como el valor de **EntryPoint** en su lugar. Sin embargo, es mejor que sea explícito.  

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
    > Estamos usando tipos primitivos en los parámetros de este ejemplo. Como la serialización es una copia bit a bit en este caso, no es necesario que hagamos nada. Observe también el uso de la clase **MyMathFuncsSafeHandle** en lugar del objeto **IntPtr** estándar. El objeto **IntPtr** se asigna automáticamente a **SafeHandle** como parte del proceso de serialización.

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

1. Abra la clase **MyMathFuncsSafeHandle** , navegue hasta el comentario **TODO** de marcador de posición en el método **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Reemplace la línea de **TODO** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Escritura de la clase MyMathFuncs

Ahora que el contenedor está completo, cree una clase MyMathFuncs que administre la referencia al objeto MyMathFuncs de C++ no administrado.  

1. **Presione Ctrl y haga clic** en el proyecto **MathFuncs.Shared** y, a continuación, elija **Agregar archivo...** en el menú **Agregar**.
2. Elija **Clase vacía** en la ventana **Nuevo archivo** , asígnele el nombre **MyMathFuncs** y, a continuación, haga clic en **Nuevo**.
3. Agregue los siguientes miembros a la clase **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implemente el constructor de la clase para que cree y almacene un manipulador para el objeto nativo **MyMathFuncs** cuando se crea una instancia de la clase:

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

6. Implemente los métodos **MyMathFuncs** con la clase **MyMathFuncsWrapper** para realizar el trabajo real de forma interna pasando el puntero que hemos almacenado en el objeto no administrado subyacente. El código debería ser como el siguiente:

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

#### <a name="creating-the-nuspec"></a>Creación del archivo nuspec

Para que la biblioteca esté empaquetada y distribuida a través de NuGet, la solución necesita un archivo **nuspec**. Esto identificará los ensamblados resultantes que se incluirán en cada plataforma compatible.

1. **Presione Ctrl y haga clic** en la solución **MathFuncs** , a continuación, elija **Agregar carpeta de soluciones** en el menú **Agregar** y asígnele el nombre **SolutionItems**.
2. **Presione Ctrl y haga clic** en la carpeta **SolutionItems** y, a continuación, elija **Nuevo archivo...** en el menú **Agregar**.
3. Elija **Archivo XML vacío** en la ventana **Nuevo archivo** , asígnele el nombre **MathFuncs.nuspec** y, a continuación, haga clic en **Nuevo**.
4. Actualice **MathFuncs.nuspec** con los metadatos de paquete básicos que se mostrarán en el consumidor de **NuGet**. Por ejemplo:

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
    > Consulte el documento de [referencia de nuspec](/nuget/reference/nuspec) para obtener más detalles sobre el esquema que se usa para este manifiesto.

5. Agregue un elemento `<files>` como elemento secundario del elemento `<package>` (justo debajo de `<metadata>`), identificando cada archivo con un elemento `<file>` independiente:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Cuando se instala un paquete en un proyecto y se especifican varios ensamblados con el mismo nombre, NuGet elige el ensamblado más específico para la plataforma determinada.

6. Agregue los elementos `<file>` para los ensamblados de **Android** :

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Agregue los elementos `<file>` para los ensamblados de **iOS** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Agregue los elementos `<file>` para los ensamblados de **netstandard2.0** :

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

Llegados a este punto, la solución contiene tres ensamblados .NET y un manifiesto de **nuspec** de apoyo.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribución del contenedor de .NET con NuGet

El paso siguiente consiste en empaquetar y distribuir el paquete de NuGet para que la aplicación pueda usarlo fácilmente y administrarse como una dependencia. El ajuste y el consumo se pueden realizar en una única solución, pero la distribución de la biblioteca a través de NuGet ayuda en el proceso de desacoplamiento y nos permite administrar estos códigos base de forma independiente.

### <a name="preparing-a-local-packages-directory"></a>Preparación de un directorio de paquetes locales

La forma más sencilla de la fuente de NuGet es un directorio local:

1. En **Finder** , desplácese a un directorio adecuado. Por ejemplo, **/Users**.
2. Elija **Nueva carpeta** en el menú **Archivos** de archivos y proporcione un nombre descriptivo como **local-nuget-feed**.

### <a name="creating-the-package"></a>Creación del paquete

1. Establezca **Configuración de compilación** en **Versión** y ejecute una compilación con **COMANDO + B**.
2. Abra **Terminal** y cambie el directorio a uno que contenga el archivo **nuspec**.
3. En **Terminal** , ejecute el comando **nuget pack** que especifica el archivo **nuspec** , la **versión** (por ejemplo, 1.0.0) y el **directorio de salida** mediante la carpeta creada en el [paso anterior](#preparing-a-local-packages-directory), es decir, **local-nuget-feed**. Por ejemplo:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confirme** que se ha creado **MathFuncs.1.0.0.nupkg** en el directorio **local-nuget-feed**.

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>Uso de una fuente de NuGet privada con Azure DevOps (opcional)

Se describe una técnica más sólida en el artículo de [introducción a los paquetes de NuGet en Azure DevOps](/azure/devops/artifacts/get-started-nuget?tabs=new-nav&view=vsts#publish-a-package), que muestra cómo crear una fuente privada e insertar el paquete (generado en el paso anterior) en esa fuente.

Es ideal que este flujo de trabajo esté totalmente automatizado, por ejemplo, con [Azure Pipelines](/azure/devops/pipelines/index?view=vsts). Para obtener más información, consulte el artículo de [introducción a Azure Pipelines](/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Consumo del paquete de NuGet desde una aplicación de Xamarin.Forms

Para completar el tutorial, cree una **aplicación de Xamarin.Forms** para consumir el paquete que acaba de publicar en la fuente de **NuGet** local.

### <a name="creating-the-xamarinforms-project"></a>Creación de un proyecto de **Xamarin.Forms**

1. Abra una segunda instancia de **Visual Studio para Mac**. Esto puede hacerse desde **Terminal** :

    ```bash
    open -n -a "Visual Studio"
    ```

2. En **Visual Studio para Mac** , haga clic en **Nuevo proyecto** (en la *página de bienvenida* ) o en **Nueva solución** (en el menú *Archivo* ).
3. En la ventana **Nuevo proyecto** , elija **Aplicación de Forms en blanco** (en *Multiplataforma > Biblioteca* ) y, a continuación, haga clic en **Siguiente**.
4. Actualice los campos siguientes y haga clic en **Siguiente** :

    - **Nombre de la aplicación:** MathFuncsApp.
    - **Identidad de la organización:** use un espacio de nombres inverso, por ejemplo, _com.{su_organización}_ .
    - **Plataformas de destino:** use el valor predeterminado (ambos destinos de iOS y Android).
    - **Código compartido:** establézcalo en .NET Standard (una solución de "biblioteca compartida" es posible, pero no entra dentro del ámbito de este tutorial).

5. Actualice los campos siguientes y haga clic en **Crear** :

    - **Nombre del proyecto:** MathFuncsApp.
    - **Nombre de la solución:** MathFuncsApp.  
    - **Ubicación:** use la ubicación de almacenamiento predeterminada (o elija una alternativa).

6. En el **Explorador de soluciones** , **presione Ctrl y haga clic** en el destino ( **MathFuncsApp.Android** o **MathFuncs.iOS** ) para las pruebas iniciales, después, elija **Establecer como proyecto de inicio**.
7. Elija el **dispositivo** o el **simulador**/**emulador** que prefiera.
8. Ejecute la solución ( **COMANDO + RETROCESO** ) para validar que el proyecto con plantilla **Xamarin.Forms** se compila y se ejecuta de forma correcta.

    > [!NOTE]
    > **iOS** (en concreto, el simulador) tiende a tener el tiempo de compilación e implementación más rápido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Incorporación de la fuente de NuGet local a la configuración de NuGet

1. En **Visual Studio** , elija **Preferencias** (en el menú **Visual Studio** ).
2. Elija **Orígenes** en la sección **NuGet** y, a continuación, haga clic en **Agregar**.
3. Actualice los campos siguientes y haga clic en **Agregar origen** :

    - **Name:** proporcione un nombre descriptivo, por ejemplo, Paquetes-Locales.  
    - **Ubicación:** especifique la carpeta **local-nuget-feed** creada en el [paso anterior](#preparing-a-local-packages-directory).

    > [!NOTE]
    > En este caso, no es necesario especificar un **nombre de usuario** ni una **contraseña**.

4. Haga clic en **OK**.

### <a name="referencing-the-package"></a>Hacer referencia al paquete

Repita los pasos siguientes para cada proyecto ( **MathFuncsApp** , **MathFuncsApp.Android** y **MathFuncsApp.iOS** ).

1. **Presione Ctrl y haga clic** en el proyecto y, a continuación, elija **Agregar paquetes de NuGet...** desde el menú **Agregar**.
2. Busque **MathFuncs**.
3. Compruebe que la **versión** del paquete es **1.0.0** y que los demás detalles son los esperados, como el **título** y la **descripción** , es decir, *MathFuncs* y la *biblioteca contenedora de C++ de ejemplo*.
4. Seleccione el paquete **MathFuncs** y, a continuación, haga clic en **Agregar paquete**.

### <a name="using-the-library-functions"></a>Uso de las funciones de biblioteca

Ahora, con una referencia al paquete **MathFuncs** en cada uno de los proyectos, las funciones están disponibles para el código de C#.

1. Abra **MainPage.xaml.cs** desde el proyecto **Xamarin.Forms** común de **MathFuncsApp** (al que se hace referencia en **MathFuncsApp.Android** y **MathFuncsApp.iOS** ).
2. Agregue instrucciones **using** para **System.Diagnostics** y **MathFuncs** en la parte superior del archivo:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Declare una instancia de la clase `MyMathFuncs` en la parte superior de la clase `MainPage`:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Invalide los métodos `OnAppearing` y `OnDisappearing` de la clase base `ContentPage`:

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

5. Actualice el método `OnAppearing` para inicializar la variable `myMathFuncs` declarada previamente:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Actualice el método `OnDisappearing` para llamar al método `Dispose` en `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implemente un método privado llamado **TestMathFuncs** como se indica a continuación:

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

8. Finalmente, llame a `TestMathFuncs` al final del método `OnAppearing`:

    ```csharp
    TestMathFuncs();
    ```

9. Ejecute la aplicación en cada plataforma de destino y valide la salida en el **panel de salida de la aplicación** de la siguiente forma:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Si se produce una excepción *DLLNotFoundException* al realizar pruebas en Android, o un error de compilación en iOS, asegúrese de comprobar que la arquitectura de la CPU del dispositivo, el emulador o el simulador que está usando son compatibles con el subconjunto que elegimos admitir.

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear una aplicación de Xamarin.Forms que usa bibliotecas nativas a través de un contenedor de .NET común distribuido a través de un paquete de NuGet. El ejemplo que se proporciona en este tutorial es intencionalmente sencillo para demostrar más fácilmente el enfoque. Una aplicación real tendrá que tratar con complejidades como el control de excepciones, las devoluciones de llamadas, la serialización de tipos más complejos y la vinculación con otras bibliotecas de dependencias. Una consideración clave es el proceso por el que la evolución del código de C++ se coordina y se sincroniza con el contenedor y las aplicaciones cliente. Este proceso puede variar en función de si uno o ambos aspectos son responsabilidad de un solo equipo. En cualquier caso, la automatización es una ventaja real. A continuación se muestran algunos recursos que proporcionan más información sobre algunos de los conceptos clave, además de los vínculos de descarga correspondientes.

### <a name="downloads"></a>Descargas

- [Herramientas de línea de comandos (CLI) de NuGet](/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Ejemplos

- [Desarrollo móvil multiplataforma de Hyperlapse con C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft PIX (C++ y Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Puerto de ejemplo Mono San Angeles](/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Lecturas adicionales

[Artículos relacionados con el contenido de esta publicación](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)
