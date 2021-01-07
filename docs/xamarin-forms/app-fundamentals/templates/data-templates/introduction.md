---
title: Introducción a las plantillas de datos de Xamarin.Forms
description: Las plantillas de datos de Xamarin.Forms permiten definir la presentación de los datos en los controles admitidos. En este artículo se ofrece una introducción a las plantillas de datos y se analiza por qué son necesarias.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4df1afb80cc8b261d9bc6d022fe814fd411c0c3c
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940088"
---
# <a name="introduction-to-no-locxamarinforms-data-templates"></a>Introducción a las plantillas de datos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Las plantillas de datos de Xamarin.Forms permiten definir la presentación de los datos en los controles admitidos. En este artículo se ofrece una introducción a las plantillas de datos y se analiza por qué son necesarias._

Considere la posibilidad de un elemento [`ListView`](xref:Xamarin.Forms.ListView) que muestra una colección de objetos `Person`. En el ejemplo de código siguiente se muestra la definición de la clase `Person`:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La clase `Person` define las propiedades `Name`, `Age` y `Location`, que se pueden establecer al crear un objeto `Person`. El objeto [`ListView`](xref:Xamarin.Forms.ListView) se usa para mostrar la colección de objetos `Person`, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Se agregan elementos al objeto [`ListView`](xref:Xamarin.Forms.ListView) en XAML mediante la inicialización de la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) a partir de una matriz de instancias de `Person`.

> [!NOTE]
> Tenga en cuenta que el elemento `x:Array` requiere un atributo `Type` que indica el tipo de los elementos de la matriz.

La página de C# equivalente se muestra en el ejemplo siguiente de código, en el que se inicializa la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) en un elemento `List` de instancias de `Person`:

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[`ListView`](xref:Xamarin.Forms.ListView) llama a `ToString` cuando se muestran los objetos de la colección. Como no hay ninguna invalidación de `Person.ToString`, `ToString` devuelve el nombre de tipo de cada objeto, como se muestra en las capturas de pantalla siguientes:

![Objeto ListView sin una plantilla de datos](introduction-images/no-data-template.png)

El objeto `Person` puede invalidar el método `ToString` para mostrar datos significativos, como se muestra en el ejemplo de código siguiente:

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Como resultado, [`ListView`](xref:Xamarin.Forms.ListView) muestra el valor de propiedad `Person.Name` para cada objeto de la colección, como se muestra en las capturas de pantalla siguientes:

![Objeto ListView con una plantilla de datos](introduction-images/override-tostring.png)

La invalidación de `Person.ToString` podría devolver una cadena con formato formada por las propiedades `Name`, `Age` y `Location`. Pero este enfoque solo ofrece un control limitado sobre la apariencia de cada elemento de datos. Para obtener más flexibilidad, se puede crear un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que defina la apariencia de los datos.

## <a name="creating-a-datatemplate"></a>Creación de una plantilla de datos

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se usa para especificar la apariencia de los datos, y normalmente se utiliza el enlace de datos para mostrar los datos. Un escenario de uso común es cuando se muestran datos de una colección de objetos en un control [`ListView`](xref:Xamarin.Forms.ListView). Por ejemplo, cuando se enlaza un elemento `ListView` a una colección de objetos `Person`, la propiedad `ListView.ItemTemplate` se establecerá en un elemento `DataTemplate` que define la apariencia de cada objeto `Person` de `ListView`. `DataTemplate` contendrá los elementos que se enlazan a los valores de propiedad de cada objeto `Person`. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

Una instancia de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se coloca como un elemento secundario directo de las propiedades enumeradas anteriormente se conoce como una *plantilla insertada*. Como alternativa, se puede definir un elemento `DataTemplate` como un recurso de nivel de control, de página o de aplicación. La elección de dónde se puede definir una instancia de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) afecta a dónde se puede usar:

- Una instancia de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definida en el nivel de control solo se puede aplicar al control.
- Un instancia de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definida en el nivel de página se puede aplicar a varios controles válidos de la página.
- Una instancia de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definida en el nivel de aplicación se puede aplicar a los controles válidos de toda la aplicación.

Las plantillas de datos situadas más abajo en la jerarquía de vistas tienen prioridad sobre las definidas más arriba cuando comparten atributos `x:Key`. Por ejemplo, una plantilla de datos de nivel de aplicación se reemplazará por una plantilla de datos de nivel de página, y una plantilla de datos de nivel de página se reemplazará por una plantilla de datos de nivel de control, o bien una plantilla de datos insertada.

## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)