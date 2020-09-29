---
title: Personalización de la apariencia de ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 41b4bb25aa170abeb58a92058f4bfb8b78af35ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457242"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>Personalización de la apariencia de un control ListView con Xamarin. Android

La apariencia de un control ListView viene determinada por el diseño de las filas que se muestran. Para cambiar la apariencia de un `ListView` , utilice un diseño de fila diferente.

## <a name="built-in-row-views"></a>Vistas de fila integradas

Hay doce vistas integradas a las que se puede hacer referencia mediante **Android. resource. layout**:

- **TestListItem** &ndash; Una sola línea de texto con un formato mínimo.

- **SimpleListItem1** &ndash; Una sola línea de texto.

- **SimpleListItem2** &ndash; Dos líneas de texto.

- **SimpleSelectableListItem** &ndash; Una sola línea de texto que admite la selección de uno o varios elementos (agregado en el nivel de API 11).

- **SimpleListItemActivated1** &ndash; Similar a SimpleListItem1, pero el color de fondo indica cuándo se selecciona una fila (agregada en el nivel de API 11).

- **SimpleListItemActivated2** &ndash; Similar a SimpleListItem2, pero el color de fondo indica cuándo se selecciona una fila (agregada en el nivel de API 11).

- **SimpleListItemChecked** &ndash; Muestra las marcas de verificación para indicar la selección.

- **SimpleListItemMultipleChoice** &ndash; Muestra las casillas para indicar la selección de varias opciones.

- **SimpleListItemSingleChoice** &ndash; Muestra los botones de radio para indicar la selección mutuamente excluyente.

- **TwoLineListItem** &ndash; Dos líneas de texto.

- **ActivityListItem** &ndash; Una sola línea de texto con una imagen.

- **SimpleExpandableListItem** &ndash; Agrupa las filas por categorías y cada grupo se puede expandir o contraer.

Cada vista de fila integrada tiene un estilo integrado asociado. Estas capturas de pantallas muestran cómo aparece cada vista:

[![Capturas de pantallas de TestListItem, SimpleSelectableListItem, SimpleListitem1 y SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Capturas de pantallas de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked y SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Capturas de pantallas de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem y SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

El archivo de ejemplo **BuiltInViews/HomeScreenAdapter. CS** (en la solución **BuiltInViews** ) contiene el código para generar las pantallas de elementos de lista no expansibles. La vista se establece en el método de la siguiente `GetView` manera:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

A continuación, se pueden establecer las propiedades de la vista haciendo referencia a los identificadores de control estándar `Text1` `Text2` y `Icon` en `Android.Resource.Id` (no establezca las propiedades que la vista no contiene o se producirá una excepción):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

El archivo de ejemplo **BuiltInExpandableViews/ExpandableScreenAdapter. CS** (en la solución **BuiltInViews** ) contiene el código para generar la pantalla SimpleExpandableListItem. La vista de grupo se establece en el método de la siguiente `GetGroupView` manera:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La vista secundaria se establece en el método de la siguiente `GetChildView` manera:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

Las propiedades de la vista de grupo y la vista secundaria se pueden establecer haciendo referencia a los `Text1` identificadores estándar y de `Text2` control, tal y como se muestra anteriormente. La captura de pantalla SimpleExpandableListItem (mostrada anteriormente) proporciona un ejemplo de una vista de grupo de una línea (SimpleExpandableListItem1) y una vista secundaria de dos líneas (SimpleExpandableListItem2). Como alternativa, se puede configurar la vista de grupo para dos líneas (SimpleExpandableListItem2) y la vista secundaria se puede configurar para una línea (SimpleExpandableListItem1), o ambas vistas de grupo y secundarias pueden tener el mismo número de líneas. 

## <a name="accessories"></a>Accessories

Las filas pueden tener accesorios agregados a la derecha de la vista para indicar el estado de la selección:

- **SimpleListItemChecked** &ndash; Crea una lista de selección única con una comprobación como indicador.

- **SimpleListItemSingleChoice** &ndash; Crea listas de tipo botón de radio donde solo se puede elegir una opción.

- **SimpleListItemMultipleChoice** &ndash; Crea listas de tipo de casilla donde se pueden elegir varias opciones.

Los accesorios anteriores se muestran en las pantallas siguientes, en su orden respectivo:

[![Capturas de pantallas de SimpleListItemChecked, SimpleListItemSingleChoice y SimpleListItemMultipleChoice con accesorios](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Para mostrar uno de estos accesorios, pase el identificador de recurso de diseño necesario al adaptador y, a continuación, establezca manualmente el estado de selección para las filas necesarias. En esta línea de código se muestra cómo crear y asignar una `Adapter` mediante uno de estos diseños:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

El `ListView` mismo es compatible con los distintos modos de selección, independientemente del accesorio que se muestre. Para evitar confusiones, use `Single` el modo de selección con `SingleChoice` accesorios y el `Checked` `Multiple` modo o con el `MultipleChoice` estilo. El modo de selección se controla mediante la `ChoiceMode` propiedad de `ListView` .

### <a name="handling-api-level"></a>Control de nivel de API

Las versiones anteriores de Xamarin. Android implementaban las enumeraciones como propiedades de entero. La versión más reciente ha introducido los tipos de enumeración de .NET adecuados, lo que facilita la detección de las opciones posibles.

En función del nivel de API de destino, `ChoiceMode` es un entero o una enumeración. El archivo de ejemplo **AccessoryViews/HomeScreen. CS** tiene un bloque comentado si desea tener como destino la API de Gingerbread:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```

### <a name="selecting-items-programmatically"></a>Seleccionar elementos mediante programación

La configuración manual de los elementos seleccionados se realiza con el `SetItemChecked` método (se puede llamar varias veces para la selección múltiple):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

El código también debe detectar las selecciones únicas de manera diferente de las selecciones múltiples. Para determinar qué fila se ha seleccionado en `Single` el modo, use la `CheckedItemPosition` propiedad de entero:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Para determinar qué filas se han seleccionado en `Multiple` modo, debe recorrer el `CheckedItemPositions` `SparseBooleanArray` . Una matriz dispersa es como un diccionario que solo contiene entradas en las que se ha cambiado el valor, por lo que debe atravesar toda la matriz buscando `true` valores para saber lo que se ha seleccionado en la lista, tal y como se muestra en el siguiente fragmento de código:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

## <a name="creating-custom-row-layouts"></a>Crear diseños de fila personalizados

Las cuatro vistas de filas integradas son muy sencillas. Para mostrar diseños más complejos (como una lista de correos electrónicos, tweets o información de contacto), se necesita una vista personalizada. Las vistas personalizadas se declaran generalmente como archivos AXML en el directorio de **recursos/diseño** y, a continuación, se cargan con su identificador de recurso mediante un adaptador personalizado. La vista puede contener cualquier número de clases de presentación (como TextViews, ImageViews y otros controles) con colores personalizados, fuentes y diseño.

Este ejemplo difiere de los ejemplos anteriores de varias maneras:

- Hereda de `Activity` , no de `ListActivity` . Puede personalizar las filas de cualquier `ListView` , pero también se pueden incluir otros controles en un `Activity` diseño (por ejemplo, un encabezado, botones u otros elementos de la interfaz de usuario). En este ejemplo se agrega un encabezado encima de `ListView` para mostrar.

- Requiere un archivo de diseño de AXML para la pantalla. en los ejemplos anteriores, `ListActivity` no requiere un archivo de diseño. Este AXML contiene una `ListView` declaración de control.

- Requiere un archivo de diseño AXML para representar cada fila. Este archivo AXML contiene los controles de texto e imagen con la configuración personalizada de fuente y color.

- Usa un archivo XML de selector personalizado opcional para establecer la apariencia de la fila cuando está seleccionada.

- La `Adapter` implementación de devuelve un diseño personalizado de la  `GetView` invalidación.

- `ItemClick` se debe declarar de forma diferente (se adjunta un controlador `ListView.ItemClick` de eventos en lugar de un reemplazo  `OnListItemClick` en `ListActivity` ).

Estos cambios se detallan a continuación, empezando por crear la vista de la actividad y la vista de fila personalizada y, a continuación, abarcando las modificaciones en el adaptador y la actividad para representarlas.

### <a name="adding-a-listview-to-an-activity-layout"></a>Agregar un control ListView a un diseño de actividad

Dado que ya `HomeScreen` no se hereda de, `ListActivity` no tiene una vista predeterminada, por lo que se debe crear un archivo de diseño AXML para la vista de HomeScreen. En este ejemplo, la vista tendrá un encabezado (con `TextView` ) y un `ListView` para mostrar los datos. El diseño se define en el archivo **Resources/layout/HomeScreen. axml** que se muestra aquí:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

La ventaja de usar un `Activity` con un diseño personalizado (en lugar de un `ListActivity` ) radica en poder agregar controles adicionales a la pantalla, como el encabezado `TextView` de este ejemplo.

### <a name="creating-a-custom-row-layout"></a>Crear un diseño de fila personalizado

Se requiere otro archivo de diseño AXML para que contenga el diseño personalizado para cada fila que aparecerá en la vista de lista. En este ejemplo, la fila tendrá un fondo verde, texto marrón y imagen alineada a la derecha. El marcado XML de Android para declarar este diseño se describe en **Resources/layout/CustomView. axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Aunque un diseño de fila personalizado puede contener muchos controles diferentes, el rendimiento del desplazamiento puede verse afectado por diseños complejos y el uso de imágenes (especialmente si tienen que cargarse a través de la red). Vea el artículo de Google para obtener más información sobre cómo solucionar problemas de rendimiento de desplazamiento.

### <a name="referencing-a-custom-row-view"></a>Hacer referencia a una vista de fila personalizada

La implementación del ejemplo de adaptador personalizado está en `HomeScreenAdapter.cs` . El método clave es `GetView` donde carga el AXML personalizado mediante el identificador de recurso `Resource.Layout.CustomView` y, a continuación, establece las propiedades de cada uno de los controles de la vista antes de devolverlos. Se muestra la clase de adaptador completa:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```

### <a name="referencing-the-custom-listview-in-the-activity"></a>Hacer referencia al control ListView personalizado en la actividad

Dado que la `HomeScreen` clase ahora hereda de `Activity` , `ListView` se declara un campo en la clase para que contenga una referencia al control declarado en AXML:

```csharp
ListView listView;
```

A continuación, la clase debe cargar el AXML de diseño personalizado de la actividad mediante el `SetContentView` método. Después, puede encontrar el `ListView` control en el diseño y, a continuación, crea y asigna el adaptador y asigna el controlador de clic. Aquí se muestra el código para el método de creación:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Por último, `ItemClick` se debe definir el controlador; en este caso, solo muestra un `Toast` mensaje:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La pantalla resultante tiene el siguiente aspecto:

[![Captura de pantalla del CustomRowView resultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)

### <a name="customizing-the-row-selector-color"></a>Personalizar el color del selector de filas

Cuando se toca una fila, debe resaltarse para los comentarios de los usuarios. Cuando una vista personalizada especifica como color de fondo como **CustomView. axml** , también invalida el resaltado de la selección. Esta línea de código de **CustomView. axml** establece el fondo en verde claro, pero también significa que no hay ningún indicador visual cuando se toca la fila:

```xml
android:background="#FFDAFF7F"
```

Para volver a habilitar el comportamiento de resaltado y también para personalizar el color que se usa, establezca el atributo Background en un selector personalizado en su lugar. El selector declarará el color de fondo predeterminado y el color de resaltado. El archivo **Resources/drawable/CustomSelector.xml** contiene la siguiente declaración:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Para hacer referencia al selector personalizado, cambie el atributo Background de **CustomView. axml** a:

```xml
android:background="@drawable/CustomSelector"
```

Una fila seleccionada y el mensaje correspondiente ahora tienen el siguiente `Toast` aspecto:

[![Una fila seleccionada en naranja, con el mensaje del sistema que muestra el nombre de la fila seleccionada](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)

### <a name="preventing-flickering-on-custom-layouts"></a>Evitar el parpadeo en los diseños personalizados

Android intenta mejorar el rendimiento del `ListView` desplazamiento mediante el almacenamiento en caché de la información de diseño. Si tiene listas de desplazamiento de datos de gran tamaño, también debe establecer la `android:cacheColorHint` propiedad en la `ListView` declaración de la definición de AXML de la actividad (en el mismo valor de color que el fondo del diseño de fila personalizado). Si no se incluye esta sugerencia, se podría producir un "parpadeo" cuando el usuario se desplace a través de una lista con colores de fondo de fila personalizados.

## <a name="related-links"></a>Vínculos relacionados

- [BuiltInViews (ejemplo)](/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (ejemplo)](/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (ejemplo)](/samples/xamarin/monodroid-samples/customrowview)