---
title: Desencadenadores de Xamarin.Forms
description: En este artículo se explica cómo usar desencadenadores de Xamarin.Forms para responder a cambios de la interfaz de usuario con XAML. Los desencadenadores permiten expresar acciones de forma declarativa en XAML que cambian la apariencia de controles en función de eventos o cambios en propiedades.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635734"
---
# <a name="xamarinforms-triggers"></a>Desencadenadores de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Los desencadenadores permiten expresar acciones de forma declarativa en XAML que cambian la apariencia de controles en función de eventos o cambios en propiedades. Además, los desencadenadores de estado, que son un grupo especializado de desencadenadores, definen cuándo se debe aplicar la clase [`VisualState`](xref:Xamarin.Forms.VisualState).

Puede asignar un desencadenador directamente a un control o agregarlo a un diccionario de recursos de nivel de aplicación o página que se vaya a aplicar a varios controles.

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

- **EnterActions y ExitActions** (no mostrados): se escriben en código y se pueden usar además de los elementos `Setter` (o en su lugar). [Se describen abajo](#enteractions-and-exitactions).

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

Además de especificar los elementos `Setter`, también puede proporcionar [`EnterActions` y `ExitActions`](#enteractions-and-exitactions).

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

Tenga en cuenta que los desencadenadores de eventos no admiten los elementos `EnterActions` y `ExitActions`[descritos abajo](#enteractions-and-exitactions).

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

## <a name="state-triggers"></a>Desencadenadores de estado

Los desencadenadores de estado se han introducido en Xamarin.Forms 4.5 y son un grupo especializado de desencadenadores que definen las condiciones en las que se debe aplicar una clase [`VisualState`](xref:Xamarin.Forms.VisualState). Sin embargo, actualmente son experimentales y solo se pueden usar con la adición de la siguiente línea de código al archivo *App.xaml.cs*:

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

Los desencadenadores de estado se agregan a la colección [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) de una clase [`VisualState`](xref:Xamarin.Forms.VisualState). Esta colección puede contener un único desencadenador de estado o varios desencadenadores de estado. Se aplicará una clase [`VisualState`](xref:Xamarin.Forms.VisualState) cuando cualquier desencadenador de estado de la colección esté activo.

Al usar desencadenadores de estado para controlar los estados visuales, Xamarin.Forms usa las siguientes reglas de prioridad para determinar qué desencadenador, y la clase [`VisualState`](xref:Xamarin.Forms.VisualState) correspondiente, se activará:

1. Cualquier desencadenador derivado de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Una clase [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activada debido a que se cumple la condición [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth).
1. Una clase [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activada debido a que se cumple la condición [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight).

Si hay varios desencadenadores activos simultáneamente (por ejemplo, dos desencadenadores personalizados), tiene prioridad el primer desencadenador declarado en el marcado.

> [!NOTE]
> Los desencadenadores de estado se pueden establecer en una clase [`Style`](xref:Xamarin.Forms.Style) o directamente en los elementos.

Para más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Desencadenador de estado

La clase [`StateTrigger`](xref:Xamarin.Forms.StateTrigger), que se deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), tiene una propiedad enlazable [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive). Un elemento `StateTrigger` desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando cambia el valor de la propiedad `IsActive`.

La clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase), que es la clase base de todos los desencadenadores de estado, tiene una propiedad [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) y un evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged). Este evento se desencadena cuando se produce un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!IMPORTANT]
> La propiedad enlazable [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) oculta la propiedad [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) heredada.

En el siguiente ejemplo de XAML, se muestra una clase [`Style`](xref:Xamarin.Forms.Style) que incluye objetos [`StateTrigger`](xref:Xamarin.Forms.StateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) implícita se destina a objetos [`Grid`](xref:Xamarin.Forms.Grid). Cuando la propiedad `IsToggled` del objeto enlazado es `true`, el color de fondo de `Grid` se establece en negro. Cuando la propiedad `IsToggled` del objeto enlazado se convierte en `false`, se desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState), y el color de fondo de `Grid` cambia a blanco.

Además, cada vez que cambia una clase [`VisualState`](xref:Xamarin.Forms.VisualState), se desencadena el evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) para el elemento `VisualState`. Cada `VisualState` registra un controlador de eventos para este evento:

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

En este ejemplo, cuando se desencadena un controlador para el evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged), el controlador indica si la clase [`VisualState`](xref:Xamarin.Forms.VisualState) está activa o no. Por ejemplo, los mensajes siguientes aparecen en la ventana de la consola cuando cambian del estado visual `Checked` al estado visual `Unchecked`:

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> Se pueden crear desencadenadores de estado personalizados a partir de la derivación de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).

### <a name="adaptive-trigger"></a>Desencadenador adaptable

Una clase [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando la ventana tiene un alto o un ancho especificado. Este desencadenador tiene dos propiedades enlazables:

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), de tipo `double`, que indica la altura mínima de la ventana a la que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), de tipo `double`, que indica la anchura mínima de la ventana a la que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Style`](xref:Xamarin.Forms.Style) que incluye objetos [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger):

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) implícita se destina a objetos [`StackLayout`](xref:Xamarin.Forms.StackLayout). Cuando el ancho de la ventana tiene entre 0 y 800 unidades independientes del dispositivo, los objetos `StackLayout` a los que se aplica la clase `Style` tendrán una orientación vertical. Cuando el ancho de la ventana es menor o igual que 800 unidades independientes del dispositivo, se desencadena el cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) y la orientación de `StackLayout` cambia a horizontal:

![Estado visual vertical del diseño de pila](triggers-images/adaptivetrigger-vertical.png "Ejemplo de AdaptiveTrigger")
![Estado visual horizontal del diseño de pila](triggers-images/adaptivetrigger-horizontal.png "Ejemplo de AdaptiveTrigger")

Las propiedades [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) y [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) se pueden utilizar de forma independiente o combinándolas entre sí. En el siguiente ejemplo de XAML se muestra la configuración de ambas propiedades:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

En este ejemplo, [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) indica que la clase correspondiente [`VisualState`](xref:Xamarin.Forms.VisualState) se aplicará cuando el ancho de la ventana actual es mayor o igual que 800 unidades independientes del dispositivo y el alto de la ventana actual es mayor o igual que 1200 unidades independientes del dispositivo.

### <a name="compare-state-trigger"></a>Comparación del desencadenador de estado

[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando una propiedad es igual a un valor específico. Este desencadenador tiene dos propiedades enlazables:

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), de tipo `object`, que indica la propiedad comparada por el desencadenador.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), de tipo `object`, que indica el valor al que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Style`](xref:Xamarin.Forms.Style) que incluye objetos [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger):

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) implícita se destina a objetos [`Grid`](xref:Xamarin.Forms.Grid). Cuando la propiedad [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) de [`CheckBox`](xref:Xamarin.Forms.CheckBox) es `false`, el color de fondo de `Grid` se establece en blanco. Cuando la propiedad `CheckBox.IsChecked` se convierte en `true`, se desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState), y el color de fondo de `Grid` cambia a negro:

[![Captura de pantalla de un cambio de estado visual desencadenado en iOS y Android](triggers-images/comparestatetrigger-unchecked.png "Ejemplo de CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Ejemplo de CompareStateTrigger")
[![Captura de pantalla de un cambio de estado visual desencadenado en iOS y Android](triggers-images/comparestatetrigger-checked.png "Ejemplo de CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Ejemplo de CompareStateTrigger")

### <a name="device-state-trigger"></a>Desencadenador de estado de dispositivos

[`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) basado en la plataforma del dispositivo en que se ejecuta la aplicación. Este desencadenador tiene una única propiedad enlazable:

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), de tipo `string`, que indica la plataforma del dispositivo en la que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Style`](xref:Xamarin.Forms.Style) que incluye objetos `DeviceStateTrigger`:

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) explícita se destina a objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Los objetos `ContentPage` que usan el estilo definen su color de fondo en plateado en iOS, en azul pálido en Android y en aguamarina en UWP. En las capturas de pantalla siguientes se muestran las páginas resultantes en iOS y Android:

[![Captura de pantalla de un cambio de estado visual desencadenado en iOS y Android](triggers-images/devicestatetrigger.png "Ejemplo de DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Ejemplo de DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Desencadenador de estado de orientación

[`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) desencadena un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando varía la orientación del dispositivo. Este desencadenador tiene una única propiedad enlazable:

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), de tipo [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation), que indica la orientación a la que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Style`](xref:Xamarin.Forms.Style) que incluye objetos `OrientationStateTrigger`:

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) explícita se destina a objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Los objetos `ContentPage` que usan el estilo definen su color de fondo en plateado cuando la orientación es vertical y en blanco cuando la orientación es horizontal.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de desencadenadores](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
- [API de desencadenador de Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
