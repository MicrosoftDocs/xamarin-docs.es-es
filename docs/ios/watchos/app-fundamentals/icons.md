---
title: Trabajar con iconos de watchos en Xamarin
description: En este documento se describen los distintos iconos necesarios para una aplicación de watchos y cómo configurar una solución para incluir estos iconos.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/26/2018
ms.openlocfilehash: e2beb12ea366918219d2c83532e8a62d94e688b5
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996271"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Trabajar con iconos de watchos en Xamarin

Apple Watch soluciones requieren dos conjuntos de iconos:

- Iconos de aplicación de iOS que aparecerán en el iPhone.
- Apple Watch los iconos que se representarán en un círculo en el menú inspección y en las pantallas de notificación. El icono de la aplicación de inspección también aparece en la aplicación [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS.

## <a name="apple-watch-icons"></a>Iconos de Apple Watch

|Icono|Descripción|Aspecto|
|-|-|-|
|Icono de aplicación de iOS|Aparece en el iPhone e inicia la aplicación primaria|![icono de aplicación de iOS](icons-images/icon-ios.png)|
|Icono de la aplicación de inspección|Aparece en la pantalla principal de Apple Watch|![icono de la aplicación watchos](icons-images/icon-home.png)|
||Aparece en las notificaciones de inspección|![icono de notificación de watchos](icons-images/notification-icon.png)|
||Aparece en la [aplicación de Apple Watch de iOS](~/ios/watchos/app-fundamentals/settings.md)|![icono de aplicación de iOS Watch](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurar la solución

Para asegurarse de que la aplicación de iOS y la aplicación de inspección muestran el nombre y el icono correctos, siga estas instrucciones para cada proyecto:

### <a name="ios-app"></a>Aplicación iOS

Consulte la guía de iconos de la [aplicación iOS](~/ios/app-fundamentals/images-icons/app-icons.md) para asegurarse de que los iconos de la aplicación iOS estén configurados correctamente.

#### <a name="infoplist"></a>Info.plist

La cadena que aparece junto a la aplicación de inspección en la [Apple Watch aplicación de configuración](~/ios/watchos/app-fundamentals/settings.md) se configura en el archivo **info. plist de la aplicación iOS**.

Confirme que **info. plist** tiene una `CFBundleName` clave y un valor (Nota: esto es diferente de `CFBundleDisplayName` , puede tener ambos):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch aplicación

Una vez configurados los iconos de la [aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md) , debe agregar un icono de aplicación Catálogo de recursos a la aplicación de inspección.

1. Haga clic con el botón derecho en el proyecto de la aplicación de inspección y seleccione **archivo > agregar > nuevo archivo... > iOS > catálogo de recursos** para agregar un catálogo de recursos al proyecto.

    ![Agregar un catálogo de recursos al proyecto](icons-images/newasset.png)

2. Haga doble clic en el archivo **Appicos. appiconset/Contents.jsen** el archivo.

    ![Contenido de appicos](icons-images/xcassets-iconset-sml.png)

3. Agregue todas las imágenes de watchos, tal como se muestra en esta captura de pantalla:

    [![Agregue todas las imágenes de watchos, tal como se muestra en esta captura de pantalla.](icons-images/appicons-sml.png)](icons-images/appicons.png#lightbox)

    Consulte las [directrices de iconos de Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) para ver los tamaños necesarios (las dimensiones también se muestran en la pantalla). Recuerde que estos iconos se recortarán automáticamente para representarse en un círculo.

    La lista de iconos debería tener un aspecto similar al siguiente:

    ![La lista de iconos en el Explorador de soluciones](icons-images/xcassets-complete-sml.png)

4. Para asegurarse de que el catálogo de recursos está incluido en la aplicación, agregue la siguiente clave y valor al archivo **info. plist de la aplicación de inspección**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

Puede comprobar que los iconos están configurados correctamente comprobando la [aplicación de configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) en el simulador de iPhone o generando una [notificación](~/ios/watchos/platform/notifications.md) y confirmando que el icono aparece en la pantalla de notificación.

> [!NOTE]
> Los iconos no pueden tener un canal alfa (la aplicación se rechazará durante el envío de la tienda de aplicaciones si hay un canal alfa). Puede comprobar si existe un canal alfa y quitarlo [mediante la aplicación de vista previa en Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).

## <a name="related-links"></a>Vínculos relacionados

- [Icono de watchos de Apple & guía de imágenes](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
