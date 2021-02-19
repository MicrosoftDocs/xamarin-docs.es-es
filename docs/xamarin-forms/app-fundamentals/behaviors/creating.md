---
title: Creación de comportamientos de Xamarin.Forms
description: Los comportamientos de Xamarin.Forms se crean mediante la derivación de la clase Behavior o Behavior<T>. En este artículo se explica cómo crear y consumir comportamientos de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5b0cc2bb4617a0f093971dfca69e8352779c088
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "93373658"
---
# <a name="create-xamarinforms-behaviors"></a>Creación de comportamientos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Los comportamientos de Xamarin.Forms se crean mediante la derivación de la clase Behavior o Behavior&lt;T&gt;. En este artículo se explica cómo crear y consumir comportamientos de Xamarin.Forms._

## <a name="overview"></a>Información general

El proceso de creación de un comportamiento de Xamarin.Forms es el siguiente:

1. Cree una clase que herede de la clase [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), donde `T` sea el tipo del control al que se debe aplicar el comportamiento.
1. Invalide el método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) para realizar cualquier configuración necesaria.
1. Invalide el método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) para realizar cualquier limpieza necesaria.
1. Implemente la funcionalidad básica del comportamiento.

El resultado es la estructura que se muestra en el ejemplo de código siguiente:

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

El método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) se desencadena justo después de que se adjunte el comportamiento a un control. Este método recibe una referencia al control al que se adjunta, y se puede usar para registrar controladores de eventos o realizar otra configuración necesaria para admitir la funcionalidad del comportamiento. Por ejemplo, se podría suscribir a un evento en un control. Después, se implementaría la funcionalidad del comportamiento en el controlador de eventos para el evento.

El método [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) se desencadena cuando se quita el comportamiento del control. Este método recibe una referencia al control al que se adjunta y se usa para realizar cualquier limpieza necesaria. Por ejemplo, podría cancelar la suscripción a un evento en un control para evitar fugas de memoria.

Después, el comportamiento se puede consumir si se adjunta a la colección [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) del control adecuado.

## <a name="creating-a-xamarinforms-behavior"></a>Creación de un comportamiento de Xamarin.Forms

En la aplicación de ejemplo, se muestra un elemento `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un control [`Entry`](xref:Xamarin.Forms.Entry) en color rojo, si no es del tipo `double`. El comportamiento se muestra en el ejemplo de código siguiente:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` se deriva de la clase [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), donde `T` es un elemento [`Entry`](xref:Xamarin.Forms.Entry). El método [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) event, with the [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) anula el registro del evento `TextChanged` para evitar fugas de memorias. El método `OnEntryTextChanged` proporciona la funcionalidad básica del comportamiento y analiza el valor especificado por el usuario en el elemento `Entry` y establece la propiedad [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) en rojo si el valor no es de tipo `double`.

> [!NOTE]
> Xamarin.Forms no establece el elemento `BindingContext` de un comportamiento, ya que los comportamientos se pueden compartir y aplicar a varios controles mediante estilos.

## <a name="consuming-a-xamarinforms-behavior"></a>Consumo de un comportamiento de Xamarin.Forms

Todos los controles de Xamarin.Forms tienen una colección [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors), a la que se pueden agregar uno o varios comportamientos, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

En el siguiente ejemplo de código, se muestra el control [`Entry`](xref:Xamarin.Forms.Entry) equivalente en C#:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

En tiempo de ejecución, el comportamiento responderá a la interacción con el control, en función de la implementación del comportamiento. En las capturas de pantalla siguientes se muestra la respuesta del comportamiento a entradas no válidas:

[![Aplicación de ejemplo con comportamiento Xamarin.Forms ](creating-images/screenshots-sml.png)](creating-images/screenshots.png#lightbox "Aplicación de ejemplo con comportamiento Xamarin.Forms Comportamiento")

> [!NOTE]
> Los comportamientos se escriben para un tipo de control específico (o una superclase que se puede aplicar a muchos controles), y solo se deben agregar a un control compatible. El intento de adjuntar un comportamiento a un control incompatible iniciará una excepción.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consumo de un comportamiento de Xamarin.Forms con un estilo

Los comportamientos también se pueden consumir mediante un estilo explícito o implícito. Pero no se puede crear un estilo que establece la propiedad [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) de un control porque la propiedad es de solo lectura. La solución consiste en agregar una propiedad adjunta a la clase de comportamiento que controle la adición y eliminación del comportamiento. El proceso es el siguiente:

1. Se agrega una propiedad adjunta a la clase de comportamiento que se usará para controlar la adición o eliminación del comportamiento del control al que se va a conectar el comportamiento. Se asegura que la propiedad adjunta registra un delegado `propertyChanged` que se ejecutará cuando cambie el valor de la propiedad.
1. Se crea un captador y establecedor `static` para la propiedad adjunta.
1. Se implementa la lógica en el delegado `propertyChanged` para agregar y quitar el comportamiento.

En el ejemplo de código siguiente se muestra una propiedad adjunta que controla la adición y eliminación de `NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

La clase `NumericValidationBehavior` contiene una propiedad adjunta denominada `AttachBehavior` con un captador y establecedor `static`, que controla la adición o eliminación del comportamiento del control al que se va a adjuntar. Esta propiedad asociada registra el método `OnAttachBehaviorChanged` que se ejecutará cuando cambie el valor de la propiedad. Este método agrega o quita el comportamiento del control, en función del valor de la propiedad adjunta `AttachBehavior`.

En el ejemplo de código siguiente se muestra un estilo *explícito* para el elemento `NumericValidationBehavior` que usa la propiedad adjunta `AttachBehavior` y que se puede aplicar a controles [`Entry`](xref:Xamarin.Forms.Entry):

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style) se puede aplicar a un control [`Entry`](xref:Xamarin.Forms.Entry) si se establece su propiedad [`Style`](xref:Xamarin.Forms.NavigableElement.Style) en la instancia de `Style` mediante la extensión de marcado `StaticResource`, como se muestra en el ejemplo de código siguiente:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Aunque se pueden agregar propiedades enlazables a un comportamiento que se establece o se consulta en XAML, si se crean comportamientos que tienen estado, no se deberían compartir entre los controles en un elemento `Style` de un objeto `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Eliminación de un comportamiento de un control

Colección [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a `Remove` or `Clear` method. The following code example demonstrates removing a specific behavior from a control's `Behaviors`:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Como alternativa, se puede borrar la colección [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) del control, como se muestra en el ejemplo de código siguiente:

```csharp
entry.Behaviors.Clear();
```

Además, tenga en cuenta que los comportamientos no se quitan de forma implícita de los controles cuando se extraen páginas de la pila de navegación. En su lugar, se deben quitar explícitamente antes de que las páginas queden fuera del ámbito.

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear y consumir comportamientos de Xamarin.Forms. Los comportamientos de Xamarin.Forms se crean mediante la derivación de la clase [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1).

## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento de Xamarin.Forms (ejemplo)](/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Comportamiento de Xamarin.Forms aplicado con un estilo (ejemplo)](/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)