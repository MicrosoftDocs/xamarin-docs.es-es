---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 50e28291d72550264e3806c0911f59a57c6d8bf0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136336"
---
# <a name="customizing-a-contentpage"></a>Personalización de una página de contenido

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage)

_Un ContentPage es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla. En este artículo se muestra cómo crear un representador personalizado para la página ContentPage, lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma._

Todos los controles de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa un [`ContentPage`](xref:Xamarin.Forms.ContentPage), se crea en iOS la instancia de la clase `PageRenderer`, que a su vez crea una instancia del control `UIViewController` nativo. En la plataforma Android, la clase `PageRenderer` crea una instancia de un control `ViewGroup`. En la Plataforma universal de Windows (UWP), la clase `PageRenderer` crea una instancia de un control `FrameworkElement`. Para obtener más información sobre el representador y las clases de control nativo a las que se asignan los controles de Xamarin.Forms, vea [Clases base y controles nativos del representador](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama muestra la relación entre la clase [`ContentPage`](xref:Xamarin.Forms.ContentPage) y los controles nativos correspondientes que la implementan:

![](contentpage-images/contentpage-classes.png "Relationship Between ContentPage Class and Implementing Native Controls")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma creando un representador personalizado para una [`ContentPage`](xref:Xamarin.Forms.ContentPage) en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#Creating_the_Xamarin.Forms_Page) una página de Xamarin.Forms.
1. [Consuma](#Consuming_the_Xamarin.Forms_Page) la página de Xamarin.Forms.
1. [Cree](#Creating_the_Page_Renderer_on_each_Platform) el representador personalizado para la página en cada plataforma.

Ahora se analizará en detalle cada elemento, para implementar un `CameraPage` que proporciona una fuente de la cámara en vivo y la capacidad de capturar una foto.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Creación de la página Xamarin.Forms

Puede agregarse una [`ContentPage`](xref:Xamarin.Forms.ContentPage) sin modificar al proyecto de Xamarin.Forms compartido, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

De forma similar, el archivo de código subyacente para el [`ContentPage`](xref:Xamarin.Forms.ContentPage) también debe permanecer sin modificaciones, como se muestra en el siguiente ejemplo de código:

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

En el siguiente ejemplo de código se muestra cómo puede crearse la página en C#:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Una instancia de la `CameraPage` se usará para mostrar la fuente de la cámara en directo en cada plataforma. La personalización del control se llevará a cabo en el representador personalizado, por lo que no se requiere ninguna implementación adicional en la clase `CameraPage`.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Consumo de la página de Xamarin.Forms

La aplicación de Xamarin.Forms debe mostrar la `CameraPage` vacía. Esto se produce cuando se pulsa un botón en la instancia de `MainPage`, lo que a su vez ejecuta el método `OnTakePhotoButtonClicked`, como se muestra en el siguiente ejemplo de código:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Este código simplemente se desplaza a la `CameraPage`, en la que representadores personalizados personalizarán el aspecto de la página en cada plataforma.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Creación del representador de página en cada plataforma

El proceso para crear la clase del representador personalizado es el siguiente:

1. Se crea una subclase de la clase `PageRenderer`.
1. Invalide el método `OnElementChanged` que representa la página nativa y escriba lógica para personalizar la página. Se llama al método `OnElementChanged` cuando se crea el correspondiente control de Xamarin.Forms.
1. Agregue un atributo `ExportRenderer` a la clase de representador de página para especificar que se usará para representar la página de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Proporcionar un representador de página en cada proyecto de la plataforma es un paso opcional. Si no hay un representador de página registrado, se usa el representador predeterminado de la página.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](contentpage-images/solution-structure.png "CameraPage Custom Renderer Project Responsibilities")

Las clases `CameraPageRenderer` del representador específico de la plataforma, que se derivan de la clase `PageRenderer` para esa plataforma, representan la instancia `CameraPage`. Esto da como resultado que cada instancia de `CameraPage` se represente con una fuente de la cámara en directo, como se muestra en las siguientes capturas de pantalla:

![](contentpage-images/screenshots.png "CameraPage on each Platform")

La clase `PageRenderer` expone el método `OnElementChanged`, al que se llama cuando se crea la página de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un parámetro `ElementChangedEventArgs` que contiene propiedades `OldElement` y `NewElement`. Estas propiedades representan el elemento de Xamarin.Forms al que *estaba* asociado el representador y el elemento de Xamarin.Forms al que *está* asociado el representador, respectivamente. En la aplicación de ejemplo, la propiedad `OldElement` es `null` y la propiedad `NewElement` contiene una referencia a la instancia de `CameraPage`.

El lugar para realizar la personalización de páginas nativas es una versión reemplazada del método `OnElementChanged` en la clase `CameraPageRenderer`. Se puede obtener una referencia a la instancia de la página de Xamarin.Forms que se representa mediante la propiedad `Element`.

Cada clase de representador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo de la página de Xamarin.Forms que se representa y el nombre de tipo del representador personalizado. El prefijo `assembly` para el atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se describe la implementación del representador personalizado de `CameraPageRenderer` para cada plataforma.

### <a name="creating-the-page-renderer-on-ios"></a>Creación del representador de página en iOS

El siguiente ejemplo de código muestra el representador de página para la plataforma iOS:

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada al método `OnElementChanged` de la clase base crea una instancia de un control `UIViewController` de iOS. Solo se procesa la secuencia en directo de cámara si el representador aún no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado.

Después, se personaliza la página mediante una serie de métodos que usan la API de `AVCapture` para proporcionar la secuencia en directo desde la cámara y la capacidad para capturar una foto.

### <a name="creating-the-page-renderer-on-android"></a>Creación del representador de página en Android

En el ejemplo de código siguiente se muestra el representador de página para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

La llamada al método de la clase base `OnElementChanged` crea una instancia de un control `ViewGroup` de Android, que es un grupo de vistas. Solo se procesa la secuencia en directo de cámara si el representador aún no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado.

Después se personaliza la página mediante la invocación de una serie de métodos que usan la API de `Camera` para proporcionar la secuencia en directo desde la cámara y la capacidad de capturar una foto, antes de que se invoque al método `AddView` para agregar la interfaz de usuario de la transmisión de cámara en vivo al `ViewGroup`. Tenga en cuenta que en Android también es necesario reemplazar el método `OnLayout` para realizar operaciones de medida y de diseño en la vista. Para obtener más información, vea el [ejemplo de representador de ContentPage](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage).

### <a name="creating-the-page-renderer-on-uwp"></a>Creación del representador de página en UWP

En el siguiente ejemplo de código se muestra el representador de página para UWP:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

La llamada al método `OnElementChanged` de la clase base crea una instancia de un control `FrameworkElement`, en el que se representa la página. Solo se procesa la secuencia en directo de cámara si el representador aún no está unido a un elemento existente de Xamarin.Forms, y siempre que exista una instancia de la página que se representa mediante el representador personalizado. Después se personaliza la página mediante la invocación de una serie de métodos que usan la API de `MediaCapture` para proporcionar la secuencia en directo desde la cámara y la capacidad de capturar una foto, antes de que se agregue la página personalizada a la colección `Children` para mostrarla.

Al implementar un representador personalizado que derive de `PageRenderer` en UWP, el método `ArrangeOverride` también debe implementarse para organizar los controles de página, ya que el representador de base no sabe qué hacer con ellos. En caso contrario, da como resultado una página en blanco. Por lo tanto, en este ejemplo el método `ArrangeOverride` llama al método `Arrange` en la instancia de `Page`.

> [!NOTE]
> Es importante detener y eliminar los objetos que proporcionan acceso a la cámara en una aplicación de UWP. Si no lo hace puede interferir con otras aplicaciones que intentan acceder a la cámara del dispositivo. Para obtener más información, vea [Display the camera preview](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access) (Mostar la vista previa de la cámara).

## <a name="summary"></a>Resumen

En este artículo se mostró cómo crear un representador personalizado para la página [`ContentPage`](xref:Xamarin.Forms.ContentPage), lo que permite que los desarrolladores reemplacen la representación nativa de forma predeterminada con su propia personalización específica de la plataforma. Un `ContentPage` es un elemento visual que muestra una vista única y ocupa la mayor parte de la pantalla.

## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererContentPage (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-contentpage) (CustomRendererContentPage [ejemplo])
