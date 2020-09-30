---
title: Guardar mapas de bits de SkiaSharp en archivos
description: Explore los distintos formatos de archivo admitidos por SkiaSharp para guardar mapas de bits en la biblioteca de fotos del usuario.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e0c4d247f3ecf9c1b8e077c4a96712e9ed138ca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562956"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Guardar mapas de bits de SkiaSharp en archivos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Después de que una aplicación SkiaSharp haya creado o modificado un mapa de bits, es posible que la aplicación desee guardar el mapa de bits en la biblioteca de fotos del usuario:

![Guardar mapas de bits](saving-images/SavingSample.png "Guardar mapas de bits")

Esta tarea abarca dos pasos:

- Convertir el mapa de bits SkiaSharp en datos en un formato de archivo determinado, como JPEG o PNG.
- Guardar el resultado en la biblioteca de fotos mediante código específico de la plataforma.

## <a name="file-formats-and-codecs"></a>Formatos de archivo y códecs

La mayoría de los formatos de archivo de mapa de bits populares de hoy en día usan la compresión para reducir el espacio de almacenamiento. Las dos amplias categorías de técnicas de compresión se denominan pérdidas _y_ _pérdida_ de tipos. Estos términos indican si el algoritmo de compresión da como resultado la pérdida de datos.

El formato de pérdida más popular fue desarrollado por el grupo de expertos fotográficos Unidos y se denomina JPEG. El algoritmo de compresión JPEG analiza la imagen mediante una herramienta matemática denominada _transformación de coseno discreto_e intenta quitar datos que no son fundamentales para conservar la fidelidad visual de la imagen. El grado de compresión se puede controlar con una configuración a la que se hace referencia normalmente como _calidad_. La configuración de mayor calidad produce archivos de mayor tamaño.

En cambio, un algoritmo de compresión sin pérdida de datos analiza la imagen en busca de repeticiones y patrones de píxeles que se pueden codificar de forma que se reduzcan los datos, pero no se pierda la información. Los datos de mapa de bits originales se pueden restaurar completamente desde el archivo comprimido. El formato de archivo comprimido sin pérdida primario que se usa hoy en día es Portable Network Graphics (PNG).

Por lo general, JPEG se utiliza para las fotografías, mientras que PNG se usa para las imágenes que se han generado de forma manual o algorítmica. Cualquier algoritmo de compresión sin pérdida que reduzca el tamaño de algunos archivos debe aumentar el tamaño de los demás. Afortunadamente, este aumento de tamaño generalmente solo se produce para los datos que contienen una gran cantidad de información aleatoria (o aparentemente aleatoria).

Los algoritmos de compresión son lo suficientemente complejos como para garantizar dos términos que describen los procesos de compresión y descompresión:

- _descodificar_ &mdash; leer un formato de archivo de mapa de bits y descomprimirlo
- _codificar_ &mdash; comprimir el mapa de bits y escribir en un formato de archivo de mapa de bits

La [`SKBitmap`](xref:SkiaSharp.SKBitmap) clase contiene varios métodos denominados `Decode` que crean un `SKBitmap` a partir de un origen comprimido. Lo único que se necesita es proporcionar un nombre de archivo, una secuencia o una matriz de bytes. El descodificador puede determinar el formato de archivo y entregarlo a la función de descodificación interna adecuada.

Además, la [`SKCodec`](xref:SkiaSharp.SKCodec) clase tiene dos métodos denominados `Create` que pueden crear un `SKCodec` objeto a partir de un origen comprimido y permitir que una aplicación sea más complicada en el proceso de descodificación. (La `SKCodec` clase se muestra en el artículo [**animar mapas de bits SkiaSharp**](animating.md#gif-animation) en conexión con la descodificación de un archivo GIF animado).

Al codificar un mapa de bits, se necesita más información: el codificador debe conocer el formato de archivo determinado que la aplicación quiere usar (JPEG o PNG, o algo más). Si se desea un formato de pérdida, la codificación también debe conocer el nivel de calidad deseado.

La `SKBitmap` clase define un [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método con la siguiente sintaxis:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Este método se describe con más detalle en breve. El mapa de bits codificado se escribe en una secuencia de escritura. (' W ' en `SKWStream` significa "grabable"). Los argumentos segundo y tercero especifican el formato de archivo y (para los formatos perdidos) de la calidad deseada comprendida entre 0 y 100.

Además, las [`SKImage`](xref:SkiaSharp.SKImage) clases y [`SKPixmap`](xref:SkiaSharp.SKPixmap) también definen `Encode` métodos que son algo más versátiles y que es preferible. Puede crear fácilmente un `SKImage` objeto a partir de un `SKBitmap` objeto mediante el [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) método estático. Puede obtener un `SKPixmap` objeto de un `SKBitmap` objeto mediante el [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) método.

Uno de los [`Encode`](xref:SkiaSharp.SKImage.Encode) métodos definidos por `SKImage` no tiene parámetros y se guarda automáticamente en formato PNG. Ese método sin parámetros es muy fácil de usar.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Código específico de la plataforma para guardar archivos de mapa de bits

Al codificar un `SKBitmap` objeto en un formato de archivo determinado, normalmente se dejará con un objeto de secuencia de algún tipo, o una matriz de datos. Algunos de los `Encode` métodos (incluido aquél sin parámetros definidos por `SKImage` ) devuelven un [`SKData`](xref:SkiaSharp.SKData) objeto, que se puede convertir en una matriz de bytes mediante el [`ToArray`](xref:SkiaSharp.SKData.ToArray) método. Estos datos se deben guardar en un archivo.

Guardar en un archivo en el almacenamiento local de la aplicación es bastante sencillo, ya que puede usar `System.IO` clases y métodos estándar para esta tarea. Esta técnica se muestra en el artículo [**animar mapas de bits SkiaSharp**](animating.md#bitmap-animation) en conexión con la animación de una serie de mapas de bits del conjunto Mandelbrot.

Si desea que el archivo se comparta con otras aplicaciones, se debe guardar en la biblioteca de fotos del usuario. Esta tarea requiere código específico de la plataforma y el uso de Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

El proyecto **SkiaSharpFormsDemo** de la aplicación [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) define una `IPhotoLibrary` interfaz que se usa con la `DependencyService` clase. Define la sintaxis de un `SavePhotoAsync` método:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Esta interfaz también define el `PickPhotoAsync` método, que se usa para abrir el selector de archivos específico de la plataforma para la biblioteca fotográfica del dispositivo.

En `SavePhotoAsync` el caso de, el primer argumento es una matriz de bytes que contiene el mapa de bits ya codificado en un formato de archivo determinado, como JPEG o PNG. Es posible que una aplicación desee aislar todos los mapas de bits que crea en una carpeta determinada, que se especifica en el parámetro siguiente, seguido del nombre de archivo. El método devuelve un valor booleano que indica que se ha realizado correctamente o no.

En las secciones siguientes se describe cómo `SavePhotoAsync` se implementa en cada plataforma.

### <a name="the-ios-implementation"></a>Implementación de iOS

La implementación de iOS de `SavePhotoAsync` usa el [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) método de `UIImage` :

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

Desafortunadamente, no hay ninguna manera de especificar un nombre de archivo o una carpeta para la imagen.

El archivo **info. plist** del proyecto de iOS requiere una clave que indique que agrega imágenes a la biblioteca de fotos:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

¡ Mire! La clave de permiso para simplemente tener acceso a la biblioteca de fotos es muy similar pero no la misma:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>La implementación de Android

La implementación de Android de `SavePhotoAsync` primero comprueba si el `folder` argumento es `null` o una cadena vacía. En ese caso, el mapa de bits se guarda en el directorio raíz de la biblioteca de fotos. De lo contrario, se obtiene la carpeta y, si no existe, se crea:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

La llamada a `MediaScannerConnection.ScanFile` no es estrictamente necesaria, pero si está probando el programa mediante la comprobación inmediata de la biblioteca fotográfica, lo ayuda a actualizar la vista de la galería de bibliotecas.

El archivo **AndroidManifest.xml** requiere la siguiente etiqueta de permiso:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `SavePhotoAsync` es muy similar en la estructura de la implementación de Android:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

La sección de **funcionalidades** del archivo **Package. Appxmanifest** requiere la **biblioteca de imágenes**.

## <a name="exploring-the-image-formats"></a>Explorar los formatos de imagen

Este es el [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método de `SKImage` nuevo:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) es una enumeración con miembros que hacen referencia a once formatos de archivo de mapa de bits, algunos de los cuales son bastante claros:

- `Astc`&mdash;Compresión de textura escalable adaptable
- `Bmp`&mdash;Mapa de bits de Windows
- `Dng`&mdash;Adobe Digital negativo
- `Gif`&mdash;Formato de intercambio de gráficos
- `Ico`&mdash;Imágenes de iconos de Windows
- `Jpeg`&mdash;Grupo de expertos fotográficos Unidos
- `Ktx`&mdash;Formato de textura de Khronos para OpenGL
- `Pkm`&mdash;Pokémon guardar archivo
- `Png`&mdash;Gráficos de red portátiles
- `Wbmp`&mdash;Formato de mapa de bits del Protocolo de aplicación inalámbrica (1 bit por píxel)
- `Webp`&mdash;Formato de Google WebP

Como verá en breve, solo se admiten tres de estos formatos de archivo ( `Jpeg` , `Png` y `Webp` ) en SkiaSharp.

Para guardar un `SKBitmap` objeto denominado `bitmap` en la biblioteca de fotos del usuario, también necesita un miembro de la `SKEncodedImageFormat` enumeración denominada `imageFormat` y (en el caso de los formatos de pérdida) de una variable de tipo entero `quality` . Puede usar el siguiente código para guardar el mapa de bits en un archivo con el nombre `filename` de la `folder` carpeta:

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

La `SKManagedWStream` clase se deriva de `SKWStream` (que significa "Stream grabable"). El `Encode` método escribe el archivo de mapa de bits codificado en esa secuencia. Los comentarios de ese código hacen referencia a algunas comprobaciones de errores que es posible que deba realizar.

La página **Guardar formatos de archivo** de la aplicación [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) usa código similar para que pueda experimentar con la opción de guardar un mapa de bits en los distintos formatos.

El archivo XAML contiene un `SKCanvasView` que muestra un mapa de bits, mientras que el resto de la página contiene todo lo que la aplicación necesita para llamar al `Encode` método de `SKBitmap` . Tiene un `Picker` para un miembro de la `SKEncodedImageFormat` enumeración, un `Slider` para el argumento Quality para formatos de mapa de bits perdidos, dos `Entry` vistas para un nombre de archivo y un nombre de carpeta, y `Button` para guardar el archivo.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

El archivo de código subyacente carga un recurso de mapa de bits y utiliza `SKCanvasView` para mostrarlo. Dicho mapa de bits nunca cambia. El `SelectedIndexChanged` controlador para `Picker` modifica el nombre de archivo con una extensión que es igual que el miembro de la enumeración:

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

El `Clicked` controlador para `Button` realiza todo el trabajo real. Obtiene dos argumentos para `Encode` desde `Picker` y `Slider` , y, a continuación, usa el código mostrado anteriormente para crear un `SKManagedWStream` para el `Encode` método. Las dos `Entry` vistas suministran nombres de carpeta y archivo para el `SavePhotoAsync` método.

La mayor parte de este método está dedicada a controlar problemas o errores. Si `Encode` crea una matriz vacía, significa que no se admite el formato de archivo determinado. Si `SavePhotoAsync` devuelve `false` , el archivo no se guardó correctamente.

Este es el programa que se ejecuta:

[![Guardar formatos de archivo](saving-images/SaveFileFormats.png "Guardar formatos de archivo")](saving-images/SaveFileFormats-Large.png#lightbox)

En esta captura de pantalla se muestran los tres formatos que se admiten en estas plataformas:

- JPEG
- PNG
- WebP

En el caso de todos los demás formatos, el `Encode` método no escribe nada en la secuencia y la matriz de bytes resultante está vacía.

El mapa de bits que guarda la página **formatos de archivo guardados** es 600-píxeles cuadrados. Con 4 bytes por píxel, es un total de 1.440.000 bytes en memoria. En la tabla siguiente se muestra el tamaño de archivo para varias combinaciones de formato de archivo y calidad:

|Formato|Calidad|Size|
|------|------:|---:|
| PNG | N/D | 492K |
| JPEG | 0 | 2.95 k |
|      | 50 | 22.1 k |
|      | 100 | 206K |
| WebP | 0 | 2.71 k |
|      | 50 | 11.9 k |
|      | 100 | 101K |

Puede experimentar con varias configuraciones de calidad y examinar los resultados.

## <a name="saving-finger-paint-art"></a>Guardar carátulas de dedos

Un uso común de un mapa de bits es el dibujo de programas, donde funciona como algo denominado _mapa de bits de instantánea_. Todo el dibujo se conserva en el mapa de bits, que el programa muestra. El mapa de bits también resulta útil para guardar el dibujo.

El artículo sobre cómo [**pintar el dedo en SkiaSharp**](../paths/finger-paint.md) muestra cómo usar el seguimiento táctil para implementar un programa de pintado de huellas primitivo. El programa admite solo un color y un ancho de trazo, pero reservó todo el dibujo en una colección de `SKPath` objetos.

La página **con el dedo pintar con guardar** en el ejemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) también conserva todo el dibujo en una colección de `SKPath` objetos, pero también representa el dibujo en un mapa de bits, que puede guardar en la biblioteca de fotos.

Gran parte de este programa es similar al programa de pintura con el **dedo** original. Una mejora es que el archivo XAML ahora crea instancias de los botones con la etiqueta **Clear** y **Save**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

El archivo de código subyacente mantiene un campo de tipo `SKBitmap` denominado `saveBitmap` . Este mapa de bits se crea o se vuelve a crear en el `PaintSurface` controlador cada vez que cambia el tamaño de la superficie de presentación. Si es necesario volver a crear el mapa de bits, el contenido del mapa de bits existente se copia en el nuevo mapa de bits para que todo se retenga independientemente de cómo cambie el tamaño de la superficie de la pantalla:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

El dibujo realizado por el `PaintSurface` controlador se produce al final y se compone únicamente de la representación del mapa de bits.

El procesamiento táctil es similar al programa anterior. El programa mantiene dos colecciones, `inProgressPaths` y `completedPaths` , que contienen todo lo que el usuario ha dibujado desde la última vez que se borró la pantalla. Para cada evento Touch, el `OnTouchEffectAction` controlador llama a `UpdateBitmap` :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

El `UpdateBitmap` método vuelve a dibujar `saveBitmap` mediante la creación de un nuevo `SKCanvas` , su borrado y, a continuación, la representación de todas las rutas de acceso en el mapa de bits. Concluye invalidando `canvasView` para que el mapa de bits se pueda dibujar en la pantalla.

Estos son los controladores de los dos botones. El botón **Borrar** borra las dos colecciones de rutas de acceso, las actualizaciones `saveBitmap` (lo que resulta en borrar el mapa de bits) y invalida `SKCanvasView` :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

El controlador del botón **Guardar** usa el [`Encode`](xref:SkiaSharp.SKImage.Encode) método simplificado de `SKImage` . Este método codifica mediante el formato PNG. El `SKImage` objeto se crea basándose en `saveBitmap` y el `SKData` objeto contiene el archivo PNG codificado.

El `ToArray` método de `SKData` obtiene una matriz de bytes. Esto es lo que se pasa al `SavePhotoAsync` método, junto con un nombre de carpeta fijo y un nombre de archivo único construido a partir de la fecha y hora actuales.

Este es el programa en acción:

[![Guardar con el dedo](saving-images/FingerPaintSave.png "Guardar con el dedo")](saving-images/FingerPaintSave-Large.png#lightbox)

En el ejemplo [**SpinPaint**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) se usa una técnica muy similar. También se trata de un programa de dibujo de dedos, salvo que el usuario pinta en un disco girando y, a continuación, reproduce los diseños en los otros cuatro cuadrantes. El color del dibujo con el dedo cambia a medida que el disco gira:

[![Girar dibujo](saving-images/SpinPaint.png "Girar dibujo")](saving-images/SpinPaint-Large.png#lightbox)

El botón **Guardar** de la `SpinPaint` clase es similar a la **pintura con dedo** en que guarda la imagen en un nombre de carpeta fijo (**SpainPaint**) y un nombre de archivo construido a partir de la fecha y la hora.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)