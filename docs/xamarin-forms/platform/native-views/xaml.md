---
title: Vistas nativas en XAML
description: Vistas nativas de iOS, Android y la plataforma Universal de Windows pueden hacer referencia directamente desde los archivos XAML de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms. En este artículo se muestra cómo consumir vistas nativas en archivos XAML de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: bf9b630613dcc5c25dac96cad71e54022ead1812
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649594"
---
# <a name="native-views-in-xaml"></a>Vistas nativas en XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Vistas nativas de iOS, Android y la plataforma Universal de Windows pueden hacer referencia directamente desde los archivos XAML de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms. En este artículo se muestra cómo consumir vistas nativas en archivos XAML de Xamarin.Forms._

En este artículo se trata los temas siguientes:

- [Consumo de vistas nativas](#consuming) – el proceso para utilizar una vista en XAML nativa.
- [Uso de enlaces nativos](#native_bindings) : a y desde las propiedades de vistas nativas de enlace de datos.
- [Pasar argumentos a vistas nativas](#passing_arguments) : pasar argumentos a los constructores de la vista nativa y llamar a vista nativa de los métodos de fábrica.
- [Que hace referencia a vistas nativas desde código](#native_view_code) – recuperar instancias de vista nativa declarados en un archivo XAML, el archivo de código subyacente.
- [Creación de subclases de vistas nativas](#subclassing) : creación de subclases de vistas nativas para definir una API compatible con XAML.  

<a name="overview" />

## <a name="overview"></a>Información general

Para insertar una vista nativa a un archivo XAML de Xamarin.Forms:

1. Agregar un `xmlns` declaración de espacio de nombres en el archivo XAML para el espacio de nombres que contiene la vista nativa.
1. Cree una instancia de la vista nativa en el archivo XAML.

> [!IMPORTANT]
> El código XAML compilado debe estar deshabilitado para cualquier página XAML que use vistas nativas. Esto se puede lograr decorando la clase de código subyacente de la página XAML con el `[XamlCompilation(XamlCompilationOptions.Skip)]` atributo. Para obtener más información sobre la compilación XAML, vea compilar [XAML en Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Para hacer referencia a una vista nativa desde un archivo de código subyacente, debe usar un proyecto de activos compartidos (SAP) y ajustar el código específico de plataforma con directivas de compilación condicional. Para obtener más información, consulte [que hace referencia a vistas nativas desde código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumo de vistas nativas

En el ejemplo de código siguiente se muestra cómo consumir vistas nativas para cada plataforma para un objeto Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

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

Así como especificar el `clr-namespace` y `assembly` para un espacio de nombres de vista nativo, un `targetPlatform` también debe especificarse. Esto se debe establecer en uno de los valores de la [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumeración y normalmente se establecerá en `iOS`, `Android`, o `Windows`. En tiempo de ejecución, el analizador XAML pasará por alto los prefijos de espacios de nombres XML que tienen un `targetPlatform` que no coincide con la plataforma en la que se ejecuta la aplicación.

Cada declaración de espacio de nombres puede utilizarse para hacer referencia a cualquier clase o estructura del espacio de nombres especificado. Por ejemplo, el `ios` declaración de espacio de nombres se puede usar para hacer referencia a cualquier clase o estructura desde iOS `UIKit` espacio de nombres. Se pueden establecer propiedades de la vista nativa a través de XAML, pero deben coincidir con los tipos de propiedad y objeto. Por ejemplo, el `UILabel.TextColor` propiedad está establecida en `UIColor.Red` utilizando el `x:Static` extensión de marcado y el `ios` espacio de nombres.

Propiedades enlazables y adjuntadas propiedades enlazables también pueden establecerse en vistas nativas mediante el uso de la `Class.BindableProperty="value"` sintaxis. Cada vista nativa se encapsula en una plataforma específica `NativeViewWrapper` instancia, que se deriva de la [ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View) clase. Establecer una propiedad enlazable o una propiedad enlazable adjunta en una vista nativa, el valor de propiedad transfiere al contenedor. Por ejemplo, se puede especificar un diseño centrado horizontal estableciendo `View.HorizontalOptions="Center"` en la vista nativa.

> [!NOTE]
> Tenga en cuenta que los estilos no se puede usar con vistas nativas, porque los estilos solo pueden tener como destino las propiedades que están respaldadas por `BindableProperty` objetos.

Constructores de widget Android requieren generalmente el Android `Context` objeto como un argumento y esto es posible acceder a través de una propiedad estática en el `MainActivity` clase. Por lo tanto, al crear un widget de Android en XAML, el `Context` objeto normalmente se debe pasar al constructor del widget mediante el `x:Arguments` atributo con un `x:Static` extensión de marcado. Para obtener más información, consulte [pasar argumentos a vistas nativas](#passing_arguments).

> [!NOTE]
> Tenga en cuenta que una vista nativa con nombres `x:Name` no es posible en un proyecto de biblioteca estándar de .NET o un proyecto de activos compartidos (SAP). Si lo hace, se generará una variable del tipo nativo, lo que provocará un error de compilación. Sin embargo, las vistas nativas pueden envolverse en `ContentView` instancias y recuperarse en el archivo de código subyacente, siempre que se utilice un SAP. Para obtener más información, consulte [Referencia a una vista nativa desde el código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Enlaces nativos

Enlace de datos se utiliza para sincronizar una interfaz de usuario con su origen de datos y simplifica la forma en que una aplicación de Xamarin.Forms muestra e interactúa con sus datos. Siempre que el objeto fuente implemente la interfaz `INotifyPropertyChanged`, los cambios en el objeto *fuente* se envían automáticamente al objeto *objetivo* por el marco vinculante, y los cambios en el objeto *objetivo* opcionalmente se pueden enviar al objeto *fuente*.

Las propiedades de las vistas nativas también pueden usar el enlace de datos. El siguiente ejemplo de código demuestra el enlace de datos usando propiedades de vistas nativas:

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

La página contiene una propiedad [`Entry`](xref:Xamarin.Forms.Entry) cuya [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) se une a la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. El [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la `NativeSwitchPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa el `INotifyPropertyChanged` interfaz.

La página también contiene un conmutador para cada plataforma. Cada conmutador nativa usa un [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay) enlace para actualizar el valor de la `NativeSwitchPageViewModel.IsSwitchOn` propiedad. Por lo tanto, cuando el conmutador está desactivado, el `Entry` está deshabilitado, y cuando el modificador está activado, el `Entry` está habilitado. Las capturas de pantalla siguientes muestran cómo esta funcionalidad en cada plataforma:

![](xaml-images/native-switch-disabled.png "Conmutador nativo Deshabilitado")
![](xaml-images/native-switch-enabled.png "Conmutador nativo habilitado")

Automáticamente se admiten enlaces bidireccionales siempre que implemente la propiedad nativa `INotifyPropertyChanged`, admite la observación de pares clave-valor (KVO) en iOS o es una `DependencyProperty` en UWP. Sin embargo, muchas vistas nativas no admiten la notificación de cambio de propiedad. Estas vistas, puede especificar un [ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valor de propiedad como parte de la expresión de enlace. Esta propiedad debe establecerse en el nombre de un evento en la vista nativo que indica cuándo ha cambiado la propiedad de destino. Luego, cuando cambia el valor del conmutador nativo, se notifica a la clase `Binding` que el usuario ha cambiado el valor del conmutador y se actualiza el valor de la propiedad `NativeSwitchPageViewModel.IsSwitchOn`.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Pasar argumentos a vistas nativas

Los argumentos de constructor se pueden pasar a vistas nativas usando el atributo `x:Arguments` con una `x:Static` extensión de marcado. Además, los métodos de fábrica de vista nativa (`public static` métodos que devuelven objetos o valores del mismo tipo que la clase o estructura que define los métodos) pueden invocarse especificando el nombre del método utilizando el atributo `x:FactoryMethod` y sus argumentos utilizando el atributo `x:Arguments`.

El ejemplo de código siguiente muestra ambas técnicas:

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

El [ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*) método de generador que se usa para establecer el [ `UILabel.Font` ](xref:UIKit.UILabel.Font) propiedad a un nuevo [ `UIFont` ](xref:UIKit.UIFont) en iOS. El `UIFont` nombre y tamaño se especifican mediante los argumentos del método que son elementos secundarios de la `x:Arguments` atributo.

El [ `Typeface.Create` ](xref:Android.Graphics.Typeface.Create*) método de generador que se usa para establecer el [ `TextView.Typeface` ](xref:Android.Widget.TextView.Typeface) propiedad a un nuevo [ `Typeface` ](xref:Android.Graphics.Typeface) en Android. El `Typeface` nombre de familia y estilo se especifican mediante los argumentos del método que son elementos secundarios de la `x:Arguments` atributo.

El [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) constructor se usa para establecer el [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propiedad a un nuevo `FontFamily` en la plataforma Universal de Windows (UWP). El `FontFamily` nombre se especifica mediante el argumento del método que es un elemento secundario de la `x:Arguments` atributo.

> [!NOTE]
> Argumentos deben coincidir con los tipos requeridos por el constructor o método generador.

Las capturas de pantalla siguientes muestran el resultado de especificar los argumentos de método y constructor del generador para establecer la fuente en diferentes vistas nativas:

![](xaml-images/passing-arguments.png "Fuentes de la configuración de vistas nativas")

Para obtener más información sobre cómo pasar argumentos en XAML, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Que hace referencia a vistas nativas desde código

Aunque no se puede nombrar una vista nativa con el `x:Name` atributo, es posible recuperar una instancia de la vista nativa declarada en un archivo XAML de su archivo de código subyacente en un proyecto de acceso compartido, siempre que la vista nativa es un elemento secundario de un [ `ContentView` ](xref:Xamarin.Forms.ContentView) que especifica un `x:Name` valor del atributo. A continuación, dentro de las directivas de compilación condicional en el archivo de código subyacente, debe:

1. Recuperar el [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propiedad de valor y convertirlo en una plataforma específica `NativeViewWrapper` tipo.
1. Recuperar el `NativeViewWrapper.NativeElement` propiedad y convertirlo en el tipo de vista nativo.

A continuación, se pueden invocar las API nativas en la vista nativa para realizar las operaciones deseadas. Este enfoque también ofrece la ventaja que varias vistas XAML nativas para diferentes plataformas pueden ser elementos secundarios del mismo [ `ContentView` ](xref:Xamarin.Forms.ContentView). En el ejemplo de código siguiente se muestra esta técnica:

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

En el ejemplo anterior, las vistas nativas para cada plataforma son elementos secundarios de [ `ContentView` ](xref:Xamarin.Forms.ContentView) controles, con el `x:Name` valor del atributo que se usa para recuperar el `ContentView` en el código subyacente:

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

El [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) acceso a la propiedad para recuperar la vista ajustada nativa como una plataforma específica `NativeViewWrapper` instancia. El `NativeViewWrapper.NativeElement` , a continuación, se obtiene acceso de propiedad para recuperar la vista nativa como tipo nativo. A continuación, se invoca la API nativa de la vista para llevar a cabo las operaciones deseadas.

IOS y Android botones nativos comparten el mismo `OnButtonTap` controlador de eventos, porque cada botón nativo consume un `EventHandler` delegar en respuesta a un evento de toque. Sin embargo, la plataforma Universal de Windows (UWP) usa un independiente `RoutedEventHandler`, que a su vez los consume el `OnButtonTap` controlador de eventos en este ejemplo. Por lo tanto, cuando un botón nativo se hace clic en, el `OnButtonTap` ejecuta el controlador de eventos, que se escala y gira el control nativo dentro de la [ `ContentView` ](xref:Xamarin.Forms.ContentView) denominado `contentViewTextParent`. Las capturas de pantalla siguientes muestran este que se producen en cada plataforma:

![](xaml-images/contentview.png "ContentView que contiene un Control nativo")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Creación de subclases de vistas nativas

Muchos iOS y Android vistas nativas no son adecuadas para crear instancias en XAML porque usan métodos, en lugar de propiedades, para configurar el control. Vistas nativas de subclase de contenedores que definen una API compatible con XAML más que usa las propiedades para el control de la instalación, y que usa eventos independientes de la plataforma es la solución a este problema. El ajustada nativas vistas pueden, a continuación, se coloca en un proyecto de activos compartidos (SAP) y rodeadas con directivas de compilación condicional, o colocar en los proyectos específicos de la plataforma y de XAML al que hace referencia en un proyecto de biblioteca de .NET Standard.

En el ejemplo de código siguiente se muestra una página de Xamarin.Forms que consume una subclase de vistas nativas:

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

La página contiene un [ `Label` ](xref:Xamarin.Forms.Label) que muestra la fruta elegida por el usuario en un control nativo. El `Label` enlaza a la `SubclassedNativeControlsPageViewModel.SelectedFruit` propiedad. El [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en una nueva instancia de la `SubclassedNativeControlsPageViewModel` clase en el archivo de código subyacente, con la clase ViewModel que implementa el `INotifyPropertyChanged` interfaz.

La página también contiene una vista de selector nativo para cada plataforma. Cada vista nativa muestra la colección de frutas enlazando su `ItemSource` propiedad a la `SubclassedNativeControlsPageViewModel.Fruits` colección. Esto permite al usuario elegir una frutas, como se muestra en las capturas de pantalla siguiente:

![](xaml-images/sub-classed.png "Vistas nativas de subclases")

En iOS y Android los selectores nativos usar métodos para configurar los controles. Por lo tanto, deben ser una subclase estos selectores para exponer propiedades para que sean compatible con XAML. En la plataforma Universal de Windows (UWP), el `ComboBox` ya es compatible con XAML y, por lo que no requiere la creación de subclases.

### <a name="ios"></a>iOS

Las subclases de la implementación de iOS la [ `UIPickerView` ](xref:UIKit.UIPickerView) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

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

El `MyUIPickerView` clase expone `ItemsSource` y `SelectedItem` propiedades y un `SelectedItemChanged` eventos. Un [ `UIPickerView` ](xref:UIKit.UIPickerView) requiere una subyacente [ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel) data model, que obtiene acceso a la `MyUIPickerView` propiedades y eventos. El `UIPickerViewModel` proporciona el modelo de datos la `PickerModel` clase:

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

El `PickerModel` clase proporciona el almacenamiento subyacente para el `MyUIPickerView` (clase), a través de la `Items` propiedad. Cada vez que el elemento seleccionado en el `MyUIPickerView` cambios, el [ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*) se ejecuta el método, que actualiza el índice seleccionado y se activa el `ItemChanged` eventos. Esto garantiza que el `SelectedItem` propiedad siempre devolverá el último elemento seleccionado por el usuario. Además, el `PickerModel` clase invalida los métodos que se utilizan para configurar el `MyUIPickerView` instancia.

### <a name="android"></a>Android

Las subclases de implementación para Android el [ `Spinner` ](xref:Android.Widget.Spinner) vista y expone propiedades y un evento que se puede consumir fácilmente desde XAML:

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

El `MySpinner` clase expone `ItemsSource` y `SelectedObject` propiedades y un `ItemSelected` eventos. Los elementos mostrados por la `MySpinner` proporcionados clase por el [ `Adapter` ](xref:Android.Widget.Adapter) asociado a la vista y los elementos se rellenan en el `Adapter` cuando el `ItemsSource` propiedad se establece en primer lugar. Cada vez que el elemento seleccionado en el `MySpinner` clase cambios, el `OnBindableSpinnerItemSelected` actualizaciones del controlador de eventos el `SelectedObject` propiedad.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir vistas nativas en archivos XAML de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [NativeSwitch (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formularios nativos](~/xamarin-forms/platform/native-forms.md)
- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
