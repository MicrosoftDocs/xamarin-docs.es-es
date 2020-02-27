---
title: Administrador de estado visual de Xamarin. Forms
description: Utilice Visual State Manager para realizar cambios en los elementos XAML basados en los estados visuales establecer desde el código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: 086adee4dc6b921abe92f6486186023a3125695c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636075"
---
# <a name="xamarinforms-visual-state-manager"></a>Administrador de estado visual de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use el administrador de estado visual para realizar cambios en los elementos XAML en función de los Estados visuales establecidos desde el código._

Visual State Manager (VSM) proporciona una manera estructurada de realizar cambios visuales en la interfaz de usuario desde el código. En la mayoría de los casos, la interfaz de usuario de la aplicación se define en XAML, y este XAML incluye marcado que describe cómo Visual State Manager afecta a los objetos visuales de la interfaz de usuario.

El VSM presenta el concepto de _Estados visuales_. Una vista de Xamarin. Forms como un `Button` puede tener varias apariencias visuales diferentes en función de su estado subyacente &mdash; si está deshabilitada, presionada o tiene el foco de entrada. Estos son los Estados del botón.

Los Estados visuales se recopilan en _grupos de Estados visuales_. Todos los estados visuales dentro de un grupo de estados visuales son mutuamente excluyentes. Estados visuales y grupos de estado visual se identifican mediante cadenas de texto simple.

El administrador de estado visual de Xamarin. Forms define un grupo de Estados visuales denominado "CommonStates" con los siguientes Estados visuales:

- "Normal"
- "Deshabilitado"
- "Con foco"
- Seleccionadas

Este grupo de Estados visuales es compatible con todas las clases que derivan de [`VisualElement`](xref:Xamarin.Forms.VisualElement), que es la clase base para [`View`](xref:Xamarin.Forms.View) y [`Page`](xref:Xamarin.Forms.Page).

También puede definir sus propios grupos de estados visuales y estados visuales, como en este artículo se muestran.

> [!NOTE]
> Los desarrolladores de Xamarin. Forms familiarizados con los [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md) son conscientes de que los desencadenadores también pueden realizar cambios en los objetos visuales de la interfaz de usuario en función de los cambios en las propiedades de una vista o en la activación de eventos. Sin embargo, usar desencadenadores para tratar con varias combinaciones de estos cambios puede ser algo confuso. Históricamente, el Administrador de estado Visual se introdujo en entornos basados en XAML de Windows para solucionar la confusión resultante de las combinaciones de estados visuales. VSM los estados visuales dentro de un grupo de estados visuales siempre son mutuamente excluyentes. En cualquier momento, solo un estado de cada grupo es el estado actual.

## <a name="common-states"></a>Estados comunes

El administrador de estado visual le permite incluir marcado en el archivo XAML que puede cambiar la apariencia visual de una vista si la vista es normal, está deshabilitada o tiene el foco de entrada. Estos se conocen como _Estados comunes_.

Por ejemplo, supongamos que tiene una vista `Entry` en la página y desea que el aspecto visual del `Entry` cambie de las siguientes maneras:

- El `Entry` debe tener un fondo rosa cuando la `Entry` está deshabilitada.
- Normalmente, el `Entry` debe tener un fondo verde.
- El `Entry` debe expandirse hasta el doble de su alto normal cuando tenga el foco de entrada.

Puede asociar el marcado VSM a una vista individual, o puede definir en un estilo si se aplica a varias vistas. Las dos secciones siguientes describen estos enfoques.

### <a name="vsm-markup-on-a-view"></a>Marcado VSM en una vista

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

El siguiente paso es incluir un par de etiquetas para cada estado visual en ese grupo. También se pueden identificar mediante `x:Name` o `Name`:

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

Observe que la segunda `Entry` también tiene un `DataTrigger` como parte de su colección `Trigger`. Esto hace que se deshabilite el `Entry` hasta que se escriba algo en el tercer `Entry`. Esta es la página de inicio que se ejecutan en iOS, Android y la plataforma Universal de Windows (UWP):

[![VSM en la vista: deshabilitado](vsm-images/VsmOnViewDisabled.png "VSM en la vista: deshabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

El estado visual actual es "deshabilitado", por lo que el fondo del segundo `Entry` es rosa en las pantallas iOS y Android. La implementación de UWP de `Entry` no permite establecer el color de fondo cuando la `Entry` está deshabilitada.

Cuando escribe texto en el tercer `Entry`, el segundo `Entry` cambia al estado "normal" y el fondo es ahora de Lima:

[![VSM en vista: normal](vsm-images/VsmOnViewNormal.png "VSM en vista: normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Cuando toca el segundo `Entry`, obtiene el foco de entrada. Se activa en estado "Focused" y se expande al doble del alto:

[![VSM en vista: centrado](vsm-images/VsmOnViewFocused.png "VSM en el centrado en la vista")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Tenga en cuenta que el `Entry` no conserva el fondo de Lima cuando obtiene el foco de entrada. Como Visual State Manager cambia entre los estados visuales, no están establecidas las propiedades establecidas por el estado anterior. Tenga en cuenta que los estados visuales son mutuamente excluyentes. El estado "normal" no significa únicamente que esté habilitado el `Entry`. Significa que el `Entry` está habilitado y no tiene el foco de entrada.

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

### <a name="visual-state-manager-markup-in-a-style"></a>Marcado de administrador de estado visual en un estilo

A menudo es necesario compartir el mismo marcado Visual State Manager entre dos o más vistas. En este caso, querrá colocar el marcado en una definición de `Style`.

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

El resto del marcado VSM es igual que antes.

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

## <a name="visual-states-in-xamarinforms"></a>Estados visuales en Xamarin. Forms

En la tabla siguiente se enumeran los Estados visuales que se definen en Xamarin. Forms:

| Clase | States | Más información |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Estados visuales del botón](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [Estados visuales de CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `CollectionView` | `Selected` | [Cambiar el color de los elementos seleccionados](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color) |
| `ImageButton` | `Pressed` | [Estados visuales de ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [Estados comunes](#common-states) |

Se puede tener acceso a cada uno de estos Estados a través del grupo de Estados visuales denominado `CommonStates`.

## <a name="set-state-on-multiple-elements"></a>Establecer el estado en varios elementos

En los ejemplos anteriores, los Estados visuales se adjuntaron y funcionaban en elementos únicos. Sin embargo, también es posible crear Estados visuales que estén asociados a un único elemento, pero que establezcan propiedades en otros elementos dentro del mismo ámbito. Esto evita tener que repetir Estados visuales en cada elemento en el que operan los Estados.

El tipo de [`Setter`](xref:Xamarin.Forms.Setter) tiene una propiedad `TargetName`, de tipo `string`, que representa el elemento de destino que manipulará el `Setter` para un estado visual. Cuando se define la propiedad `TargetName`, el `Setter` establece el `Property` del elemento definido en `TargetName` para `Value`:

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

En este ejemplo, una `Label` denominada `label` tendrá su propiedad `TextColor` establecida en `Red`. Al establecer la propiedad `TargetName` debe especificar la ruta de acceso completa a la propiedad en `Property`. Por consiguiente, para establecer la propiedad `TextColor` en un `Label`, `Property` se especifica como `Label.TextColor`.

> [!NOTE]
> Cualquier propiedad a la que hace referencia un objeto `Setter` debe estar respaldada por una propiedad enlazable.

En la página **VSM with Setter TargetName** en el ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** se muestra cómo establecer el estado en varios elementos, a partir de un único grupo de Estados visuales. El archivo XAML está compuesto de un `StackLayout` que contiene un elemento `Label`, un `Entry`y un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

El marcado de VSM está asociado a la `StackLayout`. Hay dos Estados mutuamente exclusivos, denominados "normal" y "pressed", con cada Estado que contiene etiquetas `VisualState`.

El estado "normal" está activo cuando no se presiona el `Button` y se puede escribir una respuesta a la pregunta:

[![VSM Setter TargetName: estado normal](vsm-images/VsmSetterTargetNameNormal.png "VSM Setter TargetName-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

El estado "pressed" se activa cuando se presiona el `Button`:

[![VSM Setter TargetName: estado pressed](vsm-images/VsmSetterTargetNamePressed.png "VSM Setter TargetName-pressed")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

El `VisualState` "pressed" especifica que, cuando se presiona el `Button`, su propiedad `Scale` se cambiará del valor predeterminado de 1 a 0,8. Además, el `Entry` denominado `entry` tendrá su propiedad `Text` establecida en París. Por lo tanto, el resultado es que, cuando se presiona el `Button`, se cambia de tamaño para que sea ligeramente menor y el `Entry` muestra París. A continuación, cuando se libera el `Button` se vuelve a escalar a su valor predeterminado de 1 y el `Entry` muestra cualquier texto escrito anteriormente.

> [!IMPORTANT]
> Actualmente no se admiten las rutas de acceso de propiedades en elementos `Setter` que especifican la propiedad `TargetName`.

## <a name="define-your-own-visual-states"></a>Definir sus propios Estados visuales

Cada clase que se deriva de `VisualElement` admite los tres Estados comunes "normal", "Focused" y "Disabled". Internamente, la clase [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) detecta cuándo está habilitada o deshabilitada, o tiene el foco o no está enfocada, y llama al método de [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) estático:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Este es el único código de Visual State Manager que encontrará en la clase `VisualElement`. Dado que se llama a `GoToState` para cada objeto basado en cada clase que deriva de `VisualElement`, puede usar el administrador de estado visual con cualquier objeto `VisualElement` para responder a estos cambios.

Curiosamente, no se hace referencia explícitamente al nombre del grupo de Estados visuales "CommonStates" en `VisualElement`. El nombre del grupo no es parte de la API para el Administrador de estado Visual. Dentro de uno de los dos programa de ejemplo mostrado hasta ahora, puede cambiar el nombre del grupo de "CommonStates" en cualquier otro valor y el programa seguirá funcionando. El nombre del grupo es simplemente una descripción general de los Estados de ese grupo. Implícitamente se entiende que los estados visuales en todos los grupos se excluyen mutuamente: un estado y el estado de un único es actual en cualquier momento.

Si desea implementar sus propios Estados visuales, deberá llamar a `VisualStateManager.GoToState` desde el código. Con más frecuencia, hará que esta llamada desde el archivo de código subyacente de la clase de página.

En la página de **validación de VSM** en el ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** se muestra cómo usar el administrador de estado visual en conexión con la validación de entrada. El archivo XAML consta de una `StackLayout` que contiene dos elementos `Label`, un `Entry`y un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

El marcado de VSM se adjunta al `StackLayout` (denominado `stackLayout`). Hay dos Estados mutuamente exclusivos, denominados "Valid" y "no válidos", con cada Estado que contiene etiquetas `VisualState`.

Si el `Entry` no contiene un número de teléfono válido, el estado actual es "no válido", por lo que el `Entry` tiene un fondo rosa, el segundo `Label` es visible y el `Button` está deshabilitado:

[![Validación de VSM: Estado no válido](vsm-images/VsmValidationInvalid.png "Validación de VSM: no válida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Cuando se escribe un número de teléfono válido, el estado actual es "Valid". El `Entry` obtiene un fondo de Lima, el segundo `Label` desaparece y la `Button` está habilitada ahora:

[![Validación de VSM: estado válido](vsm-images/VsmValidationValid.png "Validación de VSM: válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

El archivo de código subyacente es responsable de controlar el evento de `TextChanged` desde el `Entry`. El controlador utiliza una expresión regular para determinar si la cadena de entrada es válida o no. El método en el archivo de código subyacente denominado `GoToState` llama al método de `VisualStateManager.GoToState` estático para `stackLayout`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

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
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

Observe también que se llama al método `GoToState` desde el constructor para inicializar el estado. Siempre debe haber un estado actual. Pero no en el código está ahí cualquier referencia al nombre del grupo de estados visuales, aunque se hace referencia en el XAML como "ValidationStates" para fines de claridad.

Observe que el archivo de código subyacente solo debe tener en cuenta el objeto de la página que define los Estados visuales y llamar a `VisualStateManager.GoToState` para este objeto. Esto se debe a que ambos Estados visuales tienen como destino varios objetos en la página.

Es posible que se pregunte: Si el archivo de código subyacente debe hacer referencia al objeto en la página que define los Estados visuales, ¿por qué no se puede acceder al archivo de código subyacente simplemente a este y a otros objetos directamente? Seguramente podría. Sin embargo, la ventaja de usar VSM es que puede controlar cómo visual elementos reaccionan a otro estado completamente en XAML, que conserva todo el diseño de interfaz de usuario en una ubicación. Esto evita la apariencia visual de configuración mediante el acceso a los elementos visuales directamente desde el código subyacente.

## <a name="visual-state-triggers"></a>Desencadenadores de estado visual

Los Estados visuales admiten desencadenadores de estado, que son un grupo especializado de desencadenadores que definen las condiciones en las que se debe aplicar un [`VisualState`](xref:Xamarin.Forms.VisualState) .

Los desencadenadores de estado se agregan a la colección de [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) de una [`VisualState`](xref:Xamarin.Forms.VisualState). Esta colección puede contener un único desencadenador de estado o varios desencadenadores de estado. Se aplicará un [`VisualState`](xref:Xamarin.Forms.VisualState) cuando todos los desencadenadores de estado de la colección estén activos.

Al usar desencadenadores de estado para controlar los Estados visuales, Xamarin. Forms usa las siguientes reglas de prioridad para determinar qué desencadenador (y [`VisualState`](xref:Xamarin.Forms.VisualState)correspondiente) estará activo:

1. Cualquier desencadenador que se derive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activado debido a que se cumple la condición de [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) .
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activado debido a que se cumple la condición de [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) .

Si hay varios desencadenadores activos simultáneamente (por ejemplo, dos desencadenadores personalizados), el primer desencadenador declarado en el marcado tiene prioridad.

Para obtener más información acerca de los desencadenadores de estado, consulte [desencadenadores de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Usar el administrador de estado visual para el diseño adaptable

Puede cambiar el tamaño de una aplicación que se ejecuta en un teléfono normalmente puede verse en una vertical o relación de aspecto horizontal y un programa de Xamarin.Forms que se ejecuta en el escritorio de Xamarin.Forms para asumir diferentes tamaños y proporciones. Una aplicación bien diseñada puede mostrar su contenido distinto para estos distintos factores de forma de ventana o página.

A veces, esta técnica se conoce como _diseño adaptable_. Debido a un diseño adaptable implica únicamente objetos visuales de un programa, es una aplicación de Visual State Manager ideal.

Un ejemplo sencillo es una aplicación que muestra una pequeña colección de botones que afectan al contenido de la aplicación. En modo vertical, se pueden mostrar estos botones en una fila horizontal en la parte superior de la página:

[![Diseño adaptable de VSM: vertical](vsm-images/VsmAdaptiveLayoutPortrait.png "Diseño adaptable de VSM: vertical")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En modo horizontal, la matriz de botones podría mover a un lado y muestra en una columna:

[![Diseño adaptable de VSM: horizontal](vsm-images/VsmAdaptiveLayoutLandscape.png "Diseño adaptable de VSM: horizontal")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De arriba a abajo, el programa se está ejecutando en la plataforma Universal de Windows, iOS y Android.

La página de **diseño adaptable de VSM** en el ejemplo [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define un grupo denominado "OrientationStates" con dos Estados visuales denominados "vertical" y "horizontal". (Un enfoque más complejo podría basarse en varios diferentes anchos de página o ventana).

Marcado VSM se produce en cuatro lugares en el archivo XAML. El `StackLayout` denominado `mainStack` contiene el menú y el contenido, que es un elemento `Image`. Esta `StackLayout` debe tener una orientación vertical en modo vertical y una orientación horizontal en modo horizontal:

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

El `ScrollView` interno denominado `menuScroll` y el `StackLayout` denominado `menuStack` implementar el menú de botones. La orientación de estos diseños es opuesta a `mainStack`. Debe ser el menú horizontal en modo vertical y vertical en modo horizontal.

La cuarta sección de marcado VSM está en un estilo implícito para los botones sí mismos. Este marcado establece `VerticalOptions`, `HorizontalOptions`y `Margin` propiedades específicas de las orientaciones vertical y horizontal.

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

Puede parecer como si el archivo de código subyacente puede controlar los cambios de orientación más directamente estableciendo las propiedades de elementos en el archivo XAML, pero Visual State Manager es definitivamente un enfoque más estructurado. Todos los objetos visuales se mantienen en el archivo XAML, donde se convierten en más fáciles examinar, mantienen y modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Administrador de estado visual con Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vídeo de Xamarin. Forms 3,0 visual State Manager**

## <a name="related-links"></a>Vínculos relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Desencadenadores de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
