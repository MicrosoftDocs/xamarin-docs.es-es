---
title: "responder a los cambios de tema del sistema en Xamarin.Forms las aplicaciones" Descripción: " Xamarin.Forms las aplicaciones pueden responder a los cambios de tema del sistema operativo mediante el tipo OnAppTheme y la extensión de marcado DynamicResource".
MS. AssetID: D10506DD-BAA0-437F-A4AD-882D16E7B60D ms. Prod: Xamarin ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/22/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Responder a los cambios de tema del sistema en Xamarin.Forms las aplicaciones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Los dispositivos suelen incluir temas claros y oscuros, que hacen referencia a un amplio conjunto de preferencias de apariencia que se pueden establecer en el nivel del sistema operativo. Las aplicaciones deben respetar estos temas del sistema y responder inmediatamente cuando cambia el tema del sistema.

El tema del sistema puede cambiar por diversos motivos, en función de la configuración del dispositivo. Esto incluye el tema del sistema que el usuario cambia explícitamente, cambia debido a la hora del día y cambia debido a factores ambientales como la luz baja.

Xamarin.Formslas aplicaciones pueden responder a los cambios del tema del sistema definiendo los recursos con la `AppThemeColor` clase, la `OnAppTheme<T>` clase y la `OnAppTheme` extensión de marcado. Estos recursos se deben consumir con la `DynamicResource` extensión de marcado.

> [!IMPORTANT]
> La respuesta a un cambio de tema del sistema es experimental actualmente y solo se puede usar si se establece la `AppTheme_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

Se deben cumplir los siguientes requisitos para Xamarin.Forms que respondan a un cambio de tema del sistema:

- Xamarin.Forms4,6 o superior.
- iOS 13 o posterior.
- Android 10 (API 29) o superior.
- Compilación 14393 o posterior de UWP.

En las siguientes capturas de pantallas se muestran páginas con tema, para temas del sistema claros y oscuros en iOS y Android:

[![Captura de pantalla de la Página principal de una aplicación de la que se ha importado, en iOS y Android](system-theme-changes-images/main-page-both-themes.png "Página principal de la aplicación con la que se han importado")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Página principal de la aplicación con la que se han importado") 
 [ ![Captura de pantalla de la página de detalles de una aplicación de la que se ha importado, en iOS y Android](system-theme-changes-images/detail-page-both-themes.png "Página de detalles de la aplicación con el mismo")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Página de detalles de la aplicación con el mismo")

## <a name="define-and-consume-theme-resources"></a>Definir y consumir recursos de tema

Los recursos de los temas claros y oscuros se pueden definir con la `AppThemeColor` clase, la `OnAppTheme<T>` clase y la `OnAppTheme` extensión de marcado. Con cada enfoque, estos recursos se aplican automáticamente en función del valor del tema del sistema actual. Además, los objetos que consumen estos recursos se actualizan automáticamente si cambia el tema del sistema mientras se ejecuta una aplicación.

### <a name="appthemecolor"></a>AppThemeColor

La `AppThemeColor` clase se utiliza para definir los [`Color`](xref:Xamarin.Forms.Color) recursos para los temas del sistema Light y Dark. `AppThemeColor`los recursos se deben definir en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <AppThemeColor x:Key="PageBackgroundColor"
                       Light="White"
                       Dark="Black" />
        <AppThemeColor x:Key="NavigationBarColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="PrimaryColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="SecondaryColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="PrimaryTextColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="SecondaryTextColor"
                       Light="White"
                       Dark="White" />
        <AppThemeColor x:Key="TertiaryTextColor"
                       Light="Gray"
                       Dark="WhiteSmoke" />
        <AppThemeColor x:Key="TransparentColor"
                       Light="Transparent"
                       Dark="Transparent" />
    </Application.Resources>
</Application>
```

Cada `AppThemeColor` recurso debe tener un `x:Key` atributo, que le proporciona una clave descriptiva en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . El valor de las `Light` `Dark` propiedades y debe ser [`Color`](xref:Xamarin.Forms.Color) objetos. Además, una `Default` propiedad se puede establecer en una `Color` para que la use el objeto de consumo de forma predeterminada.

`AppThemeColor`los recursos se pueden consumir en línea:

```xaml
<Label Text="This monkey reacts appropriately to ridiculous assertions and actions"
       TextColor="{DynamicResource PrimaryTextColor}" />
```

Como alternativa, `AppThemeColor` los recursos se pueden usar con objetos implícitos o explícitos [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style TargetType="NavigationPage">
    <Setter Property="BarBackgroundColor"
            Value="{DynamicResource NavigationBarColor}" />
    <Setter Property="BarTextColor"
            Value="{DynamicResource SecondaryColor}" />
</Style>
```

> [!IMPORTANT]
> `AppThemeColor`los recursos deben usarse con la `DynamicResource` extensión de marcado. Esto garantiza que la apariencia del objeto de consumo se actualiza cuando cambia el tema del sistema.

### <a name="onappthemelttgt"></a>OnAppTheme &lt; T&gt;

La `OnAppTheme<T>` clase se usa para definir recursos de cualquier tipo para los temas del sistema Light y Dark. `OnAppTheme<T>`los recursos se deben definir en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , con el `T` argumento especificado como el valor del `x:TypeArguments` atributo:

```xaml
<Application ...>
    <Application.Resources>
        <OnAppTheme x:Key="ImageLogo"
                    x:TypeArguments="FileImageSource"
                    Light="lightlogo.png"
                    Dark="darklogo.png" />
    </Application.Resources>
</Application>
```

Cada `OnAppTheme<T>` recurso debe tener un `x:Key` atributo, que le proporciona una clave descriptiva en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . El valor de las `Light` `Dark` propiedades y debe ser objetos del tipo definido como `x:TypeArguments` atributo. Además, una `Default` propiedad se puede establecer en un objeto de tipo `T` que el objeto de consumo va a utilizar de forma predeterminada.

`OnAppTheme<T>`los recursos se pueden consumir en línea:

```xaml
<Image Source="{DynamicResource ImageLogo}"
       Aspect="AspectFit"
       HeightRequest="200" /
```

Como alternativa, `OnAppTheme<T>` los recursos se pueden usar con objetos implícitos o explícitos [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style x:Key="imageLogoStyle"
       TargetType="Image">
    <Setter Property="Source"
            Value="{DynamicResource ImageLogo}" />
    <Setter Property="Aspect"
            Value="AspectFit" />
</Style>
```

> [!IMPORTANT]
> `OnAppTheme<T>`los recursos deben usarse con la `DynamicResource` extensión de marcado. Esto garantiza que la apariencia del objeto de consumo se actualiza cuando cambia el tema del sistema.

### <a name="onapptheme-markup-extension"></a>Extensión de marcado OnAppTheme

La `OnAppTheme` extensión de marcado le permite especificar el recurso que se va a consumir, como una imagen o un color, basándose en el tema del sistema actual. Proporciona la misma funcionalidad que la `OnAppTheme<T>` clase, pero con una representación más concisa:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Image Source="{OnAppTheme Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el color del texto de la primera [`Label`](xref:Xamarin.Forms.Label) se establece en verde cuando el dispositivo usa su tema claro y se establece en rojo cuando el dispositivo está usando su tema oscuro. Del mismo modo, [`Image`](xref:Xamarin.Forms.Image) muestra un archivo de imagen diferente según el tema actual del sistema.

Para obtener más información sobre la `OnAppTheme` extensión de marcado, consulte [extensión de marcado OnAppTheme](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension).

## <a name="detect-the-current-system-theme"></a>Detectar el tema del sistema actual

El tema del sistema actual se puede detectar obteniendo el valor de la `Application.RequestedTheme` propiedad:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` propiedad devuelve un `OSAppTheme` miembro de enumeración. La enumeración `OSAppTheme` define los miembros siguientes:

- `Unspecified`, que indica que el dispositivo está utilizando un tema no especificado.
- `Light`, que indica que el dispositivo está usando su tema claro.
- `Dark`, que indica que el dispositivo está usando su tema oscuro.

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
- [Extensión de marcado OnAppTheme](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos enXamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicación de estilo a aplicaciones Xamarin.Forms con estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
