---
title: Diccionarios de recursos
description: Recursos XAML son objetos que pueden compartir y volver a utilizarse en toda una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2020
ms.custom: video
ms.openlocfilehash: 496251ec9596b9ef76fb34149acca184b5934c37
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070394"
---
# <a name="resource-dictionaries"></a>Diccionarios de recursos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Los recursos XAML son definiciones de objetos que se pueden compartir y volver a usar en una aplicación de Xamarin. Forms. Estos objetos de recursos se almacenan en un diccionario de recursos._

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) es un repositorio para los recursos utilizados por una aplicación de Xamarin. Forms. Los recursos típicos que se almacenan en un `ResourceDictionary` incluyen [estilos](~/xamarin-forms/user-interface/styles/index.md), [plantillas de control](~/xamarin-forms/app-fundamentals/templates/control-template.md), [plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colores y convertidores.

En XAML, los recursos que se almacenan en un `ResourceDictionary` pueden recuperarse y aplicarse a los elementos mediante la extensión de marcado `StaticResource`. En C#, los recursos también se pueden definir en un `ResourceDictionary` y, a continuación, recuperarse y aplicarse a los elementos mediante un indexador basado en cadenas. Sin embargo, hay pocas ventajas en el uso de un C#`ResourceDictionary` en, ya que los objetos compartidos simplemente se pueden almacenar como campos o propiedades, y se puede obtener acceso a ellos directamente sin tener que recuperarlos primero de un diccionario.

## <a name="create-and-consume-a-resourcedictionary"></a>Crear y usar un ResourceDictionary

Los recursos se definen en una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que se establece en una de las siguientes propiedades `Resources`:

- La propiedad [`Resources`](xref:Xamarin.Forms.Application.Resources) de cualquier clase que derive de [`Application`](xref:Xamarin.Forms.Application)
- La propiedad [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de cualquier clase que derive de [`VisualElement`](xref:Xamarin.Forms.Application)

Un programa de Xamarin. Forms contiene solo una clase que deriva de `Application` pero suele usar muchas clases que derivan de `VisualElement`, incluidas páginas, diseños y controles. Cualquiera de estos objetos puede tener su propiedad `Resources` establecida en un `ResourceDictionary`. La elección de dónde colocar una `ResourceDictionary` determinada afecta a la ubicación de los recursos:

- Los recursos de un `ResourceDictionary` que se adjunta a una vista como `Button` o `Label` solo se pueden aplicar a ese objeto concreto, por lo que esto no es muy útil.
- Los recursos de un `ResourceDictionary` adjuntado a un diseño como `StackLayout` o `Grid` se pueden aplicar al diseño y a todos los elementos secundarios de dicho diseño.
- Los recursos de un `ResourceDictionary` definidos en el nivel de página se pueden aplicar a la página y a todos sus elementos secundarios.
- Los recursos de un `ResourceDictionary` definidos en el nivel de aplicación se pueden aplicar en toda la aplicación.

En el siguiente código XAML se muestran los recursos definidos en un nivel de aplicación `ResourceDictionary` en el archivo **app. Xaml** creado como parte del programa de Xamarin. Forms estándar:

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

Este `ResourceDictionary` define tres recursos [`Color`](xref:Xamarin.Forms.Color) y un recurso [`Style`](xref:Xamarin.Forms.Style) . Para obtener más información sobre la clase `App`, vea [App Class](~/xamarin-forms/app-fundamentals/application-class.md).

A partir de Xamarin. Forms 3,0, no se requieren etiquetas de `ResourceDictionary` explícitas. El objeto de `ResourceDictionary` se crea automáticamente y se pueden insertar directamente los recursos entre las etiquetas del elemento de propiedad `Resources`:

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

Cada recurso tiene una clave que se especifica mediante el `x:Key` atributo, que se convierte en la clave del diccionario en el `ResourceDictionary`. La clave se usa para recuperar un recurso de la `ResourceDictionary` por la extensión de marcado [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) , como se muestra en el siguiente ejemplo de código XAML que muestra los recursos adicionales definidos en el `StackLayout`:

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

La primera instancia de [`Label`](xref:Xamarin.Forms.Label) recupera y consume el `LabelPageHeadingStyle` recurso definido en el `ResourceDictionary`de nivel de aplicación, con la segunda instancia de `Label` recuperando y consumiendo el `LabelNormalStyle` recurso definido en el nivel de control `ResourceDictionary`. Del mismo modo, la instancia de [`Button`](xref:Xamarin.Forms.Button) recupera y consume el recurso de `NormalTextColor` definido en el `ResourceDictionary`de nivel de aplicación y el recurso de `MediumBoldText` definido en el `ResourceDictionary`de nivel de control. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![consumo de recursos de ResourceDictionary](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Recursos que son específicos a una sola página no deberían incluirse en un aplicación nivel diccionario de recursos, por lo tanto los recursos, a continuación, se analizarán al iniciarse la aplicación en lugar de cuando se solicite una página. Para obtener más información, consulte [reducir el tamaño del Diccionario de recursos de aplicación](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Invalidar recursos

Cuando `ResourceDictionary` recursos comparten `x:Key` valores de atributo, los recursos definidos más abajo en la jerarquía de vistas tendrán prioridad sobre los definidos más arriba. Por ejemplo, el establecimiento del recurso `PageBackgroundColor` en `Blue` en el nivel de aplicación se reemplazará por un conjunto de recursos `PageBackgroundColor` en el nivel de página en `Yellow`. De forma similar, un recurso de `PageBackgroundColor` de nivel de página se reemplazará por un recurso `PageBackgroundColor` de nivel de control. Esta prioridad se muestra en el ejemplo de código XAML siguiente:

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

Las instancias `PageBackgroundColor` y `NormalTextColor` originales, definidas en el nivel de aplicación, se reemplazan por las instancias `PageBackgroundColor` y `NormalTextColor` definidas en el nivel de página. Por lo tanto, el color de fondo de página se convierte en azul y el texto en la página, se convierte en amarillo, como se muestra en las capturas de pantalla siguiente:

[![invalidar recursos de ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

Sin embargo, tenga en cuenta que la barra de fondo del [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) sigue siendo amarilla, porque la propiedad [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) se establece en el valor del recurso `PageBackgroundColor` definido en el `ResourceDictionary`de nivel de aplicación.

Esta es otra manera de pensar en `ResourceDictionary` prioridad: cuando el analizador de XAML encuentra un `StaticResource`, busca una clave coincidente viajando hasta el árbol visual, usando la primera coincidencia que encuentre. Si esta búsqueda finaliza en la página y todavía no se ha encontrado la clave, el analizador de XAML busca en el `ResourceDictionary` asociado al objeto `App`. Si todavía no se encuentra la clave, se produce una excepción.

## <a name="stand-alone-resource-dictionaries"></a>Diccionarios de recursos independientes

Una clase derivada de `ResourceDictionary` puede estar también en un archivo independiente independiente. El archivo resultante, a continuación, se puede compartir entre aplicaciones.

Para crear este tipo de archivo, agregue una **nueva vista de contenido** o elemento de página de **contenido** al proyecto (pero no una vista de **contenido** o una página de **contenido** con un C# solo archivo). En el archivo y C# archivo XAML, cambie el nombre de la clase base de `ContentView` o `ContentPage` a `ResourceDictionary`. En el archivo XAML, el nombre de la clase base es el elemento de nivel superior.

En el ejemplo de XAML siguiente se muestra un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) denominado `MyResourceDictionary`:

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

Este `ResourceDictionary` contiene un solo recurso, que es un objeto de tipo `DataTemplate`.

Puede crear instancias de `MyResourceDictionary` si lo coloca entre un par de etiquetas de elemento de propiedad `Resources`, por ejemplo, en un `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Una instancia de `MyResourceDictionary` se establece en la propiedad `Resources` del objeto `ContentPage`.

Sin embargo, este enfoque tiene algunas limitaciones: la propiedad `Resources` del `ContentPage` solo hace referencia a este `ResourceDictionary`. En la mayoría de los casos, desea la opción de incluir otras instancias de `ResourceDictionary` y quizás también otros recursos.

Esta tarea requiere diccionarios de recursos combinados.

## <a name="merged-resource-dictionaries"></a>Diccionarios de recursos combinados

Los diccionarios de recursos combinados combinan uno o varios objetos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) en otro `ResourceDictionary`.

### <a name="merge-local-resource-dictionaries"></a>Combinar diccionarios de recursos locales

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) local se puede combinar en otro `ResourceDictionary` estableciendo la propiedad [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) en el nombre de archivo del archivo XAML con los recursos:

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

Esta sintaxis no crea instancias de la clase `MyResourceDictionary`. En su lugar, hace referencia al archivo XAML. Por ese motivo, al establecer la propiedad [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) , no es necesario un archivo de código subyacente y se puede quitar el atributo `x:Class` de la etiqueta raíz del archivo **MyResourceDictionary. Xaml** . Además, al combinar los diccionarios de recursos con este enfoque, Xamarin. Forms creará automáticamente una instancia del [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), por lo que no se requieren las etiquetas de `ResourceDictionary` externas.

> [!IMPORTANT]
> La propiedad [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) solo se puede establecer desde XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Combinar diccionarios de recursos de otros ensamblados

También se puede combinar un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) en otro `ResourceDictionary` agregándolo a la propiedad [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) del `ResourceDictionary`. Esta técnica permite mezclar diccionarios de recursos, independientemente del ensamblado en el que residen.

> [!IMPORTANT]
> La clase [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) también define una propiedad [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) . Sin embargo, esta propiedad ha quedado en desuso y ya no debe usarse.

En el ejemplo de código siguiente se muestra `MyResourceDictionary` que se agregan a la colección [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) de un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de nivel de página:

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

En este ejemplo se muestra una instancia de `MyResourceDictionary`, que reside en el mismo ensamblado, que se agrega al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Además, también puede agregar diccionarios de recursos de otros ensamblados, otros objetos `ResourceDictionary` dentro de las etiquetas de elemento de propiedad [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) y otros recursos fuera de esas etiquetas:

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

Al colocar una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) en un ensamblado externo, asegúrese de que su acción de compilación está establecida en **EmbeddedResource**. Además, asegúrese de que tiene un archivo de código subyacente.

> [!IMPORTANT]
> Solo puede haber una `MergedDictionaries` etiqueta de elemento de propiedad en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), pero puede colocar tantos objetos de `ResourceDictionary` como desee.

Cuando los recursos de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) combinados comparten los mismos valores de atributo `x:Key`, Xamarin. Forms usa la siguiente prioridad de recursos:

1. Los recursos locales para el diccionario de recursos.
1. Los recursos incluidos en los diccionarios de recursos que se combinaron a través de la colección de `MergedDictionaries`, en el orden inverso, se enumeran en la propiedad `MergedDictionaries`.

> [!NOTE]
> La búsqueda de diccionarios de recursos puede ser una tarea de cálculo intensiva si una aplicación contiene varios diccionarios de recursos grande. Por lo tanto, para evitar búsquedas innecesarias, debe asegurarse de que cada página en una aplicación solo usa los diccionarios de recursos que son adecuados para la página.

## <a name="related-links"></a>Vínculos relacionados

- [Diccionarios de recursos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
