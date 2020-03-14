---
title: Detectar el modo oscuro en aplicaciones de Xamarin. Forms
description: Se puede admitir el modo oscuro en cualquier aplicación de Xamarin. Forms mediante una combinación de objetos ResourceDictionary, DynamicResources y conocimientos de plataforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 104237155797ca90c52ad385e8349480f9666c4c
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303507"
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

Puede establecer fácilmente un nuevo tema para la aplicación en la ubicación adecuada del código de la plataforma. Para cargar un nuevo tema, solo tiene que reemplazar el Diccionario de recursos actual de la aplicación por un diccionario de recursos con temas de su elección:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>Detectar y aplicar el tema

La detección del tema actualmente en ejecución se puede lograr mediante la característica [`RequestedTheme`](~/essentials/app-theme.md) de [Xamarin. Essentials](~/essentials/index.md) (versión 1.4.0 o posterior). Después, puede crear un método auxiliar en una nueva clase o en la clase `App` para configurar el tema:

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
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
}
```

## <a name="react-to-appearance-mode-changes"></a>Reaccionar a los cambios del modo de apariencia

El modo de apariencia de un dispositivo puede cambiar por diversos motivos, en función de cómo haya configurado el usuario sus preferencias, como elegir explícitamente un modo, una hora del día o factores ambientales como baja luz. Tendrá que agregar código de plataforma para asegurarse de que la aplicación pueda reaccionar a estos cambios, y en las secciones siguientes se describe con más detalle.

### <a name="android"></a>Android

Para admitir el modo oscuro en la aplicación, debe actualizar el tema de la aplicación, que se puede encontrar en `Resources/values/styles.xml`, para que herede de un tema `DayNight`:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

Si ha actualizado [los componentes de material](https://www.nuget.org/packages/Xamarin.Google.Android.Material/) de AndroidX (1.1.0-RC2) o una versión más reciente, puede usar:

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

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
    App.ApplyTheme();
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
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Cómo hacerlo (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos en Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicación de estilos para aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
