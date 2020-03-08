---
title: Análisis detallado de Hello, iOS Multiscreen
description: En este documento se analiza con detalle la aplicación Phoneword ampliada, para lo que se tiene en cuenta el modelo de diseño Model-View-Controller, la navegación de iOS y otros conceptos de desarrollo de iOS.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: c866e5f4-8154-4342-876e-efa0693d66f5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 3bcfb20d8283f621ac1d32730ee67be2b09efe50
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918090"
---
# <a name="hello-ios-multiscreen--deep-dive"></a>Análisis detallado de Hello, iOS Multiscreen

En el tutorial Inicio rápido, creamos y ejecutamos nuestra primera aplicación de Xamarin.iOS multipantalla. Es hora de desarrollar un conocimiento más profundo de la navegación y la arquitectura de iOS.

En esta guía se presenta el patrón *Modelo, Vista, Controlador (MVC)* y su papel en la arquitectura y la navegación de iOS.
Luego se profundiza en el controlador de navegación y se aprende a usarlo para proporcionar una experiencia de navegación conocida en iOS.

## <a name="model-view-controller-mvc"></a>Modelo-Vista-Controlador (MVC)

En el tutorial [Hello, iOS](~/ios/get-started/hello-ios/index.md), aprendimos que las aplicaciones de iOS solo tienen una *ventana* en la que los controladores de vista cargan sus *jerarquías de vista de contenido*. En el segundo tutorial Phoneword, agregamos una segunda pantalla a la aplicación y pasamos algunos datos (una lista de números de teléfono) entre las dos pantallas, como se muestra en el diagrama siguiente:

 [![](hello-ios-multiscreen-deepdive-images/08.png "This diagram illustrates passing data between two screens")](hello-ios-multiscreen-deepdive-images/08.png#lightbox)

En el ejemplo, los datos se recopilaban en la primera pantalla, se pasaban desde el primer controlador de vista al segundo y aparecían en la segunda pantalla. Esta separación de datos, controladores de vista y pantallas sigue el patrón *Modelo, Vista, Controlador (MVC)* . En las siguientes secciones se explican las ventajas del modelo, sus componentes y cómo se usa en la aplicación Phoneword.

### <a name="benefits-of-the-mvc-pattern"></a>Ventajas del modelo MVC

Modelo-Vista-Controlador es un *modelo de diseño*: una solución de arquitectura reutilizable para un problema o un caso de uso común en el código. MVC es una arquitectura para aplicaciones con una *interfaz gráfica de usuario (GUI)* . Asigna objetos de uno de tres roles de la aplicación: el *modelo* (lógica de datos o aplicación), la *vista* (interfaz de usuario) y el *controlador* (código subyacente). En el diagrama siguiente se muestran las relaciones entre las tres partes del modelo MVC y el usuario:

 [![](hello-ios-multiscreen-deepdive-images/00.png "This diagram illustrates the relationships between the three pieces of the MVC pattern and the user")](hello-ios-multiscreen-deepdive-images/00.png#lightbox)

El modelo MVC es útil porque proporciona una separación lógica entre diferentes partes de una aplicación GUI y facilita la reutilización del código y las vistas. Vamos a examinar más detalladamente cada uno de los tres roles.

> [!NOTE]
> El modelo MVC se asemeja ligeramente a la estructura de las páginas ASP.NET o las aplicaciones WPF. En estos ejemplos, la vista es el componente realmente responsable de describir la interfaz de usuario y corresponde a la página ASPX (HTML) de ASP.NET o a XAML en una aplicación WPF. El controlador es el componente que se encarga de administrar la vista y corresponde al código subyacente de ASP.NET o WPF.

### <a name="model"></a>Modelo

El objeto modelo normalmente es una representación específica de una aplicación de datos que se van a mostrar o escribir en la vista. El modelo suele estar definido de forma imprecisa, por ejemplo, en la aplicación **Phoneword_iOS**, la lista de números de teléfono (representados como una lista de cadenas) es el modelo. Si estuviéramos creando una aplicación de varias plataformas, podríamos optar por compartir el código **PhonewordTranslator** entre las aplicaciones iOS y Android. También podríamos imaginarnos ese código compartido como el modelo.

MVC es completamente independiente de la *persistencia de datos* y del *acceso* del modelo. Dicho de otro modo, a MVC no le importa el aspecto de los datos ni cómo se almacenan, solo cómo están *representados*. Por ejemplo, podríamos optar por almacenar los datos en una base de datos SQL, conservarlos en algún mecanismo de almacenamiento en la nube o simplemente usar `List<string>`. Para los fines de MVC, en el modelo solo se incluye la propia representación de los datos.

En algunos casos, la parte del modelo de MVC puede estar vacía. Por ejemplo, podríamos agregar algunas páginas estáticas a la aplicación en las que se explicara cómo funciona el traductor telefónico, por qué se ha creado y cómo ponerse en contacto con nosotros para notificar errores. Estas pantallas de la aplicación se crearían con vistas y controladores, pero no tendrían ningunos datos reales del modelo.

> [!NOTE]
> En algunos artículos, la parte de modelo del patrón MVC puede referirse a todo el back-end de la aplicación, no solo a los datos que aparecen en la interfaz de usuario. En esta guía se usa una interpretación moderna del modelo, aunque la distinción no es especialmente importante.

### <a name="view"></a>Ver

Una vista es el componente responsable de representar la interfaz de usuario. En casi todas las plataformas que usan el modelo MVC, la interfaz de usuario se compone de una jerarquía de vistas. Se puede imaginar una vista de MVC como una jerarquía de vistas con una sola vista (conocida como la vista raíz) en la parte superior de la jerarquía y una serie de vistas secundarias (conocidas como subvistas) por debajo. En iOS, la jerarquía de vistas de contenido de una pantalla corresponde al componente de vista de MVC.

### <a name="controller"></a>Controller

El objeto controlador es el componente que conecta todos los elementos y en iOS se representa mediante `UIViewController`. Se puede imaginar el controlador como el código de respaldo de una pantalla o un conjunto de vistas. El controlador es responsable de escuchar las solicitudes del usuario y de devolver la jerarquía de vistas adecuada. Escucha las solicitudes de la vista (clics, entradas de texto, etc.) y realiza el procesamiento adecuado, la modificación y la recarga de la vista. El controlador también es responsable de crear o recuperar el modelo del almacén de datos de respaldo que haya en la aplicación y de rellenar la vista con sus datos.

Los controladores también pueden administrar otros controladores. Por ejemplo, un controlador podría cargar otro controlador si necesitara mostrar otra pantalla o administrar una pila de controladores a fin de supervisar el orden y las transiciones entre ellos. En la siguiente sección se muestra un ejemplo de un controlador que administra otros controladores y se presenta un tipo especial de controlador de vista de iOS denominado *controlador de navegación*.

## <a name="navigation-controller"></a>Controlador de navegación

En la aplicación Phoneword se ha usado un controlador de navegación para administrar la navegación entre varias pantallas. El controlador de navegación es un `UIViewController` especializado representado por la clase `UINavigationController`. En lugar de administrar una única jerarquía de vistas de contenido, el controlador de navegación administra otros controladores de vista, así como su propia jerarquía especial de vistas de contenido en forma de barra de herramientas de navegación que incluye un título, el botón Atrás y otras características opcionales.

El controlador de navegación es común en las aplicaciones de iOS y proporciona navegación para aplicaciones de iOS inseparables como la aplicación **Ajustes**, como se muestra en la siguiente captura de pantalla:

 [![](hello-ios-multiscreen-deepdive-images/01.png "The navigation controller provides navigation for iOS applications like the Settings app shown here")](hello-ios-multiscreen-deepdive-images/01.png#lightbox)

El controlador de navegación tiene tres funciones principales:

- **Proporciona enlaces para la navegación hacia delante**: el controlador de navegación usa una metáfora de navegación jerárquica en la que las jerarquías de vistas de contenido se *insertan* en una *pila de navegación*. Puede imaginarse una pila de navegación como una pila de cartas en la que solo la superior es visible, como se muestra en el diagrama siguiente:  

    [![](hello-ios-multiscreen-deepdive-images/02.png "This diagram illustrates navigation as a stack of cards")](hello-ios-multiscreen-deepdive-images/02.png#lightbox)

- **Opcionalmente proporciona un botón Atrás**: cuando se inserta un nuevo elemento en la pila de navegación, la barra de título puede mostrar automáticamente un *botón Atrás* que permita al usuario navegar hacia atrás. Al presionar el botón Atrás, *extrae* el controlador de vista actual de la pila de navegación y carga la jerarquía de vistas de contenido anterior en la ventana:  

    [![](hello-ios-multiscreen-deepdive-images/03.png "This diagram illustrates 'popping' a card off the stack")](hello-ios-multiscreen-deepdive-images/03.png#lightbox)

- **Proporciona una barra de título**: la parte superior del controlador de navegación se denomina *barra de título*. Es responsable de mostrar el título del controlador de vista, como se muestra en el diagrama siguiente:  

    [![](hello-ios-multiscreen-deepdive-images/04.png "The Title Bar is responsible for displaying the view controller title")](hello-ios-multiscreen-deepdive-images/04.png#lightbox)

### <a name="root-view-controller"></a>Controlador de vista raíz

Un controlador de navegación no administra una jerarquía de vistas de contenido, por lo que no tiene nada que mostrar por sí mismo.
En su lugar, un controlador de navegación se empareja con un *controlador de vista raíz*:

 [![](hello-ios-multiscreen-deepdive-images/05.png "A navigation controller is paired with a Root view controller")](hello-ios-multiscreen-deepdive-images/05.png#lightbox)

El controlador de vista raíz representa al primer controlador de vista de la pila del controlador de navegación y la jerarquía de vistas de contenido del controlador de vista raíz es la primera jerarquía de vistas de contenido que se carga en la ventana. Si se quiere colocar toda la aplicación en la pila del controlador de navegación, se puede pasar el segoe sin origen al controlador de navegación y establecer el controlador de vista de la primera pantalla como controlador de vista raíz, como se hizo en la aplicación Phoneword:

 [![](hello-ios-multiscreen-deepdive-images/06.png "The Sourceless Segue sets the first screens view controller as the Root view controller")](hello-ios-multiscreen-deepdive-images/06.png#lightbox)

### <a name="additional-navigation-options"></a>Otras opciones de navegación

El controlador de navegación es una forma común de controlar la navegación en iOS, pero no es la única opción. Por ejemplo, un [controlador de barra de pestañas](~/ios/user-interface/controls/creating-tabbed-applications.md) puede dividir una aplicación en varias áreas funcionales y un [controlador de vista divida](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/split_view/use_split_view_to_show_two_controllers) puede usarse para crear vistas principales o detalladas. Al combinar los controladores de navegación con estos otros esquemas de navegación, obtendremos varias formas de presentar el contenido y navegar por este de forma flexible en iOS.

## <a name="handling-transitions"></a>Control de transiciones

En el tutorial de Phoneword, la transición entre los dos controladores de vista se controló de dos maneras diferentes: primero con un segoe de Storyboard y luego mediante programación. Vamos a examinar más detalladamente estas opciones.

### <a name="prepareforsegue"></a>PrepareForSegue

Cuando se agrega un segoe con una acción **Mostrar** al Storyboard, se indica a iOS que inserte el segundo controlador de vista en la pila del controlador de navegación:

 [![](hello-ios-multiscreen-deepdive-images/09.png "Setting the segue type from a dropdown list")](hello-ios-multiscreen-deepdive-images/09.png#lightbox)

La adición de un Segue al guion gráfico basta para crear una transición sencilla entre pantallas. Si quiere pasar datos entre controladores de vista, tiene que invalidar el método `PrepareForSegue` y controlar los datos por sí mismo:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);
    ...
}
```

iOS llama a `PrepareForSegue` justo antes de que se produzca la transición y pasa el Segue creado en el guion gráfico al método.
Llegados a este punto, es necesario establecer manualmente el controlador de vista de destino del segoe. El código siguiente obtiene un identificador para el controlador de vista de destino y lo convierte en la clase adecuada, CallHistoryController en este caso:

```csharp
CallHistoryController callHistoryController = segue.DestinationViewController as CallHistoryController;
```

Por último, se pasa la lista de números de teléfono (el modelo) desde `ViewController` a `CallHistoryController` al establecer la propiedad `PhoneHistory` de `CallHistoryController` en la lista de números de teléfono marcados:

```csharp
callHistoryController.PhoneNumbers = PhoneNumbers;
```

El código completo para pasar los datos mediante un Segue es el siguiente:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController as CallHistoryController;

    if (callHistoryController != null) {
         callHistoryController.PhoneNumbers = PhoneNumbers;
    }
 }
```

### <a name="navigation-without-segues"></a>Navegación sin objetos Segue

La transición desde el primer controlador de vista al segundo en el código es el mismo proceso que con un segoe, aunque varios pasos deben realizarse manualmente.
Primero se usa `this.NavigationController` para obtener una referencia al controlador de navegación en cuya pila se está. Luego se usa el método `PushViewController` del controlador de navegación para insertar manualmente el siguiente controlador de vista en la pila, pasando el controlador de vista y una opción para animar la transición (se establece en `true`).

El código siguiente controla la transición de la pantalla Phoneword a la pantalla Call History:

```csharp
this.NavigationController.PushViewController (callHistory, true);
```

Para poder pasar al siguiente controlador de vista, hay que crear una instancia suya de forma manual desde Storyboard. Para ello, se llama a `this.Storyboard.InstantiateViewController` y se pasa el identificador de Storyboard de `CallHistoryController`:

```csharp
CallHistoryController callHistory =
this.Storyboard.InstantiateViewController
("CallHistoryController") as CallHistoryController;
```

Por último, se pasa la lista de números de teléfono (el modelo) desde `ViewController` a `CallHistoryController`. Para ello, se establece la propiedad `PhoneHistory` de `CallHistoryController` en la lista de números de teléfono marcados, tal como se hizo al controlar la transición con un Segue:

```csharp
callHistory.PhoneNumbers = PhoneNumbers;
```

El código completo de la transición mediante programación es el siguiente:

```csharp
CallHistoryButton.TouchUpInside += (object sender, EventArgs e) => {
    // Launches a new instance of CallHistoryController
    CallHistoryController callHistory = this.Storyboard.InstantiateViewController ("CallHistoryController") as CallHistoryController;
    if (callHistory != null) {
     callHistory.PhoneNumbers = PhoneNumbers;
     this.NavigationController.PushViewController (callHistory, true);
    }
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Otros conceptos presentados en Phoneword

La aplicación Phoneword presentó varios conceptos que no se tratan en esta guía. Entre estos conceptos se incluyen los siguientes:

- **Creación automática de controladores de vista**: cuando se escribe un nombre de clase para el controlador de vista en el **Panel de propiedades**, iOS Designer comprueba si esa clase existe y luego genera la clase de respaldo del controlador de vista. Para más información sobre esta y otras características de iOS Designer, vea la guía [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md).
- **Controlador de vista de tabla**: `CallHistoryController` es un controlador de vista de tabla. Un controlador de vista de tabla contiene una vista de tabla, que es la herramienta de diseño y visualización de datos más común de iOS. Las tablas están fuera del ámbito de esta guía. Para obtener más información sobre los controladores de vista de tabla, vea la guía [Trabajar con tablas y las celdas](~/ios/user-interface/controls/tables/index.md).
- **Id. de Storyboard**: al establecer el identificador de Storyboard, se crea una clase de controlador de vista en Objective-C que contiene el código subyacente del controlador de vista en Storyboard. El identificador de Storyboard se usa para buscar la clase de Objective-C y crear instancias del controlador de vista en Storyboard. Para más información sobre los identificadores de guion gráfico, vea la guía [Introduction to Storyboards (Introducción a los guiones gráficos)](~/ios/user-interface/storyboards/index.md).

## <a name="summary"></a>Resumen

Enhorabuena, ha completado su primera aplicación multipantalla de iOS.

En esta guía se ha presentado el modelo MVC y se ha usado para crear una aplicación de varias pantallas. También se han examinado los controladores de navegación y el papel que desempeñan en la navegación de iOS. Ahora tiene la base necesaria para empezar a desarrollar sus propias aplicaciones de Xamarin.iOS.

A continuación vamos a aprender a crear aplicaciones multiplataforma con Xamarin con las guías [Introduction to Mobile Development (Introducción al desarrollo móvil)](~/cross-platform/get-started/introduction-to-mobile-development.md) y [Building Cross-Platform Applications (Creación de aplicaciones multiplataforma)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Hola, iOS (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Directrices de la interfaz humana de iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de aprovisionamiento de iOS](https://developer.apple.com/ios/manage/overview/index.action)
