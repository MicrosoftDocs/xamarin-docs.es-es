---
title: Parte 1. Introducción a XAML
description: En una Xamarin.Forms aplicación, XAML se usa principalmente para definir el contenido visual de una página y funciona junto con un archivo de código subyacente.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8992f2a649b1d3998a24a11cdf7e51d768a3cd09
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371344"
---
# <a name="part-1-getting-started-with-xaml"></a>Parte 1. Introducción a XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_En una Xamarin.Forms aplicación, XAML se usa principalmente para definir el contenido visual de una página y funciona junto con un archivo de código subyacente de C#._

El archivo de código subyacente proporciona compatibilidad de código para el marcado. Juntos, estos dos archivos contribuyen a una nueva definición de clase que incluye vistas secundarias e inicialización de propiedades. En el archivo XAML, se hace referencia a las clases y propiedades con atributos y elementos XML, y se establecen vínculos entre el marcado y el código.

## <a name="creating-the-solution"></a>Crear la solución

Para empezar a editar el primer archivo XAML, use Visual Studio o Visual Studio para Mac para crear una nueva Xamarin.Forms solución. (Seleccione la pestaña siguiente correspondiente a su entorno).

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Windows, inicie Visual Studio 2019 y, en la ventana Inicio, haga clic en **crear un nuevo proyecto** para crear un nuevo proyecto:

![Ventana nueva solución](get-started-with-xaml-images/win/new-solution-2019.png)

En la ventana **Crear un proyecto nuevo** , seleccione **Móvil** en la lista desplegable **Tipo de proyecto** , elija la plantilla **Aplicación móvil (Xamarin.Forms)** y haga clic en el botón **Siguiente** :

![Ventana nuevo proyecto](get-started-with-xaml-images/win/new-project-2019.png)

En la ventana **configurar el nuevo proyecto** , establezca el **nombre del proyecto** en **XamlSamples** (o el que prefiera) y haga clic en el botón **crear** .

En el cuadro de diálogo **nueva aplicación multiplataforma** , haga clic en **en blanco** y haga clic en el botón **Aceptar** :

![Cuadro de diálogo Nueva aplicación](get-started-with-xaml-images/win/new-cross-platform-app.png)

Se crean cuatro proyectos en la solución: **XamlSamples** .net Standard Library, **XamlSamples. Android** , **XamlSamples. iOS** y la solución plataforma universal de Windows, **XamlSamples. UWP**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, seleccione **archivo > nueva solución** en el menú. En el cuadro de diálogo **nuevo proyecto** , seleccione **aplicación multiplataforma >** a la izquierda y **aplicación de formularios en blanco** ( *no* **aplicación de formularios** ) en la lista de plantillas:

![Cuadro de diálogo nuevo proyecto 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Presione **siguiente**.

En el siguiente cuadro de diálogo, asigne al proyecto un nombre de **XamlSamples** (o el que prefiera). Asegúrese de que está seleccionado el botón de radio **usar .net Standard** :

![Cuadro de diálogo nuevo proyecto 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Presione **siguiente**.

En el siguiente cuadro de diálogo, puede seleccionar una ubicación para el proyecto:

![Cuadro de diálogo nuevo proyecto 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Presione **crear**

Se crean tres proyectos en la solución: **XamlSamples** .net Standard Library, **XamlSamples. Android** y **XamlSamples. iOS**.

-----

Después de crear la solución **XamlSamples** , es posible que desee probar el entorno de desarrollo seleccionando los distintos proyectos de plataforma como proyecto de inicio de la solución y compilando e implementando la aplicación simple creada por la plantilla de proyecto en los emuladores de teléfono o en dispositivos reales.

A menos que necesite escribir código específico de la plataforma, el proyecto Shared **XamlSamples** .net Standard Library es donde va a gastar prácticamente todo el tiempo de programación. Estos artículos no se tratarán fuera de ese proyecto.

### <a name="anatomy-of-a-xaml-file"></a>Anatomía de un archivo XAML

Dentro de la biblioteca de .NET Standard de **XamlSamples** hay un par de archivos con los nombres siguientes:

- **App. Xaml** , el archivo XAML; etc
- **App.Xaml.CS** , un archivo *de código subyacente* de C# asociado al archivo XAML.

Deberá hacer clic en la flecha situada junto a **app. Xaml** para ver el archivo de código subyacente.

Tanto **app. Xaml** como **app.Xaml.CS** contribuyen a una clase denominada `App` que se deriva de `Application` . La mayoría de las demás clases con archivos XAML contribuyen a una clase que deriva de `ContentPage` ; esos archivos usan XAML para definir el contenido visual de una página completa. Esto se aplica a los otros dos archivos del proyecto **XamlSamples** :

- **Mainpage. Xaml** , el archivo XAML; etc
- **Mainpage.Xaml.CS** , el archivo de código subyacente de C#.

El archivo **mainpage. Xaml** tiene el siguiente aspecto (aunque el formato puede ser un poco diferente):

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

Las dos declaraciones de espacio de nombres XML ( `xmlns` ) hacen referencia a los identificadores URI, el primero aparentemente en el sitio web de Xamarin y el segundo en Microsoft. No moleste a comprobar a qué apuntan los URI. No hay nada. Son simplemente los URI que pertenecen a Xamarin y Microsoft, y básicamente funcionan como identificadores de versión.

La primera declaración de espacio de nombres XML significa que las etiquetas definidas dentro del archivo XAML sin prefijo hacen referencia a las clases de Xamarin.Forms , por ejemplo `ContentPage` . La segunda declaración de espacio de nombres define un prefijo de `x` . Se usa para varios elementos y atributos que son intrínsecos al propio XAML y que son compatibles con otras implementaciones de XAML. Sin embargo, estos elementos y atributos son ligeramente diferentes en función del año incrustado en el URI. Xamarin.Forms admite la especificación XAML 2009, pero no todo.

La `local` declaración del espacio de nombres permite tener acceso a otras clases desde el proyecto de biblioteca de .net Standard.

Al final de la primera etiqueta, `x` se usa el prefijo para un atributo denominado `Class` . Dado que el uso de este `x` prefijo es prácticamente universal para el espacio de nombres XAML, los atributos XAML como `Class` se conocen casi siempre como `x:Class` .

El `x:Class` atributo especifica un nombre de clase .net completo: la `MainPage` clase en el `XamlSamples` espacio de nombres. Esto significa que este archivo XAML define una nueva clase denominada `MainPage` en el `XamlSamples` espacio de nombres que se deriva de `ContentPage` , la etiqueta en la que `x:Class` aparece el atributo.

El `x:Class` atributo solo puede aparecer en el elemento raíz de un archivo XAML para definir una clase de C# derivada. Esta es la única clase nueva que se define en el archivo XAML. Todo lo demás que aparece en el archivo XAML se crea simplemente con instancias de las clases existentes y se inicializa.

El archivo **mainpage.Xaml.CS** tiene el siguiente aspecto (aparte de las `using` directivas no utilizadas):

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

La `MainPage` clase se deriva de `ContentPage` , pero Fíjese en la `partial` definición de clase. Esto sugiere que debería haber otra definición de clase parcial para `MainPage` , pero donde es. ¿Y qué es ese `InitializeComponent` método?

Cuando Visual Studio compila el proyecto, analiza el archivo XAML para generar un archivo de código de C#. Si busca en el directorio **XamlSamples\XamlSamples\obj\Debug** , encontrará un archivo denominado **XamlSamples.mainpage.Xaml.g.CS**. ' G ' significa que se ha generado. Esta es la otra definición de clase parcial de `MainPage` que contiene la definición del `InitializeComponent` método al que se llama desde el `MainPage` constructor. Estas dos `MainPage` definiciones de clase parcial se pueden compilar a su vez. En función de si el XAML está compilado o no, el archivo XAML o un formato binario del archivo XAML se incrustan en el ejecutable.

En tiempo de ejecución, el código del proyecto de plataforma concreto llama a un `LoadApplication` método, pasándole una nueva instancia de la `App` clase en la biblioteca de .net Standard. `App`Crea instancias del constructor de clase `MainPage` . El constructor de esa clase llama `InitializeComponent` a, que después llama al `LoadFromXaml` método que extrae el archivo XAML (o su binario compilado) de la biblioteca de .net Standard. `LoadFromXaml` Inicializa todos los objetos definidos en el archivo XAML, los conecta todos juntos en las relaciones de elementos primarios y secundarios, asocia los controladores de eventos definidos en el código a los eventos establecidos en el archivo XAML y establece el árbol resultante de objetos como el contenido de la página.

Aunque normalmente no es necesario dedicar mucho tiempo a los archivos de código generados, a veces se producen excepciones en tiempo de ejecución en el código de los archivos generados, por lo que debe estar familiarizado con ellos.

Al compilar y ejecutar este programa, el `Label` elemento aparece en el centro de la página, como sugiere el XAML:

[![Default::: no-LOC (Xamarin. Forms)::: display](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Para los objetos visuales más interesantes, lo único que necesita es XAML más interesante.

## <a name="adding-new-xaml-pages"></a>Agregar nuevas páginas XAML

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para agregar otras clases basadas en XAML `ContentPage` al proyecto, seleccione el proyecto de biblioteca **XamlSamples** .net Standard, haga clic con el botón derecho y seleccione **Agregar > nuevo elemento..**.. En el cuadro de diálogo **Agregar nuevo elemento** , seleccione **elementos de Visual C# > Xamarin.Forms página de contenido de >** (no página de **contenido (C#)** , que crea una página de solo código, o una **vista de contenido** , que no es una página). Asigne un nombre a la página, por ejemplo, **HelloXamlPage** :

![Cuadro de diálogo Agregar nuevo elemento](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para agregar otras clases basadas en XAML `ContentPage` al proyecto, seleccione el proyecto de biblioteca **XamlSamples** .net Standard e invoque el elemento de menú **archivo > nuevo archivo** . A la izquierda del cuadro de diálogo **nuevo archivo** , seleccione **formularios** a la izquierda y **Forms ContentPage XAML** (no **Forms ContentPage** , que crea una página de solo código, o una **vista de contenido** , que no es una página). Asigne un nombre a la página, por ejemplo, **HelloXamlPage** :

![Cuadro de diálogo Nuevo archivo](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Dos archivos se agregan al proyecto, **HelloXamlPage. Xaml** y el archivo de código subyacente **HelloXamlPage.Xaml.CS**.

## <a name="setting-page-content"></a>Establecer el contenido de la página

Edite el archivo **HelloXamlPage. Xaml** para que las únicas etiquetas sean las `ContentPage` de `ContentPage.Content` y:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

Las `ContentPage.Content` etiquetas forman parte de la sintaxis única de XAML. En primer lugar, es posible que parezca que el código XML no es válido, pero son válidos. El punto no es un carácter especial en XML.

Las `ContentPage.Content` etiquetas se denominan etiquetas de *elemento de propiedad* . `Content` es una propiedad de `ContentPage` y, generalmente, se establece en una vista única o un diseño con vistas secundarias. Normalmente, las propiedades se convierten en atributos en XAML, pero sería difícil establecer un `Content` atributo en un objeto complejo. Por ese motivo, la propiedad se expresa como un elemento XML que se compone del nombre de la clase y el nombre de la propiedad separados por un punto. Ahora la `Content` propiedad se puede establecer entre las `ContentPage.Content` etiquetas, de la siguiente manera:

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

Observe también que se ha `Title` establecido un atributo en la etiqueta raíz.

En este momento, la relación entre las clases, las propiedades y el XML debe ser evidente: una Xamarin.Forms clase (como `ContentPage` o `Label` ) aparece en el archivo XAML como un elemento XML. Las propiedades de esa clase, `Title` incluidas `ContentPage` las propiedades en y siete, `Label` normalmente aparecen como atributos XML.

Existen muchos métodos abreviados para establecer los valores de estas propiedades. Algunas propiedades son tipos de datos básicos: por ejemplo, `Title` las `Text` propiedades y son de tipo, `String` `Rotation` es de tipo `Double` y `IsVisible` (que es de `true` forma predeterminada y se establece aquí solo para la ilustración) es de tipo `Boolean` .

La `HorizontalTextAlignment` propiedad es de tipo `TextAlignment` , que es una enumeración. Para una propiedad de cualquier tipo de enumeración, todo lo que necesita proporcionar es un nombre de miembro.

En el caso de las propiedades de tipos más complejos, sin embargo, los convertidores se utilizan para analizar el código XAML. Se trata de clases de Xamarin.Forms que derivan de `TypeConverter` . Muchos son clases públicas, pero otros no. Para este archivo XAML concreto, algunas de estas clases desempeñan un papel en segundo plano:

- `LayoutOptionsConverter` para la  `VerticalOptions` propiedad
- `FontSizeConverter` para la  `FontSize` propiedad
- `ColorTypeConverter` para la  `TextColor` propiedad

Estos convertidores rigen la sintaxis permitida de los valores de propiedad.

`ThicknessTypeConverter`Puede controlar uno, dos o cuatro números separados por comas. Si se proporciona un número, se aplica a los cuatro lados. Con dos números, el primero es el relleno izquierdo y derecho, y el segundo es superior e inferior. Hay cuatro números en el orden izquierdo, superior, derecho e inferior.

`LayoutOptionsConverter`Puede convertir los nombres de los campos estáticos públicos de la `LayoutOptions` estructura en valores de tipo `LayoutOptions` .

`FontSizeConverter`Puede controlar un `NamedSize` miembro o un tamaño de fuente numérico.

`ColorTypeConverter`Acepta los nombres de los campos estáticos públicos de la `Color` estructura o los valores RGB hexadecimales, con o sin canal alfa, precedidos por un signo de número (#). Esta es la sintaxis sin un canal alfa:

 `TextColor="#rrggbb"`

Cada una de las letras pequeñas es un dígito hexadecimal. Aquí se muestra cómo se incluye un canal alfa:

 `TextColor="#aarrggbb">`

En el canal alfa, tenga en cuenta que FF es totalmente opaco y 00 es completamente transparente.

Otros dos formatos permiten especificar un solo dígito hexadecimal para cada canal:

 `TextColor="#rgb"` `TextColor="#argb"`

En estos casos, el dígito se repite para formar el valor. Por ejemplo, #CF3 es el color RGB CC-FF-33.

## <a name="page-navigation"></a>Navegación de páginas

Al ejecutar el programa **XamlSamples** , `MainPage` se muestra. Para ver el nuevo, `HelloXamlPage` puede establecerlo como la nueva página de inicio en el archivo **app.Xaml.CS** o navegar a la nueva página desde `MainPage` .

Para implementar la navegación, primero cambie el código en el constructor **app.Xaml.CS** para que `NavigationPage` se cree un objeto:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

En el constructor **mainpage.Xaml.CS** , puede crear un sencillo `Button` y usar el controlador de eventos para navegar a `HelloXamlPage` :

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

Al establecer la `Content` propiedad de la página, se reemplaza el valor de la `Content` propiedad en el archivo XAML. Al compilar e implementar la nueva versión de este programa, aparece un botón en la pantalla. Al presionarlo se navega a `HelloXamlPage` . Esta es la página resultante en iPhone, Android y UWP:

[![Texto de etiqueta girado](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

Puede volver a `MainPage` usar el botón **atrás<** en iOS, con la flecha izquierda en la parte superior de la página o en la parte inferior del teléfono en Android, o mediante la flecha izquierda en la parte superior de la página en Windows 10.

No dude en experimentar con el código XAML para diferentes maneras de representar `Label` . Si necesita insertar caracteres Unicode en el texto, puede usar la sintaxis XML estándar. Por ejemplo, para poner el saludo en Comillas inteligentes, use:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Este es su aspecto:

[![Texto de etiqueta girado con caracteres Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>Interacciones de código y XAML

El ejemplo **HelloXamlPage** contiene solo un único `Label` en la página, pero esto es muy inusual. La mayoría `ContentPage` de los derivados establecen la `Content` propiedad en un diseño de algún tipo, como `StackLayout` . La `Children` propiedad de `StackLayout` se define como de tipo `IList<View>` , pero realmente es un objeto de tipo `ElementCollection<View>` y esa colección se puede rellenar con varias vistas u otros diseños. En XAML, estas relaciones de elementos primarios y secundarios se establecen con la jerarquía XML normal. A continuación se muestra un archivo XAML para una nueva página denominada **XamlPlusCodePage** :

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

Este archivo XAML se completa sintácticamente y este es el aspecto siguiente:

[![Varios controles en una página](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

Sin embargo, es probable que tenga en cuenta que este programa tiene un funcionamiento deficiente. Quizás `Slider` se supone que se debe hacer que `Label` muestre el valor actual y que, `Button` probablemente, haga algo en el programa.

Como verá en la [parte 4. Aspectos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), el trabajo de mostrar un `Slider` valor mediante un `Label` puede controlarse completamente en XAML con un enlace de datos. Pero es útil ver primero la solución de código. Incluso así, el control del `Button` clic requiere definitivamente el código. Esto significa que el archivo de código subyacente de `XamlPlusCodePage` debe contener Controladores para el `ValueChanged` evento de `Slider` y el `Clicked` evento de `Button` . Vamos a agregarlos:

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

No es necesario que estos controladores de eventos sean públicos.

De nuevo en el archivo XAML, `Slider` las `Button` etiquetas y deben incluir atributos para los `ValueChanged` `Clicked` eventos y que hacen referencia a estos controladores:

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

Si el controlador del `ValueChanged` evento de va `Slider` `Label` a usar para mostrar el valor actual, el controlador debe hacer referencia a ese objeto desde el código. `Label`Necesita un nombre, que se especifica con el `x:Name` atributo.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

El `x` prefijo del `x:Name` atributo indica que este atributo es intrínseco a XAML.

El nombre que asigna al `x:Name` atributo tiene las mismas reglas que los nombres de las variables de C#. Por ejemplo, debe empezar por una letra o un carácter de subrayado y no contener espacios incrustados.

Ahora, el `ValueChanged` controlador de eventos puede establecer `Label` para mostrar el nuevo `Slider` valor. El nuevo valor está disponible en los argumentos del evento:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

O bien, el controlador podría obtener el `Slider` objeto que genera este evento desde el `sender` argumento y obtener la `Value` propiedad de ese:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

La primera vez que se ejecuta el programa, el `Label` no muestra el `Slider` valor porque todavía no se `ValueChanged` ha desencadenado el evento. Sin embargo, cualquier manipulación de `Slider` hace que se muestre el valor:

[![Se muestra el valor del control deslizante](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Ahora para `Button` . Vamos a simular una respuesta a un `Clicked` evento mostrando una alerta con el `Text` del botón. El controlador de eventos puede convertir con seguridad el `sender` argumento en `Button` y, a continuación, tener acceso a sus propiedades:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

El método se define como `async` porque el `DisplayAlert` método es asincrónico y debe ir precedido por el `await` operador, que devuelve cuando se completa el método. Dado que este método obtiene la `Button` activación del evento desde el `sender` argumento, se podría usar el mismo controlador para varios botones.

Ha visto que un objeto definido en XAML puede desencadenar un evento que se administra en el archivo de código subyacente y que el archivo de código subyacente puede tener acceso a un objeto definido en XAML utilizando el nombre asignado a él con el `x:Name` atributo. Estas son las dos formas fundamentales en que interactúan el código y XAML.

Puede obtener información adicional sobre cómo funciona XAML examinando el **archivo XamlPlusCode.Xaml.g.CS** recién generado, que ahora incluye cualquier nombre asignado a cualquier `x:Name` atributo como campo privado. Esta es una versión simplificada de ese archivo:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La declaración de este campo permite que la variable se use libremente en cualquier parte del `XamlPlusCodePage` archivo de clase parcial bajo su jurisdicción. En tiempo de ejecución, el campo se asigna después de haber analizado el XAML. Esto significa que el `valueLabel` campo es `null` cuando se `XamlPlusCodePage` inicia el constructor, pero válido después de `InitializeComponent` llamar a.

Después de `InitializeComponent` devolver el control al constructor, los objetos visuales de la página se han construido como si se hubieran creado instancias e inicializadas en el código. El archivo XAML ya no juega ningún rol en la clase. Puede manipular estos objetos en la página de la forma que desee, por ejemplo, agregando vistas al `StackLayout` o estableciendo la `Content` propiedad de la página en otra cosa. Puede "recorrer el árbol" examinando la `Content` propiedad de la página y los elementos de las `Children` colecciones de diseños. Puede establecer propiedades en las vistas a las que se tiene acceso de esta manera, o bien asignarles los controladores de eventos de forma dinámica.

No dude. Es la página y XAML es solo una herramienta para compilar su contenido.

## <a name="summary"></a>Resumen

Con esta introducción, ha visto cómo un archivo XAML y un archivo de código contribuyen a una definición de clase, y cómo interactúan los archivos XAML y de código. Sin embargo, XAML también tiene sus propias características sintácticas únicas que permiten su uso de una manera muy flexible. Puede empezar a explorarlos en la [parte 2. Sintaxis básica de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 2. Sintaxis XAML básica](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Del enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)