---
title: Usar ListView en Xamarin. Android
description: ListView es un componente importante de la interfaz de usuario de las aplicaciones de Android; se usa en todas partes desde listas cortas de opciones de menú hasta largas listas de contactos o favoritos de Internet. Proporciona una manera sencilla de presentar una lista de desplazamiento de filas a las que se puede dar formato con un estilo integrado o personalizarse en gran medida.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: c40d21a95db564d0fdcbea5772a88e94446c07ce
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887520"
---
# <a name="xamarinandroid-listview"></a>ListView de Xamarin. Android

_ListView es un componente importante de la interfaz de usuario de las aplicaciones de Android; se usa en todas partes desde listas cortas de opciones de menú hasta largas listas de contactos o favoritos de Internet. Proporciona una manera sencilla de presentar una lista de desplazamiento de filas a las que se puede dar formato con un estilo integrado o personalizarse en gran medida._

## <a name="overview"></a>Información general

Las vistas de lista y los adaptadores se incluyen en los bloques de creación más fundamentales de las aplicaciones de Android. La `ListView` clase proporciona una manera flexible de presentar los datos, tanto si se trata de un menú corto como una lista de desplazamiento largo. Proporciona características de facilidad de uso, como el desplazamiento rápido, los índices y una o varias selecciones para ayudarle a crear interfaces de usuario fáciles de utilizar para las aplicaciones. Una instancia `ListView` necesita un *adaptador* para llenarlo con los datos que contienen las vistas de la fila.

En esta guía se explica cómo `ListView` implementar y las `Adapter` distintas clases de Xamarin. Android. También se muestra cómo personalizar la apariencia de un `ListView`y se describe la importancia del reutilización de las filas para reducir el consumo de memoria. También se explica cómo afecta `ListView` y `Adapter` utiliza el ciclo de vida de la actividad. Si está trabajando en `ListView` aplicaciones multiplataforma con Xamarin. iOS, el control es estructuralmente similar a iOS `UITableView` (y `UITableViewSource`Android `Adapter` es similar a).

En primer lugar, un tutorial breve `ListView` presenta el con un ejemplo de código básico. A continuación, se proporcionan vínculos a temas más avanzados que le ayudarán a usar `ListView` en aplicaciones reales.

> [!NOTE]
> El `RecyclerView` widget es una versión más avanzada y flexible de `ListView`. Dado `RecyclerView` que está diseñado para ser el sucesor `ListView` de ( `GridView`y) `ListView` , se recomienda usar `RecyclerView` en lugar de para el nuevo desarrollo de aplicaciones. Para obtener más información, vea [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

## <a name="listview-tutorial"></a>Tutorial de ListView

[`ListView`](xref:Android.Widget.ListView)es un[`ViewGroup`](xref:Android.Views.ViewGroup)
que crea una lista de elementos desplazables. Los elementos de la lista se insertan automáticamente en la [`IListAdapter`](xref:Android.Widget.IListAdapter)lista mediante.

En este tutorial, creará una lista desplazable de nombres de país que se leen de una matriz de cadenas. Cuando se selecciona un elemento de lista, el mensaje del sistema muestra la posición del elemento en la lista.

Inicie un nuevo proyecto denominado **HelloListView**.

Cree un archivo XML con el nombre **list_item. XML** y guárdelo dentro de la carpeta **recursos/diseño/** . Inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Este archivo define el diseño de cada elemento que se colocará en [`ListView`](xref:Android.Widget.ListView)el.

Abra `MainActivity.cs` y modifique la clase para extender [`ListActivity`](xref:Android.App.ListActivity) (en lugar de [`Activity`](xref:Android.App.Activity)):

```csharp
public class MainActivity : ListActivity
{
```

Inserte el código siguiente para el [`OnCreate()`](xref:Android.App.Activity.OnCreate*)método):

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Tenga en cuenta que esto no carga un archivo de diseño para la actividad (lo que suele [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)hacer con)).
En su lugar, al establecer[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
la propiedad agrega automáticamente un[`ListView`](xref:Android.Widget.ListView)
para rellenar toda la pantalla [`ListActivity`](xref:Android.App.ListActivity)de.
Este método toma un [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1), que administra la matriz de elementos de lista que se colocará [`ListView`](xref:Android.Widget.ListView)en.
El[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
el constructor toma la [`Context`](xref:Android.Content.Context)aplicación, la descripción del diseño de cada elemento de la lista (creada en el paso anterior `T[]` ) y un o[`Java.Util.IList<T>`](xref:Java.Util.IList)
matriz de objetos que se van a insertar en el[`ListView`](xref:Android.Widget.ListView)
(se define a continuación).

El[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
la propiedad activa el [`ListView`](xref:Android.Widget.ListView)filtrado de texto para, de modo que cuando el usuario comience a escribir, la lista se filtre.

El[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
el evento se puede utilizar para suscribir Controladores para clics. Cuando un elemento de la[`ListView`](xref:Android.Widget.ListView)
se hace clic en, se llama al controlador y a un[`Toast`](xref:Android.Widget.Toast)
se muestra el mensaje con el texto del elemento en el que se hizo clic.

Puede usar los diseños de elementos de lista proporcionados por la plataforma en lugar de definir su propio archivo [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)de diseño para.
Por ejemplo, pruebe a `Android.Resource.Layout.SimpleListItem1` usar en lugar `Resource.Layout.list_item`de.

Agregue la siguiente `using` instrucción:

```csharp
using System;
```

A continuación, agregue la siguiente matriz de cadenas como un `MainActivity`miembro de:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Esta es la matriz de cadenas que se colocará en [`ListView`](xref:Android.Widget.ListView)el.

Ejecute la aplicación. Puede desplazarse por la lista o escribir para filtrarla y, a continuación, hacer clic en un elemento para ver un mensaje. Verá algo parecido a esto:

[![Captura de pantalla de ejemplo de ListView con nombres de país](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Tenga en cuenta que el uso de una matriz de cadenas codificadas de forma rígida no es el mejor procedimiento de diseño. Una se usa en este tutorial para simplificar, a fin de demostrar el[`ListView`](xref:Android.Widget.ListView)
widget. El procedimiento recomendado consiste en hacer referencia a una matriz de cadenas definida por un recurso externo, como `string-array` con un recurso en el archivo de **recursos/valores/Strings. XML** del proyecto. Por ejemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

Para usar estas cadenas de recursos para [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1), reemplace el original[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
línea con lo siguiente:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

Ejecute la aplicación. Verá algo parecido a esto:

[![Captura de pantalla de ejemplo de ListView con una lista de nombres más pequeña](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>Continuar con ListView

Los temas restantes (vinculados a continuación) tienen una visión completa del trabajo `ListView` con la clase y los distintos tipos de tipos de adaptador que puede usar con él. La estructura es como se detalla a continuación:

- **Apariencia visual** &ndash; Partes`ListView` del control y cómo funcionan.

- **Clases** de Información general de las clases utilizadas para mostrar `ListView`un. &ndash;

- **Mostrar datos en un control ListView** Cómo mostrar una lista simple de datos, cómo implementar `ListView's` características de facilidad de uso, cómo usar diferentes diseños de fila integrados y cómo los adaptadores ahorran memoria mediante la reutilización de las vistas de fila. &ndash;

- **Apariencia personalizada** Cambiar el estilo `ListView` de con diseños personalizados, fuentes y colores. &ndash;

- **Usar SQLite** Cómo Mostrar datos de una base de datos SQLite con `CursorAdapter`un. &ndash;

- **Ciclo de vida de actividad** Consideraciones de diseño al `ListView` implementar actividades, incluido el lugar del ciclo de vida, debe rellenar los datos y cuándo liberar recursos. &ndash;

La discusión (dividida en seis partes) comienza con una visión general `ListView` de la propia clase antes de introducir ejemplos más complejos de cómo usarlas.

- [Elementos y funcionalidad de ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
- [Rellenar un control ListView con datos](~/android/user-interface/layouts/list-view/populating.md)
- [Personalización de la apariencia de ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
- [Uso de CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
- [Uso de ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
- [ListView y ciclo de vida de actividad](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Resumen

Este conjunto de temas se `ListView` ha introducido y proporcionado algunos ejemplos de cómo usar las características integradas `ListActivity`de. Se han explicado las implementaciones personalizadas de `ListView` que permitían diseños multicolor y el uso de una base de datos de SQLite, y se toca brevemente en la relevancia del ciclo de vida de la actividad en la `ListView` implementación.


## <a name="related-links"></a>Vínculos relacionados

- [AccessoryViews (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
- [Tutorial del ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Trabajar con tablas y celdas (en Xamarin. iOS)](~/ios/user-interface/controls/tables/index.md)
- [Referencia de clases de ListView](xref:Android.Widget.ListView)
- [Referencia de la clase ListActivity](xref:Android.App.ListActivity)
- [Referencia de la clase BaseAdapter](xref:Android.Widget.BaseAdapter)
- [Referencia de la clase ArrayAdapter](xref:Android.Widget.ArrayAdapter)
- [CursorAdapter (referencia de clase)](xref:Android.Widget.CursorAdapter)
