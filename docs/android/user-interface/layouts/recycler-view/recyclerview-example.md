---
title: Ejemplo de RecyclerView básico
description: Una aplicación de ejemplo que muestra cómo usar RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 7266d13136dbfc858bc3c882cacb802d83c92f52
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028839"
---
# <a name="a-basic-recyclerview-example"></a>Ejemplo de RecyclerView básico

Para entender cómo funciona `RecyclerView` en una aplicación típica, en este tema se explora la aplicación de ejemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , un ejemplo de código sencillo que usa `RecyclerView` para mostrar una gran colección de fotografías: 

[![dos capturas de pantalla de una aplicación de RecyclerView que usa CardViews para mostrar las fotos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [CardView](~/android/user-interface/controls/card-view.md) para implementar cada elemento de fotografía en el diseño de `RecyclerView`. Debido a las ventajas de rendimiento de `RecyclerView`, esta aplicación de ejemplo puede desplazarse rápidamente a través de una gran colección de fotografías sin problemas y sin retrasos apreciables.

### <a name="an-example-data-source"></a>Un origen de datos de ejemplo

En esta aplicación de ejemplo, un origen de datos de "álbum fotográfico" (representado por la clase `PhotoAlbum`) proporciona `RecyclerView` con el contenido del elemento.
`PhotoAlbum` es una colección de fotos con títulos; al crear una instancia de ella, obtiene una colección preparada de 32 fotos:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instancia de la foto de `PhotoAlbum` expone propiedades que le permiten leer su identificador de recurso de imagen, `PhotoID`y su cadena de título, `Caption`. La colección de fotografías se organiza de forma que un indexador puede acceder a cada foto. Por ejemplo, las siguientes líneas de código tienen acceso al identificador de recurso de imagen y al título de la décima foto de la colección:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` también proporciona un método de `RandomSwap` al que se puede llamar para intercambiar la primera foto de la colección con una fotografía elegida aleatoriamente en otro lugar de la colección:

```csharp
mPhotoAlbum.RandomSwap ();
```

Dado que los detalles de implementación de `PhotoAlbum` no son relevantes para comprender `RecyclerView`, el código fuente de `PhotoAlbum` no se muestra aquí. El código fuente para `PhotoAlbum` está disponible en [Photoalbum.CS](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) en la aplicación de ejemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .

### <a name="layout-and-initialization"></a>Diseño e inicialización

El archivo de diseño, **Main. axml**, consta de un único `RecyclerView` dentro de un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Tenga en cuenta que debe usar el nombre completo **Android. support. V7. widget. RecyclerView** porque `RecyclerView` está empaquetado en una biblioteca de soporte. El método `OnCreate` de `MainActivity` inicializa este diseño, crea una instancia del adaptador y prepara el origen de datos subyacente:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Este código hace lo siguiente:

1. Crea una instancia del origen de datos de `PhotoAlbum`.

2. Pasa el origen de datos del álbum de fotos al constructor del adaptador, `PhotoAlbumAdapter` (que se define más adelante en esta guía). 
   Tenga en cuenta que se recomienda pasar el origen de datos como un parámetro al constructor del adaptador. 

3. Obtiene el `RecyclerView` del diseño.

4. Conecta el adaptador a la instancia de `RecyclerView` llamando al método `RecyclerView` `SetAdapter` como se muestra anteriormente.

### <a name="layout-manager"></a>Administrador de diseño

Cada elemento del `RecyclerView` se compone de un `CardView` que contiene una imagen de fotografía y un título de fotografía (los detalles se describen en la sección de [titulares](#view-holder) de la vista a continuación). El `LinearLayoutManager` predefinido se utiliza para diseñar cada `CardView` en una disposición de desplazamiento vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Este código reside en el método `OnCreate` de la actividad principal. El constructor para el administrador de diseño requiere un *contexto*, por lo que el `MainActivity` se pasa con `this` como se ha indicado anteriormente.

En lugar de usar el `LinearLayoutManager`predefinido, puede conectar un administrador de diseño personalizado que muestre dos elementos `CardView` en paralelo, implementando un efecto de animación de activación de página para recorrer la colección de fotografías. Más adelante en esta guía, verá un ejemplo de cómo modificar el diseño mediante el intercambio en un administrador de diseño diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Ver titular

La clase de titular de vista se denomina `PhotoViewHolder`. Cada instancia de `PhotoViewHolder` contiene referencias a los `ImageView` y `TextView` de un elemento de fila asociado, que se distribuye en un `CardView` como diagrama aquí:

[![diagrama de CardView que contiene ImageView y TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva de `RecyclerView.ViewHolder` y contiene propiedades para almacenar las referencias a la `ImageView` y `TextView` que se muestran en el diseño anterior.
`PhotoViewHolder` consta de dos propiedades y un constructor:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

En este ejemplo de código, al constructor de `PhotoViewHolder` se le pasa una referencia a la vista de elemento primario (el `CardView`) que `PhotoViewHolder` ajusta. Tenga en cuenta que siempre reenvía la vista del elemento primario al constructor base. El constructor `PhotoViewHolder` llama a `FindViewById` en la vista de elemento primario para buscar cada una de sus referencias de vista secundaria, `ImageView` y `TextView`, y almacena los resultados en las propiedades `Image` y `Caption`, respectivamente. Posteriormente, el adaptador recupera las referencias de vista de estas propiedades cuando actualiza las vistas secundarias de este `CardView`con nuevos datos.

Para obtener más información sobre `RecyclerView.ViewHolder`, consulte la referencia de la [clase RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="adapter"></a>Adaptador

El adaptador carga cada fila de `RecyclerView` con los datos de una fotografía determinada. En el caso de una fotografía determinada en la posición de la fila *p*, por ejemplo, el adaptador busca los datos asociados en la posición *p* en el origen de datos y copia estos datos en el elemento de fila de la posición *p* de la colección de `RecyclerView`. El adaptador usa el titular de la vista para buscar las referencias de los `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` para esas vistas cuando el usuario se desplaza por la colección de fotografías y reutiliza las vistas.

En **RecyclerViewer**, una clase de adaptador se deriva de `RecyclerView.Adapter` para crear `PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

El miembro `mPhotoAlbum` contiene el origen de datos (el álbum de fotos) que se pasa al constructor; el constructor copia el álbum de fotos en esta variable miembro. Se implementan los siguientes métodos de `RecyclerView.Adapter` necesarios:

- **`OnCreateViewHolder`** &ndash; crea instancias del archivo de diseño del elemento y del portaherramienta de la vista.

- **`OnBindViewHolder`** &ndash; carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificado.

- **`ItemCount`** &ndash; devuelve el número de elementos del origen de datos.

El administrador de diseño llama a estos métodos mientras coloca los elementos dentro del `RecyclerView`. La implementación de estos métodos se examina en las secciones siguientes.

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

El administrador de diseño llama a `OnCreateViewHolder` cuando el `RecyclerView` necesita un nuevo titular de la vista para representar un elemento. `OnCreateViewHolder` aumenta la vista de elementos del archivo de diseño de la vista y ajusta la vista en una nueva instancia de `PhotoViewHolder`. El constructor `PhotoViewHolder` busca y almacena las referencias a las vistas secundarias en el diseño tal y como se describió anteriormente en el titular de la [vista](#view-holder).

Cada elemento de fila se representa mediante una `CardView` que contiene un `ImageView` (para la foto) y un `TextView` (para el título). Este diseño reside en el archivo **PhotoCardView. axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Este diseño representa un elemento de una sola fila en el `RecyclerView`. El método `OnBindViewHolder` (que se describe a continuación) copia los datos del origen de datos en el `ImageView` y `TextView` de este diseño.
`OnCreateViewHolder` infla este diseño para una determinada ubicación de la fotografía en el `RecyclerView` y crea instancias de una nueva instancia de `PhotoViewHolder` (que busca y almacena en caché las referencias a las vistas secundarias de `ImageView` y `TextView` en el diseño de `CardView` asociado) :

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

La instancia de marcador de vista resultante, `vh`, se devuelve al autor de la llamada (el administrador de diseño).

#### <a name="onbindviewholder"></a>OnBindViewHolder

Cuando el administrador de diseño está listo para mostrar una vista determinada en el área visible de la pantalla `RecyclerView`, llama al método de `OnBindViewHolder` del adaptador para rellenar el elemento en la posición de la fila especificada con el contenido del origen de datos. `OnBindViewHolder` obtiene la información de la fotografía de la posición de la fila especificada (el recurso de imagen de la fotografía y la cadena para el título de la fotografía) y copia estos datos en las vistas asociadas. Las vistas se encuentran a través de referencias almacenadas en el objeto de portaherramienta de la vista (que se pasa a través del parámetro `holder`):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

El objeto de titular de la vista pasada debe convertirse primero en el tipo de contenedor de la vista derivada (en este caso, `PhotoViewHolder`) antes de que se use.
El adaptador carga el recurso de imagen en la vista a la que hace referencia la propiedad `Image` del propietario de la vista y copia el texto del título en la vista a la que hace referencia la propiedad `Caption` del propietario de la vista. Esto *enlaza* la vista asociada con sus datos.

Observe que `OnBindViewHolder` es el código que se ocupa directamente de la estructura de los datos. En este caso, `OnBindViewHolder` entiende cómo asignar la posición del elemento `RecyclerView` a su elemento de datos asociado en el origen de datos. En este caso, la asignación es sencilla, ya que la posición puede usarse como un índice de matriz en el álbum de fotos. sin embargo, los orígenes de datos más complejos pueden requerir código adicional para establecer este tipo de asignación.

#### <a name="itemcount"></a>ItemCount

El método `ItemCount` devuelve el número de elementos de la colección de datos. En la aplicación de visor de fotos de ejemplo, el recuento de elementos es el número de fotos del álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obtener más información sobre `RecyclerView.Adapter`, consulte la referencia de la [clase RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

### <a name="putting-it-all-together"></a>Reunir todo

La implementación de `RecyclerView` resultante para la aplicación de foto de ejemplo consta de `MainActivity` código que crea el origen de datos, el administrador de diseño y el adaptador. `MainActivity` crea la instancia de `mRecyclerView`, crea una instancia del origen de datos y el adaptador, y se conecta en el administrador de diseño y el adaptador:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` localiza y almacena en caché las referencias de la vista:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` implementa las tres invalidaciones de método necesarias:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

Cuando este código se compila y se ejecuta, crea la aplicación básica de visualización fotográfica, tal como se muestra en las siguientes capturas de pantalla:

[![dos capturas de pantalla de la aplicación de visualización fotográfica con tarjetas fotográficas de desplazamiento vertical](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Si no se dibujan sombras (como se ha mostrado en la captura de pantalla anterior), edite **Properties/archivo AndroidManifest. XML** y agregue el siguiente valor de atributo al elemento `<application>`:

```xml
android:hardwareAccelerated="true"
```

Esta aplicación básica solo admite la exploración del álbum de fotos. No responde a los eventos táctiles de elementos ni a los cambios en los datos subyacentes. Esta funcionalidad se agrega en [la extensión del ejemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).

### <a name="changing-the-layoutmanager"></a>Cambiar el LayoutManager

Debido a la flexibilidad de `RecyclerView`, es fácil modificar la aplicación para que use un administrador de diseño diferente. En el ejemplo siguiente, se modifica para mostrar el álbum de fotos con un diseño de cuadrícula que se desplaza horizontalmente en lugar de con un diseño lineal vertical. Para ello, se modifica la creación de instancias del administrador de diseño para usar el `GridLayoutManager` como se indica a continuación:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Este cambio de código reemplaza el `LinearLayoutManager` vertical por un `GridLayoutManager` que presenta una cuadrícula formada por dos filas que se desplazan en dirección horizontal. Al compilar y ejecutar la aplicación de nuevo, verá que las fotografías se muestran en una cuadrícula y que el desplazamiento es horizontal en lugar de vertical:

[![captura de pantalla de ejemplo de la aplicación con las fotografías con desplazamiento horizontal en una cuadrícula](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Al cambiar solo una línea de código, es posible modificar la aplicación de visualización de fotografías para que use un diseño diferente con un comportamiento diferente.
Tenga en cuenta que ni el código del adaptador ni el XML de diseño tuvieron que modificarse para cambiar el estilo de diseño. 

En el tema siguiente, [extender el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esta aplicación de ejemplo básica se amplía para controlar los eventos de clic de elemento y actualizar `RecyclerView` cuando cambia el origen de datos subyacente.

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Componentes y funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Extender el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
