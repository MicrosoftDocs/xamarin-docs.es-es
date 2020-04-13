---
title: Detectar el modo oscuro en aplicaciones de Xamarin.Forms
description: El modo oscuro se puede admitir en cualquier aplicación de Xamarin.Forms mediante una combinación de ResourceDictionaries, DynamicResources y conocimiento sin plataforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 7fe1a98e6a497a5791f26df2fc96d41781b71ef6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628311"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Detectar el modo oscuro en aplicaciones de Xamarin.Forms

Las aplicaciones de Xamarin.Forms pueden responder a los cambios de tema del sistema operativo mediante la misma estrategia que permite admitir [temas.](theming.md) La principal diferencia está en cómo se activa el cambio de tema. El modo oscuro se refiere a un conjunto más amplio de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo y a qué aplicaciones se espera que respeten y respondan inmediatamente.

Los requisitos mínimos para usar el modo oscuro en las aplicaciones de Xamarin.Forms son:

- iOS 13 o superior.
- Android 9 o superior (Android 9 admite modos de apariencia que puede consultar).
- Android 10 o superior (Android 10 notifica a las aplicaciones de los cambios de modo).
- UWP v10.0.10240.0 o superior.
- Xamarin.Forms (cualquier versión).

El proceso de soporte de modos de apariencia, incluyendo la luz y la oscuridad, es el siguiente:

1. Defina los recursos compartidos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)por toda la aplicación en un archivo .
2. Defina un tema de diccionario de recursos para cada modo de apariencia que proporcione invalidaciones específicas de cada estilo que desee cambiar.
3. Establezca el tema de modo de apariencia predeterminado en el archivo **App.xaml** de la aplicación.
4. Estilo de la `DynamicResource` aplicación mediante la extensión de marcado donde desea que los estilos reaccionen cuando cambien los modos de apariencia.
5. Agregue código para escuchar los cambios de tema del sistema operativo y cargue el tema correspondiente de la aplicación.

Las siguientes capturas de pantalla muestran páginas temáticas, para el modo claro y oscuro:

[![Captura de pantalla de la página principal de una aplicación temática, en iOS y Android](theming-images/main-page-both-themes.png "Página principal de la aplicación temática")](theming-images/main-page-both-themes-large.png#lightbox "Página principal de la aplicación temática")
[![Captura de pantalla de la página de detalles de una aplicación temática, en iOS y Android](theming-images/detail-page-both-themes.png "Página de detalles de la aplicación temática")](theming-images/detail-page-both-themes-large.png#lightbox "Página de detalles de la aplicación temática")

## <a name="define-themes"></a>Definir temas

Siga la [guía de temas](theming.md) para obtener detalles paso a paso sobre la creación de temas oscuros y claros.

Puede establecer fácilmente un nuevo tema para su aplicación en la ubicación adecuada del código de la plataforma. Para cargar un nuevo tema, simplemente reemplace el diccionario de recursos actual de la aplicación con un diccionario de recursos temáticos de su elección:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>Detectar y aplicar el tema

La detección del tema que se [`RequestedTheme`](~/essentials/app-theme.md) está ejecutando actualmente se puede lograr mediante la característica de [Xamarin.Essentials](~/essentials/index.md) (versión 1.4.0 o posterior). A continuación, puede crear un método auxiliar `App` en una nueva clase o en la clase para configurar el tema:

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
        {
            // Change to dark theme
            // e.g. App.Current.Resources = new YourDarkTheme();
        }
        else
        {
            // Change to light theme
            // e.g. App.Current.Resources = new YourLightTheme();
        }
    }
}
```

## <a name="react-to-appearance-mode-changes"></a>Reaccionar a los cambios en el modo de apariencia

El modo de apariencia en un dispositivo puede cambiar por una variedad de razones dependiendo de cómo el usuario ha configurado sus preferencias, incluyendo la elección explícita de un modo, hora del día o factores ambientales como la poca luz. Tendrá que agregar código de plataforma para asegurarse de que la aplicación puede reaccionar a estos cambios y en las secciones siguientes se describe na. con más detalle.

### <a name="android"></a>Android

Para admitir el modo oscuro en la aplicación, debe actualizar el `Resources/values/styles.xml`tema de `DayNight` la aplicación, que se puede encontrar en , para heredar de un tema:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

Si ha actualizado a Componentes de [materiales](https://www.nuget.org/packages/Xamarin.Google.Android.Material/) de AndroidX (1.1.0-rc2) o más reciente, puede utilizar:

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

En el archivo **de MainActivity.cs** `ConfigChanges.UiMode` de la `ConfigurationChanges` aplicación, `Activity` agregue el campo a la propiedad en el atributo, para que la aplicación reciba una notificación de los cambios en el modo de interfaz de usuario:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

En el mismo archivo `OnConfigurationChanged` **MainActivity.cs,** reemplace el método:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);
    App.ApplyTheme();
}
```

### <a name="ios"></a>iOS

En iOS, los cambios en el modo de apariencia se `ContentPage`notifican en el UIViewController, que en Xamarin.Forms es el archivo . Para invalidar ese método, cree un representador personalizado `PageRenderer.cs`en su proyecto de iOS llamado:

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
                App.ApplyTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            App.ApplyTheme();
        }
    }
}
```

Reemplazar el `TraitCollectionDidChange` método le permite `UserInterfaceStyle` actuar sobre un cambio.

### <a name="uwp"></a>UWP

En UWP, agrega el siguiente código al archivo **de MainPage.xaml.cs** de la aplicación:

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
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Tema (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos en Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
