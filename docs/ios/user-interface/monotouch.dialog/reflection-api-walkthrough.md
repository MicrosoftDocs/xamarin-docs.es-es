---
title: Creación de una aplicación de Xamarin. iOS mediante la API de reflexión
description: En este documento se describe la API de reflexión basada en atributos MonoTouch. Dialog, que crea la interfaz de usuario basada en clases representadas con atributos.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2eef38791f420fd49b87b46fd131f1e6ce501892
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436587"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Creación de una aplicación de Xamarin. iOS mediante la API de reflexión

MT. La API de reflexión D permite que las clases se contengan con atributos que MT. D usa para crear pantallas automáticamente. La API de reflexión proporciona un enlace entre estas clases y lo que se muestra en la pantalla. Aunque esta API no proporciona el control específico que la API de elementos hace, reduce la complejidad al crear automáticamente la jerarquía de elementos basada en la decoración de clase.

## <a name="setting-up-mtd"></a>Configuración de MT. D

Módulo. D se distribuye con Xamarin. iOS. Para usarlo, haga clic con el botón derecho en el nodo **referencias** de un proyecto de Xamarin. iOS en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia al ensamblado **MonoTouch. Dialog-1** . A continuación, agregue `using MonoTouch.Dialog` las instrucciones en el código fuente según sea necesario.

## <a name="getting-started-with-the-reflection-api"></a>Introducción a la API de reflexión

El uso de la API de reflexión es tan sencillo como:

1. Crear una clase decorada con MT. Atributos D.
1. Crear una  `BindingContext` instancia de, pasándole una instancia de la clase anterior.
1. Crear un  `DialogViewController` , pasándole el  `BindingContext’s` `RootElement` .

Echemos un vistazo a un ejemplo para ilustrar cómo usar la API de reflexión. En este ejemplo, se creará una pantalla de entrada de datos simple, como se muestra a continuación:

 [![En este ejemplo, se creará una pantalla de entrada de datos simple, como se muestra aquí.](reflection-api-walkthrough-images/01-expense-entry.png)](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Crear una clase con MT. Atributos D

Lo primero que necesitamos usar la API de reflexión es una clase decorada con atributos. MT usará estos atributos. D internamente para crear objetos a partir de la API de elementos. Por ejemplo, considere la siguiente definición de clase:

```csharp
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
}
```

El `SectionAttribute` resultado hará que se creen secciones de `UITableView` , con el argumento de cadena que se usa para rellenar el encabezado de la sección. Una vez declarada una sección, cada campo que la sigue se incluirá en esa sección hasta que se declare otra sección.
El tipo de elemento de la interfaz de usuario creado para el campo dependerá del tipo del campo y del MT. El atributo D lo decora.

Por ejemplo, el `Name` campo es un `string` y se decora con un `EntryAttribute` . Como resultado, se agrega una fila a la tabla con un campo de entrada de texto y el título especificado. Del mismo modo, el `IsApproved` campo es un `bool` con `CheckboxAttribute` , lo que da lugar a una fila de la tabla con una casilla a la derecha de la celda de la tabla. Módulo. D usa el nombre del campo, agregando automáticamente un espacio, para crear el título en este caso, ya que no se especifica en un atributo.

## <a name="adding-the-bindingcontext"></a>Agregar el BindingContext

Para usar la `Expense` clase, es necesario crear un `BindingContext` . Una `BindingContext` es una clase que enlazará los datos de la clase con atributos para crear la jerarquía de elementos. Para crear uno, simplemente hay que crear una instancia de él y pasar una instancia de la clase con atributos al constructor.

Por ejemplo, para agregar la interfaz de usuario que declaró con el atributo en la `Expense` clase, incluya el código siguiente en el `FinishedLaunching` método de `AppDelegate` :

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Después, todo lo que tenemos que hacer para crear la interfaz de usuario es agregar el `BindingContext` a `DialogViewController` y establecerlo como la `RootViewController` de la ventana, como se muestra a continuación:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{   
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    var expense = new Expense ();
    var bctx = new BindingContext (null, expense, "Create a task");
    var dvc = new DialogViewController (bctx.Root);

    window.RootViewController = dvc;
    window.MakeKeyAndVisible ();

    return true;
}
```

La ejecución de la aplicación ahora da lugar a que se muestre la pantalla mostrada anteriormente.

### <a name="adding-a-uinavigationcontroller"></a>Adición de un UINavigationController

No obstante, tenga en cuenta que no se muestra el título "crear una tarea" que se pasa a `BindingContext` . Esto se debe `DialogViewController` a que no forma parte de `UINavigatonController` un. Vamos a cambiar el código para agregar un `UINavigationController` como la de la ventana `RootViewController,` y agregar `DialogViewController` como la raíz de `UINavigationController` , como se muestra a continuación:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Ahora, cuando ejecutemos la aplicación, el título aparecerá en la `UINavigationController’s` barra de navegación, tal como se muestra en la captura de pantalla siguiente:

 [![Ahora, cuando se ejecuta la aplicación, el título aparece en la barra de navegación UINavigationControllers](reflection-api-walkthrough-images/02-create-task.png)](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Mediante la inclusión de `UINavigationController` , ahora podemos aprovechar otras características de Mt. D para la que es necesaria la navegación. Por ejemplo, podemos agregar una enumeración a la `Expense` clase para definir la categoría del gasto y Mt. D creará automáticamente una pantalla de selección. Para demostrarlo, modifique la `Expense` clase para incluir un `ExpenseCategory` campo como se indica a continuación:

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}

public class Expense
{
    …

    [Caption("Category")]
    public Category ExpenseCategory;
}
```

La ejecución de la aplicación ahora da como resultado una nueva fila en la tabla para la categoría, como se muestra a continuación:

 [![La ejecución de la aplicación ahora da como resultado una nueva fila en la tabla para la categoría, tal como se muestra](reflection-api-walkthrough-images/03-set-details.png)](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Al seleccionar la fila, la aplicación navega a una nueva pantalla con las filas correspondientes a la enumeración, como se muestra a continuación:

 [![Al seleccionar la fila, la aplicación navega a una nueva pantalla con las filas correspondientes a la enumeración](reflection-api-walkthrough-images/04-set-category.png)](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha presentado un tutorial de la API de reflexión. Hemos mostrado cómo agregar atributos a una clase para controlar lo que se muestra. También se describe cómo usar un `BindingContext` para enlazar datos de una clase a la jerarquía de elementos que se crea, así como el uso de Mt. D con `UINavigationController` .

## <a name="related-links"></a>Vínculos relacionados

- [MTDReflectionWalkthrough (ejemplo)](/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Introducción al cuadro de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial de elementos JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Cuadro de diálogo MonoTouch en github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de la clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de la clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)