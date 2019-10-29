---
title: Trabajar con acciones de fila en Xamarin. iOS
description: En esta guía se muestra cómo crear acciones de deslizamiento personalizado para filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 542ae6696bae8fccfa6d5ed9842bce126760da37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021865"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabajar con acciones de fila en Xamarin. iOS

_En esta guía se muestra cómo crear acciones de deslizamiento personalizado para filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction_

![Mostrar las acciones de deslizar rápidamente en las filas](row-action-images/action02.png)

iOS proporciona dos maneras de realizar acciones en una tabla: `UISwipeActionsConfiguration` y `UITableViewRowAction`.

`UISwipeActionsConfiguration` se presentó en iOS 11 y se usa para definir un conjunto de acciones que deben realizarse cuando el usuario se desliza rápidamente _en cualquier dirección_ de una fila en una vista de tabla. Este comportamiento es similar al de la aplicación de correo electrónico nativo.

La clase `UITableViewRowAction` se utiliza para definir una acción que tendrá lugar cuando el usuario se deslice horizontalmente a la izquierda en una fila de una vista de tabla.
Por ejemplo, al editar una tabla, deslizar el dedo a la izquierda en una fila muestra un botón **eliminar** de forma predeterminada. Al adjuntar varias instancias de la clase `UITableViewRowAction` a un `UITableView`, se pueden definir varias acciones personalizadas, cada una con su propio texto, formato y comportamiento.

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Hay tres pasos necesarios para implementar las acciones de deslizamiento con `UISwipeActionsConfiguration`:

1. Invalide `GetLeadingSwipeActionsConfiguration` y/o `GetTrailingSwipeActionsConfiguration` métodos. Estos métodos devuelven un `UISwipeActionsConfiguration`.
2. Cree una instancia del `UISwipeActionsConfiguration` que se va a devolver. Esta clase toma una matriz de `UIContextualAction`.
3. Creará un control `UIContextualAction`.

Estos se explican con mayor detalle en las secciones siguientes.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. implementación de los métodos SwipeActionsConfigurations

`UITableViewController` (y también `UITableViewSource` y `UITableViewDelegate`) contienen dos métodos: `GetLeadingSwipeActionsConfiguration` y `GetTrailingSwipeActionsConfiguration`, que se usan para implementar un conjunto de acciones de deslizar rápidamente en una fila de la vista de tabla. La acción de deslizar rápidamente hace referencia a un dedo desde el lado izquierdo de la pantalla en un idioma que se escribe de izquierda a derecha y desde el lado derecho de la pantalla en un idioma que se escribe de derecha a izquierda.

En el ejemplo siguiente (del ejemplo [TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions) ) se muestra la implementación de la configuración de deslizamiento inicial. Se crean dos acciones a partir de las acciones contextuales, que se explican [a continuación](#create-uicontextualaction). Después, estas acciones se pasan a un [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations)recién inicializado, que se utiliza como valor devuelto.

```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });

    leadingSwipe.PerformsFirstActionWithFullSwipe = false;

    return leadingSwipe;
}
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. crear una instancia de un `UISwipeActionsConfiguration`

Cree una instancia de un `UISwipeActionsConfiguration` mediante el método `FromActions` para agregar una nueva matriz de `UIContextualAction`s, como se muestra en el siguiente fragmento de código:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Es importante tener en cuenta que el orden en que se muestran las acciones depende de cómo se pasen a la matriz. Por ejemplo, el código anterior de los deslizamientos iniciales muestra las acciones como se indica a continuación:

![Acciones de deslizamiento iniciales mostradas en una fila de la tabla](row-action-images/action03.png)

En el caso de los deslizamientos finales, las acciones se mostrarán como se muestra en la siguiente imagen:

![Acciones de deslizamiento hacia la derecha mostradas en una fila de la tabla](row-action-images/action04.png)

Este fragmento de código también hace uso de la nueva propiedad `PerformsFirstActionWithFullSwipe`. De forma predeterminada, esta propiedad se establece en true, lo que significa que la primera acción de la matriz se producirá cuando un usuario se deslice por completo en una fila. Si tiene una acción que no es destructiva (por ejemplo, "eliminar", es posible que no sea el comportamiento ideal y, por tanto, debe establecerla en `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Cree un control `UIContextualAction`

La acción contextual es donde realmente se crea la acción que se mostrará cuando el usuario Deslice el dedo por una fila de la tabla.

Para inicializar una acción, debe proporcionar un `UIContextualActionStyle`, un título y un `UIContextualActionHandler`. La `UIContextualActionHandler` toma tres parámetros: una acción, la vista en la que se mostró la acción y un controlador de finalización:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null));
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);

                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Se pueden editar varias propiedades visuales, como el color de fondo o la imagen de la acción. El fragmento de código anterior muestra cómo agregar una imagen a la acción y establecer el color de fondo en azul.

Una vez creadas las acciones contextuales, pueden usar para inicializar el `UISwipeActionsConfiguration` en el método `GetLeadingSwipeActionsConfiguration`.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir una o varias acciones de fila personalizadas para un `UITableView`, debe crear una instancia de la clase `UITableViewDelegate` e invalidar el método `EditActionsForRow`. Por ejemplo:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

El método estático de `UITableViewRowAction.Create` se usa para crear un nuevo `UITableViewRowAction` que mostrará un botón de **HI** cuando el usuario se deslice horizontalmente a la izquierda en una fila de la tabla. Posteriormente, se crea una nueva instancia de la `TableDelegate` y se adjunta al `UITableView`. Por ejemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Cuando se ejecuta el código anterior y el usuario se desliza a la izquierda en una fila de la tabla, se muestra el botón **HI** en lugar del botón **Delete** que se muestra de forma predeterminada:

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

Si el usuario pulsa el botón **HI** , `Hello World!` se escribirá en la consola de en Visual Studio para Mac o Visual Studio cuando la aplicación se ejecute en el modo de depuración.

## <a name="related-links"></a>Vínculos relacionados

- [TableSwipeActions (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
