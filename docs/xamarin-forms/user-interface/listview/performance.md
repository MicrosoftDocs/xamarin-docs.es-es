---
title: Rendimiento de ListView
description: Aunque ListView es una eficaz vista para mostrar los datos, tiene algunas limitaciones. En este artículo se explica cómo garantizar un rendimiento excelente con un ListView Xamarin.Forms en una aplicación.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916831"
---
# <a name="listview-performance"></a>Rendimiento de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Al escribir aplicaciones móviles, es importante el rendimiento. Los usuarios han llegado a esperar el desplazamiento suave y tiempos de carga rápida. No se puede satisfacer las expectativas de los usuarios de costo que las clasificaciones en el almacén de aplicaciones o en el caso de una aplicación de línea de negocio, costo de su organización tiempo y dinero.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) es una vista eficaz para mostrar los datos, pero tiene algunas limitaciones. El rendimiento del desplazamiento puede verse afectado cuando se usan celdas personalizadas, sobre todo cuando contienen jerarquías de vista profundamente anidadas o se usan determinados diseños que requieren una medida compleja. Afortunadamente, hay técnicas que puede usar para evitar un rendimiento deficiente.

## <a name="caching-strategy"></a>Estrategia de almacenamiento en caché

Los controles ListView suelen usarse para mostrar muchos más datos de los que caben en la pantalla. Por ejemplo, una aplicación de música podría tener una biblioteca de canciones con miles de entradas. La creación de un elemento para cada entrada desperdiciaría la memoria valiosa y funcionaría mal. Crear y destruir filas de manera constante requeriría que la aplicación creara instancias y limpie los objetos constantemente, lo que también tendría un rendimiento bajo.

Para conservar memoria, los equivalentes [`ListView`](xref:Xamarin.Forms.ListView) nativos de cada plataforma tienen características integradas para reutilizar las filas. Solo las celdas visibles en la pantalla se cargan en la memoria y el **contenido** se carga en las celdas existentes. Este patrón evita que la aplicación Cree instancias de miles de objetos, lo que ahorra tiempo y memoria.

Xamarin. Forms permite reutilizar [`ListView`](xref:Xamarin.Forms.ListView) celda a través de la enumeración [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) , que tiene los valores siguientes:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> El Plataforma universal de Windows (UWP) omite la estrategia de almacenamiento en caché de [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , porque siempre usa el almacenamiento en caché para mejorar el rendimiento. Por lo tanto, de forma predeterminada se comporta como si se aplicara la estrategia de almacenamiento en caché de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) .

### <a name="retainelement"></a>RetainElement

La estrategia de almacenamiento en caché de [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) especifica que la [`ListView`](xref:Xamarin.Forms.ListView) generará una celda para cada elemento de la lista y es el comportamiento de `ListView` predeterminado. Debe usarse en las siguientes circunstancias:

- Cada celda tiene un gran número de enlaces (20-30 +).
- La plantilla de celda cambia con frecuencia.
- La prueba revela que la estrategia de almacenamiento en caché `RecycleElement` tiene como resultado una velocidad de ejecución reducida.

Es importante reconocer las consecuencias de la estrategia de almacenamiento en caché [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) al trabajar con celdas personalizadas. Cualquier código de inicialización de la celda tendrá que ejecutar para la creación de cada celda, que puede ser varias veces por segundo. En este caso, las técnicas de diseño que estaban bien en una página, como el uso de varias instancias de [`StackLayout`](xref:Xamarin.Forms.StackLayout) anidadas, se convierten en cuellos de botella de rendimiento cuando se configuran y destruyen en tiempo real a medida que el usuario se desplaza.

### <a name="recycleelement"></a>RecycleElement

La estrategia de almacenamiento en caché de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) especifica que el [`ListView`](xref:Xamarin.Forms.ListView) intentará minimizar la superficie de memoria y la velocidad de ejecución mediante el reciclaje de las celdas de la lista. Este modo no siempre ofrece una mejora del rendimiento y las pruebas deben realizarse para determinar las mejoras. Sin embargo, es la opción preferida y debe usarse en las siguientes circunstancias:

- Cada celda tiene un número pequeño o moderado de enlaces.
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada celda define todos los datos de la celda.
- Cada celda es muy similar, y la plantilla de celda no cambia.

Durante la virtualización de la celda tendrá su contexto de enlace que se actualiza y, por lo que si una aplicación utiliza este modo debe asegurarse de que las actualizaciones de contexto de enlace se controlan apropiadamente. Todos los datos sobre la celda deben proceder del contexto de enlace o se pueden producir errores de coherencia. Este problema se puede evitar utilizando el enlace de datos para mostrar los datos de la celda. Como alternativa, los datos de celda se deben establecer en el `OnBindingContextChanged` invalidación, en lugar de en el constructor de la celda personalizada, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomCell : ViewCell
{
    Image image = null;
    
    public CustomCell ()
    {
        image = new Image();
        View = image;
    }
    
    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();
        
        var item = BindingContext as ImageItem;
        if (item != null) {
            image.Source = item.ImageUrl;
        }
    }
}
```

Para obtener más información, vea [enlazar cambios de contexto](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

En iOS y Android, si las celdas usar a representadores personalizados, debe asegurarse de que se haya implementado correctamente la notificación de cambio de propiedad. Cuando se reutilizan las celdas, sus valores de propiedad cambiarán cuando el contexto de enlace se actualice al de una celda disponible, con `PropertyChanged` eventos que se produzcan. Para obtener más información, consulte [Personalización de un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Cuando un [`ListView`](xref:Xamarin.Forms.ListView) utiliza un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la estrategia de almacenamiento en caché de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) no almacena en caché `DataTemplate`s. En su lugar, se selecciona un `DataTemplate` para cada elemento de datos de la lista.

> [!NOTE]
> La estrategia de almacenamiento en caché de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) tiene un requisito previo, introducido en Xamarin. forms 2,4, que cuando se solicita un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) a seleccionar una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que cada `DataTemplate` debe devolver el mismo tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell) . Por ejemplo, dado un [`ListView`](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` que puede devolver `MyDataTemplateA` (donde `MyDataTemplateA` devuelve una `ViewCell` de tipo `MyViewCellA`) o `MyDataTemplateB` (donde `MyDataTemplateB` devuelve una `ViewCell` de tipo `MyViewCellB`), cuando se devuelve `MyDataTemplateA`, debe devolver `MyViewCellA` o se producirá una excepción.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La estrategia de almacenamiento en caché de [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) se basa en la estrategia de almacenamiento en caché del [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) asegurándose también de que cuando un [`ListView`](xref:Xamarin.Forms.ListView) utiliza una [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s se almacena en la memoria caché por el tipo de elemento de la lista. Por lo tanto, `DataTemplate`s se seleccionan una vez por cada tipo de elemento, en lugar de una vez por cada instancia de elemento.

> [!NOTE]
> La estrategia de almacenamiento en caché [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) tiene un requisito previo que el `DataTemplate`s devuelto por la [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) debe utilizar el constructor [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que toma un `Type`.

### <a name="set-the-caching-strategy"></a>Establecimiento de la estrategia de almacenamiento en caché

El valor de enumeración [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) se especifica con una sobrecarga del constructor [`ListView`](xref:Xamarin.Forms.ListView) , como se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

En XAML, establezca el atributo `CachingStrategy` como se muestra en el código XAML siguiente:

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Este método tiene el mismo efecto que establecer el argumento de la estrategia de almacenamiento en C#caché en el constructor de.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Establecer la estrategia de almacenamiento en caché en un control ListView de subclases

Al establecer el atributo `CachingStrategy` desde XAML en una [`ListView`](xref:Xamarin.Forms.ListView) subclase no se producirá el comportamiento deseado, porque no hay ninguna propiedad `CachingStrategy` en `ListView`. Además, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) está habilitado, se generará el siguiente mensaje de error: **no se ha encontrado ninguna propiedad, propiedad enlazable o evento para ' CachingStrategy '**

La solución a este problema es especificar un constructor en el [`ListView`](xref:Xamarin.Forms.ListView) subclase que acepta un parámetro [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) y lo pasa a la clase base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Después, el valor de enumeración de [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) se puede especificar desde XAML mediante la sintaxis de `x:Arguments`:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Sugerencias de rendimiento de ListView

Existen muchas técnicas para mejorar el rendimiento de un `ListView`. Las siguientes sugerencias pueden mejorar el rendimiento de ListView

- Enlace la propiedad `ItemsSource` a una colección `IList<T>` en lugar de una colección `IEnumerable<T>`, porque las colecciones `IEnumerable<T>` no admiten el acceso aleatorio.
- Use las celdas integradas (como `TextCell` / `SwitchCell`) en lugar de `ViewCell` siempre que pueda.
- Use menos elementos. Por ejemplo, considere la posibilidad de usar una sola etiqueta de `FormattedString` en lugar de varias etiquetas.
- Reemplace el `ListView` por un `TableView` al Mostrar datos no homogéneos, es decir, datos de tipos diferentes.
- Limite el uso del método [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) . Si se sobreutiliza, degradará el rendimiento.
- En Android, evite establecer la visibilidad o el color del separador de filas de un `ListView`una vez que se haya creado una instancia, ya que esto produce una gran penalización del rendimiento.
- Evite cambiar el diseño de las celdas en función del [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Cambiar el diseño conlleva grandes costos de medición e inicialización.
- Evite las jerarquías de diseño profundamente anidado. Use `AbsoluteLayout` o `Grid` para ayudar a reducir el anidamiento.
- Evite `LayoutOptions` específicos que no sean `Fill` (`Fill` es el más barato de calcular).
- Evite colocar una `ListView` dentro de un `ScrollView` por los siguientes motivos:
  - El `ListView` implementa su propio desplazamiento.
  - El `ListView` no recibirá ningún gesto, ya que lo controlará el `ScrollView`primario.
  - El `ListView` puede presentar un encabezado y un pie de página personalizados que se desplacen con los elementos de la lista, lo que podría ofrecer la funcionalidad para la que se usó el `ScrollView`. Para obtener más información, vea [encabezados y pies de página](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Considere la posibilidad de un representador personalizado si necesita un diseño específico y complejo presentado en las celdas.

`AbsoluteLayout` tiene la posibilidad de realizar diseños sin una única llamada de medida, lo que lo convierte en un rendimiento elevado. Si no se puede usar `AbsoluteLayout`, considere la posibilidad de [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout). Si usa `RelativeLayout`, pasar las restricciones directamente será mucho más rápido que usar Expression API. Este método es más rápido porque la API de expresión usa JIT y, en iOS, el árbol debe interpretarse, lo que es más lento. Expression API es adecuado para los diseños de página donde solo es necesario en el diseño y la rotación iniciales, pero en `ListView`, donde se ejecuta constantemente durante el desplazamiento, afecta al rendimiento.

La creación de un representador personalizado para un [`ListView`](xref:Xamarin.Forms.ListView) o sus celdas es un enfoque para reducir el efecto de los cálculos de diseño en el rendimiento del desplazamiento. Para obtener más información, vea [personalizar un control ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) y [personalizar un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Vínculos relacionados

- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Representador personalizado ViewCell (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
