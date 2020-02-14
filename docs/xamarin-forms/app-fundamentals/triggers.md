---
title: Desencadenadores de Xamarin.Forms
description: En este artículo se explica cómo usar desencadenadores de Xamarin.Forms para responder a cambios de la interfaz de usuario con XAML. Los desencadenadores permiten expresar acciones de forma declarativa en XAML que cambian la apariencia de controles en función de eventos o cambios en propiedades.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: d046962bf08b85069b1a698324db76a4ac3286d9
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77144656"
---
# <a name="xamarinforms-triggers"></a>Desencadenadores de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Los desencadenadores permiten expresar acciones de forma declarativa en XAML que cambian la apariencia de controles en función de eventos o cambios en propiedades.

Puede asignar un desencadenador directamente a un control o agregarlo a un diccionario de recursos de nivel de aplicación o página que se vaya a aplicar a varios controles.

Hay varios tipos de desencadenadores:

- [Desencadenador de propiedades](#property): se produce cuando una propiedad en un control se establece en un valor determinado.

- [Desencadenador de datos](#data): usa enlaces de datos para desencadenar basándose en las propiedades de otro control.

- [Desencadenador de eventos](#event): se produce cuando tiene lugar un evento en el control.

- [Multi-desencadenador](#multi): permite establecer varias condiciones de desencadenador antes de que se produzca una acción.

- [Desencadenador adaptativo](#adaptive) (VERSIÓN PRELIMINAR): reacciona a los cambios en el ancho y el alto de una ventana de la aplicación.

- [Desencadenador de comparación](#compare) (VERSIÓN PRELIMINAR): se ejecuta cuando se comparan dos valores.

- [Desencadenador de dispositivo](#device) (VERSIÓN PRELIMINAR): se produce cuando se ejecuta en el dispositivo especificado. 

- [Desencadenador de orientación](#orientation) (VERSIÓN PRELIMINAR): se ejecuta cuando cambia la orientación del dispositivo.

Para usar los desencadenadores de VERSIÓN PRELIMINAR, asegúrese de habilitarlos mediante la marca de características en `App.xaml.cs`:

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

<a name="property" />

## <a name="property-triggers"></a>Desencadenadores de propiedad

Un desencadenador simple se puede expresar puramente en XAML, mediante la incorporación de un elemento `Trigger` a la colección de desencadenadores de un control.
En este ejemplo se muestra un desencadenador que cambia un color de fondo `Entry` cuando recibe el foco:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Las partes importantes de la declaración del desencadenador son:

- **TargetType**: tipo de control al que se aplica el desencadenador.

- **Property**: propiedad en el control que se supervisa.

- **Value**: valor, cuando se produce para la propiedad supervisada, que hace que el desencadenador se active.

- **Setter**: colección de elementos `Setter` que se puede agregar cuando se cumple la condición del desencadenador. Debe especificar los elementos `Property` y `Value` que se van a establecer.

- **EnterActions y ExitActions** (no mostrados): se escriben en código y se pueden usar además de los elementos `Setter` (o en su lugar). [Se describen abajo](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Aplicar un desencadenador mediante un estilo

Los desencadenadores también se pueden agregar a una declaración `Style` en un control, en una página o una aplicación `ResourceDictionary`. Este ejemplo declara un estilo implícito (es decir, no se establece `Key`), lo que significa que se aplica a todos los controles `Entry` en la página.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Desencadenadores de datos

Los desencadenadores de datos usan enlaces de datos para supervisar otro control a fin de que se llame a los elementos `Setter`. En lugar del atributo `Property` de un desencadenador de propiedad, establezca el atributo `Binding` para supervisar el valor especificado.

El ejemplo siguiente usa la sintaxis de enlace de datos `{Binding Source={x:Reference entry}, Path=Text.Length}`,
que es como se hace referencia a las propiedades de otro control. Cuando la longitud de `entry` es cero, el desencadenador se activa. En este ejemplo el desencadenador deshabilita el botón cuando la entrada está vacía.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> Al evaluar `Path=Text.Length`, proporcione siempre un valor predeterminado para la propiedad de destino (p. ej., `Text=""`) porque, de lo contrario, será `null` y el desencadenador no funcionará según lo esperado.

Además de especificar los elementos `Setter`, también puede proporcionar [`EnterActions` y `ExitActions`](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Desencadenadores de eventos

El elemento `EventTrigger` solo requiere una propiedad `Event`, como `"Clicked"`, en el ejemplo siguiente.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenga en cuenta que no hay elementos `Setter`, sino una referencia a una clase definida por `local:NumericValidationTriggerAction` que requiere que se declare `xmlns:local` en el código XAML de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

La propia clase implementa `TriggerAction`, lo que significa que debe proporcionar una invalidación para el método `Invoke` al que se llama cada vez que se produce el evento desencadenador.

Una implementación de acción de desencadenador debe:

- Implementar la clase genérica `TriggerAction<T>`, con el parámetro genérico correspondiente al tipo de control al que se va a aplicar el desencadenador. Puede usar superclases como `VisualElement` para escribir acciones de desencadenador que funcionen con una serie de controles, o especificar un tipo de control como `Entry`.

- Invalidar el método `Invoke`: se llama a este método cada vez que se cumplen los criterios del desencadenador.

- Opcionalmente, exponer propiedades que se pueden establecer en el código XAML cuando se declara el desencadenador. Para obtener un ejemplo de esto, vea la clase `VisualElementPopTriggerAction` en la aplicación de ejemplo que lo acompaña.

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Después, el desencadenador de eventos se puede usar desde XAML:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenga cuidado al compartir desencadenadores en una instancia de `ResourceDictionary`, ya que una instancia se comparte entre controles, con lo que cualquier estado que se configure una vez se va a aplicar a todos ellos.

Tenga en cuenta que los desencadenadores de eventos no admiten los elementos `EnterActions` y `ExitActions`[descritos abajo](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Multi-desencadenadores

Un elemento `MultiTrigger` se parece `Trigger` o `DataTrigger`, salvo que en él puede haber más de una condición. Todas las condiciones deben cumplirse para que se desencadenen los elementos `Setter`.

Este es el ejemplo de un desencadenador de un botón que se enlaza a dos entradas diferentes (`email` y `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>
    <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

La colección `Conditions` también puede contener elementos `PropertyCondition` como este:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Compilar un multi-desencadenador "require all"

El multi-desencadenador solo actualiza su control cuando se cumplen todas las condiciones. Las pruebas con "todas las longitudes de campo son cero" (como una página de inicio de sesión donde deben completarse todas las entradas) son complicadas, porque se quiere una condición "where Text.Length > 0", pero esto no se puede expresar en XAML.

Se puede hacer con un elemento `IValueConverter`. El código convertidor siguiente transforma el enlace `Text.Length` en un `bool` que indica si un campo está vacío o no:

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Para usar este convertidor en un multi-desencadenador, primero agréguelo al diccionario de recursos de la página (junto con una definición de espacio de nombres personalizada `xmlns:local`):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

A continuación se muestra el código XAML. Observe las siguientes diferencias con respecto al primer ejemplo de multi-desencadenador:

- El botón tiene `IsEnabled="false"` establecido de forma predeterminada.
- Las condiciones del multi-desencadenador usan el convertidor para convertir el valor `Text.Length` en un `boolean`.
- Cuando todas las condiciones son `true`, el establecedor convierte en `true` la propiedad `IsEnabled` del botón.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Estas capturas de pantalla muestran la diferencia entre los dos ejemplos de multi-desencadenadores anteriores. En la parte superior de las pantallas, la entrada de texto con un solo elemento `Entry` basta para habilitar el botón **Guardar**.
En la parte inferior de las pantallas, el botón **Iniciar sesión** permanece inactivo hasta que ambos campos contienen datos.

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions y ExitActions

Otra forma de implementar cambios cuando se produce un desencadenador es mediante la incorporación de colecciones `EnterActions` y `ExitActions` y la especificación de implementaciones `TriggerAction<T>`.

La colección [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) se usa para definir un elemento `IList` de objetos [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) que se invocará cuando se cumpla la condición del desencadenador. La colección [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) se usa para definir un elemento `IList` de objetos `TriggerAction` que se invocará cuando ya no se cumpla la condición del desencadenador.

> [!NOTE]
> La clase [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) omite los objetos [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) definidos en las colecciones `EnterActions` y `ExitActions`.    

Puede proporcionar *tanto* `EnterActions` como `ExitActions`, así como `Setter` en un desencadenador, pero tenga en cuenta que se llama a los elementos `Setter` de inmediato (no se espera a que se completen `EnterAction` o `ExitAction`). También puede hacer todo en el código y no usar elementos `Setter` en absoluto.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Como siempre, cuando se hace referencia a una clase en XAML, se debe declarar un espacio de nombres como `xmlns:local`, como se muestra aquí:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

El código `FadeTriggerAction` se muestra a continuación:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

<a name="adaptive" />

## <a name="adaptive-trigger-preview"></a>Desencadenador adaptativo (VERSIÓN PRELIMINAR)

Una instancia de `AdaptiveTrigger` se desencadena de forma automática cuando la ventana tiene un alto o un ancho especificado. `AdaptiveTrigger` toma dos propiedades posibles:

- **MinWindowHeight**
- **MinWindowWidth**

<a name="compare"/>

## <a name="compare-trigger-preview"></a>Desencadenador de comparación (VERSIÓN PRELIMINAR)

`CompareStateTrigger` es una instancia de `StateTrigger` muy versátil que se desencadena si **Value** (Valor) es igual a **Property** (Propiedad).

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="Checked">
                <VisualState.StateTriggers>
                    <CompareStateTrigger Property="{Binding IsChecked, Source={x:Reference CheckBox}}" Value="True" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="UnChecked">
                <VisualState.StateTriggers>
                    <CompareStateTrigger Property="{Binding IsChecked, Source={x:Reference CheckBox}}" Value="False" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>     
    </VisualStateManager.VisualStateGroups>  
    <Frame
        HorizontalOptions="Center"
        VerticalOptions="Center"
        BackgroundColor="White"
        Margin="24">
        <StackLayout
            Orientation="Horizontal">
            <CheckBox 
                x:Name="CheckBox"
                VerticalOptions="Center"/>
            <Label
                Text="Checked/Uncheck the CheckBox to modify the Grid BackgroundColor"
                VerticalOptions="Center"/>
        </StackLayout>
    </Frame>
</Grid>
```

En este ejemplo se muestra cómo modificar la propiedad **BackgroundColor** de un objeto **Grid** en función del estado de la propiedad **IsChecked** de **CheckBox**. **StateTrigger** admite enlaces que abren muchas posibilidades para la comparación de valores, no solo de los elementos de la interfaz de usuario, sino también de **BindingContext**.

<a name="device" />

## <a name="device-trigger-preview"></a>Desencadenador de dispositivo (VERSIÓN PRELIMINAR)

Una instancia de `DeviceTrigger` permite controlar cómo se aplica un estado cuando se ejecuta en una plataforma de dispositivo específica, de manera similar a usar `OnPlatform`.

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState
                x:Name="Android">
                <VisualState.StateTriggers>
                    <DeviceStateTrigger Device="Android" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Blue" />
                </VisualState.Setters>
            </VisualState>
            <VisualState
                x:Name="iOS">
                <VisualState.StateTriggers>
                    <DeviceStateTrigger Device="iOS" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>  
    </VisualStateManager.VisualStateGroups>  
    <Label
        Text="This page changes the color based on the device where the App is running."
        HorizontalOptions="Center"
        VerticalOptions="Center"/>
</Grid>
```

En el ejemplo anterior, el color de fondo será azul en un dispositivo Android y rojo en un dispositivo iOS.

<a name="orientation" />

## <a name="orientation-trigger-preview"></a>Desencadenador de orientación (VERSIÓN PRELIMINAR)

Una instancia de `OrientationTrigger` permite modificar el estado de la vista cuando el dispositivo cambia entre las orientaciones horizontal y vertical.

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState
                x:Name="Landscape">
                <VisualState.StateTriggers>
                    <OrientationStateTrigger Orientation="Landscape" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Blue" />
                </VisualState.Setters>
            </VisualState>
            <VisualState
                x:Name="Portrait">
                <VisualState.StateTriggers>
                    <OrientationStateTrigger Orientation="Portrait" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>  
    <Label
        Text="This Grid changes the color based on the orientation device where the App is running."
        HorizontalOptions="Center"
        VerticalOptions="Center"/>
</Grid>
```

En el ejemplo anterior, el fondo es de color azul cuando el dispositivo está en orientación horizontal y de color rojo cuando la orientación es vertical.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de desencadenadores](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Documentación de la API de Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
