---
title: Diccionarios de recursos de Xamarin.Forms
description: Los recursos XAML de Xamarin.Forms son objetos que se pueden compartir y reutilizar en toda una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.custom: video
ms.openlocfilehash: 8dd3c7f36ddd436a812927816a1326dbb7c48341
ms.sourcegitcommit: ee9e48e2ec643915f42a6c1641077970ae20cb17
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523215"
---
# <a name="xamarinforms-resource-dictionaries"></a>Diccionarios de recursos de Xamarin.Forms

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) es un repositorio de recursos que usa una aplicación de Xamarin.Forms. Los recursos típicos `ResourceDictionary` que se almacenan en un incluir [estilos,](~/xamarin-forms/user-interface/styles/index.md)plantillas de [control,](~/xamarin-forms/app-fundamentals/templates/control-template.md)plantillas de [datos,](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)colores y convertidores.

En XAML, se puede [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) hacer referencia a los recursos `StaticResource` almacenados en un y aplicarlos a los elementos mediante la extensión de marcado o. `DynamicResource` En C- , los recursos `ResourceDictionary` también se pueden definir en un y, a continuación, se hace referencia a ellos y se aplica a los elementos mediante un indizador basado en cadenas. Sin embargo, hay poca `ResourceDictionary` ventaja en el uso de un en C- , ya que los objetos compartidos se pueden almacenar como campos o propiedades, y tener acceso directamente sin tener que recuperarlos primero de un diccionario.

## <a name="create-resources-in-xaml"></a>Crear recursos en XAML

Cada [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto derivado [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) tiene una propiedad, que es una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que puede contener recursos. De forma [`Application`](xref:Xamarin.Forms.Application) similar, un [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) objeto derivado [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) tiene una propiedad, que es una que puede contener recursos.

Una aplicación de Xamarin.Forms contiene [`Application`](xref:Xamarin.Forms.Application)solo la clase que deriva de [`VisualElement`](xref:Xamarin.Forms.VisualElement), pero a menudo hace uso de muchas clases que derivan de , incluidas las páginas, diseños y controles. Cualquiera de estos objetos puede tener su `Resources` propiedad establecida en un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contenedor de recursos. Elegir dónde colocar `ResourceDictionary` un impacto particular donde se pueden utilizar los recursos:

- Los recursos `ResourceDictionary` de un que está [`Button`](xref:Xamarin.Forms.Button) [`Label`](xref:Xamarin.Forms.Label) asociado a una vista como o solo se pueden aplicar a ese objeto en particular.
- Los recursos `ResourceDictionary` de un diseño [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`Grid`](xref:Xamarin.Forms.Grid) adjunto, como o se pueden aplicar, al diseño y a todos los elementos secundarios de ese diseño.
- Los recursos `ResourceDictionary` de un defined en el nivel de página se pueden aplicar a la página y a todos sus elementos secundarios.
- Los recursos `ResourceDictionary` de un defined en el nivel de aplicación se pueden aplicar en toda la aplicación.

Con la excepción de los estilos implícitos, cada recurso del `x:Key` diccionario de recursos debe tener una clave de cadena única definida con el atributo.

El XAML siguiente muestra los `ResourceDictionary` recursos definidos en un nivel de aplicación en el archivo **App.xaml:**

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

        <!-- Implicit styles -->
        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{StaticResource NavigationBarColor}" />
            <Setter Property="BarTextColor"
                    Value="{StaticResource NavigationBarTextColor}" />
        </Style>

        <Style TargetType="{x:Type ContentPage}"
               ApplyToDerivedTypes="True">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>

    </Application.Resources>
</Application>
```

En este ejemplo, el [`Thickness`](xref:Xamarin.Forms.Thickness) diccionario de [`Color`](xref:Xamarin.Forms.Color) recursos define [`Style`](xref:Xamarin.Forms.Style) un recurso, varios recursos y dos recursos implícitos. Para obtener más `App` información acerca de la clase, vea [Xamarin.Forms App (Clase de aplicación)](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> También es válido colocar todos los `ResourceDictionary` recursos entre etiquetas explícitas. Sin embargo, desde Xamarin.Forms 3.0 las `ResourceDictionary` etiquetas no son necesarias. En su `ResourceDictionary` lugar, el objeto se crea automáticamente `Resources` y puede insertar los recursos directamente entre las etiquetas de elemento de propiedad.

## <a name="consume-resources-in-xaml"></a>Consumir recursos en XAML

Cada recurso tiene una clave que `x:Key` se especifica mediante el atributo, que se convierte en su clave de diccionario en el `ResourceDictionary`archivo . La clave se usa para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) hacer [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) referencia [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) a un recurso desde la extensión de marcado o con.

La `StaticResource` extensión de marcado `DynamicResource` es similar a la extensión de marcado en que ambos usan una clave de diccionario para hacer referencia a un valor de un diccionario de recursos. Sin embargo, mientras que la `StaticResource` extensión `DynamicResource` de marcado realiza una sola búsqueda de diccionario, la extensión de marcado mantiene un vínculo a la clave de diccionario. Por lo tanto, si se reemplaza la entrada de diccionario asociada a la clave, el cambio se aplica al elemento visual. Esto permite realizar cambios de recursos en tiempo de ejecución en una aplicación. Para obtener más información acerca de las extensiones de marcado, vea [Extensiones](~/xamarin-forms/xaml/markup-extensions/index.md)de marcado XAML .

En el siguiente ejemplo XAML se muestra cómo consumir [`StackLayout`](xref:Xamarin.Forms.StackLayout)recursos y también se definen recursos adicionales en:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, el [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto consume el estilo implícito definido en el diccionario de recursos de nivel de aplicación. El [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto consume `PageMargin` el recurso definido en el [`Button`](xref:Xamarin.Forms.Button) diccionario de recursos de nivel [`StackLayout`](xref:Xamarin.Forms.StackLayout) de aplicación, mientras que el objeto consume el estilo implícito definido en el diccionario de recursos. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Consumo de recursos ResourceDictionary](resource-dictionaries-images/consuming.png "Consumo de recursos ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Consumo de recursos ResourceDictionary")

> [!IMPORTANT]
> Los recursos que son específicos de una sola página no deben incluirse en un diccionario de recursos de nivel de aplicación, ya que dichos recursos se analizarán al inicio de la aplicación en lugar de cuando lo requiera una página. Para obtener más información, vea [Reducir el tamaño del diccionario](~/xamarin-forms/deploy-test/performance.md)de recursos de aplicación .

## <a name="resource-lookup-behavior"></a>Comportamiento de búsqueda de recursos

El siguiente proceso de búsqueda se [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) produce [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) cuando se hace referencia a un recurso con la extensión o de marcado:

- La clave solicitada se comprueba en el diccionario de recursos, si existe, para el elemento que establece la propiedad. Si se encuentra la clave solicitada, se devuelve su valor y finaliza el proceso de búsqueda.
- Si no se encuentra una coincidencia, el proceso de búsqueda busca el árbol visual hacia arriba, comprobando el diccionario de recursos de cada elemento primario. Si se encuentra la clave solicitada, se devuelve su valor y finaliza el proceso de búsqueda. De lo contrario, el proceso continúa hacia arriba hasta que se alcanza el elemento raíz.
- Si no se encuentra una coincidencia en el elemento raíz, se examina el diccionario de recursos de nivel de aplicación.
- Si aún no se encuentra `XamlParseException` una coincidencia, se lanza un.

Por lo tanto, cuando el `StaticResource` `DynamicResource` analizador XAML encuentra una extensión de marcado o, busca una clave coincidente viajando por el árbol visual, utilizando la primera coincidencia que encuentra. Si esta búsqueda finaliza en la página y la clave aún no `ResourceDictionary` se ha `App` encontrado, el analizador XAML busca el asociado al objeto. Si todavía no se encuentra la clave, se produce una excepción.

## <a name="override-resources"></a>Reemplazar recursos

Cuando los recursos comparten claves, los recursos definidos más abajo en el árbol visual tendrán prioridad sobre los definidos más arriba. Por ejemplo, `AppBackgroundColor` establecer `AliceBlue` un recurso en el nivel de `AppBackgroundColor` aplicación se `Teal`reemplazará por un conjunto de recursos de nivel de página en . De forma similar, un recurso de `AppBackgroundColor` nivel `AppBackgroundColor` de página se reemplazará por un recurso de nivel de control.

## <a name="stand-alone-resource-dictionaries"></a>Diccionarios de recursos independientes

Una clase derivada [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) también puede estar en un archivo independiente independiente. El archivo resultante se puede compartir entre aplicaciones.

Para crear un archivo de este tipo, agregue una nueva **vista** de contenido **o** un elemento de página de contenido al proyecto (pero no una **vista** de contenido o una página de **contenido** con solo un archivo de C.). Elimine el archivo de código subyacente y, en el archivo [`ContentView`](xref:Xamarin.Forms.ContentView) [`ContentPage`](xref:Xamarin.Forms.ContentPage) XAML, cambie el nombre de la clase base desde o hacia [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Además, `x:Class` quite el atributo de la etiqueta raíz del archivo.

En el ejemplo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) XAML siguiente se muestra un denominado **MyResourceDictionary.xaml:**

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

En este ejemplo, contiene [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) un único recurso, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)que es un objeto de tipo . **MyResourceDictionary.xaml** se puede consumir fusionándolo en otro diccionario de recursos.

## <a name="merged-resource-dictionaries"></a>Diccionarios de recursos combinados

Los diccionarios de recursos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) combinados `ResourceDictionary`combinan uno o varios objetos en otro .

### <a name="merge-local-resource-dictionaries"></a>Combinar diccionarios de recursos locales

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) archivo local se `ResourceDictionary` puede combinar `ResourceDictionary` en [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) otro mediante la creación de un objeto cuya propiedad se establece en el nombre de archivo del archivo XAML con los recursos:

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

Esta sintaxis no `MyResourceDictionary` crea una instancia de la clase. En su lugar, hace referencia al archivo XAML. Por este motivo, [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) al establecer la propiedad, no se requiere `x:Class` un archivo de código subyacente y el atributo se puede quitar de la etiqueta raíz del archivo **MyResourceDictionary.xaml.**

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad solo se puede establecer desde XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Combinar diccionarios de recursos de otros ensamblados

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) también se puede `ResourceDictionary` combinar en [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) otro agregándolo a la propiedad de la `ResourceDictionary`. Esta técnica permite combinar diccionarios de recursos, independientemente del ensamblado en el que residan. La combinación de diccionarios de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recursos de ensamblados externos requiere que una acción de compilación se `x:Class` establezca en **EmbeddedResource**, para tener un archivo de código subyacente y para definir el atributo en la etiqueta raíz del archivo.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) clase también [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) define una propiedad. Sin embargo, esta propiedad ha quedado en desuso y ya no se debe usar.

En el ejemplo de código siguiente se [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) muestran dos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)diccionarios de recursos que se agregan a la colección de un nivel de página:

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

En este ejemplo, un diccionario de recursos del mismo ensamblado y un diccionario de recursos de un ensamblado externo se combinan en el diccionario de recursos de nivel de página. Además, también puede `ResourceDictionary` agregar [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) otros objetos dentro de las etiquetas de elemento de propiedad y otros recursos fuera de esas etiquetas.

> [!IMPORTANT]
> Solo puede haber `MergedDictionaries` una etiqueta de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)elemento de propiedad `ResourceDictionary` en un , pero puede colocar tantos objetos allí como sea necesario.

Cuando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) los recursos `x:Key` combinados comparten valores de atributo idénticos, Xamarin.Forms usa la siguiente prioridad de recursos:

1. Los recursos locales para el diccionario de recursos.
1. Los recursos contenidos en los diccionarios `MergedDictionaries` de recursos que se combinaron `MergedDictionaries` a través de la colección, en el orden inverso se enumeran en la propiedad.

> [!NOTE]
> La búsqueda de diccionarios de recursos puede ser una tarea intensiva desde el uso de cálculo si una aplicación contiene varios diccionarios de recursos grandes. Por lo tanto, para evitar búsquedas innecesarias, debe asegurarse de que cada página de una aplicación solo usa diccionarios de recursos que son adecuados para la página.

## <a name="related-links"></a>Vínculos relacionados

- [Diccionarios de recursos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Estilos de Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
