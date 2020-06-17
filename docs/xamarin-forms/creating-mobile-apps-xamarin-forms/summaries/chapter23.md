---
title: "Resumen del capítulo 23. Desencadenadores y comportamientos" description: "Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 23. Desencadenadores y comportamientos" ms.prod: xamarin ms.technology: xamarin-forms ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261 author: davidbritch ms.author: dabritch ms.date: 11/07/2017 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumen del capítulo 23. Desencadenadores y comportamientos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Los desencadenadores y los comportamientos son similares, ya que ambos están diseñados para usarse en archivos XAML con el fin de simplificar las interacciones de los elementos más allá del uso de los enlaces de datos y para ampliar la funcionalidad de los elementos XAML. Los desencadenadores y los comportamientos casi siempre se usan con objetos visuales de la interfaz de usuario.

Para admitir desencadenadores y comportamientos, tanto `VisualElement` como `Style` admiten dos propiedades de colección:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) y [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) de tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) y [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) de tipo `IList<Behavior>`

## <a name="triggers"></a>Desencadenadores

Un desencadenador es una condición (un cambio de propiedad o la activación de un evento) que da como resultado una respuesta (otro cambio de propiedad o ejecución de código). La propiedad `Triggers` de `VisualElement` y `Style` es de tipo `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) es una clase abstracta de la que derivan cuatro clases selladas:

- [`Trigger`](xref:Xamarin.Forms.Trigger) para respuestas basadas en cambios de propiedad
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) para respuestas basadas en activaciones de eventos
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) para respuestas basadas en enlaces de datos
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) para respuestas basadas en varios desencadenadores

El desencadenador siempre se establece en el elemento cuya propiedad está cambiando por el desencadenador.

### <a name="the-simplest-trigger"></a>El desencadenador más sencillo

La clase [`Trigger`](xref:Xamarin.Forms.Trigger) comprueba si se ha cambiado un valor de propiedad y responde estableciendo otra propiedad del mismo elemento.

`Trigger` define tres propiedades:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) de tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) de tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) de tipo `IList<SetterBase>`, la propiedad de contenido de `Trigger`

Además, `Trigger` requiere que se la siguiente propiedad heredada de `TriggerBase` se establezca en:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) para indicar el tipo del elemento con el que está asociado `Trigger`.

`Property` y `Value` componen la condición, y la colección `Setters` es la respuesta. Cuando el elemento `Property` indicado tiene el valor indicado por `Value`, se aplican los objetos `Setter` de la colección `Setters`. Cuando `Property` tiene un valor diferente, se quitan los establecedores. `Setter` define dos propiedades que son iguales a las dos primeras propiedades de `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) de tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) de tipo `Object`

En el ejemplo [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) se muestra cómo un `Trigger` aplicado a un `Entry` puede aumentar el tamaño de `Entry` a través de la propiedad `Scale` cuando la propiedad `IsFocused` de `Entry` es `true`.

Aunque no es común, `Trigger` se puede establecer en el código, como se muestra en el ejemplo [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode).

En el ejemplo [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) se muestra cómo se puede establecer `Trigger` en `Style` para que se aplique a varios elementos `Entry`.

### <a name="trigger-actions-and-animations"></a>Acciones y animaciones de desencadenadores

También es posible ejecutar un poco de código basado en un desencadenador. Este código puede ser una animación destinada a una propiedad. Una forma habitual es usar [`EventTrigger`](xref:Xamarin.Forms.EventTrigger), que define dos propiedades:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) de tipo `string`, el nombre de un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) de tipo `IList<TriggerAction>`, una lista de acciones que se ejecutarán como respuesta.

Para usar esto, debe escribir una clase que derive de [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1), generalmente `TriggerAction<VisualElement>`. Puede definir las propiedades de esta clase. Se trata de propiedades de CLR sin formato en lugar de propiedades enlazables porque `TriggerAction` no se deriva de `BindableObject`. Debe reemplazar el método [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) al que se llama cuando se invoca la acción. El argumento es el elemento de destino.

La clase [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) es un ejemplo. Llama a la propiedad `ScaleTo` para animar la propiedad `Scale` de un elemento. Dado que una de sus propiedades es de tipo `Easing`, la clase [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) permite usar los campos estáticos `Easing` estándar en XAML.

En el ejemplo [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) se muestra cómo invocar `ScaleAction` desde objetos `EventTrigger` que supervisan los eventos `Focused` y `Unfocused`.

En el ejemplo [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) se muestra cómo definir una función de aceleración personalizada para `ScaleAction` en un archivo de código subyacente.

También puede invocar acciones mediante `Trigger` (como se distingue de `EventTrigger`). Esto requiere que tenga en cuenta que `TriggerBase` define dos colecciones:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) de tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) de tipo `IList<TriggerAction>`

En el ejemplo [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) se muestra cómo usar estas colecciones.

### <a name="more-event-triggers"></a>Más desencadenadores de eventos

La clase [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) llama a `ScaleTo` dos veces para escalar y reducir verticalmente. En el ejemplo [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) se usa esto con un estilo `EventTrigger` para proporcionar comentarios visuales cuando se presiona un elemento `Button`. Esta animación doble también es posible mediante dos acciones en la colección de tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs).

La clase [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) de la biblioteca **Xamarin.FormsBook.Toolkit** define una acción Shiver personalizable. En el ejemplo [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) se muestra este comportamiento.

La clase [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) de la biblioteca **Xamarin.FormsBook.Toolkit** está restringida a elementos `Entry` y establece la propiedad `TextColor` en rojo si la propiedad `Text` no es `double`. En el ejemplo [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) se muestra este comportamiento.

### <a name="data-triggers"></a>Desencadenadores de datos

[`DataTrigger`](xref:Xamarin.Forms.DataTrigger) es similar a `Trigger`, salvo que en lugar de supervisar una propiedad para los cambios de valor, supervisa un enlace de datos. Esto permite que una propiedad de un elemento afecte a una propiedad de otro elemento.

`DataTrigger` define tres propiedades:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) de tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) de tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) de tipo `IList<SetterBase>`

En el ejemplo [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) se requiere la biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) y se establecen los colores de los nombres de los alumnos en azul o rosa en función de la propiedad `Sex`:

[![Captura de pantalla triple de colores de género](images/ch23fg04-small.png "Colores de género")](images/ch23fg04-large.png#lightbox "Colores de género")

En el ejemplo [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) se establece la propiedad `IsEnabled` de `Entry` en `False` si la propiedad `Length` de la propiedad `Text` de `Entry` es igual a 0. Observe que la propiedad `Text` se inicializa en una cadena vacía; de forma predeterminada, es `null`, y `DataTrigger` no funcionaría correctamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinar condiciones en MultiTrigger

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) es una colección de condiciones. Cuando todos son `true`, se aplican los establecedores. La clase define dos propiedades:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) de tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) de tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) es una clase abstracta y tiene dos clases descendientes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), que tiene propiedades [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) y [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) como `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), que tiene propiedades [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) y [`Value`](xref:Xamarin.Forms.BindingCondition.Value) como `DataTrigger`

En el ejemplo [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions), `BoxView` solo se colorea cuando se activan cuatro elementos `Switch`.

En el ejemplo [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) se muestra cómo puede convertir `BoxView` en un color cuando *cualquiera* de los cuatro elementos `Switch` está activado. Esto requiere una aplicación de la ley de De Morgan e invertir toda la lógica.

La combinación de la lógica AND y OR no es tan fácil y, por lo general, requiere elementos `Switch` invisibles para los resultados intermedios. En el ejemplo [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) se muestra cómo se puede habilitar `Button` si uno de dos elementos `Entry` tiene algún texto escrito, pero no si ambos tienen algún texto escrito.

## <a name="behaviors"></a>comportamientos

Todo lo que puede hacer con un desencadenador, también puede hacerlo con un comportamiento, pero los comportamientos siempre requieren una clase que deriva de [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) e invalida los dos métodos siguientes:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

El argumento es el elemento al que está asociado el comportamiento. Por lo general, el método `OnAttachedTo` asocia algunos controladores de eventos y `OnDetachingFrom` los desasocia. Dado que este tipo de clase normalmente guarda algún estado, normalmente no se puede compartir en `Style`.

El ejemplo [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) es similar a **TriggerEntryValidation**, salvo que usa un comportamiento; la clase [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

### <a name="behaviors-with-properties"></a>Comportamientos con propiedades

`Behavior<T>` deriva de `Behavior`, que se deriva de `BindableObject`, de modo que las propiedades enlazables se pueden definir en un comportamiento. Estas propiedades pueden estar activas en los enlaces de datos.

Esto se muestra en el programa [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) que usa la clase [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit). `ValidEmailBehavior` tiene una propiedad enlazable de solo lectura y sirve como origen en los enlaces de datos.

El ejemplo [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) utiliza este mismo comportamiento para mostrar otro tipo de indicador para indicar que una dirección de correo electrónico es válida.

El ejemplo [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) es una variación del ejemplo anterior. ButtonGlide usa `DataTrigger` en combinación con ese comportamiento.

### <a name="toggles-and-check-boxes"></a>Alternancia y casillas

Es posible encapsular el comportamiento de un botón de alternancia en una clase, como [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) y, a continuación, definir todos los objetos visuales para la alternancia completa en XAML.

En el ejemplo [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) se usa `ToggleBehavior` con `DataTrigger` para usar `Label` con dos cadenas de texto para la alternancia.

El ejemplo [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) amplía este concepto cambiando entre dos objetos `FormattedString`.

La clase [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) de la biblioteca **Xamarin.FormsBook.Toolkit** deriva de `ContentView`, define una propiedad `IsToggled` e incorpora un elemento `ToggleBehavior` para la lógica de alternancia. De este modo, resulta más fácil definir el botón de alternancia en XAML, tal como se muestra en el ejemplo [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox).

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) incluye una clase [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) que se deriva de `ToggleBase` y usa una clase [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) para construir un botón de alternancia similar a `Switch` de Xamarin.Forms.

[`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) en **Xamarin.FormsBook.Toolkit** proporciona una animación que se usa para crear una opción animada en el ejemplo [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle).

### <a name="responding-to-taps"></a>Respuesta a pulsaciones

Una desventaja de `EventTrigger` es que no se puede adjuntar a `TapGestureRecognizer` para responder a las pulsaciones. Solucionar ese problema es el propósito de [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) en [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

En el ejemplo [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) se usa `TapBehavior` para usar el elemento `ShiverAction` anterior para los elementos `BoxView` que se pulsan.

En el ejemplo [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) se muestra cómo se reduce el marcado mediante la encapsulación de una clase `ShiverView`.

### <a name="radio-buttons"></a>Botones de radio

La biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) también tiene una clase [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) para crear botones de radio agrupados por un nombre de grupo `string`.

El programa [**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) usa cadenas de texto para su botón de radio. En el ejemplo [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) se usa `Style` para la diferencia de la apariencia entre los botones seleccionados y no seleccionados. En el ejemplo [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) se usan imágenes en casillas para sus botones de radio:

[![Captura de pantalla triple de imágenes de radio](images/ch23fg17-small.png "Imágenes de botón de radio")](images/ch23fg17-large.png#lightbox "Imágenes de botón de radio")

En el ejemplo [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) se dibujan botones de radio tradicionales con un punto dentro de un círculo.

### <a name="fades-and-orientation"></a>Atenuaciones y orientación

El ejemplo final, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders), permite cambiar entre tres vistas de selección de color diferentes mediante botones de radio. Las tres vistas se intensifican y atenúan con [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) en la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

El programa también responde a los cambios de orientación entre vertical y horizontal con [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) en la biblioteca **Xamarin.FormsBook.Toolkit**.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 23 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Ejemplos del capítulo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabajar con desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
