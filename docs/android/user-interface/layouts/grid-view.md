---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f71c275dd2beee6aedf41ecd19c8a4a39ab5a36f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522641"
---
# <a name="xamarinandroid-gridview"></a>GridView de Xamarin. Android

[`GridView`](xref:Android.Widget.GridView)es un[`ViewGroup`](xref:Android.Views.ViewGroup)
que muestra los elementos en una cuadrícula bidimensional desplazable. Los elementos de la cuadrícula se insertan automáticamente en el [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)diseño mediante.

En este tutorial, creará una cuadrícula de miniaturas de imagen. Cuando se selecciona un elemento, se muestra la posición de la imagen en un mensaje del sistema.

Inicie un nuevo proyecto denominado **HelloGridView**.

Busque algunas fotos que le gustaría usar o [Descargue estas imágenes de ejemplo](https://developer.android.com/shareables/sample_images.zip). Agregue los archivos de imagen al directorio Resources **/drawable** del proyecto. En la ventana **propiedades** , establezca la acción de compilación para cada en **AndroidResource**.

Abra el archivo Resources **/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

Esto [`GridView`](xref:Android.Widget.GridView) rellenará toda la pantalla. Los atributos son bastante descriptivos. Para obtener más información sobre los atributos válidos [`GridView`](xref:Android.Widget.GridView) , vea la referencia.

Abra `HelloGridView.cs` e inserte el código siguiente para el[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Una vez establecido el diseño **Main. axml** para la vista de contenido, [`GridView`](xref:Android.Widget.GridView) se captura desde el diseño con [`FindViewById`](xref:Android.App.Activity.FindViewById*). El[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
a continuación, se utiliza la propiedad para establecer un`ImageAdapter`adaptador personalizado () como el origen de todos los elementos que se van a mostrar en la cuadrícula. `ImageAdapter` Se crea en el paso siguiente.

Para hacer algo cuando se hace clic en un elemento de la cuadrícula, se suscribe un delegado anónimo al [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick) evento.
Muestra un [`Toast`](xref:Android.Widget.Toast) que muestra la posición de índice (basada en cero) del elemento seleccionado (en un escenario real, la posición se podría usar para obtener la imagen de tamaño completo para otra tarea). Tenga en cuenta que se pueden usar clases de agente de escucha de estilo Java en lugar de eventos de .NET.

Cree una nueva clase denominada `ImageAdapter` [`BaseAdapter`](xref:Android.Widget.BaseAdapter)subclases:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

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
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

En primer lugar, implementa algunos métodos necesarios heredados [`BaseAdapter`](xref:Android.Widget.BaseAdapter)de. El constructor y la [`Count`](xref:Android.Widget.BaseAdapter.Count) propiedad se explican por sí solos. Regla[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
debe devolver el objeto real situado en la posición especificada en el adaptador, pero se omite en este ejemplo. Forma[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
debe devolver el ID. de fila del elemento, pero no es necesario aquí.

El primer método necesario es [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*).
Este método crea una nueva[`View`](xref:Android.Views.View)
para cada imagen agregada a `ImageAdapter`. Cuando se llama a, un[`View`](xref:Android.Views.View)
se pasa, que suele ser un objeto reciclado (al menos después de que se haya llamado una vez), por lo que hay una comprobación para ver si el objeto es NULL. Si *es* null, un valor de[`ImageView`](xref:Android.Widget.ImageView)
se crea una instancia y se configura con las propiedades deseadas para la presentación de la imagen:

- [`LayoutParams`](xref:Android.Views.View.LayoutParameters)establece el alto y el ancho de la&mdash;vista, lo que garantiza que, independientemente del tamaño de la dibujable, cada imagen cambia de tamaño y se recorta para ajustarse a estas dimensiones, según corresponda.

- [`SetScaleType()`](xref:Android.Widget.ImageView.SetScaleType*)declara que las imágenes se deben recortar hacia el centro (si es necesario).

- [`SetPadding(int, int, int, int)`](xref:Android.Views.View.SetPadding*)define el relleno de todos los lados. (Tenga en cuenta que, si las imágenes tienen diferentes proporciones de aspecto, menos espaciado provocará un recorte de la imagen si no coincide con las dimensiones proporcionadas a ImageView).

Si el [`View`](xref:Android.Views.View) que se [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*) pasa a *no* es null, el local[`ImageView`](xref:Android.Widget.ImageView)
se inicializa con el objeto reciclado [`View`](xref:Android.Views.View) .

Al final de la[`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)
, el `position` entero que se pasa al método se usa para seleccionar una imagen de la `thumbIds` matriz, que se establece como [`ImageView`](xref:Android.Widget.ImageView)el recurso de imagen para.

Lo único que queda es definir la matriz `thumbIds` de recursos que se van a dibujar.

Ejecute la aplicación. El diseño de la cuadrícula debería tener un aspecto similar al siguiente:

[![Captura de pantalla de ejemplo de GridView que muestra 15 imágenes](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Pruebe a experimentar con los comportamientos de [`GridView`](xref:Android.Widget.GridView) y[`ImageView`](xref:Android.Widget.ImageView)
elementos ajustando sus propiedades. Por ejemplo, en lugar de usar [`LayoutParams`](xref:Android.Views.View.LayoutParameters) try using [`SetAdjustViewBounds()`](xref:Android.Widget.ImageView.SetAdjustViewBounds*).

## <a name="references"></a>Referencias

- [`GridView`](xref:Android.Widget.GridView)
- [`ImageView`](xref:Android.Widget.ImageView)
- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la licencia de atribución de [Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
