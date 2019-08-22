---
title: Compatibilidad con IPA en Xamarin.iOS
description: En este artículo se explica cómo crear un archivo IPA que pueda utilizarse para implementar una aplicación mediante la distribución ad hoc para realizar pruebas o para la distribución de aplicaciones internas en la empresa.
ms.prod: xamarin
ms.assetid: D253C2DB-852E-6FC6-C9FD-574730B8DB19
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: c430d7771d443b80bec2a3e8a79565e2a7994a8b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887586"
---
# <a name="ipa-support-in-xamarinios"></a>Compatibilidad con IPA en Xamarin.iOS

_En este artículo se explica cómo crear un archivo IPA que pueda utilizarse para implementar una aplicación mediante la distribución ad hoc para realizar pruebas o para la distribución de aplicaciones internas en la empresa._

Además de para publicar una aplicación para su venta a través del App Store de iTunes, se puede implementar para los usos siguientes:

- **Pruebas ad hoc**: una aplicación de iOS se puede implementar para un máximo de 100 usuarios, identificados por el UUID del dispositivo iOS específico, para pruebas alfa y beta. Consulte nuestra documentación [Provisioning an iOS Device for Development (Aprovisionamiento de un dispositivo iOS para el desarrollo)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning) para obtener información detallada sobre cómo agregar dispositivos iOS de prueba a su cuenta de desarrollador de Apple y la guía [Ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md) para obtener más información sobre cómo realizar la distribución de este modo.
- **Implementación interna o en la empresa**: una aplicación de iOS puede implementarse internamente (en una empresa). Para ello, se requiere ser miembro del programa Developer Enterprise de Apple. Obtenga más información sobre la distribución interna en la guía [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md).

En cualquier caso, un paquete IPA (un tipo especial de archivo .zip) se puede crear y firmar digitalmente con el perfil de aprovisionamiento de distribución correcto. En este artículo se describen los pasos necesarios para crear el paquete IPA e instalarlo en un dispositivo iOS mediante iTunes en Mac o un PC de Windows.

<a name="iTunesMetadata" />

## <a name="the-itunesmetadataplist-file"></a>Archivo iTunesMetadata.plist

Cuando se crea una aplicación de iOS en iTune Connect, ya sea para su venta o su distribución gratuita a través del App Store de iTunes , el desarrollador puede especificar cierta información, como el género, el subgénero, el aviso sobre derechos de autor, los dispositivos iOS compatibles y los requisitos del dispositivo.

Las aplicaciones de iOS que se ofrecen a través de la distribución ad hoc o interna deben admitir esta información de alguna manera, de tal forma que pueda ser visible en iTunes y en el dispositivo del usuario. De forma predeterminada, se crea un archivo pequeño de iTunesMetadata.plist cada vez que se compila el proyecto y, a su vez, se almacena en el directorio del proyecto.

También se puede crear un archivo **iTunesMetadata.plist** personalizado para proporcionar información adicional de una distribución. Para obtener más información sobre el contenido de este archivo y sobre cómo crearlo, consulte nuestra documentación sobre [The iTunesMetadata.plist Contents (Contenidos de iTunesMetadata.plist)](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_contents) y [Creating an iTunesMetadata.plist File (Creación de un archivo iTunesMetadata.plist)](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_creating).

<a name="iTunesArtwork" />

## <a name="itunes-artwork"></a>Ilustraciones de iTunes

Si la aplicación se ofrece a través de otros medios que no sean la App Store, es necesario incluir una imagen de 512 x 512 y otra de 1024 x 1024 para representar la aplicación en iTunes.

Para especificar las ilustraciones de iTunes, haga lo siguiente:

1. Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición.
2. Desplácese hasta la sección **Ilustraciones de iTunes** del editor.
3. En caso de que falte alguna imagen, haga clic en la miniatura que encontrará en el editor, seleccione el archivo de imagen deseado de las ilustraciones de iTunes en el cuadro de diálogo **Open File (Abrir archivo)** y haga clic en el botón **OK (Aceptar)** o bien **Open (Abrir)** .
4. Repita este paso hasta que se hayan especificado todas las imágenes necesarias para su aplicación.

Para obtener más información, consulte la documentación de [iTunes Artwork (Ilustraciones de iTunes)](~/ios/app-fundamentals/images-icons/app-icons.md).

<a name="createipa" />

## <a name="creating-an-ipa"></a>Creación de un IPA

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

La creación de un IPA ahora está integrada en el nuevo flujo de trabajo de publicación. Para ello, siga las instrucciones siguientes para archivar la aplicación, firmarla y guardar su IPA.

Antes de empezar a crear un IPA para una solución multiplataforma, asegúrese de haber seleccionado el proyecto de iOS como proyecto de inicio:

![](ipa-support-images/setasstartup.png "Proyecto de iOS seleccionado como proyecto de inicio")

### <a name="build-your-archive"></a>Compilar el archivo

Para crear un IPA, es necesario crear un _archivo_ de una compilación de versión de nuestra aplicación. Este archivo contiene nuestra aplicación y la información que la identifica.


1. Seleccione la configuración **Versión | Dispositivo** en Visual Studio para Mac:

    ![](ipa-support-images/buildxs01new.png "Selección de la configuración Versión | Dispositivo")

1. En el menú **Compilación**, seleccione **Archivo para publicar**:

    ![](ipa-support-images/buildxs02new.png "Selección de Archivo para publicar")

1. Una vez creado el archivo, se mostrará la vista **Archives (Archivos)** :

    ![](ipa-support-images/buildxs03new.png "Muestra de la vista Archivos")


### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

Cada vez que se compila la aplicación para el archivo, se abre automáticamente la **Archives View (Vista de archivos)** , que muestra todos los proyectos archivados, agrupados por solución. De forma predeterminada, esta vista solo muestra la solución abierta actual. Para ver todas las soluciones que tienen archivos, haga clic en la opción **Show all archives (Mostrar todos los archivos)** .

Se recomienda mantener los archivos que se implementen en los clientes, ya sean implementaciones ad hoc o internas para que cualquier información de depuración generada pueda considerarse como perteneciente a una fecha posterior.

Tenga en cuenta que, en el caso de las compilaciones que no pertenecen a la App Store, el archivo **iTunesMetadata.plist**, así como las ilustraciones definidas para iTunes, se incluirán automáticamente en su IPA, si están presentes en el archivo.

Para firmar la aplicación y prepararla para la distribución:


1. Seleccione el botón **Sign and Distribute... (Firmar y distribuir...)** que se muestra a continuación:

    ![](ipa-support-images/buildxs04new.png "Selección de Firmar y distribuir...")

1. De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **Ad-Hoc (Ad hoc)** o **Enterprise (Empresa)** interno para crear un paquete:

    ![](ipa-support-images/distribute01.png "Selección de la distribución interna Ad hoc o Empresa")

1. En la pantalla del perfil de aprovisionamiento, seleccione su identidad de firma y el perfil de aprovisionamiento correspondiente o vuelva a iniciar sesión con otra identidad:

    ![](ipa-support-images/distribute02.png "Selección de la identidad de firma y el correspondiente perfil de aprovisionamiento")

1. Compruebe los detalles del paquete y haga clic en **Publish (Publicar)** :

    ![](ipa-support-images/distribute03.png "Comprobación de los detalles del paquete")

1. Por último, guarde su IPA en el equipo:

    ![](ipa-support-images/distribute04.png "Guardado del IPA en el equipo")


### <a name="building-via-the-command-line-on-mac"></a>Compilación a través de la línea de comandos (Mac)

En algunos casos, como en un entorno de integración continua (CI), será necesario compilar su IPA a través de la línea de comandos. Para ello, siga los pasos indicados a continuación:


1. Asegúrese de que las opciones **Project Options (Opciones de proyecto) > iOS IPA Options (Opciones de IPA para iOS) > Include iTunesArtwork images (Incluir imágenes de iTunesArtwork)** y **Build ad-hoc/enterprise package (IPA) (Compilar paquete ad hoc o empresa (IPA))** estén activadas:

    ![](ipa-support-images/imagexs04.png "Opciones Incluir imágenes de iTunesArtwork y Compilar paquete ad-hoc/enterprise (IPA) activadas")

    Si lo prefiere, puede editar el archivo **.csproj** en un editor de texto y agregar manualmente las dos propiedades correspondientes a `PropertyGroup` para la configuración que se usará para compilar la aplicación:

    ```xml
    <BuildIpa>true</BuildIpa>
    <IpaIncludeArtwork>false</IpaIncludeArtwork>
    ```

1. Si incluye un archivo opcional **iTunesMetadata.plist**, haga clic en el botón **...** , selecciónelo en la lista y haga clic en el botón **OK (Aceptar)** :

     ![](ipa-support-images/imagexs03.png "Selección de iTunesMetadata.plist en la lista")

1. Llame a **msbuild** directamente y pase esta propiedad a la línea de comandos:

    ```bash
    /Library/Frameworks/Mono.framework/Commands/msbuild YourSolution.sln /p:Configuration=Ad-Hoc /p:Platform=iPhone /p:BuildIpa=true
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una vez que se haya creado y seleccionado el perfil de aprovisionamiento, se haya creado el archivo opcional **iTunesMetadata.plist** y se hayan definido las ilustraciones de iTunes en Visual Studio, puede compilar un archivo IPA para la distribución. Después, debe configurar el proyecto. Haga lo siguiente:

1. Haga clic con el botón derecho en el nombre del proyecto de Xamarin.iOS en el **Explorador de soluciones** y seleccione **Properties (Propiedades)** para abrirlo y editarlo:

    ![](ipa-support-images/imagevs01.png "Selección de Propiedades")

2. Seleccione **iOS IPA Options (Opciones de IPA para iOS)** y seleccione **Ad-Hoc (Ad hoc)** en la lista desplegable **Configuration (Configuración)** :

    ![](ipa-support-images/imagevs02.png "Selección de Ad hoc en la lista desplegable Configuración")

    > [!NOTE]
    > Podría no haber una configuración ad hoc disponible para los proyectos de Xamarin.iOS más recientes. Si no está disponible, seleccione la configuración de **Lanzamiento**.

3. Si incluye un archivo opcional **iTunesMetadata.plist**, haga clic en el botón **...** , selecciónelo en la lista y haga clic en el botón **Open (Abrir)** :

    ![](ipa-support-images/imagevs03.png "Selección de iTunesMetadata.plist en la lista")

4. También puede especificar un **nombre de paquete** para el IPA. En caso contrario, tendrá el mismo nombre que el proyecto de Xamarin.iOS.
5. Guarde los cambios en Project Properties (Propiedades del proyecto).
6. Seleccione **Ad hoc** en la lista desplegable **Configuración de compilación** si está disponible. En caso contrario, seleccione **Lanzamiento**:

    ![](ipa-support-images/imagevs05.png "Selección de Ad hoc en la lista desplegable Configuración de compilación")

7. Compile el proyecto para crear el paquete IPA.
8. El paquete IPA se creará en **Bin > Dispositivo iOS > carpeta Ad Hoc (o Lanzamiento)** :

    ![](ipa-support-images/imagevs06.png "IPA en el Explorador de archivos")

-----

<a name="Customizing-the-IPA-Location" />

## <a name="customizing-the-ipa-location"></a>Personalización de la ubicación del IPA

Se ha agregado una nueva propiedad `IpaPackageDir` de **MSBuild** para facilitar la personalización de la ubicación de archivo de salida **.ipa**. Si `IpaPackageDir` está establecido en una ubicación personalizada, el archivo **.ipa** se colocará en esa ubicación en lugar de en el subdirectorio predeterminado con marca de tiempo. Esto puede ser útil durante la creación automatizada de compilaciones basadas en una ruta de acceso del directorio específica para funcionar correctamente, como las utilizadas para las compilaciones de integración continua (CI).

Hay varias formas de utilizar la nueva propiedad:

Por ejemplo, para la salida del archivo **.ipa** al directorio predeterminado anterior (como en Xamarin.iOS 9.6 y versiones anteriores), puede establecer la propiedad `IpaPackageDir` en `$(OutputPath)` mediante uno de los siguientes enfoques. Ambos enfoques son compatibles con todas las compilaciones de Xamarin.iOS de Unified API, incluidas las compilaciones de lIDE, así como las de la línea de comandos en las que se usa **xbuild**, **msbuild** o **mdtool**:

- La primera opción se basa en establecer la propiedad `IpaPackageDir` de un elemento `<PropertyGroup>` en un archivo **MSBuild**. Por ejemplo, puede agregar el siguiente `<PropertyGroup>` a la parte inferior del archivo del proyecto de la aplicación de iOS **.csproj**, justo antes de la etiqueta de cierre `</Project>`:

    ```xml
    <PropertyGroup>
        <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Un mejor enfoque consiste en agregar un elemento `<IpaPackageDir>` a la parte inferior del `<PropertyGroup>` existente que corresponde a la configuración utilizada para compilar el archivo **.ipa**. Esta opción es mejor porque permite preparar el proyecto para la futura compatibilidad con una configuración prevista en la página de propiedades del proyecto iOS IPA Options (Opciones de IPA para iOS). Si actualmente utiliza la configuración de `Release|iPhone` para compilar el archivo **.ipa**, el grupo completo de propiedades actualizadas puede ser similar al siguiente:

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' ">
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
    </PropertyGroup>
    ```

Otro procedimiento para las compilaciones de la línea de comandos **msbuild** o **xbuild** es agregar un argumento `/p:` para establecer la propiedad `IpaPackageDir`. Observe que, en este caso, **msbuild** no expande expresiones `$()` que pasan a la línea de comandos, por lo que no es posible utilizar la sintaxis `$(OutputPath)`. En su lugar, debe proporcionar un nombre completo de la ruta de acceso. El comando **xbuild** de Mono expande las expresiones `$()`, pero sigue siendo preferible usar un nombre completo de ruta de acceso porque **xbuild** ha quedado obsoleto en favor de la [versión multiplataforma de **msbuild**](https://www.mono-project.com/docs/about-mono/releases/5.0.0/#msbuild).

Un ejemplo completo que utiliza este enfoque podría ser similar al siguiente en Windows:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

O al siguiente en Mac:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

<a name="installipa" />

## <a name="installing-an-ipa-using-itunes"></a>Instalación de un IPA mediante iTunes

El paquete IPA resultante se puede entregar a los usuarios de prueba para la instalación en sus dispositivos iOS o enviarse para la implementación empresarial. Independientemente del método que se seleccione, el usuario final instalará el paquete en la aplicación de iTunes en su Mac o PC de Windows haciendo doble clic en el archivo IPA, o bien arrastrándolo a la ventana de iTunes abierta.

La nueva aplicación de iOS se muestra en la sección **Mis aplicaciones**, en la que puede hacer clic y obtener información sobre la aplicación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 ![](ipa-support-images/installxs01.png "Nueva aplicación de iOS en la sección Mis aplicaciones")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 ![](ipa-support-images/installvs01.png "Nueva aplicación de iOS en la sección Mis aplicaciones")

-----

Ahora el usuario puede sincronizar iTunes con su dispositivo para instalar la nueva aplicación de iOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata la configuración necesaria para preparar una aplicación de Xamarin.iOS para una compilación que no pertenezca a la App Store. También se explica cómo crear un paquete IPA y cómo instalar la aplicación de iOS resultante en el dispositivos iOS del usuario final para las pruebas o la distribución interna.


## <a name="related-links"></a>Vínculos relacionados

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Archivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Ilustraciones de iTunes](~/ios/app-fundamentals/images-icons/app-icons.md#itunes)
- [Desarrollo y distribución de aplicaciones empresariales (Apple)](https://help.apple.com/xcode/mac/current/#/devba5e7054d)
- [Distribución de aplicaciones empresariales (vídeo WWDC)](https://developer.apple.com/videos/play/wwdc2014/705/)
