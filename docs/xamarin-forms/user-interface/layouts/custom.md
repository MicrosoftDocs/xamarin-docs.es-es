---
title: Crear un diseño personalizado enXamarin.Forms
description: En este artículo se explica cómo escribir una clase de diseño personalizada y se muestra una clase WrapLayout con distinción de orientación que organiza sus elementos secundarios horizontalmente por la página y, a continuación, ajusta la presentación de los elementos secundarios posteriores a filas adicionales.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3063a644a48a8796b03b1a6acedbbcbfc7acbf7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934269"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Crear un diseño personalizado enXamarin.Forms

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Formsdefine cinco clases de diseño: StackLayout, AbsoluteLayout, RelativeLayout, Grid y FlexLayout, y cada una de ellas organiza sus elementos secundarios de manera diferente. Sin embargo, a veces es necesario organizar el contenido de la página mediante un diseño no proporcionado por Xamarin.Forms . En este artículo se explica cómo escribir una clase de diseño personalizada y se muestra una clase WrapLayout con distinción de orientación que organiza sus elementos secundarios horizontalmente por la página y, a continuación, ajusta la presentación de los elementos secundarios posteriores a filas adicionales._

En Xamarin.Forms , todas las clases de diseño se derivan de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) clase y restringen el tipo genérico a [`View`](xref:Xamarin.Forms.View) y sus tipos derivados. A su vez, la `Layout<T>` clase se deriva de la [`Layout`](xref:Xamarin.Forms.Layout) clase, que proporciona el mecanismo para colocar y ajustar el tamaño de los elementos secundarios.

Cada elemento visual es responsable de determinar su propio tamaño preferido, lo que se conoce como el tamaño *solicitado* . [`Page`](xref:Xamarin.Forms.Page)[`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) los tipos derivados, y son responsables de determinar la ubicación y el tamaño de sus elementos secundarios, o secundarios, relativos a ellos mismos. Por lo tanto, el diseño implica una relación de elementos primarios y secundarios, donde el elemento primario determina cuál debe ser el tamaño de sus elementos secundarios, pero intentará dar cabida al tamaño solicitado del elemento secundario.

Se requiere un conocimiento exhaustivo de los Xamarin.Forms ciclos de diseño e invalidación para crear un diseño personalizado. Ahora se analizarán estos ciclos.

## <a name="layout"></a>Layout

El diseño comienza en la parte superior del árbol visual con una página y continúa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales de una página. Los elementos que son elementos primarios de otros elementos son responsables de ajustar el tamaño y la posición de sus elementos secundarios en relación con ellos mismos.

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase define un [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) que mide un elemento para las operaciones de diseño y un [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) que especifica el área rectangular en la que se representará el elemento. Cuando se inicia una aplicación y se muestra la primera página, un *ciclo de diseño* que consta de las primeras `Measure` llamadas y, a continuación, llama a `Layout` , comienza en el [`Page`](xref:Xamarin.Forms.Page) objeto:

1. Durante el ciclo de diseño, cada elemento primario es responsable de llamar al `Measure` método en sus elementos secundarios.
1. Una vez que se han medido los elementos secundarios, cada elemento primario es responsable de llamar al `Layout` método en sus elementos secundarios.

Este ciclo garantiza que todos los elementos visuales de la página reciban llamadas a los `Measure` `Layout` métodos y. El proceso se muestra en el diagrama siguiente:

![Xamarin.FormsCiclo de diseño](custom-images/layout-cycle.png)

> [!NOTE]
> Tenga en cuenta que los ciclos de diseño también pueden producirse en un subconjunto del árbol visual si algo cambia para afectar al diseño. Esto incluye los elementos que se agregan o quitan de una colección, como en [`StackLayout`](xref:Xamarin.Forms.StackLayout) , un cambio en la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propiedad de un elemento o un cambio en el tamaño de un elemento.

Cada Xamarin.Forms clase que tiene una `Content` propiedad o `Children` tiene un método reemplazable [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) . Las clases de diseño personalizadas que derivan de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) deben invalidar este método y asegurarse de que [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) y [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)), se llama a los métodos en todos los elementos secundarios del elemento para proporcionar el diseño personalizado deseado.

Además, cada clase que deriva de [`Layout`](xref:Xamarin.Forms.Layout) o [`Layout<View>`](xref:Xamarin.Forms.Layout`1) debe invalidar el [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método, que es donde una clase de diseño determina el tamaño que debe realizar las llamadas a [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) de sus elementos secundarios.

> [!NOTE]
> Los elementos determinan su tamaño en función de las *restricciones*, que indican la cantidad de espacio disponible para un elemento dentro del elemento primario del elemento. Restricciones que se pasan a [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) y [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) los métodos pueden oscilar entre 0 y `Double.PositiveInfinity` . Un elemento está *restringido*, o *totalmente restringido*, cuando recibe una llamada a su [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) con argumentos no infinitos: el elemento está restringido a un tamaño determinado. Un elemento no está *restringido*, o *parcialmente restringido*, cuando recibe una llamada a su `Measure` método con al menos un argumento igual a `Double.PositiveInfinity` : se puede considerar que la restricción infinita indica el ajuste automático de tamaño.

## <a name="invalidation"></a>Invalidación

La invalidación es el proceso por el que un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño o la posición correctos. Por ejemplo, si [`FontSize`](xref:Xamarin.Forms.Button.FontSize) se cambia la propiedad de un [`Button`](xref:Xamarin.Forms.Button) , `Button` se dice que no es válido porque ya no tendrá el tamaño correcto. Al cambiar el tamaño `Button` , el puede tener un efecto de rizo de los cambios en el diseño a través del resto de una página.

Los elementos se invalidan mediante la invocación del [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método, por lo general cuando una propiedad del elemento cambia, lo que podría dar lugar a un nuevo tamaño del elemento. Este método desencadena el [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, que el elemento primario del elemento controla para desencadenar un nuevo ciclo de diseño.

La [`Layout`](xref:Xamarin.Forms.Layout) clase establece un controlador para el [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento en cada elemento secundario agregado a su `Content` propiedad o `Children` colección y Desasocia el controlador cuando se quita el elemento secundario. Por lo tanto, todos los elementos del árbol visual que tienen elementos secundarios se avisan cuando uno de sus elementos secundarios cambia de tamaño. En el diagrama siguiente se muestra cómo un cambio en el tamaño de un elemento en el árbol visual puede producir cambios que se rellenen en el árbol:

![Invalidación en el árbol visual](custom-images/invalidation.png)

Sin embargo, la `Layout` clase intenta restringir el impacto de un cambio en el tamaño de un elemento secundario en el diseño de una página. Si el diseño tiene restricciones de tamaño, un cambio de tamaño secundario no afecta a nada más alto que el diseño primario en el árbol visual. Sin embargo, normalmente un cambio en el tamaño de un diseño afecta al modo en que el diseño organiza sus elementos secundarios. Por lo tanto, cualquier cambio en el tamaño de un diseño iniciará un ciclo de diseño para el diseño y el diseño recibirá las llamadas a sus [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) métodos y.

La [`Layout`](xref:Xamarin.Forms.Layout) clase también define un [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) método que tiene un propósito similar al [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método. El `InvalidateLayout` método se debe invocar cada vez que se realiza un cambio que afecta al modo en que el diseño coloca y ajusta el tamaño de sus elementos secundarios. Por ejemplo, la `Layout` clase invoca el `InvalidateLayout` método cada vez que se agrega o se quita un elemento secundario de un diseño.

[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)Se puede invalidar para implementar una memoria caché con el fin de minimizar las invocaciones repetitivas de [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) de los elementos secundarios del diseño. Al invalidar el `InvalidateLayout` método, se proporcionará una notificación de Cuándo se agregan o quitan elementos secundarios en el diseño. Del mismo modo, el [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método se puede invalidar para proporcionar una notificación cuando cambia el tamaño de uno de los elementos secundarios del diseño. En el caso de las invalidaciones de método, un diseño personalizado debe responder borrando la memoria caché. Para obtener más información, consulte [calcular y almacenar en caché datos de diseño](#calculate-and-cache-layout-data).

## <a name="create-a-custom-layout"></a>Crear un diseño personalizado

El proceso para crear un diseño personalizado es el siguiente:

1. Cree una clase que se derive de la clase `Layout<View>`. Para obtener más información, vea [crear un WrapLayout](#create-a-wraplayout).
1. [*opcional*] Agregue propiedades, respaldadas por propiedades enlazables, para los parámetros que se deben establecer en la clase de diseño. Para obtener más información, vea [Agregar propiedades respaldadas por propiedades enlazables](#add-properties-backed-by-bindable-properties).
1. Invalide el [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método para invocar [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) en todos los elementos secundarios del diseño y devuelven un tamaño solicitado para el diseño. Para obtener más información, vea [invalidar el método de la acción](#override-the-onmeasure-method).
1. Invalide el [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método para invocar [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) en todos los elementos secundarios del diseño. Error al invocar [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) en cada elemento secundario de un diseño, el elemento secundario no recibirá nunca un tamaño o una posición correctos y, por lo tanto, el elemento secundario no estará visible en la página. Para obtener más información, vea [invalidar el método LayoutChildren](#override-the-layoutchildren-method).

    > [!NOTE]
    > Al enumerar los elementos secundarios en [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) y [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) invalida, omita cualquier elemento secundario cuya [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propiedad esté establecida en `false` . Esto garantizará que el diseño personalizado no deja espacio para los elementos secundarios invisibles.

1. [*opcional*] Invalide el [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) método para recibir una notificación cuando se agreguen o quiten elementos secundarios del diseño. Para obtener más información, vea [invalidar el método InvalidateLayout](#override-the-invalidatelayout-method).
1. [*opcional*] Invalide el [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método para recibir una notificación cuando uno de los elementos secundarios del diseño cambie de tamaño. Para obtener más información, vea [invalidar el método OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

> [!NOTE]
> Tenga en cuenta que la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) invalidación no se invocará si el tamaño del diseño se rige por su elemento primario, en lugar de sus elementos secundarios. Sin embargo, se invocará la invalidación si una o ambas restricciones son infinitas, o si la clase de diseño tiene valores no predeterminados [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) o de [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedad. Por esta razón, la [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) invalidación no puede depender de los tamaños secundarios obtenidos durante la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) llamada al método. En su lugar, `LayoutChildren` debe invocar [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) en los elementos secundarios del diseño, antes de invocar [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)). Como alternativa, el tamaño de los elementos secundarios obtenidos en la `OnMeasure` invalidación se puede almacenar en caché para evitar las `Measure` invocaciones posteriores en la `LayoutChildren` invalidación, pero la clase de diseño deberá saber cuándo es necesario obtener los tamaños de nuevo. Para obtener más información, consulte [calcular y almacenar en caché datos de diseño](#calculate-and-cache-layout-data).

A continuación, la clase de diseño se puede consumir agregándola a un [`Page`](xref:Xamarin.Forms.Page) y agregando elementos secundarios al diseño. Para obtener más información, vea [consumir The WrapLayout](#consume-the-wraplayout).

### <a name="create-a-wraplayout"></a>Creación de un WrapLayout

La aplicación de ejemplo muestra una clase con distinción de orientación `WrapLayout` que organiza los elementos secundarios horizontalmente por la página y, a continuación, ajusta la presentación de los elementos secundarios posteriores a filas adicionales.

La `WrapLayout` clase asigna la misma cantidad de espacio para cada elemento secundario, conocido como el *tamaño de celda*, en función del tamaño máximo de los elementos secundarios. Los elementos secundarios menores que el tamaño de la celda se pueden colocar dentro de la celda en función de sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valores de propiedad y.

La `WrapLayout` definición de clase se muestra en el ejemplo de código siguiente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>Calcular y almacenar en caché datos de diseño

La `LayoutData` estructura almacena los datos sobre una colección de elementos secundarios en una serie de propiedades:

- `VisibleChildCount`: el número de elementos secundarios que están visibles en el diseño.
- `CellSize`: el tamaño máximo de todos los elementos secundarios, ajustado al tamaño del diseño.
- `Rows`: el número de filas.
- `Columns`: el número de columnas.

El `layoutDataCache` campo se usa para almacenar varios `LayoutData` valores. Cuando se inicia la aplicación, se `LayoutData` almacenan en caché dos objetos en el `layoutDataCache` Diccionario para la orientación actual: uno para los argumentos de restricción para la `OnMeasure` invalidación y otro para los `width` `height` argumentos y para la `LayoutChildren` invalidación. Al girar el dispositivo en orientación horizontal, se `OnMeasure` invocará de nuevo la invalidación y la `LayoutChildren` invalidación, lo que provocará que se `LayoutData` almacenen en el Diccionario otros dos objetos. Sin embargo, al devolver el dispositivo a la orientación vertical, no es necesario realizar más cálculos porque `layoutDataCache` ya tiene los datos necesarios.

En el ejemplo de código siguiente se muestra el `GetLayoutData` método, que calcula las propiedades de `LayoutData` estructuradas en función de un tamaño determinado:

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

El `GetLayoutData` método realiza las operaciones siguientes:

- Determina si un valor calculado `LayoutData` ya está en la memoria caché y lo devuelve si está disponible.
- De lo contrario, enumera todos los elementos secundarios, invocando el `Measure` método en cada elemento secundario con un ancho y un alto infinitos, y determina el tamaño máximo de los elementos secundarios.
- Siempre que haya al menos un elemento secundario visible, calcula el número de filas y columnas necesarias y, a continuación, calcula el tamaño de la celda para los elementos secundarios en función de las dimensiones de `WrapLayout` . Tenga en cuenta que el tamaño de la celda suele ser ligeramente más grande que el tamaño máximo de los elementos secundarios, pero también puede ser menor si el elemento secundario es lo suficientemente amplio como `WrapLayout` para el elemento secundario más amplio o lo suficientemente alto para el elemento secundario más alto.
- Almacena el nuevo `LayoutData` valor en la memoria caché.

#### <a name="add-properties-backed-by-bindable-properties"></a>Agregar propiedades respaldadas por propiedades enlazables

La `WrapLayout` clase define `ColumnSpacing` `RowSpacing` las propiedades y, cuyos valores se usan para separar las filas y las columnas del diseño, y que están respaldadas por propiedades enlazables. Las propiedades enlazables se muestran en el ejemplo de código siguiente:

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

El controlador modificado por la propiedad de cada propiedad enlazable invoca la `InvalidateLayout` invalidación del método para desencadenar un nuevo paso de diseño en `WrapLayout` . Para obtener más información, vea [invalidar el método InvalidateLayout](#override-the-invalidatelayout-method) e [invalidar el método OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

#### <a name="override-the-onmeasure-method"></a>Invalidar el método de la acción

La `OnMeasure` invalidación se muestra en el ejemplo de código siguiente:

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

La invalidación invoca el `GetLayoutData` método y construye un `SizeRequest` objeto a partir de los datos devueltos, a la vez que también tiene en cuenta los valores de las `RowSpacing` `ColumnSpacing` propiedades y. Para obtener más información sobre el `GetLayoutData` método, consulte [calcular y almacenar en caché datos de diseño](#calculate-and-cache-layout-data).

> [!IMPORTANT]
> [ `Measure` ] (XREF: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)) y [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) los métodos no deben solicitar nunca una dimensión infinita devolviendo un [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) valor con una propiedad establecida en `Double.PositiveInfinity` . Sin embargo, al menos uno de los argumentos de restricción en `OnMeasure` puede ser `Double.PositiveInfinity` .

#### <a name="override-the-layoutchildren-method"></a>Invalidar el método LayoutChildren

La `LayoutChildren` invalidación se muestra en el ejemplo de código siguiente:

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

La invalidación comienza con una llamada al `GetLayoutData` método y, a continuación, enumera todos los elementos secundarios para ajustar su tamaño y colocarlos dentro de la celda de cada elemento secundario. Esto se logra invocando [ `LayoutChildIntoBoundingRegion` ] (XREF: Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle)), que se usa para colocar un elemento secundario dentro de un rectángulo basándose en sus [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valores de propiedad y. Esto es equivalente a hacer una llamada a [ `Layout` ] (XREF: Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)).

> [!NOTE]
> Tenga en cuenta que el rectángulo que se pasa al `LayoutChildIntoBoundingRegion` método incluye todo el área en la que puede residir el elemento secundario.

Para obtener más información sobre el `GetLayoutData` método, consulte [calcular y almacenar en caché datos de diseño](#calculate-and-cache-layout-data).

#### <a name="override-the-invalidatelayout-method"></a>Invalidar el método InvalidateLayout

La [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) invalidación se invoca cuando se agregan o quitan elementos secundarios en el diseño, o cuando una de las `WrapLayout` propiedades cambia de valor, como se muestra en el ejemplo de código siguiente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño y descarta toda la información de diseño almacenada en caché.

> [!NOTE]
> Para detener la [`Layout`](xref:Xamarin.Forms.Layout) clase que invoca al [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) método cada vez que se agrega o se quita un elemento secundario de un diseño, invalide [ `ShouldInvalidateOnChildAdded` ] (XREF: Xamarin.Forms . Layout. ShouldInvalidateOnChildAdded ( Xamarin.Forms . View)) y [ `ShouldInvalidateOnChildRemoved` ] (XREF: Xamarin.Forms . Layout. ShouldInvalidateOnChildRemoved ( Xamarin.Forms . View)) y devuelven `false` . A continuación, la clase de diseño puede implementar un proceso personalizado cuando se agregan o quitan elementos secundarios.

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Invalidar el método OnChildMeasureInvalidated

La [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) invalidación se invoca cuando uno de los elementos secundarios del diseño cambia de tamaño y se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño secundario y descarta toda la información de diseño almacenada en caché.

### <a name="consume-the-wraplayout"></a>Consumir el WrapLayout

La `WrapLayout` clase se puede consumir colocándolo en un [`Page`](xref:Xamarin.Forms.Page) tipo derivado, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

A continuación se muestra el código de C# equivalente:

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Después, los elementos secundarios se pueden agregar al `WrapLayout` según sea necesario. En el ejemplo de código siguiente [`Image`](xref:Xamarin.Forms.Image) se muestran los elementos que se agregan a `WrapLayout` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

Cuando aparece la página que contiene el `WrapLayout` , la aplicación de ejemplo obtiene acceso de forma asincrónica a un archivo JSON remoto que contiene una lista de fotografías, crea un [`Image`](xref:Xamarin.Forms.Image) elemento para cada foto y lo agrega a `WrapLayout` . El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

![Capturas de pantallas verticales de aplicación de ejemplo](custom-images/portait-screenshots.png)

Las siguientes capturas de pantallas muestran el `WrapLayout` después de que se haya girado a la orientación horizontal:

![Ejemplo de captura de pantalla de escenario de aplicación de iOS de ejemplo ejemplo de captura de pantalla de ejemplo de aplicación ](custom-images/landscape-ios.png)
 ![ ](custom-images/landscape-android.png)
 ![ para UWP](custom-images/landscape-uwp.png)

El número de columnas de cada fila depende del tamaño de la fotografía, el ancho de la pantalla y el número de píxeles por unidad independiente del dispositivo. Los [`Image`](xref:Xamarin.Forms.Image) elementos cargan las fotos de forma asincrónica y, por lo tanto, la `WrapLayout` clase recibirá llamadas frecuentes a su [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método, ya que cada `Image` elemento recibe un nuevo tamaño basado en la foto cargada.

## <a name="related-links"></a>Vínculos relacionados

- [WrapLayout (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Diseños personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Crear diseños personalizados en Xamarin.Forms (vídeo)](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [Layout\<T>](xref:Xamarin.Forms.Layout`1)
- [Diseño](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
