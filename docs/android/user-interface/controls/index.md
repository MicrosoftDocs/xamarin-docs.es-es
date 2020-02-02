---
title: Controles de Android (widgets)
description: Bloques de creación para crear interfaces de usuario de Xamarin. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 25e33ee2d77501b3913a3598ef7855714f0b9bc1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940902"
---
# <a name="xamarinandroid-controls-widgets"></a>Controles de Xamarin. Android (widgets)

Xamarin. Android expone todos los controles de interfaz de usuario (widgets) nativos proporcionados por Android. Estos controles se pueden agregar fácilmente a las aplicaciones de Xamarin. Android mediante el Android Designer o mediante programación a través de archivos de diseño XML. Independientemente del método que elija, Xamarin. Android expone todas las propiedades y métodos de los objetos de la interfaz de C#usuario en. En las secciones siguientes se presentan los controles de interfaz de usuario de Android más comunes y se explica cómo incorporarlos a las aplicaciones de Xamarin. Android.

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[Barra de acciones](~/android/user-interface/controls/action-bar.md) 

`ActionBar` es una barra de herramientas que muestra el título de la actividad, las interfaces de navegación y otros elementos interactivos. Normalmente, la barra de acciones aparece en la parte superior de la ventana de una actividad.

![Ejemplo de barra](images/action-bar.png)

## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[Autocompletar](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` es un elemento de la vista de texto editable que muestra sugerencias de finalización automáticamente mientras el usuario está escribiendo. La lista de sugerencias se muestra en un menú desplegable en el que el usuario puede elegir un elemento del que reemplazar el contenido del cuadro de edición.

![Ejemplo de autocompletar](images/auto-complete.png)

## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[Botones](~/android/user-interface/controls/buttons/index.md)

Los botones son elementos de la interfaz de usuario que el usuario puntea para realizar una acción.

![Botones de ejemplo](images/buttons.png)

## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

La clase `Calendar` se usa para convertir una instancia específica en el tiempo (un valor de milisegundos que se desplaza del tiempo) a valores como Year, month, hour, Day del mes y la fecha de la semana siguiente.
`Calendar` admite una gran cantidad de opciones de interacción con datos de calendario, incluida la capacidad de leer y escribir eventos, asistentes y recordatorios. Al usar el proveedor de calendario en la aplicación, los datos que agregue a través de la API aparecerán en la aplicación de calendario integrada que se incluye en Android.

![Calendario de ejemplo](images/calendar.png)

## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` es un componente de interfaz de usuario que presenta el contenido de texto e imagen en las vistas que se parecen a las tarjetas. `CardView` se implementa como un widget `FrameLayout` con esquinas redondeadas y una sombra. Normalmente, un `CardView` se utiliza para presentar un elemento de una sola fila en un grupo de vistas de `ListView` o `GridView`.

![Ejemplo de vista de tarjeta](images/cardview.png)

## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[Editar texto](~/android/user-interface/controls/edit-text.md)

`EditText` es un elemento de la interfaz de usuario que se utiliza para escribir y modificar texto.

![Ejemplo de edición de texto](images/edit-text.png)

## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[Galería](~/android/user-interface/controls/gallery.md)

`Gallery` es un widget de diseño que se usa para mostrar los elementos en una lista de desplazamiento horizontal. coloca la selección actual en el centro de la vista.

![Galería de ejemplo](images/gallery.png)

## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)

La *barra de navegación* proporciona controles de navegación en dispositivos que no incluyen botones de hardware para **Inicio**, **atrás**y **menú**.

![Barra de navegación de ejemplo](images/navigation-bar.png)

## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[Selectores](~/android/user-interface/controls/pickers/index.md)

Los *selectores* son elementos de interfaz de usuario que permiten al usuario seleccionar una fecha o una hora mediante los cuadros de diálogo proporcionados por Android.

![Selector de ejemplo](images/picker.png)

## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[Menú emergente](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` se usa para mostrar menús emergentes que se adjuntan a una vista determinada.

![Menú emergente de ejemplo](images/popup-menu.png)

## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Un `RatingBar` es un elemento de la interfaz de usuario que muestra una clasificación en estrellas.

![Ejemplo de RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` es un elemento de la interfaz de usuario que proporciona una manera rápida de seleccionar un valor de un conjunto. Es similar a una lista desplegable. 

![Ejemplo de control de número](images/spinner.png)

## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` es un elemento de la interfaz de usuario que permite a los usuarios alternar entre dos Estados, como activado o desactivado. El valor predeterminado de `Switch` es OFF.

![Modificador de ejemplo](images/switch.png)

## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` es una vista que usa la representación 2D con aceleración de hardware para permitir que se muestre un flujo de contenido de vídeo o OpenGL.

![Ejemplo de vista de textura](images/texture-view.png)

## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

La `Toolbar` widget (introducida en Android 5,0 Lollipop) puede considerarse como una generalización de la interfaz de la barra de acciones &ndash; está pensada para reemplazar la barra de acciones. El `Toolbar` se puede usar en cualquier parte del diseño de una aplicación y es mucho más personalizable que una barra de acción.

![Barra de herramientas de ejemplo](images/toolbar.png)

## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

El `ViewPager` es un administrador de diseño que permite al usuario voltear a la izquierda y a la derecha a través de las páginas de datos.

![Ejemplo de ViewPager](images/viewpager.png)

## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` es un elemento de la interfaz de usuario que le permite crear su propia ventana para ver páginas web (o incluso desarrollar un explorador completo).

![Vista Web de ejemplo](images/web-view.png)
