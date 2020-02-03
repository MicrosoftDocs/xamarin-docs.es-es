---
title: Implementación de aplicaciones de watchos en la tienda de aplicaciones
description: En este documento se describe cómo implementar aplicaciones de watchos compiladas con Xamarin en la tienda de aplicaciones. Se examinan los perfiles de aprovisionamiento de distribución y iTunes Connect, y también se proporcionan algunas sugerencias para la solución de problemas.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a622684461bfe2e4a57b910288ee1f9afb54c694
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725127"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Implementación de aplicaciones de watchos en la tienda de aplicaciones

> [!IMPORTANT]
> Asegúrese de revisar la [Guía de envío del kit de inspección de Apple](https://developer.apple.com/app-store/watch/)y consulte la sección de [solución de problemas](#troubleshooting) para ver los problemas que pueda tener.

- Asegúrese de que tiene:
  - [**Perfiles de aprovisionamiento de distribución**](#provisioning) creados para los proyectos.
  - El **destino de implementación** (`MinimumOSVersion`) de la aplicación primaria de iOS establecida en **8,2** o anterior (no se admite 8,3).

- En [**iTunes Connect**](#iTunes_Connect):

  - Cree la entrada de la aplicación iOS (o agregue una **nueva versión** a una aplicación existente).
  - Agregue iconos de inspección y capturas de pantallas.

- A continuación, en [Visual Studio para Mac](#xamarin_studio) (actualmente no se admite Visual Studio):

  - Haga clic con el botón derecho en la aplicación iOS y elija **establecer como proyecto de inicio**.
  - Cambie a la configuración de **App Store** .
  - Use la característica de **archivo** para crear un archivo de aplicación.

- Por último, cambie a [Xcode 6.2 +](#xcode)

  - Vaya a la **ventana de > organizador** y elija **archivos**.
  - Seleccione la aplicación y el archivo de la lista.
  - Opcionalmente **Validar...** el archivo.
  - **Enviar..** . el archivo y siga los pasos para cargar en iTunes Connect para su revisión y aprobación.

Lea sugerencias específicas relacionadas con estos elementos. Consulte la sección de [solución de problemas](#troubleshooting) si tiene problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Perfiles de aprovisionamiento de distribución

Para compilar para la implementación de App Store, debe crear un **Perfil de aprovisionamiento de distribución** para cada identificador de aplicación de la solución.

Si tiene un identificador de aplicación comodín, *solo se necesitará un perfil de aprovisionamiento*; pero si tiene un identificador de aplicación independiente para cada proyecto, necesitará un perfil de aprovisionamiento para cada identificador de aplicación:

![](appstore-images/provisioningprofile-distribution-sml.png "The App Store Distribution profile")

Una vez que haya creado los tres perfiles, aparecerán en la lista. Recuerde descargar e instalar cada uno de ellos (haciendo doble clic en él):

![](appstore-images/provisioningprofiles-sml.png "The list of available profiles")

Para comprobar el perfil de aprovisionamiento en las opciones del **proyecto** , seleccione la pantalla **compilar > la firma del lote de iOS** y seleccione la configuración de **AppStore | iPhone** .

La lista de perfiles de **aprovisionamiento** mostrará todos los perfiles coincidentes. debería ver los perfiles coincidentes que ha creado en esta lista desplegable.

![](appstore-images/options-selectprofile-sml.png "The iOS Bundle Signing dialog")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Siga la [información general](~/ios/deploy-test/app-distribution/index.md)de la distribución de aplicaciones, en particular:

- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Al configurar la aplicación en iTunes Connect, no olvide agregar el icono de inspección y las capturas de pantallas:

![](appstore-images/itunesconnect-watch-sml.png "The Watch icon and screenshots in iTunes Connect")

El archivo de icono debe ser 1024x1024 píxeles y tendrá una máscara circular aplicada cuando se muestre. El icono no debe tener un canal alfa.

Se requiere al menos una captura de pantalla, se pueden enviar hasta cinco.
Deben ser 312x390 píxeles y mostrar la aplicación de inspección en acción.
Puede usar el simulador de inspección de 42mm para realizar capturas de pantallas en este tamaño.

<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Asegúrese de que la aplicación iOS es el proyecto de inicio. Si no es así, haga clic con el botón derecho para establecerlo:

   ![](appstore-images/xs-startup.png "Setting the startup project")

2. Elija la configuración de compilación de **AppStore** :

   ![](appstore-images/xs-appstore.png "The AppStore build configuration")

3. Elija el elemento de menú **Compilar > archivo** para iniciar el proceso de archivo:

   ![](appstore-images/xs-archive.png "The Build menu")

También puede elegir el elemento de menú **ver > archivos...** para ver los archivos que se han creado anteriormente.

  ![](appstore-images/xs-archives-sml.png "The Archives view")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrará automáticamente los archivos creados en Visual Studio para Mac.

1. Inicie Xcode y elija **Window > Organizer**:

   ![](appstore-images/xc-organizer.png "The Window menu")

2. Cambie a la pestaña **archivos** y seleccione el archivo que se creó con Visual Studio para Mac:

   ![](appstore-images/xc-archives.png "The Archives tab")

3. Opcionalmente, **valide...** el archivo y, a continuación, elija **enviar...** para cargar la aplicación en iTunes Connect.

4. Elija el equipo de desarrollo (si pertenece a más de uno) y, a continuación, confirme el envío:

   ![](appstore-images/xc-submit1.png "The development team section")

5. Visite iTunes Connect de nuevo para ver el binario cargado. Vaya a la página de configuración de la aplicación y elija **versión preliminar** en el menú superior para ver la lista de **compilaciones** :

   [![](appstore-images/itc-prerelease-sml.png "The apps configuration page in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

Después, puede enviar la aplicación para su aprobación en la página **versiones** . Consulte la introducción a la [distribución de aplicaciones de iOS](~/ios/deploy-test/app-distribution/index.md) para obtener más información.

## <a name="troubleshooting"></a>Solución de problemas

Estos son algunos errores que pueden surgir durante el envío a la tienda de aplicaciones y los pasos que puede seguir para corregirlos.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>La opción de menú Archivo no está visible en Visual Studio para Mac

Siga los [pasos anteriores](#xamarin_studio) para configurar la solución para archivar. Si no puede establecer el proyecto de inicio correctamente, asegúrese de que la configuración de compilación se establece primero en depuración o lanzamiento antes de intentar cambiar el proyecto de inicio. A continuación, vuelva a establecer la configuración de compilación en **AppStore**.

### <a name="invalid-icon"></a>Icono no válido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga las [instrucciones para quitar el canal alfa](~/ios/watchos/troubleshooting.md) de los iconos.

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion no coincide

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos los proyectos de la solución: la aplicación iOS, la extensión inspección y la aplicación Watch: deben usar el mismo número de versión. Edite cada archivo **info. plist** para que el número de versión coincida exactamente.

### <a name="missing-icons"></a>Iconos que faltan

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga las instrucciones de los [iconos trabajar con](~/ios/watchos/app-fundamentals/icons.md) para agregar todas las imágenes necesarias al proyecto de la aplicación de inspección.

### <a name="missing-icon"></a>Icono que falta

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Asegúrese de que tiene la versión más reciente de Visual Studio para Mac y de que **Appicor. appiconset** contiene un conjunto completo de imágenes. Si sigue viendo este error, vea el origen del archivo **Contents. JSON** para confirmar que contiene una entrada para todas las imágenes necesarias. Como alternativa, una vez que se haya asegurado de que está usando la versión más reciente de Xamarin, elimine y vuelva a crear el **Appicor. appiconset**.

> [!IMPORTANT]
> Hay un error conocido en la compatibilidad con el icono de inspección de Visual Studio para Mac: espera una imagen de 88x88 píxeles para la imagen de **29x29@3x** (que debe ser 87x87 píxeles).

No se puede corregir en Visual Studio para Mac: edite el recurso de imagen en Xcode o edite manualmente el archivo **Contents. JSON** .

### <a name="invalid-watchkit-support"></a>Compatibilidad con WatchKit no válida

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Este mensaje puede aparecer durante la validación y el envío, o en un correo electrónico automatizado de iTunes Connect después de una carga aparentemente correcta.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Debe **archivar** la aplicación en Visual Studio para Mac y, a continuación, cambiar a Xcode 6.2 + para validar y cargar en iTunes Connect.

Use el canal de Xamarin estable y Xcode 6.2 +.

### <a name="invalid-provisioning-profile"></a>Perfil de aprovisionamiento no válido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

Los **perfiles de aprovisionamiento de distribución** se deben proporcionar para los tres proyectos en una solución de aplicación de inspección: la aplicación de iOS, la extensión de inspección y la aplicación de inspección, ya sea explícitamente (tres perfiles) o a través de un solo perfil comodín. Compruebe que los perfiles de aprovisionamiento existen en el centro de desarrollo de iOS y que los ha descargado y los ha agregado al equipo Mac.

### <a name="invalid-code-signing-entitlements"></a>Derechos de firma de código no válidos

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Asegúrese de que los perfiles de aprovisionamiento están configurados correctamente en el centro de desarrollo de Apple y que los ha descargado e instalado. Compruebe también que se establecen en la ventana Propiedades de Visual Studio para Mac para cada proyecto.

### <a name="invalid-architecture"></a>Arquitectura no válida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Solo puede agregar aplicaciones de inspección [Unified API (64 bits) de](~/cross-platform/macios/unified/index.md) Xamarin. iOS.
Haga clic con el botón derecho en el proyecto de aplicación de iOS y vaya a **opciones > Compilar > la compilación de ios > pestaña avanzadas** y asegúrese de que las **arquitecturas admitidas** para la configuración de AppStore-iPhone incluyen **ARM64** (por ejemplo, **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Este paquete no es válido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

La aplicación de iOS primaria debe tener el valor de MinimumOSVersion establecido en ' 8,2 ' o una versión anterior.

### <a name="non-public-api-usage"></a>Uso de API no público

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Asegúrese de que está usando la versión más reciente de las herramientas de Xcode y Xamarin.
El código no debe tener acceso a ninguna API no pública.

### <a name="build-error-mt5309"></a>Error de compilación MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Este error probablemente se debe a que ha cambiado el nombre de la instalación de Xcode de **Xcode. app**. Por ejemplo, este error se producirá si cambia el nombre de la instalación a **Xcode 6.2. app**.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de envío de Apple WatchKit](https://developer.apple.com/app-store/watch/)
