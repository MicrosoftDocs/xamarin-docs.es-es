---
title: Crear un diseño personalizado
description: En este artículo se explica cómo escribir una clase de diseño personalizado y se muestra una clase de WrapLayout minúsculas orientación que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, ajusta la presentación de los elementos secundarios subsiguientes a las filas adicionales.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 11707a1e871b0988847ab4a2c266d268db063000
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645200"
---
# <a name="creating-a-custom-layout"></a>Crear un diseño personalizado

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Forms define cuatro clases de diseño: StackLayout, AbsoluteLayout, RelativeLayout y cuadrícula, y cada uno de ellos organiza a sus elementos secundarios de forma diferente. Sin embargo, a veces es necesario organizar el contenido de la página con un diseño que no se proporciona mediante Xamarin.Forms. En este artículo se explica cómo escribir una clase de diseño personalizado y se muestra una clase de WrapLayout minúsculas orientación que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, ajusta la presentación de los elementos secundarios subsiguientes a las filas adicionales._

## <a name="overview"></a>Información general

En Xamarin.Forms, se derivan todas las clases de diseño de la [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) clase y restringir el tipo genérico a [ `View` ](xref:Xamarin.Forms.View) y sus tipos derivados. A su vez, el `Layout<T>` clase se deriva de la [ `Layout` ](xref:Xamarin.Forms.Layout) (clase), que proporciona el mecanismo para colocar y secundarios de ajuste de tamaño los elementos.

Todos los elementos visuales es responsable de determinar su propio tamaño preferido, que se conoce como el *solicitado* tamaño. [`Page`](xref:Xamarin.Forms.Page), [ `Layout` ](xref:Xamarin.Forms.Layout), y [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) tipos derivados son responsables de determinar la ubicación y el tamaño de sus secundarios o elementos secundarios, con respecto a ellos mismos. Por lo tanto, el diseño implica una relación de elementos primarios y secundarios, donde el elemento primario determina cuál debe ser el tamaño de sus elementos secundarios, pero intentará adaptarse al tamaño solicitado del elemento secundario.

Se requiere un conocimiento exhaustivo de los ciclos de diseño y la invalidación de Xamarin.Forms para crear un diseño personalizado. Ahora se tratarán estos ciclos.

## <a name="layout"></a>Diseño

Diseño comienza en la parte superior del árbol visual con una página y pasa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales en una página. Los elementos que son elementos primarios a otros elementos son responsables de ajuste de tamaño y la posición de sus elementos secundarios en relación con ellos mismos.

El [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) clase define un [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método que mide un elemento para las operaciones de diseño, y un [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método que especifica el área rectangular que se representa en el elemento. Cuando se inicia una aplicación y se muestra la primera página, un *ciclo de diseño* primero que consta de `Measure` llamadas y, a continuación, `Layout` llama, se inicia en el [ `Page` ](xref:Xamarin.Forms.Page) objeto:

1. Durante el ciclo de diseño, cada elemento primario es responsable de llamar a la `Measure` método en sus elementos secundarios.
1. Después de haberse medidos los elementos secundarios, cada elemento primario es responsable de llamar a la `Layout` método en sus elementos secundarios.

Este ciclo garantiza que todos los elementos visuales en la página recibe las llamadas a la `Measure` y `Layout` métodos. El proceso se muestra en el diagrama siguiente:

![](custom-images/layout-cycle.png "Ciclo de diseño de Xamarin.Forms")

> [!NOTE]
> Tenga en cuenta que los ciclos de diseño también pueden producirse en un subconjunto del árbol visual si algo cambia para afectar al diseño. Esto incluye los elementos que se agregan o quitan de una colección, como en un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), un cambio en el [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) propiedad de un elemento o un cambio en el tamaño de un elemento.

Todas las clases de Xamarin.Forms que tiene un `Content` o un `Children` propiedad tiene un reemplazable [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método. Las clases de diseño personalizado que se derivan de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) debe invalidar este método y asegúrese de que el [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) y [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) son métodos se llama en todos los elementos secundarios del elemento, para proporcionar el diseño personalizado deseado.

Además, cada clase que derive de [ `Layout` ](xref:Xamarin.Forms.Layout) o [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) debe invalidar el [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método, que es donde una clase de diseño Determina el tamaño que deba estar realizando llamadas a la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) métodos de sus elementos secundarios.

> [!NOTE]
> Elementos determinan su tamaño según *restricciones*, que indican cuánto espacio hay disponible para un elemento dentro primario del elemento. Las restricciones se pasan a la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) y [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos pueden oscilar entre 0 y `Double.PositiveInfinity`. Es un elemento *restringida*, o *completamente restringida*, cuando recibe una llamada a su [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método con argumentos no infinita - está restringido el elemento un tamaño determinado. Es un elemento *sin restricciones*, o *parcialmente restringida*, cuando recibe una llamada a su `Measure` método con igual que al menos un argumento `Double.PositiveInfinity` : puede ser la restricción infinita considerar que indica el ajuste automático de tamaño.

## <a name="invalidation"></a>Invalidación

Invalidación es el proceso por el cual un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño correcto o la posición. Por ejemplo, si la [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize) propiedad de un [ `Button` ](xref:Xamarin.Forms.Button) cambios, el `Button` se dice que es válida porque ya no tiene el tamaño correcto. Cambiar el tamaño de la `Button` , a continuación, puede tener un efecto dominó de cambios de diseño en el resto de una página.

Invalidarán elementos propios invocando el [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método, por lo general cuando cambia una propiedad del elemento que podría dar lugar a un nuevo tamaño del elemento. Este método desencadena el [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento, que controla el primario elemento para desencadenar un nuevo ciclo de diseño.

El [ `Layout` ](xref:Xamarin.Forms.Layout) clase establece un controlador para el [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) eventos en cada elemento secundario que se agrega a su `Content` propiedad o `Children` colección y desasocia el controlador cuando el se quita el elemento secundario. Por lo tanto, todos los elementos en el árbol visual que tiene elementos secundarios es una alerta cuando uno de sus elementos secundarios cambia de tamaño. El siguiente diagrama ilustra cómo un cambio en el tamaño de un elemento en el árbol visual puede producir cambios que ripple el árbol:

![](custom-images/invalidation.png "Invalidación del árbol Visual")

Sin embargo, la `Layout` clase intenta restringir el impacto de un cambio de tamaño de un elemento secundario en el diseño de una página. Si el diseño es limitada de tamaño, a continuación, un cambio de tamaño del elemento secundario no afecta a algo mayor que el diseño del elemento primario en el árbol visual. Sin embargo, normalmente un cambio en el tamaño de un diseño afecta a cómo el diseño organiza a sus elementos secundarios. Por lo tanto, cualquier cambio en el tamaño de un diseño se iniciará un ciclo de diseño para el diseño y el diseño recibirán llamadas a su [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) y [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) métodos.

El [ `Layout` ](xref:Xamarin.Forms.Layout) clase define también un [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) método que tiene una finalidad similar a la [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) método. El `InvalidateLayout` debe invocarse el método cada vez que se realiza un cambio que afecta a cómo el diseño de posiciones y tamaños de sus elementos secundarios. Por ejemplo, el `Layout` clase invoca el `InvalidateLayout` método cada vez que se agrega o quita de un diseño de un elemento secundario.

El [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) se puede invalidar para implementar una memoria caché para minimizar las llamadas repetitivas de la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) métodos de elementos secundarios del diseño. Reemplazar el `InvalidateLayout` método proporcionará una notificación cuando se agregan o se quita del diseño los elementos secundarios. De forma similar, el [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) se puede invalidar el método para proporcionar una notificación cuando uno de los elementos secundarios del diseño cambia de tamaño. Para los reemplazos de método debe responder un diseño personalizado al borrar la caché. Para obtener más información, consulte [calcular y almacenar en caché datos](#caching).

## <a name="creating-a-custom-layout"></a>Crear un diseño personalizado

El proceso para crear un diseño personalizado es como sigue:

1. Cree una clase que se derive de la clase `Layout<View>`. Para obtener más información, consulte [creando un WrapLayout](#creating).
1. [*opcional*] agregar propiedades, respaldadas por propiedades enlazables, para cualquier parámetro que se debe establecer en la clase de diseño. Para obtener más información, consulte [agregando propiedades respaldadas por propiedades enlazables](#adding_properties).
1. Invalidar el [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) método para invocar el [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método en todo el diseño los elementos secundarios y devolver un tamaño solicitado para el diseño. Para obtener más información, consulte [invalidación del método OnMeasure](#onmeasure).
1. Invalidar el [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método para invocar el [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método en elementos secundarios de la del diseño. Error al invocar el [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método en cada elemento secundario en un diseño dará como resultado el elemento secundario nunca reciben un tamaño correcto o una posición y, por lo tanto, el elemento secundario no estará visible en la página. Para obtener más información, consulte [invalidación del método LayoutChildren](#layoutchildren).

  > [!NOTE]
>  Al enumerar los elementos secundarios en el [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) y [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) invalidaciones, omitir cualquier elemento secundario cuyo [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) propiedad está establecida en `false`. Esto garantizará que el diseño personalizado no deja espacio para elementos secundarios visibles.

1. [*opcional*] invalidar el [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) método para recibir notificaciones cuando los elementos secundarios se agregan o se quita del diseño. Para obtener más información, consulte [invalidación del método InvalidateLayout](#invalidatelayout).
1. [*opcional*] invalidar el [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) método para recibir una notificación cuando uno de los elementos secundarios del diseño cambia de tamaño. Para obtener más información, consulte [invalidación del método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Tenga en cuenta que el [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) override no se invoca si el tamaño del diseño se rige por su elemento primario, en lugar de sus elementos secundarios. Sin embargo, se invocará la invalidación si una o ambas de las restricciones son infinitos, o bien, si la clase de diseño no predeterminado [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) o [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) los valores de propiedad. Por este motivo, el [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) invalidación no puede depender de tamaños secundarios obtenidos durante el [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) llamada al método. En su lugar, `LayoutChildren` debe invocar el [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) método en elementos secundarios del diseño, antes de invocar el [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método. Como alternativa, el tamaño de los elementos secundarios se obtuvo en el `OnMeasure` invalidación puede almacenarse en caché para evitar más adelante `Measure` invocaciones en el `LayoutChildren` invalidación, pero la clase de diseño deberá saber cuándo es necesario volver a obtener los tamaños. Para obtener más información, consulte [calcular y almacenar en caché datos de diseño](#caching).

La clase de diseño puede utilizarse, a continuación, agregándolo a un [ `Page` ](xref:Xamarin.Forms.Page)y mediante la adición de elementos secundarios para el diseño. Para obtener más información, consulte [consumiendo el WrapLayout](#consuming).

<a name="creating" />

### <a name="creating-a-wraplayout"></a>Creación de un WrapLayout

La aplicación de ejemplo muestra una orientación distinción `WrapLayout` clase que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, ajusta la presentación de los elementos secundarios subsiguientes a las filas adicionales.

El `WrapLayout` clase asigna la misma cantidad de espacio para cada elemento secundario, conocido como el *tamaño de la celda*, según el tamaño máximo de los elementos secundarios. Los elementos secundarios más pequeños que el tamaño de celda se puede colocar dentro de la celda según sus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) los valores de propiedad.

El `WrapLayout` definición de clase se muestra en el ejemplo de código siguiente:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>Calcular y almacenar en caché datos de diseño

El `LayoutData` estructura almacena los datos de una colección de elementos secundarios en un número de propiedades:

- `VisibleChildCount` : el número de elementos secundarios que están visibles en el diseño.
- `CellSize` – el tamaño máximo de todos los elementos secundarios, ajustado el tamaño del diseño.
- `Rows` : el número de filas.
- `Columns` : el número de columnas.

El `layoutDataCache` campo se utiliza para almacenar varios `LayoutData` valores. Cuando se inicia la aplicación, dos `LayoutData` se almacenarán en caché de objetos en el `layoutDataCache` diccionario para la orientación actual: uno para los argumentos de restricción a la `OnMeasure` invalidación y otra para el `width` y `height` argumentos para el `LayoutChildren` invalidar. Al girar el dispositivo en orientación horizontal, el `OnMeasure` invalidar y `LayoutChildren` se invocará nuevo reemplazo, lo que producirá dos otro `LayoutData` objetos que se va a almacenar en caché en el diccionario. Sin embargo, cuando el dispositivo se vuelve a la orientación vertical, ningún cálculo adicional es necesario porque el `layoutDataCache` ya tiene los datos necesarios.

El siguiente ejemplo de código muestra la `GetLayoutData` método, que calcula las propiedades de la `LayoutData` estructurados según un tamaño determinado:

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

El `GetLayoutData` método realiza las siguientes operaciones:

- Determina si un calculado `LayoutData` valor ya está en la memoria caché y lo devuelve si está disponible.
- En caso contrario, enumera a través de todos los elementos secundarios, invocar el `Measure` método en cada elemento secundario con un ancho infinito y el alto y determina el tamaño máximo de secundarios.
- Siempre que hay al menos un elemento secundario visible, calcula el número de filas y columnas necesarios y, a continuación, calcula un tamaño de celda para los elementos secundarios en función de las dimensiones de la `WrapLayout`. Tenga en cuenta que el tamaño de celda normalmente es ligeramente más amplio que el tamaño máximo de secundarios, sino que también podría ser más pequeño si la `WrapLayout` no es lo suficientemente ancha para secundario más amplia o lo suficientemente alto para el elemento secundario más alto.
- Almacena el nuevo `LayoutData` valor en la memoria caché.

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>Agregar propiedades respaldadas por propiedades enlazables

El `WrapLayout` define la clase `ColumnSpacing` y `RowSpacing` propiedades cuyos valores se usan para separar las filas y columnas en el diseño y que están respaldadas por propiedades enlazables. Las propiedades enlazables se muestran en el ejemplo de código siguiente:

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

Invoca el controlador de cambio de propiedad de cada propiedad enlazable el `InvalidateLayout` invalidación del método para desencadenar un nuevo diseño de pasar el `WrapLayout`. Para obtener más información, consulte [invalidación del método InvalidateLayout](#invalidatelayout) y [invalidación del método OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>Invalidación del método OnMeasure

El `OnMeasure` invalidación se muestra en el ejemplo de código siguiente:

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

Invoca la invalidación del `GetLayoutData` método y construcciones un `SizeRequest` objeto a partir de los datos devueltos, teniendo en cuenta también la `RowSpacing` y `ColumnSpacing` los valores de propiedad. Para obtener más información sobre la `GetLayoutData` método, consulte [calcular y almacenar en caché datos](#caching).

> [!IMPORTANT]
> El [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) y [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) métodos nunca deben solicitar una dimensión infinita devolviendo un [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest) valor con una propiedad establecida en `Double.PositiveInfinity`. Sin embargo, al menos uno de los argumentos de restricción `OnMeasure` puede ser `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>Invalidación del método LayoutChildren

El `LayoutChildren` invalidación se muestra en el ejemplo de código siguiente:

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

La invalidación comienza con una llamada a la `GetLayoutData` método y, a continuación, enumera todos los elementos secundarios para el tamaño y colocarlos dentro de la celda de cada elemento secundario. Esto se logra invocando el [ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)) método, que se usa para colocar un elemento secundario dentro de un rectángulo en función de su [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) y [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)los valores de propiedad. Esto equivale a hacer una llamada a la secundaria [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método.

> [!NOTE]
> Tenga en cuenta que el rectángulo que se pasó a la `LayoutChildIntoBoundingRegion` método incluye el área completa en el que puede residir el elemento secundario.

Para obtener más información sobre la `GetLayoutData` método, consulte [calcular y almacenar en caché datos](#caching).

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>Invalidación del método InvalidateLayout

El [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) invalidación se invoca cuando los elementos secundarios se agregan o quitan del diseño, o cuando una de las `WrapLayout` Propiedades cambie el valor, como se muestra en el ejemplo de código siguiente:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño y descarta toda la información de diseño almacenado en caché.

> [!NOTE]
> Para detener el [ `Layout` ](xref:Xamarin.Forms.Layout) clase invocar el [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) invalidar el método cada vez que un elemento secundario se agrega o quita de un diseño, el [ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View)) y [ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View)) métodos y devolver `false`. La clase de diseño, a continuación, puede implementar un proceso personalizado cuando se agregan o quitan elementos secundarios.

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>Invalidación del método OnChildMeasureInvalidated

El [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) invalidación se invoca cuando cambia el tamaño de uno de los elementos secundarios del diseño y se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

La invalidación invalida el diseño del elemento secundario y descarta toda la información de diseño almacenado en caché.

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>Consumir el WrapLayout

El `WrapLayout` clase puede utilizarse colocando en un [ `Page` ](xref:Xamarin.Forms.Page) tipo derivado, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

El código de C# equivalente se muestra a continuación:

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

Los elementos secundarios, a continuación, se pueden agregar a la `WrapLayout` según sea necesario. El siguiente ejemplo de código muestra [ `Image` ](xref:Xamarin.Forms.Image) elementos que se agrega a la `WrapLayout`:

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

Cuando la página que contiene el `WrapLayout` aparece, la aplicación de ejemplo de forma asincrónica obtiene acceso a un archivo remoto de JSON que contiene una lista de fotos, crea un [ `Image` ](xref:Xamarin.Forms.Image) de fotografías para cada elemento y lo agrega a la `WrapLayout`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

![](custom-images/portait-screenshots.png "Capturas de pantalla de vertical de aplicación de ejemplo")

Capturas de pantalla siguientes se muestra el `WrapLayout` después de se ha girado en orientación horizontal:

![](custom-images/landscape-ios.png "Captura de pantalla de aplicación panorama de iOS de ejemplo")
![](custom-images/landscape-android.png "captura de pantalla de ejemplo Android aplicación panorama")
![](custom-images/landscape-uwp.png " Captura de pantalla de ejemplo UWP aplicación panorama")

El número de columnas en cada fila depende del tamaño de fotografía, el ancho de pantalla y el número de píxeles independientes del dispositivo unitario. El [ `Image` ](xref:Xamarin.Forms.Image) elementos cargar las fotos, de forma asincrónica y, por tanto, el `WrapLayout` clase recibirán llamadas frecuentes a su [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) método cada `Image` elemento recibe un nuevo tamaño en función de la fotografía cargada.

## <a name="related-links"></a>Vínculos relacionados

- [WrapLayout (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Diseños personalizados](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Creación de los diseños personalizados en Xamarin.Forms (vídeo)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Diseño<T>](xref:Xamarin.Forms.Layout`1)
- [Diseño](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
