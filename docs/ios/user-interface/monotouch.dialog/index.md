---
title: Introducción a MonoTouch. Dialog para Xamarin. iOS
description: En este documento se describe MonoTouch. Dialog (MT. D), un marco para el desarrollo rápido y declarativo de la interfaz de usuario con Xamarin. iOS. En él se describe cómo usar las API de los cuadros de diálogo MonoTouch. para crear una interfaz en código o JSON y usar características como la extracción para actualizar, la búsqueda, la carga de imágenes de fondo, etc.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: cc7d966e7dd0371e967775d112789af9f0ad7c59
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528516"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introducción a MonoTouch. Dialog para Xamarin. iOS

MonoTouch. Dialog, conocido como MT. En Resumen, es un kit de herramientas de desarrollo de interfaz de usuario rápido que permite a los desarrolladores crear pantallas de aplicación y navegación con la información, en lugar de tediosas tareas de crear controladores de vista, tablas, etc. Como tal, proporciona una simplificación significativa del desarrollo de la interfaz de usuario y la reducción del código. Por ejemplo, considere la siguiente captura de pantalla:

 [![](images/image1.png "Por ejemplo, considere esta captura de pantalla")](images/image1.png#lightbox)

El código siguiente se usó para definir toda la pantalla:

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

Al trabajar con tablas en iOS, a menudo hay un montón de código redundante resulta.
Por ejemplo, cada vez que se necesita una tabla, se necesita un origen de datos para rellenar la tabla. En una aplicación que tiene dos pantallas basadas en tabla que se conectan a través de un controlador de navegación, cada pantalla comparte gran parte del mismo código.

Módulo. D simplifica esto al encapsular todo el código en una API genérica para la creación de tablas. A continuación, proporciona una abstracción sobre esa API que permite una sintaxis de enlace de objeto declarativo que facilita aún más la tarea. Como tal, hay dos API disponibles en MT. D

- **API de elementos de bajo nivel** : la *API de elementos* se basa en la creación de un árbol jerárquico de elementos que representan pantallas y sus componentes. La API Elements proporciona a los desarrolladores la mayor flexibilidad y control en la creación de interfaces de IU. Además, la API de elementos tiene compatibilidad avanzada con la definición declarativa a través de JSON, que permite una declaración increíblemente rápida, así como la generación dinámica de la interfaz de usuario desde un servidor. 
- **API de reflexión de alto nivel** : también conocido como el *enlace* *API* , en las clases que se anotan con sugerencias de interfaz de usuario y, a continuación, el destino maestro. D. crea las pantallas en función de los objetos y proporciona un enlace entre lo que se muestra (opcionalmente) en pantalla, modificar y el objeto subyacente de seguridad automáticamente. En el ejemplo anterior se ilustra el uso de la API de reflexión. Esta API no proporciona el control específico que la API de elementos hace, pero reduce aún más la complejidad, ya que genera automáticamente la jerarquía de elementos basada en atributos de clase. 


Módulo. D incluye un conjunto grande de elementos de interfaz de usuario integrados para la creación de pantalla, pero también reconoce la necesidad de elementos personalizados y diseños de pantalla avanzados. Como tal, la extensibilidad es una primera clase destacada en la API. Los desarrolladores pueden extender los elementos existentes o crear otros nuevos y, a continuación, integrarse sin problemas.

Además, MT. D tiene una serie de características comunes de la experiencia de usuario de iOS integradas, como la compatibilidad con "extraer para actualizar", la carga de imágenes asincrónica y la compatibilidad con búsquedas.

En este artículo se proporciona una visión completa del trabajo con MT. D, incluido:

- **MT. Componentes D** : esto se centrará en comprender las clases que componen MT. D para habilitar la puesta al día rápidamente. 
- **Referencia de elementos** : una lista completa de los elementos integrados de Mt. D. 
- **Uso avanzado** : incluye características avanzadas, como la extracción para la actualización, la búsqueda, la carga de imágenes de fondo, el uso de LINQ para compilar jerarquías de elementos y la creación de elementos, celdas y controladores personalizados para su uso con MT. D. 

## <a name="setting-up-mtd"></a>Configuración de MT. D

Módulo. D se distribuye con Xamarin. iOS. Para usarlo, haga clic con el botón derecho en el nodo **referencias** de un proyecto de Xamarin. iOS en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia al ensamblado **MonoTouch. Dialog-1** . A continuación, `using MonoTouch.Dialog` Agregue las instrucciones en el código fuente según sea necesario.

## <a name="understanding-the-pieces-of-mtd"></a>Descripción de los componentes de MT. D

Incluso cuando se usa la API de reflexión, MT. D crea una jerarquía de elementos bajo el capó, como si se hubiera creado directamente a través de la API Elements. Además, la compatibilidad de JSON mencionada en la sección anterior también crea elementos. Por esta razón, es importante tener un conocimiento básico de los componentes de MT. D.

Módulo. D crea pantallas con las cuatro partes siguientes:

- **DialogViewController**
- **RootElement**
- **Sección**
- **Element**


### <a name="dialogviewcontroller"></a>DialogViewController

Un *DialogViewController*, o *DVC* para abreviar, hereda de `UITableViewController` y, por tanto, representa una pantalla con una tabla. DVCs se puede insertar en un controlador de navegación como un UITableViewController normal.

### <a name="rootelement"></a>RootElement

Un *RootElement* es el contenedor de nivel superior de los elementos que van a un DVC. Contiene secciones, que pueden contener elementos. RootElements no se representan; en su lugar, son simplemente contenedores de lo que realmente se representa. Se asigna un RootElement a un DVC y, a continuación, el DVC representa sus elementos secundarios.

### <a name="section"></a>Section

Una sección es un grupo de celdas de una tabla. Al igual que con una sección de tabla normal, puede tener opcionalmente un encabezado y un pie de página que pueden ser texto o incluso vistas personalizadas, como en la siguiente captura de pantalla:

 [![](images/image2.png "Al igual que con una sección de tabla normal, puede tener opcionalmente un encabezado y un pie de página que pueden ser texto o incluso vistas personalizadas, como en esta captura de pantalla.")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Un elemento representa una celda real de la tabla. Módulo. D incluye una amplia variedad de elementos que representan diferentes tipos de datos o entradas diferentes. Por ejemplo, las siguientes capturas de pantallas ilustran algunos de los elementos disponibles:

 [![](images/image3.png "Por ejemplo, en estas capturas de pantallas se muestran algunos de los elementos disponibles.")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Más información sobre secciones y RootElements

Ahora vamos a analizar RootElements y secciones con mayor detalle.

### <a name="rootelements"></a>RootElements

Se requiere al menos un RootElement para iniciar el proceso MonoTouch. Dialog.

Si un RootElement se inicializa con un valor de sección o elemento, este valor se usa para buscar un elemento secundario que proporcionará un resumen de la configuración, que se representa en el lado derecho de la pantalla. Por ejemplo, en la siguiente captura de pantalla se muestra una tabla a la izquierda con una celda que contiene el título de la pantalla de detalles a la derecha, "postre", junto con el valor del desierto seleccionado.

 [![](images/image4.png "Esta captura de pantalla muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postre, junto con el valor del desierto seleccionado")](images/image4.png#lightbox) [![](images/image5.png "esto captura de pantalla siguiente muestra una tabla de la izquierda con una celda que contiene el título de la pantalla de detalles de la derecha, postre, junto con el valor del desierto seleccionado")](images/image5.png#lightbox)

Los elementos raíz también se pueden usar dentro de las secciones para desencadenar la carga de una nueva página de configuración anidada, como se muestra anteriormente. Cuando se usa en este modo, el título proporcionado se usa mientras se representa dentro de una sección y también se usa como título de la subpágina. Por ejemplo:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

En el ejemplo anterior, cuando el usuario puntea en "postre", MonoTouch. Dialog creará una nueva página y navegará a ella con la raíz "postre" y tendrá un grupo de radio con tres valores.

En este ejemplo concreto, el grupo de radio seleccionará "chocolate pastel" en la sección "postre" porque pasamos el valor "2" al radiogrupo. Esto significa elegir el tercer elemento de la lista (índice cero).

La llamada al método Add o el C# uso de la sintaxis del inicializador 4 agrega secciones.
Los métodos INSERT se proporcionan para insertar secciones con una animación.

Si crea RootElement con una instancia de grupo (en lugar de un radiogrupo), el valor de Resumen de RootElement cuando se muestra en una sección será el recuento acumulado de todos los BooleanElements y CheckboxElements que tengan la misma clave que el valor Group. Key.

### <a name="sections"></a>Secciones

Las secciones se usan para agrupar elementos en la pantalla y son los únicos elementos secundarios directos válidos de RootElement. Las secciones pueden contener cualquiera de los elementos estándar, incluido el nuevo RootElements.

RootElements Embedded en una sección se utiliza para navegar a un nuevo nivel más profundo.

Las secciones pueden tener encabezados y pies de página como cadenas, o como UIViews.
Normalmente, solo se usarán las cadenas, pero para crear interfaces de usuario personalizadas puede usar cualquier UIView como el encabezado o el pie de página. Puede utilizar una cadena para crearlas de la siguiente manera:

```csharp
var section = new Section ("Header", "Footer");
```

Para usar vistas, solo tiene que pasar las vistas al constructor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>Recibir notificaciones

#### <a name="handling-nsaction"></a>Controlar NSAction

Módulo. D expone como un delegado para controlar las devoluciones de llamada. `NSAction`
Por ejemplo, suponga que desea controlar un evento Touch para una celda de tabla creada por MT. D. Al crear un elemento con MT. D, simplemente proporcione una función de devolución de llamada, como se muestra a continuación:

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperando valor del elemento

Combinado con la `Element.Value` propiedad, la devolución de llamada puede recuperar el valor establecido en otros elementos. Por ejemplo, considere el siguiente código:

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

Este código crea una interfaz de usuario, como se muestra a continuación. Para ver un tutorial completo de este ejemplo, consulte el tutorial sobre los elementos de la [API](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) .

 [![](images/image6.png "Combinado con la propiedad Element. Value, la devolución de llamada puede recuperar el valor establecido en otros elementos.")](images/image6.png#lightbox)

Cuando el usuario presiona la celda de la tabla inferior, se ejecuta el código de la función anónima, escribiendo el valor `element` de la instancia en el panel de salida de la **aplicación** en Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos integrados

Módulo. D incluye una serie de elementos de celda de tabla integrados conocidos como elementos.
Estos elementos se usan para mostrar una variedad de tipos diferentes en las celdas de la tabla, como cadenas, flotantes, fechas e incluso imágenes, por nombrar solo unos pocos. Cada elemento se encarga de mostrar el tipo de datos de forma adecuada. Por ejemplo, un elemento booleano mostrará un modificador para alternar su valor. Del mismo modo, un elemento Float mostrará un control deslizante para cambiar el valor de punto flotante.

Hay incluso elementos más complejos para admitir tipos de datos más completos como imágenes y HTML. Por ejemplo, un elemento HTML, que abrirá un UIWebView para cargar una página web cuando se selecciona, muestra un título en la celda de la tabla.

### <a name="working-with-element-values"></a>Trabajar con valores de elemento

Los elementos que se usan para capturar datos proporcionados por `Value` el usuario exponen una propiedad pública que contiene el valor actual del elemento en cualquier momento. Se actualiza automáticamente a medida que el usuario usa la aplicación.

Este es el comportamiento de todos los elementos que forman parte de MonoTouch. Dialog, pero no es necesario para los elementos creados por el usuario.

### <a name="string-element"></a>Elemento String

Un `StringElement` muestra un título en el lado izquierdo de una celda de la tabla y el valor de cadena en el lado derecho de la celda.

 [![](images/image7.png "Un StringElement muestra un título en el lado izquierdo de una celda de la tabla y el valor de cadena en el lado derecho de la celda.")](images/image7.png#lightbox)

Para usar un `StringElement` como botón, proporcione un delegado.

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![](images/image8.png "Para usar un StringElement como botón, proporcione un delegado")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento de cadena con estilo

`StyledStringElement` Permite presentar cadenas mediante estilos de celda de tabla integrados o con un formato personalizado.

 [![](images/image9.png "Un StyledStringElement permite presentar cadenas mediante estilos de celda de tabla integrados o con formato personalizado.")](images/image9.png#lightbox)

La `StyledStringElement` clase se deriva de `StringElement`, pero permite a los desarrolladores personalizar unas cuantas propiedades como la fuente, el color del texto, el color de la celda de fondo, el modo de salto de línea, el número de líneas que se van a mostrar y si se debe mostrar un accesorio.

### <a name="multiline-element"></a>Elemento Multiline

 [![](images/image10.png "Elemento Multiline")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento entry

`EntryElement`, Como el nombre implica, se usa para obtener los datos proporcionados por el usuario. Admite cadenas normales o contraseñas, donde los caracteres están ocultos.

 [![](images/image11.png "EntryElement se usa para obtener la entrada del usuario")](images/image11.png#lightbox)

Se inicializa con tres valores:

- Título de la entrada que se va a mostrar al usuario.
- Texto de marcador de posición (este es el texto atenuado que proporciona una sugerencia al usuario). 
- Valor del texto.


El marcador de posición y el valor pueden ser null. Sin embargo, el título es obligatorio.

En cualquier momento, el acceso a su propiedad Value puede recuperar el valor de `EntryElement`.

Además, `KeyboardType` la propiedad se puede establecer en el momento de la creación en el estilo de tipo de teclado deseado para la entrada de datos. Se puede usar para configurar el teclado usando los valores de `UIKeyboardType` que se enumeran a continuación:

- Numeric
- Teléfono
- URL
- Email


### <a name="boolean-element"></a>Elemento Boolean

 [![](images/image12.png "Elemento Boolean")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>CheckBox, elemento

 [![](images/image13.png "CheckBox, elemento")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento radio

Un `RadioElement` requiere`RootElement`que se especifique en. `RadioGroup`

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![](images/image14.png "Un radioelemento requiere que se especifique un radiogrupo en el RootElement")](images/image14.png#lightbox)

 `RootElements`también se usan para coordinar los elementos de radio. Los `RadioElement` miembros pueden abarcar varias secciones (por ejemplo, para implementar algo similar al selector de tono de timbre y separar los tonos de timbre personalizados de los tonos del sistema). La vista de Resumen mostrará el elemento de radio que está seleccionado actualmente. Para utilizarlo, cree el `RootElement` con el constructor de grupo, de la siguiente manera:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

El nombre del grupo de `RadioGroup` se usa para mostrar el valor seleccionado en la página contenedora (si existe) y el valor, que es cero en este caso, es el índice del primer elemento seleccionado.

### <a name="badge-element"></a>Elemento de distintivo

 [![](images/image15.png "Elemento de distintivo")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento Float

 [![](images/image16.png "Elemento Float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Activity, elemento

 [![](images/image17.png "Activity, elemento")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento Date

 ![](images/image18.png "Elemento Date")

Cuando se selecciona la celda correspondiente a DateElement, se presenta un selector de fecha, como se muestra a continuación:

 [![](images/image19.png "Cuando se selecciona la celda correspondiente a DateElement, se presenta un selector de fecha como se muestra")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![](images/image20.png "Elemento Time")](images/image20.png#lightbox)

Cuando se selecciona la celda correspondiente a TimeElement, se presenta un selector de hora como se muestra a continuación:

 [![](images/image21.png "Cuando se selecciona la celda correspondiente a TimeElement, se presenta un selector de hora como se muestra")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento DateTime

 [![](images/image22.png "Elemento DateTime")](images/image22.png#lightbox)

Cuando se selecciona la celda correspondiente a DateTimeElement, se presenta un selector de fecha y hora, como se muestra a continuación:

 [![](images/image23.png "Cuando se selecciona la celda correspondiente a DateTimeElement, se presenta un selector de fecha y hora como se muestra")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

Muestra el valor de su `Caption` propiedad en la celda de la tabla. `HTMLElement` Al seleccionado, el `Url` asignado al elemento se carga en un `UIWebView` control, tal y como se muestra a continuación:

 [![](images/image25.png "Al seleccionado, la dirección URL asignada al elemento se carga en un control UIWebView, como se muestra a continuación")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento message

 [![](images/image26.png "Elemento message")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Cargar más elemento

Use este elemento para permitir que los usuarios carguen más elementos en la lista. Puede personalizar los subtítulos normales y de carga, así como la fuente y el color del texto.
El `UIActivity` indicador comienza a animar y el título de carga se muestra cuando un usuario pulsa la celda y, `NSAction` a continuación, se ejecuta el pasado en el constructor. Una vez finalizado el `NSAction` código de, el `UIActivity` indicador detiene la animación y se vuelve a mostrar el título normal.

### <a name="uiview-element"></a>Elemento UIView

Además, cualquier personalizado `UIView` se puede mostrar `UIViewElement`mediante.

### <a name="owner-drawn-element"></a>Elemento dibujado por el propietario

Este elemento debe ser de subclase, ya que es una clase abstracta. Debe reemplazar el `Height(RectangleF bounds)` método en el que debe devolver el alto del elemento, así como `Draw(RectangleF bounds, CGContext context, UIView view)` en el que debe hacer todo el dibujo personalizado dentro de los límites especificados, mediante los parámetros de contexto y vista. Este elemento realiza el pesado trabajo de subclases `UIView`y la coloca en la celda que se va a devolver, lo que solo necesita implementar dos invalidaciones simples. Puede ver una mejor implementación de ejemplo en la aplicación de ejemplo en `DemoOwnerDrawnElement.cs` el archivo.

Este es un ejemplo muy sencillo de implementación de la clase:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
}
```

### <a name="json-element"></a>Elemento JSON

Es una subclase de `RootElement` que extiende un `RootElement` para poder cargar el contenido de un elemento secundario anidado desde una dirección URL local o remota. `JsonElement`

Es `JsonElement` una`RootElement` instancia de con la que se pueden crear instancias de dos formas. Una versión crea un `RootElement` que cargará el contenido a petición. Estos se crean mediante los `JsonElement` constructores que toman un argumento adicional al final, la dirección URL de la que se va a cargar el contenido:

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

El otro formulario crea los datos a partir de un archivo local o `System.Json.JsonObject` de un existente que ya ha analizado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obtener más información sobre el uso de JSON con MT. D, vea el tutorial del [tutorial de elementos JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) .

## <a name="other-features"></a>Otras características

### <a name="pull-to-refresh-support"></a>Compatibilidad con la incorporación de cambios a la actualización

 *Extracción a* La *actualización* es un efecto visual encontrado originalmente en la aplicación *Tweetie2* , que se convirtió en un efecto popular entre muchas aplicaciones.

Para agregar compatibilidad automática de extracción a actualización a los cuadros de diálogo, solo tiene que hacer dos cosas: enlazar un controlador de eventos para recibir una notificación cuando el usuario extrae los datos y notificar `DialogViewController` a cuando se han cargado los datos para volver a su estado predeterminado.

Enlazar una notificación es sencillo. simplemente Conéctese al `RefreshRequested` evento en, `DialogViewController`de la siguiente manera:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Después, en el `OnUserRequestedRefresh`método, se pone en cola algunos datos, se solicitan algunos datos de la red o se gira un subproceso para calcular los datos. Una vez `DialogViewController` que se han cargado los datos, debe notificar al que se encuentra el nuevo dato y restaurar la vista a su estado predeterminado; para ello, llame `ReloadComplete`a:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Buscar soporte técnico

Para admitir la búsqueda, establezca `EnableSearch` la propiedad `DialogViewController`en. También puede establecer la `SearchPlaceholder` propiedad que se va a usar como texto de marca de agua en la barra de búsqueda.

La búsqueda cambiará el contenido de la vista a medida que el usuario escribe. Busca en los campos visibles y los muestra al usuario. `DialogViewController` Expone tres métodos para iniciar, terminar o desencadenar una nueva operación de filtro en los resultados mediante programación. Estos métodos se enumeran a continuación:

- `StartSearch`
- `FinishSearch`
- `PerformFilter`


El sistema es extensible, por lo que puede modificar este comportamiento si lo desea.

### <a name="background-image-loading"></a>Carga de la imagen de fondo

MonoTouch. Dialog incorpora el cargador de imágenes de la aplicación [TweetStation](https://github.com/migueldeicaza/TweetStation) . Este cargador de imágenes se puede usar para cargar imágenes en segundo plano, admite el almacenamiento en caché y puede notificar el código cuando se carga la imagen.

También limitará el número de conexiones de red salientes.

El cargador de imágenes se implementa en `ImageLoader` la clase, lo único que debe hacer es llamar `DefaultRequestImage` al método, deberá proporcionar el URI de la imagen que desea cargar, así como una instancia de la `IImageUpdated` interfaz que se invocará cuando la ha de la imagen. s cargados.

Por ejemplo, el código siguiente carga una imagen de una dirección URL `BadgeElement`en un:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

La clase ImageLoader expone un método de purga al que se puede llamar cuando se desea liberar todas las imágenes que están actualmente almacenadas en la memoria caché. El código actual tiene una memoria caché para 50 imágenes. Si desea usar un tamaño de caché diferente (por ejemplo, si espera que las imágenes sean demasiado grandes para que las imágenes de 50 sean demasiado grandes), puede crear instancias de ImageLoader y pasar el número de imágenes que desea conservar en la memoria caché.

## <a name="using-linq-to-create-element-hierarchy"></a>Usar LINQ para crear la jerarquía de elementos

Mediante el uso de Clever de LINQ C#y la sintaxis de inicialización, LINQ se puede usar para crear una jerarquía de elementos. Por ejemplo, el código siguiente crea una pantalla a partir de algunas matrices de cadenas y controla la selección de celdas a través de una función `StringElement`anónima que se pasa a cada una de ellas:

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

Esto puede combinarse fácilmente con un almacén de datos XML o con datos de una base de datos para crear aplicaciones complejas prácticamente por completo de los datos.

## <a name="extending-mtd"></a>Extensión de MT. D

### <a name="creating-custom-elements"></a>Crear elementos personalizados

Puede crear su propio elemento heredando de un elemento existente o derivando del elemento de la clase raíz.

Para crear su propio elemento, querrá invalidar los métodos siguientes:

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

Si el elemento puede tener un tamaño variable, debe implementar la `IElementSizing` interfaz, que contiene un método:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Si planea implementar el `GetCell` método `base.GetCell(tv)` llamando a y personalizando la celda devuelta, también debe invalidar la `CellKey` propiedad para devolver una clave que será única para el elemento, como la siguiente:

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

Esto funciona con la mayoría de los elementos, pero `StringElement` no `StyledStringElement` con y, ya que usan su propio conjunto de claves para varios escenarios de representación. Tendría que replicar el código en esas clases.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

Tanto la API de reflexión como la de elementos usan `DialogViewController`el mismo. A veces, querrá personalizar el aspecto de la vista o puede que desee usar algunas características de `UITableViewController` que van más allá de la creación básica de interfaces de usuario.

Es simplemente una subclase `UITableViewController` de y puede personalizarla de la misma manera que personalizaría un `UITableViewController`. `DialogViewController`

Por ejemplo, si desea cambiar el estilo de lista para `Grouped` que sea o `Plain`, puede establecer este valor cambiando la propiedad al crear el controlador, como se muestra a continuación:

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

En el caso de las `DialogViewController`personalizaciones más avanzadas de, como establecer su fondo, la subclase y la invalidación de los métodos adecuados, tal como se muestra en el ejemplo siguiente:

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

Otro punto de personalización son los siguientes métodos virtuales en `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Este método debe devolver una subclase de `DialogViewController.Source` para los casos en los que las celdas tengan un tamaño uniforme o una `DialogViewController.SizingSource` subclase de si las celdas son desiguales.

Puede usar esta invalidación para capturar cualquiera de los `UITableViewSource` métodos. Por ejemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) lo utiliza para realizar un seguimiento cuando el usuario se ha desplazado a la parte superior y se actualiza en consecuencia el número de tweets no leídos.

## <a name="validation"></a>Validación

Los elementos no proporcionan la validación por sí mismos, ya que los modelos adecuados para páginas web y aplicaciones de escritorio no se asignan directamente al modelo de interacción de iPhone.

Si desea realizar la validación de datos, debe hacerlo cuando el usuario desencadene una acción con los datos especificados. Por ejemplo, un botón **listo** o **siguiente** en la barra de herramientas superior `StringElement` , o algunos usados como un botón para ir a la siguiente fase.

Aquí es donde llevaría a cabo la validación de entrada básica y quizás una validación más complicada como la comprobación de la validez de una combinación de usuario/contraseña con un servidor.

La forma de notificar un error al usuario es específica de la aplicación. Puede mostrar un `UIAlertView` o mostrar una sugerencia.

## <a name="summary"></a>Resumen

En este artículo se ha tratado mucha información sobre MonoTouch. Dialog. Se han explicado los aspectos básicos del funcionamiento de MT. D funciona y se han tratado los diversos componentes que conforman MT. D. También se ha mostrado la amplia gama de elementos y personalizaciones de tablas admitidas por MT. D y se describe cómo MT. D se puede extender con elementos personalizados. Además, se ha explicado la compatibilidad de JSON en MT. D que permite la creación dinámica de elementos desde JSON.


## <a name="related-links"></a>Vínculos relacionados

- [Tutorial: creación de una aplicación con Elements API](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial: creación de una aplicación con Reflection API](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial: Usar un elemento JSON para crear una interfaz de usuario](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Marcado JSON MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo MonoTouch en github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referencia de la clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de la clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
