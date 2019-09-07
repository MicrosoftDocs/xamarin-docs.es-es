---
title: Vinculación de aplicaciones en Android
description: En esta guía se explica cómo Android 6,0 admite la vinculación de aplicaciones, una técnica que permite a las aplicaciones móviles responder a las direcciones URL de los sitios Web. Se explica qué es la vinculación de aplicaciones, cómo implementar la vinculación de aplicaciones en una aplicación Android 6,0 y cómo configurar un sitio web para conceder permisos a la aplicación móvil para un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d65e8fabff88489571bba9d03379ff605a6ed0fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757734"
---
# <a name="app-linking-in-android"></a>Vinculación de aplicaciones en Android

_En esta guía se explica cómo Android 6,0 admite la vinculación de aplicaciones, una técnica que permite a las aplicaciones móviles responder a las direcciones URL de los sitios Web. Se explica qué es la vinculación de aplicaciones, cómo implementar la vinculación de aplicaciones en una aplicación Android 6,0 y cómo configurar un sitio web para conceder permisos a la aplicación móvil para un dominio._

## <a name="app-linking-overview"></a>Información general de vinculación de aplicaciones

Las aplicaciones móviles ya no viven en un &ndash; silo en muchos casos son componentes importantes de sus negocios, junto con su sitio Web. Es deseable que las empresas conecten sin problemas su presencia web y sus aplicaciones móviles, con vínculos en un sitio web que inician aplicaciones móviles y muestran contenido relevante en la aplicación móvil. *Aplicación: vinculación* (también conocido como *vinculación profunda*) es una técnica que permite a un dispositivo móvil responder a un URI e iniciar una aplicación móvil que se corresponda con ese URI.

Android controla la vinculación de aplicaciones a través del *sistema* &ndash; de intención cuando el usuario hace clic en un vínculo en un explorador móvil, el explorador móvil enviará un intento de que Android delegue a una aplicación registrada. Por ejemplo, al hacer clic en un vínculo de un sitio web de cocina, se abriría una aplicación móvil que está asociada a ese sitio web y se muestra una receta específica al usuario. Si hay más de una aplicación registrada para controlar esa intención, Android generará lo que se conoce como un *cuadro de diálogo de desambiguación* que le pedirá a un usuario la aplicación de la aplicación que debe administrar la intención, por ejemplo:

![Captura de pantalla de ejemplo de un cuadro de diálogo de desambiguación](app-linking-images/01-disambiguation-dialog.png)

Android 6,0 mejora esto mediante el control automático de vínculos. Es posible que Android registre automáticamente una aplicación como el controlador predeterminado para un URI &ndash; que la aplicación iniciará automáticamente y navegará directamente a la actividad correspondiente. El modo en que Android 6,0 decide controlar un URI haga clic en depende de los siguientes criterios:

1. **Una aplicación existente ya está asociada con el URI** &ndash; Es posible que el usuario ya haya asociado una aplicación existente con un URI. En ese caso, Android seguirá usando esa aplicación.
2. **No hay ninguna aplicación existente asociada al URI, pero se instala una aplicación auxiliar** . &ndash; En este escenario, el usuario no ha especificado una aplicación existente, por lo que Android usará la aplicación de soporte instalada para controlar la solicitud.
3. **No hay ninguna aplicación existente asociada al URI, pero hay muchas aplicaciones auxiliares instaladas** . &ndash; Dado que hay varias aplicaciones que admiten el URI, se mostrará el cuadro de diálogo de anulación de ambigüedades y el usuario debe seleccionar qué aplicación controlará el URI.

Si el usuario no tiene ninguna aplicación instalada que admita el URI y otra se instala posteriormente, Android establecerá esa aplicación como el controlador predeterminado para el URI después de comprobar la asociación con el sitio web que está asociado con el URI.

En esta guía se explica cómo configurar una aplicación Android 6,0 y cómo crear y publicar el archivo de vínculos de recursos digitales para admitir la vinculación de aplicaciones en Android 6,0.

## <a name="requirements"></a>Requisitos

En esta guía se requiere Xamarin. Android 6,1 y una aplicación destinada a Android 6,0 (nivel de API 23) o superior.

La vinculación de aplicaciones es posible en versiones anteriores de Android mediante el [paquete de NuGet remaches](https://www.nuget.org/packages/Rivets/) del almacén de componentes de Xamarin. El paquete de remaches no es compatible con la vinculación de aplicaciones en Android 6,0; no admite la vinculación de aplicaciones Android 6,0.

## <a name="configuring-app-linking-in-android-60"></a>Configuración de la vinculación de aplicaciones en Android 6,0

La configuración de vínculos de aplicación en Android 6,0 implica dos pasos principales:

1. **Agregar uno o varios filtros de intención para el URI del sitio web** &ndash; la guía de filtros de intención de Android en cómo administrar una dirección URL haga clic en un explorador móvil.
2. **Publicación de un archivo *JSON de vínculos de activos digitales* en el sitio web** &ndash; este es un archivo que se carga en un sitio web y que se usa en Android para comprobar la relación entre la aplicación móvil y el dominio del sitio Web. Sin esto, Android no puede instalar la aplicación como el identificador predeterminado de los URI. el usuario debe hacerlo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configuración del filtro de intención

Es necesario configurar un filtro de intención que asigne un URI (o un conjunto de URI posible) de un sitio web a una actividad en una aplicación Android. En Xamarin. Android, esta relación se establece mediante la etiquetación de una actividad con el [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). El filtro de intención debe declarar la siguiente información:

- **`Intent.ActionView`** &ndash; Se registrará el filtro de intención para responder a las solicitudes de visualización de la información.
- **`Categories`** El filtro de intención debe registrar ambos **[propósitos. CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** y **[intención. CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** para poder administrar correctamente el URI Web. &ndash;
- **`DataScheme`** El filtro de intención debe `http` declarar y/ `https`o. &ndash; Estos son los únicos dos esquemas válidos.
- **`DataHost`** &ndash; Este es el dominio del que se originarán los URI.
- **`DataPathPrefix`** &ndash; Se trata de una ruta de acceso opcional a los recursos del sitio Web.
- **`AutoVerify`** &ndash; El`autoVerify` atributo indica a Android que Compruebe la relación entre la aplicación y el sitio Web. Esto se tratará más adelante.

En el ejemplo siguiente se muestra cómo usar [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) para controlar vínculos desde `https://www.recipe-app.com/recipes` y hacia `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android comprobará todos los hosts identificados por los filtros de intención en el archivo de recursos digitales en el sitio Web antes de registrar la aplicación como el controlador predeterminado para un URI. Todos los filtros de intención deben pasar la comprobación antes de que Android pueda establecer la aplicación como el controlador predeterminado.

### <a name="creating-the-digital-assets-link-file"></a>Crear el archivo de vínculo de recursos digitales

La vinculación de aplicaciones Android 6,0 requiere que Android Compruebe la asociación entre la aplicación y el sitio Web antes de establecer la aplicación como el controlador predeterminado para el URI. Esta comprobación se realizará cuando se instale la aplicación por primera vez. El archivo de *vínculos de recursos digitales* es un archivo JSON que se hospeda en los webdomains relevantes.

> [!NOTE]
> El `android:autoVerify` filtro&ndash; de intención debe establecer el atributo; en caso contrario, Android no realizará la comprobación.

El archivo lo coloca el webmaster del dominio en la ubicación **https://domain/.well-known/assetlinks.json** .

El archivo de recursos digitales contiene los metadatos necesarios para que Android Compruebe la asociación. Un archivo **assetlinks. JSON** tiene los siguientes pares clave-valor:

- `namespace`&ndash; espacio de nombres de la aplicación Android.
- `package_name`&ndash; el nombre del paquete de la aplicación de Android (declarado en el manifiesto de aplicación).
- `sha256_cert_fingerprints`&ndash; las huellas digitales SHA256 de la aplicación firmada. Consulte la guía de [búsqueda de la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md) para obtener más información sobre cómo obtener la huella digital SHA1 de una aplicación.

El siguiente fragmento de código es un ejemplo de **assetlinks. JSON** con una sola aplicación enumerada:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

Es posible registrar más de una huella digital SHA256 para admitir distintas versiones o compilaciones de la aplicación. Este archivo **assetlinks. JSON** siguiente es un ejemplo de registro de varias aplicaciones:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

El [sitio web de Google Digital Asset links](https://developers.google.com/digital-asset-links/tools/generator) tiene una herramienta en línea que puede ayudarle a crear y probar el archivo de recursos digitales.

### <a name="testing-app-links"></a>Probar la aplicación: vínculos

Después de implementar los vínculos de la aplicación, se deben probar las distintas partes para asegurarse de que funcionan según lo previsto.

Es posible confirmar que el archivo de recursos digitales está formateado y hospedado correctamente mediante la API de vínculos de activos digitales de Google, tal como se muestra en este ejemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Se pueden realizar dos pruebas para asegurarse de que los filtros de intención se han configurado correctamente y de que la aplicación se establece como el controlador predeterminado para un URI:

1. El archivo de recursos digitales se hospeda correctamente como se describió anteriormente. La primera prueba enviará un intento que Android debe redirigir a la aplicación móvil. La aplicación de Android debe iniciar y mostrar la actividad registrada para la dirección URL. En un símbolo del sistema, escriba:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Muestra las directivas de control de vínculos existentes para las aplicaciones instaladas en un dispositivo determinado. El siguiente comando volcará una lista de directivas de vínculo para cada usuario en el dispositivo con la siguiente información. En el símbolo del sistema, escriba el siguiente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash; Nombre del paquete de la aplicación.
    - **`Domain`** &ndash; Los dominios (separados por espacios) cuyos vínculos Web controlará la aplicación
    - **`Status`** &ndash; Este es el estado actual de control de vínculos de la aplicación. Un valor de **siempre** significa que la aplicación ha `android:autoVerify=true` declarado y ha pasado la comprobación del sistema. Va seguido de un número hexadecimal que representa el registro del sistema Android de la preferencia.

    Por ejemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumen

En esta guía se describe cómo funciona la vinculación de aplicaciones en Android 6,0. A continuación, se explica cómo configurar una aplicación Android 6,0 para admitir y responder a los vínculos de la aplicación. También se describe cómo probar la vinculación de aplicaciones en una aplicación Android.

## <a name="related-links"></a>Vínculos relacionados

- [Buscar la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md)
- [Actividades e intents](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Vínculos a recursos digitales de Google](https://developers.google.com/digital-asset-links/)
- [Generador y evaluador de la lista de instrucciones](https://developers.google.com/digital-asset-links/tools/generator)
