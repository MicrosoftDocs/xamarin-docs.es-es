---
title: Plantillas de control de Xamarin.Forms
description: Las plantillas de control de Xamarin.Forms definen la estructura visual de los controles personalizados derivados de ContentView y las páginas derivadas de ContentPage.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ad65c46f216af9a24eb02ab55411f0a89ac61100
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374620"
---
# <a name="xamarinforms-control-templates"></a>Plantillas de control de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Las plantillas de control de Xamarin.Forms permiten definir la estructura visual de los controles personalizados derivados de [`ContentView`](xref:Xamarin.Forms.ContentView) y las páginas derivadas de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Las plantillas de control separan la interfaz de usuario (IU) de un control personalizado, o de una página, de la lógica que implementa el control o la página. También se puede insertar contenido adicional en el control personalizado (o en la página) con plantilla en una ubicación predefinida.

Por ejemplo, se puede crear una plantilla de control que redefina la interfaz de usuario proporcionada por un control personalizado. La instancia de control personalizada requerida puede consumir entonces la plantilla de control. Como alternativa, se puede crear una plantilla de control que defina cualquier interfaz de usuario común que se usará en varias páginas de una aplicación. La plantilla de control se puede usar en varias páginas, cada una de las cuales sigue mostrando su contenido único.

## <a name="create-a-controltemplate"></a>Creación de una clase ControlTemplate

En el ejemplo siguiente se muestra el código de un control personalizado `CardView`:

```csharp
public class CardView : ContentView
{
    public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(nameof(CardTitle), typeof(string), typeof(CardView), string.Empty);
    public static readonly BindableProperty CardDescriptionProperty = BindableProperty.Create(nameof(CardDescription), typeof(string), typeof(CardView), string.Empty);
    // ...

    public string CardTitle
    {
        get => (string)GetValue(CardTitleProperty);
        set => SetValue(CardTitleProperty, value);
    }

    public string CardDescription
    {
        get => (string)GetValue(CardDescriptionProperty);
        set => SetValue(CardDescriptionProperty, value);
    }
    // ...
}
```

La clase `CardView`, que se deriva de [`ContentView`](xref:Xamarin.Forms.ContentView), representa un control personalizado que muestra los datos en un diseño de tipo tarjeta. La clase contiene propiedades, que están respaldadas por propiedades enlazables, de los datos que muestra. Sin embargo, la clase `CardView` no define ninguna interfaz de usuario. En su lugar, la interfaz de usuario se definirá con una plantilla de control. Para más información sobre cómo crear controles personalizados derivados de `ContentView`, vea [ContentView de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/contentview.md).

Se crea una plantilla de control con el tipo [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Cuando se crea una clase `ControlTemplate`, se combinan los objetos [`View`](xref:Xamarin.Forms.View) para compilar la interfaz de usuario de un control o página personalizados. Una clase `ControlTemplate` solo debe tener un objeto `View` como elemento raíz. Sin embargo, el elemento raíz normalmente contiene otros objetos `View`. La combinación de objetos constituye la estructura visual del control.

Aunque se puede definir una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en línea, el enfoque habitual para declararla [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) es como un recurso de un diccionario de recursos. Dado que las plantillas de control son recursos, siguen las mismas reglas de ámbito que se aplican a todos los recursos. Por ejemplo, si se declara una plantilla de control en el elemento raíz del archivo XAML de definición de la aplicación, se puede usar en cualquier parte de esta. Si define la plantilla en una página, solo esa página puede usar la plantilla de control. Para más información sobre los recursos, vea [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

En el siguiente ejemplo de XAML se muestra una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para objetos `CardView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
      <ControlTemplate x:Key="CardViewControlTemplate">
          <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                 BackgroundColor="{Binding CardColor}"
                 BorderColor="{Binding BorderColor}"
                 CornerRadius="5"
                 HasShadow="True"
                 Padding="8"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">
              <Grid>
                  <Grid.RowDefinitions>
                      <RowDefinition Height="75" />
                      <RowDefinition Height="4" />
                      <RowDefinition Height="Auto" />
                  </Grid.RowDefinitions>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="75" />
                      <ColumnDefinition Width="200" />
                  </Grid.ColumnDefinitions>
                  <Frame IsClippedToBounds="True"
                         BorderColor="{Binding BorderColor}"
                         BackgroundColor="{Binding IconBackgroundColor}"
                         CornerRadius="38"
                         HeightRequest="60"
                         WidthRequest="60"
                         HorizontalOptions="Center"
                         VerticalOptions="Center">
                      <Image Source="{Binding IconImageSource}"
                             Margin="-20"
                             WidthRequest="100"
                             HeightRequest="100"
                             Aspect="AspectFill" />
                  </Frame>
                  <Label Grid.Column="1"
                         Text="{Binding CardTitle}"
                         FontAttributes="Bold"
                         FontSize="Large"
                         VerticalTextAlignment="Center"
                         HorizontalTextAlignment="Start" />
                  <BoxView Grid.Row="1"
                           Grid.ColumnSpan="2"
                           BackgroundColor="{Binding BorderColor}"
                           HeightRequest="2"
                           HorizontalOptions="Fill" />
                  <Label Grid.Row="2"
                         Grid.ColumnSpan="2"
                         Text="{Binding CardDescription}"
                         VerticalTextAlignment="Start"
                         VerticalOptions="Fill"
                         HorizontalOptions="Fill" />
              </Grid>
          </Frame>
      </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Cuando se declara una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) como un recurso, debe tener una clave especificada con el atributo `x:Key` para que se pueda identificar en el diccionario de recursos. En este ejemplo, el elemento raíz de `CardViewControlTemplate` es un objeto [`Frame`](xref:Xamarin.Forms.Frame). El objeto `Frame` usa la extensión de marcado `RelativeSource` para establecer su elemento `BindingContext` en la instancia del objeto en tiempo de ejecución a la que se aplicará la plantilla, lo que se conoce como *elemento primario con plantilla*. El objeto `Frame` usa una combinación de objetos [`Grid`](xref:Xamarin.Forms.Grid), `Frame`, [`Image`](xref:Xamarin.Forms.Image), [`Label`](xref:Xamarin.Forms.Label), and [`BoxView`](xref:Xamarin.Forms.BoxView) para definir la estructura visual de un objeto `CardView`. Las expresiones de enlace de estos objetos se resuelven a partir de las propiedades `CardView`, debido a la herencia de `BindingContext` del elemento `Frame` raíz. Para más información sobre la extensión de marcado `RelativeSource`, consulte [Enlaces relativos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Consumo de una clase ControlTemplate

Una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se puede aplicar a un control personalizado derivado [`ContentView`](xref:Xamarin.Forms.ContentView) mediante el establecimiento de su propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) en el objeto de la plantilla de control. Igualmente, una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se puede aplicar a una página derivada [`ContentPage`](xref:Xamarin.Forms.ContentPage) mediante el establecimiento de su propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) en el objeto de la plantilla de control. Cuando se aplica una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) en tiempo de ejecución, todos los controles que se definen en ella `ControlTemplate` se agregan al árbol visual del control personalizado, o la página, con plantilla.

En el ejemplo siguiente se muestra que `CardViewControlTemplate` se asigna a la propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) de cada objeto `CardView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, los controles de `CardViewControlTemplate` forman parte del árbol visual de cada objeto `CardView`. Dado que el objeto [`Frame`](xref:Xamarin.Forms.Frame) raíz de la plantilla de control establece su elemento `BindingContext` en el elemento primario con plantilla, `Frame` y sus elementos secundarios resuelven su expresión de enlace a partir de las propiedades de cada objeto `CardView`.

Las capturas de pantallas siguientes muestran que `CardViewControlTemplate` se ha aplicado a tres objetos `CardView`:

[![Capturas de pantallas de objetos CardView con plantilla, en iOS y Android](control-template-images/relativesource-controltemplate.png "Objetos CardView con plantilla")](control-template-images/relativesource-controltemplate-large.png#lightbox "Objetos CardView con plantilla")

> [!IMPORTANT]
> El momento en el que se aplica [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a una instancia de control se puede detectar si se invalida el método `OnApplyTemplate` en el control personalizado, o la página, con plantilla. Para más información, consulte [Obtención de un elemento con nombre de una plantilla](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Paso de parámetros con TemplateBinding

La extensión de marcado `TemplateBinding` enlaza una propiedad de un elemento que se encuentra en una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a una propiedad pública definida por el control personalizado, o la página, con plantilla. Cuando se usa `TemplateBinding`, permite que las propiedades del control actúen como parámetros para la plantilla. Por consiguiente, cuando se establece una propiedad de un control personalizado, o una página, con plantilla, ese valor se pasa al elemento que contiene `TemplateBinding`.

> [!IMPORTANT]
> La expresión de marcado `TemplateBinding` permite quitar el enlace de `RelativeSource` de la plantilla de control anterior y reemplazar las expresiones de `Binding`.

La extensión de marcado `TemplateBinding` define las siguientes propiedades:

- `Path`, de tipo `string`, la ruta de acceso a la propiedad.
- `Mode`, de tipo `BindingMode`, la dirección en la que los cambios se propagan entre *source* y *target*.
- `Converter`, de tipo `IValueConverter`, el convertidor de valores de enlace.
- `ConverterParameter`, de tipo `object`, el parámetro para el convertidor de valores de enlace.
- `StringFormat`, de tipo `string`, el formato de cadena del enlace.

El valor de `ContentProperty` para la extensión de marcado `TemplateBinding` es `Path`. Por lo tanto, la parte "Path=" de la extensión de marcado se puede omitir si la ruta de acceso es el primer elemento de la expresión `TemplateBinding`. Para más información sobre el uso de estas propiedades en una expresión de enlace, consulte [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> La extensión de marcado `TemplateBinding` solo debe usarse dentro de una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Sin embargo, al intentar usar una expresión `TemplateBinding` fuera de una clase `ControlTemplate`, no se producirá un error de compilación o se producirá una excepción.

En el siguiente ejemplo de XAML se muestra una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para objetos `CardView`, que usa la extensión de marcado `TemplateBinding`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BackgroundColor="{TemplateBinding CardColor}"
                   BorderColor="{TemplateBinding BorderColor}"
                   CornerRadius="5"
                   HasShadow="True"
                   Padding="8"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="75" />
                        <RowDefinition Height="4" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="75" />
                        <ColumnDefinition Width="200" />
                    </Grid.ColumnDefinitions>
                    <Frame IsClippedToBounds="True"
                           BorderColor="{TemplateBinding BorderColor}"
                           BackgroundColor="{TemplateBinding IconBackgroundColor}"
                           CornerRadius="38"
                           HeightRequest="60"
                           WidthRequest="60"
                           HorizontalOptions="Center"
                           VerticalOptions="Center">
                        <Image Source="{TemplateBinding IconImageSource}"
                               Margin="-20"
                               WidthRequest="100"
                               HeightRequest="100"
                               Aspect="AspectFill" />
                    </Frame>
                    <Label Grid.Column="1"
                           Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold"
                           FontSize="Large"
                           VerticalTextAlignment="Center"
                           HorizontalTextAlignment="Start" />
                    <BoxView Grid.Row="1"
                             Grid.ColumnSpan="2"
                             BackgroundColor="{TemplateBinding BorderColor}"
                             HeightRequest="2"
                             HorizontalOptions="Fill" />
                    <Label Grid.Row="2"
                           Grid.ColumnSpan="2"
                           Text="{TemplateBinding CardDescription}"
                           VerticalTextAlignment="Start"
                           VerticalOptions="Fill"
                           HorizontalOptions="Fill" />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

En este ejemplo, la extensión de marcado `TemplateBinding` resuelve las expresiones de enlace a partir de las propiedades de cada objeto `CardView`. Las capturas de pantallas siguientes muestran que `CardViewControlTemplate` se ha aplicado a tres objetos `CardView`:

[![Capturas de pantallas de objetos CardView con plantilla](control-template-images/templatebinding-controltemplate.png "Objetos CardView con plantilla")](control-template-images/templatebinding-controltemplate-large.png#lightbox "Objetos CardView con plantilla")

> [!IMPORTANT]
> El uso de la extensión de marcado `TemplateBinding` es equivalente a establecer el elemento `BindingContext` del elemento raíz de la plantilla en su elemento primario con plantilla con la extensión de marcado `RelativeSource` y, luego, resolver los enlaces de objetos secundarios con la extensión de marcado `Binding`. De hecho, la extensión de marcado `TemplateBinding` crea un elemento `Binding` cuyo valor de `Source` es `RelativeBindingSource.TemplatedParent`.

## <a name="apply-a-controltemplate-with-a-style"></a>Aplicación de una clase ControlTemplate con un estilo

También se pueden aplicar plantillas de control con estilos. Para ello se crea un estilo *implicit* o *explicit* que consume [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

En el siguiente ejemplo de XAML se muestra un estilo *implicit* que consume el objeto `CardViewControlTemplate`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            ...
        </ControlTemplate>

        <Style TargetType="controls:CardView">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource CardViewControlTemplate}" />
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"/>
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el estilo *implicit* [`Style`](xref:Xamarin.Forms.Style) se aplica automáticamente a cada objeto `CardView` y se establece la propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) de cada `CardView` en `CardViewControlTemplate`.

Para obtener más información sobre los estilos, vea [Estilos de Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Redefinición de la interfaz de usuario de un control

Cuando se crea una instancia de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) y se asigna a la propiedad `ControlTemplate` de un control personalizado derivado [`ContentView`](xref:Xamarin.Forms.ContentView) o a una página derivada [`ContentPage`](xref:Xamarin.Forms.ContentPage), la estructura visual definida para el control personalizado, o la página, se reemplaza por la estructura visual definida en la clase `ControlTemplate`.

Por ejemplo, el control personalizado `CardViewUI` define su interfaz de usuario mediante el código XAML siguiente:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ControlTemplateDemos.Controls.CardViewUI"
             x:Name="this">
    <Frame BindingContext="{x:Reference this}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="75" />
                <ColumnDefinition Width="200" />
            </Grid.ColumnDefinitions>
            <Frame IsClippedToBounds="True"
                   BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Gray'}"
                   CornerRadius="38"
                   HeightRequest="60"
                   WidthRequest="60"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image Source="{Binding IconImageSource}"
                       Margin="-20"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill" />
            </Frame>
            <Label Grid.Column="1"
                   Text="{Binding CardTitle, FallbackValue='Card title'}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     Grid.ColumnSpan="2"
                     BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Grid.ColumnSpan="2"
                   Text="{Binding CardDescription, FallbackValue='Card description'}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
        </Grid>
    </Frame>
</ContentView>
```

Sin embargo, los controles que componen esta interfaz de usuario se pueden reemplazar definiendo una nueva estructura visual en una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) y asignándola a la propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) de un objeto `CardViewUI`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="{TemplateBinding IconImageSource}"
                        BackgroundColor="{TemplateBinding IconBackgroundColor}"
                        WidthRequest="100"
                        HeightRequest="100"
                        Aspect="AspectFill"
                        HorizontalOptions="Center"
                        VerticalOptions="Center" />
                <StackLayout Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="John Doe"
                             CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Jane Doe"
                             CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Xamarin Monkey"
                             CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, la estructura visual del objeto `CardViewUI` se redefine en una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) que proporciona una estructura visual más compacta adecuada para una lista condensada:

[![Capturas de pantallas de objetos CardViewUI con plantilla, en iOS y Android](control-template-images/redefine-controltemplate.png "Objetos CardViewUI con plantilla")](control-template-images/redefine-controltemplate-large.png#lightbox "Objetos CardViewUI con plantilla")

## <a name="substitute-content-into-a-contentpresenter"></a>Sustitución del contenido por un objeto ContentPresenter

Se puede colocar un objeto [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) en una plantilla de control para marcar dónde aparecerá el contenido que se va a mostrar en el control personalizado, o la página, con plantilla. El control personalizado, o la página, que consume la plantilla de control definirá entonces el contenido que se mostrará mediante `ContentPresenter`. En el diagrama siguiente se ilustra un elemento `ControlTemplate` para una página que contiene una serie controles, incluido un control `ContentPresenter` marcado por un rectángulo de color azul:

![Plantilla de control de ContentPage](control-template-images/control-template.png "Plantilla de control de ContentPage")

En el código XAML siguiente se muestra una plantilla de control denominada `TealTemplate` que contiene un elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) en su estructura visual:

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="0.1*" />
            <RowDefinition Height="0.8*" />
            <RowDefinition Height="0.1*" />
        </Grid.RowDefinitions>
        <BoxView Color="Teal" />
        <Label Margin="20,0,0,0"
               Text="{TemplateBinding HeaderText}"
               TextColor="White"
               FontSize="Title"
               VerticalOptions="Center" />
        <ContentPresenter Grid.Row="1" />
        <BoxView Grid.Row="2"
                 Color="Teal" />
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        <controls:HyperlinkLabel Grid.Row="2"
                                 Margin="0,0,20,0"
                                 Text="Help"
                                 TextColor="White"
                                 Url="https://docs.microsoft.com/xamarin/xamarin-forms/"
                                 HorizontalOptions="End"
                                 VerticalOptions="Center" />
    </Grid>
</ControlTemplate>
```

En el ejemplo siguiente se muestra que `TealTemplate` se ha asignado a la propiedad [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) de una página derivada [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<controls:HeaderFooterPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"                           
                           ControlTemplate="{StaticResource TealTemplate}"
                           HeaderText="MyApp"
                           ...>
    <StackLayout Margin="10">
        <Entry Placeholder="Enter username" />
        <Entry Placeholder="Enter password"
               IsPassword="True" />
        <Button Text="Login" />
    </StackLayout>
</controls:HeaderFooterPage>
```

Cuando se aplica `TealTemplate` a la página en tiempo de ejecución, el contenido de la página se sustituye por el elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) definido en la plantilla de control:

[![Capturas de pantallas del objeto de página con plantilla, en iOS y Android](control-template-images/tealtemplate-contentpage.png "ContentPage con plantilla")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage con plantilla")

## <a name="get-a-named-element-from-a-template"></a>Obtención de un elemento con nombre desde una plantilla

Los elementos con nombre dentro de una plantilla de control se pueden recuperar del control personalizado, o la página, con plantilla. Para ello se puede usar el método `GetTemplateChild`, que devuelve el elemento con nombre en el árbol visual [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) con instancias, si se encuentra. De lo contrario, devuelve `null`.

Una vez que se crearon instancias de una plantilla de control, se llama al método `OnApplyTemplate` de la plantilla. Por lo tanto, se debe llamar al método `GetTemplateChild` desde una invalidación de `OnApplyTemplate` en el control, o la página, con plantilla.

> [!IMPORTANT]
> Se debe llamar al método `GetTemplateChild` solo una vez que se ha llamado al método `OnApplyTemplate`.

En el siguiente código XAML se muestra una plantilla de control denominada `TealTemplate` que se puede aplicar a páginas derivadas [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        ...
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        ...
    </Grid>
</ControlTemplate>
```

En este ejemplo, se designa el elemento [`Label`](xref:Xamarin.Forms.Label), y se puede recuperar en el código de la página con plantilla. Para ello, se llama al método `GetTemplateChild` desde la invalidación `OnApplyTemplate` de la página con plantilla:

```csharp
public partial class AccessTemplateElementPage : HeaderFooterPage
{
    Label themeLabel;

    public AccessTemplateElementPage()
    {
        InitializeComponent();
    }

    protected override void OnApplyTemplate()
    {
        base.OnApplyTemplate();
        themeLabel = (Label)GetTemplateChild("changeThemeLabel");
        themeLabel.Text = OriginalTemplate ? "Aqua Theme" : "Teal Theme";
    }
}
```

En este ejemplo, se recupera el objeto [`Label`](xref:Xamarin.Forms.Label) llamado `changeThemeLabel` una vez que se ha creado una instancia de `ControlTemplate`. Luego, la clase `AccessTemplateElementPage` puede acceder a `changeThemeLabel` y manipularlo. Las capturas de pantalla siguientes muestran que se ha cambiado el texto mostrado por el `Label`:

[![Capturas de pantallas de objeto de página con plantilla](control-template-images/get-named-element.png "ContentPage con plantilla")](control-template-images/get-named-element-large.png#lightbox "ContentPage con plantilla")

## <a name="bind-to-a-viewmodel"></a>Enlace a ViewModel

Una clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) puede enlazar datos a un objeto ViewModel, incluso cuando `ControlTemplate` enlaza al elemento primario con plantilla (la instancia del objeto en tiempo de ejecución a la que se aplica la plantilla).

En el siguiente ejemplo de XAML se muestra una página que consume un objeto ViewModel llamado `PeopleViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ControlTemplateDemos"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.BindingContext>
        <local:PeopleViewModel />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <DataTemplate x:Key="PersonTemplate">
            <controls:CardView BorderColor="DarkGray"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Description}"
                               ControlTemplate="{StaticResource CardViewControlTemplate}" />
        </DataTemplate>
    </ContentPage.Resources>

    <StackLayout Margin="10"
                 BindableLayout.ItemsSource="{Binding People}"
                 BindableLayout.ItemTemplate="{StaticResource PersonTemplate}" />
</ContentPage>
```

En este ejemplo, el elemento `BindingContext` de la página se establece en una instancia `PeopleViewModel`. Este objeto ViewModel expone una colección `People` y un elemento `ICommand` llamado `DeletePersonCommand`. El elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) de la página un diseño enlazable para enlazar datos a la colección `People` y el elemento `ItemTemplate` del diseño enlazable está establecido en el recurso `PersonTemplate`. Este elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) especifica que cada elemento de la colección `People` se mostrará mediante un objeto `CardView`. La estructura visual del objeto `CardView` se define mediante un elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominado `CardViewControlTemplate`:

```xaml
<ControlTemplate x:Key="CardViewControlTemplate">
    <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Label Text="{Binding CardTitle}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     BackgroundColor="{Binding BorderColor}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Text="{Binding CardDescription}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
            <Button Text="Delete"
                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeletePersonCommand}"
                    CommandParameter="{Binding CardTitle}"
                    HorizontalOptions="End" />
        </Grid>
    </Frame>
</ControlTemplate>
```

En este ejemplo, el elemento raíz de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) es un objeto [`Frame`](xref:Xamarin.Forms.Frame). El objeto `Frame` usa la extensión de marcado `RelativeSource` para establecer su `BindingContext` en el elemento primario con plantilla. Las expresiones de enlace del objeto `Frame` y sus elementos secundarios se resuelven a partir de propiedades `CardView`, por la herencia de `BindingContext` del elemento `Frame` raíz. Las capturas de pantallas siguientes muestran la página con la colección `People`, que consta de tres elementos:

[![Capturas de pantallas de tres objetos CardView con plantilla](control-template-images/viewmodel-controltemplate.png "Objetos CardView con plantilla")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Objetos CardView con plantilla")

Mientras que los objetos [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) enlazan a propiedades de su elemento primario con plantilla, el elemento [`Button`](xref:Xamarin.Forms.Button) de la plantilla de control enlaza a su elemento primario con plantilla y al elemento `DeletePersonCommand` de ViewModel. Esto se debe a que la propiedad `Button.Command` vuelve a definir su origen de enlace como el contexto de enlace del antecesor cuyo tipo de contexto de enlace es `PeopleViewModel`, que es el elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). Luego, la parte `Path` de las expresiones de enlace puede resolver la propiedad `DeletePersonCommand`. Sin embargo, la propiedad `Button.CommandParameter` no modifica su origen de enlace, sino que la hereda de su elemento primario en la clase [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Por lo tanto, la propiedad `CommandParameter` enlaza a la propiedad `CardTitle` de `CardView`.

El efecto general de los enlaces [`Button`](xref:Xamarin.Forms.Button) es que cuando se pulsa el elemento `Button`, se ejecuta el elemento `DeletePersonCommand` de la clase `PeopleViewModel`, y se pasa el valor de la propiedad `CardName` a `DeletePersonCommand`. Como resultado, el objeto `CardView` especificado se quita del diseño enlazable:

[![Capturas de pantallas de dos objetos CardView con plantilla](control-template-images/viewmodel-itemdeleted.png "Objetos CardView con plantilla")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Objetos CardView con plantilla")

Para más información sobre los enlaces relativos, consulte [Enlaces relativos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Vínculos relacionados

- [ControlTemplateDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [ContentView de Xamarin.Forms](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Enlaces relativos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Estilos de Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)