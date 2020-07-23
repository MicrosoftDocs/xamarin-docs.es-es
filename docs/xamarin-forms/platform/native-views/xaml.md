---
title: Vistas nativas en XAML
description: Se puede hacer referencia directamente a las vistas nativas de iOS, Android y el Plataforma universal de Windows desde Xamarin.Forms los archivos XAML. Las propiedades y los controladores de eventos se pueden establecer en las vistas nativas y pueden interactuar con las Xamarin.Forms vistas. En este artículo se muestra cómo consumir vistas nativas de Xamarin.Forms archivos XAML.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2c271c3537c6e96497763c67c5b8128148191f16
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937363"
---
# <a name="native-views-in-xaml"></a>Vistas nativas en XAML

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Se puede hacer referencia directamente a las vistas nativas de iOS, Android y el Plataforma universal de Windows desde Xamarin.Forms los archivos XAML. Las propiedades y los controladores de eventos se pueden establecer en las vistas nativas y pueden interactuar con las Xamarin.Forms vistas. En este artículo se muestra cómo consumir vistas nativas de Xamarin.Forms archivos XAML._

Para insertar una vista nativa en un Xamarin.Forms archivo XAML:

1. Agregue una `xmlns` declaración de espacio de nombres en el archivo XAML para el espacio de nombres que contiene la vista nativa.
1. Cree una instancia de la vista nativa en el archivo XAML.

> [!IMPORTANT]
> El código XAML compilado debe estar deshabilitado para cualquier página XAML que use vistas nativas. Esto se puede lograr decorando la clase de código subyacente de la página XAML con el `[XamlCompilation(XamlCompilationOptions.Skip)]` atributo. Para obtener más información sobre la compilación de XAML, vea [compilación de XAML en Xamarin.Forms ](~/xamarin-forms/xaml/xamlc.md).

Para hacer referencia a una vista nativa desde un archivo de código subyacente, debe usar un proyecto de recurso compartido (SAP) y ajustar el código específico de la plataforma con directivas de compilación condicional. Para obtener más información, vea [referencia a las vistas nativas desde el código](#refer-to-native-views-from-code).

## <a name="consume-native-views"></a>Consumir vistas nativas

En el ejemplo de código siguiente se muestra cómo consumir vistas nativas para cada plataforma a Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Además de especificar `clr-namespace` y `assembly` para un espacio de nombres de vista nativa, `targetPlatform` también se debe especificar un. Debe establecerse en `iOS` , `Android` , `UWP` , `Windows` (que es equivalente a `UWP` ), `macOS` , `GTK` , `Tizen` o `WPF` . En tiempo de ejecución, el analizador de XAML omitirá cualquier prefijo de espacio de nombres XML que tenga un `targetPlatform` que no coincida con la plataforma en la que se ejecuta la aplicación.

Cada declaración de espacio de nombres se puede utilizar para hacer referencia a cualquier clase o estructura del espacio de nombres especificado. Por ejemplo, la `ios` declaración de espacio de nombres se puede utilizar para hacer referencia a cualquier clase o estructura del espacio de nombres de iOS `UIKit` . Las propiedades de la vista nativa se pueden establecer a través de XAML, pero los tipos de propiedad y de objeto deben coincidir. Por ejemplo, la `UILabel.TextColor` propiedad se establece en `UIColor.Red` utilizando la `x:Static` extensión de marcado y el `ios` espacio de nombres.

Las propiedades enlazables y las propiedades enlazables asociadas también se pueden establecer en vistas nativas mediante la `Class.BindableProperty="value"` Sintaxis. Cada vista nativa se ajusta en una instancia específica de `NativeViewWrapper` la plataforma, que deriva de la [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) clase. Al establecer una propiedad enlazable o una propiedad enlazable asociada en una vista nativa, se transfiere el valor de propiedad al contenedor. Por ejemplo, se puede especificar un diseño horizontal centrado estableciendo `View.HorizontalOptions="Center"` en la vista nativa.

> [!NOTE]
> Tenga en cuenta que los estilos no se pueden usar con vistas nativas, ya que los estilos solo pueden tener como destino propiedades respaldadas por `BindableProperty` objetos.

Los constructores de widgets de Android normalmente requieren el `Context` objeto Android como argumento, y se puede hacer que esté disponible a través de una propiedad estática en la `MainActivity` clase. Por consiguiente, al crear un widget de Android en XAML, el `Context` objeto se debe pasar generalmente al constructor del widget mediante el `x:Arguments` atributo con una `x:Static` extensión de marcado. Para obtener más información, vea [pasar argumentos a vistas nativas](#pass-arguments-to-native-views).

> [!NOTE]
> Tenga en cuenta que el nombre de una vista nativa con `x:Name` no es posible en un proyecto de biblioteca de .net Standard o un proyecto de recursos compartidos (SAP). Si lo hace, se generará una variable del tipo nativo, lo que producirá un error de compilación. Sin embargo, las vistas nativas se pueden encapsular en `ContentView` instancias y recuperar en el archivo de código subyacente, siempre que se use un SAP. Para obtener más información, vea [hacer referencia a la vista nativa desde el código](#refer-to-native-views-from-code).

## <a name="native-bindings"></a>Enlaces nativos

El enlace de datos se utiliza para sincronizar una interfaz de usuario con su origen de datos y simplifica el modo en que una Xamarin.Forms aplicación muestra e interactúa con sus datos. Siempre que el objeto de origen implementa la `INotifyPropertyChanged` interfaz, el marco de enlace inserta automáticamente los cambios en el objeto de *origen* en el objeto de *destino* , y los cambios en el objeto de *destino* se pueden insertar opcionalmente en el objeto de *origen* .

Las propiedades de las vistas nativas también pueden usar el enlace de datos. En el ejemplo de código siguiente se muestra el enlace de datos mediante las propiedades de las vistas nativas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La página contiene una [`Entry`](xref:Xamarin.Forms.Entry) cuya [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad se enlaza a la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la `NativeSwitchPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa la `INotifyPropertyChanged` interfaz.

La página también contiene un conmutador nativo para cada plataforma. Cada conmutador nativo usa un [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) enlace para actualizar el valor de la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. Por lo tanto, cuando el modificador está desactivado, el `Entry` está deshabilitado y cuando el modificador está activado, el `Entry` está habilitado. Las siguientes capturas de pantallas muestran esta funcionalidad en cada plataforma:

![Conmutador nativo deshabilitado modificador nativo ](xaml-images/native-switch-disabled.png)
 ![ habilitado](xaml-images/native-switch-enabled.png)

Los enlaces bidireccionales se admiten automáticamente siempre que la propiedad nativa implementa `INotifyPropertyChanged` , o admite la observación de valor de clave (KVO) en iOS o es `DependencyProperty` en UWP. Sin embargo, muchas vistas nativas no admiten la notificación de cambio de propiedad. Para estas vistas, puede especificar un [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valor de propiedad como parte de la expresión de enlace. Esta propiedad debe establecerse en el nombre de un evento en la vista nativa que indica cuándo ha cambiado la propiedad de destino. A continuación, cuando cambia el valor del modificador nativo, `Binding` se notifica a la clase que el usuario ha cambiado el valor del modificador y el valor de la `NativeSwitchPageViewModel.IsSwitchOn` propiedad se ha actualizado.

## <a name="pass-arguments-to-native-views"></a>Pasar argumentos a vistas nativas

Los argumentos de constructor se pueden pasar a vistas nativas mediante el `x:Arguments` atributo con una `x:Static` extensión de marcado. Además, se puede llamar a los métodos del generador de vistas nativas ( `public static` métodos que devuelven objetos o valores del mismo tipo que la clase o estructura que define los métodos) especificando el nombre del método mediante el `x:FactoryMethod` atributo y sus argumentos mediante el `x:Arguments` atributo.

En el ejemplo de código siguiente se muestran ambas técnicas:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

El [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) Factory Method se usa para establecer la [`UILabel.Font`](xref:UIKit.UILabel.Font) propiedad en un nuevo [`UIFont`](xref:UIKit.UIFont) en iOS. El `UIFont` nombre y el tamaño se especifican mediante los argumentos del método que son elementos secundarios del `x:Arguments` atributo.

El [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) Factory Method se usa para establecer la [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) propiedad en un nuevo [`Typeface`](xref:Android.Graphics.Typeface) en Android. El `Typeface` nombre de familia y el estilo se especifican mediante los argumentos del método que son elementos secundarios del `x:Arguments` atributo.

El [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) constructor se usa para establecer la [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propiedad en una nueva `FontFamily` en el plataforma universal de Windows (UWP). El `FontFamily` nombre se especifica mediante el argumento de método que es un elemento secundario del `x:Arguments` atributo.

> [!NOTE]
> Los argumentos deben coincidir con los tipos requeridos por el constructor o Factory Method.

Las siguientes capturas de pantallas muestran el resultado de la especificación de Factory Method y argumentos de constructor para establecer la fuente en diferentes vistas nativas:

![Establecer fuentes en vistas nativas](xaml-images/passing-arguments.png)

Para obtener más información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

## <a name="refer-to-native-views-from-code"></a>Referencia a vistas nativas desde el código

Aunque no es posible asignar un nombre a una vista nativa con el `x:Name` atributo, es posible recuperar una instancia de vista nativa declarada en un archivo XAML de su archivo de código subyacente en un proyecto de acceso compartido, siempre que la vista nativa sea un elemento secundario de un [`ContentView`](xref:Xamarin.Forms.ContentView) que especifique un `x:Name` valor de atributo. Después, dentro de las directivas de compilación condicional en el archivo de código subyacente, debe:

1. Recupere el [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) valor de propiedad y conviértalo a un tipo específico de la plataforma `NativeViewWrapper` .
1. Recupere la `NativeViewWrapper.NativeElement` propiedad y conviértala en el tipo de vista nativa.

A continuación, se puede invocar la API nativa en la vista nativa para realizar las operaciones deseadas. Este enfoque también ofrece la ventaja de que varias vistas nativas de XAML para distintas plataformas pueden ser elementos secundarios del mismo [`ContentView`](xref:Xamarin.Forms.ContentView) . En el ejemplo de código siguiente se muestra esta técnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

En el ejemplo anterior, las vistas nativas de cada plataforma son elementos secundarios de [`ContentView`](xref:Xamarin.Forms.ContentView) los controles, con el `x:Name` valor de atributo que se usa para recuperar `ContentView` en el código subyacente:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)Se tiene acceso a la propiedad para recuperar la vista nativa ajustada como una instancia específica de la plataforma `NativeViewWrapper` . A `NativeViewWrapper.NativeElement` continuación, se tiene acceso a la propiedad para recuperar la vista nativa como su tipo nativo. A continuación, se invoca la API de la vista nativa para realizar las operaciones deseadas.

Los botones nativos de iOS y Android comparten el mismo `OnButtonTap` controlador de eventos, porque cada botón nativo consume un `EventHandler` delegado en respuesta a un evento Touch. Sin embargo, el Plataforma universal de Windows (UWP) usa un independiente `RoutedEventHandler` que, a su vez, utiliza el `OnButtonTap` controlador de eventos en este ejemplo. Por consiguiente, cuando se hace clic en un botón nativo, el `OnButtonTap` controlador de eventos ejecuta, que escala y gira el control nativo que se encuentra dentro del [`ContentView`](xref:Xamarin.Forms.ContentView) denominado `contentViewTextParent` . Las capturas de pantallas siguientes demuestran que esto sucede en cada plataforma:

![ContentView que contiene un control nativo](xaml-images/contentview.png)

## <a name="subclass-native-views"></a>Vistas nativas de subclase

Muchas vistas nativas de iOS y Android no son adecuadas para crear instancias en XAML porque usan métodos, en lugar de propiedades, para configurar el control. La solución a este problema es la subclase de vistas nativas en contenedores que definen una API más compatible con XAML que usa las propiedades para configurar el control y que usa eventos independientes de la plataforma. Las vistas nativas ajustadas se pueden colocar en un proyecto de recursos compartidos (SAP) y encerrarse con directivas de compilación condicional, o bien colocarse en proyectos específicos de la plataforma y se puede hacer referencia a ella desde XAML en un proyecto de biblioteca de .NET Standard.

En el ejemplo de código siguiente se muestra una Xamarin.Forms página que utiliza vistas nativas de subclases:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La página contiene un [`Label`](xref:Xamarin.Forms.Label) que muestra los frutos elegidos por el usuario desde un control nativo. `Label`Enlaza a la `SubclassedNativeControlsPageViewModel.SelectedFruit` propiedad. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la `SubclassedNativeControlsPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa la `INotifyPropertyChanged` interfaz.

La página también contiene una vista de selector nativa para cada plataforma. Cada vista nativa muestra la colección de Fruits enlazando su `ItemSource` propiedad a la `SubclassedNativeControlsPageViewModel.Fruits` colección. Esto permite al usuario seleccionar una fruta, tal como se muestra en las siguientes capturas de pantallas:

![Vistas nativas de subclases](xaml-images/sub-classed.png)

En iOS y Android, los selectores nativos usan métodos para configurar los controles. Por lo tanto, estos selectores deben ser subclases para exponer propiedades con el fin de que sean compatibles con XAML. En el Plataforma universal de Windows (UWP), `ComboBox` ya es compatible con XAML y, por tanto, no requiere la subclase.

### <a name="ios"></a>iOS

La implementación de iOS subclase la [`UIPickerView`](xref:UIKit.UIPickerView) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

La `MyUIPickerView` clase expone `ItemsSource` `SelectedItem` las propiedades y, y un `SelectedItemChanged` evento. Un [`UIPickerView`](xref:UIKit.UIPickerView) requiere un [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) modelo de datos subyacente, al que se tiene acceso mediante las `MyUIPickerView` propiedades y el evento. La `UIPickerViewModel` clase proporciona el modelo de datos `PickerModel` :

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

La `PickerModel` clase proporciona el almacenamiento subyacente para la `MyUIPickerView` clase, a través de la `Items` propiedad. Cada vez que cambia el elemento seleccionado en el `MyUIPickerView` , [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) se ejecuta el método, que actualiza el índice seleccionado y activa el `ItemChanged` evento. Esto garantiza que la `SelectedItem` propiedad siempre devolverá el último elemento seleccionado por el usuario. Además, la `PickerModel` clase invalida los métodos que se usan para configurar la instancia de `MyUIPickerView` .

### <a name="android"></a>Android

La implementación de Android subclase la [`Spinner`](xref:Android.Widget.Spinner) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

La `MySpinner` clase expone `ItemsSource` `SelectedObject` las propiedades y, y un `ItemSelected` evento. Los elementos que muestra la `MySpinner` clase los proporciona el [`Adapter`](xref:Android.Widget.Adapter) asociado a la vista, y los elementos se rellenan en `Adapter` cuando `ItemsSource` se establece la propiedad por primera vez. Cada vez que el elemento seleccionado en la `MySpinner` clase cambia, el `OnBindableSpinnerItemSelected` controlador de eventos actualiza la `SelectedObject` propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [NativeSwitch (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
- [Paso de argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
