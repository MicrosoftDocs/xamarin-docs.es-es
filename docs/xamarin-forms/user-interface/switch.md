---
title: Xamarin.Forms Conmutador
description: El Xamarin.Forms modificador es un tipo de botón que el usuario puede manipular para alternar entre los Estados de activado y desactivado. En este artículo se explica cómo usar la clase switch para mostrar un elemento de interfaz de usuario de alternancia.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b44a3fa34044327be6a928588397c3371f196336
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371136"
---
# <a name="no-locxamarinforms-switch"></a>Xamarin.Forms Conmutador

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

El Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) control es un botón de alternancia horizontal que el usuario puede manipular para alternar entre los Estados on y OFF, que están representados por un `boolean` valor. La `Switch` clase hereda de [`View`](xref:Xamarin.Forms.View) .

Las siguientes capturas de pantallas muestran un `Switch` control en su estado **de alternancia activado** y **desactivado** en iOS y Android:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-default.png "Conmutadores en iOS y Android")

El `Switch` control define las siguientes propiedades:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) es un `boolean` valor que indica si `Switch` está **activado**.
- [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) es un `Color` que afecta al modo `Switch` en que se representa en el estado de alternancia o **activada**.
- `ThumbColor` es el `Color` de la Thumb del modificador.

Estas propiedades están respaldadas por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que se puede aplicar un `Switch` estilo a y ser el destino de los enlaces de datos.

El `Switch` control define un `Toggled` evento que se desencadena cuando `IsToggled` cambia la propiedad, ya sea a través de la manipulación de usuarios o cuando una aplicación establece la `IsToggled` propiedad. El `ToggledEventArgs` objeto que acompaña al `Toggled` evento tiene una propiedad única denominada `Value` , de tipo `bool` . Cuando se desencadena el evento, el valor de la `Value` propiedad refleja el nuevo valor de la `IsToggled` propiedad.

## <a name="create-a-switch"></a>Crear un conmutador

`Switch`Se puede crear una instancia de en XAML. Su `IsToggled` propiedad se puede establecer para alternar `Switch` . De forma predeterminada, la `IsToggled` propiedad es `false` . En el ejemplo siguiente se muestra cómo crear una instancia `Switch` de en XAML con el `IsToggled` conjunto de propiedades opcional:

```xaml
<Switch IsToggled="true"/>
```

`Switch`También se puede crear en el código:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Cambiar apariencia

Además de las propiedades que [`Switch`](xref:Xamarin.Forms.Switch) heredan de la [`View`](xref:Xamarin.Forms.View) clase, `Switch` también define `OnColor` `ThumbColor` las propiedades y. La `OnColor` propiedad se puede establecer para definir el `Switch` color cuando se cambia a su estado, **on** y la `ThumbColor` propiedad se puede establecer para definir el `Color` de la Thumb del conmutador. En el ejemplo siguiente se muestra cómo crear una instancia `Switch` de en XAML con estas propiedades establecidas:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

También se pueden establecer las propiedades al crear un `Switch` en el código:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

En la captura de pantalla siguiente se muestra el `Switch` en sus Estados **de alternancia on** y **OFF** , con las `OnColor` `ThumbColor` propiedades y establecidas:

![Captura de pantalla de los cambios en los Estados activado y desactivado, en iOS y Android](switch-images/switch-states-colors.png "Conmutadores en iOS y Android")

## <a name="respond-to-a-switch-state-change"></a>Responder a un cambio de estado del conmutador

Cuando la `IsToggled` propiedad cambia, ya sea a través de la manipulación del usuario o cuando una aplicación establece la `IsToggled` propiedad, se `Toggled` desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<Switch Toggled="OnToggled" />
```

El archivo de código subyacente contiene el controlador del `Toggled` evento:

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

El `sender` argumento del controlador de eventos es el `Switch` responsable de desencadenar este evento. Puede utilizar la `sender` propiedad para tener acceso al `Switch` objeto o para distinguir entre varios `Switch` objetos que comparten el mismo `Toggled` controlador de eventos.

`Toggled`También se puede asignar el controlador de eventos en el código:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Enlazar datos a un conmutador

El `Toggled` controlador de eventos se puede eliminar mediante el enlace de datos y los desencadenadores para responder a un `Switch` cambio de estado de alternancia.

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

En este ejemplo, [`Label`](xref:Xamarin.Forms.Label) utiliza una expresión de enlace en `DataTrigger` para supervisar la `IsToggled` propiedad del `Switch` denominado `styleSwitch` . Cuando esta propiedad se convierte en `true` , `FontAttributes` `FontSize` se cambian las propiedades y de `Label` . Cuando la `IsToggled` propiedad vuelve a `false` , las `FontAttributes` `FontSize` propiedades y de `Label` se restablecen a su estado inicial.

Para obtener información acerca de los desencadenadores, vea [ Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="switch-visual-states"></a>Cambiar Estados visuales

[`Switch`](xref:Xamarin.Forms.Switch) tiene `On` `Off` Estados visuales y que se pueden usar para iniciar un cambio visual cuando [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) cambia la propiedad.

En el siguiente ejemplo de XAML se muestra cómo definir Estados visuales para los `On` `Off` Estados y:

```xaml
<Switch IsToggled="True">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="On">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="MediumSpringGreen" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Off">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Switch>
```

En este ejemplo, el `On` [`VisualState`](xref:Xamarin.Forms.VisualState) valor de especifica que cuando la [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propiedad es `true` , la `ThumbColor` propiedad se establecerá en verde primavera medio. `Off` `VisualState` Especifica que cuando la `IsToggled` propiedad es `false` , la `ThumbColor` propiedad se establecerá en rojo. Por lo tanto, el efecto general es que cuando el `Switch` está en una posición de desactivación, su Thumb es rojo, y su Thumb es verde primavera medio cuando el `Switch` está en una posición ON:

![Captura de pantalla del cambio en VisualState, en iOS y Android](switch-images/on-visualstate.png "Cambiar en VisualState") 
 ![Captura de pantalla de desactivar VisualState, en iOS y Android](switch-images/off-visualstate.png "Desactivar VisualState")

Para obtener más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-switch"></a>Deshabilitar un modificador

Una aplicación puede entrar en un estado en el que la `Switch` alternancia no es una operación válida. En tales casos, `Switch` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false` . Esto impedirá que los usuarios puedan manipular el `Switch` .

## <a name="related-links"></a>Vínculos relacionados

- [Cambiar demostraciones](/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)