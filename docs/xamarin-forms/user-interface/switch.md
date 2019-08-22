---
title: Modificador de Xamarin. Forms
description: El modificador de Xamarin. Forms es un tipo de botón que el usuario puede manipular para alternar entre los Estados activado y desactivado. En este artículo se explica cómo usar la clase switch para mostrar un elemento de interfaz de usuario de alternancia.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 1f2ef838287e32df5df42f73e4b43816d618552d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887886"
---
# <a name="xamarinforms-switch"></a>Modificador de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

El control Xamarin. [`Switch`](xref:Xamarin.Forms.Switch) Forms es un botón de alternancia horizontal que el usuario puede manipular para alternar entre los Estados on y OFF, que están representados por `boolean` un valor. La `Switch` clase hereda de [`View`](xref:Xamarin.Forms.View).

Las siguientes capturas de `Switch` pantallas muestran un control en su estado **de alternancia activado** y desactivado en iOS y Android:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-default.png "Conmutadores en iOS y Android")

El `Switch` control define dos propiedades:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)es un `boolean` valor que indica `Switch` si está **activado**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)es un `Color` que afecta al `Switch` modo en que se representa en el estado de alternancia o **activada**.
* `ThumbColor`es el `Color` de la Thumb del modificador.

Estas propiedades están respaldadas por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que `Switch` significa que se puede aplicar un estilo a y ser el destino de los enlaces de datos.

El `Switch` control define un `Toggled` evento que se desencadena cuando cambia `IsToggled` la propiedad, ya sea a través de la manipulación de usuarios o `IsToggled` cuando una aplicación establece la propiedad. El `ToggledEventArgs` objeto que acompaña al `Toggled` evento tiene una propiedad única denominada `Value`, de tipo `bool`. Cuando se desencadena el evento, el valor de la `Value` propiedad refleja el nuevo valor de la `IsToggled` propiedad.

## <a name="create-a-switch"></a>Crear un conmutador

Se `Switch` puede crear una instancia de en XAML. Su `IsToggled` propiedad se puede establecer para alternar `Switch`. De forma predeterminada, `IsToggled` la propiedad `false`es. En el ejemplo siguiente se muestra cómo crear una `Switch` instancia de en XAML con `IsToggled` el conjunto de propiedades opcional:

```xaml
<Switch IsToggled="true"/>
```

También `Switch` se puede crear en el código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambiar apariencia

Además de las [`Switch`](xref:Xamarin.Forms.Switch) propiedades que heredan de la [`View`](xref:Xamarin.Forms.View) clase, `Switch` también define `OnColor` las propiedades `ThumbColor` y. La `OnColor` propiedad se puede establecer para definir el `Switch` color cuando se cambia a su estado, y la `ThumbColor` propiedad se puede establecer para definir el `Color` de la Thumb del conmutador. En el ejemplo siguiente se muestra cómo crear una `Switch` instancia de en XAML con estas propiedades establecidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

También se pueden establecer las propiedades al crear un `Switch` en el código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

En la captura de pantalla `Switch` siguiente se muestra el en sus Estados **de alternancia on** y `OnColor` **OFF** , con las propiedades y `ThumbColor` establecidas:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-colors.png "Conmutadores en iOS y Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a un cambio de estado del conmutador

Cuando la `IsToggled` propiedad cambia, ya sea a través de la manipulación del usuario o `IsToggled` cuando una aplicación `Toggled` establece la propiedad, se desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<Switch Toggled="OnToggled" />
```

El archivo de código subyacente contiene el controlador para el `Toggled` eventos:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

El `sender` argumento del controlador de eventos es el `Switch` responsable de desencadenar este evento. Puede utilizar la `sender` propiedad para `Switch` tener acceso al objeto o para distinguir entre varios `Switch` objetos que comparten el mismo `Toggled` controlador de eventos.

También `Toggled` se puede asignar el controlador de eventos en el código:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Enlazar datos a un conmutador

El `Toggled` controlador de eventos se puede eliminar mediante el enlace de datos y los desencadenadores para `Switch` responder a un cambio de estado de alternancia.

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

En este [`Label`](xref:Xamarin.Forms.Label) ejemplo, utiliza una expresión `DataTrigger` de enlace en para `Switch` supervisar la `IsToggled` propiedad del denominado `styleSwitch`. Cuando esta propiedad se `true`convierte en `FontAttributes` , `FontSize` `Label` se cambian las propiedades y de. Cuando la `IsToggled` propiedad vuelve a `false`, `FontAttributes` laspropiedades`FontSize` y de serestablecenasuestadoinicial.`Label`

Para obtener información sobre los desencadenadores, consulte los desencadenadores de [Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Deshabilitar un modificador

Una aplicación puede entrar en un estado en `Switch` el que la alternancia no es una operación válida. En tales casos, `Switch` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false`. Esto impedirá que los usuarios puedan manipular el `Switch`.

## <a name="related-links"></a>Vínculos relacionados

* [Cambiar demostraciones](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
