---
title: Interfaz de comandos de Xamarin.Forms
description: En este artículo se explica cómo implementar la propiedad Command con enlaces de datos de Xamarin.Forms. La interfaz de comandos proporciona un enfoque alternativo para implementar comandos que se adapta mucho mejor a la arquitectura MVVM.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 253255f08cec6f08e03df94798c8572f7cf10f30
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139729"
---
# <a name="the-xamarinforms-command-interface"></a>Interfaz de comandos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

En la arquitectura Model-View-ViewModel (MVVM), los enlaces de datos se definen entre las propiedades de ViewModel, que suele ser una clase que se deriva de `INotifyPropertyChanged`, y las propiedades en la vista, que suele ser el archivo XAML. A veces, una aplicación tiene necesidades que van más allá de estos enlaces de propiedad y solicita al usuario que inicie comandos que influyen en el modelo de vista. Por lo general, estos comandos se señalizan mediante clics de botón o pulsaciones con el dedo, y tradicionalmente se procesan en el archivo de código subyacente en un controlador para el evento `Clicked` del elemento `Button` o el evento `Tapped` de un elemento `TapGestureRecognizer`.

La interfaz de comandos proporciona un enfoque alternativo para implementar comandos que se adapta mucho mejor a la arquitectura MVVM. El propio modelo de vista puede contener comandos, que son métodos que se ejecutan en respuesta a una actividad específica en la vista como un clic de `Button`. Los enlaces de datos se definen entre estos comandos y el objeto `Button`.

Para permitir un enlace de datos entre un objeto `Button` y un ViewModel, el objeto `Button` define dos propiedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de tipo `Object`

Para usar la interfaz de comandos, defina un enlace de datos que tenga como destino la propiedad `Command` del objeto `Button` donde el origen sea una propiedad en el modelo de vista de tipo `ICommand`. El modelo de vista contiene código asociado con la propiedad `ICommand` que se ejecuta cuando se hace clic en el botón. Puede establecer `CommandParameter` en datos arbitrarios para distinguir entre varios botones si todos se enlazan a la misma propiedad `ICommand` en el modelo de vista.

Las propiedades `Command` y `CommandParameter` también se definen mediante las clases siguientes:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) y, por tanto, [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), que se deriva de `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) y, por tanto, [`ImageCell`](xref:Xamarin.Forms.ImageCell), que se deriva de `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) define una propiedad [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) de tipo `ICommand` y una propiedad [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter). La propiedad [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) de [`ListView`](xref:Xamarin.Forms.ListView) también es de tipo `ICommand`.

Todos estos comandos se pueden controlar en un modelo de vista de forma que no dependa del objeto de interfaz de usuario concreto de la vista.

## <a name="the-icommand-interface"></a>La interfaz ICommand

La interfaz [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand) no forma parte de Xamarin.Forms. En su lugar, se define en el espacio de nombres [System.Windows.Input](xref:System.Windows.Input) y consta de dos métodos y un evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Para usar la interfaz de comandos, el modelo de vista contiene propiedades de tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

El modelo de vista también debe hacer referencia a una clase que implemente la interfaz `ICommand`. Esta clase se describirá en breve. En la vista, la propiedad `Command` de un objeto `Button` está enlazada a esa propiedad:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Cuando el usuario presiona el elemento `Button`, `Button` llama al método `Execute` del objeto `ICommand` enlazado a su propiedad `Command`. Es la parte más sencilla de la interfaz de comandos.

El método `CanExecute` es más complejo. Cuando se define por primera vez el enlace en la propiedad `Command` del objeto `Button`, y cuando cambia de algún modo el enlace de datos, el objeto `Button` llama al método `CanExecute` del objeto `ICommand`. Si `CanExecute` devuelve `false`, el objeto `Button` se deshabilita a sí mismo. Esto indica que el comando concreto no está disponible actualmente o no es válido.

El objeto `Button` también adjunta un controlador al evento `CanExecuteChanged` de `ICommand`. El evento se desencadena desde dentro del modelo de vista. Cuando se desencadena ese evento, el objeto `Button` vuelve a llamar a `CanExecute`. El objeto `Button` se habilita a sí mismo si `CanExecute` devuelve `true` y se deshabilita si `CanExecute` devuelve `false`.

> [!IMPORTANT]
> No use la propiedad `IsEnabled` de `Button` si usa la interfaz de comandos.  

## <a name="the-command-class"></a>La clase Command

Cuando en el modelo de vista se define una propiedad de tipo `ICommand`, el modelo de vista también debe contener o hacer referencia a una clase que implemente la interfaz `ICommand`. Esta clase debe contener o hacer referencia a los métodos `Execute` y `CanExecute`, y desencadenar el evento `CanExecuteChanged` cada vez que el método `CanExecute` pueda devolver otro valor.

Puede escribir este tipo de clase, o bien puede usar una escrita por otra persona. Como `ICommand` forma parte de Microsoft Windows, se ha usado durante años con las aplicaciones MVVM de Windows. El uso de una clase de Windows que implementa `ICommand` permite compartir los modelos de vista entre las aplicaciones de Windows y las de Xamarin.Forms.

Si el uso compartido de modelos de vista entre Windows y Xamarin.Forms no constituye un problema, puede usar la clase [`Command`](xref:Xamarin.Forms.Command) o [`Command<T>`](xref:Xamarin.Forms.Command`1) incluida en Xamarin.Forms para implementar la interfaz `ICommand`. Estas clases permiten especificar los cuerpos de los métodos `Execute` y `CanExecute` en los constructores de clase. Use `Command<T>` cuando use la propiedad `CommandParameter` para distinguir entre varias vistas enlazadas a la misma propiedad `ICommand`, y la clase `Command` más sencilla cuando no sea un requisito.

## <a name="basic-commanding"></a>Comandos básicos

En la página **Entrada de personas** del programa [**Demostraciones de enlace de datos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) se muestran algunos comandos sencillos implementados en un modelo de vista.

`PersonViewModel` define tres propiedades denominadas `Name`, `Age` y `Skills` que definen una persona. Esta clase *no* contiene ninguna propiedad `ICommand`:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
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

El elemento `PersonCollectionViewModel` que se muestra a continuación crea objetos de tipo `PersonViewModel` y permite al usuario rellenar los datos. Para ello, la clase define las propiedades `IsEditing` de tipo `bool` y `PersonEdit` de tipo `PersonViewModel`. Además, la clase define tres propiedades de tipo `ICommand` y una propiedad denominada `Persons` de tipo `IList<PersonViewModel>`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
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

Esta lista abreviada no incluye el constructor de la clase, que es donde se definen las tres propiedades de tipo `ICommand`, que se mostrará en breve. Tenga en cuenta que los cambios en las tres propiedades de tipo `ICommand` y la propiedad `Persons` no hacen que se desencadenen eventos `PropertyChanged`. Estas propiedades se establecen al crear la clase por primera vez y no cambian a partir de entonces.

Antes de examinar el constructor de la clase `PersonCollectionViewModel`, veremos los archivos XAML para el programa **Entrada de personas**. Este archivo contiene un elemento `Grid` con su propiedad `BindingContext` establecida en el elemento `PersonCollectionViewModel`. El objeto `Grid` contiene un objeto `Button` con el texto **New** con su propiedad `Command` enlazada a la propiedad `NewCommand` del modelo de vista, un formulario de entrada con propiedades enlazadas a la propiedad `IsEditing`, así como las propiedades de `PersonViewModel`, y dos botones más enlazados a las propiedades `SubmitCommand` y `CancelCommand` de la clase ViewModel. El objeto `ListView` final muestra la colección de las personas que ya se han introducido:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Este es el funcionamiento: el usuario presiona primero el botón **New** (Nuevo). Esto habilita el formulario de entrada, pero deshabilita el botón **New**. Después, el usuario escribe un nombre, la edad y las habilidades. En cualquier momento durante la edición, el usuario puede presionar el botón **Cancel** (Cancelar) para volver a empezar. El botón **Submit** (Enviar) solo se habilita cuando se ha escrito un nombre y una edad válida. Al presionar este botón **Submit**, se transfiere a la persona a la colección mostrada por `ListView`. Después de presionar el botón **Cancel** o **Submit**, se borra el formulario de entrada y se vuelve a habilitar el botón **New**.

En la pantalla de iOS de la izquierda se muestra el diseño antes de escribir una vigencia válida. En la pantallas de Android se muestra el botón **Enviar** habilitado después de haber establecido una edad:

[![Entrada de persona](commanding-images/personentry-small.png "Entrada de persona")](commanding-images/personentry-large.png#lightbox "Entrada de persona")

El programa no tiene ninguna función para editar las entradas existentes y no guarda las entradas al salir de la página.

Toda la lógica para los botones **New**, **Submit** y **Cancel** se controla en `PersonCollectionViewModel` a través de definiciones de las propiedades `NewCommand`, `SubmitCommand` y `CancelCommand`. El constructor de `PersonCollectionViewModel` establece estas tres propiedades en objetos de tipo `Command`.  

Un [constructor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de la clase `Command` permite pasar argumentos de tipo `Action` y `Func<bool>` correspondientes a los métodos `Execute` y `CanExecute`. Es más fácil definir estas acciones y funciones como funciones lambda directamente en el constructor de `Command`. Esta es la definición del objeto `Command` para la propiedad `NewCommand`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Cuando el usuario hace clic en el botón **New**, se ejecuta la función `execute` pasada al constructor de `Command`. Esto crea un objeto `PersonViewModel`, establece un controlador en el evento `PropertyChanged` de ese objeto, establece `IsEditing` en `true`, y llama al método `RefreshCanExecutes` definido después del constructor.

Además de implementar la interfaz `ICommand`, la clase `Command` también define un método denominado `ChangeCanExecute`. El modelo de vista debe llamar a `ChangeCanExecute` para una propiedad `ICommand` cada vez que suceda algo que pueda cambiar el valor devuelto del método `CanExecute`. Una llamada a `ChangeCanExecute` hace que la clase `Command` desencadene el método `CanExecuteChanged`. El objeto `Button` ha adjuntado un controlador para ese evento y responde mediante una nueva llamada a `CanExecute` y, después, se habilita a sí mismo en función del valor devuelto de ese método.

Cuando el método `execute` de `NewCommand` llama a `RefreshCanExecutes`, la propiedad `NewCommand` recibe una llamada a `ChangeCanExecute`, y `Button` llama al método `canExecute`, que ahora devuelve `false` porque la propiedad `IsEditing` es `true`.

El controlador `PropertyChanged` para el nuevo objeto `PersonViewModel` llama al método `ChangeCanExecute` de `SubmitCommand`. Aquí se muestra la implementación de esa propiedad de comando:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

La función `canExecute` para `SubmitCommand` se llama cada vez que cambia una propiedad en el objeto `PersonViewModel` que se está editando. Solo devuelve `true` cuando la propiedad `Name` tiene al menos un carácter de longitud, y `Age` es mayor que 0. En ese momento, se habilita el botón **Submit**.

La función `execute` para **Submit** quita el controlador de cambio de propiedad de `PersonViewModel`, agrega el objeto a la colección `Persons` y devuelve todo a las condiciones iniciales.

La función `execute` para el botón **Cancel** hace lo mismo que el botón **Submit** excepto agregar el objeto a la colección:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

El método `canExecute` devuelve `true` en cualquier momento que se modifique un elemento `PersonViewModel`.

Estas técnicas se podrían adaptar para escenarios más complejos: una propiedad de `PersonCollectionViewModel` se podría enlazar a la propiedad `SelectedItem` del objeto `ListView` para editar los elementos existentes, y se podría agregar un botón **Delete** (Eliminar) para eliminar esos elementos.

No es necesario definir los métodos `execute` y `canExecute` como funciones lambda. Puede escribirlos como métodos privados estándar en el modelo de vista y hacer referencia a ellos en constructores de `Command`. Pero este enfoque tiende a crear una gran cantidad de métodos a los que solo se hace referencia una vez en el modelo de vista.

## <a name="using-command-parameters"></a>Uso de parámetros de comando

A veces es conveniente que uno o varios botones (u otros objetos de interfaz de usuario) compartan la misma propiedad `ICommand` en el modelo de vista. En este caso, se usa la propiedad `CommandParameter` para distinguir los botones.

Se puede seguir usando la clase `Command` para estas propiedades `ICommand` compartidas. La clase define un [constructor alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) que acepta métodos `execute` y `canExecute` con parámetros de tipo `Object`. Esta es la forma de pasar `CommandParameter` a estos métodos.

Pero cuando se usa `CommandParameter`, resulta más fácil utilizar la clase [`Command<T>`](xref:Xamarin.Forms.Command`1) genérica para especificar el tipo del objeto establecido en `CommandParameter`. Los métodos `execute` y `canExecute` que especifique tienen parámetros de ese tipo.

En la página **Teclado decimal** se ilustra esta técnica y se muestra cómo implementar un teclado numérico para escribir números decimales. El elemento `BindingContext` para el objeto `Grid` es un elemento `DecimalKeypadViewModel`. La propiedad `Entry` de este modelo de vista se enlaza a la propiedad `Text` de un elemento `Label`. Todos los objetos `Button` están enlazados a varios comandos del modelo de vista: `ClearCommand`, `BackspaceCommand` y `DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

Los 11 botones para los 10 dígitos y el separador decimal comparten un enlace a `DigitCommand`. `CommandParameter` distingue entre estos botones. El valor establecido en `CommandParameter` suele ser el mismo que el texto que se muestra en el botón, excepto por el separador decimal que, para una mayor claridad, se muestra con un carácter de punto central.

Este es el programa en acción:

[![Teclado decimal](commanding-images/decimalkeyboard-small.png "Teclado decimal")](commanding-images/decimalkeyboard-large.png#lightbox "Teclado decimal")

Observe que el botón para el separador decimal en las tres capturas de pantalla está deshabilitado porque el número introducido ya contiene un separador decimal.

`DecimalKeypadViewModel` define una propiedad `Entry` de tipo `string` (que es la única que desencadena un evento `PropertyChanged`) y tres propiedades de tipo `ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

El botón correspondiente a `ClearCommand` siempre está habilitado y simplemente establece la entrada en "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

Como el botón siempre está habilitado, no es necesario especificar un argumento `canExecute` en el constructor de `Command`.

La lógica para escribir números y el retroceso es un poco complicada, porque si no se ha especificado ningún dígito, la propiedad `Entry` es la cadena "0". Si el usuario escribe más ceros, `Entry` todavía contiene un solo cero. Si el usuario escribe cualquier otro dígito, ese dígito reemplaza al cero. Pero si el usuario escribe un separador decimal antes de cualquier otro dígito, `Entry` es la cadena "0.".

El botón **Backspace** (Retroceso) solo se habilita cuando la longitud de la entrada es mayor que 1, o bien si `Entry` no es igual a la cadena "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

La lógica para la función `execute` para el botón **Backspace** garantiza que `Entry` es al menos una cadena de "0".

La propiedad `DigitCommand` está enlazada a 11 botones, que se identifican a sí mismos con la propiedad `CommandParameter` de forma individual. Se podría establecer `DigitCommand` en una instancia de la clase `Command` normal, pero es más fácil usar la clase `Command<T>` genérica. Cuando se usa la interfaz de comandos con XAML, las propiedades `CommandParameter` suelen ser cadenas, y ese es el tipo del argumento genérico. Las funciones `execute` y `canExecute` tienen argumentos de tipo `string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

El método `execute` anexa el argumento de cadena a la propiedad `Entry`. Pero si el resultado comienza con un cero (pero no un cero y un separador decimal), ese cero inicial se debe quitar mediante la función `Substring`.

El método `canExecute` devuelve `false` solo si el argumento es el separador decimal (lo que indica que se presiona el separador decimal) y `Entry` ya contiene un separador decimal.

Todos los métodos `execute` llaman a `RefreshCanExecutes`, que llama a `ChangeCanExecute` para `DigitCommand` y `ClearCommand`. Esto garantiza que los botones de retroceso y separador decimal se habilitan o deshabilitan en función de la secuencia actual de dígitos especificados.

## <a name="adding-commands-to-existing-views"></a>Adición de comandos a las vistas existentes

Si quiere emplear la interfaz de comandos con vistas que no la admiten, puede usar un comportamiento de Xamarin.Forms que convierte un evento en un comando. Esto se describe en el artículo [**EventToCommandBehavior reutilizable**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Comandos asincrónicos para menús de navegación

Los comandos son útiles para la implementación de menús de navegación, como los del propio programa [**Demostraciones de enlace de datos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos). Este es un fragmento de **MainPage.xaml**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Al usar comandos con XAML, las propiedades `CommandParameter` normalmente se establecen en cadenas. Pero en este caso, se usa una extensión de marcado XAML para que `CommandParameter` sea de tipo `System.Type`.

Cada propiedad `Command` se enlaza a una propiedad denominada `NavigateCommand`. Esa propiedad se define en el archivo de código subyacente, **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

El constructor establece la propiedad `NavigateCommand` en un método `execute` que crea una instancia del parámetro `System.Type` y, después, navega hasta ella. Como la llamada a `PushAsync` requiere un operador `await`, el método `execute` debe estar marcado como asincrónico. Esto se consigue con la palabra clave `async` por delante de la lista de parámetros.

El constructor establece también el elemento `BindingContext` de la página en sí mismo para que los enlaces hagan referencia a `NavigateCommand` en esta clase.

El orden del código de este constructor establece una diferencia: la llamada a `InitializeComponent` hace que se analice el código XAML, pero en ese momento, el enlace a una propiedad denominada `NavigateCommand` no se puede resolver porque `BindingContext` está establecido en `null`. Si se establece `BindingContext` en el constructor *antes* de establecer `NavigateCommand`, el enlace se puede resolver cuando se establezca `BindingContext`, pero en ese momento, `NavigateCommand` sigue siendo `null`. Establecer `NavigateCommand` después de `BindingContext` no tendrá ningún efecto en el enlace porque un cambio en `NavigateCommand` no desencadena un evento `PropertyChanged` y el enlace no sabe que `NavigateCommand` ahora es válido.

El establecimiento de `NavigateCommand` y `BindingContext` (en cualquier orden) antes de llamar a `InitializeComponent` funcionará porque los dos componentes del enlace se establecen cuando el analizador XAML encuentra la definición de enlace.

En ocasiones, los enlaces de datos pueden resultar complicados, pero como ha visto en esta serie de artículos, son eficaces y versátiles, y ayudan considerablemente a organizar el código mediante la separación de la lógica subyacente de la interfaz de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
- [Capítulo sobre el enlace de datos del libro Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
