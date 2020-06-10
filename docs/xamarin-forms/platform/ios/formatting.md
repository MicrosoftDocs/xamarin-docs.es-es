---
Título: "agregar formato específico de iOS" Descripción: "en este artículo se explica cómo establecer la apariencia específica de iOS sin usar un Xamarin.Forms representador personalizado".
MS. Prod: Xamarin ms. AssetID: CE50E207-D092-4D88-8439-1B51F178E7ED ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/29/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="adding-ios-specific-formatting"></a>Agregar formato específico de iOS

Una manera de establecer el formato específico de iOS es crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para un control y establecer estilos y colores específicos de la plataforma para cada plataforma.

Otras opciones para controlar el Xamarin.Forms aspecto de la aplicación de iOS incluyen:

- Configurar las opciones de presentación en [ **info. plist**](#customizing-infoplist)
- Establecer estilos de control a través de la [ `UIAppearance` API](#uiappearance-api)

Estas alternativas se describen a continuación.

## <a name="customizing-infoplist"></a>Personalización de info. plist

El archivo **info. plist** le permite configurar algunos aspectos de renderering de una aplicación de iOS, como, por ejemplo, cómo se muestra la barra de estado (y si).

Por ejemplo, el [ejemplo todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) usa el código siguiente para establecer el color de la barra de navegación y el color del texto en todas las plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

El resultado se muestra en el siguiente fragmento de código de pantalla. Tenga en cuenta que los elementos de la barra de estado son negros (esto no se puede establecer en Xamarin.Forms porque es una característica específica de la plataforma).

![](theme-images/status-default-sml.png "iOS Theming")

Idealmente, la barra de estado también podría ser algo que podamos realizar directamente en el proyecto de iOS. Agregue las siguientes entradas al archivo **info. plist** para forzar que la barra de estado esté en blanco:

![](theme-images/info-plist.png "iOS Info.plist Entries")

o bien, edite el archivo **info. plist** correspondiente directamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Ahora, cuando se ejecuta la aplicación, la barra de navegación es verde y su texto es blanco (debido al Xamarin.Forms formato) *y* el texto de la barra de estado también está en blanco gracias a la configuración específica de iOS:

![](theme-images/status-white-sml.png "iOS Theming")

## <a name="uiappearance-api"></a>API de UIAppearance

La [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) se puede usar para establecer propiedades visuales en muchos controles de iOS *sin* tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

La adición de una sola línea de código al método **AppDelegate.CS** `FinishedLaunching` puede aplicar estilo a todos los controles de un tipo determinado mediante su `Appearance` propiedad. El código siguiente contiene dos ejemplos: aplicar estilos a la barra de pestañas y control de modificador de forma global:

**AppDelegate.CS** en el proyecto de iOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

De forma predeterminada, el icono de la barra de pestañas seleccionada en un[`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
sería azul:

![](theme-images/tabbar-default.png "Default iOS Tab Bar Icon in TabbedPage")

Para cambiar este comportamiento, establezca la `UITabBar.Appearance` propiedad:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Esto hace que la pestaña seleccionada sea verde:

![](theme-images/tabbar-custom.png "Green iOS Tab Bar Icon in TabbedPage")

El uso de esta API le permite personalizar la apariencia delXamarin.Forms
`TabbedPage`en iOS con muy poco código. Consulte la [receta personalización de pestañas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) para obtener más información sobre el uso de un representador personalizado para establecer una fuente específica para la pestaña.

### <a name="uiswitch"></a>UISwitch

El `Switch` control es otro ejemplo en el que se puede aplicar un estilo sencillo:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Estas dos capturas de pantalla muestran el `UISwitch` control predeterminado a la izquierda y la versión personalizada (valor `Appearance` ) de la derecha en el [ejemplo de todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo):

![](theme-images/switch-default.png "Color de UISwitch predeterminado") ![](theme-images/switch-custom.png "Color de UISwitch personalizado")

### <a name="other-controls"></a>Otros controles

Muchos controles de interfaz de usuario de iOS pueden tener sus colores predeterminados y otros atributos establecidos mediante la [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).

## <a name="related-links"></a>Vínculos relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar pestañas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
