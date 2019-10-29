---
title: Localización de aplicaciones y recursos de cadena
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7191c37e81da728dfda21da2eaecc95f7b58a401
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025082"
---
# <a name="application-localization-and-string-resources"></a>Localización de aplicaciones y recursos de cadena

La localización de aplicaciones es el acto de proporcionar recursos alternativos para tener como destino una región o configuración regional específica. Por ejemplo, puede proporcionar cadenas de idioma localizadas para varios países o cambiar los colores o el diseño para que coincidan con determinadas referencias culturales. Android cargará y usará los recursos adecuados para la configuración regional del dispositivo en tiempo de ejecución sin realizar ningún cambio en el código fuente.

Por ejemplo, en la imagen siguiente se muestra la misma aplicación que se ejecuta en tres configuraciones regionales de dispositivo diferentes, pero el texto que se muestra en cada botón es específico de la configuración regional en la que se establece cada dispositivo:

[![ejemplos de tres configuraciones regionales diferentes](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

En este ejemplo, el contenido de un archivo de diseño, **Main. axml** tiene un aspecto similar al siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

En el ejemplo anterior, la cadena del botón se cargó desde los recursos proporcionando el identificador de recurso para la cadena:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cadenas de recursos para tres idiomas](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>Localizar aplicaciones Android

Lea la [Introducción a la localización](~/cross-platform/app-fundamentals/localization.md) para obtener sugerencias e instrucciones sobre cómo localizar aplicaciones móviles.

La guía sobre la [localización de aplicaciones Android](~/android/app-fundamentals/localization.md) contiene ejemplos más específicos sobre cómo traducir cadenas y localizar imágenes con Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [Localizar aplicaciones Android](~/android/app-fundamentals/localization.md)
- [Información general sobre la localización entre plataformas](~/cross-platform/app-fundamentals/localization.md)
