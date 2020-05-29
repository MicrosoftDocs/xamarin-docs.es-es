---
title: Xamarin.Formsdiccionarios de recursos
description: Xamarin.FormsLos recursos XAML son objetos que se pueden compartir y reutilizar en una Xamarin.Forms aplicación.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: ''
ms.openlocfilehash: a1c7cfd4a0f3549b11ac51dc13b40da552f6b758
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139417"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.Formsdiccionarios de recursos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) es un repositorio para los recursos utilizados por una Xamarin.Forms aplicación. Los recursos típicos que se almacenan en un `ResourceDictionary` incluyen [estilos](~/xamarin-forms/user-interface/styles/index.md), [plantillas de control](~/xamarin-forms/app-fundamentals/templates/control-template.md), [plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), colores y convertidores.

En XAML, se puede hacer referencia a los recursos que se almacenan en, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) y se pueden aplicar a los elementos mediante la `StaticResource` extensión de `DynamicResource` marcado o. En C#, los recursos también se pueden definir en `ResourceDictionary` y después se hace referencia a ellos y se aplican a los elementos mediante un indexador basado en cadenas. Sin embargo, hay pocas ventajas en el uso `ResourceDictionary` de en C#, ya que los objetos compartidos se pueden almacenar como campos o propiedades, y se puede obtener acceso a ellos directamente sin tener que recuperarlos primero de un diccionario.

## <a name="create-resources-in-xaml"></a>Crear recursos en XAML

Cada [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto derivado tiene una [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propiedad, que es un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que puede contener recursos. Del mismo modo, un [`Application`](xref:Xamarin.Forms.Application) objeto derivado tiene una [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propiedad, que es un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que puede contener recursos.

Una Xamarin.Forms aplicación solo contiene una clase que se deriva de [`Application`](xref:Xamarin.Forms.Application) , pero a menudo usa muchas clases que derivan de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , incluidas páginas, diseños y controles. Cualquiera de estos objetos puede tener su `Resources` propiedad establecida en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que contenga recursos. Elección del lugar en el que se colocarán `ResourceDictionary` los recursos que se pueden usar:

- Los recursos de un `ResourceDictionary` que está asociado a una vista como [`Button`](xref:Xamarin.Forms.Button) o [`Label`](xref:Xamarin.Forms.Label) solo se pueden aplicar a ese objeto concreto.
- Los recursos de un `ResourceDictionary` asociado a un diseño como [`StackLayout`](xref:Xamarin.Forms.StackLayout) o [`Grid`](xref:Xamarin.Forms.Grid) se pueden aplicar al diseño y a todos los elementos secundarios de ese diseño.
- Los recursos de un `ResourceDictionary` definido en el nivel de página se pueden aplicar a la página y a todos sus elementos secundarios.
- Los recursos de un `ResourceDictionary` definido en el nivel de aplicación se pueden aplicar en toda la aplicación.

A excepción de los estilos implícitos, cada recurso del Diccionario de recursos debe tener una clave de cadena única que se defina con el `x:Key` atributo.

En el siguiente código XAML se muestran los recursos definidos en un nivel de aplicación `ResourceDictionary` en el archivo **app. Xaml** :

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

En este ejemplo, el Diccionario de recursos define un [`Thickness`](xref:Xamarin.Forms.Thickness) recurso, varios [`Color`](xref:Xamarin.Forms.Color) recursos y dos recursos implícitos [`Style`](xref:Xamarin.Forms.Style) . Para obtener más información sobre la `App` clase, vea [ Xamarin.Forms App (clase](~/xamarin-forms/app-fundamentals/application-class.md)).

> [!NOTE]
> También es válido para colocar todos los recursos entre etiquetas explícitas `ResourceDictionary` . Sin embargo, como Xamarin.Forms 3,0, las `ResourceDictionary` etiquetas no son necesarias. En su lugar, el `ResourceDictionary` objeto se crea automáticamente y se pueden insertar directamente los recursos entre las `Resources` etiquetas del elemento de propiedad.

## <a name="consume-resources-in-xaml"></a>Consumir recursos en XAML

Cada recurso tiene una clave que se especifica mediante el `x:Key` atributo, que se convierte en su clave de diccionario en `ResourceDictionary` . La clave se usa para hacer referencia a un recurso desde [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) la [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extensión de marcado o.

La `StaticResource` extensión de marcado es similar a la `DynamicResource` extensión de marcado en que ambos usan una clave de diccionario para hacer referencia a un valor de un diccionario de recursos. Sin embargo, mientras que la `StaticResource` extensión de marcado realiza una búsqueda de un solo diccionario, la `DynamicResource` extensión de marcado mantiene un vínculo a la clave del diccionario. Por lo tanto, si se reemplaza la entrada del diccionario asociada a la clave, el cambio se aplica al elemento visual. Esto permite realizar cambios en el recurso en tiempo de ejecución en una aplicación. Para obtener más información sobre las extensiones de marcado, vea [extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

En el siguiente ejemplo de XAML se muestra cómo consumir recursos y también se definen recursos adicionales en un [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

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

En este ejemplo, el [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto consume el estilo implícito definido en el Diccionario de recursos de nivel de aplicación. El [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto consume el `PageMargin` recurso definido en el Diccionario de recursos de nivel de aplicación, mientras que el [`Button`](xref:Xamarin.Forms.Button) objeto consume el estilo implícito definido en el [`StackLayout`](xref:Xamarin.Forms.StackLayout) Diccionario de recursos. El resultado es el aspecto que se muestra en las capturas de pantalla siguientes:

[![Consumo de recursos de ResourceDictionary](resource-dictionaries-images/consuming.png "Consumo de recursos de ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Consumo de recursos de ResourceDictionary")

> [!IMPORTANT]
> Los recursos que son específicos de una sola página no deben incluirse en un diccionario de recursos de nivel de aplicación, ya que dichos recursos se analizarán en el inicio de la aplicación en lugar de cuando los solicite una página. Para obtener más información, consulte [reducir el tamaño del Diccionario de recursos de aplicación](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportamiento de búsqueda de recursos

El siguiente proceso de búsqueda se produce cuando se hace referencia a un recurso con la [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extensión de marcado o:

- La clave solicitada se comprueba en el Diccionario de recursos, si existe, para el elemento que establece la propiedad. Si se encuentra la clave solicitada, se devuelve su valor y finaliza el proceso de búsqueda.
- Si no se encuentra ninguna coincidencia, el proceso de búsqueda busca en el árbol visual hacia arriba y comprueba el Diccionario de recursos de cada elemento primario. Si se encuentra la clave solicitada, se devuelve su valor y finaliza el proceso de búsqueda. De lo contrario, el proceso continúa hacia arriba hasta que se alcanza el elemento raíz.
- Si no se encuentra ninguna coincidencia en el elemento raíz, se examina el Diccionario de recursos de nivel de aplicación.
- Si todavía no se encuentra ninguna coincidencia, `XamlParseException` se produce una excepción.

Por lo tanto, cuando el analizador de XAML encuentra `StaticResource` una `DynamicResource` extensión de marcado o, busca una clave coincidente yendo hacia arriba por el árbol visual, usando la primera coincidencia que encuentra. Si esta búsqueda finaliza en la página y todavía no se ha encontrado la clave, el analizador de XAML busca en el `ResourceDictionary` `App` objeto adjunto. Si todavía no se encuentra la clave, se produce una excepción.

## <a name="override-resources"></a>Invalidar recursos

Cuando los recursos comparten claves, los recursos definidos en un nivel inferior en el árbol visual tendrán prioridad sobre las definidas más arriba. Por ejemplo, el establecimiento de un `AppBackgroundColor` recurso en `AliceBlue` en el nivel de aplicación se reemplazará por un recurso de nivel de página `AppBackgroundColor` establecido en `Teal` . Del mismo modo, un recurso de nivel de página reemplazará a un recurso de nivel de página `AppBackgroundColor` `AppBackgroundColor` .

## <a name="stand-alone-resource-dictionaries"></a>Diccionarios de recursos independientes

Una clase derivada de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) también puede estar en un archivo independiente independiente. El archivo resultante se puede compartir entre aplicaciones.

Para crear este tipo de archivo, agregue una nueva **vista de contenido** o elemento de página de **contenido** al proyecto (pero no una vista de **contenido** o una página de **contenido** con solo un archivo de C#). Elimine el archivo de código subyacente y, en el archivo XAML, cambie el nombre de la clase base de [`ContentView`](xref:Xamarin.Forms.ContentView) o [`ContentPage`](xref:Xamarin.Forms.ContentPage) a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Además, quite el `x:Class` atributo de la etiqueta raíz del archivo.

En el ejemplo de XAML siguiente se muestra un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) denominado **MyResourceDictionary. Xaml**:

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

En este ejemplo, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contiene un único recurso, que es un objeto de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . **MyResourceDictionary. Xaml** se puede usar combinándolos en otro diccionario de recursos.

## <a name="merged-resource-dictionaries"></a>Diccionarios de recursos combinados

Los diccionarios de recursos combinados combinan uno o más [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) objetos en otro `ResourceDictionary` .

### <a name="merge-local-resource-dictionaries"></a>Combinar diccionarios de recursos locales

Un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) archivo local se puede combinar en otro `ResourceDictionary` creando un `ResourceDictionary` objeto cuya [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad se establece en el nombre del archivo XAML con los recursos:

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

Esta sintaxis no crea instancias de la `MyResourceDictionary` clase. En su lugar, hace referencia al archivo XAML. Por ese motivo, al establecer la [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad, no es necesario un archivo de código subyacente y el `x:Class` atributo se puede quitar de la etiqueta raíz del archivo **MyResourceDictionary. Xaml** .

> [!IMPORTANT]
> La [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propiedad solo se puede establecer desde XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Combinar diccionarios de recursos de otros ensamblados

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)También se puede combinar en otro si se `ResourceDictionary` agrega a la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) propiedad de `ResourceDictionary` . Esta técnica permite mezclar diccionarios de recursos, independientemente del ensamblado en el que residen. La combinación de diccionarios de recursos de ensamblados externos requiere [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que una acción de compilación se establezca en **EmbeddedResource**, que tenga un archivo de código subyacente y que defina el `x:Class` atributo en la etiqueta raíz del archivo.

> [!WARNING]
> La [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) clase también define una [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propiedad. Sin embargo, esta propiedad ha quedado en desuso y ya no debe usarse.

En el ejemplo de código siguiente se muestran dos diccionarios de recursos que se agregan a la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) colección de un nivel de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

En este ejemplo, se combinan un diccionario de recursos del mismo ensamblado y un diccionario de recursos de un ensamblado externo en el Diccionario de recursos de nivel de página. Además, también puede agregar otros `ResourceDictionary` objetos dentro [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) de las etiquetas del elemento de propiedad y otros recursos fuera de esas etiquetas.

> [!IMPORTANT]
> Solo puede haber una `MergedDictionaries` etiqueta de elemento de propiedad en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , pero puede colocar tantos objetos como `ResourceDictionary` sea necesario.

Cuando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) los recursos combinados comparten valores de atributo idénticos `x:Key` , Xamarin.Forms usa la siguiente prioridad de recursos:

1. Recursos locales del Diccionario de recursos.
1. Los recursos incluidos en los diccionarios de recursos que se combinaron a través de la `MergedDictionaries` colección, en el orden inverso, se enumeran en la `MergedDictionaries` propiedad.

> [!NOTE]
> La búsqueda de diccionarios de recursos puede ser una tarea que requiere un uso intensivo de la informática si una aplicación contiene varios diccionarios de recursos de gran tamaño. Por lo tanto, para evitar búsquedas innecesarias, debe asegurarse de que todas las páginas de una aplicación solo usan diccionarios de recursos adecuados para la página.

## <a name="related-links"></a>Vínculos relacionados

- [Diccionarios de recursos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsStil](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
