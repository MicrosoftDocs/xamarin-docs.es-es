---
title: Comportamientos asociados
description: Los comportamientos asociados son clases estáticas con una o varias propiedades asociadas. En este artículo, se explica cómo crear y usar comportamientos asociados.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08b1c738dc87bb9373436a3fd96486eb15341112
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139430"
---
# <a name="attached-behaviors"></a>Comportamientos asociados

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_Los comportamientos asociados son clases estáticas con una o varias propiedades asociadas. En este artículo, se explica cómo crear y usar comportamientos asociados._

## <a name="overview"></a>Información general

Una propiedad asociada es un tipo especial de propiedad enlazable. Se define en una clase, pero se asocia a otros objetos y puede reconocerse en XAML como un atributo que contiene una clase y un nombre de propiedad separado por un punto.

Una propiedad asociada puede definir un delegado `propertyChanged` que se ejecutará cuando cambie el valor de la propiedad (por ejemplo, cuando la propiedad se establezca en un control). Cuando se ejecute el delegado `propertyChanged`, se pasará una referencia al control donde esté asociado, así como los parámetros que contienen los valores nuevos y anteriores de la propiedad. Este delegado puede usarse para agregar nuevas funciones al control al que se asocie la propiedad; para hacerlo, se manipula la referencia en la que se pasa, como se muestra a continuación:

1. El delegado `propertyChanged` transmite la referencia del control, que se recibe como un elemento [`BindableObject`](xref:Xamarin.Forms.BindableObject), al tipo de control cuyo comportamiento se ha diseñado para mejorar.
1. El delegado `propertyChanged` modifica las propiedades del control, llama a métodos del control o registra controladores de eventos expuestos por el control para implementar la función básica de comportamiento.

Un problema con los comportamientos asociados es que se definen en una clase `static`, con propiedades y métodos de `static`. Esto hace que sea difícil crear comportamientos asociados que tengan un estado. Además, los comportamientos de Xamarin.Forms han reemplazado los comportamientos asociados como método preferido para la creación de comportamientos. Para obtener más información sobre los comportamientos de Xamarin.Forms, vea [Comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) y [Comportamientos reutilizables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Crear un comportamiento asociado

En la aplicación de ejemplo, se muestra un elemento `NumericValidationBehavior`, que resalta el valor especificado por el usuario en un control [`Entry`](xref:Xamarin.Forms.Entry) en color rojo, si no es del tipo `double`. El comportamiento se muestra en el siguiente ejemplo de código:

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

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
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

La clase `NumericValidationBehavior` contiene una propiedad asociada denominada `AttachBehavior` con un captador y establecedor de `static`, que controla la adición o eliminación del comportamiento del control al que se va a asociar. Esta propiedad asociada registra el método `OnAttachBehaviorChanged` que se ejecutará cuando cambie el valor de la propiedad. Este método registra o anula el registro de un controlador de eventos para el evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) basándose en el valor de la propiedad asociada `AttachBehavior`. El método `OnEntryTextChanged` proporciona la función básica del comportamiento, que analiza el valor especificado por el usuario en el elemento [ y establece la propiedad `Entry`](xref:Xamarin.Forms.Entry)`TextColor` en rojo si el valor no es del tipo `double`.

## <a name="consuming-an-attached-behavior"></a>Usar un comportamiento asociado

Para usar la clase `NumericValidationBehavior`, se puede agregar la propiedad asociada `AttachBehavior` a un control [`Entry`](xref:Xamarin.Forms.Entry), como se muestra en el siguiente ejemplo de código de XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

En el siguiente ejemplo de código, se muestra el control [`Entry`](xref:Xamarin.Forms.Entry) equivalente en C#:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

En tiempo de ejecución, el comportamiento responderá a la interacción con el control, según la implementación del comportamiento. En las capturas de pantalla siguientes, se muestra la respuesta del comportamiento asociado en respuesta a entradas no válidas:

[![](attached-images/screenshots-sml.png "Sample Application with Attached Behavior")](attached-images/screenshots.png#lightbox "Sample Application with Attached Behavior")

> [!NOTE]
> Los comportamientos asociados se escriben para un tipo de control específico (o una superclase que se puede aplicar a muchos controles) y solo se tienen que agregar a un control compatible. Al intentar asociar un comportamiento a un control incompatible, se producirá un comportamiento desconocido que depende de la implementación del comportamiento.

### <a name="removing-an-attached-behavior-from-a-control"></a>Quitar un comportamiento asociado de un control

Para quitar la clase `NumericValidationBehavior` de un control, establezca la propiedad asociada `AttachBehavior` en `false`, como se muestra en el siguiente ejemplo de código de XAML:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

En el siguiente ejemplo de código, se muestra el control [`Entry`](xref:Xamarin.Forms.Entry) equivalente en C#:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

En tiempo de ejecución, el método `OnAttachBehaviorChanged` se ejecutará cuando el valor de la propiedad asociada `AttachBehavior` se establezca en `false`. Después, el método `OnAttachBehaviorChanged` anulará el registro del controlador de eventos del evento [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) con el fin de garantizar que el comportamiento no se ejecute cuando el usuario interactúe con el control.

## <a name="summary"></a>Resumen

En este artículo, se explica cómo crear y consumir comportamientos asociados. Los comportamientos asociados son clases `static` con una o varias propiedades asociadas.

## <a name="related-links"></a>Vínculos relacionados

- [Comportamientos asociados (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)
