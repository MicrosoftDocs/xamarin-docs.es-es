---
title: Rellenar un control ListView de Xamarin. Android con datos
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: e934dd0f35b7c734228d637fe646d0e2c20e9dad
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758623"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>Rellenar un control ListView de Xamarin. Android con datos

Para agregar filas a un `ListView` , debe agregarlo al diseño e implementar un `IListAdapter` con métodos que las `ListView` llamadas a se rellenan a sí mismos. Android incluye las clases `ListActivity` y `ArrayAdapter` integradas que puede usar sin definir ningún código o XML de diseño personalizado. La `ListActivity` clase crea automáticamente un `ListView` y expone una `ListAdapter` propiedad para proporcionar las vistas de fila que se van a mostrar a través de un adaptador.

Los adaptadores integrados toman un identificador de recurso de vista como parámetro que se utiliza para cada fila. Puede usar recursos integrados como los de `Android.Resource.Layout` para que no tenga que escribir los suyos propios.

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Usar la cadena ListActivity&lt;y ArrayAdapter&gt;

En el ejemplo **BasicTable/HomeScreen. CS** se muestra cómo usar estas clases para mostrar en solo unas pocas líneas de código: `ListView`

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```

### <a name="handling-row-clicks"></a>Controlar los clics de fila

`ListView` Normalmente, también permitirá al usuario tocar una fila para realizar alguna acción (como reproducir una canción, llamar a un contacto o Mostrar otra pantalla). Para responder a los toques de los usuarios, debe haber un método más `ListActivity` implementado de la &ndash; `OnListItemClick` &ndash; siguiente manera:

[![Captura de pantalla de un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Ahora el usuario puede tocar una fila y aparecerá `Toast` una alerta:

[![Captura de pantalla de la notificación del sistema que aparece cuando se toca una fila](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>Implementar un ListAdapter

`ArrayAdapter<string>`es excelente debido a su simplicidad, pero es extremadamente limitado. Sin embargo, a menudo se tiene una colección de entidades de negocio, en lugar de solo las cadenas que desea enlazar.
Por ejemplo, si los datos constan de una colección de clases Employee, es posible que desee que la lista muestre los nombres de cada empleado. Para personalizar el comportamiento de un `ListView` control para controlar qué datos se muestran, debe implementar una subclase `BaseAdapter` de invalidar los cuatro elementos siguientes:

- **Recuento** &ndash; Para indicar al control cuántas filas hay en los datos.

- **GetView (** &ndash; Para devolver una vista para cada fila, rellenado con datos.
    Este método tiene un parámetro para `ListView` que se pase en una fila existente sin usar para volver a usar.

- **GetItemId** &ndash; Devuelve un identificador de fila (normalmente, el número de fila, aunque puede ser cualquier valor largo que desee).

- Este indexador &ndash; **[int]** para devolver los datos asociados a un número de fila determinado.

En el código de ejemplo de **BasicTableAdapter/HomeScreenAdapter. CS** se muestra cómo `BaseAdapter`subclase:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```

### <a name="using-a-custom-adapter"></a>Usar un adaptador personalizado

El uso del adaptador personalizado es similar al integrado `ArrayAdapter`, pasando un `context` y el `string[]` de los valores que se van a mostrar:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Dado que en este ejemplo se usa el mismo`SimpleListItem1`diseño de fila (), la aplicación resultante tendrá el mismo aspecto que el ejemplo anterior.

### <a name="row-view-re-use"></a>Reutilización de la vista de filas

En este ejemplo solo hay seis elementos. Dado que la pantalla puede caber en ocho, no es necesario volver a usar ninguna fila. Sin embargo, al mostrar cientos o miles de filas, sería un desperdicio de memoria para crear cientos o miles de `View` objetos cuando solo ocho caben en la pantalla al mismo tiempo. Para evitar esta situación, cuando una fila desaparece de la pantalla, su vista se coloca en una cola para su reutilización. A medida que el usuario se desplaza `ListView` , `GetView` las llamadas para solicitar nuevas vistas &ndash; para mostrar si están disponibles pasa una vista no utilizada `convertView` en el parámetro. Si este valor es null, el código debe crear una nueva instancia de vista; de lo contrario, puede volver a establecer las propiedades de ese objeto y volver a utilizarlo.

El `GetView` método debe seguir este patrón para volver a usar las vistas de fila:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Las implementaciones de adaptador personalizado *siempre* deben volver a `convertView` usar el objeto antes de crear nuevas vistas para asegurarse de que no se queden sin memoria al mostrar listas largas.

`CursorAdapter`Algunas implementaciones de adaptador (como) no tienen un `GetView` método, sino que requieren dos métodos `NewView` diferentes y `BindView` que aplican la reutilización de filas al separar las responsabilidades `GetView` de en dos modalidades. Hay un `CursorAdapter` ejemplo más adelante en el documento.

## <a name="enabling-fast-scrolling"></a>Habilitar el desplazamiento rápido

El desplazamiento rápido ayuda al usuario a desplazarse por las listas largas proporcionando un "controlador" adicional que actúa como una barra de desplazamiento para tener acceso directamente a una parte de la lista. En esta captura de pantalla se muestra el controlador de desplazamiento rápido:

[![Captura de pantalla del desplazamiento rápido con un controlador de desplazamiento](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Hacer que el controlador de desplazamiento rápido aparezca es tan sencillo como establecer la `FastScrollEnabled` propiedad en `true`:

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>Agregar un índice de sección

Un índice de sección proporciona información adicional a los usuarios cuando se desplaza rápidamente a través de una larga &ndash; lista que muestra la "sección" a la que se han desplazado. Para que el índice de la sección aparezca, la subclase del adaptador `ISectionIndexer` debe implementar la interfaz para proporcionar el texto del índice en función de las filas que se muestran:

[![Captura de pantalla de H que aparece encima de la sección que empieza por H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Para implementar `ISectionIndexer` , debe agregar tres métodos a un adaptador:

- **GetSections** &ndash; Proporciona la lista completa de títulos de índice de la sección que se pueden mostrar. Este método requiere una matriz de objetos de Java, por lo que el código `Java.Lang.Object[]` debe crear un objeto a partir de una colección .net. En nuestro ejemplo, devuelve una lista de los caracteres iniciales de la lista como `Java.Lang.String` .

- **GetPositionForSection** &ndash; Devuelve la primera posición de fila de un índice de sección determinado.

- **GetSectionForPosition** &ndash; Devuelve el índice de la sección que se va a mostrar para una fila determinada.

El archivo `SectionIndex/HomeScreenAdapter.cs` de ejemplo implementa esos métodos y código adicional en el constructor. El constructor crea el índice de la sección recorriendo en bucle todas las filas y extrayendo el primer carácter del título (los elementos ya deben estar ordenados para que funcione).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

Con las estructuras de datos creadas, `ISectionIndexer` los métodos son muy sencillos:

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

Los títulos de índice de la sección no necesitan asignar 1:1 a las secciones reales. Este es el motivo `GetPositionForSection` por el cual existe el método.
`GetPositionForSection`ofrece la oportunidad de asignar los índices que se encuentran en la lista de índices a las secciones que se encuentran en la vista de lista. Por ejemplo, puede tener una "z" en el índice, pero es posible que no tenga una sección de tabla para cada letra, por lo que en lugar de la asignación "z" a 26, puede asignarse a 25 o 24, o al índice de sección "z" que debe asignarse.

## <a name="related-links"></a>Vínculos relacionados

- [BasicTableAndroid (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
