---
title: Autocompletar para Xamarin. Android
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/31/2018
ms.openlocfilehash: 5a11ab06321b890d8f1f5a35a8456b06a900fbcc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029376"
---
# <a name="auto-complete-for-xamarinandroid"></a>Autocompletar para Xamarin. Android

`AutoCompleteTextView` es un elemento de la vista de texto editable que muestra sugerencias de finalización automáticamente mientras el usuario está escribiendo. La lista de sugerencias se muestra en un menú desplegable en el que el usuario puede elegir un elemento del que reemplazar el contenido del cuadro de edición.

![Ejemplo de autocompletar](images/auto-complete.png)

## <a name="overview"></a>Información general

Para crear un widget de entrada de texto que proporcione sugerencias de autocompletar, use el [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget. Las sugerencias se reciben de una colección de cadenas asociadas al widget a través de un [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter).

En este tutorial, creará una [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget que proporciona sugerencias para un nombre de país.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

El [`TextView`](xref:Android.Widget.TextView) es una etiqueta que presenta el [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget.

## <a name="tutorial"></a>Tutorial

Inicie un nuevo proyecto denominado *HelloAutoComplete*.

Cree un archivo XML denominado `list_item.xml` y guárdelo en la carpeta **recursos/diseño** . Establezca la acción de compilación de este archivo en `AndroidResource`. Edite el archivo para que tenga el siguiente aspecto:

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView> 
```

Este archivo define una [`TextView`](xref:Android.Widget.TextView) simple que se utilizará para cada elemento que aparece en la lista de sugerencias.

Abra **Resources/layout/main. axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

Abra **MainActivity.CS** e inserte el código siguiente para el [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

Una vez que la vista de contenido se establece en el diseño de `main.xml`, el [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget se captura del diseño con [`FindViewById`](xref:Android.App.Activity.FindViewById*). A continuación, se inicializa una nueva [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) para enlazar el `list_item.xml` diseño a cada elemento de la lista de la matriz de cadenas de `COUNTRIES` (definida en el paso siguiente). Por último, se llama a `SetAdapter()` para asociar el [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) a la [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget para que la matriz de cadenas rellene la lista de sugerencias.

Dentro de la clase `MainActivity`, agregue la matriz de cadenas:

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

Esta es la lista de sugerencias que se proporcionarán en una lista desplegable cuando el usuario escribe en el [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget.

Ejecute la aplicación. A medida que escribe, debería ver algo parecido a esto:

[![captura de pantalla de autocompletar de ejemplo con nombres que contienen "CA"](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)

## <a name="more-information"></a>Más información

Tenga en cuenta que el uso de una matriz de cadenas codificadas de forma rígida no es una práctica de diseño recomendada, ya que el código de la aplicación debe centrarse en el comportamiento, no en el contenido. El contenido de la aplicación, como las cadenas, se debe externalizar desde el código para facilitar modificaciones en el contenido y facilitar la localización del contenido. Las cadenas codificadas de forma rígida se usan en este tutorial solo para simplificar y centrarse en el [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget. En su lugar, la aplicación debe declarar dichas matrices de cadenas en un archivo XML. Esto se puede hacer con un recurso de `<string-array>` en el archivo `res/values/strings.xml` del proyecto. Por ejemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
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

Para usar estas cadenas de recursos para el [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), reemplace el [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) original
línea del constructor con lo siguiente:

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```

### <a name="references"></a>Referencias

- [Receta AutoCompleteTextView](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; proyecto de ejemplo de Xamarin. Android para el `AutoCompleteTextView`
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la [licencia de atribución de Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/). Este tutorial se basa en el [tutorial de autocompletar de Android *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)._
