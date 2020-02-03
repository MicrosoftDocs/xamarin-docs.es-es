---
title: Barra de navegación de Xamarin. Android
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: 99d0303dc1560796cb372d0b8af2fafd16c6097f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725145"
---
# <a name="xamarinandroid-navigation-bar"></a>Barra de navegación de Xamarin. Android

Android 4 presentó una nueva característica de interfaz de usuario del sistema denominada *barra de navegación*, que proporciona controles de navegación en los dispositivos que no incluyen botones de hardware para **Inicio**, **atrás**y **menú**.
En la captura de pantalla siguiente se muestra la barra de navegación desde un dispositivo primo de Nexus:

 [![ejemplo de una barra de navegación de Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Hay disponibles varias nuevas marcas que controlan la visibilidad de la barra de navegación y sus controles, así como la visibilidad de la barra del sistema que se presentó en Android 3. Las marcas se definen en la clase `Android.View.View` y se enumeran a continuación:

- `SystemUiFlagVisible` &ndash; hace que la barra de navegación esté visible.
- `SystemUiFlagLowProfile` &ndash; atenúa los controles de la barra de navegación.
- `SystemUiFlagHideNavigation` &ndash; oculta la barra de navegación.

Estas marcas se pueden aplicar a cualquier vista de la jerarquía de vistas estableciendo la propiedad `SystemUiVisibility`. Si varias vistas tienen esta propiedad establecida, el sistema las combina con una operación OR y las aplica siempre que la ventana en la que se establecen las marcas conserva el foco. Al quitar una vista, también se quitarán las marcas que haya establecido.

En el ejemplo siguiente se muestra una aplicación sencilla en la que al hacer clic en cualquiera de los botones se cambia el `SystemUiVisibility`:

 [![capturas de pantallas en las que se muestra la SystemUiVisibility visible, bajo perfil y oculto](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

El código para cambiar el `SystemUiVisibility` establece la propiedad en un `TextView` desde el controlador de eventos Click de cada botón, como se muestra a continuación:

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);

lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};

hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};

visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

Además, un cambio `SystemUiVisibility` genera un evento `SystemUiVisibilityChange`. Al igual que el establecimiento de la propiedad `SystemUiVisibility`, se puede registrar un controlador para el evento `SystemUiVisibilityChange` para cualquier vista de la jerarquía. Por ejemplo, el código siguiente usa la instancia de `TextView` para registrarse para el evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>Vínculos relacionados

- [SystemUIVisibilityDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
