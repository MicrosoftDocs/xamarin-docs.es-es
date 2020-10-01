---
title: Incorporación de reconocedores de gesto de arrastrar y colocar
description: En este artículo se explica cómo reconocer los gestos de arrastrar y colocar con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4CB2F270-908A-4A89-B852-70BC04066E8C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d3eb7edbb24c7e28ee375e1de85f6a7597ec63ac
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561020"
---
# <a name="add-drag-and-drop-gesture-recognizers"></a>Incorporación de reconocedores de gesto de arrastrar y colocar

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)

Un gesto de arrastrar y colocar permite arrastrar elementos y sus paquetes de datos asociados desde una ubicación en pantalla a otra ubicación mediante un gesto continuo. Arrastrar y colocar puede tener lugar en una sola aplicación o puede iniciarse en una aplicación y finalizar en otra.

> [!IMPORTANT]
> El reconocedor de gesto de arrastrar y colocar Xamarin.Forms es actualmente experimental y solo se puede usar estableciendo la marca `DragAndDrop_Experimental`. Para más información, vea [Marcas experimentales](~/xamarin-forms/internals/experimental-flags.md).
>
> El reconocimiento de los gestos de arrastrar y colocar se admite en iOS, Android y la Plataforma universal de Windows (UWP). Sin embargo, en iOS se requiere una plataforma mínima de iOS 11.

El *origen de arrastre*, que es el elemento en el que se inicia el gesto de arrastrar, puede proporcionar los datos que se van a transferir rellenando un objeto de paquete de datos. Cuando el origen de arrastre se libera, se produce la colocación. Es entonces cuando el *destino de colocación*, que es el elemento bajo el origen de arrastre, procesa el paquete de datos.

El proceso para habilitar la función de arrastrar y colocar en una aplicación es el siguiente:

1. Habilite la función de arrastrar en un elemento agregando un objeto `DragGestureRecognizer` a su colección `GestureRecognizers` y estableciendo la propiedad `DragGestureRecognizer.CanDrag` en `true`. Para más información, consulte [Habilitación del arrastre](#enable-drag).
1. [opcional] Cree un paquete de datos. Xamarin.Forms rellena automáticamente el paquete de datos para los controles de imagen y texto pero, para el resto de contenido, deberá crear su propio paquete de datos. Para obtener más información, consulte [Creación de un paquete de datos](#build-a-data-package).
1. Habilite la función de colocar en un elemento agregando un objeto `DropGestureRecognizer` a su colección `GestureRecognizers` y estableciendo la propiedad `DropGestureRecognizer.AllowDrop` en `true`. Para más información, consulte [Habilitación de la colocación](#enable-drop).
1. [opcional] Controle el evento `DropGestureRecognizer.DragOver` para indicar el tipo de operación que el destino de colocación permite. Para más información, consulte [Controlar el evento DragOver](#handle-the-dragover-event).
1. [opcional] Procese el paquete de datos para recibir el contenido colocado. Xamarin.Forms recuperará automáticamente los datos de texto e imagen del paquete de datos pero, para el resto de contenido, deberá procesar el paquete de datos. Para más información, consulte [Proceso del paquete de datos](#process-the-data-package).

> [!NOTE]
> Actualmente no se admite el arrastre de elementos hacia y desde [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="enable-drag"></a>Habilitación del arrastre

En Xamarin.Forms, la clase `DragGestureRecognizer` proporciona el reconocimiento del gesto de arrastrar. Esta clase define las propiedades siguientes:

- `CanDrag`, de tipo `bool`, que indica si el elemento al que está asociado el reconocedor de gestos puede ser un origen de arrastre. El valor predeterminado de esta propiedad es `false`.
- `DragStartingCommand`, de tipo `ICommand`, que se ejecuta cuando un gesto de arrastrar se reconoce por primera vez.
- `DragStartingCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `DragStartingCommand`.
- `DropCompletedCommmand`, de tipo `ICommand`, que se ejecuta cuando el origen de arrastre se coloca.
- `DropCompletedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `DropCompletedCommand`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

La clase `DragGestureRecognizer` también define los eventos `DragStarting` y `DropCompleted`. Cuando un objeto `DragGestureRecognizer` detecta un gesto de arrastrar, ejecuta `DragStartingCommand` e invoca el evento `DragStarting`. Después, cuando el objeto `DragGestureRecognizer` detecta la finalización de un gesto de colocar, ejecuta `DropCompletedCommand` e invoca el evento `DropCompleted`.

El objeto `DragStartingEventArgs` que acompaña al evento `DragStarting` define las siguientes propiedades:

- `Handled`, de tipo `bool`, indica si el controlador de eventos ha controlado el evento o si Xamarin.Forms debe continuar su propio procesamiento.
- `Cancel`, de tipo `bool`, indica si se debe cancelar el evento.
- `Data`, de tipo `DataPackage`, indica el paquete de datos que acompaña al origen de arrastre. Se trata de una propiedad de solo lectura.

El objeto `DropCompletedEventArgs` que acompaña al evento `DropCompleted` tiene una propiedad `DropResult` de solo lectura, de tipo `DataPackageOperation`. Para más información sobre la enumeración `DataPackageOperation`, consulte [Controlar el evento DragOver](#handle-the-dragover-event).

En el ejemplo de XAML siguiente se muestra `DragGestureRecognizer`, que se adjunta a [`Image`](xref:Xamarin.Forms.Image):

```xaml
<Image Source="monkeyface.png">
    <Image.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True" />
    </Image.GestureRecognizers>
</Image>
```

En este ejemplo, se puede iniciar un gesto de arrastrar en [`Image`](xref:Xamarin.Forms.Image).

> [!TIP]
> En iOS, Android y UWP, un gesto de arrastrar se inicia con una pulsación larga seguida de un arrastre.

## <a name="build-a-data-package"></a>Creación de un paquete de datos

Xamarin.Forms creará automáticamente un paquete de datos, cuando se inicie una arrastre, para los siguientes controles:

- Controles de texto. Los valores de texto se pueden arrastrar desde los objetos [`CheckBox`](xref:Xamarin.Forms.CheckBox), [`DatePicker`](xref:Xamarin.Forms.DatePicker), [`Editor`](xref:Xamarin.Forms.Editor), [`Entry`](xref:Xamarin.Forms.Entry), [`Label`](xref:Xamarin.Forms.Label), [`RadioButton`](xref:Xamarin.Forms.RadioButton), [`Switch`](xref:Xamarin.Forms.Switch) y [`TimePicker`](xref:Xamarin.Forms.TimePicker).
- Controles de imagen. Las imágenes se pueden arrastrar desde los controles [`Button`](xref:Xamarin.Forms.Button), [`Image`](xref:Xamarin.Forms.Image) y [`ImageButton`](xref:Xamarin.Forms.ImageButton).

En la tabla siguiente se muestran las propiedades que se leen, y cualquier conversión que se intenta, cuando se inicia una operación de arrastrar en un control de texto:

| Control | Propiedad | Conversión |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `bool` convertido en `string`. |
| `DatePicker` | `Date` | `DateTime` convertido en `string`. |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `bool` convertido en `string`. |
| `Switch` | `IsToggled` | `bool` convertido en `string`. |
| `TimePicker` | `Time` | `TimeSpan` convertido en `string`. |

En el caso de contenido que no sea texto ni imágenes, deberá crear un paquete de datos usted mismo.

La clase `DataPackage` representa los paquetes de datos, que define las siguientes propiedades:

- `Properties`, de tipo `DataPackagePropertySet`, que es una colección de propiedades que comprenden los datos contenidos en `DataPackage`. Esta propiedad es de solo lectura.
- `Image`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), que es la imagen contenida en `DataPackage`.
- `Text`, de tipo `string`, que es el texto contenido en `DataPackage`.
- `View`, de tipo `DataPackageView`, que es una versión de solo lectura de `DataPackage`.

La clase `DataPackagePropertySet` representa una bolsa de propiedades almacenada en `Dictionary<string,object>`. Para información sobre la clase `DataPackageView`, vea [Proceso del paquete de datos](#process-the-data-package).

### <a name="store-image-or-text-data"></a>Almacenamiento de datos de imagen o texto

Los datos de imagen o texto se pueden asociar a un origen de arrastre mediante el almacenamiento de los datos en la propiedad `DataPackage.Image` o `DataPackage.Text`. Esto puede realizarse en el controlador del evento `DragStarting`.

En el ejemplo de XAML siguiente se muestra `DragGestureRecognizer`, que registra un controlador para el evento `DragStarting`:

```xaml
<Path Stroke="Black"
      StrokeThickness="4">
    <Path.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Path.GestureRecognizers>
    <Path.Data>
        <!-- PathGeometry goes here -->
    </Path.Data>
</Path>
```

En este ejemplo, `DragGestureRecognizer` se adjunta a un objeto `Path`. El evento `DragStarting` se desencadena cuando se detecta un gesto de arrastrar en `Path`, que ejecuta el controlador de eventos `OnDragStarting`:

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    e.Data.Text = "My text data goes here";
}
```

El objeto `DragStartingEventArgs` que acompaña al evento `DragStarting` tiene una propiedad `Data`, de tipo `DataPackage`. En este ejemplo, la propiedad `Text` del objeto `DataPackage` se establece en `string`. Después, se puede acceder a `DataPackage` en la colocación para recuperar `string`.

### <a name="store-data-in-the-property-bag"></a>Almacenamiento de datos en la bolsa de propiedades

Todos los datos, incluidas las imágenes y el texto, se pueden asociar a un origen de arrastre almacenando los datos en la colección `DataPackage.Properties`. Esto puede realizarse en el controlador del evento `DragStarting`.

En el ejemplo de XAML siguiente se muestra `DragGestureRecognizer`, que registra un controlador para el evento `DragStarting`:

```xaml
<Rectangle Stroke="Red"
           Fill="DarkBlue"
           StrokeThickness="4"
           HeightRequest="200"
           WidthRequest="200">
    <Rectangle.GestureRecognizers>
        <DragGestureRecognizer CanDrag="True"
                               DragStarting="OnDragStarting" />
    </Rectangle.GestureRecognizers>
</Rectangle>
```

En este ejemplo, `DragGestureRecognizer` se adjunta a un objeto `Rectangle`. El evento `DragStarting` se desencadena cuando se detecta un gesto de arrastrar en `Rectangle`, que ejecuta el controlador de eventos `OnDragStarting`:

```csharp
void OnDragStarting(object sender, DragStartingEventArgs e)
{
    Shape shape = (sender as Element).Parent as Shape;
    e.Data.Properties.Add("Square", new Square(shape.Width, shape.Height));
}
```

El objeto `DragStartingEventArgs` que acompaña al evento `DragStarting` tiene una propiedad `Data`, de tipo `DataPackage`. La colección `Properties` del objeto `DataPackage`, que es una colección `Dictionary<string, object>`, puede modificarse para almacenar los datos necesarios. En este ejemplo, el diccionario `Properties` se modifica para almacenar un objeto `Square`, que representa el tamaño de `Rectangle`, con respecto a una clave "Square".

## <a name="enable-drop"></a>Habilitación de la colocación

En Xamarin.Forms, la clase `DropGestureRecognizer` proporciona el reconocimiento del gesto de colocar. Esta clase define las propiedades siguientes:

- `AllowDrop`, de tipo `bool`, que indica si el elemento al que está asociado el reconocedor de gestos puede ser un destino de colocación. El valor predeterminado de esta propiedad es `false`.
- `DragOverCommand`, de tipo `ICommand`, que se ejecuta cuando el origen de arrastre se arrastra sobre el destino de colocación.
- `DragOverCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `DragOverCommand`.
- `DropCommand`, de tipo `ICommand`, que se ejecuta cuando el origen de arrastre se coloca sobre el destino de colocación.
- `DropCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `DropCommand`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

La clase `DropGestureRecognizer` también define los eventos `DragOver` y `Drop`. Cuando `DropGestureRecognizer` reconoce un origen de arrastre sobre el destino de colocación, ejecuta `DragOverCommand` e invoca el evento `DragOver`. Después, cuando `DropGestureRecognizer` reconoce un gesto de colocación sobre el destino de colocación, ejecuta `DropCommand` e invoca el evento `Drop`.

La clase `DragEventArgs` que acompaña al evento `DragOver` define las siguientes propiedades:

- `Data`, de tipo `DataPackage`, que contiene los datos asociados al origen de arrastre. Esta propiedad es de solo lectura.
- `AcceptedOperation`, de tipo `DataPackageOperation`, que especifica las operaciones que el destino de colocación permite.

Para más información sobre la enumeración `DataPackageOperation`, consulte [Controlar el evento DragOver](#handle-the-dragover-event).

La clase `DropEventArgs` que acompaña al evento `Drop` define las siguientes propiedades:

- `Data`, de tipo `DataPackageView`, que es una versión de solo lectura del paquete de datos.
- `Handled`, de tipo `bool`, indica si el controlador de eventos ha controlado el evento o si Xamarin.Forms debe continuar su propio procesamiento.

En el ejemplo de XAML siguiente se muestra `DropGestureRecognizer`, que se adjunta a [`Image`](xref:Xamarin.Forms.Image):

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True" />
    </Image.GestureRecognizers>
</Image>
```

En este ejemplo, cuando un origen de arrastre se coloca en el destino de colocación [`Image`](xref:Xamarin.Forms.Image), el origen de arrastre se copia en el destino de colocación, siempre que dicho origen de arrastre sea una clase [`ImageSource`](xref:Xamarin.Forms.ImageSource). Esto se produce porque Xamarin.Forms copia automáticamente las imágenes arrastradas, y el texto, en destinos de colocación compatibles.

## <a name="handle-the-dragover-event"></a>Controlar el evento DragOver

El evento `DropGestureRecognizer.DragOver` se puede controlar opcionalmente para indicar qué tipo de operaciones permite el destino de colocación. Esto se puede lograr estableciendo la propiedad `AcceptedOperation`, de tipo `DataPackageOperation`, del objeto `DragEventArgs` que acompaña al evento `DragOver`.

La enumeración `DataPackageOperation` define los miembros siguientes:

- `None`, indica que no se realizará ninguna acción.
- `Copy`, indica que el contenido de origen de arrastre se copiará en el destino de colocación.

> [!IMPORTANT]
> Cuando se crea un objeto de `DragEventArgs`, el valor predeterminado de la propiedad `AcceptedOperation` es `DataPackageOperation.Copy`.

En el ejemplo de XAML siguiente se muestra `DropGestureRecognizer`, que registra un controlador para el evento `DragOver`:

```xaml
<Image BackgroundColor="Silver"
       HeightRequest="300"
       WidthRequest="250">
    <Image.GestureRecognizers>
        <DropGestureRecognizer AllowDrop="True"
                               DragOver="OnDragOver" />
    </Image.GestureRecognizers>
</Image>
```

En este ejemplo, `DropGestureRecognizer` se adjunta a un objeto [`Image`](xref:Xamarin.Forms.Image). El evento `DragOver` se desencadena cuando se arrastra un origen de arrastre sobre el destino de colocación, pero sin colocarse, lo que ejecuta el controlador de eventos `OnDragOver`:

```csharp
void OnDragOver(object sender, DragEventArgs e)
{
    e.AcceptedOperation = DataPackageOperation.None;
}
```

En este ejemplo, la propiedad `AcceptedOperation` del objeto `DragEventArgs` se establece en `DataPackageOperation.None`. Esto garantiza que no se realiza ninguna acción cuando se coloca un origen de arrastre sobre el destino de colocación.

## <a name="process-the-data-package"></a>Proceso del paquete de datos

El evento `Drop` se desencadena cuando se libera un origen de arrastre sobre un destino de colocación. Cuando esto ocurre, Xamarin.Forms intentará recuperar automáticamente datos del paquete de datos cuando se coloca un origen de arrastre en los controles siguientes:

- Controles de texto. Los valores de texto se pueden colocar en los objetos [`CheckBox`](xref:Xamarin.Forms.CheckBox), [`DatePicker`](xref:Xamarin.Forms.DatePicker), [`Editor`](xref:Xamarin.Forms.Editor), [`Entry`](xref:Xamarin.Forms.Entry), [`Label`](xref:Xamarin.Forms.Label), [`RadioButton`](xref:Xamarin.Forms.RadioButton), [`Switch`](xref:Xamarin.Forms.Switch) y [`TimePicker`](xref:Xamarin.Forms.TimePicker).
- Controles de imagen. Las imágenes se pueden colocar en los controles [`Button`](xref:Xamarin.Forms.Button), [`Image`](xref:Xamarin.Forms.Image)y [`ImageButton`](xref:Xamarin.Forms.ImageButton).

En la tabla siguiente se muestran las propiedades que se establecen, y cualquier conversión que se intenta, cuando un origen de arrastre basado en texto se coloca en un control de texto:

| Control | Propiedad | Conversión |
| --- | --- | --- |
| `CheckBox` | `IsChecked` | `string` se convierte en `bool`. |
| `DatePicker` | `Date` | `string` se convierte en `DateTime`. |
| `Editor` | `Text` ||
| `Entry` | `Text` ||
| `Label` | `Text` ||
| `RadioButton` | `IsChecked` | `string` se convierte en `bool`. |
| `Switch` | `IsToggled` | `string` se convierte en `bool`. |
| `TimePicker` | `Time` | `string` se convierte en `TimeSpan`. |

En el caso de contenido que no sea texto ni imágenes, deberá procesar el paquete de datos usted mismo.

La clase `DropEventArgs` que acompaña al evento `Drop` define una propiedad `Data`, de tipo `DataPackageView`. Esta propiedad representa una versión de solo lectura del paquete de datos.

### <a name="retrieve-image-or-text-data"></a>Recuperación de datos de imagen o texto

Los datos de imagen o texto se pueden recuperar de un paquete de datos en el controlador para el evento `Drop`, mediante métodos definidos en la clase `DataPackageView`.

La clase `DataPackageView` incluye los métodos `GetImageAsync` y `GetTextAsync`. El método `GetImageAsync` recupera una imagen del paquete de datos, que estaba almacenada en la propiedad `DataPackage.Image`, y devuelve `Task<ImageSource>`. De forma similar, el método `GetTextAsync` recupera texto del paquete de datos, que estaba almacenada en la propiedad `DataPackage.Text`, y devuelve `Task<string>`.

En el ejemplo siguiente se muestra un controlador de eventos `Drop` que recupera texto del paquete de datos para `Path`:

```csharp
async void OnDrop(object sender, DropEventArgs e)
{
    string text = await e.Data.GetTextAsync();

    // Perform logic to take action based on the text value.
}
```

En este ejemplo, los datos de texto se recuperan del paquete de datos mediante el método `GetTextAsync`. Después, se puede tomar una acción basada en el valor de texto.

### <a name="retrieve-data-from-the-property-bag"></a>Recuperación de datos de la bolsa de propiedades

Todos los datos se pueden recuperar de un paquete de datos en el controlador del evento `Drop` mediante el acceso a la colección `Properties` del paquete de datos.

La clase `DataPackageView` define una propiedad `Properties`, de tipo `DataPackagePropertySetView`. La clase `DataPackagePropertySetView` representa una bolsa de propiedades de solo lectura almacenada en `Dictionary<string, object>`.

En el ejemplo siguiente se muestra un controlador de eventos `Drop` que recupera datos de la bolsa de propiedades de un paquete de datos para `Rectangle`:

```csharp
void OnDrop(object sender, DropEventArgs e)
{
    Square square = (Square)e.Data.Properties["Square"];

    // Perform logic to take action based on retrieved value.
}
```

En este ejemplo, el objeto de `Square` se recupera de la bolsa de propiedades del paquete de datos mediante la especificación de la clave de diccionario "Square". Después, se puede tomar una acción basada en el valor recuperado.

## <a name="related-links"></a>Vínculos relacionados

- [Gesto de arrastrar y colocar (ejemplo)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-draganddropgesture/)