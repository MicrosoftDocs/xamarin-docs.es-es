---
title: Modo de enlace de Xamarin.Forms
description: En este artículo se explica cómo controlar el flujo de información entre el origen y el destino mediante un modo de enlace, que se especifica con un miembro de la enumeración BindingMode. Todas las propiedades enlazables tienen un modo de enlace predeterminado, que indica el modo en vigor cuando esa propiedad es un destino de enlace de datos.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ab0603555c55d969e1bee8ae709df3cac23de73
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373645"
---
# <a name="xamarinforms-binding-mode"></a>Modo de enlace de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/databindingdemos)

En el [artículo anterior](basic-bindings.md), las páginas **Enlace de código alternativo** y **Enlace XAML alternativo** contenían un objeto `Label` con su propiedad `Scale` enlazada a la propiedad `Value` de un elemento `Slider`. Como el valor inicial de `Slider` es 0, la propiedad `Scale` de `Label` se establece en 0 en lugar de 1, y el elemento `Label` desaparece.

En el ejemplo [**DataBindingDemos**](/samples/xamarin/xamarin-forms-samples/databindingdemos), la página **Enlace inverso** es similar a los programas del artículo anterior, salvo que el enlace de datos se define en `Slider` en lugar de `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label"
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

A primera vista, esto podría parecer al contrario: ahora `Label` es el origen de enlace de datos y `Slider` es el destino. El enlace hace referencia a la propiedad `Opacity` de `Label`, que tiene un valor predeterminado de 1.

Como cabría esperar, `Slider` se inicializa en el valor 1 a partir del valor `Opacity` inicial de `Label`. Esto se muestra en la captura de pantalla de iOS de la izquierda:

[![Enlace inverso](binding-mode-images/reversebinding-small.png "Enlace inverso")](binding-mode-images/reversebinding-large.png#lightbox "Enlace inverso")

Pero es posible que le sorprenda que `Slider` siga funcionando, como se muestra en la captura de pantalla de Android. Esto parece sugerir que el enlace de datos funciona mejor cuando `Slider` es el destino de enlace en lugar de `Label`, porque la inicialización funciona de la forma esperada.

La diferencia entre el ejemplo **Enlace inverso** y los ejemplos anteriores implica el *modo de enlace*.

## <a name="the-default-binding-mode"></a>El modo de enlace predeterminado

El modo de enlace se especifica con un miembro de la enumeración [`BindingMode`](xref:Xamarin.Forms.BindingMode):

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; los datos van en ambas direcciones entre el origen y el destino
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; los datos van del origen al destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; los datos van del destino al origen
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; los datos van del origen al destino, pero solo cuando cambia `BindingContext` (novedad de Xamarin.Forms 3.0)

Todas las propiedades enlazables tienen un modo de enlace predeterminado que se establece cuando se crea la propiedad enlazable, y que está disponible en la propiedad [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) del objeto `BindableProperty`. Este modo de enlace predeterminado indica el modo en vigor cuando esa propiedad es un destino de enlace de datos.

El modo de enlace predeterminado para la mayoría de las propiedades como `Rotation`, `Scale` y `Opacity` es `OneWay`. Cuando estas propiedades son destinos de enlace de datos, la propiedad de destino se establece desde el origen.

Pero el modo de enlace predeterminado para la propiedad `Value` de `Slider` es `TwoWay`. Esto significa que cuando la propiedad `Value` es un destino de enlace de datos, el destino se establece desde el origen (como es habitual), pero el origen también se establece desde el destino. Esto es lo que permite que `Slider` se establezca en el valor `Opacity` inicial.

Es posible que parezca que este enlace bidireccional crea un bucle infinito, pero no es lo que sucede. Las propiedades enlazables no señalan un cambio de propiedad a menos que la propiedad cambie realmente. Esto evita un bucle infinito.

### <a name="two-way-bindings"></a>Enlaces bidireccionales

La mayoría de las propiedades enlazables tienen un modo de enlace predeterminado de `OneWay`, pero las propiedades siguientes tienen un modo de enlace predeterminado de `TwoWay`:

- Propiedad `Date` de `DatePicker`
- Propiedad `Text` de `Editor`, `Entry`, `SearchBar` y `EntryCell`
- Propiedad `IsRefreshing` de `ListView`
- Propiedad `SelectedItem` de `MultiPage`
- Propiedades `SelectedIndex` y `SelectedItem` de `Picker`
- Propiedad `Value` de `Slider` y `Stepper`
- Propiedad `IsToggled` de `Switch`
- Propiedad `On` de `SwitchCell`
- Propiedad `Time` de `TimePicker`

Estas propiedades concretas se definen como `TwoWay` por una muy buena razón:

Cuando los enlaces de datos se usan con la arquitectura de aplicación Model-View-ViewModel (MVVM), la clase ViewModel es el origen del enlace de datos y la vista, que consta de vistas como `Slider`, son destinos de enlace de datos. Los enlaces de MVVM son más similares al ejemplo **Enlace inverso** que los enlaces de los ejemplos anteriores. Es muy probable que quiera que cada vista de la página se inicialice con el valor de la propiedad correspondiente en el modelo de vista, pero los cambios en la vista también deberían afectar a la propiedad ViewModel.

Las propiedades con modos de enlace predeterminados de `TwoWay` son las que probablemente se usen más en escenarios de MVVM.

### <a name="one-way-to-source-bindings"></a>Enlaces unidireccionales al origen

Las propiedades enlazables de solo lectura tienen un modo de enlace predeterminado de `OneWayToSource`. Solo hay una propiedad enlazable de lectura y escritura que tiene un modo de enlace predeterminado de `OneWayToSource`:

- Propiedad `SelectedItem` de `ListView`

La razón es que el resultado de un enlace en la propiedad `SelectedItem` debe ser el establecimiento del origen de enlace. En un ejemplo posterior de este artículo se invalida este comportamiento.

### <a name="one-time-bindings"></a>Enlaces de un solo uso

Varias propiedades enlazables tienen un modo de enlace predeterminado de `OneTime`, que incluye la propiedad `IsTextPredictionEnabled` de `Entry`.

Las propiedades de destino con un modo de enlace de `OneTime` solo se actualizan cuando cambia el contexto de enlace. Para los enlaces en estas propiedades de destino, esto simplifica la infraestructura de enlace ya que no es necesario supervisar los cambios en las propiedades de origen.

## <a name="viewmodels-and-property-change-notifications"></a>Modelos de vista y notificaciones de cambio de propiedad

En la página **Selector de colores simple** se muestra el uso de un modelo de vista simple. Los enlaces de datos permiten al usuario seleccionar un color mediante tres elementos `Slider` para el matiz, la saturación y la luminosidad.

El modelo de vista es el origen del enlace de datos. El modelo de vista *no* define propiedades enlazables, pero implementa un mecanismo de notificación que permite que la infraestructura de enlace reciba una notificación cuando cambia el valor de una propiedad. Este mecanismo de notificación es la interfaz de [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged), que define un único evento denominado [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Una clase que implemente esta interfaz normalmente desencadena el evento cuando cambia el valor de una de sus propiedades públicas. No es necesario desencadenar el evento si la propiedad no cambia nunca. (La interfaz `INotifyPropertyChanged` también se implementa mediante `BindableObject` y se desencadena un evento `PropertyChanged` cada vez que una propiedad enlazable cambia de valor).

La `HslColorViewModel` clase define cinco propiedades: las propiedades `Hue`, `Saturation`, `Luminosity` y `Color` están interrelacionadas. Cuando cambia el valor de cualquiera de los tres componentes de color, se vuelve a calcular la propiedad `Color` y se desencadenan eventos `PropertyChanged` para las cuatro propiedades:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Cuando cambia la propiedad `Color`, el método estático `GetNearestColorName` de la clase `NamedColor` (incluido también en la solución **DataBindingDemos**)obtiene el color con nombre más cercano y establece la propiedad `Name`. Esta propiedad `Name` tiene un descriptor de acceso `set` privado, por lo que no se puede establecer desde fuera de la clase.

Cuando se establece una clase ViewModel como un origen de enlace, la infraestructura de enlace adjunta un controlador al evento `PropertyChanged`. De esta manera, el enlace puede recibir una notificación de cambios en las propiedades y, después, puede establecer las propiedades de destino a partir de los valores cambiados.

Pero cuando una propiedad de destino (o la definición `Binding` en una propiedad de destino) tiene un elemento `BindingMode` de tipo `OneTime`, no es necesario que la infraestructura de enlace adjunte un controlador al evento `PropertyChanged`. La propiedad de destino solo se actualiza cuando cambia `BindingContext` y no cuando cambia la propiedad de origen.

El archivo XAML **Selector de colores simple** crea una instancia de `HslColorViewModel` en el diccionario de recursos de la página e inicializa la propiedad `Color`. La propiedad `BindingContext` de `Grid` se establece en una extensión de enlace `StaticResource` para hacer referencia a ese recurso:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel"
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />

            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

Los elementos `BoxView`, `Label` y tres vistas `Slider` heredan el contexto de enlace del elemento `Grid`. Todas estas vistas son destinos de enlace que hacen referencia a las propiedades de origen en el modelo de vista. Para la propiedad `Color` de `BoxView` y la propiedad `Text` de `Label`, los enlaces de datos son `OneWay`: Las propiedades de la vista se establecen de las propiedades de ViewModel.

Pero la propiedad `Value` de `Slider` es `TwoWay`. Esto permite que cada elemento `Slider` se establezca a partir del modelo de vista, y también que el modelo de vista se establezca a partir de cada elemento `Slider`.

Cuando se ejecuta por primera vez el programa, `BoxView`, `Label` y los tres elementos `Slider` están establecidos a partir del modelo de vista en función de la propiedad `Color` inicial establecida cuando se creó la instancia del modelo de vista. Esto se muestra en la captura de pantalla de iOS de la izquierda:

[![Selector de colores sencillo](binding-mode-images/simplecolorselector-small.png "Selector de colores sencillo")](binding-mode-images/simplecolorselector-large.png#lightbox "Selector de colores sencillo")

Al manipular los controles deslizantes, se actualizan `BoxView` y `Label` en consecuencia, como se muestra en la captura de pantalla de Android.

Un enfoque común consiste en crear instancias de ViewModel en el diccionario de recursos. También se pueden crear instancias de ViewModel dentro de etiquetas de elemento de propiedad para la propiedad `BindingContext`. En el archivo XAML **Selector de colores simple**, intente quitar `HslColorViewModel` del diccionario de recursos y establézcalo en la propiedad `BindingContext` de `Grid` de esta forma:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

El contexto de enlace se puede establecer de varias formas. En ocasiones, el archivo de código subyacente crea una instancia de ViewModel y la establece en la propiedad `BindingContext` de la página. Todos estos enfoques son válidos.

## <a name="overriding-the-binding-mode"></a>Reemplazo del modo de enlace

Si el modo de enlace predeterminado en la propiedad de destino no es adecuado para un enlace de datos concreto, es posible invalidarlo si se establece la propiedad [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` (o la propiedad [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) de la extensión de marcado `Binding`) en uno de los miembros de la enumeración `BindingMode`.

Pero establecer la propiedad `Mode` en `TwoWay` no siempre funciona como cabría esperar. Por ejemplo, intente modificar el archivo XAML **Enlace XAML alternativo** para incluir `TwoWay` en la definición del enlace:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Se podría esperar que `Slider` se inicializara en el valor inicial de la propiedad `Scale`, que es 1, pero no es lo que sucede. Cuando se inicializa un enlace `TwoWay`, primero se establece el destino a partir del origen, lo que significa que la propiedad `Scale` se establece en el valor predeterminado de `Slider` de 0. Cuando se configura el enlace `TwoWay` en el elemento `Slider`, el elemento `Slider` se establece inicialmente a partir del origen.

Puede establecer el modo de enlace en `OneWayToSource` en el ejemplo **Enlace XAML alternativo**:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Ahora se inicializa `Slider` en 1 (el valor predeterminado de `Scale`) pero la manipulación de `Slider` no afecta a la propiedad `Scale`, por lo que esto no es muy útil.

> [!NOTE]
> La clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) también define las propiedades [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) y [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), que pueden escalar el elemento `VisualElement` de forma diferente en dirección horizontal y vertical.

Una aplicación muy útil de la invalidación del modo de enlace predeterminado con `TwoWay` implica la propiedad `SelectedItem` de `ListView`. El modo de enlace predeterminado es `OneWayToSource`. Cuando se establece un enlace de datos en la propiedad `SelectedItem` para hacer referencia a una propiedad de origen en un modelo de vista, esa propiedad de origen se establece a partir de la selección `ListView`. Pero en algunas circunstancias, es posible que le interese que también se inicialice `ListView` a partir del modelo de vista.

En la página **Configuración de ejemplo** se muestra esta técnica. Esta página representa una implementación simple de la configuración de la aplicación, que a menudo se define en un modelo de vista, como en este archivo `SampleSettingsViewModel`:

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Cada configuración de la aplicación es una propiedad que se guarda en el diccionario de propiedades de Xamarin.Forms en un método denominado `SaveState` y se carga desde ese diccionario al constructor. Hacia la parte inferior de la clase hay dos métodos que ayudan a simplificar los modelos de vista y hacer que sean menos propensos a los errores. El método `OnPropertyChanged` de la parte inferior tiene un parámetro opcional que se establece en la propiedad que realiza la llamada. Esto evita errores ortográficos al especificar el nombre de la propiedad como una cadena.

El método `SetProperty` de la clase hace incluso más: compara el valor que se establece en la propiedad con el valor almacenado como un campo, y solo llama a `OnPropertyChanged` cuando los dos valores no son iguales.

En la clase `SampleSettingsViewModel` se definen dos propiedades para el color de fondo: la propiedad `BackgroundNamedColor` es de tipo `NamedColor`, que es una clase que también se incluye en la solución **DataBindingDemos**. La propiedad `BackgroundColor` es de tipo `Color` y se obtiene de la propiedad `Color` del objeto `NamedColor`.

La clase `NamedColor` usa la fijación de .NET para enumerar todos los campos públicos estáticos en la estructura `Color` de Xamarin.Forms y almacenarlos con sus nombres en una colección que sea accesible desde la propiedad `All` estática:

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

En la clase `App` del proyecto **DataBindingDemos** se define una propiedad denominada `Settings` de tipo `SampleSettingsViewModel`. Esta propiedad se inicializa cuando se crea una instancia de la clase `App` y el método `SaveState` se llama cuando se llama al método `OnSleep`:

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Para obtener más información sobre los métodos de ciclo de vida de aplicación, vea el artículo [**Ciclo de vida de aplicación**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Prácticamente todo lo demás se controla en el archivo **SampleSettingsPage.xaml**. El elemento `BindingContext` de la página se establece mediante una extensión de marcado `Binding`: el origen de enlace es la propiedad estática `Application.Current`, que es la instancia de la clase `App` del proyecto, y `Path` se establece en la propiedad `Settings`, que es el objeto `SampleSettingsViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Todos los elementos secundarios de la página heredan el contexto de enlace. La mayoría de los demás enlaces de esta página son para las propiedades de `SampleSettingsViewModel`. La propiedad `BackgroundColor` se usa para establecer la propiedad `BackgroundColor` de `StackLayout`, y las propiedades `Entry`, `DatePicker`, `Switch` y `Stepper` se enlazan a otras propiedades del modelo de vista.

La propiedad `ItemsSource` de `ListView` se establece en la propiedad estática `NamedColor.All`. Esto rellena `ListView` con todas las instancias de `NamedColor`. Para cada elemento de `ListView`, el contexto de enlace para el elemento se establece en un objeto `NamedColor`. Los elementos `BoxView` y `Label` de `ViewCell` están enlazados a propiedades de `NamedColor`.

La propiedad `SelectedItem` de `ListView` es de tipo `NamedColor` y se enlaza a la propiedad `BackgroundNamedColor` de `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

El modo de enlace predeterminado para `SelectedItem` es `OneWayToSource`, que establece la propiedad ViewModel a partir del elemento seleccionado. El modo `TwoWay` permite que se inicialice `SelectedItem` a partir del modelo de vista.

Pero cuando `SelectedItem` se establece de esta forma, `ListView` no se desplaza automáticamente para mostrar el elemento seleccionado. Se necesita un poco de código en el archivo de código subyacente:

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem,
                                   ScrollToPosition.MakeVisible,
                                   false);
        }
    }
}
```

En la captura de pantalla de iOS de la izquierda se muestra el programa al ejecutarlo por primera vez. El constructor de `SampleSettingsViewModel` inicializa el color de fondo en blanco, y eso es lo que está seleccionado en `ListView`:

[![Configuración de ejemplo](binding-mode-images/samplesettings-small.png "Configuración de ejemplo")](binding-mode-images/samplesettings-large.png#lightbox "Configuración de ejemplo")

La otra captura de pantalla muestra la configuración modificada. Al experimentar con esta página, recuerde colocar el programa en modo de suspensión o finalícelo en el dispositivo o emulador que se está ejecutando. La finalización del programa desde el depurador de Visual Studio no provocará que se llame a la invalidación de `OnSleep` en la clase `App`.

En el artículo siguiente verá cómo especificar los [**formatos de cadena**](string-formatting.md) de los enlaces de datos que se establecen en la propiedad `Text` de `Label`.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre el enlace de datos del libro Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)