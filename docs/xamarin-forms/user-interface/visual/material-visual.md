---
title: Material visual de Xamarin. Forms
description: El material visual de Xamarin. Forms se puede usar para crear aplicaciones de Xamarin. Forms que tengan un aspecto muy idéntico en iOS y Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/18/2019
ms.openlocfilehash: b447bd255270eb87f6139ddacadb9d31348ab7d7
ms.sourcegitcommit: db2cf1b54125499d5320636927484707810c8e27
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2019
ms.locfileid: "72850492"
---
# <a name="xamarinforms-material-visual"></a>Material visual de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

El [diseño de material](https://material.io) es un sistema de diseño de bien fundamentadas creado por Google que prescribe el tamaño, el color, el espaciado y otros aspectos de cómo deben buscarse y comportarse las vistas y los diseños.

El material visual de Xamarin. Forms se puede usar para aplicar reglas de diseño de materiales a aplicaciones de Xamarin. Forms, y crear aplicaciones que tengan una apariencia muy idéntica en iOS y Android. Cuando el material visual está habilitado, las vistas admitidas adoptan el mismo diseño multiplataforma, lo que crea una apariencia unificada.

[capturas de pantallas visuales de materiales![](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

El proceso para habilitar el visual material de Xamarin. Forms en la aplicación es:

1. Agregue el paquete de NuGet [Xamarin. Forms. visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) a los proyectos de la plataforma iOS y Android. Este paquete de NuGet ofrece representadores de diseño de material optimizados en iOS y Android. En iOS, el paquete proporciona la dependencia transitiva a [Xamarin. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que es un C# enlace a [los componentes de material de Google para iOS](https://material.io/develop/ios/). En Android, el paquete proporciona destinos de compilación para asegurarse de que el TargetFramework está correctamente configurado.
1. Inicialice el material visual en cada proyecto de plataforma. Para obtener más información, vea [Initialize material visual](#initialize-material-visual).
1. Cree controles visuales de materiales estableciendo la propiedad [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) en `Material` en cualquier página que deba adoptar las reglas de diseño del material. Para obtener más información, consulte [consumo de representadores de materiales](#apply-material-visual).
1. opta Personalizar los controles de materiales. Para obtener más información, vea [Personalizar controles de materiales](#customize-material-visual).

> [!IMPORTANT]
> En Android, el material visual requiere una versión mínima de 5,0 (API 21) o superior, y un TargetFramework de la versión 9,0 (API 28). Además, el proyecto de plataforma requiere las bibliotecas de compatibilidad con Android 28.0.0 o superior, y su tema debe heredar de un tema de componentes de materiales o seguir heredando de un tema AppCompat. Para obtener más información, consulte [Introducción a los componentes de materiales para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

El material visual actualmente admite los siguientes controles:

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
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

Después de instalar el paquete de NuGet de [Xamarin. Forms. visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , los representadores de materiales deben inicializarse en cada proyecto de plataforma.

En iOS, esto debe ocurrir en **AppDelegate.CS** al invocar el método `Xamarin.Forms.FormsMaterial.Init` *después* del método `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

En Android, esto debe ocurrir en **MainActivity.CS** al invocar el método `Xamarin.Forms.FormsMaterial.Init` *después* del método `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>Aplicar el material visual

Las aplicaciones pueden habilitar el aspecto visual de los materiales estableciendo la propiedad [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) en una página, diseño o vista, para `Material`:

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

Al establecer la propiedad `VisualElement.Visual` en `Material` se indica a la aplicación que use los representadores de material visual en lugar de los representadores predeterminados. La propiedad [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) se puede establecer en cualquier tipo que implemente `IVisual`, con la clase [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) que proporciona las siguientes propiedades de `IVisual`:

- `Default`: indica que la vista debe representarse mediante el representador predeterminado.
- `MatchParent`: indica que la vista debe utilizar el mismo representador que su elemento primario directo.
- `Material`: indica que la vista debe representarse mediante un representador de materiales.

> [!IMPORTANT]
> La propiedad [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) se define en la clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) , con vistas que heredan el valor de la propiedad `Visual` de sus elementos primarios. Por consiguiente, al establecer la propiedad `Visual` en un [`ContentPage`](xref:Xamarin.Forms.ContentPage) se garantiza que las vistas admitidas en la página utilizarán ese visual. Además, la propiedad `Visual` se puede invalidar en una vista.

Las siguientes capturas de pantallas muestran una interfaz de usuario que se representa mediante los representadores predeterminados:

[![Captura de pantalla de los representadores predeterminados, en iOS y Android](material-visual-images/default-renderers.png "Vistas con representadores predeterminados")](material-visual-images/default-renderers-large.png#lightbox)

Las siguientes capturas de pantallas muestran la misma interfaz de usuario que se representa mediante los representadores de materiales:

[![Captura de pantalla de representadores de materiales, en iOS y Android](material-visual-images/material-renderers.png "Vistas con representadores de materiales")](material-visual-images/material-renderers-large.png#lightbox)

Las principales diferencias visibles entre los representadores predeterminados y los representadores de materiales, que se muestran aquí, son que los representadores de materiales ponen en mayúsculas [`Button`](xref:Xamarin.Forms.Button) texto y redondean las esquinas de los bordes de [`Frame`](xref:Xamarin.Forms.Frame) . Sin embargo, los representadores de materiales usan controles nativos y, por lo tanto, puede haber diferencias en la interfaz de usuario entre plataformas para áreas como fuentes, sombras, colores y elevación.

> [!NOTE]
> Los componentes del diseño material se adhieren a las directrices de Google. Como resultado, los representadores de diseño de material se sesgan hacia ese tamaño y comportamiento. Cuando necesite un mayor control de los estilos o el comportamiento, puede crear su propio [efecto](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamiento](~/xamarin-forms/app-fundamentals/behaviors/index.md)o [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para obtener los detalles que necesita.

## <a name="customize-material-visual"></a>Personalización del material visual

El paquete de NuGet visual de material es una colección de representadores que obtienen los controles de Xamarin. Forms. La personalización de los controles visuales de materiales es idéntica a la personalización de los controles predeterminados.

Los efectos son la técnica recomendada cuando el objetivo es personalizar un control existente. Si existe un representador visual de material, es menos trabajo personalizar el control con un efecto que en la subclase del representador. Para obtener más información sobre los efectos, consulte [efectos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

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

La subclase de un representador de materiales es casi idéntica a los representadores que no son de materiales. Sin embargo, al exportar un representador que subclase un representador de materiales, debe proporcionar un tercer argumento al atributo `ExportRenderer` que especifica el tipo de `VisualMarker.MaterialVisual`:

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

En este ejemplo, el `ExportRendererAttribute` especifica que la clase `CustomMaterialProgressBarRenderer` se usará para representar la vista [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , con el tipo `IVisual` registrado como tercer argumento.

> [!NOTE]
> Un representador que especifica un tipo de `IVisual`, como parte de su `ExportRendererAttribute`, se usará para representar en las vistas, en lugar del representador predeterminado. En el momento de la selección del representador, se inspecciona la propiedad `Visual` de la vista y se incluye en el proceso de selección del representador.

Para obtener más información acerca de los representadores personalizados, vea [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Material visual (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Creación de un representador visual de Xamarin. Forms](create.md)
- [Efectos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
