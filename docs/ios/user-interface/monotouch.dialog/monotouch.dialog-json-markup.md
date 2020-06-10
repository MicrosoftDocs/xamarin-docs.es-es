---
title: Marcado JSON de MonoTouch.Dialog
description: En este documento se describe la sintaxis de JSON que se puede usar para compilar una interfaz de usuario de Xamarin. iOS mediante MonoTouch. Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: fc6066155a4171b106e772c1fe6fe7ee3e5c67cf
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573513"
---
# <a name="monotouchdialog-json-markup"></a>Marcado JSON de MonoTouch.Dialog

En esta página se describe el marcado JSON aceptado por el [JsonElement](xref:MonoTouch.Dialog.JsonElement) del cuadro de diálogo.

Comencemos con un ejemplo. El siguiente es un archivo JSON completo que se puede pasar a JsonElement.

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

El marcado anterior produce la siguiente interfaz de usuario:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "The UI created by the given markup")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento del árbol puede contener la propiedad `"id"` . En tiempo de ejecución es posible hacer referencia a secciones individuales o elementos mediante el indexador JsonElement. Por ejemplo:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax"></a>

## <a name="root-element-syntax"></a>Sintaxis del elemento raíz

El elemento raíz contiene los siguientes valores:

- `title`
- `sections` (opcional)

El elemento raíz puede aparecer dentro de una sección como elemento para crear un controlador anidado. En ese caso, la propiedad adicional `"type"` se debe establecer en`"root"`

 <a name="url"></a>

### <a name="url"></a>url

Si `"url"` se establece la propiedad, si el usuario pulsa en este RootElement, el código solicitará un archivo de la dirección URL especificada y hará que el contenido se muestre en la nueva información. Puede utilizar esto para crear una extensión de la interfaz de usuario del servidor en función de lo que el usuario pulse.

 <a name="group"></a>

### <a name="group"></a>group

Si se establece, establece el valor de GroupName para el elemento raíz. Los nombres de grupo se usan para elegir un resumen que se muestra como el valor del elemento raíz de uno de los elementos anidados del elemento. Este es el valor de una casilla o el valor de un botón de radio.

 <a name="radioselected"></a>

### <a name="radioselected"></a>radioseleccionada

Identifica el elemento de radio que está seleccionado en elementos anidados.

 <a name="title"></a>

### <a name="title"></a>título

Si está presente, será el título que se usa para el RootElement

 <a name="type"></a>

### <a name="type"></a>type

Debe establecerse en `"root"` cuando aparece en una sección (se usa para anidar controladores).

 <a name="sections"></a>

### <a name="sections"></a>secciones

Se trata de una matriz JSON con secciones individuales

 <a name="Section_Syntax"></a>

## <a name="section-syntax"></a>Sintaxis de la sección

La sección contiene:

- `header` (opcional)
- `footer` (opcional)
- Matriz `elements`

 <a name="header"></a>

### <a name="header"></a>header

Si está presente, el texto del encabezado se muestra como un título de la sección.

 <a name="footer"></a>

### <a name="footer"></a>pie de página

Si está presente, el pie de página se muestra en la parte inferior de la sección.

 <a name="elements"></a>

### <a name="elements"></a>Elementos

Se trata de una matriz de elementos. Cada elemento debe contener al menos una clave, la `"type"` clave que se utiliza para identificar el tipo de elemento que se va a crear.
Algunos de los elementos comparten algunas propiedades comunes como `"caption"` y `"value"` . Esta es la lista de elementos admitidos:

- `string`elementos (con y sin estilo)
- `entry`líneas (normal o contraseña)
- `boolean`valores (mediante modificadores o imágenes)

Los elementos de cadena se pueden usar como botones proporcionando un método para invocar cuando el usuario pulsa la celda o el accesorio,

 <a name="Rendering_Elements"></a>

## <a name="rendering-elements"></a>Representar elementos

Los elementos de representación se basan en C# StringElement y StyledStringElement y pueden representar la información de varias maneras y es posible representarlas de varias maneras. Los elementos más sencillos se pueden crear de la siguiente manera:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Esto mostrará una cadena simple con todos los valores predeterminados: fuente, Fondo, color del texto y decoraciones. Es posible enlazar acciones a estos elementos y hacer que se comporten como botones estableciendo la `"ontap"` propiedad o las `"onaccessorytap"` propiedades:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Lo anterior invocará el método "ShowPhotos" en la clase "Acme. fotolibrary". `"onaccessorytap"`Es similar, pero solo se invocará si el usuario pulsa el accesorio en lugar de puntear en la celda. Para habilitar esta configuración, también debe establecer el accesorio:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Los elementos de representación pueden mostrar dos cadenas a la vez, una es la leyenda y otra es el valor. La forma en que estas cadenas se representan dependen del estilo, puede establecerse con la `"style"` propiedad. El valor predeterminado mostrará el título a la izquierda y el valor a la derecha. Vea la sección sobre el estilo para obtener más detalles. Los colores se codifican con el símbolo "#" seguido de números hexadecimales que representan los valores de los valores rojo, verde, azul y quizás alfa. El contenido se puede codificar de forma abreviada (3 ó 4 dígitos hexadecimales) que representa valores RGB o RGBA. O el formato largo (6 u 8 dígitos) que representan los valores RGB o RGBA. La versión corta es una abreviatura para escribir el mismo dígito hexadecimal dos veces. Por lo tanto, la constante "#1bc" es interpretará en rojo = 0x11, verde = 0xbb y Blue = 0xcc. Si el valor alfa no está presente, el color es opaco. He aquí algunos ejemplos:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory"></a>

### <a name="accessory"></a>paquete

Determina el tipo de accesorio que se va a mostrar en el elemento de representación, los valores posibles son:

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

Si el valor no está presente, no se muestra ningún accesorio.

 <a name="background"></a>

### <a name="background"></a>background

La propiedad Background establece el color de fondo de la celda. El valor es una dirección URL a una imagen (en este caso, se invocará el descargador de imágenes asincrónicas y se actualizará el fondo una vez descargada la imagen) o puede ser un color especificado mediante la sintaxis de color.

 <a name="caption"></a>

### <a name="caption"></a>caption

Cadena principal que se va a mostrar en el elemento de representación. La fuente y el color se pueden personalizar mediante el establecimiento de las `"textcolor"` `"font"` propiedades y. La propiedad determina el estilo de representación `"style"` .

 <a name="color_and_detailcolor"></a>

### <a name="color-and-detailcolor"></a>color y detailcolor

Color que se va a usar para el texto principal o el texto detallado.

 <a name="detailfont_and_font"></a>

### <a name="detailfont-and-font"></a>detailfont y Font

Fuente que se va a usar para el título o el texto de detalle. El formato de una especificación de fuente es el nombre de fuente seguido opcionalmente por un guión y el tamaño del punto.
Las siguientes son especificaciones de fuente válidas:

- Helvetica
- "Helvetica-14"

 <a name="linebreak"></a>

### <a name="linebreak"></a>linebreak

Determina cómo se dividen las líneas. Los valores posibles son:

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

Tanto `character-wrap` como `word-wrap` se pueden utilizar junto con la `"lines"` propiedad establecida en cero para convertir el elemento de representación en un elemento de varias líneas.

 <a name="ontap_and_onaccessorytap"></a>

### <a name="ontap-and-onaccessorytap"></a>ONTAP y onaccessorytap

Estas propiedades deben apuntar a un nombre de método estático de la aplicación que toma un objeto como parámetro. Al crear la jerarquía mediante los métodos JsonDialog. FromFile o JsonDialog. FromJson, puede pasar un valor de objeto opcional. A continuación, este valor de objeto se pasa a los métodos. Puede utilizar esto para pasar algún contexto al método estático. Por ejemplo:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines"></a>

### <a name="lines"></a>lines

Si se establece en cero, hará que el tamaño del elemento sea automático según el contenido de las cadenas contenidas. Para que esto funcione, también debe establecer la `"linebreak"` propiedad en `"character-wrap"` o `"word-wrap"` .

 <a name="style"></a>

### <a name="style"></a>estilo

El estilo determina el tipo de estilo de celda que se utilizará para representar el contenido y se corresponden con los valores de la enumeración UITableViewCellStyle.
Los valores posibles son:

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`: texto con un subtítulo.

 <a name="subtitle"></a>

### <a name="subtitle"></a>subtitle

Valor que se va a usar para el subtítulo. Se trata de un acceso directo para establecer el estilo en `"subtitle"` y establecer la `"value"` propiedad en una cadena.
Esto hace ambas cosas con una sola entrada.

 <a name="textcolor"></a>

### <a name="textcolor"></a>TextColor

Color que se va a usar para el texto.

 <a name="value"></a>

### <a name="value"></a>value

Valor secundario que se va a mostrar en el elemento de representación. El diseño de esto se ve afectado por la `"style"` configuración de. La fuente y el color se pueden personalizar estableciendo `"detailfont"` y `"detailcolor"` .

 <a name="Boolean_Elements"></a>

## <a name="boolean-elements"></a>Elementos booleanos

Los elementos booleanos deben establecer el tipo en `"bool"` , pueden contener un `"caption"` para mostrar y el `"value"` está establecido en true o false. Si `"on"` `"off"` se establecen las propiedades y, se supone que son imágenes. Las imágenes se resuelven en relación con el directorio de trabajo actual de la aplicación. Si desea hacer referencia a los archivos relativos al paquete, puede usar `"~"` como un acceso directo para representar el directorio de la agrupación de aplicaciones. Por ejemplo `"~/favorite.png"` , será el archivo favorito. png incluido en el archivo de agrupación. Por ejemplo:

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type"></a>

### <a name="type"></a>type

El tipo se puede establecer en `"boolean"` o `"checkbox"` . Si se establece en booleano, utilizará un UISlider o imágenes (si `"on"` `"off"` se establecen ambos y). Si se establece en CheckBox, se usará una casilla. La `"group"` propiedad se puede utilizar para etiquetar un elemento booleano como perteneciente a un grupo determinado. Esto resulta útil si la raíz contenedora también tiene una `"group"` propiedad, ya que la raíz resumirá los resultados con un recuento de todos los booleanos (o casillas) que pertenecen al mismo grupo.

 <a name="Entry_Elements"></a>

## <a name="entry-elements"></a>Elementos entry

Los elementos de entrada se utilizan para permitir que el usuario escriba datos. El tipo de los elementos de entrada es `"entry"` o `"password"` . La `"caption"` propiedad se establece en el texto que se va a mostrar a la derecha y `"value"` se establece en el valor inicial para establecer la entrada en. `"placeholder"`Se usa para mostrar una sugerencia al usuario para las entradas vacías (aparece atenuada). Estos son algunos ejemplos:

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect"></a>

### <a name="autocorrect"></a>Autocorrección

Determina el estilo de corrección automática que se va a utilizar para la entrada. Los valores posibles son true o false (o las cadenas `"yes"` y `"no"` ).

 <a name="capitalization"></a>

### <a name="capitalization"></a>uso de mayúsculas

Estilo de uso de mayúsculas que se va a usar para la entrada. Los valores posibles son:

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption"></a>

### <a name="caption"></a>caption

Título que se va a usar para la entrada.

 <a name="keyboard"></a>

### <a name="keyboard"></a>teclado

Tipo de teclado que se va a usar para la entrada de datos. Los valores posibles son:

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder"></a>

### <a name="placeholder"></a>marcador de posición

El texto de sugerencia que se muestra cuando la entrada tiene un valor vacío.

 <a name="return-key"></a>

### <a name="return-key"></a>Return-Key

La etiqueta utilizada para la tecla RETURN. Los valores posibles son:

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value"></a>

### <a name="value"></a>value

Valor inicial de la entrada.

 <a name="Radio_Elements"></a>

## <a name="radio-elements"></a>Elementos de radio

Los elementos de radio tienen el tipo `"radio"` . La propiedad selecciona el elemento que está seleccionado `radioselected` en el elemento raíz que lo contiene.
Además, si se establece un valor para la `"group"` propiedad, este botón de radio pertenece a ese grupo.

 <a name="Date_and_Time_Elements"></a>

## <a name="date-and-time-elements"></a>Elementos de fecha y hora

Los tipos de elementos `"datetime"` , `"date"` y `"time"` se utilizan para representar fechas con horas, fechas u horas. Estos elementos toman como parámetros un título y un valor. El valor se puede escribir en cualquier formato admitido por la función DateTime. Parse de .NET. Ejemplo:

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element"></a>

## <a name="htmlweb-element"></a>HTML/elemento Web

Puede crear una celda que, cuando se Puntee, insertará un UIWebView que representa el contenido de una dirección URL especificada, ya sea local o remota con el `"html"` tipo. Las dos únicas propiedades de este elemento son `"caption"` y `"url"` :

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
