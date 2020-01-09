---
title: Controles de interfaz de usuario en Xamarin. iOS
description: En este documento se proporcionan vínculos a guías que describen los distintos controles de interfaz de usuario de iOS disponibles para los desarrolladores de Xamarin. iOS. En el contenido vinculado se describen las alertas, botones, vistas de colección, imágenes, controles de cámara manuales, mapas, etiquetas, selectores, selectores de fecha y mucho más.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a4bf0b89a9ab336bf47ddcd104760211d912f423
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663439"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controles de interfaz de usuario en Xamarin. iOS

En este documento se presentan algunos de los controles de interfaz de usuario de iOS más comunes y cómo usarlos.

## <a name="alertsalertsmd"></a>[Alertas](alerts.md)

A partir de iOS 8, UIAlertController ha terminado de reemplazar UIActionSheet y UIAlertView, que ahora están en desuso.

## <a name="buttonsbuttonsmd"></a>[Botones](buttons.md)

La clase botón se usa para representar distintos estilos de botón en pantallas de iOS. En esta sección se presentan las diferentes opciones para trabajar con botones en iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Vistas de colección](uicollectionview.md)

Las vistas de colección, disponibles en la clase `UICollectionView`, son un nuevo concepto en iOS 6 que presenta la presentación de varios elementos en la pantalla mediante diseños. Los patrones para proporcionar datos a una `UICollectionView` para crear elementos e interactuar con dichos elementos siguen los mismos patrones de delegación y de origen de datos que se usan habitualmente en el desarrollo de iOS.

## <a name="imagesimagemd"></a>[Imágenes](image.md)

Agregar imágenes a la aplicación requiere dos pasos: en primer lugar, agregue las imágenes al proyecto; a continuación, agregue controles y código para mostrarlos en una pantalla. Consulte el artículo sobre [Cómo trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener una cobertura más detallada del control de imágenes en Xamarin. iOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controles manuales de la cámara](intro-to-manual-camera-controls.md)

Los controles de cámara manuales, proporcionados por el `AVFoundation Framework` en iOS 8, permiten que una aplicación móvil tenga control total sobre la cámara de un dispositivo iOS. Este nivel específico de control se puede usar para crear aplicaciones de cámara de nivel profesional y proporcionar composiciones de artista mediante el ajuste de los parámetros de la cámara mientras se toma una imagen o un vídeo.

## <a name="mapsios-mapsindexmd"></a>[Asignaciones](ios-maps/index.md)

Las asignaciones son una característica común en todos los sistemas operativos móviles modernos. iOS ofrece compatibilidad de asignación de forma nativa a través del marco del kit de mapas. Con el kit de mapa, las aplicaciones pueden agregar fácilmente mapas completos e interactivos. Estas asignaciones pueden personalizarse de varias formas, como agregar anotaciones para marcar ubicaciones en un mapa y superponer gráficos de formas arbitrarias. El kit de mapas incluso tiene compatibilidad integrada para mostrar la ubicación actual de un dispositivo.

## <a name="labelslabelsmd"></a>[Etiquetas](labels.md)

El control `UILabel` se usa para mostrar texto de solo lectura y de una sola línea.

## <a name="pickers-and-date-pickerspickermd"></a>[Selectores y selectores de fecha](picker.md)

El control selector muestra el control "rueda-like" que contiene una lista desplazable de valores con el valor seleccionado que se va a resaltar. Los usuarios giran la rueda para seleccionar la opción que deseen.

Un caso de usuario específico para los selectores para establecer la fecha y la hora. Para proporcionar a este Apple, ha creado una subclase personalizada de la clase UIPickerView denominada UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicadores de progreso y actividad](progress-activity-indicator.md)

iOS proporciona dos maneras principales de indicar el progreso de la aplicación: indicadores de actividad (incluido un indicador de actividad de _red_ específico) y barras de progreso.

## <a name="search-barssearchbarmd"></a>[Barras de búsqueda](searchbar.md)

UISearchBar se usa para buscar en una lista de valores. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Controles deslizantes, conmutadores y controles segmentados](slider-switch-segmented-controls.md)

El control deslizante permite la selección simple de un valor numérico dentro de un intervalo. iOS usa el `UISwitch` como entrada booleana que se puede representar mediante un botón de radio en otras plataformas. Un control segmentado es una manera organizada de permitir que los usuarios interactúen con un pequeño número de opciones.

## <a name="stack-viewuistackviewmd"></a>[Vista de pila](uistackview.md)

El control de vista de pila (`UIStackView`) aprovecha la eficacia de las clases de diseño y tamaño automáticos para administrar una pila de subvistas, ya sea horizontal o verticalmente, que responde dinámicamente a la orientación y el tamaño de pantalla del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tablas y vistas](tables/index.md)

en esta sección se presentan las clases que se usan para crear y mostrar tablas y, a continuación, se proporcionan ejemplos de cómo usarlas en Xamarin. iOS. Tratará el uso de la apariencia predeterminada para las tablas, la personalización del diseño, la implementación de la edición y el uso del diseñador de Xamarin iOS para diseñar una tabla visualmente. A veces, la presentación es obviamente una lista de filas (por ejemplo, la aplicación de música) y otras veces es difícil reconocer el control de tabla (por ejemplo, la edición en la aplicación de contactos o una conversación en la aplicación de mensajes).

## <a name="text-inputtext-inputmd"></a>[Entrada de texto](text-input.md)

La aceptación de la entrada de texto del usuario se logra con el `UITextField` para las entradas de una sola línea y UITextView para el texto editable de varias líneas. Puede arrastrar cualquiera de estos controles a una pantalla y hacer doble clic para establecer el texto inicial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barras de pestañas y controladores de la barra de pestañas](creating-tabbed-applications.md)

las aplicaciones de iOS que usan una interfaz de usuario de navegación por tabulación se compilan mediante la clase UITabBarController. En este artículo veremos cómo configurar una aplicación con pestañas que contiene varios controladores y vistas. A continuación, examinaremos cómo cargar un UITabBarController cuando no sea el controlador raíz, como después de una pantalla de inicio de sesión.

## <a name="web-viewswebviewmd"></a>[Vistas web](webview.md)

En este artículo, exploraremos las vistas web proporcionadas por Apple –`WKWebview` y `SFSafariViewController`: sus similitudes y diferencias y cómo se pueden usar.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
