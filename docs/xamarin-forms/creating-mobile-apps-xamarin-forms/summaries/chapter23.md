---
title: Resumen del capítulo 23. Los desencadenadores y comportamientos
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 23. Los desencadenadores y comportamientos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760589"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumen del capítulo 23. Los desencadenadores y comportamientos

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Los desencadenadores y comportamientos son similares, en que ambos están diseñados para usarse en archivos XAML para simplificar las interacciones del elemento más allá del uso de enlaces de datos y para ampliar la funcionalidad de los elementos XAML. Desencadenadores y comportamientos casi siempre se utilizan con objetos de interfaz de usuario visual.

Para admitir los desencadenadores y comportamientos, ambos `VisualElement` y `Style` admiten dos propiedades de colección:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) y [ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers) de tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) y [ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors) de tipo `IList<Behavior>`

## <a name="triggers"></a>Desencadenadores

Un desencadenador es una condición (un cambio de propiedad o el desencadenamiento de un evento) que da como resultado una respuesta (otro cambio de propiedad o ejecutar algún código). El `Triggers` propiedad de `VisualElement` y `Style` es de tipo `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) es una clase abstracta desde la que derivan de cuatro clases selladas:

- [`Trigger`](xref:Xamarin.Forms.Trigger) para las respuestas según los cambios de propiedad
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) para las respuestas en función de activación de eventos
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) para las respuestas en función de los enlaces de datos
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) para las respuestas según varios desencadenadores

El desencadenador siempre se establece en el elemento cuya propiedad se está cambiando el desencadenador.

### <a name="the-simplest-trigger"></a>El desencadenador más sencillo

El [ `Trigger` ](xref:Xamarin.Forms.Trigger) comprueba si hay un cambio de un valor de propiedad de clase y responde mediante el establecimiento de otra propiedad del mismo elemento.

`Trigger` define tres propiedades:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) de tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) de tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) de tipo `IList<SetterBase>`, la propiedad de contenido `Trigger`

Además, `Trigger` requiere que se hereda la propiedad siguiente `TriggerBase` establecerse:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) para indicar el tipo de elemento en el que el `Trigger` está conectado

El `Property` y `Value` comprenden la condición y el `Setters` colección es la respuesta. Cuando el indicado `Property` tiene el valor indicado por `Value`, el `Setter` objetos en el `Setters` colección se aplican. Cuando el `Property` tiene un valor diferente, se quitan los establecedores. `Setter` define dos propiedades que son las mismas que las primeras dos propiedades de `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) de tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) de tipo `Object`

El [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) muestra cómo un `Trigger` aplicado a un `Entry` puede aumentar el tamaño de la `Entry` a través de la `Scale` propiedad cuando la `IsFocused`propiedad de la `Entry` es `true`.

Aunque no es común, el `Trigger` puede establecerse en el código, como el [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) muestra.

El [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) ejemplo muestra cómo el `Trigger` puede establecerse un `Style` para aplicar a varios `Entry` elementos.

### <a name="trigger-actions-and-animations"></a>Las acciones del desencadenador y animaciones

También es posible ejecutar un poco de código en función de un desencadenador. Este código puede ser una animación que tiene como destino una propiedad. Una forma habitual es usar un [ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger), que define dos propiedades:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) de tipo `string`, el nombre de un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) de tipo `IList<TriggerAction>`, una lista de acciones para ejecutar en respuesta.

Para ello, deberá escribir una clase que derive de [ `TriggerAction<T>` ](xref:Xamarin.Forms.TriggerAction`1), por lo general `TriggerAction<VisualElement>`. Puede definir las propiedades de esta clase. Éstos son propiedades CLR estándar en lugar de las propiedades enlazables porque `TriggerAction` no se deriva de `BindableObject`. Se debe reemplazar el [ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*) método que se llama cuando se invoca la acción. El argumento es el elemento de destino.

El [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca es un ejemplo. Lo llama el `ScaleTo` propiedad se va a animar el `Scale` propiedad de un elemento. Dado que uno de sus propiedades es de tipo `Easing`, el [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) clase le permite usar el estándar `Easing` campos estáticos en XAML.

El [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) ejemplo muestra cómo invocar el `ScaleAction` desde `EventTrigger` objetos que supervisa la `Focused` y `Unfocused` eventos.

El [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) ejemplo muestra cómo definir una función de aceleración personalizada para `ScaleAction` en un archivo de código subyacente.

También se pueden invocar acciones mediante una `Trigger` (distinguished desde `EventTrigger`). Esto requiere que se tenga en cuenta que `TriggerBase` define dos colecciones:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) de tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) de tipo `IList<TriggerAction>`

El [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) ejemplo muestra cómo usar estas colecciones.

### <a name="more-event-triggers"></a>Varios desencadenadores de eventos

El [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) llamadas a bibliotecas `ScaleTo` dos veces para escalar vertical y horizontalmente. El [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) ejemplo utiliza en un estilo `EventTrigger` para proporcionar comentarios visuales cuando un `Button` está presionado. Esta animación double también es posible usar dos acciones en la colección de tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

El [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) clase en el **Xamarin.FormsBook.Toolkit** biblioteca define una acción shiver personalizable. El [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) ejemplo muestra.

El [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) clase en el **Xamarin.FormsBook.Toolkit** está restringida a la biblioteca `Entry` elementos y conjuntos de la `TextColor` propiedad if rojo el `Text` propiedad no es un `double`. El [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) ejemplo muestra.

### <a name="data-triggers"></a>Desencadenadores de datos

El [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) es similar a la `Trigger` , salvo que en lugar de una propiedad para los cambios de valores de supervisión, supervisa un enlace de datos. Esto permite una propiedad en un elemento para afectar a una propiedad en otro elemento.

`DataTrigger` define tres propiedades:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) de tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) de tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) de tipo `IList<SetterBase>`

El [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) ejemplo requiere la [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca y establece los colores de los nombres de los estudiantes a azul o Rosa según el `Sex` propiedad:

[![Captura de pantalla de colores de género triple](images/ch23fg04-small.png "sexo colores")](images/ch23fg04-large.png#lightbox "sexo colores")

El [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) conjuntos de ejemplo la `IsEnabled` propiedad de un `Entry` a `False` si el `Length` propiedad de la `Text` propiedad de la `Entry`es igual a 0. Tenga en cuenta que el `Text` propiedad se inicializa en una cadena vacía; de forma predeterminada es `null`y el `DataTrigger` no funcionaban correctamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinar condiciones en la MultiTrigger

El [ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger) es una colección de condiciones. Cuando están todos `true`, a continuación, se aplican los establecedores. La clase define dos propiedades:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) de tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) de tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) es una clase abstracta y tiene dos de las clases descendientes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), que tiene [ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property) y [ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value) propiedades como `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), que tiene [ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding) y [ `Value` ](xref:Xamarin.Forms.BindingCondition.Value) propiedades como `DataTrigger`

En el [ **y las CONDICIONESGENERALES** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) ejemplo, un `BoxView` sólo se colorea cuando cuatro `Switch` elementos se activan.

El [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) ejemplo demuestra cómo puede un `BoxView` un color cuando *cualquier* de cuatro `Switch` elementos están activados. Esto requiere una aplicación de las leyes de Morgan y toda la lógica de reversión.

Combinación de AND y o lógica no es tan fácil y requiere generalmente invisible `Switch` elementos para los resultados intermedios. El [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) muestra cómo un `Button` puede habilitarse si cualquiera de dos `Entry` elementos tienen algún texto escrito, pero no si ambas tienen algún texto escrito.

## <a name="behaviors"></a>comportamientos

Todo lo puede hacer con un desencadenador, que también puede hacer con un comportamiento, pero los comportamientos siempre requieren una clase que derive de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) y reemplaza los dos métodos siguientes:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

El argumento es el elemento que está asociado el comportamiento a. Por lo general, el `OnAttachedTo` método asocia algunos controladores de eventos, y `OnDetachingFrom` desasocia de ellos. Dado que esta clase guarda normalmente algún estado, por lo general no se puede compartir en un `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) es similar al ejemplo **TriggerEntryValidation** salvo que usa un comportamiento &mdash; el [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="behaviors-with-properties"></a>Comportamientos con propiedades

`Behavior<T>` se deriva de `Behavior`, que se deriva de `BindableObject`, por lo que se pueden definir propiedades enlazables en un comportamiento. Estas propiedades pueden estar activas en los enlaces de datos.

Esto se muestra en el [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programa que hace que el uso de la [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) clase en el [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. `ValidEmailBehavior` tiene una propiedad enlazable de solo lectura y actúa como un origen de los enlaces de datos.

El [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) ejemplo utiliza este mismo comportamiento para mostrar otro tipo de indicador para señalar que la dirección de correo electrónico es válida.

El [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) ejemplo es una variación en el ejemplo anterior. ButtonGlide utiliza un `DataTrigger` en combinación con ese comportamiento.

### <a name="toggles-and-check-boxes"></a>Los conmutadores y casillas de verificación

Es posible encapsular el comportamiento de un botón de alternancia en una clase como [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca y, a continuación, defina todos los objetos visuales para el botón de alternancia completamente en XAML.

El [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) ejemplo se utiliza el `ToggleBehavior` con un `DataTrigger` para usar un `Label` con dos cadenas de texto para el botón de alternancia.

El [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) ejemplo amplía este concepto al cambiar entre dos `FormattedString` objetos.

El [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) clase en el **Xamarin.FormsBook.Toolkit** deriva de la biblioteca `ContentView`, define un `IsToggled` propiedad e incorpora un `ToggleBehavior` para el botón de alternancia lógica. Así resulta más fácil definir el botón de alternancia en XAML, tal como lo demuestra el [ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) ejemplo.

El [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) incluye un [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) clase que derive de `ToggleBase` y usa un [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)clase para construir un botón de alternancia que se parezca a Xamarin.Forms `Switch`.

Un [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) en el **Xamarin.FormsBook.Toolkit** proporciona una animación que se utiliza para realizar una palanca animada en el [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)ejemplo.

### <a name="responding-to-taps"></a>Responder a las pulsaciones

Una desventaja de `EventTrigger` es que no se puede adjuntar a un `TapGestureRecognizer` para responder a las pulsaciones. Este problema es el propósito de [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) en el [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

El [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) ejemplo usa `TapBehavior` antes de usar `ShiverAction` para puntear `BoxView` elementos.

El [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) ejemplo muestra cómo reducir en el marcado que encapsula un `ShiverView` clase.

### <a name="radio-buttons"></a>Botones de radio

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tiene también un [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) clase para hacer que los botones de radio que se agrupan por un `string` nombre del grupo.

El [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programa utiliza cadenas de texto para su botón de radio. El [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) ejemplo usa un `Style` la diferencia de aspecto entre botones checked y unchecked. El [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) ejemplo usa las imágenes con conversión boxing de sus botones de opción:

[![Captura de pantalla triple de imágenes de Radio](images/ch23fg17-small.png "imágenes de botón de Radio")](images/ch23fg17-large.png#lightbox "imágenes de botón de Radio")

El [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) ejemplo dibuja tradicional botones de radio que aparece con un punto dentro de un círculo.

### <a name="fades-and-orientation"></a>Las fusiones y orientación

El último ejemplo, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) le permite cambiar entre tres vistas diferentes de la selección del color utilizando los botones de radio. Las tres vistas fundido de entrada y salida mediante un [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

El programa también responde a los cambios de orientación entre vertical y horizontal usando un [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) en el **Xamarin.FormsBook.Toolkit** biblioteca.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 23 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capítulo 23 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabajar con desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
