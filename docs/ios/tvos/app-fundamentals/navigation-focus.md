---
title: Trabajar con la navegación y el foco de tvOS en Xamarin
description: En este artículo se describe el concepto de enfoque y cómo se usa para presentar y controlar la navegación dentro de una aplicación Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: b84150c8fdca04a9b21c28d126d221d2a9bc5a3f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769261"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Trabajar con la navegación y el foco de tvOS en Xamarin

_En este artículo se describe el concepto de enfoque y cómo se usa para presentar y controlar la navegación dentro de una aplicación Xamarin. tvOS._

En este artículo se describe el concepto de [enfoque](#Focus-and-Selection) y cómo se usa para controlar la [navegación](#Navigation) en una interfaz de usuario de la aplicación Xamarin. tvOS. Veremos cómo los controles de navegación tvOS integrados usan el foco, el resaltado y la selección para proporcionar la navegación de la interfaz de usuario de la aplicación Xamarin. tvOS.

[![](navigation-focus-images/intro01.png "Navegación por la interfaz de usuario de tvOS apps")](navigation-focus-images/intro01.png#lightbox)

A continuación, echaremos un vistazo a cómo se puede usar el enfoque con imágenes de [Parallax](#Focus-and-Parallax) y *capas* para proporcionar pistas visuales para el estado de navegación actual al usuario final.

Por último, veremos trabajar con el [foco](#Working-with-Focus), [las actualizaciones de enfoque, las](#Working-with-Focus-Updates) [guías de enfoque](#Working-with-Focus-Guides), el [foco en colecciones](#Working-with-Focus-in-Collections) y la [habilitación de Parallax](#enabling-parallax) en las vistas de imagen en las aplicaciones de Xamarin. tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navegación

Los usuarios de la aplicación de Xamarin. tvOS no interactuarán con su interfaz directamente como con iOS, donde tocan imágenes en la pantalla del dispositivo, pero de forma indirecta desde el salón mediante el uso [remoto de Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Tendrá que tener esto en cuenta al diseñar la interfaz de usuario de la aplicación para que fluya de forma natural, pero mantenga el usuario sumergido en la experiencia de Apple TV.

Una aplicación tvOS correcta implementa la navegación de forma que admite sin problemas el propósito de la aplicación y la estructura de los datos que presenta sin llamar la atención a la propia navegación. Diseñe la navegación para que se sienta natural y familiar sin dominar la interfaz de usuario o dibujar el foco fuera del contenido y de la experiencia del usuario de las aplicaciones.

[![](navigation-focus-images/nav01.png "La aplicación de configuración de tvOS")](navigation-focus-images/nav01.png#lightbox)

Mientras se usa Apple TV, el usuario suele navegar por un conjunto de pantallas apiladas, cada una de las cuales presenta un conjunto de contenido determinado. A su vez, cada nueva pantalla puede conducir a una o varias pantallas secundarias de contenido mediante controles de interfaz de usuario estándar como [botones](~/ios/tvos/user-interface/buttons.md), [barras de pestañas](~/ios/tvos/user-interface/tab-bars.md), tablas, [vistas de colección](~/ios/tvos/user-interface/collection-views.md) o [vistas divididas](~/ios/tvos/user-interface/split-views.md).

Con cada nueva pantalla de datos, el usuario navega más en esta pila de pantallas. Mediante el botón de **menú** del control remoto Siri, pueden navegar hacia atrás por la pila para volver a una pantalla o menú principal anterior.

Apple sugiere tener en cuenta lo siguiente al diseñar la navegación de la aplicación tvOS:

- **Diseño de la navegación para que la búsqueda de contenido sea rápida y fácil** : los usuarios quieran acceder al contenido dentro de la aplicación en el menor número de pulsaciones, clics y deslizar el dedo como sea posible. Simplifique la navegación y organice el contenido en el número mínimo de pantallas.
- **Cree una interfaz fluida mediante Touch** : Asegúrese de que un usuario puede desplazarse entre _los elementos_ que pueden recibir el foco con una fricción mínima usando el menor número posible de gestos.
- **Diseño pensando en el foco** : dado que el usuario está interactuando con el contenido de la habitación, debe trasladar el foco a un elemento de la interfaz de usuario antes de interactuar con él mediante el Siri remoto. Los usuarios se sienten frustrados con la aplicación si requieren demasiados gestos para lograr sus objetivos.
- **Proporcionar navegación hacia atrás a través del botón de menú** : para crear una experiencia fácil y familiar, permita que los usuarios naveguen hacia atrás mediante el botón de **menú** de Siri remoto. Presionar el botón de **menú** siempre debe volver a la pantalla anterior o volver al menú principal de la aplicación. En el nivel superior de la aplicación, al presionar el botón de **menú** debe volver a la pantalla principal de Apple TV.
- **Normalmente, evite mostrar un botón atrás** , ya que si presiona el botón de **menú** en el control remoto de Siri navega hacia atrás a través de la pila de pantalla, evite mostrar un control adicional que duplique este comportamiento. Una excepción a esta regla es para la compra de pantallas o pantallas con acciones destructivas (como la eliminación de contenido) donde se debe mostrar también un botón **Cancelar** .
- **Mostrar colecciones grandes en una sola pantalla, en lugar de muchas** , el control remoto Siri se diseñó para facilitar el traslado a través de una gran colección de contenido con movimientos sencillos. Si la aplicación funciona con una gran colección de elementos que puedan recibir el foco, considere la posibilidad de mantenerlos en una sola pantalla en lugar de dividirlos en muchas pantallas que requieran más navegación por la parte del usuario.
- **Use controles estándar para la navegación** de nuevo para crear una experiencia de usuario sencilla y familiar, siempre que sea posible, use `UIKit` controles integrados como controles de página, barras de pestañas, controles segmentados, vistas de tabla, vistas de colección y vistas divididas para navegación de la aplicación. Puesto que el usuario ya está familiarizado con estos elementos, puede navegar de manera intuitiva por la aplicación.
- **Favorecer la navegación de contenido horizontal** : debido a la naturaleza de Apple TV, el deslizamiento de izquierda a derecha en el Siri remoto es más natural que el de arriba y abajo. Tenga en cuenta esta opción al diseñar diseños de contenido para la aplicación.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Foco y selección

En Apple TV, se considera que una imagen, un botón o cualquier otro elemento de la interfaz de usuario está _en el foco_ cuando es el destino de la navegación actual.

[![](navigation-focus-images/focus01.png "Ejemplo de enfoque y selección")](navigation-focus-images/focus01.png#lightbox)

A diferencia de los dispositivos iOS en los que el usuario está interactuando directamente con los elementos de la pantalla táctil del dispositivo, los usuarios interactúan con los elementos de tvOS desde el salón mediante el uso remoto de Siri. Para presentar y controlar esta interacción del usuario, Apple TV usa un modelo basado en el _enfoque_ .

Con los gestos y las pulsaciones de botón en el [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), el usuario mueve el foco de un elemento de la interfaz de usuario a otro. Una vez que el foco se ha desplazado al elemento deseado, el usuario hace clic para seleccionar el contenido o activa la acción representada por ese elemento.

A medida que cambian los focos, se usan animaciones y efectos sutiles (como el efecto de Parallax en las imágenes) para identificar claramente el elemento de la interfaz de usuario que tiene el foco actualmente.

Apple tiene las siguientes sugerencias para trabajar con el foco y la selección:

- **Usar controles de IU integrados para los efectos de movimiento** : `UIKit` mediante y la API de foco en la interfaz de usuario, el modelo de foco aplicará automáticamente los efectos visuales y de movimiento predeterminados a los elementos de la interfaz de usuario. Esto hace que su aplicación sea nativa y familiar a los usuarios de la plataforma Apple TV, y permite el movimiento fluido e intuitivo entre los elementos que pueden recibir el foco.
- **Cambiar el foco en direcciones esperadas** : en Apple TV, casi todos los elementos usan la manipulación indirecta. Por ejemplo, el usuario usa el control remoto Siri para trasladar el foco y el sistema desplaza automáticamente la interfaz para mantener visible el elemento que tiene actualmente el foco. Si la aplicación implementa este tipo de interacción, asegúrese de que el foco se mueve en la dirección del gesto del usuario. Por tanto, si el usuario que se desplaza hacia la derecha en el enfoque remoto de Siri debe moverse a la derecha (lo que podría hacer que la pantalla se desplace a la izquierda). La única excepción a esta regla son los elementos de pantalla completa que usan la manipulación directa (donde el deslizamiento hacia arriba sube el elemento).
- **Asegúrese de que el elemento con el foco sea obvio** : dado que los usuarios interactúan con los elementos de la interfaz de usuario desde Afar, es fundamental que el elemento actualmente enfocado se destaque. Normalmente, se controlará automáticamente mediante `UIKit` elementos integrados. En el caso de los controles personalizados, use características como el tamaño o la sombra del elemento para mostrar el foco.
- **Use Parallax para hacer que los elementos con foco respondan** y los gestos circulares en el Siri remoto tengan como resultado un movimiento suave y en tiempo real del elemento enfocado. Este [efecto de Parallax](#Focus-and-Parallax) está integrado `UIKit` en _las imágenes superpuestas_ para proporcionar al usuario una sensación de conexión con el elemento con el foco.
- **Crear elementos enfocables del tamaño adecuado** : los elementos grandes con un espaciado amplio son más fáciles de seleccionar y navegar que los elementos más pequeños.
- **Diseñe el elemento de la interfaz de usuario para que tenga el foco enfocado o** no, por lo general, Apple TV representa el elemento enfocado aumentando su tamaño. Asegúrese de que los elementos de la interfaz de usuario de la aplicación tengan un aspecto excelente en cualquier tamaño de presentación y, si es necesario, proporcione recursos para elementos de mayor tamaño.
- **Representar los cambios de foco** de forma fluida: Use la animación para atenuar suavemente entre un estado **centrado** y sin **foco** para evitar que las transiciones se discordante.
- **No mostrar un cursor** : los usuarios esperan navegar por la interfaz de usuario de la aplicación con el foco y no moviendo un cursor alrededor de la pantalla. La interfaz de usuario siempre debe usar el modelo de enfoque para presentar una experiencia de usuario coherente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Trabajar con el foco

Puede haber ocasiones en las que desee crear un control personalizado que puede convertirse en un elemento que pueda recibir el foco. Si es así, `CanBecomeFocused` invalide `true`la propiedad y `false`devuelva. de lo contrario, devuelve. Por ejemplo:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

En cualquier momento puede usar la `Focused` propiedad de un control para ver si es un `UIKit` elemento actual. Si `true` el elemento de la interfaz de usuario tiene actualmente el foco, de lo contrario, no lo hace. Por ejemplo:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Aunque no puede cambiar directamente el foco a otro elemento de la interfaz de usuario a través de código, puede especificar qué elemento de la interfaz de usuario obtiene `PreferredFocusedView` primero el `true`foco cuando se carga una pantalla estableciendo su propiedad en. Por ejemplo:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Trabajar con actualizaciones de foco 

Cuando el usuario hace que el foco cambie de un elemento de la interfaz de usuario a otro (por ejemplo, en respuesta a un gesto en el Siri remoto), se enviará un _evento de actualización del foco_ al elemento que pierde el foco y el elemento obtiene el foco.

En el caso de los elementos `UIView` personalizados `UIViewController`que heredan de o, puede invalidar varios métodos para trabajar con el evento de actualización de foco:

- **DidUpdateFocus** : se llamará a este método cada vez que la vista gane o pierda el foco.
- **ShouldUpdateFocus** : Use este método para definir dónde se puede desplazar el foco.

Para solicitar que el motor de foco vuelva a mover el `PreferredFocusedView` foco al elemento de la `SetNeedsUpdateFocus` interfaz de usuario, llame al método del controlador de vista.

> [!IMPORTANT]
> La `SetNeedsUpdateFocus` llamada a solo tiene efecto si el controlador de vista al que se está llamando contiene la vista que tiene el foco actualmente.

<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Trabajar con guías de foco

El motor de enfoque integrado en tvOS es excelente en el control de movimientos entre elementos que se encuentran en una cuadrícula horizontal y vertical. Normalmente, no es necesario hacer nada más que agregar los elementos de la interfaz de usuario al diseño de la interfaz y el motor de foco controlará automáticamente el movimiento entre esos elementos sin intervención del desarrollador.

Sin embargo, puede haber ocasiones, debido a la necesidades del diseño de la interfaz de usuario, cuando los elementos de la interfaz de usuario no se encuentran en una cuadrícula horizontal y vertical y podrían ser inaccesibles porque son diagonales entre sí. Esto ocurre porque el motor de enfoque estaba diseñado para controlar el movimiento sencillo, inactivo, izquierdo y derecho entre los elementos de la interfaz de usuario solamente.

Tome el siguiente diseño de la interfaz de usuario para obtener un ejemplo:

 [![](navigation-focus-images/guide01.png "Ejemplo de cómo trabajar con guías de enfoque")](navigation-focus-images/guide01.png#lightbox)

Dado que el botón **más información** no está en una cuadrícula horizontal y vertical con el botón **comprar** , el usuario no podrá tener acceso a él. Sin embargo, esto se puede corregir fácilmente mediante una _Guía de enfoque_ para proporcionar sugerencias de movimiento al motor de foco. 

Una guía de enfoque`UIFocusGuide`() expone un área no visible de la vista como enfocable al motor de foco, lo que permite redirigir el foco a otra vista.

Por lo tanto, para resolver el ejemplo anterior, se podría agregar el código siguiente al controlador de vista para crear una guía de enfoque entre los botones **más información** y **comprar** :

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

En primer lugar, `UIFocusGuide` se crea un nuevo y se agrega a la colección de guía de `AddLayoutGuide` diseño de la vista mediante el método.

Después, los delimitadores superior, izquierdo, ancho y alto de la guía de enfoque se ajustan en relación con los botones **más información** y **comprar** para colocarlos entre ellos. Vea:

[![](navigation-focus-images/guide02.png "Guía de enfoque de ejemplo")](navigation-focus-images/guide02.png#lightbox)

También es importante tener en cuenta que las nuevas restricciones se activan a medida que se crean estableciendo su `Active` propiedad en: `true`

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la nueva guía de enfoque establecida y agregada a la vista, se puede `DidUpdateFocus` invalidar el método del controlador de vista y agregar el código siguiente para desplazarse entre los botones **más información** y **comprar** :

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

En primer lugar, este código obtiene `NextFocusedView` el del`context` queseha`UIFocusUpdateContext` pasado (). Si esta vista es `null`, no se necesita ningún procesamiento y el método salió.

A continuación, `nextFocusableItem` se evalúa. Si coincide con los botones **más información** o **comprar** , el foco se envía al botón opuesto mediante la propiedad de la guía `PreferredFocusedView` de enfoque. Por ejemplo:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

En caso de que ninguno de los botones sea el origen del cambio de foco `PreferredFocusedView` , se borra la propiedad:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Trabajar con el foco en colecciones

A `UICollectionView` la `UITableView` `UITableViewDelegate` hora de decidir si un elemento individual puede ser enfocable en o en, invalidará los métodos de o respectivamente. `UICollectionViewDelegate` Por ejemplo:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

El `CanFocusItem` método devuelve `true` si el elemento actual puede tener el foco; de lo contrario `false`, devuelve.

`UICollectionView` Si desea `UITableView` que o recuerde y restaure el foco al último elemento cuando pierde y vuelve a obtener el foco, establezca la `RemembersLastFocusedIndexPath` propiedad en `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Focus y Parallax

Como se indicó anteriormente, se considera que un elemento de la interfaz de usuario está _en el foco_ cuando es el elemento actual durante un evento de navegación. Normalmente, a medida que un elemento entra en el foco, su tamaño se incrementa ligeramente y se eleva visualmente con una sombra. 

Si el usuario realiza un gesto circular lento en el Siri remoto, el elemento con el foco se verá en tiempo real en respuesta a este movimiento. A medida que se produce el Sway, se aplica un brillo iluminado a su imagen, lo que hará que la superficie parezca brillante. Después de una cantidad determinada de inactividad, el contenido desenfocado se atenúa y el elemento enfocado crecerá aún más. 

Estos efectos funcionan juntos para proporcionar una conexión mental entre el contenido de la pantalla de TV y el usuario que interactúa con Apple TV desde el sofá.

En Apple TV, este efecto de Parallax se usa en todo el sistema para transmitir una sensación de profundidad 3D y de dinámica a los elementos enfocados. tvOS usa una serie de imágenes transparentes y en [capas](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) que se mueven y escalan dinámicamente para crear este efecto.

Las imágenes superpuestas son necesarias para el icono de la aplicación de tvOS y se admiten para el contenido dinámico de estante superior. Aunque no es necesario, Apple sugiere encarecidamente la implementación de imágenes superpuestas en cualquier otro elemento que pueda recibir el foco en la interfaz de usuario de la aplicación.

### <a name="enabling-parallax"></a>Habilitar Parallax

El `UIImageView` control (o cualquier control que herede de `UIImageView`) admite automáticamente el efecto parallax. De forma predeterminada, esta compatibilidad está deshabilitada. para habilitarla, use el código siguiente:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Con esta propiedad establecida en `true`, la vista de imagen obtendrá automáticamente el efecto parallax cuando lo seleccione el usuario y el foco.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el concepto de enfoque y cómo se usa para controlar la navegación en una interfaz de usuario de la aplicación Xamarin. tvOS. Se examina cómo los controles de navegación tvOS integrados usan el foco, el resaltado y la selección para proporcionar la navegación. A continuación, se ha examinado cómo se puede usar el enfoque con imágenes de Parallax y capas para proporcionar pistas visuales para el estado de navegación actual al usuario final. Por último, examinó el trabajo con el foco, las actualizaciones de enfoque, el foco en colecciones y la habilitación de Parallax.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
