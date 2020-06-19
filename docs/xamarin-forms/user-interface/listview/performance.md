---
title: Rendimiento de ListView
description: Aunque ListView es una vista eficaz para Mostrar datos, tiene algunas limitaciones. En este artículo se explica cómo garantizar un gran rendimiento con un Xamarin.Forms control ListView en una aplicación.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2b8e057d9687cd0a472451fc73cc578f9358277
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139898"
---
# <a name="listview-performance"></a>Rendimiento de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Al escribir aplicaciones móviles, el rendimiento es importante. Los usuarios tienen que esperar un desplazamiento suave y tiempos de carga rápidos. Si no se cumplen las expectativas de los usuarios, el costo de las clasificaciones en el almacén de aplicaciones, o en el caso de una aplicación de línea de negocio, cuestan el tiempo y el dinero de la organización.

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) Es una vista eficaz para mostrar los datos, pero tiene algunas limitaciones. El rendimiento del desplazamiento puede verse afectado cuando se usan celdas personalizadas, sobre todo cuando contienen jerarquías de vista profundamente anidadas o se usan determinados diseños que requieren una medida compleja. Afortunadamente, hay técnicas que puede usar para evitar un bajo rendimiento.

## <a name="caching-strategy"></a>Estrategia de almacenamiento en caché

Los controles ListView suelen usarse para mostrar muchos más datos de los que caben en la pantalla. Por ejemplo, una aplicación de música podría tener una biblioteca de canciones con miles de entradas. La creación de un elemento para cada entrada desperdiciaría la memoria valiosa y funcionaría mal. Crear y destruir filas de manera constante requeriría que la aplicación creara instancias y limpie los objetos constantemente, lo que también tendría un rendimiento bajo.

Para conservar memoria, los [`ListView`](xref:Xamarin.Forms.ListView) equivalentes nativos de cada plataforma tienen características integradas para reutilizar las filas. Solo las celdas visibles en la pantalla se cargan en la memoria y el **contenido** se carga en las celdas existentes. Este patrón evita que la aplicación Cree instancias de miles de objetos, lo que ahorra tiempo y memoria.

Xamarin.Formspermite [`ListView`](xref:Xamarin.Forms.ListView) la reutilización de celdas a través de la [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) enumeración, que tiene los valores siguientes:

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> El Plataforma universal de Windows (UWP) omite la [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché, ya que siempre usa el almacenamiento en caché para mejorar el rendimiento. Por lo tanto, de forma predeterminada se comporta como si [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) se aplicara la estrategia de almacenamiento en caché.

### <a name="retainelement"></a>RetainElement

La [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) estrategia de almacenamiento en caché especifica que [`ListView`](xref:Xamarin.Forms.ListView) generará una celda para cada elemento de la lista y es el `ListView` comportamiento predeterminado. Debe usarse en las siguientes circunstancias:

- Cada celda tiene un gran número de enlaces (20-30 +).
- La plantilla de celda cambia con frecuencia.
- La prueba revela que la `RecycleElement` estrategia de almacenamiento en caché tiene como resultado una velocidad de ejecución reducida.

Es importante reconocer las consecuencias de la estrategia de [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) almacenamiento en caché al trabajar con celdas personalizadas. Cualquier código de inicialización de celda deberá ejecutarse para cada creación de celda, que puede ser varias veces por segundo. En este caso, las técnicas de diseño que estaban bien en una página, como el uso de varias instancias anidadas [`StackLayout`](xref:Xamarin.Forms.StackLayout) , se convierten en cuellos de botella de rendimiento cuando se configuran y destruyen en tiempo real a medida que el usuario se desplaza.

### <a name="recycleelement"></a>RecycleElement

La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché especifica que el [`ListView`](xref:Xamarin.Forms.ListView) intentará minimizar la superficie de memoria y la velocidad de ejecución al reciclar las celdas de la lista. Este modo no siempre ofrece una mejora del rendimiento y las pruebas deben realizarse para determinar las mejoras. Sin embargo, es la opción preferida y debe usarse en las siguientes circunstancias:

- Cada celda tiene un número pequeño o moderado de enlaces.
- Cada una de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) las celdas define todos los datos de la celda.
- Cada celda es muy similar, y la plantilla de celda no cambia.

Durante la virtualización, la celda tendrá su contexto de enlace actualizado y, por tanto, si una aplicación utiliza este modo, debe asegurarse de que las actualizaciones de contexto de enlace se controlen adecuadamente. Todos los datos sobre la celda deben provienen del contexto de enlace o se pueden producir errores de coherencia. Este problema se puede evitar utilizando el enlace de datos para mostrar los datos de la celda. Como alternativa, los datos de celda deben establecerse en la `OnBindingContextChanged` invalidación, en lugar de en el constructor de la celda personalizada, como se muestra en el ejemplo de código siguiente:

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

En iOS y Android, si las celdas usan representadores personalizados, deben asegurarse de que la notificación de cambio de propiedad está implementada correctamente. Cuando se reutilizan las celdas, sus valores de propiedad cambiarán cuando el contexto de enlace se actualice al de una celda disponible, con `PropertyChanged` los eventos que se generan. Para obtener más información, consulte [Personalización de un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement con un DataTemplateSelector

Cuando [`ListView`](xref:Xamarin.Forms.ListView) usa un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , la estrategia de almacenamiento en caché no [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) almacena en caché `DataTemplate` . En su lugar, `DataTemplate` se selecciona un para cada elemento de datos de la lista.

> [!NOTE]
> La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) estrategia de almacenamiento en caché tiene un requisito previo, presentado en Xamarin.Forms 2,4, que cuando [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) se le pide que seleccione un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que cada uno `DataTemplate` debe devolver el mismo [`ViewCell`](xref:Xamarin.Forms.ViewCell) tipo. Por ejemplo, dado un [`ListView`](xref:Xamarin.Forms.ListView) con un `DataTemplateSelector` que puede devolver `MyDataTemplateA` (donde `MyDataTemplateA` devuelve un `ViewCell` de tipo `MyViewCellA` ) o `MyDataTemplateB` (donde `MyDataTemplateB` devuelve un `ViewCell` de tipo `MyViewCellB` ), cuando se devuelve, `MyDataTemplateA` debe devolver o se `MyViewCellA` producirá una excepción.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estrategia de almacenamiento en caché se basa en la estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) asegurándose también de que cuando [`ListView`](xref:Xamarin.Forms.ListView) utiliza un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para seleccionar [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , `DataTemplate` el tipo de elemento de la lista almacena en la memoria caché. Por lo tanto, `DataTemplate` se seleccionan una vez por cada tipo de elemento, en lugar de una vez por cada instancia de elemento.

> [!NOTE]
> La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) estrategia de almacenamiento en caché tiene un requisito previo de que los `DataTemplate` s devueltos por [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) deben usar el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) constructor que toma un `Type` .

### <a name="set-the-caching-strategy"></a>Establecimiento de la estrategia de almacenamiento en caché

El [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeración se especifica con una [`ListView`](xref:Xamarin.Forms.ListView) sobrecarga de constructor, tal y como se muestra en el ejemplo de código siguiente:

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

Este método tiene el mismo efecto que establecer el argumento de la estrategia de almacenamiento en caché en el constructor en C#.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Establecer la estrategia de almacenamiento en caché en un control ListView de subclases

Al establecer el `CachingStrategy` atributo desde XAML en una subclase [`ListView`](xref:Xamarin.Forms.ListView) , no se producirá el comportamiento deseado, porque no hay ninguna `CachingStrategy` propiedad en `ListView` . Además, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) está habilitado, se generará el siguiente mensaje de error: **no se ha encontrado ninguna propiedad, propiedad enlazable o evento para ' CachingStrategy '**

La solución a este problema es especificar un constructor en la subclase [`ListView`](xref:Xamarin.Forms.ListView) que acepta un [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) parámetro y lo pasa a la clase base:

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Después, el [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valor de enumeración se puede especificar desde XAML mediante la `x:Arguments` Sintaxis:

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Sugerencias de rendimiento de ListView

Existen muchas técnicas para mejorar el rendimiento de un `ListView` . Las siguientes sugerencias pueden mejorar el rendimiento de ListView

- Enlace la `ItemsSource` propiedad a una `IList<T>` colección en lugar de una `IEnumerable<T>` colección, ya que las `IEnumerable<T>` colecciones no admiten el acceso aleatorio.
- Use las celdas integradas (como `TextCell`  /  `SwitchCell` ) en lugar de `ViewCell` siempre que pueda.
- Usar menos elementos. Por ejemplo, considere la posibilidad de usar una sola `FormattedString` etiqueta en lugar de varias etiquetas.
- Reemplazar `ListView` por `TableView` cuando se muestran datos no homogéneos; es decir, datos de tipos diferentes.
- Limite el uso del [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) método. Si se sobreutiliza, se degradará el rendimiento.
- En Android, evite establecer `ListView` la visibilidad o el color del separador de filas de una vez que se haya creado una instancia, ya que esto produce una gran penalización del rendimiento.
- Evite cambiar el diseño de las celdas en función del [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Cambiar el diseño conlleva grandes costos de medición e inicialización.
- Evite las jerarquías de diseño profundamente anidadas. Use `AbsoluteLayout` o `Grid` para ayudar a reducir el anidamiento.
- Evite `LayoutOptions` un específico distinto de `Fill` ( `Fill` es el más barato de calcular).
- Evite colocar un `ListView` dentro `ScrollView` de por los siguientes motivos:
  - `ListView`Implementa su propio desplazamiento.
  - `ListView`No recibirá ningún gesto, ya que lo controlará el elemento primario `ScrollView` .
  - `ListView`Puede presentar un encabezado y un pie de página personalizados que se desplacen con los elementos de la lista, lo que podría ofrecer la funcionalidad para la que `ScrollView` se utilizó. Para obtener más información, vea [encabezados y pies de página](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Considere la posibilidad de un representador personalizado si necesita un diseño específico y complejo presentado en las celdas.

`AbsoluteLayout`tiene la posibilidad de realizar diseños sin una única llamada de medida, lo que lo convierte en un rendimiento elevado. Si `AbsoluteLayout` no se puede usar, tenga en cuenta [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Si se usa `RelativeLayout` , pasar restricciones directamente será mucho más rápido que el uso de Expression API. Este método es más rápido porque la API de expresión usa JIT y, en iOS, el árbol debe interpretarse, lo que es más lento. Expression API es adecuado para los diseños de página donde solo es necesario en el diseño y la rotación iniciales, pero en `ListView` , donde se ejecuta constantemente durante el desplazamiento, afecta al rendimiento.

La creación de un representador personalizado para un [`ListView`](xref:Xamarin.Forms.ListView) o sus celdas es un enfoque para reducir el efecto de los cálculos de diseño en el rendimiento del desplazamiento. Para obtener más información, vea [personalizar un control ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) y [personalizar un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Vínculos relacionados

- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Representador personalizado ViewCell (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
