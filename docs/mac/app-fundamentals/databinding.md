---
title: Enlace de datos y codificación de valores de clave en Xamarin. Mac
description: En este artículo se describe el uso del código clave-valor y la observación del valor de clave para permitir el enlace de datos a los elementos de la interfaz de usuario en la Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 81a1f63078a5f7a2a70f731d1790f85f4283d22f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030219"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Enlace de datos y codificación de valores de clave en Xamarin. Mac

_En este artículo se describe el uso del código clave-valor y la observación del valor de clave para permitir el enlace de datos a los elementos de la interfaz de usuario en la Interface Builder de Xcode._

## <a name="overview"></a>Información general

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a las mismas técnicas de codificación de valores clave y de enlace de datos que un desarrollador que trabaja en *Objective-C* y *Xcode* . Dado que Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para enlazar datos con elementos de interfaz de usuario en lugar de escribir código.

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

[![Un ejemplo de la aplicación en ejecución](databinding-images/intro01.png "Un ejemplo de la aplicación en ejecución")](databinding-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con la codificación de valores clave y el enlace de datos en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican los atributos `Register` y `Export` que se usan para C# conectar las clases a Objective-C. objetos y elementos de la interfaz de usuario.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Qué es la codificación de valores de clave

La codificación de valores clave (KVC) es un mecanismo para tener acceso indirectamente a las propiedades de un objeto, mediante claves (cadenas con formato especial) para identificar propiedades en lugar de tener acceso a ellas a través de variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de los descriptores de acceso compatibles con el código de clave-valor en la aplicación de Xamarin. Mac, obtiene acceso a otras características de macOS (conocido anteriormente como OS X), como la observación de clave-valor (KVO), el enlace de datos, los enlaces de cacao y la creación de scripts.

Mediante el uso de la codificación de valores clave y técnicas de enlace de datos en la aplicación de Xamarin. Mac, puede reducir considerablemente la cantidad de código que debe escribir y mantener para rellenar y trabajar con los elementos de la interfaz de usuario. También tiene la ventaja de desacoplar aún más los datos de copia de seguridad (_modelo de datos_) de la interfaz de usuario de front-end (_Model-View-Controller_), lo que permite un diseño de aplicaciones más sencillo y flexible.

Por ejemplo, echemos un vistazo a la siguiente definición de clase de un objeto compatible con KVC:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

En primer lugar, el atributo `[Register("PersonModel")]` registra la clase y la expone a Objective-C. A continuación, la clase debe heredar de `NSObject` (o una subclase que hereda de `NSObject`), agrega varios métodos base que permiten a la clase ser compatible con KVC. A continuación, el atributo `[Export("Name")]` expone la propiedad `Name` y define el valor de clave que se usará más adelante para obtener acceso a la propiedad a través de las técnicas KVC y KVO.

Por último, para poder realizar cambios observados con valores clave en el valor de la propiedad, el descriptor de acceso debe ajustar los cambios en su valor en `WillChangeValue` y `DidChangeValue` llamadas al método (especificando la misma clave que el atributo `Export`).  Por ejemplo:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Este paso es _muy_ importante para el enlace de datos en la Interface Builder de Xcode (como veremos más adelante en este artículo).

Para obtener más información, consulte la [Guía de programación de codificación de clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)de Apple.

### <a name="keys-and-key-paths"></a>Claves y rutas de acceso de claves

Una _clave_ es una cadena que identifica una propiedad específica de un objeto. Normalmente, una clave corresponde al nombre de un método de descriptor de acceso en un objeto compatible con el código de valor de clave. Las claves deben usar la codificación ASCII, que normalmente comienzan con una letra minúscula y no pueden contener espacios en blanco. Por lo tanto, según el ejemplo anterior, `Name` sería un valor de clave de `Name` propiedad de la clase `PersonModel`. La clave y el nombre de la propiedad que exponen no tienen que ser iguales; sin embargo, en la mayoría de los casos son.

Una _ruta de acceso de clave_ es una cadena de claves separadas por puntos que se usa para especificar una jerarquía de propiedades de objeto que se va a atravesar. La propiedad de la primera clave de la secuencia es relativa al receptor y cada clave subsiguiente se evalúa con respecto al valor de la propiedad anterior. Del mismo modo, se usa la notación de puntos para atravesar un objeto y sus propiedades en C# una clase.

Por ejemplo, si expandió la clase `PersonModel` y agregó `Child` propiedad:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }

        public PersonModel ()
        {
        }
    }
}
```

La ruta de acceso de la clave al nombre del elemento secundario sería `self.Child.Name` o simplemente `Child.Name` (en función de cómo se usaba el valor de clave).

### <a name="getting-values-using-key-value-coding"></a>Obtener valores mediante el código de valor de clave

El método `ValueForKey` devuelve el valor de la clave especificada (como un `NSString`), con respecto a la instancia de la clase KVC que recibe la solicitud. Por ejemplo, si `Person` es una instancia de la clase `PersonModel` definida anteriormente:

```csharp
// Read value
var name = Person.ValueForKey (new NSString("Name"));
```

Esto devolvería el valor de la propiedad `Name` de esa instancia de `PersonModel`.

### <a name="setting-values-using-key-value-coding"></a>Establecer valores mediante el código clave-valor

Del mismo modo, el `SetValueForKey` establecer el valor de la clave especificada (como un `NSString`), con respecto a la instancia de la clase KVC que recibe la solicitud. De nuevo, con una instancia de la clase `PersonModel`, como se muestra a continuación:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Cambiaría el valor de la propiedad `Name` a `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Observar los cambios de valor

Mediante el uso de la función de valor de clave (KVO), puede adjuntar un observador a una clave específica de una clase conforme a KVC y recibir una notificación cada vez que se modifique el valor de esa clave (ya sea mediante técnicas C# KVC o el acceso directo a la propiedad especificada en el código). Por ejemplo:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Ahora, cada vez que se modifica la propiedad `Name` de la instancia de `Person` de la clase `PersonModel`, el nuevo valor se escribe en la consola.

Para obtener más información, consulte la guía de programación de la [Introducción a los valores de clave](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)de Apple.

## <a name="data-binding"></a>Enlace de datos

En las secciones siguientes se muestra cómo puede usar un código de valor de clave y una clase compatible con la observación de clave-valor para enlazar datos a elementos de la interfaz de usuario en la Interface Builder C# de Xcode, en lugar de leer y escribir valores mediante código. De esta manera se separa el _modelo de datos_ de las vistas que se usan para mostrarlos, lo que hace que la aplicación de Xamarin. Mac sea más flexible y fácil de mantener. También reduce en gran medida la cantidad de código que se debe escribir.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Definir el modelo de datos

Antes de poder enlazar datos a un elemento de la interfaz de usuario en Interface Builder, debe tener una clase compatible con KVC/KVO definida en la aplicación de Xamarin. Mac para que actúe como _modelo de datos_ para el enlace. El modelo de datos proporciona todos los datos que se mostrarán en la interfaz de usuario y recibirá cualquier modificación de los datos que el usuario realice en la interfaz de usuario mientras se ejecuta la aplicación.

Por ejemplo, si estuviera escribiendo una aplicación que administraba un grupo de empleados, podría usar la siguiente clase para definir el modelo de datos:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

La mayoría de las características de esta clase se trataron en la sección sobre la [codificación de valores de clave](#What_is_Key-Value_Coding) anteriores. Sin embargo, echemos un vistazo a algunos elementos específicos y algunas adiciones que se realizaron para permitir que esta clase actúe como modelo de datos para **controladores de matriz** y **controladores de árbol** (que vamos a usar más adelante en las vistas de **árbol**de enlace de datos, **vistas de esquema** y las **vistas de colección**).

En primer lugar, como un empleado podría ser un administrador, hemos usado un `NSArray` (específicamente un `NSMutableArray` para que se puedan modificar los valores) para permitir que los empleados que han administrado se adjunten a ellos:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Dos cosas que debe tener en cuenta aquí:

1. Hemos usado un `NSMutableArray` en lugar de una matriz C# o colección estándar, ya que se trata de un requisito para enlazar datos a controles AppKit como **vistas de tabla**, **vistas de esquema** y **colecciones**.
2. Exponíamos la matriz de empleados convirtiéndola en un `NSArray` para el enlace de datos y cambió C# su nombre con formato,`People`, a uno que el enlace de datos espera,`personModelArray`en la forma **{CLASS_NAME} array** (tenga en cuenta que el primer carácter se ha en minúsculas).

A continuación, es necesario agregar algunos métodos públicos de nombre especial para admitir **controladores de matriz** y controladores de **árbol**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Estos permiten que los controladores soliciten y modifiquen los datos que muestran. Al igual que el `NSArray` expuesto anterior, tienen una Convención de nomenclatura muy específica (que difiere de C# las convenciones de nomenclatura típicas):

- `addObject:`: agrega un objeto a la matriz.
- `insertObject:in{class_name}ArrayAtIndex:`, donde `{class_name}` es el nombre de la clase. Este método inserta un objeto en el índice especificado de la matriz.
- `removeObjectFrom{class_name}ArrayAtIndex:`, donde `{class_name}` es el nombre de la clase. Este método quita el objeto de la matriz en un índice determinado.
- `set{class_name}Array:`, donde `{class_name}` es el nombre de la clase. Este método permite reemplazar el transporte existente por uno nuevo.

Dentro de estos métodos, hemos ajustado los cambios en la matriz en `WillChangeValue` y `DidChangeValue` mensajes para el cumplimiento de KVO.

Por último, puesto que la propiedad `Icon` se basa en el valor de la propiedad `isManager`, es posible que los cambios en la propiedad `isManager` no se reflejen en el `Icon` para los elementos de la interfaz de usuario enlazados a datos (durante KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
```

Para corregirlo, usamos el código siguiente:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Tenga en cuenta que, además de su propia clave, el descriptor de acceso `isManager` también está enviando los mensajes `WillChangeValue` y `DidChangeValue` de la clave de `Icon` para que pueda ver el cambio también.

En el resto de este artículo, usaremos el `PersonModel` modelo de datos.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Enlace de datos simple

Con nuestro modelo de datos definido, echemos un vistazo a un sencillo ejemplo de enlace de datos en la Interface Builder de Xcode. Por ejemplo, vamos a agregar un formulario a nuestra aplicación de Xamarin. Mac que se puede usar para editar el `PersonModel` que se definió anteriormente. Vamos a agregar algunos campos de texto y una casilla para mostrar y editar las propiedades de nuestro modelo.

En primer lugar, vamos a agregar un nuevo **controlador de vista** a nuestro archivo **Main. Storyboard** en Interface Builder y nombrar su clase `SimpleViewController`:

[![Agregar un nuevo controlador de vista](databinding-images/simple01.png "Agregar un nuevo controlador de vista")](databinding-images/simple01-large.png#lightbox)

A continuación, vuelva a Visual Studio para Mac, edite el archivo **SimpleViewController.CS** (que se agregó automáticamente a nuestro proyecto) y exponga una instancia del `PersonModel` al que se va a enlazar el formulario. Agregue el código siguiente:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

A continuación, cuando se cargue la vista, vamos a crear una instancia de nuestra `PersonModel` y rellenarla con este código:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Ahora tenemos que crear el formulario, haga doble clic en el archivo **Main. Storyboard** para abrirlo y editarlo en Interface Builder. Diseño del formulario para que tenga un aspecto similar al siguiente:

[![Editar el guion gráfico en Xcode](databinding-images/simple02.png "Editar el guion gráfico en Xcode")](databinding-images/simple02-large.png#lightbox)

Para enlazar los datos del formulario al `PersonModel` que exponíamos a través de la clave `Person`, haga lo siguiente:

1. Seleccione el campo de texto **nombre de empleado** y cambie al **Inspector de enlaces**.
2. Active la casilla **enlazar con** y seleccione **controlador de vista simple** en la lista desplegable. Después, escriba `self.Person.Name` para la **ruta de acceso**de la clave:

    [![Escribir la ruta de acceso de la clave](databinding-images/simple03.png "Escribir la ruta de acceso de la clave")](databinding-images/simple03-large.png#lightbox)
3. Seleccione el campo de texto **ocupación** y active la casilla **enlazar con** y seleccione **controlador de vista simple** en la lista desplegable. Después, escriba `self.Person.Occupation` para la **ruta de acceso**de la clave:

    [![Escribir la ruta de acceso de la clave](databinding-images/simple04.png "Escribir la ruta de acceso de la clave")](databinding-images/simple04-large.png#lightbox)
4. Active la casilla **empleado como administrador** y active la casilla **enlazar con** y seleccione **controlador de vista simple** en la lista desplegable. Después, escriba `self.Person.isManager` para la **ruta de acceso**de la clave:

    [![Escribir la ruta de acceso de la clave](databinding-images/simple05.png "Escribir la ruta de acceso de la clave")](databinding-images/simple05-large.png#lightbox)
5. Seleccione el campo **de texto número de empleados administrados** y active la casilla **enlazar con** y seleccione **controlador de vista simple** en la lista desplegable. Después, escriba `self.Person.NumberOfEmployees` para la **ruta de acceso**de la clave:

    [![Escribir la ruta de acceso de la clave](databinding-images/simple06.png "Escribir la ruta de acceso de la clave")](databinding-images/simple06-large.png#lightbox)
6. Si el empleado no es un administrador, queremos ocultar el número de empleados con etiquetas y campos de texto administrados.
7. Seleccione la etiqueta **administrada número de empleados** , expanda el control situado **oculto** y active la casilla **enlazar con** y seleccione **controlador de vista simple** en la lista desplegable. Después, escriba `self.Person.isManager` para la **ruta de acceso**de la clave:

    [![Escribir la ruta de acceso de la clave](databinding-images/simple07.png "Escribir la ruta de acceso de la clave")](databinding-images/simple07-large.png#lightbox)
8. Seleccione `NSNegateBoolean` en la lista desplegable **transformador de valor** :

    ![Selección de la transformación clave NSNegateBoolean](databinding-images/simple08.png "Selección de la transformación clave NSNegateBoolean")
9. Esto indica al enlace de datos que la etiqueta se ocultará si el valor de la propiedad `isManager` es `false`.
10. Repita los pasos 7 y 8 para el campo **de texto administrado número de empleados** .
11. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Si ejecuta la aplicación, los valores de la propiedad `Person` rellenarán automáticamente el formulario:

[![Mostrar un formulario rellenado automáticamente](databinding-images/simple09.png "Mostrar un formulario rellenado automáticamente")](databinding-images/simple09-large.png#lightbox)

Cualquier cambio que los usuarios realice en el formulario se volverá a escribir en la propiedad `Person` en el controlador de vista. Por ejemplo, al anular la selección de **Employee** , el administrador actualiza la `Person` instancia de la `PersonModel` y el **número de empleados etiqueta administrada** y campo de texto se oculta automáticamente (a través del enlace de datos):

[![Ocultar el número de empleados para usuarios que no son administradores](databinding-images/simple10.png "Ocultar el número de empleados para usuarios que no son administradores")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Enlace de datos de vista de tabla

Ahora que tenemos los aspectos básicos del enlace de datos fuera de la vista, echemos un vistazo a una tarea de enlace de datos más compleja mediante el uso de un _controlador de matriz_ y un enlace de datos a una vista de tabla. Para obtener más información sobre cómo trabajar con vistas de tabla, consulte nuestra documentación de [vistas de tabla](~/mac/user-interface/table-view.md) .

En primer lugar, vamos a agregar un nuevo **controlador de vista** a nuestro archivo **Main. Storyboard** en Interface Builder y nombrar su clase `TableViewController`:

[![Agregar un nuevo controlador de vista](databinding-images/table01.png "Agregar un nuevo controlador de vista")](databinding-images/table01-large.png#lightbox)

A continuación, vamos a editar el archivo **TableViewController.CS** (que se agregó automáticamente a nuestro proyecto) y se expone una matriz (`NSArray`) de `PersonModel` clases a las que se va a enlazar el formulario. Agregue el código siguiente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Al igual que hicimos en la clase `PersonModel` anterior en la sección [definir el modelo de datos](#Defining_your_Data_Model) , hemos expuesto cuatro métodos públicos con un nombre especial para que el controlador de la matriz y leer y escribir datos de nuestra colección de `PersonModels`.

A continuación, cuando se cargue la vista, es necesario rellenar la matriz con este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Ahora tenemos que crear la vista de tabla, haga doble clic en el archivo **Main. Storyboard** para abrirlo para su edición en Interface Builder. Diseñar la tabla para que tenga un aspecto similar al siguiente:

[![Diseñar una nueva vista de tabla](databinding-images/table02.png "Diseñar una nueva vista de tabla")](databinding-images/table02-large.png#lightbox)

Es necesario agregar un controlador de la **matriz** para proporcionar datos enlazados a la tabla, haga lo siguiente:

1. Arrastre un **controlador** de la matriz desde el **Inspector de biblioteca** hasta el editor de la **interfaz**:

    ![Seleccionar un controlador de la matriz de la biblioteca](databinding-images/table03.png "Seleccionar un controlador de la matriz de la biblioteca")
2. Seleccione **controlador de matriz** en la jerarquía de la **interfaz** y cambie al **Inspector de atributo**:

    [![Seleccionar el inspector de atributos](databinding-images/table04.png "Seleccionar el inspector de atributos")](databinding-images/table04-large.png#lightbox)
3. Escriba `PersonModel` como nombre de la **clase**, haga clic en el botón **más** y agregue tres claves. Asígneles el nombre `Name`, `Occupation` y `isManager`:

    ![Agregar las rutas de acceso de clave necesarias](databinding-images/table05.png "Agregar las rutas de acceso de clave necesarias")
4. Esto indica al controlador de la matriz lo que está administrando una matriz de y qué propiedades debe exponer (a través de claves).
5. Cambie al **Inspector de enlaces** y, en **matriz de contenido** , seleccione **enlazar a** y controlador de vista de **tabla**. Escriba una **ruta de acceso de clave de modelo** de `self.personModelArray`:

    ![Escribir una ruta de acceso de clave](databinding-images/table06.png "Escribir una ruta de acceso de clave")
6. Esto une el controlador de la matriz a la matriz de `PersonModels` que exponíamos en nuestro controlador de vistas.

Ahora debemos enlazar nuestra vista de tabla al controlador de la matriz, haga lo siguiente:

1. Seleccione la vista de tabla y el **Inspector de enlace**:

    [![Seleccionar el inspector de enlace](databinding-images/table07.png "Seleccionar el inspector de enlace")](databinding-images/table07-large.png#lightbox)
2. En el control situado contenido de la **tabla** , seleccione **enlazar con** y controlador de la **matriz**. Escriba `arrangedObjects` para el campo **clave del controlador** :

    ![Definir la clave del controlador](databinding-images/table08.png "Definir la clave del controlador")
3. Seleccione la **celda vista de tabla** en la columna **empleado** . En el **Inspector de enlaces** en el **valor** control situado, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Name` para la **ruta de acceso**de la clave del modelo:

    [![Establecer la ruta de acceso de la clave del modelo](databinding-images/table09.png "Establecer la ruta de acceso de la clave del modelo")](databinding-images/table09-large.png#lightbox)
4. `objectValue` es el `PersonModel` actual de la matriz que se está administrando mediante el controlador de la matriz.
5. Seleccione la **celda vista de tabla** en la columna **ocupación** . En el **Inspector de enlaces** en el **valor** control situado, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Occupation` para la **ruta de acceso**de la clave del modelo:

    [![Establecer la ruta de acceso de la clave del modelo](databinding-images/table10.png "Establecer la ruta de acceso de la clave del modelo")](databinding-images/table10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Si ejecutamos la aplicación, la tabla se rellenará con nuestra matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/table11.png "Ejecutar la aplicación")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>vista Esquema enlace de datos

el enlace de datos en una vista de esquema es muy similar al enlace en una vista de tabla. La diferencia clave es que vamos a usar un **controlador de árbol** en lugar de un **controlador** de la matriz para proporcionar los datos enlazados a la vista de esquema. Para obtener más información sobre cómo trabajar con las vistas de esquema, consulte la documentación de las [vistas de esquema](~/mac/user-interface/outline-view.md) .

En primer lugar, vamos a agregar un nuevo **controlador de vista** a nuestro archivo **Main. Storyboard** en Interface Builder y nombrar su clase `OutlineViewController`:

[![Agregar un nuevo controlador de vista](databinding-images/outline01.png "Agregar un nuevo controlador de vista")](databinding-images/outline01-large.png#lightbox)

A continuación, vamos a editar el archivo **OutlineViewController.CS** (que se agregó automáticamente a nuestro proyecto) y se expone una matriz (`NSArray`) de `PersonModel` clases a las que se va a enlazar el formulario. Agregue el código siguiente:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Al igual que hicimos en la clase `PersonModel` anterior en la sección [definir el modelo de datos](#Defining_your_Data_Model) , hemos expuesto cuatro métodos públicos con un nombre especial para que el controlador de árbol y leer y escribir datos de nuestra colección de `PersonModels`.

A continuación, cuando se cargue la vista, es necesario rellenar la matriz con este código:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Ahora tenemos que crear la vista de esquema, haga doble clic en el archivo **Main. Storyboard** para abrirlo para su edición en Interface Builder. Diseñar la tabla para que tenga un aspecto similar al siguiente:

[![Crear la vista de esquema](databinding-images/outline02.png "Crear la vista de esquema")](databinding-images/outline02-large.png#lightbox)

Necesitamos agregar un controlador de **árbol** para proporcionar datos enlazados a nuestro esquema; haga lo siguiente:

1. Arrastre un **controlador de árbol** desde el **Inspector de biblioteca** hasta el editor de la **interfaz**:

    ![Seleccionar un controlador de árbol de la biblioteca](databinding-images/outline03.png "Seleccionar un controlador de árbol de la biblioteca")
2. Seleccione **controlador de árbol** en la jerarquía de la **interfaz** y cambie al **Inspector de atributo**:

    [![Seleccionar el inspector de atributos](databinding-images/outline04.png "Seleccionar el inspector de atributos")](databinding-images/outline04-large.png#lightbox)
3. Escriba `PersonModel` como nombre de la **clase**, haga clic en el botón **más** y agregue tres claves. Asígneles el nombre `Name`, `Occupation` y `isManager`:

    ![Agregar las rutas de acceso de clave necesarias](databinding-images/outline05.png "Agregar las rutas de acceso de clave necesarias")
4. Esto indica al controlador de árbol lo que está administrando una matriz de y qué propiedades debe exponer (a través de claves).
5. En la sección **controlador de árbol** , escriba `personModelArray` para **elementos secundarios**, escriba `NumberOfEmployees` en el **recuento** y escriba `isEmployee` en **hoja**:

    ![Establecer las rutas de acceso de las claves del controlador de árbol](databinding-images/outline05.png "Establecer las rutas de acceso de las claves del controlador de árbol")
6. Esto indica al controlador de árbol dónde encontrar los nodos secundarios, cuántos nodos secundarios hay y si el nodo actual tiene nodos secundarios.
7. Cambie al **Inspector de enlaces** y, en **matriz de contenido** , seleccione **enlazar a** y el **propietario del archivo**. Escriba una **ruta de acceso de clave de modelo** de `self.personModelArray`:

    ![Editar la ruta de acceso de la clave](databinding-images/outline06.png "Editar la ruta de acceso de la clave")
8. Esto une el controlador de árbol a la matriz de `PersonModels` que exponíamos en nuestro controlador de vistas.

Ahora debemos enlazar nuestra vista de esquema al controlador de árbol, haga lo siguiente:

1. Seleccione la vista esquema y, en el **Inspector de enlace** , seleccione:

    [![Seleccionar el inspector de enlace](databinding-images/outline07.png "Seleccionar el inspector de enlace")](databinding-images/outline07-large.png#lightbox)
2. En la **vista esquema contenido** control situado, seleccione **enlazar con** y **controlador de árbol**. Escriba `arrangedObjects` para el campo **clave del controlador** :

    ![Establecimiento de la clave de controlador](databinding-images/outline08.png "Establecimiento de la clave de controlador")
3. Seleccione la **celda vista de tabla** en la columna **empleado** . En el **Inspector de enlaces** en el **valor** control situado, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Name` para la **ruta de acceso**de la clave del modelo:

    [![Escribir la ruta de acceso de la clave del modelo](databinding-images/outline09.png "Escribir la ruta de acceso de la clave del modelo")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` es el `PersonModel` actual de la matriz que se está administrando mediante el controlador de árbol.
5. Seleccione la **celda vista de tabla** en la columna **ocupación** . En el **Inspector de enlaces** en el **valor** control situado, seleccione **enlazar a** y **vista de celda de tabla**. Escriba `objectValue.Occupation` para la **ruta de acceso**de la clave del modelo:

    [![Escribir la ruta de acceso de la clave del modelo](databinding-images/outline10.png "Escribir la ruta de acceso de la clave del modelo")](databinding-images/outline10-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Si ejecutamos la aplicación, el contorno se rellenará con nuestra matriz de `PersonModels`:

[![Ejecutar la aplicación](databinding-images/outline11.png "Ejecutar la aplicación")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Enlace de datos de la vista de colección

El enlace de datos con una vista de colección es muy similar al enlace con una vista de tabla, ya que un controlador de matriz se utiliza para proporcionar datos para la colección. Dado que la vista de colección no tiene un formato de presentación preestablecido, se requiere más trabajo para proporcionar comentarios de interacción del usuario y realizar un seguimiento de la selección del usuario.

> [!IMPORTANT]
> Debido a un problema en Xcode 7 y macOS 10,11 (y versiones posteriores), las vistas de colección no se pueden usar en un archivo de guion gráfico (. Storyboard). Como resultado, tendrá que seguir usando los archivos. Xib para definir las vistas de colección para las aplicaciones de Xamarin. Mac. Consulte nuestra documentación sobre [vistas de colección](~/mac/user-interface/collection-view.md) para obtener más información.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`:

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1. **Collection View Item** -  That manages a single instance of an item in the collection.
2. **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Depuración de bloqueos nativos

Al cometer un error en los enlaces de datos, se puede producir un _bloqueo nativo_ en el código no administrado y hacer que la aplicación de Xamarin. Mac produzca un error por completo con un `SIGABRT`:

[![Ejemplo de un cuadro de diálogo de bloqueo nativo](databinding-images/debug01.png "Ejemplo de un cuadro de diálogo de bloqueo nativo")](databinding-images/debug01-large.png#lightbox)

Normalmente hay cuatro causas principales para los bloqueos nativos durante el enlace de datos:

1. El modelo de datos no hereda de `NSObject` ni de una subclase de `NSObject`.
2. No expondría su propiedad a Objective-C mediante el atributo `[Export("key-name")]`.
3. No ha ajustado los cambios en el valor del descriptor de acceso en `WillChangeValue` y `DidChangeValue` llamadas al método (especificando la misma clave que el atributo `Export`).
4. Tiene una clave mal escrita o incorrecta en el inspector de **enlace** de Interface Builder.

### <a name="decoding-a-crash"></a>Descodificar un bloqueo

Vamos a generar un bloqueo nativo en nuestro enlace de datos para que podamos mostrar cómo ubicarlo y corregirlo. En Interface Builder, vamos a cambiar nuestro enlace de la primera etiqueta en el ejemplo de vista de colección de `Name` a `Title`:

[![Editar la clave de enlace](databinding-images/debug02.png "Editar la clave de enlace")](databinding-images/debug02-large.png#lightbox)

Vamos a guardar el cambio, volver a Visual Studio para Mac para sincronizar con Xcode y ejecutar la aplicación. Cuando se muestra la vista de recopilación, la aplicación se bloquea momentáneamente con un error `SIGABRT` (como se muestra en la salida de la **aplicación** en Visual Studio para Mac), ya que el `PersonModel` no expone una propiedad con la clave `Title`:

[![Ejemplo de un error de enlace](databinding-images/debug03.png "Ejemplo de un error de enlace")](databinding-images/debug03-large.png#lightbox)

Si se desplaza a la parte superior del error en la salida de la **aplicación** , podemos ver la clave para solucionar el problema:

[![Buscar el problema en el registro de errores](databinding-images/debug04.png "Buscar el problema en el registro de errores")](databinding-images/debug04-large.png#lightbox)

Esta línea nos indica que el `Title` de claves no existe en el objeto al que se está enlazando. Si cambiamos de nuevo el enlace a `Name` en Interface Builder, guardar, sincronizar, volver a generar y ejecutar, la aplicación se ejecutará de la manera esperada sin problema.

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con el enlace de datos y el código de clave-valor en una aplicación de Xamarin. Mac. En primer lugar, se examinó la exposición C# de una clase a Objective-C mediante el uso de código de clave-valor (KVC) y el valor de clave (KVO). A continuación, se mostró cómo usar una clase compatible con KVO y cómo enlazar datos a elementos de la interfaz de usuario en la Interface Builder de Xcode. Por último, mostraba un enlace de datos complejo mediante **controladores de matriz** y **controladores de árbol**.

## <a name="related-links"></a>Vínculos relacionados

- [Guion gráfico de MacDatabinding (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-storyboard)
- [MacDatabinding Xib (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabinding-xibs)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Controles estándar](~/mac/user-interface/standard-controls.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Vistas de colección](~/mac/user-interface/collection-view.md)
- [Guía de programación de codificación de clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introducción a la guía de programación de la observación de clave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Introducción a los temas de programación de enlaces de cacao](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introducción a la referencia de enlaces de chocolate](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
