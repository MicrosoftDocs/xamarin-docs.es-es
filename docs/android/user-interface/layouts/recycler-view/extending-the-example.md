---
title: Extender el ejemplo de RecyclerView
description: Agregar controladores de eventos de clic de elemento a la aplicación de ejemplo RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: d50971c1ef0064463e576a895729ad84577e1788
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028880"
---
# <a name="extending-the-recyclerview-example"></a>Extender el ejemplo de RecyclerView

La aplicación básica descrita en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) no hace mucho &ndash; simplemente se desplaza y muestra una lista fija de elementos de fotografía para facilitar la exploración. En las aplicaciones reales, los usuarios esperan poder interactuar con la aplicación punteando en los elementos de la pantalla. Además, el origen de datos subyacente puede cambiar (o ser modificado por la aplicación) y el contenido de la pantalla debe permanecer coherente con estos cambios. En las secciones siguientes, aprenderá a controlar los eventos de clic de elemento y a actualizar `RecyclerView` cuando el origen de datos subyacente cambie.

### <a name="handling-item-click-events"></a>Controlar eventos de clic de elemento

Cuando un usuario toca un elemento en el `RecyclerView`, se genera un evento de clic en el elemento para notificar a la aplicación de qué elemento se ha tocado. Este evento no se genera mediante `RecyclerView` &ndash; en su lugar, la vista del elemento (que está incluida en el titular de la vista) detecta los toques y notifica que estos tocan como eventos de clic.

Para ilustrar cómo controlar los eventos de clic de elemento, en los pasos siguientes se explica cómo se modifica la aplicación básica de visualización de fotografías para informar de qué fotografía ha sido tocada por el usuario. Cuando se produce un evento de clic de elemento en la aplicación de ejemplo, tiene lugar la siguiente secuencia:

1. La `CardView` de la fotografía detecta el evento de clic de elemento y lo notifica al adaptador.

2. El adaptador reenvía el evento (con información de posición del elemento) al controlador de clic de elemento de la actividad.

3. El controlador de clic de elemento de la actividad responde al evento de clic del elemento.

En primer lugar, se agrega un miembro de controlador de eventos denominado `ItemClick` a la definición de clase `PhotoAlbumAdapter`:

```csharp
public event EventHandler<int> ItemClick;
```

A continuación, se agrega un método de controlador de eventos de clic de elemento a `MainActivity`.
Este controlador muestra brevemente una notificación del sistema que indica qué elemento de fotografía se ha tocado:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

A continuación, se necesita una línea de código para registrar el controlador de `OnItemClick` con `PhotoAlbumAdapter`. Un buen lugar para hacerlo es inmediatamente después de crear `PhotoAlbumAdapter`: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

En este ejemplo básico, el registro del controlador tiene lugar en el método de `OnCreate` de la actividad principal, pero una aplicación de producción podría registrar el controlador en `OnResume` y anular su registro en `OnPause` &ndash; vea [ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md) de la actividad para obtener más información.

`PhotoAlbumAdapter` llamará ahora a `OnItemClick` cuando reciba un evento de clic del elemento. El siguiente paso consiste en crear un controlador en el adaptador que genere este evento `ItemClick`. El método siguiente, `OnClick`, se agrega inmediatamente después del método de `ItemCount` del adaptador:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Este método `OnClick` es el agente de *escucha* del adaptador para los eventos de clic de elemento de las vistas de elementos. Antes de que se pueda registrar este agente de escucha con una vista de elemento (a través del titular de la vista de la vista de elemento), el constructor de `PhotoViewHolder` debe modificarse para aceptar este método como argumento adicional y registrar `OnClick` con el evento `Click` de la vista de elemento.
Este es el constructor de `PhotoViewHolder` modificado:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

El parámetro `itemView` contiene una referencia al `CardView` que el usuario ha tocado. Tenga en cuenta que la clase base del portaherramienta de la vista conoce la posición del diseño del elemento (`CardView`) que representa (a través de la propiedad `LayoutPosition`) y esta posición se pasa al método `OnClick` del adaptador cuando tiene lugar un evento de clic del elemento. El método `OnCreateViewHolder` del adaptador se modifica para pasar el método de `OnClick` del adaptador al constructor del propietario de la vista:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Ahora, al compilar y ejecutar la aplicación de visualización de fotografías de ejemplo, al pulsar una foto en la pantalla se mostrará una notificación que indica qué fotografía se ha tocado:

[![notificación del sistema de ejemplo que aparece cuando se puntea una tarjeta fotográfica](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

En este ejemplo se muestra un solo enfoque para implementar controladores de eventos con `RecyclerView`. Otro enfoque que podría usarse aquí es colocar los eventos en el titular de la vista y hacer que el adaptador se suscriba a estos eventos. Si la aplicación de foto de ejemplo proporciona una capacidad de edición de fotografías, se necesitarían eventos independientes para el `ImageView` y el `TextView` dentro de cada `CardView`: los toques del `TextView` iniciaría un cuadro de diálogo de `EditView` que permite al usuario editar el título. y los toques en el `ImageView` iniciaría una herramienta de retoque de fotografías que permite al usuario recortar o girar la foto. En función de las necesidades de la aplicación, debe diseñar el mejor método para controlar y responder a los eventos de toque.

Para demostrar cómo se pueden actualizar `RecyclerView` cuando cambia el conjunto de datos, la aplicación de visualización de fotografías de ejemplo se puede modificar para seleccionar aleatoriamente una foto en el origen de datos y cambiarla con la primera foto. En primer lugar, se agrega un botón de **selección aleatoria** al diseño **principal. axml** de la aplicación de fotos de ejemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

A continuación, se agrega el código al final del método `OnCreate` de la actividad principal para buscar el botón `Random Pick` en el diseño y adjuntar un controlador:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Este controlador llama al método `RandomSwap` del álbum de fotos cuando se puntea el botón de **selección aleatoria** . El método `RandomSwap` intercambia una fotografía aleatoriamente con la primera foto del origen de datos y, a continuación, devuelve el índice de la fotografía de intercambio aleatorio. Al compilar y ejecutar la aplicación de ejemplo con este código, al pulsar el botón de **selección aleatoria** no se produce un cambio de pantalla porque el `RecyclerView` no es consciente del cambio en el origen de datos.

Para mantener `RecyclerView` actualizados después de que cambie el origen de datos, se debe modificar el controlador de clics de **selección aleatoria** para llamar al método de `NotifyItemChanged` del adaptador para cada elemento de la colección que ha cambiado (en este caso, dos elementos han cambiado: la primera foto y el Foto intercambiada). Esto hace que `RecyclerView` actualice su pantalla para que sea coherente con el nuevo estado del origen de datos:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

Ahora, cuando se puntea el botón de **selección aleatoria** , `RecyclerView` actualiza la pantalla para mostrar que una foto más abajo en la colección se ha intercambiado con la primera foto de la colección:

[![primera captura de pantalla antes del intercambio, segunda captura de pantalla después del intercambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Por supuesto, se podría haber llamado a `NotifyDataSetChanged` en lugar de realizar las dos llamadas a `NotifyItemChanged`, pero hacerlo haría que `RecyclerView` actualizara toda la colección, aunque solo hubieran cambiado dos elementos de la colección. Llamar a `NotifyItemChanged` es significativamente más eficaz que llamar a `NotifyDataSetChanged`.

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerViewer (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Componentes y funcionalidad de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
