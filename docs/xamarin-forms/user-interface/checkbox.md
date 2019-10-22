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
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739154"
---
# <a name="xamarinforms-checkbox"></a>CheckBox de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin. Forms `CheckBox` es un tipo de botón que puede estar activado o vacío. Cuando se activa una casilla, se considera que está activada. Cuando una casilla está vacía, se considera que está desactivada.

`CheckBox` define una propiedad `bool` denominada `IsChecked`, que indica si la `CheckBox` está activada. Esta propiedad también está respaldada por un objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que se puede aplicar un estilo a y ser el destino de los enlaces de datos.

> [!NOTE]
> La propiedad enlazable `IsChecked` tiene un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` define un evento `CheckedChanged` que se desencadena cuando cambia la propiedad `IsChecked`, ya sea a través de la manipulación de usuarios o cuando una aplicación establece la propiedad `IsChecked`. El objeto `CheckedChangedEventArgs` que acompaña al evento `CheckedChanged` tiene una propiedad única denominada `Value`, de tipo `bool`. Cuando se desencadena el evento, el valor de la propiedad `Value` se establece en el nuevo valor de la propiedad `IsChecked`.

## <a name="create-a-checkbox"></a>Crear una casilla

En el ejemplo siguiente se muestra cómo crear una instancia de un `CheckBox` en XAML:

```xaml
<CheckBox />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla vacía, en iOS y Android](checkbox-images/checkbox-empty.png "Casilla vacío")

De forma predeterminada, la `CheckBox` está vacía. El `CheckBox` se puede comprobar mediante la manipulación de usuarios o estableciendo la propiedad `IsChecked` en `true`:

```xaml
<CheckBox IsChecked="true" />
```

Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de una casilla activada, en iOS y Android](checkbox-images/checkbox-checked.png "Casilla activada")

Como alternativa, se puede crear un `CheckBox` en el código:

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Responder a un estado de cambio de casilla

Cuando cambia la propiedad `IsChecked`, ya sea a través de la manipulación del usuario o cuando una aplicación establece la propiedad `IsChecked`, se desencadena el evento `CheckedChanged`. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

El archivo de código subyacente contiene el controlador del evento `CheckedChanged`:

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

El `sender` argumento es el `CheckBox` responsable de este evento. Puede utilizar esto para tener acceso al objeto `CheckBox` o para distinguir entre varios objetos `CheckBox` que comparten el mismo evento `CheckedChanged`.

Como alternativa, se puede registrar un controlador de eventos para el evento `CheckedChanged` en el código:

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Enlace de datos de una casilla

El controlador de eventos `CheckedChanged` se puede eliminar mediante el enlace de datos y los desencadenadores para responder a una `CheckBox` que se está comprobando o vacía:

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

En este ejemplo, el [`Label`](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en un desencadenador de datos para supervisar la propiedad `IsChecked` de la `CheckBox`. Cuando esta propiedad se vuelve `true`, las propiedades `FontAttributes` y `FontSize` de la `Label` cambian. Cuando la propiedad `IsChecked` vuelve a `false`, las propiedades `FontAttributes` y `FontSize` de la `Label` se restablecen a su estado inicial.

En las siguientes capturas de pantalla, la captura de pantalla de iOS muestra el formato de [`Label`](xref:Xamarin.Forms.Label) cuando el `CheckBox` está vacío, mientras que en la captura de pantalla de Android se muestra el formato de `Label` cuando se comprueba el `CheckBox`:

[![Captura de pantalla de una casilla enlazada a datos, en iOS y Android](checkbox-images/checkbox-databinding.png "Casilla enlazado a datos")](checkbox-images/checkbox-databinding-large.png#lightbox "Casilla enlazado a datos")

Para obtener más información sobre los desencadenadores, consulte los [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Deshabilitar una casilla

A veces, una aplicación entra en un estado en el que un `CheckBox` que se está comprobando no es una operación válida. En tales casos, el `CheckBox` se puede deshabilitar estableciendo su propiedad `IsEnabled` en `false`.

## <a name="checkbox-appearance"></a>Apariencia de la casilla

Además de las propiedades que `CheckBox` heredan de la clase [`View`](xref:Xamarin.Forms.View) , `CheckBox` define también una propiedad `Color` que establece su color en [`Color`](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

En las siguientes capturas de pantallas se muestra una serie de objetos `CheckBox` comprobados, donde cada objeto tiene su propiedad `Color` establecida en un [`Color`](xref:Xamarin.Forms.Color)diferente:

![Captura de pantalla de las casillas de color, en iOS y Android](checkbox-images/checkbox-colors.png "Casilla coloreado")

## <a name="checkbox-visual-states"></a>Estados visuales de la casilla

`CheckBox` tiene un [`VisualState`](xref:Xamarin.Forms.VisualState) de `IsChecked` que se puede utilizar para iniciar un cambio visual en el `CheckBox` cuando se activa.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para el estado `IsChecked`:

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

En este ejemplo, la `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que cuando se comprueba la `CheckBox`, su propiedad `Color` se establecerá en verde. El `VisualState` `Normal` especifica que cuando el `CheckBox` se encuentra en un estado normal, su propiedad `Color` se establecerá en rojo. Por lo tanto, el efecto general es que el `CheckBox` es rojo cuando está vacío y verde cuando está activado.

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de CheckBox (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
