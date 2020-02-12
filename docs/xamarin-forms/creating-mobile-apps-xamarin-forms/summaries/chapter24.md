---
title: Resumen del capítulo 24. Navegación de páginas
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 24. Navegación de páginas'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77130847"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumen del capítulo 24. Navegación de páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muchas aplicaciones constan de varias páginas, entre los que el usuario se desplaza. La aplicación siempre tiene una página *principal* o *una página principal y* , desde allí, el usuario navega a otras páginas, que se mantienen en una pila para desplazarse hacia atrás. En el capítulo 25 se describen otras opciones de navegación [ **. Variedades de páginas**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modales y no modales

`VisualElement` define una propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de tipo [`INavigation`](xref:Xamarin.Forms.INavigation), que incluye los dos métodos siguientes para navegar a una nueva página:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Ambos métodos aceptan una instancia de `Page` como argumento y devuelven un objeto `Task`. Los dos métodos siguientes que se vaya a la página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si la interfaz de usuario tiene su propio botón **atrás** (como lo hacen Android y Windows Phone), no es necesario que la aplicación llame a estos métodos.

Aunque estos métodos están disponibles en cualquier `VisualElement`, por lo general se les llama desde la propiedad `Navigation` de la instancia de `Page` actual.

Por lo general, las aplicaciones usar páginas modales cuando el usuario tiene que proporcionar cierta información en la página antes de volver a la página anterior. Las páginas que no son modales a veces se denominan modelo no modal o *jerárquica*. No hay nada en la página en sí distingue como modal o no modal; en su lugar se rige por el método utilizado para navegar hasta él. Para que funcione en todas las plataformas, una página modal debe proporcionar su propia interfaz de usuario para navegar a la página anterior.

El ejemplo [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) permite explorar la diferencia entre las páginas modales y las páginas modales. Cualquier aplicación que use la navegación por la página debe pasar su página principal al constructor [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , generalmente en la clase `App` del programa. Una ventaja es que ya no es necesario establecer un `Padding` en la página para iOS.

Descubrirá que, en el caso de las páginas no modales, se muestra la propiedad [`Title`](xref:Xamarin.Forms.Page.Title) de la página. IOS, Android y las plataformas de Tablet PC y escritorio de Windows proporcionan un elemento de interfaz de usuario para desplazarse a la página anterior. Por supuesto, los dispositivos Android y Windows Phone tienen un botón **atrás** estándar para volver.

En el caso de las páginas modales, no se muestra la página `Title` y no se proporciona ningún elemento de la interfaz de usuario para volver a la página anterior. Aunque puede usar el botón **atrás** de Android y Windows Phone Standard para volver a la página anterior, la página modal de las otras plataformas debe proporcionar su propio mecanismo para volver.

### <a name="animated-page-transitions"></a>Transiciones de página animado

Se proporcionan versiones alternativas de los distintos métodos de navegación con un segundo argumento booleano que se establece en `true` si desea que la transición de la página incluya una animación:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Sin embargo, los métodos de navegación de página estándar incluyen la animación de forma predeterminada, por lo que solo son valiosos para navegar a una página determinada en el inicio (como se describe en el final de este capítulo) o al proporcionar su propia animación de entrada (como se describe en [**Chapter22. Animación**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variaciones visuales y funcionales

`NavigationPage` incluye dos propiedades que se pueden establecer al crear una instancia de la clase en el método `App`:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` también incluye cuatro propiedades enlazables asociadas que afectan a la página concreta en la que se establecen:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) y [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) y [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) y [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funcionan solo en iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) y [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funcionan solo en iOS y Android

### <a name="exploring-the-mechanics"></a>Explorar la mecánica

Los métodos de navegación de página son todos asincrónicos y deben usarse con `await`. La finalización no indica que navegación de página se ha completado, pero solo que es seguro examinar la pila de navegación de páginas.

Cuando una página navega a otra, la primera página normalmente recibe una llamada a su método [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) y la segunda página obtiene una llamada a su método [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) . De forma similar, cuando una página vuelve a otra, la primera página obtiene una llamada a su método `OnDisappearing` y la segunda página normalmente recibe una llamada a su método `OnAppearing`. El orden de estas llamadas (y la finalización de los métodos asincrónicos que invoca el panel de navegación) es dependiente de la plataforma. El uso de la palabra "general" de las dos instrucciones anteriores es debido a la navegación de páginas modal Android, en el que no se producen estas llamadas al método.

Además, las llamadas a los métodos `OnAppearing` y `OnDisappearing` no indican necesariamente la navegación por la página.

La interfaz de `INavigation` incluye dos propiedades de colección que permiten examinar la pila de navegación:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) de tipo `IReadOnlyList<Page>` para la pila no modal
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) de tipo `IReadOnlyList<Page>` para la pila modal

Es más seguro acceder a estas pilas desde la propiedad `Navigation` del `NavigationPage` (que debe ser la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App`). Solo es seguro examinar estas pilas después de han completado los métodos de navegación de página asincrónica. La propiedad [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) del `NavigationPage` no indica la página actual si la página actual es una página modal, sino que indica en su lugar la última página no modal.

El ejemplo [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) permite explorar la navegación por las páginas y las pilas, así como los tipos válidos de navegación por las páginas:

- Una página no modal puede navegar a otra página no modal o una página modal
- Una página modal solo puede navegar a otra página modal

### <a name="enforcing-modality"></a>Exigir la modalidad

Una aplicación utiliza una página modal cuando resulta necesario obtener la información del usuario. El usuario debe prohibirse devuelvan a la página anterior hasta que se proporcione esa información. En iOS, es fácil proporcionar un botón **atrás** y habilitarlo solo cuando el usuario haya finalizado la página. Pero para dispositivos Android y Windows Phone, la aplicación debe invalidar el método [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) y devolver `true` si el programa ha controlado el propio botón **atrás** , tal como se muestra en el ejemplo [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

En el ejemplo [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) se muestra cómo funciona en un escenario de MVVM.

## <a name="navigation-variations"></a>Variaciones de navegación

Si una determinada página modal se puede navegar a varias veces, debe conservar la información para que el usuario puede editar la información en lugar de ello, escríbalo todo de nuevo. Puede controlar esto manteniendo la instancia concreta de la página modal, pero un mejor enfoque (especialmente en iOS) conserva la información de un modelo de vista.

### <a name="making-a-navigation-menu"></a>Hacer que un menú de navegación

En el ejemplo [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) se muestra cómo usar un `TableView` para enumerar los elementos de menú. Cada elemento está asociado a un `Type` objeto de una página determinada. Cuando se selecciona ese elemento, el programa crea una instancia de la página y navega a ella.

[![Captura de pantalla triple del tipo de galería de vistas](images/ch24fg21-small.png "Elementos de menú de lista de TableView")](images/ch24fg21-large.png#lightbox "Elementos de menú de lista de TableView")

El ejemplo [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) es un poco diferente en que el menú contiene instancias de cada página en lugar de tipos. Esto ayuda a conservar la información de cada página, pero se deben crear instancias de todas las páginas al inicio del programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) muestra varias funciones definidas por `INavigation` que permiten manipular la pila de navegación de una manera estructurada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) y [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animación opcional

### <a name="dynamic-page-generation"></a>Generación de páginas dinámicas

En el ejemplo [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) se muestra cómo crear una página en tiempo de ejecución en función de los datos proporcionados por el usuario.

## <a name="patterns-of-data-transfer"></a>Patrones de transferencia de datos

A menudo es necesario compartir datos entre páginas &mdash; para transferir datos a una página navegada y para que una página devuelva datos a la página que lo invocó. Existen varias técnicas para hacerlo.

### <a name="constructor-arguments"></a>Argumentos de constructor

Al navegar a una página nueva, es posible crear una instancia de la clase de página con un argumento de constructor que permite que la página que se inicialice. En el ejemplo [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) se muestra esto. También es posible que la página en la que se ha navegado tenga su `BindingContext` establecida por la página que navega a ella.

### <a name="properties-and-method-calls"></a>Propiedades y llamadas a métodos

Los ejemplos de transferencia de datos restantes exploran el problema de pasar información entre páginas cuando se desplaza una página a otra página y viceversa. En estas discusiones, la página *principal* navega a la página de *información* y debe transferir la información inicializada a la página de *información* . La página de *información* obtiene información adicional del usuario y la transfiere a la página *principal* .

La página *principal* puede acceder fácilmente a métodos y propiedades públicos en la página de *información* en cuanto cree una instancia de esa página. La página de *información* también puede tener acceso a métodos y propiedades públicos en la página *principal* , pero elegir un buen momento para esto puede ser complicado. En el ejemplo [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) se realiza esta invalidación `OnDisappearing`. Una desventaja es que la página de *información* necesita conocer el tipo de la página *principal* .

### <a name="messagingcenter"></a>MessagingCenter

La clase de [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin. Forms proporciona otra manera para que dos páginas se comuniquen entre sí. Los mensajes se identifican mediante una cadena de texto y pueden adjuntarse a cualquier objeto.

Un programa que desea recibir mensajes de un tipo determinado debe suscribirse a ellos mediante [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) y especificar una función de devolución de llamada. Posteriormente, puede cancelar la suscripción llamando a [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La función de devolución de llamada recibe cualquier mensaje enviado desde el tipo especificado con el nombre especificado enviado a través del método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) .

El programa [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) muestra cómo transferir datos mediante el centro de mensajería, pero de nuevo esto requiere que la página de *información* conozca el tipo de la página *principal* .

### <a name="events"></a>Eventos

El evento es un enfoque útil para una clase enviar información a otra clase sin conocer el tipo de la clase. En el ejemplo [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) , la clase *info* define un evento que se activa cuando la información está lista. Sin embargo, no hay ningún lugar adecuado para que la página *principal* desasocie el controlador de eventos.

### <a name="the-app-class-intermediary"></a>El intermediario de clase de aplicación

En el ejemplo [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) se muestra cómo obtener acceso a las propiedades definidas en la clase `App` tanto en la página *principal* como en la página de *información* . Esto es una buena solución, pero la siguiente sección describe algo mejor.

### <a name="switching-to-a-viewmodel"></a>Cambiar a una clase ViewModel

El uso de un ViewModel para la información permite que la página *principal* y *la página de información* compartan la instancia de la clase de información. Esto se muestra en el ejemplo [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Guardar y restaurar el estado de la página

El método intermediario de clase `App` o el enfoque ViewModel es idóneo cuando la aplicación debe guardar información si el programa entra en suspensión mientras la página de *información* está activa. En el ejemplo [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) se muestra esto.

## <a name="saving-and-restoring-the-navigation-stack"></a>Guardado y restauración de la pila de navegación

Por lo general, un programa de varias páginas que entra en suspensión debería navegar a la misma página cuando se restaura. Esto significa que este tipo de programa debería guardar el contenido de la pila de navegación. En esta sección se muestra cómo automatizar este proceso en una clase diseñada para este propósito. Esta clase también llama a las páginas individuales para que puedan guardar y restaurar su estado de la página.

La biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define una interfaz denominada [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que las clases pueden implementar para guardar y restaurar elementos en el Diccionario de `Properties`.

La clase [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) de la biblioteca **Xamarin. FormsBook. Toolkit** deriva de `Application`. A continuación, puede derivar la clase de `App` de `MultiPageRestorableApp` y realizar algunas tareas de mantenimiento.

El [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) muestra el uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo como una aplicación de la vida real

El ejemplo [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) también hace uso de `MultiPageRestorableApp` y permite escribir y editar notas que se guardan en el diccionario de `Properties`.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 24 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Ejemplos de capítulo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
