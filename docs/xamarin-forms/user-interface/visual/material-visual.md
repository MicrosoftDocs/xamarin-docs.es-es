---
title: Xamarin.FormsMaterial visual
description: Xamarin.FormsEl material visual se puede usar para crear Xamarin.Forms aplicaciones que tengan un aspecto muy idéntico en iOS y Android.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bba7d77d8cf565b1b2db2c1324e171389c5d0280
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127187"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.FormsMaterial visual

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

El [diseño de material](https://material.io) es un sistema de diseño de bien fundamentadas creado por Google que prescribe el tamaño, el color, el espaciado y otros aspectos de cómo deben buscarse y comportarse las vistas y los diseños.

Xamarin.FormsEl material visual se puede usar para aplicar reglas de diseño de materiales a Xamarin.Forms aplicaciones y crear aplicaciones que tengan una apariencia muy idéntica en iOS y Android. Cuando el material visual está habilitado, las vistas admitidas adoptan el mismo diseño multiplataforma, lo que crea una apariencia unificada.

[![Capturas de pantallas de material visual](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

El proceso para habilitar el Xamarin.Forms contenido visual en la aplicación es el siguiente:

1. Agregue el [ Xamarin.Forms . Paquete NuGet de visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) en los proyectos de la plataforma iOS y Android. Este paquete de NuGet ofrece representadores de diseño de material optimizados en iOS y Android. En iOS, el paquete proporciona la dependencia transitiva a [Xamarin. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que es un enlace de C# a [los componentes de material de Google para iOS](https://material.io/develop/ios/). En Android, el paquete proporciona destinos de compilación para asegurarse de que el TargetFramework está correctamente configurado.
1. Inicialice el material visual en cada proyecto de plataforma. Para obtener más información, vea [Initialize material visual](#initialize-material-visual).
1. Cree controles visuales de materiales estableciendo la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad en `Material` en cualquier página que deba adoptar las reglas de diseño del material. Para obtener más información, consulte [consumo de representadores de materiales](#apply-material-visual).
1. opta Personalizar los controles de materiales. Para obtener más información, vea [Personalizar controles de materiales](#customize-material-visual).

> [!IMPORTANT]
> En Android, el material visual requiere una versión mínima de 5,0 (API 21) o superior, y un TargetFramework de la versión 9,0 (API 28). Además, el proyecto de plataforma requiere las bibliotecas de compatibilidad con Android 28.0.0 o superior, y su tema debe heredar de un tema de componentes de materiales o seguir heredando de un tema AppCompat. Para obtener más información, consulte [Introducción a los componentes de materiales para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

El material visual actualmente admite los siguientes controles:

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

Los representadores de materiales tienen en cuenta los controles de materiales, que aplican las reglas de diseño de materiales. Funcionalmente, los representadores de material no son diferentes a los representadores predeterminados. Para obtener más información, vea [personalizar el material visual](#customize-material-visual).

## <a name="initialize-material-visual"></a>Inicializar el material visual

Después de instalar el [ Xamarin.Forms . Paquete NuGet de visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , los representadores de materiales deben inicializarse en cada proyecto de plataforma.

En iOS, esto debe ocurrir en **AppDelegate.CS** invocando el `Xamarin.Forms.FormsMaterial.Init` método *después* del `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

En Android, esto debe ocurrir en **MainActivity.CS** invocando el `Xamarin.Forms.FormsMaterial.Init` método *después* del `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>Aplicar el material visual

Las aplicaciones pueden habilitar el material visual estableciendo la [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad en una página, diseño o vista, para `Material` :

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

Al establecer la `VisualElement.Visual` propiedad en `Material` , se indica a la aplicación que use los representadores de material visual en lugar de los representadores predeterminados. La [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad se puede establecer en cualquier tipo que implemente `IVisual` , con la [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) clase que proporciona las `IVisual` propiedades siguientes:

- `Default`: indica que la vista debe representarse mediante el representador predeterminado.
- `MatchParent`: indica que la vista debe utilizar el mismo representador que su elemento primario directo.
- `Material`: indica que la vista debe representarse mediante un representador de materiales.

> [!IMPORTANT]
> La [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propiedad se define en la [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase, con vistas que heredan el `Visual` valor de propiedad de sus elementos primarios. Por consiguiente, si `Visual` se establece la propiedad en, [`ContentPage`](xref:Xamarin.Forms.ContentPage) se garantiza que cualquier vista admitida en la página utilizará ese visual. Además, la `Visual` propiedad se puede invalidar en una vista.

Las siguientes capturas de pantallas muestran una interfaz de usuario que se representa mediante los representadores predeterminados:

[![Captura de pantalla de los representadores predeterminados, en iOS y Android](material-visual-images/default-renderers.png "Vistas con representadores predeterminados")](material-visual-images/default-renderers-large.png#lightbox)

Las siguientes capturas de pantallas muestran la misma interfaz de usuario que se representa mediante los representadores de materiales:

[![Captura de pantalla de representadores de materiales, en iOS y Android](material-visual-images/material-renderers.png "Vistas con representadores de materiales")](material-visual-images/material-renderers-large.png#lightbox)

Las principales diferencias visibles entre los representadores predeterminados y los representadores de materiales, que se muestran aquí, son que los representadores de materiales ponen en mayúsculas [`Button`](xref:Xamarin.Forms.Button) el texto y redondean las esquinas de los [`Frame`](xref:Xamarin.Forms.Frame) bordes. Sin embargo, los representadores de materiales usan controles nativos y, por lo tanto, puede haber diferencias en la interfaz de usuario entre plataformas para áreas como fuentes, sombras, colores y elevación.

> [!NOTE]
> Los componentes del diseño material se adhieren a las directrices de Google. Como resultado, los representadores de diseño de material se sesgan hacia ese tamaño y comportamiento. Cuando necesite un mayor control de los estilos o el comportamiento, puede crear su propio [efecto](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamiento](~/xamarin-forms/app-fundamentals/behaviors/index.md)o [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para obtener los detalles que necesita.

## <a name="customize-material-visual"></a>Personalización del material visual

El paquete de NuGet visual de material es una colección de representadores que obtienen los Xamarin.Forms controles. La personalización de los controles visuales de materiales es idéntica a la personalización de los controles predeterminados.

Los efectos son la técnica recomendada cuando el objetivo es personalizar un control existente. Si existe un representador visual de material, es menos trabajo personalizar el control con un efecto que en la subclase del representador. Para obtener más información sobre los efectos, vea [ Xamarin.Forms efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

Los representadores personalizados son la técnica recomendada cuando no existe un representador de materiales. Las siguientes clases de representador se incluyen con el material visual:

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

La subclase de un representador de materiales es casi idéntica a los representadores que no son de materiales. Sin embargo, al exportar un representador que subclase un representador de materiales, debe proporcionar un tercer argumento al `ExportRenderer` atributo que especifica el `VisualMarker.MaterialVisual` tipo:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

En este ejemplo, `ExportRendererAttribute` especifica que la `CustomMaterialProgressBarRenderer` clase se utilizará para representar la [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) vista, con el `IVisual` tipo registrado como tercer argumento.

> [!NOTE]
> Se usará un representador que especifique un `IVisual` tipo, como parte de su `ExportRendererAttribute` , para representar en las vistas, en lugar del representador predeterminado. En el momento de la selección del representador, la `Visual` propiedad de la vista se inspecciona y se incluye en el proceso de selección del representador.

Para obtener más información acerca de los representadores personalizados, vea [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Material visual (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Creación de un Xamarin.Forms representador visual](create.md)
- [Xamarin.FormsEfectos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
