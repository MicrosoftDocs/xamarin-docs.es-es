---
title: Resumen del capítulo 24. Navegación de páginas
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 24. Navegación de páginas'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 340aa8c7327ba75986b960d5e0f6bda66c116fc5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770926"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumen del capítulo 24. Navegación de páginas

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muchas aplicaciones constan de varias páginas, entre los que el usuario se desplaza. La aplicación siempre tiene un *principal* página o *principal* página, y a partir de ahí el usuario navega a otras páginas, que se mantienen en una pila para desplazarse hacia atrás. Otras opciones de navegación se tratan en [ **capítulo 25. Página variedades**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modales y no modales

`VisualElement` define un [ `Navigation` ](xref:Xamarin.Forms.NavigableElement.Navigation) propiedad de tipo [ `INavigation` ](xref:Xamarin.Forms.INavigation), que incluye los dos métodos siguientes para navegar a una página nueva:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Ambos métodos aceptan un `Page` instancia como un argumento y devuelven un `Task` objeto. Los dos métodos siguientes que se vaya a la página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si la interfaz de usuario tiene su propio **volver** botón (como los teléfonos Android y Windows), no es necesario para la aplicación a llamar a estos métodos.

Aunque estos métodos están disponibles desde cualquier `VisualElement`, por lo general, se les llama desde el `Navigation` propiedad del elemento actual `Page` instancia.

Por lo general, las aplicaciones usar páginas modales cuando el usuario tiene que proporcionar cierta información en la página antes de volver a la página anterior. Las páginas que no son modales a veces se denominan no modales o *jerárquica*. No hay nada en la página en sí distingue como modal o no modal; en su lugar se rige por el método utilizado para navegar hasta él. Para que funcione en todas las plataformas, una página modal debe proporcionar su propia interfaz de usuario para navegar a la página anterior.

El [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) ejemplo permite explorar la diferencia entre las páginas modales y no modales. Cualquier aplicación que utiliza la navegación de página debe pasar su página principal para el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) constructor, por lo general en el programa `App` clase. Una ventaja es que ya no necesita establecer un `Padding` en la página de iOS.

Descubrirá que no modal las páginas, la página de [ `Title` ](xref:Xamarin.Forms.Page.Title) se muestra la propiedad. IOS, Android y las plataformas de Tablet PC y escritorio de Windows proporcionan un elemento de interfaz de usuario para desplazarse a la página anterior. Del curso, Android y Windows, dispositivos de teléfono tienen un estándar **volver** botón para volver atrás.

Las páginas modales, la página `Title` no se muestra, y no se proporciona ningún elemento de interfaz de usuario para volver a la página anterior. Aunque puede usar el estándar de Android y Windows phone **volver** botón para volver a la página anterior, la página modal en las otras plataformas debe proporcionar su propio mecanismo para volver atrás.

### <a name="animated-page-transitions"></a>Transiciones de página animado

Se proporcionan versiones alternativas de los distintos métodos de navegación con un segundo argumento booleano que se establece en `true` si desea que la transición de página para incluir una animación:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Sin embargo, los métodos de navegación de página estándar incluyen la animación de forma predeterminada, por lo que solo son valiosos para navegar a una página determinada en el inicio (como se describe al final de este capítulo) o al proporcionar su propia animación de entrada (como se describe en [ **Chapter22. Animación**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variaciones visuales y funcionales

`NavigationPage` incluye dos propiedades que se pueden establecer al crear instancias de la clase en su `App` método:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` También incluye cuatro propiedades enlazables adjuntas que afectan a la página correspondiente en el que se establecen:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) y [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) y [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) y [ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) profesional solo en iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.ImageSource)) y [ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funcionan en solo iOS y Android

### <a name="exploring-the-mechanics"></a>Explorar la mecánica

Los métodos de navegación de página son completamente asincrónicos y debe usarse con `await`. La finalización no indica que navegación de página se ha completado, pero solo que es seguro examinar la pila de navegación de páginas.

Cuando se desplaza una página a otra, la primera página normalmente recibe una llamada a su [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) método y la segunda página recibe una llamada a su [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) método. De forma similar, cuando se devuelve una página a otra, la primera página recibe una llamada a su `OnDisappearing` método y la segunda página generalmente recibe una llamada a su `OnAppearing` método. El orden de estas llamadas (y la finalización de los métodos asincrónicos que invoca el panel de navegación) es dependiente de la plataforma. El uso de la palabra "general" de las dos instrucciones anteriores es debido a la navegación de páginas modal Android, en el que no se producen estas llamadas al método.

Además, las llamadas a la `OnAppearing` y `OnDisappearing` métodos no indican necesariamente la navegación de páginas.

El `INavigation` interfaz incluye dos propiedades de colección que permiten examinar la pila de navegación:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) de tipo `IReadOnlyList<Page>` para la pila no modal
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) de tipo `IReadOnlyList<Page>` para la pila modal

Resulta más seguro tener acceso a estas pilas de la `Navigation` propiedad de la `NavigationPage` (que debe ser el `App` la clase [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad). Solo es seguro examinar estas pilas después de han completado los métodos de navegación de página asincrónica. El [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage) propiedad de la `NavigationPage` no indica la página actual si la página actual es una página modal, pero indica en su lugar la última página no modal.

El [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) ejemplo le permite explorar la navegación de páginas y las pilas y los tipos válidos de las navegaciones de página:

- Una página no modal puede navegar a otra página no modal o una página modal
- Una página modal solo puede navegar a otra página modal

### <a name="enforcing-modality"></a>Exigir la modalidad

Una aplicación utiliza una página modal cuando resulta necesario obtener la información del usuario. El usuario debe prohibirse devuelvan a la página anterior hasta que se proporcione esa información. En iOS, es fácil proporcionar un **volver** botón y solo lo habilite cuando el usuario ha terminado con la página. Pero para dispositivos de Android y Windows phone, la aplicación debe invalidar el [ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) método y devolver `true` si se ha controlado el programa la **volver** botón Sí, como se muestra en el [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) ejemplo.

El [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) ejemplo muestra cómo funciona esto en un escenario MVVM.

## <a name="navigation-variations"></a>Variaciones de navegación

Si una determinada página modal se puede navegar a varias veces, debe conservar la información para que el usuario puede editar la información en lugar de ello, escríbalo todo de nuevo. Puede controlar esto manteniendo la instancia concreta de la página modal, pero un mejor enfoque (especialmente en iOS) conserva la información de un modelo de vista.

### <a name="making-a-navigation-menu"></a>Hacer que un menú de navegación

El [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) ejemplo muestra cómo utilizar un `TableView` para enumerar los elementos de menú. Cada elemento está asociado con un `Type` objeto para una página determinada. Cuando se selecciona ese elemento, el programa crea una instancia de la página y navega a ella.

[![Captura de pantalla triple del tipo de vista de galería](images/ch24fg21-small.png "TableView enumerar los elementos de menú")](images/ch24fg21-large.png#lightbox "TableView enumerar los elementos de menú")

El [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) ejemplo es un poco diferente, el menú contiene las instancias de cada página en lugar de tipos. Esto ayuda a conservar la información de cada página, pero se deben crear instancias de todas las páginas al inicio del programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) muestra varias funciones definidas por `INavigation` que permiten manipular la pila de navegación de una manera estructurada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) y [ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animación opcional

### <a name="dynamic-page-generation"></a>Generación de páginas dinámicas

El [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) muestra la creación de una página en tiempo de ejecución basándose en la entrada del usuario.

## <a name="patterns-of-data-transfer"></a>Patrones de transferencia de datos

A menudo es necesario compartir datos entre páginas &mdash; para transferir datos a una página navegar y en una página devolver datos a la página que lo invocó. Existen varias técnicas para hacerlo.

### <a name="constructor-arguments"></a>Argumentos de constructor

Al navegar a una página nueva, es posible crear una instancia de la clase de página con un argumento de constructor que permite que la página que se inicialice. El [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) muestra esto. También es posible que la página de navegar a tener su `BindingContext` establecido por la página que navega a ella.

### <a name="properties-and-method-calls"></a>Propiedades y llamadas a métodos

Los ejemplos de transferencia de datos restantes exploran el problema de pasar información entre páginas cuando se desplaza una página a otra página y viceversa. En estas conversaciones, el *principal* página navega a la *información* página y debe transferir la información inicializado el *información* página. El *información* página obtiene información adicional del usuario y transfiere la información a la *principal* página.

El *principal* página puede acceder fácilmente a los métodos públicos y propiedades en el *información* página tan pronto como crea una instancia de esa página. El *información* página también puede tener acceso a los métodos públicos y propiedades en el *principal* página, pero la elección de un buen momento para que esto puede ser complicado. El [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) ejemplo hace esto en su `OnDisappearing` invalidar. Una desventaja es que el *información* debe saber el tipo de página el *principal* página.

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase proporciona otra manera de dos páginas para comunicarse entre sí. Los mensajes se identifican mediante una cadena de texto y pueden adjuntarse a cualquier objeto.

Un programa que desea recibir mensajes de un tipo determinado debe suscribirse a ellos mediante [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) y especificar una función de devolución de llamada. Más adelante puede cancelar la suscripción mediante una llamada a [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La función de devolución de llamada recibe los mensajes enviados desde el tipo especificado con el nombre especificado que se envían a través de la [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método.

El [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programa muestra cómo transferir datos con el centro de mensajería, pero nuevamente, esto requiere que el *información* página conoce el tipo de la *doméstica* página.

### <a name="events"></a>Eventos

El evento es un enfoque útil para una clase enviar información a otra clase sin conocer el tipo de la clase. En el [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) ejemplo el *información* clase define un evento que activa cuando la información esté lista. Sin embargo, no hay ningún lugar conveniente para el *principal* página desasociar el controlador de eventos.

### <a name="the-app-class-intermediary"></a>El intermediario de clase de aplicación

El [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) ejemplo muestra cómo obtener acceso a las propiedades definidas en el `App` clase tanto el *principal* página y el *información*página. Esto es una buena solución, pero la siguiente sección describe algo mejor.

### <a name="switching-to-a-viewmodel"></a>Cambiar a una clase ViewModel

El uso de un modelo de vista para la información que permite la *principal* página y el *información* página para compartir la instancia de la clase de información. Esto se muestra en el [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) ejemplo.

### <a name="saving-and-restoring-page-state"></a>Guardar y restaurar el estado de la página

El `App` intermediario de clase o el enfoque de ViewModel es ideal cuando la aplicación debe guardar la información si el programa entra en suspensión mientras el *información* página está activa. El [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) muestra esto.

## <a name="saving-and-restoring-the-navigation-stack"></a>Guardado y restauración de la pila de navegación

Por lo general, un programa de varias páginas que entra en suspensión debería navegar a la misma página cuando se restaura. Esto significa que este tipo de programa debería guardar el contenido de la pila de navegación. En esta sección se muestra cómo automatizar este proceso en una clase diseñada para este propósito. Esta clase también llama a las páginas individuales para que puedan guardar y restaurar su estado de la página.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define una interfaz denominada [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que las clases pueden implementar para guardar y restaurar los elementos en el `Properties`diccionario.

El [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) clase en el **Xamarin.FormsBook.Toolkit** deriva de la biblioteca `Application`. A continuación, puede derivar su `App` clase `MultiPageRestorableApp` y realizar algunas tareas de mantenimiento.

El [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) muestra el uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo como una aplicación de la vida real

El [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) ejemplo también usa `MultiPageRestorableApp` y permiten escribir y editar notas de la que se guardan en el `Properties` diccionario.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 24 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capítulo 24 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
