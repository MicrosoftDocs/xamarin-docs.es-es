---
title: Resumen del capítulo 18. MVVM
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771075"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumen del capítulo 18. MVVM

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Una de las mejores maneras de diseñar una aplicación es mediante la separación de la interfaz de usuario desde el código subyacente, que a veces se denomina el *lógica de negocios*. Existen varias técnicas, pero lo que está diseñado para entornos basados en XAML se conoce como Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Relaciones mutuas MVVM

Una aplicación MVVM tiene tres capas:

- El modelo proporciona los datos subyacentes, a veces a través de archivos o accede la web
- La vista es la capa de presentación o la interfaz de usuario, que generalmente se implementa en XAML
- El modelo de vista conecta el modelo y la vista

El modelo es que ignoran la persistencia del ViewModel y ViewModel es que ignoran la persistencia de la vista. Estas tres capas suelen conectan entre sí mediante los mecanismos siguientes:

![Ver, ViewModel y View](images/ch18fg03.png "MVVM")

En muchos programas más pequeños (y otros incluso más grandes), a menudo el modelo está ausente o su funcionalidad está integrada en el modelo de vista.

## <a name="viewmodels-and-data-binding"></a>ViewModels y enlace de datos

Para ponerse en contacto los enlaces de datos, un modelo de vista debe ser capaz de notificar a la vista cuando ha cambiado una propiedad de la clase ViewModel. El modelo de vista hace implementando el [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interfaz en el `System.ComponentModel` espacio de nombres. Esto forma parte de .NET en lugar de Xamarin.Forms. (Generalmente ViewModels intenta mantener la independencia de la plataforma.)

El `INotifyPropertyChanged` interfaz declara un evento único denominado [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) que indica la propiedad que ha cambiado.

### <a name="a-viewmodel-clock"></a>Un reloj de ViewModel

El [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca define una propiedad de tipo `DateTime` que los cambios según un temporizador. La clase implementa `INotifyPropertyChanged` y se activa el `PropertyChanged` evento cada vez que el `DateTime` los cambios de propiedad.

El [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) ejemplo crea una instancia de este ViewModel y utiliza los enlaces de datos para el modelo de vista para mostrar la información de hora y fecha de actualización.

### <a name="interactive-properties-in-a-viewmodel"></a>Propiedades interactivas en una clase ViewModel

Las propiedades en una clase ViewModel pueden ser más interactivas, como se muestra en el [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) (clase), que forma parte de la [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) ejemplo. Los enlaces de datos proporcionan valores multiplicando y multiplicador de dos `Slider` elementos y mostrar el producto con un `Label`. Sin embargo, puede realizar grandes cambios a esta interfaz de usuario en XAML sin cambios consecutivas en el modelo de vista o el archivo de código subyacente.

### <a name="a-color-viewmodel"></a>Un modelo de vista de Color

El [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca integra los modelos de color RGB y HSL. Se muestra en el [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) ejemplo:

[![Captura de pantalla triple de TK](images/ch18fg08-small.png "modelo de Color HSL")](images/ch18fg08-large.png#lightbox "modelo de Color HSL")

### <a name="streamlining-the-viewmodel"></a>Optimizar el modelo de vista

Se puede mejorar el código de ViewModels definiendo un `OnPropertyChanged` método mediante el [ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) atributo, que obtiene automáticamente el nombre de propiedad que realiza la llamada. El [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca hace esto y proporciona una clase base para los modelos de vista.

## <a name="the-command-interface"></a>La interfaz de comandos

MVVM funciona con los enlaces de datos y enlaces de datos trabajar con las propiedades, por lo que parece ser deficientes en cuanto a control de MVVM un `Clicked` eventos de un `Button` o un `Tapped` eventos de un `TapGestureRecognizer`. Para permitir que ViewModel controlar dichos eventos, Xamarin.Forms es compatible con la *interfaz comandos*.

La interfaz de comandos se manifiesta en el `Button` con dos propiedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command) de tipo [ `ICommand` ](xref:System.Windows.Input.ICommand) (definido en el `System.Windows.Input` espacio de nombres)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de tipo `Object`

Para admitir la interfaz de comandos, un modelo de vista debe definir una propiedad de tipo `ICommand` es decir, a continuación, datos enlazados a la `Command` propiedad de la `Button`. El `ICommand` interfaz declara dos métodos y un evento:

- Un [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object)) método con un argumento de tipo `object`
- Un [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) método con un argumento de tipo `object` que devuelve `bool`
- Un [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged) eventos

Internamente, una clase ViewModel establece cada propiedad del tipo `ICommand` a una instancia de una clase que implementa el `ICommand` interfaz. A través del enlace de datos, el `Button` llama inicialmente el `CanExecute` método y se deshabilita si el método devuelve `false`. También establece un controlador para el `CanExecuteChanged` eventos y llamadas `CanExecute` cada vez que se desencadena ese evento. Si el `Button` está habilitada, llama a la `Execute` método cada vez que el `Button` se hace clic en.

Es posible que tenga algunos ViewModels anteriores a Xamarin.Forms y estos ya es posible que son compatibles con la interfaz de comandos. Nuevo ViewModels destinadas a usarse solo con Xamarin.Forms, Xamarin.Forms proporciona un [ `Command` ](xref:Xamarin.Forms.Command) clase y un [ `Command<T>` ](xref:Xamarin.Forms.Command`1) de clases que implementan la `ICommand` interfaz. El tipo genérico es el tipo de argumento para el `Execute` y `CanExecute` métodos.

### <a name="simple-method-executions"></a>Ejecuciones del método simple

El [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) ejemplo muestra cómo usar la interfaz de comandos en una clase ViewModel. El [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) clase define dos propiedades de tipo `ICommand` y también define dos propiedades privadas que pasa a la más sencilla [ `Command` constructor](xref:Xamarin.Forms.Command.%23ctor(System.Action)). El programa contiene los enlaces de datos de este modelo de vista a la `Command` propiedades de dos `Button` elementos.

El `Button` elementos se pueden reemplazar fácilmente con `TapGestureRecognizer` objetos en XAML con ningún cambio de código.

### <a name="a-calculator-almost"></a>Una calculadora, casi

El [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) de ejemplo hace uso de ambos el `Execute` y `CanExecute` métodos de `ICommand`. Usa un [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) biblioteca. El ViewModel contiene seis propiedades del tipo `ICommand`. Estas se inicializan desde el [ `Command` constructor](xref:Xamarin.Forms.Command.%23ctor(System.Action)) y [ `Command` constructor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` y [ `Command<T>` constructor](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. Las teclas numéricas de la máquina de sumar están enlazadas a la propiedad que se inicializa con `Command<T>`y un `string` argumento `Execute` y `CanExecute` identifica la clave concreta.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels y el ciclo de vida de aplicación

El `AdderViewModel` usado en el **AddingMachine** ejemplo también define dos métodos denominados `SaveState` y `RestoreState`. Estos métodos se llaman desde la aplicación cuando entra en suspensión y cuando inicie de nuevo.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 18 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Ejemplos de capítulo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Patrones de aplicación empresarial utilizando el libro electrónico de Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
