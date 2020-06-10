---
title: " Xamarin.Forms RelativeLayout" Descripción: "en este artículo se explica cómo usar la Xamarin.Forms clase RelativeLayout para crear interfaces de IU que se escalen para ajustarse a cualquier tamaño de pantalla".
MS. Prod: Xamarin ms. AssetID: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 11/25/2015 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-relativelayout"></a>Xamarin.FormsRelativeLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

`RelativeLayout`se utiliza para colocar y ajustar el tamaño de las vistas en relación con las propiedades de las vistas de diseño o del mismo nivel. A diferencia `AbsoluteLayout` de, no `RelativeLayout` tiene el concepto de delimitador móvil y no tiene medios para colocar elementos en relación con los bordes inferior o derecho del diseño. `RelativeLayout`permite colocar elementos fuera de sus propios límites.

[![](relative-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](relative-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Propósito

`RelativeLayout`se puede usar para colocar las vistas en la pantalla en relación con el diseño general o con otras vistas.

![](relative-layout-images/flag.png "RelativeLayout Exploration")

## <a name="usage"></a>Uso

### <a name="understanding-constraints"></a>Descripción de las restricciones

La colocación y el ajuste del tamaño de una vista en una `RelativeLayout` se realiza con restricciones. Una expresión de restricción puede incluir la siguiente información:

- **Tipo** &ndash; de indica si la restricción es relativa al elemento primario o a otra vista.
- **Propiedad** &ndash; la propiedad que se va a usar como base para la restricción.
- **Factor** &ndash; factor que se va a aplicar al valor de propiedad.
- **Constante** &ndash; de valor que se va a usar como desplazamiento del valor.
- **ElementName** &ndash; nombre de la vista a la que la restricción es relativa.

En XAML, las restricciones se expresan como `ConstraintExpression` s. Considere el ejemplo siguiente:

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

En C#, las restricciones se expresan de forma ligeramente diferente, mediante el uso de funciones en lugar de expresiones en la vista. Las restricciones se especifican como argumentos en el método del diseño `Add` :

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

Tenga en cuenta los siguientes aspectos del diseño anterior:

- Las `x` `y` restricciones y se especifican con sus propias restricciones.
- En C#, las restricciones relativas se definen como funciones. Conceptos como no `Factor` están allí, pero se pueden implementar manualmente.
- La `x` coordenada del cuadro se define como la mitad del ancho del elemento primario,-100.
- La `y` coordenada del cuadro se define como la mitad del alto del elemento primario,-100.

> [!NOTE]
> Debido a la forma en que se definen las restricciones, es posible crear diseños más complejos en C# de los que se pueden especificar con XAML.

En los dos ejemplos anteriores se definen las restricciones como `RelativeToParent` &ndash; es decir, sus valores son relativos al elemento primario. También es posible definir restricciones en relación con otra vista. Esto permite diseños más intuitivos (para el desarrollador) y puede hacer que el propósito del código de diseño sea más evidente.

Considere un diseño en el que un elemento debe ser de 20 píxeles más bajo que otro. Si ambos elementos se definen con valores constantes, el menor podría tener su `Y` restricción definida como una constante de 20 píxeles mayor que la `Y` restricción del elemento superior. Este enfoque es corto si el elemento superior se coloca usando una proporción, de modo que no se conoce el tamaño del píxel. En ese caso, la restricción del elemento en función de la posición de otro elemento es más sólida:

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

Para lograr el mismo diseño en C#:

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

Esto genera el siguiente resultado, con la posición del cuadro azul determinada en _relación_ con la posición del cuadro rojo:

![](relative-layout-images/red-blue-box.png "RelativeLayout with Red and Blue BoxViews")

### <a name="sizing"></a>Ajuste de tamaño

Las vistas diseñadas por `RelativeLayout` tienen dos opciones para especificar su tamaño:

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest`y `WidthRequest` especifican el alto y el ancho previstos de la vista, pero pueden reemplazarse por diseños según sea necesario. `WidthConstraint`y `HeightConstraint` permiten establecer el alto y el ancho como un valor relativo a las propiedades del diseño u otra vista, o como un valor constante.

## <a name="exploring-a-complex-layout"></a>Exploración de un diseño complejo
Cada uno de los diseños tiene puntos fuertes y débiles para crear diseños concretos. En esta serie de artículos de diseño, se ha creado una aplicación de ejemplo con el mismo diseño de página implementado mediante tres diseños diferentes.

Considere el siguiente código XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

El código anterior da como resultado el siguiente diseño:

![](relative-layout-images/relative.png "Complex RelativeLayout")

Observe que `RelativeLayouts` los elementos están anidados porque, en algunos casos, el anidamiento de diseños puede ser más fácil que presentar todos los elementos dentro del mismo diseño. Observe también que algunos elementos son `RelativeToView` , ya que permite un diseño más sencillo y intuitivo cuando las relaciones entre las vistas posicionan la guía.

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
