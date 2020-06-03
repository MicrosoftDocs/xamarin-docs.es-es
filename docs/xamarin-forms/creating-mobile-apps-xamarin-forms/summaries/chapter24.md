---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 24. Page navigation''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09622adc269027b589a7345a7d4411c3dcecbf0c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136648"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumen del capítulo 24. Navegación de páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muchas aplicaciones se componen de varias páginas entre las que se desplaza el usuario. La aplicación siempre tiene una página *principal* o página de *inicio* y, desde ahí, el usuario se desplaza a otras páginas, que se mantienen en una pila para volver a ellas. En el [**Capítulo 25. Variedades de página**](chapter25.md) se describen opciones de navegación adicionales.

## <a name="modal-pages-and-modeless-pages"></a>Páginas modales y no modales

`VisualElement` define una propiedad [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de tipo [`INavigation`](xref:Xamarin.Forms.INavigation), que incluye los siguientes dos métodos para ir a una nueva página:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Ambos métodos aceptan una instancia de `Page` como argumento y devuelven un objeto `Task`. Los dos métodos siguientes permiten volver a la página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si la interfaz de usuario tiene su propio botón **Atrás** (como en los teléfonos Android y Windows), no es necesario que la aplicación llame a estos métodos.

Aunque estos métodos están disponibles en cualquier `VisualElement`, por lo general se les llama desde la propiedad `Navigation` de la instancia de `Page` actual.

Las aplicaciones suelen usar páginas modales cuando el usuario debe proporcionar información sobre la página antes de volver a la página anterior. Las páginas que no son modales a veces se denominan sin modo o *jerárquicas*. Nada en la propia página indica que sea modal o no modal, sino que se rige por el método que se usa para ir a ella. Para que funcione en todas las plataformas, una página modal debe proporcionar su propia interfaz de usuario para volver a la página anterior.

El ejemplo [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) permite explorar la diferencia entre las páginas no modales y modales. Cualquier aplicación que use la navegación de páginas debe pasar su página principal al constructor [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), generalmente en la clase `App` del programa. Una ventaja es que ya no es necesario establecer un elemento `Padding` en la página para iOS.

Descubrirá que, en el caso de las páginas no modales, se muestra la propiedad [`Title`](xref:Xamarin.Forms.Page.Title) de la página. Todas las plataformas de escritorio y tableta de iOS, Android y Windows proporcionan un elemento de interfaz de usuario para volver a la página anterior. Por supuesto, los dispositivos de teléfono Android y Windows tienen un botón **Atrás** estándar para volver.

En las páginas modales, el elemento `Title` de la página no se muestra y no se proporciona ningún elemento de la interfaz de usuario para volver a la página anterior. Aunque puede usar el botón **Atrás** estándar de los teléfonos Android y Windows para volver a la página anterior, la página modal de las otras plataformas debe proporcionar su propio mecanismo para volver.

### <a name="animated-page-transitions"></a>Transiciones de página animadas

Se proporcionan versiones alternativas de los distintos métodos de navegación con un segundo argumento booleano que se establece en `true` si quiere que la transición de página incluya una animación:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Sin embargo, los métodos de navegación de páginas estándar incluyen la animación de forma predeterminada, por lo que solo son valiosos para ir a una página determinada al inicio (como se describe hacia el final de este capítulo) o al proporcionar su propia animación de entrada (como se describe en el [**Capítulo 22. Animación**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variaciones funcionales y visuales

`NavigationPage` incluye dos propiedades que se pueden establecer al crear una instancia de la clase en el método `App`:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` también incluye cuatro propiedades enlazables asociadas que afectan a la página concreta en la que se establecen:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) y [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) y [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) y [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funcionan solo en iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) y [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funcionan solo en iOS y Android

### <a name="exploring-the-mechanics"></a>Exploración de la mecánica

Los métodos de navegación de páginas son todos asincrónicos y deben usarse con `await`. La finalización no indica que se haya completado la navegación de páginas, sino que solo es seguro examinar la pila de navegación de páginas.

Cuando una página lleva a otra, la primera suele obtener una llamada a su método [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing), y la segunda página obtiene una llamada a su método [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing). De igual forma, cuando una página devuelve a otra, la primera obtiene una llamada a su método `OnDisappearing` y la segunda página obtiene generalmente una llamada a su método `OnAppearing`. El orden de estas llamadas (y la finalización de los métodos asincrónicos que invoca la navegación) depende de la plataforma. El uso de la palabra "general" en las dos instrucciones anteriores se debe a la navegación de páginas modales de Android, en la que no se producen estas llamadas de método.

Además, las llamadas a los métodos `OnAppearing` y `OnDisappearing` no indican necesariamente la navegación de páginas.

La interfaz `INavigation` incluye dos propiedades de colección que permiten examinar la pila de navegación:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) de tipo `IReadOnlyList<Page>` para la pila no modal
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) de tipo `IReadOnlyList<Page>` para la pila modal

Es más seguro acceder a estas pilas desde la propiedad `Navigation` de `NavigationPage` (que debe ser la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la clase `App`). Solo es seguro examinar estas pilas una vez completados los métodos de navegación de páginas asincrónicos. La propiedad [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) de `NavigationPage` no indica la página actual si esta es una página modal, sino que indica en su lugar la última página no modal.

El ejemplo [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) le permite explorar la navegación de páginas y las pilas, así como los tipos lícitos de navegaciones de páginas:

- Una página no modal puede llevar a otra página no modal o a una página modal
- Una página modal solo puede llevar a otra página modal

### <a name="enforcing-modality"></a>Aplicación de la modalidad

Una aplicación usa una página modal cuando es necesario obtener información del usuario. Se le debe prohibir al usuario que vuelva a la página anterior hasta que se proporcione esa información. En iOS, es fácil proporcionar un botón **Atrás** y habilitarlo solo cuando el usuario haya terminado con la página. Pero en el caso de los dispositivos telefónicos Android y Windows, la aplicación debe invalidar el método [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) y devolver `true` si el programa ha administrado el botón **Atrás** por sí solo, como se muestra en el ejemplo [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement).

En el ejemplo [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) se muestra cómo funciona esto en un escenario de MVVM.

## <a name="navigation-variations"></a>Variaciones de navegación

Si se puede ir a una página modal determinada varias veces, esta debe conservar la información de forma que el usuario pueda editarla en lugar de escribirla toda de nuevo. Para ello, puede conservar la instancia en particular de la página modal; sin embargo, un mejor enfoque (especialmente en iOS) es conservar la información en un modelo de vista.

### <a name="making-a-navigation-menu"></a>Creación de un menú de navegación

En el ejemplo [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) se muestra cómo usar `TableView` para enumerar los elementos de menú. Cada elemento está asociado a un objeto `Type` de una página determinada. Cuando se selecciona ese elemento, el programa crea una instancia de la página y se desplaza a ella.

[![Captura de pantalla triple del tipo de galería de vistas](images/ch24fg21-small.png "Elementos de menú de lista de TableView")](images/ch24fg21-large.png#lightbox "Elementos de menú de lista de TableView")

El ejemplo [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) es un poco diferente, ya que el menú contiene instancias de cada página en lugar de tipos. Esto ayuda a conservar la información de cada página, pero se deben crear instancias de todas las páginas al inicio del programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) muestra varias funciones definidas por `INavigation` que permiten manipular la pila de navegación de una manera estructurada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) y [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) con animación opcional

### <a name="dynamic-page-generation"></a>Generación de páginas dinámicas

En el ejemplo [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) se muestra cómo crear una página en tiempo de ejecución según los datos proporcionados por el usuario.

## <a name="patterns-of-data-transfer"></a>Patrones de transferencia de datos

Con frecuencia es necesario compartir datos entre páginas para transferir datos a una página a la que se ha ido y para que una página devuelva datos a la página que la invocó. Para ello, existen varias técnicas.

### <a name="constructor-arguments"></a>Argumentos del constructor

Al desplazarse a una nueva página, es posible crear una instancia de la clase de página con un argumento de constructor que permita que la página se inicialice por sí sola. En el ejemplo [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) se muestra cómo. También es posible que la página a la que se ha ido tenga su valor de `BindingContext` establecido por la página que lleva a ella.

### <a name="properties-and-method-calls"></a>Propiedades y llamadas de métodos

En el resto de ejemplos de transferencia de datos se explora el problema de pasar información entre páginas cuando una página lleva a otra y viceversa. En estas explicaciones, la página de *inicio* lleva a la página de *información*, y debe transferir a esta *información* inicializada. La página de *información* obtiene información adicional del usuario y la transfiere a la página de *inicio*.

La página de *inicio* puede acceder fácilmente a métodos públicos y propiedades de la página de *información* en cuanto crea la instancia de esa página. La página de *información* también puede acceder a métodos públicos y propiedades de la página de *inicio*, pero puede resultar complicado elegir un buen momento para ello. En el ejemplo [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) se hace esto en su invalidación de `OnDisappearing`. Una desventaja es que la página de *información* tiene que conocer el tipo de la página de *inicio*.

### <a name="messagingcenter"></a>MessagingCenter

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms proporciona otra manera de que dos páginas se comuniquen entre sí. Los mensajes se identifican mediante una cadena de texto y pueden ir acompañados de cualquier objeto.

Un programa que desea recibir mensajes de un tipo determinado debe suscribirse a ellos mediante [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) y especificar una función de devolución de llamada. Posteriormente, puede cancelar la suscripción llamando a [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La función de devolución de llamada recibe los mensajes enviados desde el tipo especificado con el nombre especificado enviado mediante el método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*).

El programa [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) muestra cómo se transfieren los datos mediante el centro de mensajería, pero, de nuevo, es necesario para ello que la página de *información* conozca el tipo de la página de *inicio*.

### <a name="events"></a>Events

El evento es un enfoque de larga tradición para que una clase envíe información a otra sin conocer el tipo de esa clase. En el ejemplo [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3), la clase *info* define un evento que se activa cuando la información está lista. Sin embargo, no hay ningún lugar adecuado para que la página de *inicio* desasocie el controlador de eventos.

### <a name="the-app-class-intermediary"></a>El intermediario de la clase App

En el ejemplo [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) se muestra cómo obtener acceso a las propiedades definidas en la clase `App` tanto en la página de *inicio* como en la página de *información*. Aunque esta es una buena solución, en la sección siguiente se describe otra mejor.

### <a name="switching-to-a-viewmodel"></a>Cambio a ViewModel

El uso de ViewModel para la información permite que la página de *inicio* y la página de *información* compartan la instancia de la clase de información. Esto se muestra en el ejemplo [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5).

### <a name="saving-and-restoring-page-state"></a>Guardado y restauración del estado de la página

El intermediario de la clase `App` o el enfoque ViewModel son idóneos cuando la aplicación debe guardar información si el programa entra en suspensión mientras la página de *información* está activa. En el ejemplo [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) se muestra cómo.

## <a name="saving-and-restoring-the-navigation-stack"></a>Guardado y restauración de la pila de navegación

En general, un programa de varias páginas que entra en suspensión debe ir a la misma página cuando se restaure. Esto significa que dicho programa debe guardar el contenido de la pila de navegación. En esta sección se muestra cómo automatizar este proceso en una clase diseñada para este fin. Esta clase también llama a las páginas individuales para permitirles guardar y restaurar su estado.

La biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define una interfaz llamada [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que pueden implementar las clases para guardar y restaurar elementos en el diccionario `Properties`.

La clase [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) de la biblioteca **Xamarin.FormsBook.Toolkit** se deriva de `Application`. Luego, puede derivar la clase de `App` de `MultiPageRestorableApp` y realizar algunas tareas de mantenimiento.

En [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) se muestra el uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo como una aplicación del mundo real

El ejemplo [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) también utiliza `MultiPageRestorableApp` y permite escribir y editar notas que se guardan en el diccionario de `Properties`.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 24 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Ejemplos del capítulo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
