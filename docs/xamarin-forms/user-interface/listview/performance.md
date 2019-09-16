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
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998181"
---
# <a name="listview-performance"></a>Rendimiento de ListView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Al escribir aplicaciones móviles, es importante el rendimiento. Los usuarios han llegado a esperar el desplazamiento suave y tiempos de carga rápida. No se puede satisfacer las expectativas de los usuarios de costo que las clasificaciones en el almacén de aplicaciones o en el caso de una aplicación de línea de negocio, costo de su organización tiempo y dinero.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) es una vista eficaz para mostrar los datos, pero tiene algunas limitaciones. El rendimiento del desplazamiento puede verse afectado cuando se usan celdas personalizadas, sobre todo cuando contienen jerarquías de vista profundamente anidadas o se usan determinados diseños que requieren una medida compleja. Afortunadamente, hay técnicas que puede usar para evitar un rendimiento deficiente.

## <a name="caching-strategy"></a>Estrategia de almacenamiento en caché

Los controles ListView suelen usarse para mostrar muchos más datos de los que caben en la pantalla. Por ejemplo, una aplicación de música podría tener una biblioteca de canciones con miles de entradas. La creación de un elemento para cada entrada desperdiciaría la memoria valiosa y funcionaría mal. Crear y destruir filas de manera constante requeriría que la aplicación creara instancias y limpie los objetos constantemente, lo que también tendría un rendimiento bajo.

Para conservar memoria, los equivalentes [`ListView`](xref:Xamarin.Forms.ListView) nativos de cada plataforma tienen características integradas para reutilizar las filas. Solo las celdas visibles en pantalla se cargan en memoria y el **contenido** se carga en las celdas existentes. Este patrón evita que la aplicación Cree instancias de miles de objetos, lo que ahorra tiempo y memoria.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) permite la reutilización de celdas a través de la [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) enumeración, que tiene los valores siguientes:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> La plataforma Universal de Windows (UWP) pasa por alto el [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) almacenamiento en caché de estrategia, porque siempre usa almacenamiento en caché para mejorar el rendimiento. Por lo tanto, de forma predeterminada se comporta como si la [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) se aplica la estrategia de almacenamiento en caché.

### <a name="retainelement"></a>RetainElement

El [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché especifica que el [ `ListView` ](xref:Xamarin.Forms.ListView) generará una celda para cada elemento en la lista y el valor predeterminado es `ListView` comportamiento. Debe usarse en las siguientes circunstancias:

- Cada celda tiene un gran número de enlaces (20-30 +).
- La plantilla de celda cambia con frecuencia.
- La prueba revela que `RecycleElement` la estrategia de almacenamiento en caché tiene como resultado una velocidad de ejecución reducida.

Es importante tener en cuenta las consecuencias de la [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché cuando se trabaja con celdas personalizadas. Cualquier código de inicialización de la celda tendrá que ejecutar para la creación de cada celda, que puede ser varias veces por segundo. En este caso, las técnicas de diseño que estaban bien en una página, como el uso [`StackLayout`](xref:Xamarin.Forms.StackLayout) de varias instancias anidadas, se convierten en cuellos de botella de rendimiento cuando se configuran y destruyen en tiempo real a medida que el usuario se desplaza.

### <a name="recycleelement"></a>RecycleElement

El [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché especifica que el [ `ListView` ](xref:Xamarin.Forms.ListView) intentará minimizar su velocidad de consumo y la ejecución de memoria mediante el reciclado de celdas de la lista. Este modo no siempre ofrece una mejora del rendimiento y las pruebas deben realizarse para determinar las mejoras. Sin embargo, es la opción preferida y debe usarse en las siguientes circunstancias:

- Cada celda tiene un número pequeño o moderado de enlaces.
- Cada una de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) las celdas define todos los datos de la celda.
- Cada celda es muy similar, y la plantilla de celda no cambia.

Durante la virtualización de la celda tendrá su contexto de enlace que se actualiza y, por lo que si una aplicación utiliza este modo debe asegurarse de que las actualizaciones de contexto de enlace se controlan apropiadamente. Todos los datos sobre la celda deben proceder del contexto de enlace o se pueden producir errores de coherencia. Este problema se puede evitar utilizando el enlace de datos para mostrar los datos de la celda. Como alternativa, se deben establecer los datos de la celda la `OnBindingContextChanged` invalidar, en lugar de en el constructor de la celda personalizada, como se muestra en el ejemplo de código siguiente:

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

Para obtener más información, consulte [cambios de contexto de enlace](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

En iOS y Android, si las celdas usar a representadores personalizados, debe asegurarse de que se haya implementado correctamente la notificación de cambio de propiedad. Cuando se reutilizan las celdas sus valores de propiedad cambiará cuando se actualiza el contexto de enlace a la de una celda disponible, con `PropertyChanged` eventos provocados. Para obtener más información, consulte [personalizar ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Cuando un [ `ListView` ](xref:Xamarin.Forms.ListView) usa un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) almacenamiento en caché estrategia no almacena en caché `DataTemplate`s. En su lugar, un `DataTemplate` está seleccionado para cada elemento de datos en la lista.

> [!NOTE]
> El [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento tiene un requisito previo, introducido en 2.4 de Xamarin.Forms, que, cuando un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) se le pide que seleccione un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)que cada `DataTemplate` debe devolver el mismo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) tipo. Por ejemplo, dada una [ `ListView` ](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` que puede devolver `MyDataTemplateA` (donde `MyDataTemplateA` devuelve un `ViewCell` typu `MyViewCellA`), o `MyDataTemplateB` (donde `MyDataTemplateB`devuelve un `ViewCell` typu `MyViewCellB`), cuando `MyDataTemplateA` devuelto debe devolver `MyViewCellA` o se producirá una excepción.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

El [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estrategia de almacenamiento en caché se basa en el [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché asegurándose además que, cuando un [ `ListView` ](xref:Xamarin.Forms.ListView) usa un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s se almacenan en caché por el tipo de elemento de la lista. Por lo tanto, `DataTemplate`s se seleccionan una vez por cada tipo de elemento, en lugar de una vez por cada instancia del elemento.

> [!NOTE]
> El [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estrategia de almacenamiento tiene un requisito previo que la `DataTemplate`s devuelto por la [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) debe utilizar el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) constructor que toma un `Type`.

### <a name="set-the-caching-strategy"></a>Establecimiento de la estrategia de almacenamiento en caché

El [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) se especifica el valor de enumeración con un [ `ListView` ](xref:Xamarin.Forms.ListView) sobrecarga del constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

En XAML, establezca el `CachingStrategy` atributo como se muestra en el código XAML siguiente:

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

Al establecer `CachingStrategy` el atributo desde XAML en una [`ListView`](xref:Xamarin.Forms.ListView) subclase, no se producirá el comportamiento deseado, porque no `CachingStrategy` hay ninguna `ListView`propiedad en. Además, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) está habilitado, se generará el siguiente mensaje de error: **No se encontró ninguna propiedad, propiedad enlazable o evento para ' CachingStrategy '**

La solución a este problema consiste en especificar un constructor de subclases [ `ListView` ](xref:Xamarin.Forms.ListView) que acepta un [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) parámetro y lo pasa a la clase base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

El [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) se puede especificar el valor de enumeración de XAML mediante el uso de la `x:Arguments` sintaxis:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Sugerencias de rendimiento de ListView

Existen muchas técnicas para mejorar el rendimiento de un `ListView`. Las siguientes sugerencias pueden mejorar el rendimiento de ListView

- Enlazar el `ItemsSource` propiedad a un `IList<T>` colección en lugar de un `IEnumerable<T>` colección, porque `IEnumerable<T>` colecciones no admiten el acceso aleatorio.
- Use las celdas `TextCell` integradas (como  /  `SwitchCell` ) en lugar de `ViewCell` siempre que pueda.
- Use menos elementos. Por ejemplo, considere la posibilidad de `FormattedString` usar una sola etiqueta en lugar de varias etiquetas.
- Reemplace el `ListView` con un `TableView` al mostrar los datos no homogéneos, es decir, los datos de diferentes tipos.
- Limitar el uso de la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método. Si se sobreutiliza, degradará el rendimiento.
- En Android, evite establecer un `ListView`de visibilidad del separador de fila o de color después de que se ha creado una instancia, como resultado una reducción del rendimiento.
- Evite cambiar el diseño de la celda según la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Cambiar el diseño conlleva grandes costos de medición e inicialización.
- Evite las jerarquías de diseño profundamente anidado. Use `AbsoluteLayout` o `Grid` para ayudar a reducir el anidamiento.
- Evite un `LayoutOptions` específico distinto `Fill` de`Fill` (es el más barato de calcular).
- Evite colocar un `ListView` dentro de un `ScrollView` por las razones siguientes:
  - El `ListView` implementa su propio desplazamiento.
  - El `ListView` no recibirán los gestos, como se controlarán por el elemento primario `ScrollView`.
  - El `ListView` puede presentar un encabezado personalizado y un pie de página que se desplaza por los elementos de la lista, ofreciendo la funcionalidad que el `ScrollView` utilizó. Para obtener más información, vea [encabezados y pies de página](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Considere la posibilidad de un representador personalizado si necesita un diseño específico y complejo presentado en las celdas.

`AbsoluteLayout`tiene la posibilidad de realizar diseños sin una única llamada de medida, lo que lo convierte en un rendimiento elevado. Si `AbsoluteLayout` no se puede usar, considere la posibilidad de [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Si usa `RelativeLayout`, pasar directamente restricciones será considerablemente más rápido que utilizar la API de la expresión. Este método es más rápido porque la API de expresión usa JIT y, en iOS, el árbol debe interpretarse, lo que es más lento. La expresión de API es adecuada para los diseños de página donde solo requería en el diseño inicial y la rotación, pero en `ListView`, donde se ejecuta constantemente durante el desplazamiento, repercute en el rendimiento.

Creación de un representador personalizado para un [ `ListView` ](xref:Xamarin.Forms.ListView) o sus celdas es un enfoque para reducir el efecto de los cálculos de diseño en el rendimiento del desplazamiento. Para obtener más información, consulte [personalizar un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) y [personalizar ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Vínculos relacionados

- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
