---
title: Aplicaciones de desgaste de empaquetado
description: En este artículo se explica cómo empaquetar aplicaciones de desgaste de Android.
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 3263bfd9cea184a7bfbb29099abe2f1b3deb4126
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458061"
---
# <a name="packaging-wear-apps"></a>Aplicaciones de desgaste de empaquetado

> [!WARNING]
> Es posible que los siguientes documentos y proyectos de ejemplo ya no se mantengan.
> A partir de [Xamarin. Android 11,1][xa-11.1], ya no se admite el empaquetado automático de una aplicación de desgaste de Android dentro de una aplicación de bolsillo de Android. En su lugar, se recomienda distribuir las aplicaciones de desgaste de Android como [aplicaciones independientes][standalone] .

Las aplicaciones de desgaste de Android 1,0 se empaquetan con una aplicación Android completa para su distribución en Google Play.

Las aplicaciones de desgaste de Android 2,0 pueden enviarse a Google Play como [aplicaciones independientes][standalone].

[xa-11.1]: /xamarin/android/release-notes/11/11.1
[standalone]: https://developer.android.com/training/wearables/apps/standalone-apps

## <a name="automatic-packaging"></a>Empaquetado automático

A partir de Xamarin Android 5,0, la aplicación de desgaste se empaqueta automáticamente como un recurso en la aplicación de mano cuando se crea una referencia de proyecto desde el proyecto de mano al proyecto de desgaste. Puede usar los pasos siguientes para crear esta asociación: 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Si la aplicación de desgaste todavía no forma parte de la solución de mano, haga clic con el botón derecho en el nodo de la solución y seleccione **agregar > agregar proyecto existente..**..

2. Navegue hasta el archivo **. csproj** de la aplicación de desgaste, selecciónelo y haga clic en **abrir**. El proyecto de aplicación de desgaste debe estar ahora visible en la solución de mano.

3. Haga clic con el botón secundario en el nodo **referencias** y seleccione **Agregar referencia**.

4. En el cuadro de diálogo **Administrador de referencias** , habilite el proyecto de desgaste (haga clic para agregar una marca de verificación) y, a continuación, haga clic en **Aceptar**.

5. Cambie el nombre del paquete para el proyecto de desgaste para que coincida con el nombre de paquete del proyecto de mano (el nombre del paquete se puede cambiar en **propiedades > manifiesto de Android**).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Si la aplicación de desgaste todavía no forma parte de la solución de mano, haga clic con el botón derecho en el nodo de la solución y seleccione **agregar > agregar proyecto existente..**..

2. Navegue hasta el archivo **. csproj** de la aplicación de desgaste, selecciónelo y haga clic en **abrir**. El proyecto de aplicación de desgaste debe estar ahora visible en la solución de mano.

3. Haga clic con el botón secundario en el nodo del proyecto de mano de la solución y haga clic en **Editar referencias..**..

4. En el cuadro de diálogo **Editar referencias** , habilite el proyecto de desgaste (haga clic para agregar una marca de verificación) y, a continuación, haga clic en **Aceptar**.

5. Cambie el nombre del paquete para el proyecto de desgaste para que coincida con el nombre de paquete del proyecto de mano (el nombre del paquete se puede cambiar en **Opciones de proyecto > aplicación de Android**).

-----

Tenga en cuenta que recibirá un error **XA5211** si el nombre del paquete de la aplicación de desgaste no coincide con el nombre del paquete de la aplicación de mano. Por ejemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corregir este error, cambie el nombre del paquete de la aplicación de desgaste para que coincida con el nombre del paquete de la aplicación de mano.

Al hacer clic en **Compilar > compilar todo**, esta asociación desencadena el empaquetado automático del proyecto de desgaste en el proyecto principal de mano (teléfono). La aplicación de desgaste se crea automáticamente e incluye como un recurso en la aplicación de mano.

El ensamblado que genera el proyecto de aplicación de desgaste no se usa como referencia de ensamblado en el proyecto de mano (teléfono). En su lugar, el proceso de compilación hace lo siguiente:

- Comprueba que los nombres de paquete coinciden. 

- Genera XML y lo agrega al proyecto de mano para asociarlo a la aplicación de desgaste. Por ejemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- Agrega la aplicación de desgaste como un recurso **sin procesar** al proyecto de mano. 

## <a name="manual-packaging"></a>Empaquetado manual

Puede escribir aplicaciones de desgaste de Android en Xamarin. Android antes de la versión 5,0, pero debe seguir estas instrucciones de empaquetado manual para distribuir la aplicación: 

1. Asegúrese de que el proyecto de portátil y los proyectos de mano (teléfono) tienen el mismo número de versión y el mismo nombre de paquete.

2. Compile manualmente el proyecto portátil como una compilación de **versión** .

3. Agregue manualmente la versión **. APK** del paso (2) en el directorio **Resources/RAW** del proyecto de mano (teléfono).

4. Agregue manualmente un nuevo recurso de recursos XML **/XML/wearable_app_desc.xml** en el proyecto de mano que hace referencia a portátil **apk** del paso (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Agregue manualmente un `<meta-data />` elemento al elemento **AndroidManifest.xml** del proyecto de mano `<application>` que hace referencia al nuevo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Vea también las [instrucciones manuales](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)del sitio para desarrolladores de Android packging.