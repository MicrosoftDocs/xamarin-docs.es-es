---
title: Diccionarios de recursos
description: Recursos XAML son objetos que pueden compartir y volver a utilizarse en toda una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: a9b9b2d12193161e0cb4514600381c3a7a38495a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529320"
---
# <a name="resource-dictionaries"></a>Diccionarios de recursos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Los recursos XAML son definiciones de objetos que se pueden compartir y volver a usar en una aplicación de Xamarin. Forms. Estos objetos de recursos se almacenan en un diccionario de recursos._

Un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) es un repositorio para los recursos utilizados por una aplicación de Xamarin.Forms. Recursos típicos que se almacenan en un `ResourceDictionary` incluyen [estilos](~/xamarin-forms/user-interface/styles/index.md), [plantillas de control](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colores y los convertidores de tipos.

En XAML, los recursos que se almacenan en un `ResourceDictionary` puedan recuperarse y aplica a los elementos mediante el `StaticResource` extensión de marcado. En C#, también se pueden definir los recursos en un `ResourceDictionary` y, a continuación, recuperar y aplicar a los elementos mediante el uso de un indexador de cadena. Sin embargo, existen muy pocas ventajas al uso de un `ResourceDictionary` en C#, como objetos compartidos simplemente se pueden almacenar como campos o propiedades y obtener acceso a directamente sin tener que primero recuperarlos de un diccionario.

## <a name="create-and-consume-a-resourcedictionary"></a>Crear y usar un ResourceDictionary

Los recursos se definen en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) es decir, se establece en uno de los siguientes `Resources` propiedades:

- El [ `Resources` ](xref:Xamarin.Forms.Application.Resources) propiedad de cualquier clase que deriva de [`Application`](xref:Xamarin.Forms.Application)
- El [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) propiedad de cualquier clase que deriva de [`VisualElement`](xref:Xamarin.Forms.Application)

Un programa de Xamarin.Forms contiene sólo una clase que derive de `Application` pero a menudo hace uso de muchas clases que derivan de `VisualElement`, incluidas las páginas, diseños y controles. Cualquiera de estos objetos pueden tener su `Resources` propiedad establecida en un `ResourceDictionary`. Elegir dónde colocar un determinado `ResourceDictionary` impactos donde se pueden usar los recursos:

- Los recursos en un `ResourceDictionary` que está asociado a una vista como `Button` o `Label` sólo puede aplicarse a ese objeto concreto, por lo que esto no es muy útil.
- Los recursos en un `ResourceDictionary` adjunta a un diseño, como `StackLayout` o `Grid` se pueden aplicar para el diseño y todos los elementos secundarios de ese diseño.
- Los recursos en un `ResourceDictionary` definido en la página de nivel se puede aplicar a la página y a todos sus elementos secundarios.
- Los recursos en un `ResourceDictionary` definido en la aplicación de nivel se puede aplicar a lo largo de la aplicación.

El siguiente XAML muestra los recursos definidos en un nivel de aplicación `ResourceDictionary` en el **App.xaml** archivo creado como parte del programa de Xamarin.Forms estándar:

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Esto `ResourceDictionary` define tres [ `Color` ](xref:Xamarin.Forms.Color) recursos y un [ `Style` ](xref:Xamarin.Forms.Style) recursos. Para obtener más información sobre la `App` de clases, vea [clase App](~/xamarin-forms/app-fundamentals/application-class.md).

A partir de 3.0 de Xamarin.Forms, la configuración explícita `ResourceDictionary` las etiquetas no son necesarias. El `ResourceDictionary` objeto se crea automáticamente y los recursos puede insertar directamente entre el `Resources` etiquetas de elemento de propiedad:

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

Cada recurso tiene una clave que se especifica utilizando el `x:Key` atributo, que se convierte en la clave de diccionario en el `ResourceDictionary`. La clave se usa para recuperar un recurso desde el `ResourceDictionary` por la [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extensión de marcado, como se muestra en el siguiente ejemplo de código XAML que se muestra recursos adicionales definidos en el `StackLayout`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

La primera [ `Label` ](xref:Xamarin.Forms.Label) instancia recupera y consume el `LabelPageHeadingStyle` recurso definido en el nivel de aplicación `ResourceDictionary`, con la segunda `Label` instancia recuperar y consumir el `LabelNormalStyle`recurso definido en el nivel de control `ResourceDictionary`. De forma similar, el [ `Button` ](xref:Xamarin.Forms.Button) instancia recupera y consume el `NormalTextColor` recurso definido en el nivel de aplicación `ResourceDictionary`y el `MediumBoldText` recurso definido en el nivel de control `ResourceDictionary`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](resource-dictionaries-images/screenshots-sml.png "Consumo de recursos de ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "consumiendo recursos ResourceDictionary")

> [!NOTE]
> Recursos que son específicos a una sola página no deberían incluirse en un aplicación nivel diccionario de recursos, por lo tanto los recursos, a continuación, se analizarán al iniciarse la aplicación en lugar de cuando se solicite una página. Para obtener más información, consulte [reducir el tamaño del diccionario de recursos de aplicación](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Invalidar recursos

Cuando `ResourceDictionary` compartan recursos `x:Key` los valores de atributo definidos más abajo en la jerarquía de vistas de recursos tienen prioridad sobre aquellas definidas mayor seguridad. Por ejemplo, si se establece la `PageBackgroundColor` recurso `Blue` en la aplicación de nivel serán reemplazado por un nivel de página `PageBackgroundColor` del conjunto de recursos `Yellow`. De forma similar, un nivel de página `PageBackgroundColor` recursos serán reemplazados por un nivel de control `PageBackgroundColor` recursos. Esta prioridad se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

La versión original `PageBackgroundColor` y `NormalTextColor` instancias, definidas en el nivel de aplicación, se reemplazan por la `PageBackgroundColor` y `NormalTextColor` instancias definidas en el nivel de página. Por lo tanto, el color de fondo de página se convierte en azul y el texto en la página, se convierte en amarillo, como se muestra en las capturas de pantalla siguiente:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Reemplazar los recursos de ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "reemplazar los recursos de ResourceDictionary")

Sin embargo, tenga en cuenta que la barra de fondo de la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) está de color amarillo todavía, porque el [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propiedad está establecida en el valor de la `PageBackgroundColor` recursos definidos en la aplicación nivel `ResourceDictionary`.

Esta es otra manera de pensar `ResourceDictionary` en la prioridad: Cuando el analizador de XAML encuentra un `StaticResource`, busca una clave coincidente viajando a través del árbol visual, usando la primera coincidencia que encuentra. Si esta búsqueda finaliza en la página y la clave aún no se ha encontrado, el analizador XAML busca el `ResourceDictionary` conectados a la `App` objeto. Si todavía no se encuentra la clave, se produce una excepción.

## <a name="stand-alone-resource-dictionaries"></a>Diccionarios de recursos independientes

Una clase derivada de `ResourceDictionary` también pueden estar en un archivo independiente independiente. (Más concretamente, una clase derivada de `ResourceDictionary` requiere normalmente un _par_ de archivos debido a los recursos se definen en un archivo XAML, pero un archivo de código subyacente con un `InitializeComponent` también es necesario llamar a.) El archivo resultante, a continuación, se puede compartir entre aplicaciones.

Para crear este archivo, agregue un nuevo **vista contenido** o **página de contenido** al proyecto (pero no un **vista contenido** o **página de contenido** con solo un archivo de C#). En el archivo XAML y archivo de C#, cambie el nombre de la clase base desde `ContentView` o `ContentPage` a `ResourceDictionary`. En el archivo XAML, el nombre de la clase base es el elemento de nivel superior.

El siguiente ejemplo XAML se muestra un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) denominado `MyResourceDictionary`:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Esto `ResourceDictionary` contiene un único recurso, que es un objeto de tipo `DataTemplate`.

Puede crear una instancia `MyResourceDictionary` colocándola entre un par de `Resources` elementos de propiedad etiquetas, por ejemplo, en un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Una instancia de `MyResourceDictionary` está establecido en el `Resources` propiedad de la `ContentPage` objeto.

Sin embargo, este enfoque tiene algunas limitaciones: La `Resources` propiedad `ResourceDictionary`de solo hace referencia a este. `ContentPage` En la mayoría de los casos, desea que la opción de incluir otros `ResourceDictionary` instancias y quizás también otros recursos.

Esta tarea requiere diccionarios de recursos combinados.

## <a name="merged-resource-dictionaries"></a>Diccionarios de recursos combinados

Los diccionarios de recursos combinados combinan uno o [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) más objetos en `ResourceDictionary`otro.

### <a name="merge-local-resource-dictionaries"></a>Combinar diccionarios de recursos locales

Un local [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) se puede combinar en otro `ResourceDictionary` estableciendo la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad en el nombre del archivo XAML con los recursos:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

Esta sintaxis no crea instancias de la `MyResourceDictionary` clase. En su lugar, hace referencia al archivo XAML. Por ese motivo, al establecer la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad, el archivo de código subyacente (**MyResourceDictionary.Xaml.CS**) no es necesario y el `x:Class` atributo se puede quitar de la etiqueta raíz del archivo **MyResourceDictionary. Xaml** . Además, al combinar los diccionarios de recursos con este enfoque, Xamarin. Forms creará automáticamente [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)una instancia de, `ResourceDictionary` de ahí que no se requieran las etiquetas externas.

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad solo se puede establecer desde XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Combinar diccionarios de recursos de otros ensamblados

También se `ResourceDictionary` [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) puede combinar en otro si se agrega `ResourceDictionary`a la propiedad de. [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) Esta técnica permite mezclar diccionarios de recursos, independientemente del ensamblado en el que residen.

> [!IMPORTANT]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) clase también define una [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propiedad. Sin embargo, esta propiedad ha quedado en desuso y ya no debe usarse.

En el ejemplo de código `MyResourceDictionary` siguiente se muestra que [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) se va a agregar a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)la colección de un nivel de página:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

En este ejemplo se muestra una `MyResourceDictionary`instancia de, que reside en el mismo ensamblado, que se agrega [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a. Además, también puede agregar diccionarios de recursos de otros ensamblados, otros `ResourceDictionary` objetos dentro de [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) las etiquetas del elemento de propiedad y otros recursos fuera de esas etiquetas:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

> [!IMPORTANT]
> Solo puede haber una `MergedDictionaries` etiqueta de elemento de propiedad en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `ResourceDictionary` objeto, pero puede colocar tantos objetos como desee.

Cuando combina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) recursos comparten idénticos `x:Key` valores de atributo, Xamarin.Forms utiliza la prioridad de recursos siguientes:

1. Los recursos locales para el diccionario de recursos.
1. Los recursos contenidos en los diccionarios de recursos que se han combinado a través de la `MergedDictionaries` colección, en el orden inverso que aparecen en la `MergedDictionaries` propiedad.

> [!NOTE]
> La búsqueda de diccionarios de recursos puede ser una tarea de cálculo intensiva si una aplicación contiene varios diccionarios de recursos grande. Por lo tanto, para evitar búsquedas innecesarias, debe asegurarse de que cada página en una aplicación solo usa los diccionarios de recursos que son adecuados para la página.

## <a name="related-links"></a>Vínculos relacionados

- [Diccionarios de recursos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
