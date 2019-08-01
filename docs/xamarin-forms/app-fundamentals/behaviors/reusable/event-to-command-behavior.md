---
title: EventToCommandBehavior reutilizable
description: Los comportamientos se pueden usar para asociar comandos a los controles que no se han diseñado para interactuar con los comandos. En este artículo se explica cómo crear y consumir un comportamiento de Xamarin.Forms para invocar un comando cuando se desencadena un evento.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650914"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior reutilizable

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Los comportamientos se pueden usar para asociar comandos a los controles que no se han diseñado para interactuar con los comandos. En este artículo se explica cómo crear y consumir un comportamiento de Xamarin.Forms para invocar un comando cuando se desencadena un evento._

## <a name="overview"></a>Información general

La clase `EventToCommandBehavior` es un comportamiento personalizado de Xamarin.Forms reutilizable que ejecuta un comando como respuesta al desencadenamiento de *cualquier* evento. De forma predeterminada, los argumentos para el evento se pasarán al comando y, opcionalmente, se pueden convertir mediante una implementación [`IValueConverter`](xref:Xamarin.Forms.IValueConverter).

Para usar el comportamiento, se deben establecer las propiedades de comportamiento siguientes:

- **EventName**: el nombre del evento que escucha el comportamiento.
- **Command**: el elemento `ICommand` que se va a ejecutar. El comportamiento espera encontrar la instancia de `ICommand` en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del control adjunto, que se puede heredar de un elemento primario.

También se pueden establecer las siguientes propiedades de comportamiento opcionales:

- **CommandParameter**: un elemento `object` que se va a pasar al comando.
- **Converter**: una implementación [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) que cambiará el formato de los datos de argumento de evento mientras el motor de enlace los pasa entre el *origen* y el *destino*.

> [!NOTE]
> `EventToCommandBehavior` es una clase personalizada que se puede encontrar en el [ejemplo de comportamiento EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) y que no forma parte de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Creación del comportamiento

La clase `EventToCommandBehavior` se deriva de la clase `BehaviorBase<T>`, que a su vez se deriva de la clase [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1). El propósito de la clase `BehaviorBase<T>` es proporcionar una clase base para cualquier comportamiento de Xamarin.Forms que requiera que el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento se establezca en el control adjunto. Esto garantiza que el comportamiento puede enlazar y ejecutar la interfaz `ICommand` especificada por la propiedad `Command` cuando se consume el comportamiento.

La clase `BehaviorBase<T>` proporciona un método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) reemplazable que establece el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento y un método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) reemplazable que limpia `BindingContext`. Además, la clase almacena una referencia al control adjunto en la propiedad `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implementación de propiedades enlazables

La clase `EventToCommandBehavior` define cuatro instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), que ejecutan un comando definido por el usuario cuando se desencadena un evento. Estas propiedades se muestran en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

Cuando se consume la clase `EventToCommandBehavior`, la propiedad `Command` debe estar enlazada a datos con una interfaz `ICommand` que se ejecutará en respuesta al desencadenamiento de eventos que se define en la propiedad `EventName`. El comportamiento espera encontrar la interfaz `ICommand` en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del control adjunto.

De forma predeterminada, los argumentos de evento para el evento se pasarán al comando. Estos datos se pueden convertir, opcionalmente, cuando el motor de enlace los pasa entre el *origen* y el *destino*, mediante la especificación de una implementación [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) como valor de la propiedad `Converter`. Como alternativa, se puede pasar un parámetro para el comando si se especifica el valor de propiedad `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implementación de las invalidaciones

La clase `EventToCommandBehavior` invalida los métodos [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) y [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) de la clase `BehaviorBase<T>`, como se muestra en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

El método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) realiza la instalación mediante una llamada al método `RegisterEvent`, pasando el valor de la propiedad `EventName` como un parámetro. El método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) realiza la limpieza mediante una llamada al método `DeregisterEvent`, pasando el valor de la propiedad `EventName` como un parámetro.

### <a name="implementing-the-behavior-functionality"></a>Implementación de la funcionalidad del comportamiento

El propósito del comportamiento es ejecutar el comando definido por la propiedad `Command` en respuesta al desencadenamiento del evento definido por la propiedad `EventName`. La funcionalidad básica del comportamiento se muestra en el ejemplo de código siguiente:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

El método `RegisterEvent` se ejecuta en respuesta a la asociación de `EventToCommandBehavior` a un control y recibe el valor de la propiedad `EventName` como un parámetro. Después, el método intenta localizar el evento definido en la propiedad `EventName`, en el control adjunto. Siempre que el evento se pueda localizar, el método `OnEvent` se registra para que sea el método de controlador para el evento.

El método `OnEvent` se ejecuta en respuesta al desencadenamiento del evento que se define en la propiedad `EventName`. Siempre que la propiedad `Command` haga referencia a una interfaz `ICommand` válida, el método intenta recuperar un parámetro para pasarlo a `ICommand` como sigue:

- Si la propiedad `CommandParameter` define un parámetro, se recuperará.
- De lo contrario, si la propiedad `Converter` define una implementación [`IValueConverter`](xref:Xamarin.Forms.IValueConverter), el convertidor se ejecuta y convierte los datos del argumento de evento mientras el motor de enlace los pasa entre el *origen* y el *destino*.
- En caso contrario, se asume que los argumentos del evento son el parámetro.

Después, se ejecuta la interfaz `ICommand` enlazada a datos, y se pasa el parámetro al comando, siempre que el método [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) devuelva `true`.

Aunque aquí no se muestre, `EventToCommandBehavior` también incluye un método `DeregisterEvent` que ejecuta el método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)). El método `DeregisterEvent` se usa para localizar y anular el registro del evento definido en la propiedad `EventName`, para limpiar las posibles fugas de memoria.

## <a name="consuming-the-behavior"></a>Consumo del comportamiento

La clase `EventToCommandBehavior` se puede adjuntar a la colección [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de un control, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

La propiedad `Command` del comportamiento está enlazada a datos a la propiedad `OutputAgeCommand` del modelo de vista asociado, mientras que la propiedad `Converter` se establece en la instancia de `SelectedItemConverter`, que devuelve el objeto [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) del control [`ListView`](xref:Xamarin.Forms.ListView) del elemento [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

En tiempo de ejecución, el comportamiento responderá a la interacción con el control. Cuando se selecciona un elemento en el control [`ListView`](xref:Xamarin.Forms.ListView), se desencadena el evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected), que ejecutará `OutputAgeCommand` en el modelo de vista. A su vez, esto actualiza la propiedad `SelectedItemText` del modelo de vista a la que se enlaza [`Label`](xref:Xamarin.Forms.Label), como se muestra en las capturas de pantalla siguientes:

[![](event-to-command-behavior-images/screenshots-sml.png "Aplicación de ejemplo con EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

La ventaja de usar este comportamiento para ejecutar un comando cuando se desencadena un evento es que se pueden asociar comandos con controles que no se han diseñado para interactuar con los comandos. Además, esto quita el código de control de eventos reutilizable de los archivos de código subyacente.

## <a name="summary"></a>Resumen

En este artículo se ha explicado el uso de un comportamiento de Xamarin.Forms para invocar un comando cuando se desencadena un evento. Los comportamientos se pueden usar para asociar comandos a los controles que no se han diseñado para interactuar con los comandos.

## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de EventToCommand (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Comportamiento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
