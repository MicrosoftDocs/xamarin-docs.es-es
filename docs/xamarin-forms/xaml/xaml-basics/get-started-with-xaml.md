---
title: Parte 1. Introducción a XAML
description: En una aplicación de Xamarin.Forms, XAML se utiliza principalmente para definir el contenido visual de una página y funciona junto con un archivo de código subyacente.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 085960b92aa0f349d4cb2737385e723d8b6aa6b7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656513"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introducción a XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_En una aplicación de Xamarin.Forms, se usa principalmente para definir el contenido visual de una página XAML y funciona junto con un C# archivo de código subyacente._

El archivo de código subyacente proporciona compatibilidad de código para el marcado. Juntos, estos dos archivos contribuyen a una nueva definición de clase que incluye vistas secundarias e inicialización de la propiedad. En el archivo XAML, se hace referencia a clases y propiedades con los elementos y atributos XML y se establecen vínculos entre el código y marcado.

## <a name="creating-the-solution"></a>Crear la solución

Para comenzar a editar el primer archivo XAML, utilice Visual Studio o Visual Studio para Mac para crear una nueva solución de Xamarin.Forms. (Seleccione la pestaña siguiente correspondiente a su entorno).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En Windows, use Visual Studio para seleccionar **archivo > Nuevo > proyecto** en el menú. En el **nuevo proyecto** cuadro de diálogo, seleccione **Visual C# > multiplataforma** a la izquierda y, a continuación, **aplicación móvil (Xamarin.Forms)** en la lista en el centro.

![](get-started-with-xaml-images/win/newprojectdialog.w157.png "Cuadro de diálogo nuevo proyecto")

Seleccione una ubicación para la solución, asígnele un nombre de **XamlSamples** (o lo que prefiere) y presione **Aceptar**.

En la siguiente pantalla, seleccione el **aplicación vacía** plantilla y la **.NET Standard** estrategia de uso compartido de código:

![](get-started-with-xaml-images/win/newcrossplatformapp.png "Cuadro de diálogo nueva aplicación")

Presione **Aceptar**.

Se crean cuatro proyectos en la solución: el **XamlSamples** biblioteca .NET Standard, **XamlSamples.Android**, **XamlSamples.iOS**y la plataforma Universal de Windows solución de **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, seleccione **archivo > nueva solución** en el menú. En el **nuevo proyecto** cuadro de diálogo, seleccione **multiplataforma > aplicación** a la izquierda, y **aplicación de Forms en blanco** (*no* **aplicación de formularios** ) en la lista de plantillas:

![](get-started-with-xaml-images/mac/newprojectdialog1.png "Nuevo cuadro de diálogo de proyecto 1")

Presione **siguiente**.

En el cuadro de diálogo siguiente, asigne al proyecto un nombre de **XamlSamples** (o lo que prefiera). Asegúrese de que el **uso .NET Standard** botón de radio está seleccionado:

![](get-started-with-xaml-images/mac/newprojectdialog2.png "Nuevo cuadro de diálogo de proyecto 2")

Presione **siguiente**.

En el cuadro de diálogo siguiente, puede seleccionar una ubicación para el proyecto:

![](get-started-with-xaml-images/mac/newprojectdialog3.png "Cuadro de diálogo de proyecto nuevo 3")

Presione **crear**

Se crean tres proyectos en la solución: el **XamlSamples** biblioteca .NET Standard, **XamlSamples.Android**, y **XamlSamples.iOS**.

-----

Después de crear el **XamlSamples** solución, desea probar el entorno de desarrollo seleccionando los diversos proyectos de plataforma como proyecto de inicio de la solución y crear e implementar la aplicación simple crean por la plantilla de proyecto en dispositivos reales o emuladores de teléfono.

A menos que necesite escribir código específico de plataforma, el recurso compartido **XamlSamples** proyecto de biblioteca de .NET Standard es donde va a pasar prácticamente todo su tiempo de programación. Estos artículos no se atreviesen fuera de ese proyecto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomía de un archivo XAML

Dentro de la **XamlSamples** biblioteca .NET Standard son un par de archivos con los nombres siguientes:

- **App.XAML**, el archivo XAML; y
- **App.Xaml.cs**, un C# *código* archivo asociado con el archivo XAML.

Necesitará hacer clic en la flecha situada junto a **App.xaml** para ver el archivo de código subyacente.

Ambos **App.xaml** y **App.xaml.cs** contribuyen a una clase denominada `App` que se deriva de `Application`. La mayoría de otras clases con archivos XAML contribuyen a una clase que derive de `ContentPage`; esos archivos usan XAML para definir el contenido visual de una página completa. Es el caso de los dos archivos en el **XamlSamples** proyecto:

- **MainPage.xaml**, el archivo XAML; y
- **MainPage.xaml.cs**, el C# archivo de código subyacente.

El **MainPage.xaml** archivo tiene este aspecto (aunque el formato puede ser un poco diferente):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

Las dos declaraciones de espacio`xmlns`de nombres XML () hacen referencia a los identificadores URI, el primero aparentemente en el sitio web de Xamarin y el segundo en Microsoft. No se moleste en comprobar qué esos URI que quiera. No hay nada. Son simplemente los URI que pertenecen a Xamarin y Microsoft, y básicamente funcionan como identificadores de versión.

La primera declaración de espacio de nombres XML significa que las etiquetas definidas en el archivo XAML con ningún prefijo hacen referencia a las clases de Xamarin.Forms, por ejemplo `ContentPage`. La segunda declaración de espacio de nombres define un prefijo de `x`. Esto sirve para varios elementos y atributos que son intrínsecos de XAML propio y que son compatibles con otras implementaciones de XAML. Sin embargo, estos elementos y atributos son ligeramente diferentes según el año incrustado en el URI. Xamarin.Forms es compatible con la especificación de XAML 2009, pero no todos del mismo.

El `local` declaración de espacio de nombres permite obtener acceso a otras clases desde el proyecto de biblioteca .NET Standard.

Al final de la primera etiqueta, el `x` prefijo se utiliza para un atributo denominado `Class`. Dado que el uso de este `x` prefijo es prácticamente universal para el espacio de nombres XAML, los atributos XAML, como `Class` casi siempre se conocen como `x:Class`.

El `x:Class` atributo especifica un nombre de clase de .NET completo: el `MainPage` clase en el `XamlSamples` espacio de nombres. Esto significa que este archivo XAML define una clase nueva denominada `MainPage` en el `XamlSamples` espacio de nombres que se deriva de `ContentPage`: la etiqueta en el que el `x:Class` atributo aparece.

El `x:Class` atributo solo puede aparecer en el elemento raíz de un archivo XAML para definir una derivada C# clase. Se trata de la clase solo nueva definida en el archivo XAML. Todo el contenido que aparece en el archivo XAML está en su lugar simplemente crear instancias de clases existentes e inicializado.

El **MainPage.xaml.cs** archivo tiene este aspecto (aparte de sin usar `using` directivas):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

El `MainPage` clase se deriva de `ContentPage`, pero tenga en cuenta el `partial` definición de clase. Esto sugiere que debe haber otra definición de clase parcial para `MainPage`, pero ¿dónde está? Y ¿qué es eso `InitializeComponent` método?

Cuando Visual Studio compila el proyecto, analiza el archivo XAML para generar un C# archivo de código. Si observa la **XamlSamples\XamlSamples\obj\Debug** directory, encontrará un archivo denominado **XamlSamples.MainPage.xaml.g.cs**. La "g" son las siglas de generadas. Se trata de la otra definición de clase parcial de `MainPage` que contiene la definición de la `InitializeComponent` método se llama desde el `MainPage` constructor. Estos dos parcial `MainPage` las definiciones de clase, a continuación, se pueden compilar juntas. Dependiendo de si el XAML se compila o no, el archivo XAML o en un formato binario del archivo XAML se incrusta en el archivo ejecutable.

En tiempo de ejecución de código en el proyecto de plataforma concreta llama a un `LoadApplication` método y pasa a él una nueva instancia de la `App` class en la biblioteca .NET Standard. El `App` crea una instancia de constructor de clase `MainPage`. El constructor de esa clase llama a `InitializeComponent`, que llama a la `LoadFromXaml` método que se extrae el archivo XAML (o el binario compilado) de la biblioteca .NET Standard. `LoadFromXaml` Inicializa todos los objetos definidos en el archivo XAML, se conectan todos juntos en las relaciones de elementos primarios y secundarios, adjunta los controladores de eventos definidos en el código para los eventos que se establece en el archivo XAML y establece el árbol resultante de objetos como el contenido de la página.

Aunque normalmente no es necesario pasar mucho tiempo con archivos de código generado, en ocasiones, en tiempo de ejecución se producen excepciones en código en los archivos generados, por lo que debe estar familiarizado con ellas.

Al compilar y ejecutar este programa, el `Label` elemento aparece en el centro de la página, tal como sugiere el XAML:

[![](get-started-with-xaml-images/xamlsamples.png "Predeterminado para mostrar de Xamarin.Forms")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "visualización predeterminada de Xamarin.Forms")

Para los objetos visuales más interesantes, todo lo que necesita es más interesante de XAML.

## <a name="adding-new-xaml-pages"></a>Agregar nuevas páginas XAML

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para agregar otros basada en XAML `ContentPage` clases al proyecto, seleccione el **XamlSamples** biblioteca estándar de .NET del proyecto e invocar el **proyecto > Agregar nuevo elemento** elemento de menú. En la izquierda de la **Agregar nuevo elemento** cuadro de diálogo, seleccione **Visual C#**  y **Xamarin.Forms**. En la lista seleccione **página de contenido** (no **página de contenido (C#)** , que crea una página de sólo código, o **vista contenido**, que no es una página). Asigne un nombre, por ejemplo, de la página **HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.w157.png "Agregar nuevo elemento de cuadro de diálogo")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para agregar otros basada en XAML `ContentPage` clases al proyecto, seleccione el **XamlSamples** biblioteca estándar de .NET del proyecto e invocar el **archivo > nuevo archivo** elemento de menú. En la izquierda de la **nuevo archivo** cuadro de diálogo, seleccione **formularios** a la izquierda, y **formularios ContentPage Xaml** (no **ContentPage de Forms**, que crea una página de sólo código, o **vista contenido**, que no es una página). Asigne un nombre, por ejemplo, de la página **HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "Cuadro de diálogo nuevo archivo")

-----

Se agregan dos archivos al proyecto, **HelloXamlPage.xaml** y el archivo de código subyacente **HelloXamlPage.xaml.cs**.

## <a name="setting-page-content"></a>Contenido de la página de configuración

Editar el **HelloXamlPage.xaml** archivo para que las etiquetas sola son aquellas de `ContentPage` y `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

El `ContentPage.Content` etiquetas forman parte de la sintaxis de XAML única. En primer lugar, es posible que parecen ser XML no válido, pero son válidas. El período no es un carácter especial en XML.

El `ContentPage.Content` se denominan etiquetas *property (elemento)* etiquetas. `Content` es una propiedad de `ContentPage`y generalmente se establece en una vista única o un diseño con vistas secundarias. Normalmente las propiedades se convierten en atributos en XAML, pero sería complicado establecer un `Content` atributo a un objeto complejo. Por ese motivo, la propiedad se expresa como un elemento XML formada por el nombre de clase y el nombre de propiedad separados por un punto. Ahora el `Content` propiedad puede establecerse entre el `ContentPage.Content` etiquetas, similar al siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Tenga en cuenta también que un `Title` se ha establecido el atributo en la etiqueta raíz.

En este momento, la relación entre las clases, las propiedades y el XML debe ser evidente: Una clase de Xamarin. Forms ( `ContentPage` como `Label`o) aparece en el archivo XAML como un elemento XML. Propiedades de esa clase, incluidas `Title` en `ContentPage` y siete propiedades de `Label`, suelen aparecer como atributos XML.

Existen muchos métodos abreviados para establecer los valores de estas propiedades. Algunas propiedades son tipos de datos básicos: Por ejemplo, las `Title` propiedades `Text` y son de tipo `String`, `Rotation` es de tipo `Double`y `IsVisible` (que es `true` de forma predeterminada y se establece aquí solo para la ilustración) es de `Boolean`tipo.

El `HorizontalTextAlignment` propiedad es de tipo `TextAlignment`, que es una enumeración. Para una propiedad de cualquier tipo de enumeración, todo lo que necesita la fuente de alimentación es un nombre de miembro.

Sin embargo, para las propiedades de tipos más complejos, los convertidores de tipos se utilizan para analizar el XAML. Estas son las clases de Xamarin.Forms que se derivan de `TypeConverter`. Muchos son las clases públicas, pero otras no. Para este archivo XAML en particular, varias de estas clases desempeñan un papel en segundo plano:

-  `LayoutOptionsConverter` para el `VerticalOptions` propiedad
-  `FontSizeConverter` para el `FontSize` propiedad
-  `ColorTypeConverter` para el `TextColor` propiedad

Estos convertidores rigen la sintaxis permitida de los valores de propiedad.

El `ThicknessTypeConverter` puede controlar uno, dos o cuatro números separados por comas. Si se proporciona un número, se aplica a los cuatro lados. Con dos números, el primero es left y right relleno y el segundo es la parte superior e inferior. Son cuatro números en el orden izquierda, superior, derecha e inferior.

El `LayoutOptionsConverter` puede convertir los nombres de campos estáticos públicos de la `LayoutOptions` a valores de tipo `LayoutOptions`.

El `FontSizeConverter` puede controlar un `NamedSize` miembro o un tamaño de fuente numérico.

El `ColorTypeConverter` acepta los nombres de campos estáticos públicos de la `Color` estructura o los valores RGB hexadecimales, con o sin un canal alfa, precedido por un signo de número (#). Esta es la sintaxis sin un canal alfa:

 `TextColor="#rrggbb"`

Cada una de las letras poco es un dígito hexadecimal. Aquí es cómo se incluye un canal alfa:

 `TextColor="#aarrggbb">`

Para el canal alfa, tenga en cuenta que es completamente opaco FF y 00 es completamente transparente.

Otros dos formatos le permiten especificar un único dígito hexadecimal para cada canal:

 `TextColor="#rgb"` `TextColor="#argb"`

En estos casos, el dígito se repite para formar el valor. Por ejemplo, #CF3 es el color RGB CC-FF-33.

## <a name="page-navigation"></a>Navegación de páginas

Al ejecutar el **XamlSamples** programa, el `MainPage` se muestra. Para ver el nuevo `HelloXamlPage` puede establecer que, como el inicio de la nueva página en el **App.xaml.cs** de archivos o vaya a la nueva página desde `MainPage`.

Para implementar la navegación, cambie primero el código de la **App.xaml.cs** constructor para que un `NavigationPage` se crea el objeto:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

En el **MainPage.xaml.cs** constructor, puede crear una sencilla `Button` y usar el controlador de eventos para navegar a `HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

Establecer el `Content` propiedad de la página reemplaza la configuración de la `Content` propiedad en el archivo XAML. Al compilar e implementar la nueva versión de este programa, aparece un botón en la pantalla. Al presionar navega a `HelloXamlPage`. Esta es la página resultante en el iPhone, Android y UWP:

[![](get-started-with-xaml-images/helloxaml1.png "Girar el texto de la etiqueta")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "etiqueta texto girado")

Puede navegar hasta `MainPage` utilizando el **< Atrás** botón en iOS, mediante la flecha izquierda en la parte superior de la página o en la parte inferior del teléfono en Android, o con la flecha izquierda en la parte superior de la página en Windows 10.

No dude en experimentar con el XAML para las distintas formas representar el `Label`. Si tiene que insertar caracteres Unicode en el texto, puede usar la sintaxis XML estándar. Por ejemplo, para poner el saludo en las comillas tipográficas, use:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Este es su aspecto:

[![](get-started-with-xaml-images/helloxaml2.png "Etiqueta de texto con caracteres Unicode girado")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "gira el texto de la etiqueta con caracteres Unicode")

## <a name="xaml-and-code-interactions"></a>Interacciones de código y XAML

El **HelloXamlPage** ejemplo contiene una sola `Label` en la página, pero esto es muy poco común. La mayoría `ContentPage` conjunto derivados del `Content` ordenar propiedad a un diseño de algunos, como un `StackLayout`. El `Children` propiedad de la `StackLayout` se define como de tipo `IList<View>` pero es realmente un objeto de tipo `ElementCollection<View>`, y que se puede rellenar la colección con varias vistas u otros esquemas. En XAML, estas relaciones de elementos primarios y secundarios se establecen con la jerarquía XML normal. Este es un archivo XAML para una nueva página denominada **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Este archivo XAML es sintácticamente completa, y este es su aspecto:

[![](get-started-with-xaml-images/xamlpluscode1.png "Varios controles en una página")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "varios controles en una página")

Sin embargo, es probable que observe este programa sea funcionalmente deficientes. Quizás el `Slider` se supone que debe hacer que el `Label` para mostrar el valor actual y el `Button` probablemente está pensado para hacer algo dentro del programa.

Como verá en [parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), el trabajo de mostrar un `Slider` valor mediante un `Label` puede controlarse completamente en XAML con un enlace de datos. Pero resulta útil ver primero la solución de código. Aun así, controlar la `Button` clic definitivamente requiere código. Esto significa que el archivo de código subyacente para `XamlPlusCodePage` debe contener controladores para la `ValueChanged` eventos de la `Slider` y `Clicked` eventos de la `Button`. Vamos a agregarlos:

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Estos controladores de eventos no es necesario ser público.

En el archivo XAML, el `Slider` y `Button` etiquetas deben incluir los atributos para el `ValueChanged` y `Clicked` eventos que hacen referencia a estos controladores:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Tenga en cuenta que la asignación de un controlador a un evento tiene la misma sintaxis que asignar un valor a una propiedad.

Si el controlador para el `ValueChanged` eventos de la `Slider` va a utilizar el `Label` para mostrar el valor actual, el controlador necesita hacer referencia a ese objeto desde el código. El `Label` necesita un nombre, que se especifica con el `x:Name` atributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

El `x` prefijo de la `x:Name` atributo indica que este atributo es intrínseco a XAML.

El nombre que asigne a la `x:Name` atributo tiene las mismas reglas que C# los nombres de variable. Por ejemplo, debe comenzar con una letra o un carácter de subrayado y no contener espacios incrustados.

Ahora el `ValueChanged` controlador de eventos puede establecer el `Label` para mostrar el nuevo `Slider` valor. El nuevo valor está disponible en los argumentos de evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

O bien, podría obtener el controlador de la `Slider` objeto que genera este evento desde el `sender` argumento y obtener el `Value` propiedad desde que:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Al ejecutar el programa, en primer lugar el `Label` no muestra el `Slider` valor porque el `ValueChanged` aún no ha desencadenado el evento. Pero cualquier manipulación de los `Slider` hace que el valor que se mostrará:

[![](get-started-with-xaml-images/xamlpluscode2.png "Valor del control deslizante muestra")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "valor del control deslizante muestra")

Ahora el `Button`. Vamos a simular una respuesta a un `Clicked` eventos mediante una alerta con el `Text` del botón. El controlador de eventos puede difundir con seguridad el `sender` argumento para un `Button` y, a continuación, obtener acceso a sus propiedades:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

El método se define como `async` porque el `DisplayAlert` método es asincrónico y debe ir precedido por el `await` operador, que se devuelve cuando se completa el método. Dado que este método obtiene la `Button` desencadenando el evento desde el `sender` argumento, el mismo controlador podría usarse para varios botones.

Ha visto que un objeto definido en XAML puede desencadenar un evento que se controla en el archivo de código subyacente y que el archivo de código subyacente puede tener acceso a un objeto definido en XAML con el nombre asignado a él con el `x:Name` atributo. Estas son las dos maneras fundamentales que interactúan de código y XAML.

Algunas características adicionales sobre cómo funciona el XAML se puede deducir examinando recién generado **XamlPlusCode.xaml.g.cs archivo**, que ahora incluye cualquier nombre asignado a cualquier `x:Name` atributo como un campo privado. Esta es una versión simplificada de ese archivo:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La declaración de este campo permite que la variable se usan libremente en cualquier lugar dentro de la `XamlPlusCodePage` archivo de clase parcial en su jurisdicción. En tiempo de ejecución, el campo se asigna después de que se ha analizado el XAML. Esto significa que el `valueLabel` campo es `null` cuando el `XamlPlusCodePage` constructor comienza válido, pero después `InitializeComponent` se llama.

Después de `InitializeComponent` devuelve el control vuelve al constructor, se han construido los objetos visuales de la página como si se tenía inicializados en el código y crea una instancia. El archivo XAML ya no desempeña ningún rol en la clase. Puede manipular estos objetos en la página de cualquier manera que desee, por ejemplo, mediante la adición de vistas para la `StackLayout`, o la configuración de la `Content` propiedad de la página a otra cosa completamente. Se puede "recorrer el árbol" examinando el `Content` propiedad de la página y los elementos de la `Children` colecciones de diseños. Puede establecer las propiedades de las vistas que se tiene acceso de esta forma, o controladores de eventos se les asignan dinámicamente.

No dude. Es la página y XAML es sólo una herramienta para crear su contenido.

## <a name="summary"></a>Resumen

Con esta introducción, ha visto cómo contribuir con un archivo XAML y el archivo de código para una definición de clase, y cómo interactúan los archivos XAML y código. Pero XAML también tiene sus propias características sintácticas únicas que le permiten que se usará de forma muy flexible. Puede empezar a explorar estos en [parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
