---
title: El Administrador de estado Visual de Xamarin.Forms
description: Utilice Visual State Manager para realizar cambios en los elementos XAML basados en los estados visuales establecer desde el código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: 99bf55b6e956efe54510e822d379665879f3ba44
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940294"
---
# <a name="the-xamarinforms-visual-state-manager"></a>El Administrador de estado Visual de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilice Visual State Manager para realizar cambios en los elementos XAML basados en los estados visuales establecer desde el código._

El Administrador de estado Visual (VSM) es nuevo en Xamarin.Forms 3.0. VSM proporciona una manera estructurada para realizar cambios visuales en la interfaz de usuario desde el código. En la mayoría de los casos, la interfaz de usuario de la aplicación se define en XAML, y este XAML incluye marcado que describe cómo Visual State Manager afecta a los objetos visuales de la interfaz de usuario.

VSM introduce el concepto de _estados visuales_. Una vista de Xamarin.Forms, como un `Button` puede tener varios una apariencia visual distinta según su estado subyacente &mdash; si está deshabilitado, o presiona o, tiene foco de entrada. Estos son los Estados del botón.

Estados visuales se recopilan en _grupos de estado visual_. Todos los estados visuales dentro de un grupo de estados visuales son mutuamente excluyentes. Estados visuales y grupos de estado visual se identifican mediante cadenas de texto simple.

Xamarin.Forms Visual State Manager define un grupo de estados visuales denominado "CommonStates" con tres estados visuales:

- "Normal"
- "Deshabilitado"
- "Con foco"

Es compatible con todas las clases que derivan de este grupo de estados visuales [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), que es la clase base para [ `View` ](xref:Xamarin.Forms.View) y [ `Page` ](xref:Xamarin.Forms.Page).

También puede definir sus propios grupos de estados visuales y estados visuales, como en este artículo se muestran.

> [!NOTE]
> Xamarin.Forms los desarrolladores familiarizados con [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md) son conscientes de que los desencadenadores también pueden realizar cambios en los objetos visuales en la interfaz de usuario según los cambios en las propiedades de una vista o la activación de eventos. Sin embargo, usar desencadenadores para tratar con varias combinaciones de estos cambios puede ser algo confuso. Históricamente, el Administrador de estado Visual se introdujo en entornos basados en XAML de Windows para solucionar la confusión resultante de las combinaciones de estados visuales. VSM los estados visuales dentro de un grupo de estados visuales siempre son mutuamente excluyentes. En cualquier momento, solo un estado de cada grupo es el estado actual.

## <a name="the-common-states"></a>Los Estados comunes

El Administrador de estado Visual permite incluir secciones en el archivo XAML que se puede cambiar la apariencia visual de una vista si la vista normal o está deshabilitado, o tiene el foco de entrada. Estos se conocen como el _Estados comunes_.

Por ejemplo, suponga que tiene un `Entry` vista en la página, y desea que la apariencia visual de la `Entry` para cambiar de las maneras siguientes:

- El `Entry` debe tener un rosa en segundo plano cuando el `Entry` está deshabilitado.
- El `Entry` debe tener un fondo verde lima con normalidad.
- El `Entry` debe expandir a dos veces su altura normal cuando tiene foco de entrada.

Puede asociar el marcado VSM a una vista individual, o puede definir en un estilo si se aplica a varias vistas. Las dos secciones siguientes describen estos enfoques.

### <a name="vsm-markup-on-a-view"></a>Marcado VSM en una vista

Para asociar el marcado VSM a un `Entry` ver, separar primero el `Entry` en etiquetas de inicio y finalización:

```xaml
<Entry FontSize="18">

</Entry>
```

Asignó un tamaño de fuente explícita porque uno de los Estados que usará el `FontSize` propiedad duplicar el tamaño del texto en el `Entry`.

A continuación, inserte `VisualStateManager.VisualStateGroups` etiquetas entre las etiquetas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) es una propiedad enlazable adjunta definida por el [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) clase. (Para obtener más información sobre las propiedades enlazables asociadas, vea el artículo [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)). Así es como se adjunta la propiedad `VisualStateGroups` al objeto `Entry`.

El `VisualStateGroups` propiedad es de tipo [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), que es una colección de [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) objetos. Dentro de la `VisualStateManager.VisualStateGroups` etiquetas, inserte un par de `VisualStateGroup` etiquetas para cada grupo de estados visuales que desea incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Tenga en cuenta que el `VisualStateGroup` etiqueta tiene una `x:Name` atributo que indica el nombre del grupo. El `VisualStateGroup` clase define un `Name` propiedad que puede usar en su lugar:

```xaml
<VisualStateGroup Name="CommonStates">
```

Puede usar `x:Name` o `Name` , pero no ambos en el mismo elemento.

El `VisualStateGroup` clase define una propiedad denominada [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), que es una colección de [ `VisualState` ](xref:Xamarin.Forms.VisualState) objetos. `States` es el _propiedad de contenido_ de `VisualStateGroups` por lo que puede incluir la `VisualState` etiquetas directamente entre el `VisualStateGroup` etiquetas. (Contenido de las propiedades se describen en el artículo [esencial sintaxis XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

El siguiente paso es incluir un par de etiquetas para cada estado visual en ese grupo. También pueden identificarse mediante `x:Name` o `Name`:

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

`VisualState` define una propiedad denominada [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), que es una colección de [ `Setter` ](xref:Xamarin.Forms.Setter) objetos. Estas son las mismas `Setter` objetos que se usan en un [ `Style` ](xref:Xamarin.Forms.Style) objeto.

`Setters` es _no_ la propiedad content de `VisualState`, por lo que es necesario incluir las etiquetas de elemento de propiedad para el `Setters` propiedad:

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

Ahora puede insertar uno o varios `Setter` objetos entre cada par de `Setters` etiquetas. Estos son los `Setter` objetos que definen los estados visuales que se ha descrito anteriormente:

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

Cada `Setter` etiqueta indica el valor de una propiedad determinada cuando ese estado está activo. Cualquier propiedad que se hace referencia a un `Setter` objeto debe estar respaldado por una propiedad enlazable.

Similar a este marcado es la base de la **VSM en la vista** página en el **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** programa de ejemplo. La página incluye tres `Entry` vistas, pero solo la segunda se tiene la marca de VSM conectada a ella:

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

Tenga en cuenta que el segundo `Entry` también tiene un `DataTrigger` como parte de su `Trigger` colección. Esto hace que el `Entry` deshabilitarse hasta que no se escribe algo en la tercera `Entry`. Esta es la página de inicio que se ejecutan en iOS, Android y la plataforma Universal de Windows (UWP):

[![VSM en la vista: deshabilitado](vsm-images/VsmOnViewDisabled.png "VSM en la vista: deshabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

El estado visual actual es "Disabled" por lo que el fondo de la segunda `Entry` es de color fucsia en iOS y Android pantallas. La implementación de UWP de `Entry` no permite establecer el fondo de color cuando el `Entry` está deshabilitado.

Cuando escriba algún texto en la tercera `Entry`, el segundo `Entry` modificadores en el estado "Normal" y el fondo es ahora CAL:

[![VSM en vista: normal](vsm-images/VsmOnViewNormal.png "VSM en vista: normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Al tocar el segundo `Entry`, obtiene el foco de entrada. Se activa en estado "Focused" y se expande al doble del alto:

[![VSM en vista: centrado](vsm-images/VsmOnViewFocused.png "VSM en el centrado en la vista")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Tenga en cuenta que el `Entry` no conserva el fondo de color verde cuando recibe el foco de entrada. Como Visual State Manager cambia entre los estados visuales, no están establecidas las propiedades establecidas por el estado anterior. Tenga en cuenta que los estados visuales son mutuamente excluyentes. El estado "Normal" no significa solamente que el `Entry` está habilitado. Esto significa que el `Entry` está habilitado y no tiene el foco de entrada.

Si desea que el `Entry` para tener un fondo de color verde en el estado "Enfocado", agregue otro `Setter` a ese estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

En orden para estas `Setter` objetos funcione correctamente, un `VisualStateGroup` debe contener `VisualState` objetos para todos los Estados de ese grupo. Si no hay un estado visual que no tiene ningún `Setter` objetos, incluirla como una etiqueta vacía:

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Marcado de administrador de estado visual en un estilo

A menudo es necesario compartir el mismo marcado Visual State Manager entre dos o más vistas. En este caso, querrá poner el marcado en un `Style` definición.

Aquí está la existente implícito `Style` para el `Entry` elementos en el **VSM en vista** página:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Agregar `Setter` las etiquetas para el `VisualStateManager.VisualStateGroups` propiedad enlazable asociada:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La propiedad de contenido para `Setter` es `Value`, por lo que el valor de la `Value` propiedad se puede especificar directamente dentro de esas etiquetas. Que es propiedad de tipo `VisualStateGroupList`:

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

Dentro de estas etiquetas pueden incluir uno o varios `VisualStateGroup` objetos:

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

El resto del marcado VSM es igual que antes.

Este es el **VSM en estilo** página que muestra el marcado VSM completo:

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

Ahora todos los `Entry` vistas en esta página responden a la misma manera para sus estados visuales. Tenga en cuenta también que el estado de "Focused" incluye ahora un segundo `Setter` que ofrece cada `Entry` una CAL fondo también cuando tiene foco de entrada:

[![VSM en estilo](vsm-images/VsmInStyle.png "VSM en estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Estados visuales en Xamarin. Forms

En la tabla siguiente se enumeran los Estados visuales que se definen en Xamarin. Forms:

| Clase | Estados | Más información |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Estados visuales del botón](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CollectionView` | `Selected` | [Cambiar el color de los elementos seleccionados](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color) |
| `ImageButton` | `Pressed` | [Estados visuales de ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused` | [Estados comunes](#the-common-states) |

Se puede tener acceso a cada uno de estos Estados a través del grupo de Estados visuales denominado `CommonStates`.

## <a name="defining-your-own-visual-states"></a>Definir sus propios estados visuales

Cada clase que derive de `VisualElement` admite los tres estados comunes "Normal", "Con foco" y "Disabled". Internamente, el [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) clase detecta cuando se está convirtiendo en habilitado o deshabilitado, o con y sin foco y llama a estático [ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) método:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Este es el único código Visual State Manager que encontrará en el `VisualElement` clase. Dado que `GoToState` se llama para cada objeto en función de cada clase que derive de `VisualElement`, puede utilizar Visual State Manager con cualquier `VisualElement` objeto para responder a estos cambios.

Curiosamente, el nombre del grupo de estados visuales "CommonStates" no se mencionan explícitamente en `VisualElement`. El nombre del grupo no es parte de la API para el Administrador de estado Visual. Dentro de uno de los dos programa de ejemplo mostrado hasta ahora, puede cambiar el nombre del grupo de "CommonStates" en cualquier otro valor y el programa seguirá funcionando. El nombre del grupo es simplemente una descripción general de los Estados de ese grupo. Implícitamente se entiende que los estados visuales en todos los grupos se excluyen mutuamente: un estado y el estado de un único es actual en cualquier momento.

Si desea implementar sus propios estados visuales, deberá llamar a `VisualStateManager.GoToState` desde el código. Con más frecuencia, hará que esta llamada desde el archivo de código subyacente de la clase de página.

El **VSM validación** página en el **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** ejemplo muestra cómo utilizar Visual State Manager en relación con la validación de entrada. El archivo XAML consta de dos `Label` elementos, un `Entry`, y `Button`:

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

Marcado VSM está asociado a la segunda `Label` (denominado `helpLabel`) y el `Button` (denominado `submitButton`). Hay dos estados se excluyen mutuamente, denominados "Valid" y "No válido". Tenga en cuenta que contiene cada uno de los dos grupos de "ValidationState" `VisualState` etiquetas para "Valid" y "No válido", aunque uno de ellos está vacío en cada caso.

Si el `Entry` no contiene un número de teléfono válido, a continuación, el estado actual es "No válido" por lo que el segundo `Label` está visible y el `Button` está deshabilitado:

[![Validación de VSM: Estado no válido](vsm-images/VsmValidationInvalid.png "Validación de VSM: no válida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Cuando se escribe un número de teléfono válido, el estado actual es "Valid". El segundo `Entry` desaparece y el `Button` ahora está habilitado:

[![Validación de VSM: estado válido](vsm-images/VsmValidationValid.png "Validación de VSM: válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

El archivo de código subyacente es responsable de controlar el evento de `TextChanged` desde el `Entry`. El controlador utiliza una expresión regular para determinar si la cadena de entrada es válida o no. El método en el archivo de código subyacente denominado `GoToState` llama estático `VisualStateManager.GoToState` método para ambos `helpLabel` y `submitButton`:

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

Observe también que el `GoToState` se llama al método desde el constructor para inicializar el estado. Siempre debe haber un estado actual. Pero no en el código está ahí cualquier referencia al nombre del grupo de estados visuales, aunque se hace referencia en el XAML como "ValidationStates" para fines de claridad.

Tenga en cuenta que el archivo de código subyacente debe tener en cuenta todos los objetos en la página que se ve afectada por estos estados visuales y llamar a `VisualStateManager.GoToState` para cada uno de estos objetos. En este ejemplo, es solo dos objetos (el `Label` y `Button`), pero podría ser varias más.

Tal vez se pregunte: el archivo de código subyacente debe hacer referencia a todos los objetos en la página que se ve afectado por estos estados visuales, ¿por qué no el archivo de código subyacente simplemente tendrán acceso a los objetos directamente? Seguramente podría. Sin embargo, la ventaja de usar VSM es que puede controlar cómo visual elementos reaccionan a otro estado completamente en XAML, que conserva todo el diseño de interfaz de usuario en una ubicación. Esto evita la apariencia visual de configuración mediante el acceso a los elementos visuales directamente desde el código subyacente.

Puede ser tentador que considere la posibilidad de derivar una clase de `Entry` y quizás definir una propiedad que se puede establecer para una función de validación externo. La clase que derive de `Entry` , a continuación, puede llamar a la `VisualStateManager.GoToState` método. Este esquema funcionará bien, pero solo si el `Entry` era el único objeto afectado por los diferentes estados visuales. En este ejemplo, un `Label` y un `Button` son también se ve afectado. No hay ninguna manera de marcado VSM adjunta a un `Entry` a otros objetos en la página y no hay forma de control para marcado VSM asociado a estos otros objetos para hacer referencia a un cambio en el estado visual de otro objeto.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Mediante el Administrador de estado Visual para el diseño adaptable

Puede cambiar el tamaño de una aplicación que se ejecuta en un teléfono normalmente puede verse en una vertical o relación de aspecto horizontal y un programa de Xamarin.Forms que se ejecuta en el escritorio de Xamarin.Forms para asumir diferentes tamaños y proporciones. Una aplicación bien diseñada puede mostrar su contenido distinto para estos distintos factores de forma de ventana o página.

Esta técnica se conoce a veces como _diseño adaptable_. Debido a un diseño adaptable implica únicamente objetos visuales de un programa, es una aplicación de Visual State Manager ideal.

Un ejemplo sencillo es una aplicación que muestra una pequeña colección de botones que afectan al contenido de la aplicación. En modo vertical, se pueden mostrar estos botones en una fila horizontal en la parte superior de la página:

[![Diseño adaptable de VSM: vertical](vsm-images/VsmAdaptiveLayoutPortrait.png "Diseño adaptable de VSM: vertical")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En modo horizontal, la matriz de botones podría mover a un lado y muestra en una columna:

[![Diseño adaptable de VSM: horizontal](vsm-images/VsmAdaptiveLayoutLandscape.png "Diseño adaptable de VSM: horizontal")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De arriba a abajo, el programa se está ejecutando en la plataforma Universal de Windows, iOS y Android.

El **diseño adaptable VSM** página en el [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) ejemplo define un grupo denominado "OrientationStates" con dos estados visuales denominados "Vertical" y "Horizontal". (Un enfoque más complejo podría basarse en varios diferentes anchos de página o ventana).

Marcado VSM se produce en cuatro lugares en el archivo XAML. El `StackLayout` denominado `mainStack` contiene el menú y el contenido, que es un `Image` elemento. Esto `StackLayout` debe tener una orientación vertical en modo vertical y una orientación horizontal en modo horizontal:

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

Interno `ScrollView` denominado `menuScroll` y `StackLayout` denominado `menuStack` implementar en el menú de botones. La orientación de estos diseños es opuesta de `mainStack`. Debe ser el menú horizontal en modo vertical y vertical en modo horizontal.

La cuarta sección de marcado VSM está en un estilo implícito para los botones sí mismos. Este marcado establece `VerticalOptions`, `HorizontalOptions`y `Margin` propiedades específicas de las orientaciones vertical y horizontal.

Los conjuntos de archivos de código subyacente del `BindingContext` propiedad de `menuStack` para implementar `Button` comandos y también se adjunta un controlador para el `SizeChanged` eventos de la página:

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

El `SizeChanged` llamadas del controlador `VisualStateManager.GoToState` para las dos `StackLayout` y `ScrollView` elementos y, a continuación, recorre en bucle los elementos secundarios de `menuStack` para llamar a `VisualStateManager.GoToState` para el `Button` elementos.

Puede parecer como si el archivo de código subyacente puede controlar los cambios de orientación más directamente estableciendo las propiedades de elementos en el archivo XAML, pero Visual State Manager es definitivamente un enfoque más estructurado. Todos los objetos visuales se mantienen en el archivo XAML, donde se convierten en más fáciles examinar, mantienen y modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Administrador de estado visual con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo de Xamarin. Forms 3,0 visual State Manager**

## <a name="related-links"></a>Vínculos relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
