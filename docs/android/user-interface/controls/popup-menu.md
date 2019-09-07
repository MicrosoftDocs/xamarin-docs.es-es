---
title: Menú emergente
description: Cómo agregar un menú emergente anclado a una vista determinada.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 8a808e6ea49338de5b6bd1618fc2227e6cf5f0b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764894"
---
# <a name="xamarinandroid-popup-menu"></a>Menú emergente de Xamarin. Android

El [menú](xref:Android.Widget.PopupMenu) desacoplado (también denominado _menú contextual_) es un menú delimitado a una vista determinada. En el ejemplo siguiente, una sola actividad contiene un botón. Cuando el usuario pulsa el botón, se muestra un menú emergente de tres elementos:

[![Ejemplo de una aplicación con un botón y un menú emergente de tres elementos](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>Crear un menú emergente

El primer paso es crear un archivo de recursos de menú para el menú y colocarlo en **recursos/menú**. Por ejemplo, el siguiente XML es el código para el menú de tres elementos que se muestra en la captura de pantalla anterior, **Resources/menu/popup_menu. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

A continuación, cree una instancia `PopupMenu` de y delimite a su vista. Cuando se crea una instancia de `PopupMenu`, se pasa a su constructor una referencia `Context` a y a la vista a la que se adjuntará el menú. Como resultado, el menú emergente se delimita en esta vista durante su construcción.

En el ejemplo siguiente, `PopupMenu` se crea en el controlador de eventos click para el botón (que se denomina `showPopupMenu`). Este botón también es la vista a la que `PopupMenu` está anclada, tal como se muestra en el ejemplo de código siguiente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Por último, el menú emergente debe estar *inflado* con el recurso de menú que se creó anteriormente. En el ejemplo siguiente, se agrega la llamada al método [Inflate](xref:Android.Views.LayoutInflater.Inflate*) del menú y se llama a su método [Show](xref:Android.Widget.PopupMenu.Show) para mostrarlo:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>Control de eventos de menú

Cuando el usuario selecciona un elemento de menú, se genera el evento click de [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) y se descarta el menú. Al puntear en cualquier lugar fuera del menú simplemente se descartará. En cualquier caso, cuando se descarta el menú, se generará su [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) . En el código siguiente se agregan controladores de eventos `MenuItemClick` para `DismissEvent` los eventos y:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
    menu.Show ();
};
```

## <a name="related-links"></a>Vínculos relacionados

- [PopupMenuDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)
