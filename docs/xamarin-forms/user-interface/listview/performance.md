---
title: Rendimiento de ListView
description: Aunque ListView es una eficaz vista para mostrar los datos, tiene algunas limitaciones. En este artículo se explica cómo garantizar un rendimiento excelente con un ListView Xamarin.Forms en una aplicación.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: a1fae280f42f91fce4b4fe28c3f728cf14c7a21c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528903"
---
# <a name="listview-performance"></a>Rendimiento de ListView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Al escribir aplicaciones móviles, es importante el rendimiento. Los usuarios han llegado a esperar el desplazamiento suave y tiempos de carga rápida. No se puede satisfacer las expectativas de los usuarios de costo que las clasificaciones en el almacén de aplicaciones o en el caso de una aplicación de línea de negocio, costo de su organización tiempo y dinero.

Aunque [ `ListView` ](xref:Xamarin.Forms.ListView) es una eficaz vista para mostrar los datos, tiene algunas limitaciones. El rendimiento del desplazamiento puede verse afectado cuando se usa celdas personalizadas, especialmente cuando contienen jerarquías de vista profundamente anidadas o utilicen algunos diseños que requieren una gran cantidad de medida. Afortunadamente, hay técnicas que puede usar para evitar un rendimiento deficiente.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Estrategia de almacenamiento en caché

ListView a menudo se usa para mostrar muchos más datos que puede ajustar en la pantalla. Considere la posibilidad de una aplicación de música, por ejemplo. Una biblioteca de canciones puede tener miles de entradas. El enfoque simple, que sería crear una fila por cada canción, tendría un rendimiento deficiente. Ese enfoque desperdicia memoria valiosa y puede ralentizar el desplazamiento a un rastreo. Otro enfoque consiste en crear y destruir las filas, como los datos se desplazan en la vista. Esto requiere la creación de instancias constante y limpieza de los objetos de vista, que puede ser muy lento.

Para ahorrar memoria, nativo [ `ListView` ](xref:Xamarin.Forms.ListView) equivalentes para cada plataforma tienen características integradas para la reutilización de las filas. Solo las celdas visibles en pantalla se cargan en memoria y el **contenido** se carga en las celdas existentes. Esto evita que la aplicación de la necesidad de crear instancias de miles de objetos, ahorra tiempo y memoria.

Xamarin.Forms permite [ `ListView` ](xref:Xamarin.Forms.ListView) celda volver a usar a través de la [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) enumeración, que tiene los siguientes valores:

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

El [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché especifica que el [ `ListView` ](xref:Xamarin.Forms.ListView) generará una celda para cada elemento en la lista y el valor predeterminado es `ListView` comportamiento. Por lo general debe utilizarse en las siguientes circunstancias:

- Cuando cada celda tiene un gran número de enlaces (20-30 +).
- Cuando la plantilla de celda cambia con frecuencia.
- Cuando las pruebas revelan que el `RecycleElement` almacenamiento en caché de resultados de la estrategia una velocidad de ejecución reducida.

Es importante tener en cuenta las consecuencias de la [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché cuando se trabaja con celdas personalizadas. Cualquier código de inicialización de la celda tendrá que ejecutar para la creación de cada celda, que puede ser varias veces por segundo. En este caso, las técnicas de diseño que eran aceptables en una página, como uso de varios anidados [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) instancias, se convierten en los cuellos de botella de rendimiento cuando se programa de instalación y se destruye en tiempo real como el usuario se desplaza.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

El [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché especifica que el [ `ListView` ](xref:Xamarin.Forms.ListView) intentará minimizar su velocidad de consumo y la ejecución de memoria mediante el reciclado de celdas de la lista. Este modo no siempre ofrece una mejora del rendimiento y las pruebas deben realizarse para determinar las mejoras. Sin embargo, suele ser la opción preferida y debe usarse en las siguientes circunstancias:

- Cuando cada celda tiene un pequeño o un número moderado de enlaces.
- Cuando cada celda [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) define todos los datos de celda.
- Cuando cada celda es similar en gran medida, con la plantilla de celda que no cambian.

Durante la virtualización de la celda tendrá su contexto de enlace que se actualiza y, por lo que si una aplicación utiliza este modo debe asegurarse de que las actualizaciones de contexto de enlace se controlan apropiadamente. Todos los datos sobre la celda deben proceder del contexto de enlace o se pueden producir errores de coherencia. Esto puede realizarse mediante el uso de enlace de datos para mostrar datos de la celda. Como alternativa, se deben establecer los datos de la celda la `OnBindingContextChanged` invalidar, en lugar de en el constructor de la celda personalizada, como se muestra en el ejemplo de código siguiente:

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

### <a name="setting-the-caching-strategy"></a>Establecimiento de la estrategia de almacenamiento en caché

El [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) se especifica el valor de enumeración con un [ `ListView` ](xref:Xamarin.Forms.ListView) sobrecarga del constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

En XAML, establezca el `CachingStrategy` atributo tal como se muestra en el código siguiente:

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

Esto tiene el mismo efecto que establecer el argumento de estrategia de almacenamiento en caché en el constructor en C# Tenga en cuenta que no hay ningún `CachingStrategy` propiedad `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Establecimiento de la estrategia de almacenamiento en caché en un ListView crean Subclase

Establecer el `CachingStrategy` atributo de XAML en una subclase [ `ListView` ](xref:Xamarin.Forms.ListView) no producirá el comportamiento deseado, porque no hay ningún `CachingStrategy` propiedad `ListView`. Además, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) está habilitado, se generará el siguiente mensaje de error: **No se encontró ninguna propiedad, propiedad enlazable o evento para ' CachingStrategy '**

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

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Mejorar el rendimiento de ListView

Existen muchas técnicas para mejorar el rendimiento de un `ListView`:

- Enlazar el `ItemsSource` propiedad a un `IList<T>` colección en lugar de un `IEnumerable<T>` colección, porque `IEnumerable<T>` colecciones no admiten el acceso aleatorio.
- Utilice las celdas integradas (como `TextCell`  /  `SwitchCell` ) en lugar de `ViewCell` siempre puede.
- Use menos elementos. Por ejemplo, considere el uso de una sola `FormattedString` etiqueta en lugar de varias etiquetas.
- Reemplace el `ListView` con un `TableView` al mostrar los datos no homogéneos, es decir, los datos de diferentes tipos.
- Limitar el uso de la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) método. Si se sobreutiliza, degradará el rendimiento.
- En Android, evite establecer un `ListView`de visibilidad del separador de fila o de color después de que se ha creado una instancia, como resultado una reducción del rendimiento.
- Evite cambiar el diseño de la celda según la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Esto provoca costos de inicialización y de diseño grandes.
- Evite las jerarquías de diseño profundamente anidado. Use `AbsoluteLayout` o `Grid` para ayudar a reducir el anidamiento.
- Evitar específico `LayoutOptions` distinto `Fill` (relleno es el más barato de proceso).
- Evite colocar un `ListView` dentro de un `ScrollView` por las razones siguientes:
    - El `ListView` implementa su propio desplazamiento.
    - El `ListView` no recibirán los gestos, como se controlarán por el elemento primario `ScrollView`.
    - El `ListView` puede presentar un encabezado personalizado y un pie de página que se desplaza por los elementos de la lista, ofreciendo la funcionalidad que el `ScrollView` utilizó. Para obtener más información, consulte [encabezados y pies de página](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
- Considere la posibilidad de un representador personalizado si necesita un diseño muy específico y complejo presentan en sus celdas.

`AbsoluteLayout` tiene la posibilidad de realizar diseños sin una llamada única medida. Esto resulta muy eficaz para el rendimiento. Si `AbsoluteLayout` no se puede usar, considere la posibilidad de [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Si usa `RelativeLayout`, pasar directamente restricciones será considerablemente más rápido que utilizar la API de la expresión. Eso es porque la expresión de API usa JIT, y en iOS el árbol debe interpretarse, que es más lento. La expresión de API es adecuada para los diseños de página donde solo requería en el diseño inicial y la rotación, pero en `ListView`, donde se ejecuta constantemente durante el desplazamiento, repercute en el rendimiento.

Creación de un representador personalizado para un [ `ListView` ](xref:Xamarin.Forms.ListView) o sus celdas es un enfoque para reducir el efecto de los cálculos de diseño en el rendimiento del desplazamiento. Para obtener más información, consulte [personalizar un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) y [personalizar ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Vínculos relacionados

- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
