---
title: CheckBox de Xamarin. Forms
description: La casilla de Xamarin. Forms es un tipo de botón que puede estar activado o vacío. Cuando se activa una casilla, se considera que está activada. Cuando una casilla está vacía, se considera que está desactivada.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739154"
---
# <a name="xamarinforms-checkbox"></a>CheckBox de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin. Forms `CheckBox` es un tipo de botón que puede estar activado o vacío. Cuando se activa una casilla, se considera que está activada. Cuando una casilla está vacía, se considera que está desactivada.

`CheckBox`define una `bool` propiedad denominada `IsChecked`,queindica si estáactivada.`CheckBox` Esta propiedad también está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que se puede aplicar un estilo a y ser el destino de los enlaces de datos.

> [!NOTE]
> La `IsChecked` propiedad enlazable tiene un modo de enlace predeterminado [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)de.

`CheckBox`define un `CheckedChanged` evento que se desencadena cuando cambia `IsChecked` la propiedad, ya sea a través de la manipulación de usuarios o `IsChecked` cuando una aplicación establece la propiedad. El `CheckedChangedEventArgs` objeto que acompaña al `CheckedChanged` evento tiene una propiedad única denominada `Value`, de tipo `bool`. Cuando se desencadena el evento, el valor de la `Value` propiedad se establece en el nuevo valor de la `IsChecked` propiedad.

## <a name="create-a-checkbox"></a>Crear una casilla

El ejemplo siguiente muestra cómo crear una instancia de un `CheckBox` en XAML:

```xaml
<CheckBox />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla vacía, en iOS y Android](checkbox-images/checkbox-empty.png "Casilla vacío")

De forma predeterminada, `CheckBox` está vacío. Se `CheckBox` puede comprobar mediante la manipulación de usuarios o estableciendo la `IsChecked` propiedad en `true`:

```xaml
<CheckBox IsChecked="true" />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla activada, en iOS y Android](checkbox-images/checkbox-checked.png "Casilla activada")

Como alternativa, se `CheckBox` puede crear en el código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a un estado de cambio de casilla

Cuando la `IsChecked` propiedad cambia, ya sea a través de la manipulación del usuario o `IsChecked` cuando una aplicación `CheckedChanged` establece la propiedad, se desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

El archivo de código subyacente contiene el controlador para el `CheckedChanged` eventos:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

El `sender` argumento es el `CheckBox` responsable de este evento. Se puede usar para tener acceso a la `CheckBox` objeto, o para distinguir entre varias `CheckBox` objetos que comparten el mismo `CheckedChanged` eventos.

Como alternativa, se puede registrar un controlador `CheckedChanged` de eventos para el evento en el código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Enlace de datos de una casilla

El `CheckedChanged` controlador de eventos se puede eliminar mediante el enlace de datos y los desencadenadores para `CheckBox` responder a un control que se está comprobando o está vacío:

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

En este ejemplo, [`Label`](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en un desencadenador de datos para supervisar la `IsChecked` propiedad `CheckBox`de. Cuando esta propiedad se `true`convierte en `FontAttributes` , `FontSize` las propiedades y `Label` del cambio. Cuando la `IsChecked` propiedad vuelve a `false`, `FontAttributes` laspropiedades`FontSize` y de serestablecenasuestadoinicial.`Label`

En las siguientes capturas de pantalla, la captura [`Label`](xref:Xamarin.Forms.Label) de pantalla de `CheckBox` iOS muestra el formato cuando está vacío, mientras `Label` que en la `CheckBox` captura de pantalla de Android se muestra el formato cuando se comprueba el:

[![Captura de pantalla de una casilla enlazada a datos, en iOS y Android](checkbox-images/checkbox-databinding.png "Casilla enlazado a datos")](checkbox-images/checkbox-databinding-large.png#lightbox "Casilla enlazado a datos")

Para obtener más información sobre los desencadenadores, consulte los [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Deshabilitar una casilla

A veces, una aplicación entra en un `CheckBox` estado en el que un que se está comprobando no es una operación válida. En tales casos, `CheckBox` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false`.

## <a name="checkbox-appearance"></a>Apariencia de la casilla

Además de las `CheckBox` propiedades que heredan de la [`View`](xref:Xamarin.Forms.View) clase, `CheckBox` también define [`Color`](xref:Xamarin.Forms.Color)una `Color` propiedad que establece su color en:

```xaml
<CheckBox Color="Red" />
```

En las siguientes capturas de pantallas se `CheckBox` muestran una serie de objetos comprobados, donde cada objeto [`Color`](xref:Xamarin.Forms.Color)tiene su `Color` propiedad establecida en un valor diferente:

![Captura de pantalla de las casillas de color, en iOS y Android](checkbox-images/checkbox-colors.png "Casilla coloreado")

## <a name="checkbox-visual-states"></a>Estados visuales de la casilla

`CheckBox`tiene un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar `CheckBox` un cambio visual en cuando se activa.

En el siguiente ejemplo XAML se muestra cómo definir un estado visual para el `IsChecked` estado:

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

`IsChecked` En este ejemplo, [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que cuando `CheckBox` se comprueba, su `Color` propiedad se establecerá en verde. Especifica que `Color` cuando el `CheckBox` está en un estado normal, su propiedad se establecerá en rojo. `Normal` `VisualState` Por lo tanto, el efecto general es `CheckBox` que el es rojo cuando está vacío y verde cuando está activado.

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de CheckBox (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
