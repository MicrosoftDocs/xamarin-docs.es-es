---
title: Modificador de Xamarin. Forms
description: El modificador de Xamarin. Forms es un tipo de botón que el usuario puede manipular para alternar entre los Estados activado y desactivado. En este artículo se explica cómo usar la clase switch para mostrar un elemento de interfaz de usuario de alternancia.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506539"
---
# <a name="xamarinforms-switch"></a>Modificador de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

El control de Xamarin. Forms [`Switch`](xref:Xamarin.Forms.Switch) es un botón de alternancia horizontal que el usuario puede manipular para alternar entre los Estados on y OFF, que están representados por un valor `boolean`. La clase `Switch` hereda de [`View`](xref:Xamarin.Forms.View).

En las siguientes capturas de pantallas se muestra un control de `Switch` en su estado **de alternancia activado** y **desactivado** en iOS y Android:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-default.png "Conmutadores en iOS y Android")

El control `Switch` define las siguientes propiedades:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) es un valor `boolean` que indica si la `Switch` está **activada**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) es una `Color` que afecta al modo en que el `Switch` se representa en el estado de alternancia o **activado**.
* `ThumbColor` es el `Color` del control de posición del conmutador.

Estas propiedades están respaldadas por un objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que se puede aplicar un estilo a los `Switch` y ser el destino de los enlaces de datos.

El control `Switch` define un evento `Toggled` que se desencadena cuando cambia la propiedad `IsToggled`, ya sea a través de la manipulación del usuario o cuando una aplicación establece la propiedad `IsToggled`. El objeto `ToggledEventArgs` que acompaña al evento `Toggled` tiene una propiedad única denominada `Value`, de tipo `bool`. Cuando se desencadena el evento, el valor de la propiedad `Value` refleja el nuevo valor de la propiedad `IsToggled`.

## <a name="create-a-switch"></a>Crear un conmutador

Se puede crear una instancia de `Switch` en XAML. Su propiedad `IsToggled` se puede establecer para alternar el `Switch`. De forma predeterminada, la propiedad `IsToggled` es `false`. En el ejemplo siguiente se muestra cómo crear una instancia de un `Switch` en XAML con el conjunto de propiedades de `IsToggled` opcional:

```xaml
<Switch IsToggled="true"/>
```

También se puede crear un `Switch` en el código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambiar apariencia

Además de las propiedades que [`Switch`](xref:Xamarin.Forms.Switch) heredan de la clase [`View`](xref:Xamarin.Forms.View) , `Switch` también define las propiedades `OnColor` y `ThumbColor`. La propiedad `OnColor` se puede establecer para definir el color **de** `Switch` cuando se cambia a su estado, y la propiedad `ThumbColor` se puede establecer para definir el `Color` del control de posición. En el ejemplo siguiente se muestra cómo crear una instancia de un `Switch` en XAML con estas propiedades establecidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

También se pueden establecer las propiedades al crear un `Switch` en el código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

En la captura de pantalla siguiente se muestra el `Switch` en sus Estados **de alternancia on** y **OFF** , con las propiedades `OnColor` y `ThumbColor` establecidas:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-colors.png "Conmutadores en iOS y Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a un cambio de estado del conmutador

Cuando cambia la propiedad `IsToggled`, ya sea a través de la manipulación del usuario o cuando una aplicación establece la propiedad `IsToggled`, se desencadena el evento `Toggled`. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<Switch Toggled="OnToggled" />
```

El archivo de código subyacente contiene el controlador del evento `Toggled`:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

El argumento `sender` del controlador de eventos es el `Switch` responsable de activar este evento. Puede utilizar la propiedad `sender` para tener acceso al objeto `Switch` o para distinguir entre varios objetos `Switch` que comparten el mismo controlador de eventos `Toggled`.

También se puede asignar el controlador de eventos `Toggled` en el código:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Enlazar datos a un conmutador

El controlador de eventos `Toggled` se puede eliminar mediante el enlace de datos y los desencadenadores para responder a un `Switch` cambiar los Estados de alternancia.

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

En este ejemplo, el [`Label`](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en un `DataTrigger` para supervisar la propiedad `IsToggled` de la `Switch` denominada `styleSwitch`. Cuando esta propiedad se vuelve `true`, se cambian las propiedades `FontAttributes` y `FontSize` de la `Label`. Cuando la propiedad `IsToggled` vuelve a `false`, las propiedades `FontAttributes` y `FontSize` de la `Label` se restablecen a su estado inicial.

Para obtener información sobre los desencadenadores, consulte los [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Deshabilitar un modificador

Una aplicación puede entrar en un estado en el que el `Switch` que se está alternando no es una operación válida. En tales casos, el `Switch` se puede deshabilitar estableciendo su propiedad `IsEnabled` en `false`. Esto impedirá que los usuarios puedan manipular el `Switch`.

## <a name="related-links"></a>Vínculos relacionados

* [Cambiar demostraciones](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
