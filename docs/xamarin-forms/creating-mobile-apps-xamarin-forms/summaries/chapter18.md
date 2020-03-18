---
title: Resumen del capítulo 18. MVVM
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771075"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumen del capítulo 18. MVVM

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Una de las mejores formas de diseñar una aplicación es separar la interfaz de usuario del código subyacente, que a veces se denomina *lógica de negocios*. Existen varias técnicas, pero la que se adapta a los entornos basados en XAML se conoce como Modelo-Vista-Modelo de vista o MVVM.

## <a name="mvvm-interrelationships"></a>Interrelaciones de MVVM

Una aplicación MVVM tiene tres niveles:

- Modelo proporciona datos subyacentes, a veces a través de archivos o accesos web.
- Vista es la interfaz de usuario o la capa de presentación, normalmente implementada en XAML.
- Modelo de vista conecta el Modelo y la Vista.

Modelo es independiente de Modelo de vista y Modelo de vista es independiente de Vista. Estas tres capas generalmente se conectan entre sí mediante los mecanismos siguientes:

![Vista, Modelo de vista y Vista](images/ch18fg03.png "MVVM")

En muchos programas más pequeños (e incluso más grandes), a menudo Modelo está ausente o su funcionalidad está integrada en Modelo de vista.

## <a name="viewmodels-and-data-binding"></a>Modelos de vista y enlace de datos

Para interactuar con los enlaces de datos, un Modelo de vista debe ser capaz de informar a Vista del cambio de una propiedad en Modelo de vista. Para ello, Modelo de vista implementa la interfaz de [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) en el espacio de nombres de `System.ComponentModel`. Esto forma parte de .NET y no de Xamarin.Forms. (Por lo general, los Modelos de vista intentan mantener la independencia de la plataforma).

La interfaz `INotifyPropertyChanged` declara un evento único denominado [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) que indica la propiedad que ha cambiado.

### <a name="a-viewmodel-clock"></a>Un reloj de Modelo de vista

[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) define una propiedad de tipo `DateTime` que cambia en función de un temporizador. La clase implementa `INotifyPropertyChanged` y activa el evento `PropertyChanged` cada vez que cambia la propiedad `DateTime`.

En el ejemplo [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) se crea una instancia de este Modelo de vista y se usan enlaces de datos al Modelo de vista para mostrar la información de fecha y hora actualizada.

### <a name="interactive-properties-in-a-viewmodel"></a>Propiedades interactivas en un Modelo de vista

Las propiedades de un Modelo de vista pueden ser más interactivas, como se muestra por la clase [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs), que forma parte del ejemplo [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier). Los enlaces de datos proporcionan los valores de multiplicando y multiplicador de dos elementos `Slider` y muestran el producto con `Label`. Sin embargo, puede realizar cambios extensivos en esta interfaz de usuario en XAML sin que se realicen cambios en el Modelo de vista ni en el archivo de código subyacente.

### <a name="a-color-viewmodel"></a>Un Modelo de vista de colores

[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra los modelos de color RGB y HSL. Esto se muestra en el ejemplo [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders):

[![Captura de pantalla triple de TK](images/ch18fg08-small.png "Modelo de color HSL")](images/ch18fg08-large.png#lightbox "Modelo de color HSL")

### <a name="streamlining-the-viewmodel"></a>Optimización del Modelo de vista

El código de los Modelos de vista se puede simplificar mediante la definición de un método `OnPropertyChanged` que use el atributo [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute), que obtiene automáticamente el nombre de la propiedad que realiza la llamada. La clase [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) de la biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) lo hace y proporciona una clase base para los Modelos de vista.

## <a name="the-command-interface"></a>Interfaz de comandos

MVVM funciona con enlaces de datos, y los enlaces de datos funcionan con propiedades, por lo que MVVM parece ser deficiente cuando se trata de controlar un evento `Clicked` de `Button` o un evento `Tapped` de `TapGestureRecognizer`. Para permitir que los Modelos de vista controlen estos eventos, Xamarin.Forms admite la *interfaz de comandos*.

La interfaz de comandos se manifiesta en `Button` con dos propiedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [`ICommand`](xref:System.Windows.Input.ICommand) (definido en el espacio de nombres `System.Windows.Input`)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de tipo `Object`

Para admitir la interfaz de comandos, un Modelo de vista debe definir una propiedad de tipo `ICommand` que sea entonces un enlace de datos a la propiedad `Command` de `Button`. La interfaz `ICommand` declara dos métodos y un evento:

- Un método [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) con un argumento de tipo `object`
- Un método [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) con un argumento de tipo `object` que devuelve `bool`
- Un evento [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)

Internamente, un Modelo de vista establece cada propiedad de tipo `ICommand` en una instancia de una clase que implementa la interfaz `ICommand`. A través del enlace de datos, `Button` llama inicialmente al método `CanExecute` y se deshabilita a sí mismo si el método devuelve `false`. También establece un controlador para el evento `CanExecuteChanged` y llama a `CanExecute` cada vez que se desencadena ese evento. Si `Button` está habilitado, llama al método `Execute` cada vez que se hace clic en `Button`.

Es posible que tenga algunos Modelos de vista que son anteriores a Xamarin. Forms, y que estos ya admitan la interfaz de comandos. En el caso de los nuevos Modelos de vista que se van a usar solo con Xamarin.Forms, Xamarin.Forms proporciona una clase [`Command`](xref:Xamarin.Forms.Command) y una clase [`Command<T>`](xref:Xamarin.Forms.Command`1) que implementa la interfaz `ICommand`. El tipo genérico es el tipo del argumento para los métodos `Execute` y `CanExecute`.

### <a name="simple-method-executions"></a>Ejecuciones de método simples

En el ejemplo [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) se muestra cómo usar la interfaz de comandos en un Modelo de vista. La clase de [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) define dos propiedades de tipo `ICommand` y también define dos propiedades privadas que se pasan al [constructor `Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action)) más sencillo. El programa contiene enlaces de datos de este Modelo de vista a las propiedades `Command` de dos elementos `Button`.

Los elementos `Button` se pueden reemplazar fácilmente por objetos `TapGestureRecognizer` en XAML sin cambios de código.

### <a name="a-calculator-almost"></a>Una calculadora, casi

En el ejemplo [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) se usan los métodos `Execute` y `CanExecute` de `ICommand`. Usa una clase [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) en la biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs). El Modelo de vista contiene seis propiedades de tipo `ICommand`. Se inicializan desde el [constructor `Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action)) y el [constructor `Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` y el [constructor `Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. Todas las claves numéricas del equipo que agrega están enlazadas a la propiedad que se inicializa con `Command<T>`, y un argumento `string` a `Execute` y `CanExecute` identifica la clave determinada.

## <a name="viewmodels-and-the-application-lifecycle"></a>Modelos de vista y el ciclo de vida de la aplicación

`AdderViewModel` utilizado en el ejemplo **AddingMachine** también define dos métodos denominados `SaveState` y `RestoreState`. Se llama a estos métodos desde la aplicación cuando entra en suspensión y cuando se inicia de nuevo.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 18 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Ejemplos del capítulo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Patrones de las aplicaciones empresariales con el libro electrónico de Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
