---
title: Detectar el modo oscuro en aplicaciones de Xamarin. Forms
description: Se puede admitir el modo oscuro en cualquier aplicación de Xamarin. Forms mediante una combinación de objetos ResourceDictionary, DynamicResources y conocimientos de plataforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486293"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Detectar el modo oscuro en aplicaciones de Xamarin. Forms

Las aplicaciones de Xamarin. Forms pueden responder a los cambios de tema del sistema operativo mediante la misma estrategia que le permite admitir [temas](theming.md). La principal diferencia radica en cómo se desencadena el cambio de tema. El modo oscuro hace referencia a un conjunto más amplio de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo y a qué aplicaciones se espera que respeten y respondan inmediatamente.

Los requisitos mínimos para usar el modo oscuro en las aplicaciones de Xamarin. Forms son:

- iOS 13 o posterior.
- Android 9 o posterior (Android 9 admite los modos de apariencia que puede consultar).
- Android 10 o posterior (Android 10 notifica a las aplicaciones de los cambios de modo).
- UWP v 10.0.10240.0 o superior.
- Xamarin. Forms (cualquier versión).

El proceso de admitir modos de apariencia, como Light y Dark, es el siguiente:

1. Defina los recursos compartidos por toda la aplicación en una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
2. Defina un tema de Diccionario de recursos para cada modo de aspecto que proporcione invalidaciones específicas de cada estilo que desee cambiar.
3. Establezca el tema del modo de apariencia predeterminado en el archivo **app. Xaml** de la aplicación.
4. Use la extensión de marcado `DynamicResource` para aplicar estilo a la aplicación en la que desea que los estilos reaccione cuando cambien los modos de apariencia.
5. Agregue código para escuchar los cambios de tema del sistema operativo y cargue el tema correspondiente de la aplicación.

En las siguientes capturas de pantallas se muestran páginas con diapositivas, para el modo claro y oscuro:

[![Captura de pantalla de la Página principal de una aplicación de la que se ha importado, en iOS y android](theming-images/main-page-both-themes.png "Página principal de la aplicación con la que se han importado")](theming-images/main-page-both-themes-large.png#lightbox "Página principal de la aplicación con la que se han importado")
[ ![captura de pantalla de la página de detalles de una aplicación de la que se ha importado, en iOS y Android](theming-images/detail-page-both-themes.png "Página de detalles de la aplicación con el mismo")](theming-images/detail-page-both-themes-large.png#lightbox "Página de detalles de la aplicación con el mismo")

## <a name="define-themes"></a>Definir temas

Siga la [Guía de temas](theming.md) para obtener instrucciones paso a paso sobre cómo crear temas oscuros y claros.

## <a name="react-to-appearance-mode-changes"></a>Reaccionar a los cambios del modo de apariencia

El modo de apariencia de un dispositivo puede cambiar por diversos motivos, en función de cómo haya configurado el usuario sus preferencias, como elegir explícitamente un modo, una hora del día o factores ambientales como baja luz. Tendrá que agregar código de plataforma para asegurarse de que la aplicación pueda reaccionar a estos cambios, y en las secciones siguientes se describe con más detalle.

### <a name="android"></a>Android

En el archivo **MainActivity.CS** de la aplicación, agregue el campo `ConfigChanges.UiMode` a la propiedad `ConfigurationChanges` en el atributo `Activity`, de modo que se notificará a la aplicación los cambios en el modo de la interfaz de usuario:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

En el mismo archivo **MainActivity.CS** , invalide el método `OnConfigurationChanged`:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

En iOS, los cambios en el modo de apariencia se notifican en el UIViewController, que en Xamarin. Forms es el `ContentPage`. Para invalidar ese método, cree un representador personalizado en el proyecto de iOS denominado `PageRenderer.cs`:

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

Reemplazar el método `TraitCollectionDidChange` le permite actuar sobre un cambio `UserInterfaceStyle`.

### <a name="uwp"></a>UWP

En UWP, agregue el código siguiente al archivo **mainpage.Xaml.CS** de la aplicación:

```csharp
public sealed partial class MainPage
{

    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>Establecer temas oscuros y claros

Después de seguir la guía de [temas](theming.md) , puede establecer fácilmente un nuevo tema para la aplicación en la ubicación adecuada del código de plataforma anterior. Para cargar un nuevo tema, solo tiene que reemplazar el Diccionario de recursos actual de la aplicación por un diccionario de recursos con temas de su elección:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>Vínculos relacionados

- [Cómo hacerlo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos en Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
