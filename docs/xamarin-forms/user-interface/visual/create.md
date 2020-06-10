---
Título: "crear un Xamarin.Forms representador visual" Descripción: "cree Xamarin.Forms objetos visuales que se apliquen de forma selectiva a objetos VisualElement, sin tener que crear vistas de subclases Xamarin.Forms ".
MS. Prod: Xamarin ms. AssetID: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/12/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="create-a-xamarinforms-visual-renderer"></a>Creación de un Xamarin.Forms representador visual

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin.FormsVisual permite crear representadores y aplicarlos de forma selectiva a los [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos, sin tener que realizar vistas de subclases Xamarin.Forms . Se usará un representador que especifique un `IVisual` tipo, como parte de su `ExportRendererAttribute` , para representar en las vistas, en lugar del representador predeterminado. En el momento de la selección del representador, la `Visual` propiedad de la vista se inspecciona y se incluye en el proceso de selección del representador.

> [!IMPORTANT]
> Actualmente [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) no se puede cambiar la propiedad una vez representada la vista, pero esto cambiará en una versión futura.

El proceso para crear y utilizar un Xamarin.Forms representador visual es el siguiente:

1. Cree representadores de plataforma para la vista necesaria. Para obtener más información, vea [crear representadores](#create-platform-renderers).
1. Cree un tipo que se derive de `IVisual` . Para obtener más información, vea [crear un tipo IVisual](#create-an-ivisual-type).
1. Registre el `IVisual` tipo como parte del `ExportRendererAttribute` que decora los representadores. Para obtener más información, consulte [registrar el tipo IVisual](#register-the-ivisual-type).
1. Para usar el representador visual, establezca la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad de la vista en el `IVisual` nombre. Para obtener más información, vea [usar el representador visual](#consume-the-visual-renderer).
1. opta Registre un nombre para el `IVisual` tipo. Para obtener más información, vea [registrar un nombre para el tipo IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Creación de representadores de plataforma

Para obtener información sobre cómo crear una clase de representador, vea [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Sin embargo, tenga en cuenta que un Xamarin.Forms representador visual se aplica a una vista sin tener que subclaser la vista.

Las clases de representador que se describen aquí implementan un personalizado [`Button`](xref:Xamarin.Forms.Button) que muestra el texto con una sombra.

### <a name="ios"></a>iOS

En el ejemplo de código siguiente se muestra el representador de botón para iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

En el ejemplo de código siguiente se muestra el representador de botón para Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Creación de un tipo de IVisual

En la biblioteca multiplataforma, cree un tipo que derive de `IVisual` :

```csharp
public class CustomVisual : IVisual
{
}
```

Después, el `CustomVisual` tipo se puede registrar en las clases de representador, lo [`Button`](xref:Xamarin.Forms.Button) que permite a los objetos participar en el uso de los representadores.

## <a name="register-the-ivisual-type"></a>Registrar el tipo de IVisual

En los proyectos de la plataforma, agregue `ExportRendererAttribute` en el nivel de ensamblado:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

En este ejemplo del proyecto de plataforma iOS, el `ExportRendererAttribute` especifica que la `CustomButtonRenderer` clase se usará para representar objetos de consumo [`Button`](xref:Xamarin.Forms.Button) , con el `IVisual` tipo registrado como tercer argumento. Se usará un representador que especifique un `IVisual` tipo, como parte de su `ExportRendererAttribute` , para representar en las vistas, en lugar del representador predeterminado.

## <a name="consume-the-visual-renderer"></a>Usar el representador visual

Un [`Button`](xref:Xamarin.Forms.Button) objeto puede optar por usar las clases de representador estableciendo su [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad en `Custom` :

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> En XAML, un convertidor de tipos quita la necesidad de incluir el sufijo "visual" en el [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) valor de propiedad. Sin embargo, también se puede especificar el nombre de tipo completo.

El código de C# equivalente es el siguiente:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

En el momento de la selección del representador, la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad de [`Button`](xref:Xamarin.Forms.Button) se inspecciona y se incluye en el proceso de selección del representador. Si no se encuentra un representador, se Xamarin.Forms usará el representador predeterminado.

Las capturas de pantallas siguientes muestran el representado [`Button`](xref:Xamarin.Forms.Button) , que muestra su texto con una sombra:

[![Captura de pantalla del botón personalizado con texto de sombra, en iOS y Android](material-visual-images/custom-button.png "Botón con texto de sombra")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registro de un nombre para el tipo IVisual

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)Se puede utilizar para registrar opcionalmente un nombre diferente para el `IVisual` tipo. Este enfoque se puede usar para resolver conflictos de nomenclatura entre diferentes bibliotecas visuales o en situaciones en las que solo desea hacer referencia a un visual con un nombre diferente al nombre de su tipo.

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)Debe definirse en el nivel de ensamblado en la biblioteca multiplataforma o en el proyecto de plataforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

`IVisual`A continuación, el tipo se puede consumir a través de su nombre registrado:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Al consumir un visual a través de su nombre registrado, se debe incluir cualquier sufijo "visual".

## <a name="related-links"></a>Vínculos relacionados

- [Material visual (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Objeto visual de material de Xamarin.Forms](material-visual.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
