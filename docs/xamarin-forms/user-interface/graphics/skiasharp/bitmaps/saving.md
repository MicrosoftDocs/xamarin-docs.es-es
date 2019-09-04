---
title: Guardar mapas de bits de SkiaSharp en archivos
description: Explore los distintos formatos de archivo compatibles con SkiaSharp para guardar los mapas de bits en la biblioteca de fotos del usuario.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 2431c19ceac08db8cb9fef3834695514e714f68f
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228039"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Guardar mapas de bits de SkiaSharp en archivos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Después de una aplicación de SkiaSharp ha creado o modificado un mapa de bits, la aplicación que le interese guardar el mapa de bits a biblioteca de fotos del usuario:

![Guardar mapas de bits](saving-images/SavingSample.png "guardar mapas de bits")

Esta tarea abarca dos pasos:

- Convertir el mapa de bits de SkiaSharp a los datos en un formato de archivo determinado, como JPEG o PNG.
- Guardando el resultado a la biblioteca de fotos con código específico de plataforma.

## <a name="file-formats-and-codecs"></a>Códecs y formatos de archivo

Usar compresión para reducir el espacio de almacenamiento da formato a la mayoría de los archivos de mapa de bits más populares de hoy en día. Las dos amplias categorías de las técnicas de compresión se denominan _con pérdida_ y _sin pérdida de datos_. Estos términos de indican si el algoritmo de compresión da como resultado la pérdida de datos.

El formato más popular con pérdida de datos fue desarrollado por el Joint Photographic Experts Group y se denomina JPEG. El algoritmo de compresión JPEG analiza la imagen mediante una herramienta matemática denominada el _transformación coseno discretos_e intenta quitar los datos que no son cruciales para conservar la fidelidad visual de la imagen. Se puede controlar el grado de compresión con un valor que se suele denominado _calidad_. Como resultado valores de alta calidad en archivos de mayor tamaño.

En cambio, un algoritmo de compresión sin pérdida de datos analiza la imagen de repetición y patrones de píxeles que se pueden codificar de forma que reduce los datos pero no tiene como resultado la pérdida de la información. Los datos de mapa de bits original se pueden restaurar completamente desde el archivo comprimido. El formato de archivo comprimido sin pérdida de datos principal en uso hoy en día es Portable Network Graphics (PNG).

Por lo general, se usa JPEG para las fotografías, mientras PNG se utiliza para las imágenes que se han generado manualmente o de forma algorítmica. Cualquier algoritmo de compresión sin pérdida de datos que reduce el tamaño de algunos archivos necesariamente debe aumentar el tamaño de los demás. Afortunadamente, este aumento de tamaño solo se produce para los datos que contiene una gran cantidad de información aleatoria (o aparentemente aleatoria).

La compresión de los algoritmos son lo suficientemente complejos como para justificar dos términos que se describen los procesos de compresión y descompresión:

- _descodificar_ &mdash; leer un formato de archivo de mapa de bits y los descomprime
- _codificar_ &mdash; comprimir el mapa de bits y escribir en un formato de archivo de mapa de bits

El [ `SKBitmap` ](xref:SkiaSharp.SKBitmap) clase contiene varios métodos denominados `Decode` que crean un `SKBitmap` desde un origen comprimidos. Todo lo que necesita es proporcionar un nombre de archivo, secuencia o matriz de bytes. El descodificador puede determinar el formato de archivo y entregarlo a la función apropiada de descodificación interna.

Además, el [ `SKCodec` ](xref:SkiaSharp.SKCodec) clase tiene dos métodos denominados `Create` que puede crear un `SKCodec` objeto desde un origen comprimidos y permitir que una aplicación a involucrarse más en el proceso de descodificación. (El `SKCodec` clase se muestra en el artículo [ **animar los mapas de bits de SkiaSharp** ](animating.md#gif-animation) en relación con un archivo GIF animado de descodificación.)

Al codificar un mapa de bits, se necesita más información: El codificador debe conocer el formato de archivo determinado que la aplicación quiere usar (JPEG o PNG, o algo más). Si se desea un formato con pérdida de datos, la codificación también debe conocer el nivel de calidad deseado.

El `SKBitmap` clase define uno [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método con la sintaxis siguiente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Este método se describe más detalladamente en breve. El mapa de bits codificado se escribe en una secuencia de escritura. ('W' en `SKWStream` es el acrónimo de "escritura".) Los argumentos segundo y terceros especifican el formato de archivo y (para los formatos con pérdida de datos) la calidad deseada comprendida entre 0 y 100.

Además, el [ `SKImage` ](xref:SkiaSharp.SKImage) y [ `SKPixmap` ](xref:SkiaSharp.SKPixmap) también definen clases `Encode` métodos que son un poco más versátiles y es posible que prefiera. Puede crear fácilmente un `SKImage` objeto desde un `SKBitmap` objeto estático [ `SKImage.FromBitmap` ](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) método. Puede obtener un `SKPixmap` objeto desde un `SKBitmap` objeto utilizando el [ `PeekPixels` ](xref:SkiaSharp.SKBitmap.PeekPixels) método.

Uno de los [ `Encode` ](xref:SkiaSharp.SKImage.Encode) métodos definidos por `SKImage` no tiene parámetros y guarda automáticamente en un formato PNG. Es muy fácil de usar ese método sin parámetros.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Código específico de plataforma para guardar los archivos de mapa de bits

Cuando se codifica un `SKBitmap` dan formato a objeto en un archivo determinado, por lo general obtendrá un objeto de secuencia de algún tipo o una matriz de datos. Algunos de los `Encode` métodos (incluido con ningún parámetro definido por el `SKImage`) devolver un [ `SKData` ](xref:SkiaSharp.SKData) objeto, que se puede convertir en una matriz de bytes utilizando el [ `ToArray` ](xref:SkiaSharp.SKData.ToArray) método. Estos datos, a continuación, deben guardarse en un archivo.

Guardar datos en un archivo en almacenamiento local para la aplicación es bastante fácil porque puede utilizar estándar `System.IO` clases y métodos para esta tarea. Esta técnica se muestra en el artículo [ **animar los mapas de bits de SkiaSharp** ](animating.md#bitmap-animation) en relación con la animación de una serie de mapas de bits del conjunto de Mandelbrot.

Si desea que el archivo compartido por otras aplicaciones, se debe guardar en la biblioteca de fotos del usuario. Esta tarea requiere código específico de plataforma y el uso de Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

El **SkiaSharpFormsDemo** del proyecto en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) aplicación define un `IPhotoLibrary` interfaz utilizada con el `DependencyService` clase. Esto define la sintaxis de un `SavePhotoAsync` método:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Esta interfaz también define la `PickPhotoAsync` método, que se usa para abrir el selector de archivos específicos de la plataforma para la biblioteca de fotografías del dispositivo.

Para `SavePhotoAsync`, el primer argumento es una matriz de bytes que contiene el mapa de bits ya está codificado en un formato de archivo determinado, como JPEG o PNG. Es posible que una aplicación podría desear aislar todos los mapas de bits que crea en una carpeta concreta, que se especifica en el parámetro siguiente, seguido del nombre de archivo. El método devuelve un valor booleano que indica el éxito o no.

Las secciones siguientes describen cómo `SavePhotoAsync` se implementa en cada plataforma.

### <a name="the-ios-implementation"></a>La implementación de iOS

La implementación de iOS de `SavePhotoAsync` usa el [ `SaveToPhotosAlbum` ](xref:UIKit.UIImage.SaveToPhotosAlbum*) método `UIImage`:

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

Lamentablemente, no hay ninguna forma de especificar un nombre de archivo o carpeta para la imagen.

El **Info.plist** archivo en el proyecto de iOS requiere una clave que indica que agrega imágenes a la biblioteca de fotos:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

¡Cuidado! La clave de permiso para acceder simplemente a la biblioteca de fotos es muy similar, pero no es el mismo:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>La implementación de Android

La implementación de Android `SavePhotoAsync` comprueba primero si el `folder` argumento es `null` o una cadena vacía. Si es así, el mapa de bits se guarda en el directorio raíz de la biblioteca de fotografías. En caso contrario, se obtiene la carpeta y, si no existe, se crea:

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

La llamada a `MediaScannerConnection.ScanFile` no es estrictamente necesaria, pero si está probando el programa activando inmediatamente a la biblioteca de fotos, mucha ayuda mediante la actualización de la vista de biblioteca de galería.

El **AndroidManifest.xml** archivo requiere la siguiente etiqueta de permiso:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `SavePhotoAsync` es una estructura muy similar a la implementación de Android:

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

El **capacidades** sección de la **Package.appxmanifest** archivo requiere **biblioteca imágenes**.

## <a name="exploring-the-image-formats"></a>Exploración de los formatos de imagen

Este es el [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) método `SKImage` nuevo:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) es una enumeración con miembros que hacen referencia a los formatos de archivo de mapa de bits once, algunos de los cuales son bastante confusa:

- `Astc` &mdash; Compresión de textura escalable adaptable
- `Bmp` &mdash; Mapa de bits de Windows
- `Dng` &mdash; Negativo Digital de Adobe
- `Gif` &mdash; Formato de intercambio de gráficos
- `Ico` &mdash; Imágenes de icono de Windows
- `Jpeg` &mdash; Joint Photographic Experts Group
- `Ktx` &mdash; Formato de textura Khronos para OpenGL
- `Pkm` &mdash; Pokémon Guardar archivo
- `Png` &mdash; Portable Network Graphics
- `Wbmp` &mdash; Formato de mapa de bits de protocolo de aplicación inalámbrica (1 bit por píxel)
- `Webp` &mdash; Formato de Google WebP

Como verá en breve, solo tres de estos formatos de archivo (`Jpeg`, `Png`, y `Webp`) realmente son compatibles con SkiaSharp.

Para guardar un `SKBitmap` objeto denominado `bitmap` a biblioteca de fotos del usuario, también se necesita un miembro de la `SKEncodedImageFormat` enumeración denominada `imageFormat` y (para los formatos con pérdida de datos) entero `quality` variable. Puede usar el siguiente código para guardar ese mapa de bits en un archivo con el nombre `filename` en el `folder` carpeta:

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

El `SKManagedWStream` clase se deriva de `SKWStream` (que es el acrónimo de "secuencia de escritura"). El `Encode` método escribe el archivo de mapa de bits codificados en esa secuencia. Los comentarios en el código hacen referencia a algún error de comprobación es posible que deba realizar.

El **guardar formatos de archivo** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) aplicación utiliza un código similar para que pueda experimentar al guardar un mapa de bits en los distintos formatos.

El archivo XAML contiene una `SKCanvasView` que muestra un mapa de bits, mientras que el resto de la página contiene todo lo que la aplicación necesita llamar a la `Encode` método `SKBitmap`. Tiene un `Picker` para un miembro de la `SKEncodedImageFormat` enumeración, un `Slider` para el argumento de calidad para formatos de mapa de bits con pérdida de datos, dos `Entry` vistas para un nombre de nombre de archivo y carpeta y un `Button` para guardar el archivo.

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

El archivo de código subyacente carga un recurso de mapa de bits y utiliza el `SKCanvasView` para mostrarla. Que de mapa de bits nunca los cambios. El `SelectedIndexChanged` controlador para el `Picker` modifica el nombre de archivo con una extensión que es el mismo que el miembro de enumeración:

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

El `Clicked` controlador para el `Button` funciona todas real. Obtiene dos argumentos `Encode` desde el `Picker` y `Slider`y, a continuación, usa el código mostrado anteriormente para crear un `SKManagedWStream` para el `Encode` método. Los dos `Entry` vistas suministrar los nombres de carpeta y archivo para el `SavePhotoAsync` método.

La mayoría de este método se dedica a controlar los problemas o errores. Si `Encode` crea una matriz vacía, significa que no se admite el formato de archivo determinado. Si `SavePhotoAsync` devuelve `false`, a continuación, el archivo no se ha guardado correctamente.

Este es el programa que se ejecuta:

[![Guardar formatos de archivo](saving-images/SaveFileFormats.png "guardar formatos de archivo")](saving-images/SaveFileFormats-Large.png#lightbox)

Esa captura de pantalla muestra los formatos de tres solo se admiten en estas plataformas:

- JPEG
- PNG
- WebP

Para todos los demás formatos, el `Encode` método escribe nada en la secuencia y la matriz de bytes resultante está vacía.

El mapa de bits que el **guardar formatos de archivo** página guarda es 600 píxeles al cuadrado. Con 4 bytes por píxel, es un total de bytes 1.440.000 en memoria. En la tabla siguiente se muestra el tamaño del archivo para diversas combinaciones de formato de archivo y la calidad:

|Formato|Calidad|Tamaño|
|------|------:|---:|
| PNG | N/D | 492K |
| JPEG | 0 | 2,95 K |
|      | 50 | 22.1 K |
|      | 100 | 206K |
| WebP | 0 | 2,71 K |
|      | 50 | 11,9 K |
|      | 100 | 101K |

Puede experimentar con distintas configuraciones de calidad y examinar los resultados.

## <a name="saving-finger-paint-art"></a>Guardando el arte finger-paint

Un uso habitual de un mapa de bits se encuentra en el dibujo de programas, donde las funciones como algo que se denomina un _mapa de bits de sombra_. Todo el dibujo se retiene en el mapa de bits, que se muestra a continuación, el programa. El mapa de bits también resulta útil para guardar el dibujo.

El [ **pintar con los dedos en SkiaSharp** ](../paths/finger-paint.md) artículo muestra cómo usar un toque de seguimiento para implementar un programa para primitivo. El programa compatible con un solo color y ancho del trazo de un único, pero conserva todo el plano en una colección de `SKPath` objetos.

El **pintura con los dedos con Guardar** página en el [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) ejemplo también conserva todo el plano en una colección de `SKPath` objetos, pero también representa el dibujo en un mapa de bits, que puede guardar en la biblioteca de fotos.

Gran parte de este programa es similar a la versión original **pintura con los dedos** programa. Una de estas mejoras es que el archivo XAML ahora crea una instancia de botones con la etiqueta **clara** y **guardar**:

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

El archivo de código subyacente mantiene un campo de tipo `SKBitmap` denominado `saveBitmap`. Este mapa de bits se crea o vuelve a crear en el `PaintSurface` controlador cada vez que el tamaño de la presentación de surface cambios. Si necesita volver a crear el mapa de bits, el contenido del mapa de bits existente se copia al nuevo mapa de bits para que todo lo que se conserva independientemente de cómo la superficie de visualización cambia de tamaño:

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

El dibujo realizarlo el `PaintSurface` controlador se produce al final y consiste únicamente en el mapa de bits de representación.

El procesamiento de interacción es similar al programa anterior. El programa mantiene dos colecciones, `inProgressPaths` y `completedPaths`, que contienen todo lo que el usuario ha dibujado desde la última vez que se ha borrado la presentación. Para cada evento de toque, el `OnTouchEffectAction` llamadas del controlador `UpdateBitmap`:

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

El `UpdateBitmap` método vuelve a dibujar `saveBitmap` creando un nuevo `SKCanvas`, borrarlo y, a continuación, representar todas las rutas de acceso en el mapa de bits. Concluye al invalidar `canvasView` para que se puede dibujar el mapa de bits en la pantalla.

Estos son los controladores para los dos botones. El **clara** botón borra ambas colecciones de la ruta de acceso, las actualizaciones `saveBitmap` (lo que da como resultado de borrar el mapa de bits) e invalida el `SKCanvasView`:

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

El **guardar** controlador del botón usa el simplificada [ `Encode` ](xref:SkiaSharp.SKImage.Encode) método desde `SKImage`. Este método codifica con el formato PNG. El `SKImage` se crea un objeto basado en `saveBitmap`y el `SKData` objeto contiene el archivo PNG codificado.

El `ToArray` método `SKData` Obtiene una matriz de bytes. Esto es lo que se pasa a la `SavePhotoAsync` método junto con un nombre de carpeta fijo y un nombre de archivo único construido a partir de la fecha y hora actuales.

Este es el programa en acción:

[![Un toque Paint guardar](saving-images/FingerPaintSave.png "Finger Paint guardar")](saving-images/FingerPaintSave-Large.png#lightbox)

Se usa una técnica muy similar en el [ **SpinPaint** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) ejemplo. Esto también es un programa para salvo que pinta el usuario en un disco de giro que, a continuación, reproduzca los diseños de sus cuatro cuadrantes. El color de los cambios de pintura dedo como el disco está girando:

[![Ponga en Paint](saving-images/SpinPaint.png "ponga en Paint")](saving-images/SpinPaint-Large.png#lightbox)

El **guardar** botón de `SpinPaint` es similar a la clase **pintura con los dedos** en que guarda la imagen en un nombre de carpeta fijo (**SpainPaint**) y se construye a partir de un nombre de archivo la fecha y hora.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
