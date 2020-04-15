---
title: Vinculación de aplicaciones en Android
description: En esta guía se explica cómo Android 6.0 admite la vinculación de aplicaciones, una técnica que permite a las aplicaciones móviles responder a las direcciones URL de los sitios web. Se explicará qué es la vinculación de aplicaciones, cómo implementar la vinculación de aplicaciones en una aplicación Android 6.0 y cómo configurar un sitio web para conceder permisos a la aplicación móvil en un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487833"
---
# <a name="app-linking-in-android"></a>Vinculación de aplicaciones en Android

_En esta guía se explica cómo Android 6.0 admite la vinculación de aplicaciones, una técnica que permite a las aplicaciones móviles responder a las direcciones URL de los sitios web. Se explicará qué es la vinculación de aplicaciones, cómo implementar la vinculación de aplicaciones en una aplicación Android 6.0 y cómo configurar un sitio web para conceder permisos a la aplicación móvil en un dominio._

## <a name="app-linking-overview"></a>Información general sobre la vinculación de aplicaciones

Las aplicaciones móviles ya no residen aisladas, en muchos casos, constituyen componentes importantes de los negocios y sitios web. Lo deseable que las empresas conecten sin problemas su presencia web y sus aplicaciones móviles, con vínculos en el sitio web que inicien aplicaciones móviles y que muestren contenido relevante en dichas aplicaciones. *La vinculación de aplicaciones* (también conocida como *vinculación profunda*) es una técnica que permite a un dispositivo móvil responder a un URI e iniciar una aplicación móvil que se corresponda con ese URI.

Android controla la vinculación de aplicaciones a través del *sistema de intención*. Cuando el usuario hace clic en un vínculo desde un explorador móvil, este enviará una intención de que Android hará un redireccionamiento a una aplicación registrada. Por ejemplo, al hacer clic en un vínculo de un sitio web de cocina, se abriría una aplicación móvil que está asociada a ese sitio web y mostrará una receta específica al usuario. Si hay más de una aplicación registrada para controlar esa intención, Android generará lo que se conoce como un *cuadro de diálogo de desambiguación*, que le pedirá a un usuario qué seleccione qué aplicación debe administrar la intención. Por ejemplo:

![Captura de pantalla de ejemplo de un cuadro de diálogo de desambiguación](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 mejora esto mediante el control automático de vínculos. Es posible que Android registre automáticamente una aplicación como el controlador predeterminado de un URI. La aplicación se iniciará automáticamente y navegará directamente a la actividad correspondiente. El modo en que Android 6.0 decide controlar el clic en un URI depende de los siguientes criterios:

1. **Una aplicación ya está asociada al URI**: puede que el usuario ya haya asociado una aplicación con un URI. En ese caso, Android seguirá usando esa aplicación.
2. **No hay ninguna aplicación asociada al URI, pero se instalará una aplicación auxiliar**: en este escenario, el usuario no ha especificado una aplicación, por lo que Android usará la aplicación auxiliar instalada para controlar la solicitud.
3. **No hay ninguna aplicación asociada al URI, pero se instalan muchas aplicaciones auxiliares**: esto se debe a que hay varias aplicaciones que admiten el URI, se muestra el cuadro de diálogo de desambiguación y el usuario debe seleccionar qué aplicación controlará el URI.

Si el usuario no tiene ninguna aplicación instalada que admita el URI y otra se instala posteriormente, Android establecerá esa aplicación como el controlador predeterminado del URI después de comprobar la asociación con el sitio web que está asociado a dicho URI.

En esta guía se explica cómo configurar una aplicación Android 6.0, además de cómo crear y publicar el archivo de Digital Asset Links para admitir la vinculación de aplicaciones en Android 6.0.

## <a name="requirements"></a>Requisitos

En esta guía se requiere Xamarin.Android 6.1 y una aplicación destinada a Android 6.0 (nivel de API 23) o una versión superior.

La vinculación de aplicaciones es posible en versiones anteriores de Android mediante el [paquete de NuGet de Rivets](https://www.nuget.org/packages/Rivets/) desde el almacén de componentes de Xamarin. El paquete de Rivets no es compatible con la vinculación de aplicaciones en Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configuración de la vinculación de aplicaciones en Android 6.0

Para configurar los vínculos de aplicaciones en Android 6.0 implica dos pasos principales:

1. **Agregar uno o varios filtros de intención para el URI del sitio web**: los filtros de intención indican a Android cómo controlar un clic en una dirección URL en un explorador móvil.
2. **Publicar un archivo *JSON de Digital Asset Links* en el sitio web**: se trata de un archivo que se carga en un sitio web y que se usa en Android para comprobar la relación entre la aplicación móvil y el dominio del sitio web. Sin esto, Android no puede instalar la aplicación como el controlador predeterminado de los URI; el usuario debe hacerlo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configuración del filtro de intención

Es necesario configurar un filtro de intención que asigne un URI (o posiblemente un conjunto de URI) de un sitio web a una actividad en una aplicación Android. En Xamarin.Android, esta relación se establece agregando una actividad con [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute). El filtro de intención debe declarar la siguiente información:

- **`Intent.ActionView`** : se registrará el filtro de intención para responder a las solicitudes de visualización de la información.
- **`Categories`** : el filtro de intención debe registrar **[Intent.CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** y **[Intent.CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** para poder controlar correctamente el URI web.
- **`DataScheme`** : el filtro de intención debe declarar `http` o `https`. Esos son los únicos dos esquemas válidos.
- **`DataHost`** : este es el dominio del que se originarán los URI.
- **`DataPathPrefix`** : se trata de una ruta de acceso opcional a los recursos del sitio web.
- **`AutoVerify`** : el atributo `autoVerify` indica a Android que compruebe la relación entre la aplicación y el sitio web. Esto se tratará más adelante.

En el ejemplo siguiente se muestra cómo usar [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) para controlar los vínculos desde `https://www.recipe-app.com/recipes` y `http://www.recipe-app.com/recipes`:

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

Android comprobará todos los hosts identificados por los filtros de intención en el archivo Digital Asset Links del sitio web antes de registrar la aplicación como el controlador predeterminado de un URI. Todos los filtros de intención deben pasar la comprobación antes de que Android pueda establecer la aplicación como el controlador predeterminado.

### <a name="creating-the-digital-assets-link-file"></a>Creación del archivo Digital Asset Links

La vinculación de aplicaciones de Android 6.0 requiere que Android compruebe la asociación entre la aplicación y el sitio web antes de establecer la aplicación como el controlador predeterminado del URI. Esta comprobación se realizará cuando se instale la aplicación por primera vez. El archivo *Digital Asset Links* es un archivo JSON que se hospeda en los dominios web pertinentes.

> [!NOTE]
> El filtro de intención debe establecer el atributo `android:autoVerify`; de lo contrario, Android no realizará la comprobación.

El archivo lo coloca el administrador web del dominio en la ubicación **https://domain/.well-known/assetlinks.json** .

El archivo Digital Asset Links contiene los metadatos necesarios para que Android compruebe la asociación. Un archivo **assetlinks.json** tiene los siguientes pares clave-valor:

- `namespace`: el espacio de nombres de la aplicación Android.
- `package_name`: el nombre del paquete de la aplicación Android (declarado en el manifiesto de aplicación).
- `sha256_cert_fingerprints`: las huellas digitales SHA256 de la aplicación firmada. Para obtener más información sobre cómo obtener la huella digital SHA1 de una aplicación, consulte la guía [Buscar la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md).

El siguiente fragmento de código es un ejemplo de **assetlinks.json** con una sola aplicación enumerada:

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

Es posible registrar más de una huella digital SHA256 para admitir distintas versiones o compilaciones de la aplicación. Este archivo **assetlinks.json** es un ejemplo de registro de varias aplicaciones:

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

El sitio web [Google Digital Asset links](https://developers.google.com/digital-asset-links/tools/generator) tiene una herramienta en línea que puede ayudarlo a crear y probar el archivo Digital Asset Links.

### <a name="testing-app-links"></a>Pruebas de los vínculos de aplicaciones

Después de implementar los vínculos de aplicaciones, se deben probar las distintas partes para asegurarse de que funcionan según lo previsto.

Es posible confirmar que el archivo Digital Asset Links tiene el formato adecuado y está hospedado correctamente mediante la API Google Digital Asset Links, tal como se muestra en este ejemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Se pueden realizar dos pruebas para asegurarse de que los filtros de intención se hayan configurado correctamente y de que la aplicación se establece como el controlador predeterminado de un URI:

1. El archivo Digital Asset Links está hospeda correctamente como se describió anteriormente. La primera prueba enviará una intención de que Android debe realizar un redireccionamiento a la aplicación móvil. La aplicación Android debe iniciar y mostrar la actividad registrada para la dirección URL. En un símbolo del sistema, escriba:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. Muestra las directivas de control de vínculos de las aplicaciones instaladas en un dispositivo determinado. El siguiente comando volcará una lista de directivas de vínculo correspondiente a cada usuario del dispositivo con la siguiente información. En el símbolo del sistema, escriba el siguiente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** : el nombre del paquete de la aplicación.
    - **`Domain`** : los dominios (separados por espacios) cuyos vínculos web controlará la aplicación
    - **`Status`** : este es el estado actual del control de vínculos de la aplicación. Un valor de **always** significa que la aplicación tiene declarado `android:autoVerify=true` y ha pasado la comprobación del sistema. Va seguido de un número hexadecimal que representa el registro del sistema Android de la preferencia.

    Por ejemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumen

En esta guía se describe cómo funciona la vinculación de aplicaciones en Android 6.0. A continuación, se explica cómo configurar una aplicación Android 6.0 para admitir los vínculos de aplicaciones y responder a ellos. También se describe cómo probar la vinculación de aplicaciones en una aplicación Android.

## <a name="related-links"></a>Vínculos relacionados

- [Buscar la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md)
- [Vínculos de aplicaciones](https://developers.facebook.com/docs/applinks)
- [Google Digital Assets Links](https://developers.google.com/digital-asset-links/)
- [Generador y evaluador de listas de instrucciones](https://developers.google.com/digital-asset-links/tools/generator)
