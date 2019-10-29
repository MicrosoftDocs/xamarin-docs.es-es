---
title: Trabajar con tamaños de pantalla en Xamarin. Android y desgaste del SO
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 86e05dc0e9cd5df325126cc5a339b36dd27c1e45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030353"
---
# <a name="working-with-screen-sizes"></a>Trabajar con tamaños de pantalla

Los dispositivos de desgaste de Android pueden tener una pantalla rectangular o redonda, que también puede tener tamaños diferentes.

![Capturas de pantallas de los desgastes rectangulares y redondos](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Tipo de pantalla de identificación

La biblioteca de soporte técnico de desgaste proporciona algunos controles que le ayudan a detectar y adaptarse a diferentes formas de pantalla, como `WatchViewStub` y `BoxInsetLayout`.

Tenga en cuenta que algunos de los demás controles de la biblioteca de compatibilidad (como `GridViewPager`) detectan *automáticamente* la forma de pantalla y no deben agregarse como elementos secundarios de los controles que se describen a continuación.

### <a name="watchviewstub"></a>WatchViewStub

Vea el ejemplo de [WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub) para ver cómo detectar el tipo de pantalla y mostrar un diseño diferente para cada tipo.

El archivo de diseño principal contiene una `android.support.wearable.view.WatchViewStub` que hace referencia a diferentes diseños para pantallas rectangulares y redondas mediante los atributos `app:rectLayout` y `app:roundLayout`:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

La solución contiene diferentes diseños para cada estilo que se seleccionarán en tiempo de ejecución:

![Archivos que se muestran en recursos/diseño](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

En lugar de crear diseños diferentes para cada tipo de pantalla, también puede crear una vista única que se adapte a pantallas rectangulares o redondas.

En este [ejemplo de Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) se muestra cómo usar el `BoxInsetLayout` para usar el mismo diseño en pantallas rectangulares y redondas.

## <a name="wear-ui-designer"></a>Diseñador de IU de desgaste

Xamarin Android Designer admite pantallas rectangulares y redondas:

![Selección de la pantalla cuadrada del desgaste de Android en la Android Designer de Xamarin](screen-sizes-images/design-screen-type.png)

Aquí se muestra la superficie de diseño en estilo rectangular:

![Superficie de diseño en estilo rectangular](screen-sizes-images/design-rect.png) 

Aquí se muestra la superficie de diseño en el estilo redondo:

![Superficie de diseño en estilo redondo](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>Simulador de desgaste

El **Administrador del emulador de Google** contiene definiciones de dispositivos para ambos tipos de pantalla. Puede crear emuladores rectangulares y redondos para probar la aplicación.

![Desgaste de las definiciones de dispositivos que se muestran en el administrador de emuladores de Google](screen-sizes-images/emulator-devices.png)

El emulador se representará como esto para una pantalla rectangular:

![Representación del emulador de una pantalla rectangular](screen-sizes-images/recipe-2.png) 

Se representará como esto para una pantalla redonda:

![Representación del emulador de una pantalla redonda](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicaciones de pantalla completa para el desgaste de Android](https://www.youtube.com/watch?v=naf_WbtFAlY) desde [developers.Google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).
