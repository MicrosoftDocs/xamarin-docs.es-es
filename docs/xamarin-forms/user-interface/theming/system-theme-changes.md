---
title: Responder a los cambios de tema del sistema en Xamarin.Forms las aplicaciones
description: Xamarin.Formslas aplicaciones pueden responder a los cambios de tema del sistema operativo mediante el tipo OnAppTheme y la extensión de marcado DynamicResource.
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28bcbed3a03a2abbec42a619062579419a3063a4
ms.sourcegitcommit: 8a18471b3d96f3f726b66f9bc50a829f1c122f29
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988205"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Responder a los cambios de tema del sistema en Xamarin.Forms las aplicaciones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Los dispositivos suelen incluir temas claros y oscuros, que hacen referencia a un amplio conjunto de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo. Las aplicaciones deben respetar estos temas del sistema y responder inmediatamente cuando cambia el tema del sistema.

El tema del sistema puede cambiar por diversos motivos, en función de la configuración del dispositivo. Esto incluye el tema del sistema que el usuario cambia explícitamente, cambia debido a la hora del día y cambia debido a factores ambientales como la luz baja.

Xamarin.Formslas aplicaciones pueden responder a los cambios del tema del sistema mediante el consumo de recursos con la `AppThemeBinding` extensión de marcado y los `SetAppThemeColor` métodos de `SetOnAppTheme<T>` extensión y.

> [!IMPORTANT]
> La respuesta a un cambio de tema del sistema es experimental actualmente y solo se puede usar si se establece la `AppTheme_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

Se deben cumplir los siguientes requisitos para Xamarin.Forms que respondan a un cambio de tema del sistema:

- Xamarin.Forms4.6.0.967 o superior.
- iOS 13 o posterior.
- Android 10 (API 29) o superior.
- Compilación 14393 o posterior de UWP.

En las siguientes capturas de pantallas se muestran páginas con tema, para temas del sistema claros y oscuros en iOS y Android:

[![Captura de pantalla de la Página principal de una aplicación de la que se ha importado, en iOS y Android](system-theme-changes-images/main-page-both-themes.png "Página principal de la aplicación con la que se han importado")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Página principal de la aplicación con la que se han importado") 
 [ ![Captura de pantalla de la página de detalles de una aplicación de la que se ha importado, en iOS y Android](system-theme-changes-images/detail-page-both-themes.png "Página de detalles de la aplicación con el mismo")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Página de detalles de la aplicación con el mismo")

## <a name="define-and-consume-theme-resources"></a>Definir y consumir recursos de tema

Los recursos de los temas claros y oscuros se pueden usar con la `AppThemeBinding` extensión de marcado y los `SetAppThemeColor` métodos de `SetOnAppTheme<T>` extensión y. Con estos enfoques, los recursos se aplican automáticamente en función del valor del tema del sistema actual. Además, los objetos que consumen estos recursos se actualizan automáticamente si cambia el tema del sistema mientras se ejecuta una aplicación.

### <a name="appthemebinding-markup-extension"></a>Extensión de marcado AppThemeBinding

La `AppThemeBinding` extensión de marcado le permite consumir un recurso, como una imagen o un color, basándose en el tema del sistema actual:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el color del texto de la primera [`Label`](xref:Xamarin.Forms.Label) se establece en verde cuando el dispositivo usa su tema claro y se establece en rojo cuando el dispositivo está usando su tema oscuro. Del mismo modo, [`Image`](xref:Xamarin.Forms.Image) muestra un archivo de imagen diferente según el tema actual del sistema.

Además, los recursos definidos en una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) se pueden usar con la `StaticResource` extensión de marcado:

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

En este ejemplo, el color de fondo del [`Grid`](xref:Xamarin.Forms.Grid) y el [`Button`](xref:Xamarin.Forms.Button) estilo cambian en función de si el dispositivo usa su tema claro o el tema oscuro.

Para obtener más información sobre la `AppThemeBinding` extensión de marcado, consulte [extensión de marcado AppThemeBinding](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension).

### <a name="extension-methods"></a>Métodos de extensión

Xamarin.Formsincluye `SetAppThemeColor` `SetOnAppTheme<T>` métodos de extensión y que permiten [`VisualElement`](xref:Xamarin.Forms.VisualElement) a los objetos responder a los cambios de tema del sistema.

El `SetAppThemeColor` método permite [`Color`](xref:Xamarin.Forms.Color) especificar objetos que se establecerán en una propiedad de destino basada en el tema del sistema actual:

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

En este ejemplo, el color del texto de [`Label`](xref:Xamarin.Forms.Label) está establecido en verde cuando el dispositivo usa su tema claro y se establece en rojo cuando el dispositivo está usando su tema oscuro.

El `SetOnAppTheme<T>` método permite especificar objetos de tipo `T` que se establecerán en una propiedad de destino basada en el tema del sistema actual:

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

En este ejemplo, el [`Image`](xref:Xamarin.Forms.Image) `lightlogo.png` se muestra cuando el dispositivo usa su tema claro y `darklogo.png` cuando el dispositivo está usando su tema oscuro.

## <a name="detect-the-current-system-theme"></a>Detectar el tema del sistema actual

El tema del sistema actual se puede detectar obteniendo el valor de la `Application.RequestedTheme` propiedad:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` propiedad devuelve un `OSAppTheme` miembro de enumeración. La enumeración `OSAppTheme` define los miembros siguientes:

- `Unspecified`, que indica que el dispositivo está utilizando un tema no especificado.
- `Light`, que indica que el dispositivo está usando su tema claro.
- `Dark`, que indica que el dispositivo está usando su tema oscuro.

## <a name="set-the-current-user-theme"></a>Establecer el tema del usuario actual

El tema que utiliza la aplicación se puede establecer con la `Application.UserAppTheme` propiedad, que es de tipo `OSAppTheme` , independientemente del tema del sistema que esté operativo actualmente:

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

En este ejemplo, la aplicación está configurada para usar el tema definido para el modo oscuro del sistema, independientemente de qué tema del sistema esté operativo actualmente.

> [!NOTE]
> Establezca la `UserAppTheme` propiedad en `OSAppTheme.Unspecified` como predeterminada en el tema del sistema operativo.

## <a name="react-to-theme-changes"></a>Reaccionar a cambios de tema

El tema del sistema en un dispositivo puede cambiar por diversos motivos, en función de cómo esté configurado el dispositivo. Xamarin.Formsse puede notificar a las aplicaciones cuando el tema del sistema cambia mediante el control del `Application.RequestedThemeChanged` evento:

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

El `AppThemeChangedEventArgs` objeto, que acompaña al `RequestedThemeChanged` evento, tiene una propiedad única denominada `RequestedTheme` , de tipo `OSAppTheme` . Esta propiedad se puede examinar para detectar el tema del sistema solicitado.

## <a name="related-links"></a>Vínculos relacionados

- [SystemThemes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Extensión de marcado AppThemeBinding](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
