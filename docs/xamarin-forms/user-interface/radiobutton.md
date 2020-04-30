---
title: RadioButton de Xamarin. Forms
description: El RadioButton de Xamarin. Forms es un tipo de botón que permite a los usuarios seleccionar una opción de un conjunto. Cada opción está representada por un botón de radio y solo puede seleccionar un botón de radio de un grupo.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
ms.openlocfilehash: 128ab4f6f00adaf86afc08eba37bcb81d3a04a90
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82533004"
---
# <a name="xamarinforms-radiobutton"></a>RadioButton de Xamarin. Forms

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin. Forms `RadioButton` es un tipo de botón que permite a los usuarios seleccionar una opción de un conjunto. Cada opción está representada por un botón de radio y solo puede seleccionar un botón de radio de un grupo. La `RadioButton` clase hereda de la [`Button`](xref:Xamarin.Forms.Button) clase.

En las siguientes capturas de pantallas se muestran `RadioButton` los objetos en su estado desactivado y seleccionado, en iOS y Android:

![Captura de pantalla de RadioButtons en Estados seleccionados y borrados, en iOS y Android](radiobutton-images/radiobutton-states.png "RadioButtons en iOS y Android")

> [!IMPORTANT]
> `RadioButton`es experimental actualmente y solo se puede usar si se establece `RadioButton_Experimental` la marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

El `RadioButton` control define las siguientes propiedades:

- `IsChecked`, de tipo `bool`, que define si `RadioButton` está seleccionado. Esta propiedad usa un `TwoWay` enlace y tiene un valor predeterminado de `false`.
- `GroupName`, de tipo `string`, que define el nombre que especifica qué `RadioButton` controles se excluyen mutuamente. Esta propiedad tiene un valor predeterminado de `null`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

El `RadioButton` control define un `CheckedChanged` evento que se desencadena cuando cambia `IsChecked` la propiedad, ya sea a través de la manipulación de usuario o mediante programación. El `CheckedChangedEventArgs` objeto que acompaña al `CheckedChanged` evento tiene una propiedad única denominada `Value`, de tipo. `bool` Cuando se desencadena el evento, el valor de la `Value` propiedad se establece en el nuevo valor de la `IsChecked` propiedad.

Además, la `RadioButton` clase hereda las siguientes propiedades utilizadas normalmente de la [`Button`](xref:Xamarin.Forms.Button) clase:

- [`Command`](xref:Xamarin.Forms.Button.Command), de tipo `ICommand`, que se ejecuta cuando se `RadioButton` selecciona el.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter), de tipo `object`, que es el parámetro que se pasa a `Command`.
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes), de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), que determina el estilo del texto.
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), de tipo `string`, que define la familia de fuentes.
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize), de tipo `double`, que define el tamaño de fuente.
- [`Text`](xref:Xamarin.Forms.Button.Text), de tipo `string`, que define el texto que se va a mostrar.
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor), de tipo [`Color`](xref:Xamarin.Forms.Color), que define el color del texto mostrado.

Para obtener más información sobre [`Button`](xref:Xamarin.Forms.Button) el control, vea el [botón de Xamarin. Forms](~/xamarin-forms/user-interface/button.md).

## <a name="create-radiobuttons"></a>Crear RadioButtons

En el ejemplo siguiente se muestra cómo crear `RadioButton` instancias de objetos en XAML:

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

En este ejemplo, `RadioButton` los objetos se agrupan implícitamente dentro del mismo contenedor primario. Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de RadioButtons agrupados implícitamente, en iOS y Android](radiobutton-images/radiobuttons.png "Botones de radiola agrupación implícita en iOS y Android")

Como alternativa, `RadioButton` se pueden crear objetos en el código:

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>RadioButtons de grupo

Los botones de radio funcionan en grupos y existen dos enfoques para agrupar los botones de radio:

- Colocándolos en el mismo contenedor primario. Esto se conoce como agrupación implícita.
- Estableciendo la `GroupName` propiedad en cada botón de radio en el mismo valor. Esto se conoce como agrupación explícita.

En el siguiente ejemplo de XAML se muestra `RadioButton` cómo agrupar objetos `GroupName` explícitamente estableciendo sus propiedades:

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

En este ejemplo, cada `RadioButton` es mutuamente excluyente porque comparte el mismo `GroupName` valor. Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de RadioButtons agrupados explícitamente, en iOS y Android](radiobutton-images/grouped-radiobuttons.png "Botones de radiola agrupación explícita en iOS y Android")

## <a name="respond-to-a-radiobutton-state-change"></a>Responder a un cambio de estado de RadioButton

Un botón de radio tiene dos estados: seleccionado o borrado. Cuando se selecciona un botón de radio, `IsChecked` su propiedad `true`es. Cuando se borra un botón de radio, `IsChecked` su propiedad `false`es. Un botón de radio se puede desactivar haciendo clic en otro botón de radio del mismo grupo, pero no se puede borrar haciendo clic de nuevo en él. Sin embargo, puede borrar un botón de radio mediante programación estableciendo su `IsChecked` propiedad en `false`.

Cuando la `IsChecked` propiedad cambia, ya sea a través de la manipulación de `CheckedChanged` usuario o mediante programación, se desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

El código subyacente contiene el controlador del `CheckedChanged` evento:

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

El `sender` argumento es el `RadioButton` responsable de este evento. Puede utilizar esto para tener acceso al `RadioButton` objeto o para distinguir entre varios `RadioButton` objetos que comparten el mismo `CheckedChanged` controlador de eventos.

Como alternativa, se puede registrar un controlador `CheckedChanged` de eventos para el evento en el código:

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> Un enfoque alternativo para responder a un `RadioButton` cambio de estado es definir `ICommand` y asignarlo a la `RadioButton.Command` propiedad. Para obtener más información, vea [Button: Using the Command interface](~/xamarin-forms/user-interface/button.md#using-the-command-interface).

## <a name="radiobutton-visual-states"></a>Estados visuales de RadioButton

`RadioButton`tiene un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual cuando se `RadioButton` selecciona un.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual `IsChecked` para el estado:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, los objetos [`Style`](xref:Xamarin.Forms.Style) de `RadioButton` destinos implícitos. `IsChecked` `RadioButton` `TextColor` `Opacity` Especifica que cuando se selecciona un, su propiedad se establecerá en verde con un valor de 1. [`VisualState`](xref:Xamarin.Forms.VisualState) `Normal` `RadioButton` `TextColor` Especifica que cuando se encuentra en un estado desactivado, su propiedad se establecerá en rojo con `Opacity` un valor de 0,5. `VisualState` Por lo tanto, el efecto general es que `RadioButton` cuando se borra un, es rojo y parcialmente transparente, y es verde sin transparencia cuando se selecciona:

![Captura de pantalla de la apariencia de RadioButton establecida por estado visual, en iOS y Android](radiobutton-images/ischecked-visualstate.png "Estados visuales de RadioButton en iOS y Android")

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-radiobutton"></a>Deshabilitar un RadioButton

A veces, una aplicación entra en un `RadioButton` estado en el que un que se está comprobando no es una operación válida. En tales casos, se `RadioButton` puede deshabilitar estableciendo su `IsEnabled` propiedad en. `false`

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de RadioButton (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Botón de Xamarin. Forms](~/xamarin-forms/user-interface/button.md)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
