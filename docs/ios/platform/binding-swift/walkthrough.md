---
title: 'Tutorial: Enlace de una biblioteca Swift de iOS'
description: En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. iOS para un marco de trabajo de SWIFT existente, Gigya. Trata temas como la compilación de un marco de trabajo de SWIFT, el enlace y el uso del enlace en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 3c63b1a4ed58b0efcc510085934a5380e6049ae7
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853149"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>Tutorial: Enlace de una biblioteca Swift de iOS

> [!IMPORTANT]
> Actualmente estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Lleve a cabo [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar sobre los esfuerzos futuros de desarrollo.

Xamarin permite a los desarrolladores de dispositivos móviles crear experiencias móviles nativas multiplataforma con Visual Studio y C#. Puede usar los componentes del SDK de la plataforma iOS de la caja. Pero en muchos casos, también quiere usar los SDK de terceros desarrollados para esa plataforma, que Xamarin permite hacer a través de los enlaces. Para incorporar un marco de trabajo de Objective-C de terceros a la aplicación de Xamarin. iOS, debe crear un enlace de Xamarin. iOS para poder usarlo en sus aplicaciones.

La plataforma iOS, junto con sus lenguajes y herramientas nativos, están evolucionando constantemente y SWIFT es una de las áreas más dinámicas en el mundo del desarrollo de iOS. Hay una serie de SDK de terceros, que ya se han migrado de Objective-C a SWIFT y nos presenta nuevos desafíos. Aunque el proceso de enlace de SWIFT es similar a Objective-C, requiere pasos adicionales y valores de configuración para compilar y ejecutar correctamente una aplicación de Xamarin. iOS que sea aceptable para el AppStore.

El objetivo de este documento es describir un enfoque de alto nivel para abordar este escenario y proporcionar una guía paso a paso detallada con un ejemplo sencillo.

## <a name="background"></a>Fondo

En la versión 2014, la característica SWIFT fue introducida inicialmente por Apple en y ahora está en la versión 5,1 con la adopción de marcos de terceros que crecen con rapidez. Tiene algunas opciones para enlazar un marco de trabajo de SWIFT y este documento describe el enfoque mediante el encabezado de la interfaz generada por Objective-C. La herramienta Xcode crea automáticamente el encabezado cuando se crea un marco de trabajo, y se usa como una manera de comunicarse desde el mundo administrado al mundo SWIFT.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started#installing-objective-sharpie)
- [APPCENTER CLI](https://docs.microsoft.com/appcenter/test-cloud/) (opcional)

## <a name="build-a-native-library"></a>Crear una biblioteca nativa

El primer paso es crear un marco de trabajo de SWIFT nativo con el encabezado Objective-C habilitado. El marco de trabajo lo proporciona normalmente un desarrollador de terceros y tiene el encabezado incrustado en el paquete en el directorio siguiente: ** \<FrameworkName> . Framework/headers/ \<FrameworkName> -SWIFT. h**.

Este encabezado expone las interfaces públicas, que se usarán para crear los metadatos de enlace de Xamarin. iOS y generar clases de C# que exponen los miembros de la plataforma SWIFT. Si el encabezado no existe o tiene una interfaz pública incompleta (por ejemplo, no ve clases o miembros) tiene dos opciones:

- Actualice el código fuente de SWIFT para generar el encabezado y marcar los miembros necesarios con el `@objc` atributo.
- Compilar un marco de proxy donde se controla la interfaz pública y el proxy todas las llamadas al marco subyacente

En este tutorial, se describe el segundo enfoque, ya que tiene menos dependencias en el código fuente de terceros, que no siempre está disponible. Otro motivo para evitar el primer enfoque es el esfuerzo adicional necesario para admitir futuros cambios de marco. Una vez que empiece a agregar cambios al código fuente de terceros, es responsable de dar soporte a estos cambios y, potencialmente, combinarlos con cada actualización futura.

Como ejemplo, en este tutorial se crea un enlace para el [SDK de Gigya SWIFT](https://developers.gigya.com/display/GD/Swift+SDK) :

1. Abra Xcode y cree un nuevo marco de SWIFT, que será un proxy entre el código de Xamarin. iOS y el marco SWIFT de terceros. Haga clic en **archivo > nuevo > proyecto** y siga los pasos del asistente:

    ![proyecto de creación de Framework Xcode](walkthrough-images/xcode-create-framework-project.png)

    ![proyecto de nombre de Xcode](walkthrough-images/xcode-name-framework-project.png)

1. Descargue el [marco de trabajo de Gigya](https://developers.gigya.com/display/GD/Swift+SDK) desde el sitio web para desarrolladores y desempaquete. En el momento de escribir este documento, la versión más reciente es [Gigya SWIFT SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. Seleccione **SwiftFrameworkProxy** en el explorador de archivos de proyecto y, a continuación, seleccione la pestaña general.

1. Arrastre y coloque el paquete **Gigya. Framework** en la lista marcos de trabajo y bibliotecas de Xcode en la pestaña general, active la opción **copiar elementos si es necesario** mientras agrega el marco de trabajo:

    ![marco de copia de Xcode](walkthrough-images/xcode-copy-framework.png)

    Compruebe que se ha agregado el marco de trabajo de SWIFT al proyecto; de lo contrario, las siguientes opciones no estarán disponibles.

1. Asegúrese de que la opción no **Insertar** esté seleccionada, que se controlará más adelante manualmente:

    [![Xcode donotembed, opción](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. Asegúrese de que la opción de configuración de compilación **siempre inserta las bibliotecas estándar de SWIFT**, lo que incluye las bibliotecas de SWIFT con el marco de trabajo establecido en no. Se controlará más adelante de forma manual, que SWIFT dylibs se incluye en el paquete final:

    [![Xcode siempre inserta la opción false](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. Asegúrese de que la opción **Habilitar Bitcode** está establecida en **no**. A partir de ahora, Xamarin. iOS no incluye Bitcode, mientras que Apple requiere que todas las bibliotecas admitan las mismas arquitecturas:

    [![Xcode enable Bitcode false (opción)](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    Para comprobar que el marco resultante tiene la opción Bitcode deshabilitada, ejecute el siguiente comando de terminal en el marco de trabajo:

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    La salida debe estar vacía; de lo contrario, querrá revisar la configuración del proyecto para la configuración específica.

1. Asegúrese de que la opción **nombre de encabezado de interfaz generada por Objective-C** esté habilitada y especifique un nombre de encabezado. El nombre predeterminado es ** \<FrameworkName> -SWIFT. h**:

    [![Xcode objectice-c encabezado de la opción habilitada](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. Exponga los métodos deseados y márquelos con el `@objc` atributo y aplique las reglas adicionales que se definen a continuación. Si compila el marco de trabajo sin este paso, el encabezado Objective-C generado estará vacío y Xamarin. iOS no podrá tener acceso a los miembros de la plataforma SWIFT. Exponga la lógica de inicialización del SDK de Gigya SWIFT subyacente mediante la creación de un nuevo archivo SWIFT **SwiftFrameworkProxy. SWIFT** y la definición del código siguiente:

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    Algunas notas importantes sobre el código anterior:

    - Importe el módulo Gigya aquí del SDK de Gigya de terceros original y ahora puede tener acceso a cualquier miembro del marco.
    - Marque la clase SwiftFrameworkProxy con el `@objc` atributo especificando un nombre; de lo contrario, se generará un nombre no ilegible único, como `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy` . El nombre de tipo debe estar claramente definido porque se usará más adelante por su nombre.
    - Heredar la clase de proxy de `NSObject` ; de lo contrario, no se generará en el archivo de encabezado Objective-C.
    - Marque todos los miembros que se van a exponer como `public` .

1. Cambie la configuración de compilación del esquema de **Debug** a **Release**. Para ello, abra el cuadro de diálogo de **destino de > Xcode > editar esquema** y establezca la opción de **configuración de compilación** en **Release**:

    ![esquema de edición de Xcode](walkthrough-images/xcode-edit-scheme.png)

    ![versión de esquema de edición de Xcode](walkthrough-images/xcode-edit-scheme-release.png)

1. En este momento, el marco de trabajo está listo para crearse. Cree el marco de trabajo para el simulador y las arquitecturas de dispositivo y, a continuación, combine los resultados como un único paquete de marco. Identifique las versiones instaladas del SDK para compilar el código fuente mediante la herramienta **xcodebuild** :

    ```bash
    xcodebuild -showsdks
    ```

    El resultado será similar al siguiente:

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    Elija un SDK de iOS y una versión del SDK del simulador de iOS que desee, en este caso, la versión 13,2 y ejecute la compilación con el siguiente comando:

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > Si tiene un área de trabajo en lugar de un proyecto, compile el área de trabajo y especifique el destino como parámetro necesario. También desea especificar un directorio de salida porque, en el caso de las áreas de trabajo, este directorio será diferente del de las compilaciones del proyecto.

    > [!TIP]
    > También puede usar [el script auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14) para compilar el marco de trabajo para todas las arquitecturas aplicables o simplemente compilarlo desde el dispositivo y el simulador de conmutación de Xcode en el selector de destino.

1. Hay dos marcos de SWIFT, uno para cada plataforma, combinados como un único paquete que se va a incrustar en un proyecto de enlace de Xamarin. iOS más adelante. Con el fin de crear un marco FAT, que combina ambas arquitecturas, debe realizar los pasos siguientes. El paquete de Framework es simplemente una carpeta, por lo que puede realizar todos los tipos de operaciones, como agregar, quitar y reemplazar archivos:

    - Vaya a la carpeta de salida de compilación con las subcarpetas **Release-iPhoneOS** y **Release-iphonesimulator** y copie uno de los marcos de trabajo como una versión inicial de la salida final (marco FAT).

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - Combinación de módulos de otra compilación con los módulos de marco FAT

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - Combinación de la configuración de iPhoneOS y iphonesimulator como un marco FAT

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - Comprobar los resultados

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        La salida debe presentar lo siguiente, que refleja el nombre del marco de trabajo y las arquitecturas incluidas:

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > Si desea admitir solo una plataforma (por ejemplo, si va a compilar una aplicación, que solo se puede ejecutar en un dispositivo), puede omitir el paso para crear la biblioteca FAT y usar el marco de salida de la compilación del dispositivo anterior.

    > [!TIP]
    > También puede usar [el script de la aplicación auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24) para crear el marco FAT, que automatiza todos los pasos anteriores.

## <a name="prepare-metadata"></a>Preparar metadatos

En este momento, debe tener el marco con el encabezado de la interfaz generada por Objective-C listo para que lo consuma un enlace de Xamarin. iOS.  El siguiente paso consiste en preparar las interfaces de definición de API, que se usan en un proyecto de enlace para generar clases de C#. Estas definiciones pueden crearse de forma manual o automática mediante la herramienta [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) y el archivo de encabezado generado. Use Sharpie para generar los metadatos:

1. Descargue la herramienta [Sharpie de objetivo](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) más reciente desde el sitio web de descargas oficiales e instálelo mediante el asistente. Una vez completada la instalación, puede ejecutar el comando Sharpie para comprobarlo:

    ```bash
    sharpie -v
    ```

1. Genere metadatos mediante Sharpie y el archivo de encabezado de Objective-C generado automáticamente:

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    La salida refleja los archivos de metadatos que se generan: **ApiDefinitions.CS** y **StructsAndEnums.CS**. Guarde estos archivos para el paso siguiente para incluirlos en un proyecto de enlace de Xamarin. iOS junto con las referencias nativas:

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    La herramienta generará metadatos de C# para cada miembro de Objective-C expuesto, que tendrá un aspecto similar al código siguiente. Como puede ver, podría definirse manualmente porque tiene un formato legible y una asignación de miembros directos:

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > El nombre del archivo de encabezado podría ser diferente si ha cambiado la configuración predeterminada de Xcode para el nombre del encabezado. De forma predeterminada, tiene el nombre de un proyecto con el sufijo **-SWIFT** . Siempre puede comprobar el archivo y su nombre Si navega a la carpeta encabezados del paquete de Framework.

    > [!TIP]
    > Como parte del proceso de automatización, puede usar [el script auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35) para generar metadatos automáticamente una vez que se crea el marco FAT.

## <a name="build-a-binding-library"></a>Crear una biblioteca de enlaces

El siguiente paso consiste en crear un proyecto de enlace de Xamarin. iOS con la plantilla de enlace de Visual Studio, agregar los metadatos necesarios, las referencias nativas y luego compilar el proyecto para generar una biblioteca consumible:

1. Abra Visual Studio para Mac y cree un nuevo proyecto de biblioteca de enlace de Xamarin. iOS y asígnele un nombre, en este caso SwiftFrameworkProxy. Binding y complete el asistente. La plantilla de enlace de Xamarin. iOS se encuentra en la siguiente ruta de acceso: **iOS > library > biblioteca de enlace**:

    ![crear biblioteca de enlace de Visual Studio](walkthrough-images/visualstudio-create-binding-library.png)

1. Elimine los archivos de metadatos existentes **ApiDefinition.CS** y **Structs.CS** , ya que se reemplazarán por completo con los metadatos generados por la herramienta Objective Sharpie.
1. Copie los metadatos generados por Sharpie en uno de los pasos anteriores, seleccione la acción de compilación siguiente en la ventana Propiedades: **ObjBindingApiDefinition** para el archivo **ApiDefinitions.CS** y **ObjBindingCoreSource** para el archivo **StructsAndEnums.CS** :

    ![metadatos de la estructura del proyecto de Visual Studio](walkthrough-images/visualstudio-project-structure-metadata.png)

    Los propios metadatos describen cada clase y miembro de Objective-C expuesto con el lenguaje C#. Puede ver la definición del encabezado Objective-C original junto con la declaración de C#:

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    Aunque es un código C# válido, no se usa tal cual, sino que se usa en las herramientas de Xamarin. iOS para generar clases de C# basadas en esta definición de metadatos. Como resultado, en lugar de la interfaz SwiftFrameworkProxy obtiene una clase de C# con el mismo nombre, al que se puede crear una instancia del código de Xamarin. iOS. Esta clase obtiene los métodos, las propiedades y otros miembros definidos por los metadatos, a los que se llamará en un modo de C#.

1. Agregue una referencia nativa al marco FAT anterior generado, así como a cada una de las dependencias de ese marco. En este caso, agregue las referencias nativas de SwiftFrameworkProxy y Gigya Framework al proyecto de enlace:

    - Para agregar referencias de marco nativo, abra el buscador y navegue hasta la carpeta con los marcos de trabajo. Arrastre y coloque los marcos de trabajo en la ubicación de referencias nativas en el Explorador de soluciones. Como alternativa, puede usar la opción del menú contextual de la carpeta referencias nativas y hacer clic en **Agregar referencia nativa** para buscar los marcos y agregarlos:

    ![referencias nativas de la estructura de proyecto de Visual Studio](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - Actualice las propiedades de todas las referencias nativas y Active tres opciones importantes:

        - Establecer Smart Link = true
        - Establecer forzar carga = false
        - Establezca la lista de marcos de trabajo que se usan para crear los marcos originales. En este caso, cada marco de trabajo tiene solo dos dependencias: Foundation y UIKit. Establézcalo en el campo marcos:

        ![Opciones de proxy de nativeref de Visual Studio](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        Si tiene marcas de enlazador adicionales que especificar, establézcala en el campo marcadores del enlazador. En este caso, manténgalo vacío.

    - Especifique marcas de enlazador adicionales cuando sea necesario. Si la biblioteca que se va a enlazar solo expone las API de Objective-C, pero internamente usa SWIFT, es posible que vea problemas como:

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        En las propiedades del proyecto de enlace de la biblioteca nativa, se deben agregar los siguientes valores a las marcas del enlazador:

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        Las dos primeras opciones (las dos  `-L ...`   ) indican al compilador nativo dónde encontrar las bibliotecas de SWIFT. El compilador nativo omitirá las bibliotecas que no tienen la arquitectura correcta, lo que significa que es posible pasar la ubicación de las bibliotecas del simulador y las bibliotecas de dispositivos al mismo tiempo, de modo que funcione tanto para el simulador como para las compilaciones de dispositivos (estas rutas de acceso solo son correctas para iOS; para tvOS y watchos, deben actualizarse). Una desventaja es que este enfoque requiere que la Xcode correcta esté en/Application/Xcode.app, si el consumidor de la biblioteca de enlace tiene Xcode en una ubicación diferente, no funcionará. La solución alternativa consiste en agregar estas opciones en los argumentos Mtouch adicionales en las opciones de compilación de iOS del proyecto ejecutable ( `--gcc_flags -L... -L...` ). La tercera opción hace que el vinculador nativo almacene la ubicación de las bibliotecas SWIFT en el archivo ejecutable, de modo que el sistema operativo pueda encontrarlos.

1. La acción final es crear la biblioteca y asegurarse de que no tiene ningún error de compilación. A menudo, encontrará que los enlaces de metadatos generados por Objective Sharpie se anotarán con el  `[Verify]`   atributo. Estos atributos indican que debe comprobar que Objective Sharpie hizo lo correcto comparando el enlace con la declaración de Objective-C original (que se proporcionará en un Comentario sobre la declaración enlazada). Puede obtener más información acerca de los miembros marcados con el atributo mediante [el siguiente vínculo](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify). Una vez compilado el proyecto, se puede usar en una aplicación de Xamarin. iOS.

## <a name="consume-the-binding-library"></a>Usar la biblioteca de enlaces

El último paso es usar la biblioteca de enlace de Xamarin. iOS en una aplicación de Xamarin. iOS. Cree un nuevo proyecto de Xamarin. iOS, agregue referencia a la biblioteca de enlace y Active Gigya SWIFT SDK:

1. Cree el proyecto de Xamarin. iOS. Puede usar la **aplicación de > de iOS > aplicación de vista única** como punto de partida:

    ![nueva aplicación de Visual Studio](walkthrough-images/visualstudio-app-new.png)

1. Agregue una referencia de proyecto de enlace al proyecto de destino o al archivo. dll creado previamente. Trate la biblioteca de enlace como una biblioteca normal de Xamarin. iOS:

    ![referencias de aplicación de Visual Studio](walkthrough-images/visualstudio-app-refs.png)

1. Actualice el código fuente de la aplicación y agregue la lógica de inicialización al ViewController principal, que activa el SDK de Gigya.

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. Cree un botón con un nombre **btnLogin** y agregue el siguiente controlador de clic de botón para activar un flujo de autenticación:

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. Ejecute la aplicación y, en la salida de depuración, debería ver la línea siguiente: `Gigya initialized with domain: us1.gigya.com` . Haga clic en el botón para activar el flujo de autenticación:

    [![resultado del proxy SWIFT](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

¡Enhorabuena! Ha creado correctamente una aplicación Xamarin. iOS y una biblioteca de enlaces, que consume un marco de trabajo SWIFT. La aplicación anterior se ejecutará correctamente en iOS 12.2 + porque, a partir de esta versión de iOS, [Apple presentó la estabilidad de ABI](https://swift.org/blog/swift-5-1-released/) y cada iOS a partir de la versión 12.2 + incluye bibliotecas en tiempo de ejecución de SWIFT, que podrían usarse para ejecutar la aplicación compilada con SWIFT 5.1 +. Si necesita agregar compatibilidad con versiones anteriores de iOS, hay que realizar algunos pasos más:

1. Con el fin de agregar compatibilidad con iOS 12,1 y versiones anteriores, desea enviar dylibss SWIFT específicos que se usan para compilar el marco de trabajo. Use el paquete de NuGet [Xamarin. iOS. SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) para procesar y copiar las bibliotecas necesarias con el IPA. Agregue la referencia de NuGet al proyecto de destino y vuelva a compilar la aplicación. No se requieren pasos adicionales, el paquete NuGet instalará tareas específicas, que se ejecutan con el proceso de compilación, identifican los dylibss de SWIFT necesarios y los empaquetan con el IPA final.

1. Para enviar la aplicación a la tienda de aplicaciones que quiere usar Xcode y la opción de distribución, que actualizará el archivo IPA y la carpeta **SwiftSupport** dylibs para que lo acepte el AppStore:

    ○ Archive la aplicación. En el menú Visual Studio para Mac, seleccione **Build > Archive for Publishing**:

    ![archivo de Visual Studio para publicar](walkthrough-images/visualstudio-archiveforpublishing.png)

    Esta acción compila el proyecto y lo consigue en el organizador, al que puede tener acceso Xcode para su distribución.

    ○ Distribuir a través de Xcode. Abra Xcode y navegue hasta la opción de menú **organizador de > ventana** :

    ![archivos de Visual Studio](walkthrough-images/visualstudio-archives.png)

    Seleccione el archivo creado en el paso anterior y haga clic en el botón distribuir aplicación. Siga el Asistente para cargar la aplicación en AppStore.

1. Este paso es opcional, pero es importante comprobar que la aplicación se puede ejecutar en iOS 12,1 y versiones anteriores, así como en 12,2. Puede hacerlo con la ayuda de Test Cloud y el marco de trabajo de UITest. Cree un proyecto de UITest y una prueba de IU básica, que ejecuta la aplicación:

    - Cree un proyecto de UITest y configúrelo para su aplicación de Xamarin. iOS:

        ![Visual Studio UITest nuevo](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > Puede encontrar más información sobre cómo crear un proyecto de UITest y configurarlo para su aplicación mediante [el siguiente vínculo](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

    - Cree una prueba básica para ejecutar la aplicación y use algunas de las características del SDK de SWIFT. Esta prueba activa la aplicación, intenta iniciar sesión y, a continuación, presiona el botón Cancelar:

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > Obtenga más información sobre UITests Framework y la automatización de la interfaz de usuario mediante [el siguiente vínculo](https://docs.microsoft.com/appcenter/test-cloud/uitest/).

    - Cree una aplicación de iOS en App Center, cree una nueva serie de pruebas con un nuevo conjunto de dispositivos para ejecutar la prueba:

        ![Visual Studio App Center nuevo](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > Obtenga más información sobre AppCenter Test Cloud mediante [el siguiente vínculo](https://docs.microsoft.com/appcenter/test-cloud/).

    - Instalación de la CLI de AppCenter

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > Asegúrese de que tiene el nodo v 6.3 o posterior instalado

    - Ejecute la prueba con el siguiente comando. Asegúrese también de que la línea de comandos de AppCenter está conectada actualmente.

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - Compruebe el resultado. En el portal de AppCenter, vaya a la **aplicación > prueba > ejecuciones de pruebas**:

        ![resultado de Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        Seleccione la serie de pruebas deseada y compruebe el resultado:

        ![se ejecuta Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

Ha desarrollado una aplicación básica de Xamarin. iOS que usa un marco de trabajo de SWIFT nativo a través de una biblioteca de enlace de Xamarin. iOS. En el ejemplo se proporciona una manera sencilla de usar el marco seleccionado y, en la aplicación real, se le pedirá que exponga más API y prepare los metadatos de estas API. El script para generar metadatos simplificará los cambios futuros en las API de Framework.

## <a name="related-links"></a>Vínculos relacionados

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Comprobación de metadatos de Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Enlace de Objective-C Framework](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [SDK de iOS para Gigya (marco de SWIFT)](https://developers.gigya.com/display/GD/Swift+SDK)
- [Estabilidad de la ABI de SWIFT 5,1](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automatización de Xamarin UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configuración de Xamarin. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Repositorio del proyecto de ejemplo](https://github.com/alexeystrakh/xamarin-binding-swift-framework)
