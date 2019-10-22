---
title: El administrador de estado visual de Xamarin. Forms
description: Use el administrador de estado visual para realizar cambios en los elementos XAML en función de los Estados visuales establecidos desde el código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656056"
---
# <a name="the-xamarinforms-visual-state-manager"></a>El administrador de estado visual de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use el administrador de estado visual para realizar cambios en los elementos XAML en función de los Estados visuales establecidos desde el código._

Visual State Manager (VSM) es nuevo en Xamarin. Forms 3,0. El VSM proporciona una manera estructurada de realizar cambios visuales en la interfaz de usuario desde el código. En la mayoría de los casos, la interfaz de usuario de la aplicación se define en XAML y este código XAML incluye el marcado que describe cómo afecta visual State Manager a los objetos visuales de la interfaz de usuario.

El VSM presenta el concepto de _Estados visuales_. Una vista de Xamarin. Forms como un `Button` puede tener varias apariencias visuales diferentes en función de su estado subyacente &mdash; si está deshabilitada, presionada o tiene el foco de entrada. Estos son los Estados del botón.

Los Estados visuales se recopilan en _grupos de Estados visuales_. Todos los Estados visuales de un grupo de Estados visuales se excluyen mutuamente. Los Estados visuales y los grupos de Estados visuales se identifican mediante cadenas de texto simples.

El administrador de estado visual de Xamarin. Forms define un grupo de Estados visuales denominado "CommonStates" con tres Estados visuales:

- "Normal"
- Disponible
- Dedica

Este grupo de Estados visuales es compatible con todas las clases que derivan de [`VisualElement`](xref:Xamarin.Forms.VisualElement), que es la clase base para [`View`](xref:Xamarin.Forms.View) y [`Page`](xref:Xamarin.Forms.Page). 

También puede definir sus propios grupos de Estados visuales y Estados visuales, como se muestra en este artículo.

> [!NOTE]
> Los desarrolladores de Xamarin. Forms familiarizados con los [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md) son conscientes de que los desencadenadores también pueden realizar cambios en los objetos visuales de la interfaz de usuario en función de los cambios en las propiedades de una vista o en la activación de eventos. Sin embargo, el uso de desencadenadores para tratar varias combinaciones de estos cambios puede resultar bastante confuso. Históricamente, el administrador de estado visual se presentó en entornos basados en XAML de Windows para aliviar la confusión resultante de las combinaciones de Estados visuales. Con VSM, los Estados visuales dentro de un grupo de Estados visuales siempre son mutuamente excluyentes. En cualquier momento, solo un estado de cada grupo es el estado actual.

## <a name="the-common-states"></a>Estados comunes

El administrador de estado visual le permite incluir secciones en el archivo XAML que pueden cambiar la apariencia visual de una vista si la vista es normal, está deshabilitada o tiene el foco de entrada. Estos se conocen como _Estados comunes_.

Por ejemplo, supongamos que tiene una vista `Entry` en la página y desea que el aspecto visual del `Entry` cambie de las siguientes maneras:

- El `Entry` debe tener un fondo rosa cuando la `Entry` está deshabilitada.
- Normalmente, el `Entry` debe tener un fondo verde.
- El `Entry` debe expandirse hasta el doble de su alto normal cuando tenga el foco de entrada.

Puede adjuntar el marcado de VSM a una vista individual o puede definirlo en un estilo si se aplica a varias vistas. En las dos secciones siguientes se describen estos enfoques.

### <a name="vsm-markup-on-a-view"></a>Marcado de VSM en una vista

Para asociar el marcado de VSM a una vista `Entry`, separe primero el `Entry` en etiquetas de inicio y cierre:

```xaml
<Entry FontSize="18">

</Entry>
```

Se le asigna un tamaño de fuente explícito, ya que uno de los Estados utilizará la propiedad `FontSize` para doblar el tamaño del texto en el `Entry`.

A continuación, inserte `VisualStateManager.VisualStateGroups` etiquetas entre esas etiquetas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) es una propiedad enlazable adjunta definida por la clase [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) . (Para obtener más información sobre las propiedades enlazables asociadas, vea el artículo [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)). Así es como se adjunta la propiedad `VisualStateGroups` al objeto `Entry`.

La propiedad `VisualStateGroups` es de tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), que es una colección de objetos [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) . Dentro de las etiquetas de `VisualStateManager.VisualStateGroups`, inserte un par de etiquetas de `VisualStateGroup` para cada grupo de Estados visuales que quiera incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que la etiqueta `VisualStateGroup` tiene un atributo `x:Name` que indica el nombre del grupo. La clase `VisualStateGroup` define una propiedad `Name` que puede usar en su lugar:

```xaml
<VisualStateGroup Name="CommonStates">
```

Puede usar `x:Name` o `Name`, pero no ambos en el mismo elemento.

La clase `VisualStateGroup` define una propiedad denominada [`States`](xref:Xamarin.Forms.VisualStateGroup.States), que es una colección de objetos [`VisualState`](xref:Xamarin.Forms.VisualState) . `States` es la _propiedad de contenido_ de `VisualStateGroups` para que pueda incluir las etiquetas de `VisualState` directamente entre las etiquetas de `VisualStateGroup`. (Las propiedades de contenido se describen en el artículo [sintaxis básica de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)).

El siguiente paso consiste en incluir un par de etiquetas para cada estado visual de ese grupo. También se pueden identificar mediante `x:Name` o `Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` define una propiedad denominada [`Setters`](xref:Xamarin.Forms.VisualState.Setters), que es una colección de objetos [`Setter`](xref:Xamarin.Forms.Setter) . Estos son los mismos `Setter` objetos que se usan en un objeto [`Style`](xref:Xamarin.Forms.Style) .

`Setters` _no_ es la propiedad de contenido de `VisualState`, por lo que es necesario incluir etiquetas de elemento de propiedad para la propiedad `Setters`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Ahora puede insertar uno o más objetos `Setter` entre cada par de etiquetas de `Setters`. Estos son los objetos `Setter` que definen los Estados visuales descritos anteriormente:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Cada etiqueta `Setter` indica el valor de una propiedad determinada cuando ese estado es actual. Cualquier propiedad a la que hace referencia un objeto `Setter` debe estar respaldada por una propiedad enlazable.

El marcado similar a este es la base de la página de **VSM en la vista** en el programa de ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La página incluye tres vistas de `Entry`, pero solo la segunda tiene el marcado de VSM asociado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Observe que la segunda `Entry` también tiene un `DataTrigger` como parte de su colección `Trigger`. Esto hace que se deshabilite el `Entry` hasta que se escriba algo en el tercer `Entry`. Esta es la página en el inicio que se ejecuta en iOS, Android y el Plataforma universal de Windows (UWP):

[![VSM en la vista: deshabilitado](vsm-images/VsmOnViewDisabled.png "VSM en la vista: deshabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

El estado visual actual es "deshabilitado", por lo que el fondo del segundo `Entry` es rosa en las pantallas iOS y Android. La implementación de UWP de `Entry` no permite establecer el color de fondo cuando la `Entry` está deshabilitada. 

Cuando escribe texto en el tercer `Entry`, el segundo `Entry` cambia al estado "normal" y el fondo es ahora de Lima:

[![VSM en vista: normal](vsm-images/VsmOnViewNormal.png "VSM en vista: normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Cuando toca el segundo `Entry`, obtiene el foco de entrada. Cambia al estado "centrado" y se expande a dos veces su alto:

[![VSM en vista: centrado](vsm-images/VsmOnViewFocused.png "VSM en el centrado en la vista")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Tenga en cuenta que el `Entry` no conserva el fondo de Lima cuando obtiene el foco de entrada. Como el administrador de estado visual cambia entre los Estados visuales, las propiedades establecidas por el estado anterior no están establecidas. Tenga en cuenta que los Estados visuales son mutuamente excluyentes. El estado "normal" no significa únicamente que esté habilitado el `Entry`. Significa que el `Entry` está habilitado y no tiene el foco de entrada. 

Si desea que el `Entry` tenga un fondo Lima en el estado "centrado", agregue otro `Setter` a ese estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para que estos objetos `Setter` funcionen correctamente, un `VisualStateGroup` debe contener `VisualState` objetos para todos los Estados de ese grupo. Si hay un estado visual que no tiene ningún objeto `Setter`, inclúyalo de todos modos como una etiqueta vacía:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Marcado de Visual State Manager en un estilo

A menudo es necesario compartir el mismo marcado de Visual State Manager entre dos o más vistas. En este caso, querrá colocar el marcado en una definición de `Style`.

Esta es la `Style` implícita existente para los elementos de `Entry` en la página de **VSM en la vista** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Agregue etiquetas de `Setter` para la propiedad `VisualStateManager.VisualStateGroups` enlazable adjunta:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

La propiedad de contenido para `Setter` es `Value`, por lo que el valor de la propiedad `Value` se puede especificar directamente dentro de esas etiquetas. Esa propiedad es de tipo `VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

Dentro de esas etiquetas puede incluir uno o varios objetos `VisualStateGroup`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

El resto del marcado de VSM es igual que antes.

Esta es la página **de VSM en estilo** que muestra el marcado de VSM completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Ahora todas las vistas de `Entry` de esta página responden de la misma manera a sus Estados visuales. Observe también que el estado "Focused" ahora incluye una segunda `Setter` que proporciona a cada `Entry` un fondo de verde lima también cuando tiene el foco de entrada:

[![VSM en estilo](vsm-images/VsmInStyle.png "VSM en estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Definir sus propios Estados visuales

Cada clase que se deriva de `VisualElement` admite los tres Estados comunes "normal", "Focused" y "Disabled". Internamente, la clase [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) detecta cuándo está habilitada o deshabilitada, o tiene el foco o no está enfocada, y llama al método de [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) estático:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Este es el único código de Visual State Manager que encontrará en la clase `VisualElement`. Dado que se llama a `GoToState` para cada objeto basado en cada clase que deriva de `VisualElement`, puede usar el administrador de estado visual con cualquier objeto `VisualElement` para responder a estos cambios.

Curiosamente, no se hace referencia explícitamente al nombre del grupo de Estados visuales "CommonStates" en `VisualElement`. El nombre de grupo no forma parte de la API para el administrador de estado visual. En uno de los dos programas de ejemplo mostrados hasta el momento, puede cambiar el nombre del grupo de "CommonStates" a cualquier otro y el programa seguirá funcionando. El nombre de grupo es simplemente una descripción general de los Estados de ese grupo. Se entiende implícitamente que los Estados visuales de cualquier grupo se excluyen mutuamente: un estado y solo un estado es actual en cualquier momento.

Si desea implementar sus propios Estados visuales, deberá llamar a `VisualStateManager.GoToState` desde el código. Lo más frecuente es que realice esta llamada desde el archivo de código subyacente de la clase de página.

En la página de **validación de VSM** en el ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** se muestra cómo usar el administrador de estado visual en conexión con la validación de entrada. El archivo XAML está compuesto de dos `Label` elementos, un `Entry` y `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

El marcado de VSM se adjunta al segundo `Label` (denominado `helpLabel`) y al `Button` (denominado `submitButton`). Hay dos Estados mutuamente exclusivos, denominados "válido" y "no válido". Observe que cada uno de los dos grupos "ValidationState" contiene etiquetas `VisualState` para "Valid" y "Invalid", aunque uno de ellos está vacío en cada caso. 

Si el `Entry` no contiene un número de teléfono válido, el estado actual es "no válido", por lo que el segundo `Label` es visible y el `Button` está deshabilitado:

[![Validación de VSM: Estado no válido](vsm-images/VsmValidationInvalid.png "Validación de VSM: no válida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Cuando se escribe un número de teléfono válido, el estado actual se convierte en "válido". La segunda `Entry` desaparece y la `Button` ahora está habilitada:

[![Validación de VSM: estado válido](vsm-images/VsmValidationValid.png "Validación de VSM: válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

El archivo de código subyacente es de respuesta para controlar el evento de `TextChanged` desde el `Entry`. El controlador utiliza una expresión regular para determinar si la cadena de entrada es válida o no. El método en el archivo de código subyacente denominado `GoToState` llama al método de `VisualStateManager.GoToState` estático para `helpLabel` y `submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Observe también que se llama al método `GoToState` desde el constructor para inicializar el estado. Siempre debe haber un estado actual. Pero en el código no hay ninguna referencia al nombre del grupo de Estados visuales, aunque se hace referencia a él en el XAML como "ValidationStates" para fines de claridad. 

Observe que el archivo de código subyacente debe tener en cuenta todos los objetos de la página afectados por estos Estados visuales y llamar a `VisualStateManager.GoToState` para cada uno de estos objetos. En este ejemplo, solo hay dos objetos (el `Label` y el `Button`), pero podría ser más.

Es posible que se pregunte: Si el archivo de código subyacente debe hacer referencia a todos los objetos de la página afectados por estos Estados visuales, ¿por qué no se puede acceder al archivo de código subyacente simplemente a los objetos? Seguramente podría. Sin embargo, la ventaja de utilizar el VSM es que puede controlar el modo en que los elementos visuales reaccionan a un estado diferente completamente en XAML, lo que mantiene todo el diseño de la interfaz de usuario en una ubicación. Esto evita establecer la apariencia visual al obtener acceso a los elementos visuales directamente desde el código subyacente.

Podría ser tentador considerar la posibilidad de derivar una clase de `Entry` y quizás definir una propiedad que se puede establecer en una función de validación externa. La clase que deriva de `Entry` puede llamar al método `VisualStateManager.GoToState`. Este esquema funciona bien, pero solo si el `Entry` era el único objeto afectado por los distintos Estados visuales. En este ejemplo, también se ven afectadas un `Label` y un `Button`. No hay ninguna manera de que el marcado de VSM se adjunte a una `Entry` para controlar otros objetos en la página y no hay forma de que el marcado de VSM asociado a estos otros objetos haga referencia a un cambio en el estado visual de otro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Uso del administrador de estado visual para el diseño adaptable

Una aplicación de Xamarin. Forms que se ejecuta en un teléfono se puede ver normalmente en una relación de aspecto vertical u horizontal, y se puede cambiar el tamaño de un programa de Xamarin. Forms que se ejecuta en el escritorio para que asuma muchos tamaños diferentes y relaciones de aspecto. Una aplicación bien diseñada podría mostrar su contenido de forma diferente para estos diversos factores de página o de formulario de ventana. 

A veces, esta técnica se conoce como _diseño adaptable_. Dado que el diseño adaptable solo implica los objetos visuales de un programa, es una aplicación ideal de Visual State Manager.

Un ejemplo sencillo es una aplicación que muestra una pequeña colección de botones que afectan al contenido de la aplicación. En el modo vertical, estos botones se pueden mostrar en una fila horizontal en la parte superior de la página:

[![Diseño adaptable de VSM: vertical](vsm-images/VsmAdaptiveLayoutPortrait.png "Diseño adaptable de VSM: vertical")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En el modo horizontal, la matriz de botones puede moverse a un lado y mostrarse en una columna:

[![Diseño adaptable de VSM: horizontal](vsm-images/VsmAdaptiveLayoutLandscape.png "Diseño adaptable de VSM: horizontal")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Desde la parte superior a la inferior, el programa se ejecuta en el Plataforma universal de Windows, Android e iOS.

La página de **diseño adaptable de VSM** en el ejemplo [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define un grupo denominado "OrientationStates" con dos Estados visuales denominados "vertical" y "horizontal". (Un enfoque más complejo podría basarse en varios anchos de página o de ventana diferentes). 

El marcado de VSM se produce en cuatro lugares en el archivo XAML. El `StackLayout` denominado `mainStack` contiene el menú y el contenido, que es un elemento `Image`. Esta `StackLayout` debe tener una orientación vertical en modo vertical y una orientación horizontal en modo horizontal:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

El `ScrollView` interno denominado `menuScroll` y el `StackLayout` denominado `menuStack` implementar el menú de botones. La orientación de estos diseños es opuesta a `mainStack`. El menú debe ser horizontal en modo vertical y vertical en modo horizontal.

La cuarta sección del marcado de VSM está en un estilo implícito para los propios botones. Este marcado establece `VerticalOptions`, `HorizontalOptions` y `Margin` propiedades específicas de las orientaciones de Portait y horizontal.

El archivo de código subyacente establece la propiedad `BindingContext` de `menuStack` para implementar los comandos `Button` y también asocia un controlador al evento `SizeChanged` de la página:

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

El controlador de `SizeChanged` llama a `VisualStateManager.GoToState` para los dos elementos `StackLayout` y `ScrollView` y, a continuación, recorre en bucle los elementos secundarios de `menuStack` para llamar a `VisualStateManager.GoToState` para los elementos `Button`.

Puede parecer que si el archivo de código subyacente puede controlar los cambios de orientación más directamente estableciendo las propiedades de los elementos en el archivo XAML, pero el administrador de estado visual es definitivamente un enfoque más estructurado. Todos los objetos visuales se guardan en el archivo XAML, donde resultan más fáciles de examinar, mantener y modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Visual State Manager con Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo de Xamarin. Forms 3,0 visual State Manager**

## <a name="related-links"></a>Vínculos relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
