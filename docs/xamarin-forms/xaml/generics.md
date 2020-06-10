---
title: "genéricos en Xamarin.Forms XAML" Description: " Xamarin.Forms XAML proporciona compatibilidad para consumir tipos CLR genéricos especificando las restricciones genéricas como argumentos de tipo".
MS. Prod: Xamarin ms. AssetID: 97B73048-4F90-41AD-AB48-8EB804C4998B ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/28/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="generics-in-xamarinforms-xaml"></a>Genéricos en Xamarin.Forms XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)

Xamarin.FormsXAML proporciona compatibilidad para consumir tipos CLR genéricos especificando las restricciones genéricas como argumentos de tipo. Esta compatibilidad se proporciona mediante la `x:TypeArguments` Directiva, que pasa los argumentos de tipo restrictivos de un genérico al constructor del tipo genérico.

> [!IMPORTANT]
> No se admite la definición de clases genéricas en Xamarin.Forms XAML, con la `x:TypeArguments` Directiva.

Los argumentos de tipo se especifican como una cadena y normalmente tienen el prefijo, como `sys:String` y `sys:Int32` . El prefijo es necesario porque los tipos típicos de restricciones genéricas de CLR proceden de bibliotecas que no están asignadas al Xamarin.Forms espacio de nombres predeterminado. Sin embargo, los tipos integrados de XAML 2009 como `x:String` y `x:Int32` , también se pueden especificar como argumentos de tipo, donde `x` es el espacio de nombres del lenguaje xaml para XAML 2009. Para obtener más información sobre los tipos integrados de XAML 2009, consulte [primitivas del lenguaje xaml 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives).

Se pueden especificar varios argumentos de tipo mediante un delimitador de coma. Además, si una restricción genérica usa tipos genéricos, los argumentos de tipo de restricción anidados deben incluirse entre paréntesis.

> [!NOTE]
> La `x:Type` extensión de marcado proporciona una referencia de tipo de CLR para un tipo genérico y tiene una función similar al `typeof` operador en C#. Para obtener más información, consulte [extensión de marcado x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

## <a name="single-primitive-type-argument"></a>Argumento de tipo primitivo único

Se puede especificar un único argumento de tipo primitivo como argumento de cadena con prefijo mediante la `x:TypeArguments` Directiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="x:String">
                <x:String>Baboon</x:String>
                <x:String>Capuchin Monkey</x:String>
                <x:String>Blue Monkey</x:String>
                <x:String>Squirrel Monkey</x:String>
                <x:String>Golden Lion Tamarin</x:String>
                <x:String>Howler Monkey</x:String>
                <x:String>Japanese Macaque</x:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

En este ejemplo, `System.Collections.Generic` se define como el `scg` espacio de nombres XAML. La `CollectionView.ItemsSource` propiedad se establece en una `List<T>` instancia de que se crea con un `string` argumento de tipo, utilizando el tipo integrado de XAML 2009 `x:String` . La `List<string>` colección se inicializa con varios `string` elementos.

Como alternativa, pero equivalente, `List<T>` se pueden crear instancias de la colección con el `String` tipo clr:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="sys:String">
                <sys:String>Baboon</sys:String>
                <sys:String>Capuchin Monkey</sys:String>
                <sys:String>Blue Monkey</sys:String>
                <sys:String>Squirrel Monkey</sys:String>
                <sys:String>Golden Lion Tamarin</sys:String>
                <sys:String>Howler Monkey</sys:String>
                <sys:String>Japanese Macaque</sys:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

## <a name="single-object-type-argument"></a>Argumento de tipo de objeto único

Se puede especificar un único argumento de tipo de objeto como un argumento de cadena con prefijo mediante la `x:TypeArguments` Directiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="models:Monkey">
                <models:Monkey Name="Baboon"
                               Location="Africa and Asia"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                <models:Monkey Name="Capuchin Monkey"
                               Location="Central and South America"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />
                <models:Monkey Name="Blue Monkey"
                               Location="Central and East Africa"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage>
```

En este ejemplo, `GenericsDemo.Models` se define como el `models` espacio de nombres XAML y `System.Collections.Generic` se define como el `scg` espacio de nombres XAML. La `CollectionView.ItemsSource` propiedad se establece en una `List<T>` instancia de que se crea con un `Monkey` argumento de tipo. La `List<Monkey>` colección se inicializa con varios `Monkey` elementos y una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de cada `Monkey` objeto se establece como la `ItemTemplate` de [`CollectionView`](xref:Xamarin.Forms.CollectionView) .

## <a name="multiple-type-arguments"></a>Varios argumentos de tipo

Se pueden especificar varios argumentos de tipo como argumentos de cadena con prefijo, delimitados por una coma, mediante la `x:TypeArguments` Directiva. Cuando una restricción genérica usa tipos genéricos, los argumentos de tipo de restricción anidados se incluyen entre paréntesis:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="scg:KeyValuePair(x:String,models:Monkey)">
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Baboon</x:String>
                        <models:Monkey Location="Africa and Asia"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Capuchin Monkey</x:String>
                        <models:Monkey Location="Central and South America"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />   
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Blue Monkey</x:String>
                        <models:Monkey Location="Central and East Africa"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding Value.ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Key}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Value.Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage    
```

En este ejemplo, `GenericsDemo.Models` se define como el `models` espacio de nombres XAML y `System.Collections.Generic` se define como el `scg` espacio de nombres XAML. La `CollectionView.ItemsSource` propiedad se establece en un `List<T>` que se crea una instancia de con una `KeyValuePair<TKey, TValue>` restricción, con los argumentos de tipo de restricción interna `string` y `Monkey` . La `List<KeyValuePair<string,Monkey>>` colección se inicializa con varios `KeyValuePair` elementos, mediante el constructor no predeterminado `KeyValuePair` , y un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de cada `Monkey` objeto se establece como la `ItemTemplate` de [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Para obtener información sobre cómo pasar argumentos a un constructor no predeterminado, vea [pasar argumentos de constructor](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments).

## <a name="related-links"></a>Vínculos relacionados

- [Genéricos en XAML (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [Primitivas del lenguaje XAML 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [Extensión de marcado x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)
- [Pasar argumentos de constructor](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments)
