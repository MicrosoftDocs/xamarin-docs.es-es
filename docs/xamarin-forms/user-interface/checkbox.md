---
title: Xamarin.FormsCasilla
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8399dde2e4e2c9fb53b38fca2923eb0e3bfc6ce3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136479"
---
# <a name="xamarinforms-checkbox"></a>Xamarin.FormsCasilla

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin.Forms `CheckBox` Es un tipo de botón que puede estar activado o vacío. Cuando se activa una casilla, se considera que está activada. Cuando una casilla está vacía, se considera que está desactivada.

`CheckBox`define una `bool` propiedad denominada `IsChecked` , que indica si `CheckBox` está activada. Esta propiedad también está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que se puede aplicar un estilo a y ser el destino de los enlaces de datos.

> [!NOTE]
> La `IsChecked` propiedad enlazable tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) .

`CheckBox`define un `CheckedChanged` evento que se desencadena cuando `IsChecked` cambia la propiedad, ya sea a través de la manipulación de usuarios o cuando una aplicación establece la `IsChecked` propiedad. El `CheckedChangedEventArgs` objeto que acompaña al `CheckedChanged` evento tiene una propiedad única denominada `Value` , de tipo `bool` . Cuando se desencadena el evento, el valor de la `Value` propiedad se establece en el nuevo valor de la `IsChecked` propiedad.

## <a name="create-a-checkbox"></a>Crear una casilla

En el ejemplo siguiente se muestra cómo crear una instancia `CheckBox` de en XAML:

```xaml
<CheckBox />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla vacía, en iOS y Android](checkbox-images/checkbox-empty.png "Casilla vacío")

De forma predeterminada, `CheckBox` está vacío. `CheckBox`Se puede comprobar mediante la manipulación de usuarios o estableciendo la `IsChecked` propiedad en `true` :

```xaml
<CheckBox IsChecked="true" />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla activada, en iOS y Android](checkbox-images/checkbox-checked.png "Casilla activada")

Como alternativa, `CheckBox` se puede crear en el código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a un estado de cambio de casilla

Cuando la `IsChecked` propiedad cambia, ya sea a través de la manipulación del usuario o cuando una aplicación establece la `IsChecked` propiedad, se `CheckedChanged` desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

El archivo de código subyacente contiene el controlador del `CheckedChanged` evento:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

El `sender` argumento es el `CheckBox` responsable de este evento. Puede utilizar esto para tener acceso al `CheckBox` objeto o para distinguir entre varios `CheckBox` objetos que comparten el mismo `CheckedChanged` controlador de eventos.

Como alternativa, se puede registrar un controlador de eventos para el `CheckedChanged` evento en el código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Enlace de datos de una casilla

El `CheckedChanged` controlador de eventos se puede eliminar mediante el enlace de datos y los desencadenadores para responder a un control `CheckBox` que se está comprobando o está vacío:

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

En este ejemplo, [`Label`](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en un desencadenador de datos para supervisar la `IsChecked` propiedad de `CheckBox` . Cuando esta propiedad se convierte en `true` , las `FontAttributes` `FontSize` propiedades y del `Label` cambio. Cuando la `IsChecked` propiedad vuelve a `false` , las `FontAttributes` `FontSize` propiedades y de `Label` se restablecen a su estado inicial.

En las siguientes capturas de pantalla, la captura de pantalla de iOS muestra el [`Label`](xref:Xamarin.Forms.Label) formato cuando `CheckBox` está vacío, mientras que en la captura de pantalla de Android se muestra el `Label` formato cuando `CheckBox` se comprueba el:

[![Captura de pantalla de una casilla enlazada a datos, en iOS y Android](checkbox-images/checkbox-databinding.png "Casilla enlazado a datos")](checkbox-images/checkbox-databinding-large.png#lightbox "Casilla enlazado a datos")

Para obtener más información acerca de los desencadenadores, vea [ Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Deshabilitar una casilla

A veces, una aplicación entra en un estado en el que un `CheckBox` que se está comprobando no es una operación válida. En tales casos, `CheckBox` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false` .

## <a name="checkbox-appearance"></a>apariencia de CheckBox

Además de las propiedades que `CheckBox` heredan de la [`View`](xref:Xamarin.Forms.View) clase, `CheckBox` también define una `Color` propiedad que establece su color en [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<CheckBox Color="Red" />
```

En las siguientes capturas de pantallas se muestran una serie de objetos comprobados `CheckBox` , donde cada objeto tiene su `Color` propiedad establecida en un valor diferente [`Color`](xref:Xamarin.Forms.Color) :

![Captura de pantalla de las casillas de color, en iOS y Android](checkbox-images/checkbox-colors.png "Casilla coloreado")

## <a name="checkbox-visual-states"></a>Estados visuales de la casilla

`CheckBox`tiene un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual en `CheckBox` cuando se activa.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para el `IsChecked` Estado:

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

En este ejemplo, `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que cuando `CheckBox` se comprueba, su propiedad se `Color` establecerá en verde. `Normal` `VisualState` Especifica que cuando el `CheckBox` está en un estado normal, su `Color` propiedad se establecerá en rojo. Por lo tanto, el efecto general es que el `CheckBox` es rojo cuando está vacío y verde cuando está activado.

Para obtener más información sobre los Estados visuales, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de CheckBox (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin.FormsDesencadenadores](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsAdministrador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
