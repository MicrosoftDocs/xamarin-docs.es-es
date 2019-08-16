---
title: Control de la galería de Android
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 04fac4084328c0af962282dea7a31e3c00457bef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523008"
---
# <a name="xamarinandroid-gallery-control"></a>Control de la galería de Xamarin. Android

[`Gallery`](xref:Android.Widget.Gallery)es un widget de diseño que se usa para mostrar los elementos en una lista de desplazamiento horizontal y coloca la selección actual en el centro de la vista.

> [!IMPORTANT]
> Este widget quedó en desuso en Android 4,1 (nivel de API 16). 

En este tutorial, creará una galería de fotos y mostrará un mensaje de notificación cada vez que se seleccione un elemento de la galería.

Una vez `Main.axml` establecido el diseño para la vista de contenido `Gallery` , se captura desde el diseño con [`FindViewById`](xref:Android.App.Activity.FindViewById*).
El[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
a continuación, se utiliza la propiedad para establecer un `ImageAdapter`adaptador personalizado () como el origen de todos los elementos que se van a mostrar en el dallery. `ImageAdapter` Se crea en el paso siguiente.

Para hacer algo cuando se hace clic en un elemento de la galería, se suscribe un delegado anónimo a la[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
. Muestra un[`Toast`](xref:Android.Widget.Toast)
que muestra la posición de índice (basada en cero) del elemento seleccionado (en un escenario real, la posición se podría usar para obtener la imagen de tamaño completo para otra tarea).

En primer lugar, hay algunas variables de miembro, incluida una matriz de identificadores que hacen referencia a las imágenes guardadas en el directorio de recursos Dibujables (Resources **/drawable**).

A continuación se encuentra el constructor de clase, donde[`Context`](xref:Android.Content.Context)
para una `ImageAdapter` instancia de se define y se guarda en un campo local.
A continuación, se implementan algunos métodos necesarios heredados de [`BaseAdapter`](xref:Android.Widget.BaseAdapter).
El constructor y el[`Count`](xref:Android.Widget.BaseAdapter.Count)
la propiedad se explica por sí misma. Regla[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
debe devolver el objeto real situado en la posición especificada en el adaptador, pero se omite en este ejemplo. Forma[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
debe devolver el ID. de fila del elemento, pero no es necesario aquí.

El método realiza el trabajo para aplicar una imagen a un[`ImageView`](xref:Android.Widget.ImageView)
que se incrustará en el[`Gallery`](xref:Android.Widget.Gallery)
En este método, el miembro[`Context`](xref:Android.Content.Context)
se usa para crear un nuevo [`ImageView`](xref:Android.Widget.ImageView).
El[`ImageView`](xref:Android.Widget.ImageView)
se prepara aplicando una imagen de la matriz local de recursos que se van a dibujar y estableciendo[`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
alto y ancho de la imagen, estableciendo la escala para que quepa el[`ImageView`](xref:Android.Widget.ImageView)
dimensiones y, por último, establecer el fondo para usar el atributo con estilo adquirido en el constructor.

Vea [`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType) para ver otras opciones de escalado de imágenes.

## <a name="walkthrough"></a>Tutorial

Inicie un nuevo proyecto denominado *HelloGallery*.

[![Captura de pantalla del nuevo proyecto de Android en el cuadro de diálogo Nueva solución](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Busque algunas fotos que le gustaría usar o [Descargue estas imágenes de ejemplo](https://developer.android.com/shareables/sample_images.zip).
Agregue los archivos de imagen al directorio Resources **/drawable** del proyecto. En la ventana **propiedades** , establezca la acción de compilación para cada en **AndroidResource**.

Abra **Resources/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Abra `MainActivity.cs` e inserte el código siguiente para el[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Cree una nueva clase denominada `ImageAdapter` [`BaseAdapter`](xref:Android.Widget.BaseAdapter)subclases:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

    public override Java.Lang.Object GetItem (int position)
    {
          return null;
    }

    public override long GetItemId (int position)
    {
          return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Ejecute la aplicación. Debería ser similar a la captura de pantalla siguiente:

![Captura de pantalla de HelloGallery que muestra imágenes de ejemplo](gallery-images/hellogallery3.png)

## <a name="references"></a>Referencias

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de atribución de [Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
