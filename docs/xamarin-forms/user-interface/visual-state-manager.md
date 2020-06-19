---
title: Administrador de estado visual de Xamarin.Forms
description: Use el administrador de estado visual para realizar cambios en los elementos XAML en función de los Estados visuales establecidos desde el código.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3dda730446ec2b4268f42ee5af853400b33565d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946236"
---
# <a name="xamarinforms-visual-state-manager"></a>Administrador de estado visual de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Use el administrador de estado visual para realizar cambios en los elementos XAML en función de los Estados visuales establecidos desde el código._

Visual State Manager (VSM) proporciona una manera estructurada de realizar cambios visuales en la interfaz de usuario desde el código. En la mayoría de los casos, la interfaz de usuario de la aplicación se define en XAML y este código XAML incluye el marcado que describe cómo afecta visual State Manager a los objetos visuales de la interfaz de usuario.

El VSM presenta el concepto de _Estados visuales_. Una Xamarin.Forms vista como `Button` puede tener varias apariencias visuales diferentes en función de su estado subyacente &mdash; , independientemente de si está deshabilitada, presionada o tiene el foco de entrada. Estos son los Estados del botón.

Los Estados visuales se recopilan en _grupos de Estados visuales_. Todos los Estados visuales de un grupo de Estados visuales se excluyen mutuamente. Los Estados visuales y los grupos de Estados visuales se identifican mediante cadenas de texto simples.

Xamarin.FormsVisual State Manager define un grupo de Estados visuales denominado "CommonStates" con los siguientes Estados visuales:

- "Normal"
- Disponible
- Dedica
- Seleccionadas

Este grupo de Estados visuales es compatible con todas las clases que derivan de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , que es la clase base para [`View`](xref:Xamarin.Forms.View) y [`Page`](xref:Xamarin.Forms.Page) .

También puede definir sus propios grupos de Estados visuales y Estados visuales, como se muestra en este artículo.

> [!NOTE]
> Xamarin.Formslos desarrolladores familiarizados con los [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md) son conscientes de que los desencadenadores también pueden realizar cambios en los objetos visuales de la interfaz de usuario en función de los cambios en las propiedades de una vista o en la activación de eventos. Sin embargo, el uso de desencadenadores para tratar varias combinaciones de estos cambios puede resultar bastante confuso. Históricamente, el administrador de estado visual se presentó en entornos basados en XAML de Windows para aliviar la confusión resultante de las combinaciones de Estados visuales. Con VSM, los Estados visuales dentro de un grupo de Estados visuales siempre son mutuamente excluyentes. En cualquier momento, solo un estado de cada grupo es el estado actual.

## <a name="common-states"></a>Estados comunes

El administrador de estado visual le permite incluir marcado en el archivo XAML que puede cambiar la apariencia visual de una vista si la vista es normal, está deshabilitada o tiene el foco de entrada. Estos se conocen como _Estados comunes_.

Por ejemplo, supongamos que tiene una `Entry` vista en la página y desea que el aspecto visual del `Entry` cambie de las siguientes maneras:

- El `Entry` debe tener un fondo rosa cuando el `Entry` está deshabilitado.
- El `Entry` debe tener normalmente un fondo verde.
- `Entry`Debe expandirse al doble de su alto normal cuando tenga el foco de entrada.

Puede adjuntar el marcado de VSM a una vista individual o puede definirlo en un estilo si se aplica a varias vistas. En las dos secciones siguientes se describen estos enfoques.

### <a name="vsm-markup-on-a-view"></a>Marcado de VSM en una vista

Para adjuntar el marcado de VSM a una `Entry` vista, separe primero el `Entry` en las etiquetas inicial y final:

```xaml
<Entry FontSize="18">

</Entry>
```

Se le asigna un tamaño de fuente explícito, ya que uno de los Estados utilizará la `FontSize` propiedad para doblar el tamaño del texto en `Entry` .

A continuación, inserte `VisualStateManager.VisualStateGroups` etiquetas entre esas etiquetas:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)es una propiedad enlazable adjunta definida por la [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) clase. (Para obtener más información sobre las propiedades enlazables asociadas, vea el artículo [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md)). Así es como `VisualStateGroups` se adjunta la propiedad al `Entry` objeto.

La `VisualStateGroups` propiedad es de tipo [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList) , que es una colección de [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) objetos. Dentro de las `VisualStateManager.VisualStateGroups` etiquetas, inserte un par de `VisualStateGroup` etiquetas para cada grupo de Estados visuales que quiera incluir:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Observe que la `VisualStateGroup` etiqueta tiene un `x:Name` atributo que indica el nombre del grupo. La `VisualStateGroup` clase define una `Name` propiedad que puede usar en su lugar:

```xaml
<VisualStateGroup Name="CommonStates">
```

Puede usar o, `x:Name` `Name` pero no ambos en el mismo elemento.

La `VisualStateGroup` clase define una propiedad denominada [`States`](xref:Xamarin.Forms.VisualStateGroup.States) , que es una colección de [`VisualState`](xref:Xamarin.Forms.VisualState) objetos. `States`es la _propiedad de contenido_ de `VisualStateGroups` , por lo que puede incluir las `VisualState` etiquetas directamente entre las `VisualStateGroup` etiquetas. (Las propiedades de contenido se describen en el artículo [sintaxis básica de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)).

El siguiente paso consiste en incluir un par de etiquetas para cada estado visual de ese grupo. También se pueden identificar mediante `x:Name` o `Name` :

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

`VisualState`define una propiedad denominada [`Setters`](xref:Xamarin.Forms.VisualState.Setters) , que es una colección de [`Setter`](xref:Xamarin.Forms.Setter) objetos. Estos son los mismos `Setter` objetos que se usan en un [`Style`](xref:Xamarin.Forms.Style) objeto.

`Setters`_no_ es la propiedad de contenido de `VisualState` , por lo que es necesario incluir etiquetas de elemento de propiedad para la `Setters` propiedad:

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

Ahora puede insertar uno o más `Setter` objetos entre cada par de `Setters` etiquetas. Estos son los `Setter` objetos que definen los Estados visuales descritos anteriormente:

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

Cada `Setter` etiqueta indica el valor de una propiedad determinada cuando ese estado es actual. Cualquier propiedad a la que hace referencia un `Setter` objeto debe estar respaldada por una propiedad enlazable.

El marcado similar a este es la base de la página de **VSM en la vista** en el programa de ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La página incluye tres `Entry` vistas, pero solo la segunda tiene el marcado de VSM asociado:

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

Observe que el segundo `Entry` también tiene `DataTrigger` como parte de su `Trigger` colección. Esto hace que `Entry` se deshabilite el objeto hasta que se escriba algo en el tercero `Entry` . Esta es la página en el inicio que se ejecuta en iOS, Android y el Plataforma universal de Windows (UWP):

[![VSM en la vista: deshabilitado](vsm-images/VsmOnViewDisabled.png "VSM en la vista: deshabilitado")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

El estado visual actual es "deshabilitado", por lo que el segundo plano `Entry` es rosa en las pantallas iOS y Android. La implementación de UWP de no `Entry` permite establecer el color de fondo cuando `Entry` está deshabilitado.

Al escribir un texto en el tercero `Entry` , el segundo `Entry` cambia al estado "normal" y el fondo es ahora Lima:

[![VSM en vista: normal](vsm-images/VsmOnViewNormal.png "VSM en vista: normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Cuando toca el segundo `Entry` , obtiene el foco de entrada. Cambia al estado "centrado" y se expande a dos veces su alto:

[![VSM en vista: centrado](vsm-images/VsmOnViewFocused.png "VSM en el centrado en la vista")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Tenga en cuenta que el no `Entry` conserva el fondo de Lima cuando obtiene el foco de entrada. Como el administrador de estado visual cambia entre los Estados visuales, las propiedades establecidas por el estado anterior no están establecidas. Tenga en cuenta que los Estados visuales son mutuamente excluyentes. El estado "normal" no significa únicamente que `Entry` esté habilitado. Significa que `Entry` está habilitado y no tiene el foco de entrada.

Si desea que el `Entry` tenga un fondo Lima en el estado "centrado", agregue otro `Setter` a ese estado visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Para que estos `Setter` objetos funcionen correctamente, `VisualStateGroup` debe contener `VisualState` objetos para todos los Estados de ese grupo. Si hay un estado visual que no tiene ningún `Setter` objeto, inclúyalo de todos modos como una etiqueta vacía:

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Marcado de Visual State Manager en un estilo

A menudo es necesario compartir el mismo marcado de Visual State Manager entre dos o más vistas. En este caso, querrá colocar el marcado en una `Style` definición.

Este es el implícito existente `Style` para los `Entry` elementos de la página de **VSM en la vista** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Agregue `Setter` etiquetas para la `VisualStateManager.VisualStateGroups` propiedad enlazable adjunta:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La propiedad de contenido de `Setter` es `Value` , por lo que el valor de la `Value` propiedad se puede especificar directamente dentro de esas etiquetas. Esa propiedad es de tipo `VisualStateGroupList` :

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

Dentro de esas etiquetas puede incluir uno o varios `VisualStateGroup` objetos:

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

Ahora todas las `Entry` vistas de esta página responden de la misma manera a sus Estados visuales. Observe también que el estado "Focused" ahora incluye un segundo `Setter` que proporciona cada `Entry` fondo de verde lima también cuando tiene el foco de entrada:

[![VSM en estilo](vsm-images/VsmInStyle.png "VSM en estilo")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>Estados visuales enXamarin.Forms

En la tabla siguiente se enumeran los Estados visuales que se definen en Xamarin.Forms :

| Clase | States | Más información |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [Estados visuales del botón](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CheckBox` | `IsChecked` | [Estados visuales de la casilla](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [Estados visuales de CarouselView](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [Estados visuales de ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `RadioButton` | `IsChecked` | [Estados visuales de RadioButton](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) |
| `Switch` | `On`, `Off` | [Cambiar Estados visuales](~/xamarin-forms/user-interface/switch.md#switch-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [Estados comunes](#common-states) |

Se puede tener acceso a cada uno de estos Estados a través del grupo de Estados visuales denominado `CommonStates` .

Además, `CollectionView` implementa el `Selected` Estado. Para obtener más información, vea cambiar el color de los [elementos seleccionados](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Establecer el estado en varios elementos

En los ejemplos anteriores, los Estados visuales se adjuntaron y funcionaban en elementos únicos. Sin embargo, también es posible crear Estados visuales que estén asociados a un único elemento, pero que establezcan propiedades en otros elementos dentro del mismo ámbito. Esto evita tener que repetir Estados visuales en cada elemento en el que operan los Estados.

El [`Setter`](xref:Xamarin.Forms.Setter) tipo tiene una `TargetName` propiedad, de tipo `string` , que representa el elemento de destino que `Setter` manipulará para un estado visual. Cuando `TargetName` se define la propiedad, `Setter` establece el `Property` del elemento definido en `TargetName` a `Value` :

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

En este ejemplo, un `Label` denominado tendrá `label` su `TextColor` propiedad establecida en `Red` . Al establecer la `TargetName` propiedad, debe especificar la ruta de acceso completa a la propiedad en `Property` . Por consiguiente, para establecer la `TextColor` propiedad en `Label` , `Property` se especifica como `Label.TextColor` .

> [!NOTE]
> Cualquier propiedad a la que hace referencia un `Setter` objeto debe estar respaldada por una propiedad enlazable.

En la página **VSM with Setter TargetName** en el ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** se muestra cómo establecer el estado en varios elementos, a partir de un único grupo de Estados visuales. El archivo XAML se compone de un `StackLayout` que contiene un `Label` elemento, un `Entry` y un `Button` :

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

El marcado de VSM está asociado a `StackLayout` . Hay dos Estados mutuamente excluyentes, denominados "normal" y "pressed", donde cada estado contiene `VisualState` etiquetas.

El estado "normal" está activo cuando `Button` no se presiona y se puede escribir una respuesta a la pregunta:

[![VSM Setter TargetName: estado normal](vsm-images/VsmSetterTargetNameNormal.png "VSM Setter TargetName-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

El estado "pressed" se activa cuando `Button` se presiona:

[![VSM Setter TargetName: estado pressed](vsm-images/VsmSetterTargetNamePressed.png "VSM Setter TargetName-pressed")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

"Pressed" `VisualState` especifica que cuando `Button` se presiona, su `Scale` propiedad se cambiará del valor predeterminado de 1 a 0,8. Además, el `Entry` nombre tendrá `entry` su `Text` propiedad establecida en París. Por lo tanto, el resultado es que, cuando `Button` se presiona, se cambia la escala para que sea ligeramente menor y `Entry` muestra París. A continuación, cuando `Button` se libera, se vuelve a escalar a su valor predeterminado de 1 y `Entry` muestra cualquier texto escrito anteriormente.

> [!IMPORTANT]
> Actualmente no se admiten las rutas de acceso de propiedades en `Setter` los elementos que especifican la `TargetName` propiedad.

## <a name="define-your-own-visual-states"></a>Definir sus propios Estados visuales

Cada clase que deriva de `VisualElement` admite los Estados comunes "normal", "Focused" y "Disabled". Además, la `CollectionView` clase admite el estado "seleccionado". Internamente, la [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) clase detecta cuándo está habilitada o deshabilitada, o enfocada o no enfocada, y llama al método estático [ `VisualStateManager.GoToState` ] (XREF: Xamarin.Forms . VisualStateManager. GoToState ( Xamarin.Forms . Método VisualElement, System. String)):

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Este es el único código de Visual State Manager que encontrará en la `VisualElement` clase. Dado que `GoToState` se llama a para cada objeto basado en cada clase que se deriva de `VisualElement` , puede usar el administrador de estado visual con cualquier `VisualElement` objeto para responder a estos cambios.

Curiosamente, no se hace referencia explícitamente al nombre del grupo de Estados visuales "CommonStates" en `VisualElement` . El nombre de grupo no forma parte de la API para el administrador de estado visual. En uno de los dos programas de ejemplo mostrados hasta el momento, puede cambiar el nombre del grupo de "CommonStates" a cualquier otro y el programa seguirá funcionando. El nombre de grupo es simplemente una descripción general de los Estados de ese grupo. Se entiende implícitamente que los Estados visuales de cualquier grupo se excluyen mutuamente: un estado y solo un estado es actual en cualquier momento.

Si desea implementar sus propios Estados visuales, deberá llamar a `VisualStateManager.GoToState` desde el código. Lo más frecuente es que realice esta llamada desde el archivo de código subyacente de la clase de página.

En la página de **validación de VSM** en el ejemplo **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** se muestra cómo usar el administrador de estado visual en conexión con la validación de entrada. El archivo XAML está compuesto de un `StackLayout` que contiene dos `Label` elementos, un `Entry` y un `Button` :

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

El marcado de VSM se adjunta a `StackLayout` (con nombre `stackLayout` ). Hay dos Estados mutuamente excluyentes, denominados "Valid" y "Invalid", donde cada estado contiene `VisualState` etiquetas.

Si no `Entry` contiene un número de teléfono válido, el estado actual es "no válido", por lo que `Entry` tiene un fondo rosa, el segundo `Label` es visible y el `Button` está deshabilitado:

[![Validación de VSM: Estado no válido](vsm-images/VsmValidationInvalid.png "Validación de VSM: no válida")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Cuando se escribe un número de teléfono válido, el estado actual se convierte en "válido". El `Entry` obtiene un fondo de Lima, el segundo `Label` desaparece y `Button` ahora está habilitado:

[![Validación de VSM: estado válido](vsm-images/VsmValidationValid.png "Validación de VSM: válida")](vsm-images/VsmValidationValid-Large.png#lightbox)

El archivo de código subyacente es responsable de controlar el `TextChanged` evento desde `Entry` . El controlador utiliza una expresión regular para determinar si la cadena de entrada es válida o no. El método en el archivo de código subyacente denominado `GoToState` llama al `VisualStateManager.GoToState` método estático para `stackLayout` :

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

Observe también que `GoToState` se llama al método desde el constructor para inicializar el estado. Siempre debe haber un estado actual. Pero en el código no hay ninguna referencia al nombre del grupo de Estados visuales, aunque se hace referencia a él en el XAML como "ValidationStates" para fines de claridad.

Observe que el archivo de código subyacente solo debe tener en cuenta el objeto en la página que define los Estados visuales y para llamar a `VisualStateManager.GoToState` para este objeto. Esto se debe a que ambos Estados visuales tienen como destino varios objetos en la página.

Es posible que se pregunte: Si el archivo de código subyacente debe hacer referencia al objeto en la página que define los Estados visuales, ¿por qué no se puede acceder al archivo de código subyacente simplemente a este y a otros objetos directamente? Seguramente podría. Sin embargo, la ventaja de utilizar el VSM es que puede controlar el modo en que los elementos visuales reaccionan a un estado diferente completamente en XAML, lo que mantiene todo el diseño de la interfaz de usuario en una ubicación. Esto evita establecer la apariencia visual al obtener acceso a los elementos visuales directamente desde el código subyacente.

## <a name="visual-state-triggers"></a>Desencadenadores de estado visual

Los Estados visuales admiten desencadenadores de estado, que son un grupo especializado de desencadenadores que definen las condiciones en las que [`VisualState`](xref:Xamarin.Forms.VisualState) se debe aplicar.

Los desencadenadores de estado se agregan a la colección [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) de una clase [`VisualState`](xref:Xamarin.Forms.VisualState). Esta colección puede contener un único desencadenador de estado o varios desencadenadores de estado. Se aplicará una clase [`VisualState`](xref:Xamarin.Forms.VisualState) cuando cualquier desencadenador de estado de la colección esté activo.

Al usar desencadenadores de estado para controlar los estados visuales, Xamarin.Forms usa las siguientes reglas de prioridad para determinar qué desencadenador, y la clase [`VisualState`](xref:Xamarin.Forms.VisualState) correspondiente, se activará:

1. Cualquier desencadenador derivado de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Una clase [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activada debido a que se cumple la condición [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth).
1. Una clase [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activada debido a que se cumple la condición [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight).

Si hay varios desencadenadores activos simultáneamente (por ejemplo, dos desencadenadores personalizados), tiene prioridad el primer desencadenador declarado en el marcado.

Para obtener más información sobre desencadenadores de estado, vea [Desencadenadores de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Usar el administrador de estado visual para el diseño adaptable

Xamarin.FormsNormalmente, una aplicación que se ejecuta en un teléfono puede verse en una relación de aspecto vertical u horizontal, y Xamarin.Forms se puede cambiar el tamaño de un programa que se ejecuta en el escritorio para que asuma muchos tamaños diferentes y relaciones de aspecto. Una aplicación bien diseñada podría mostrar su contenido de forma diferente para estos diversos factores de página o de formulario de ventana.

A veces, esta técnica se conoce como _diseño adaptable_. Dado que el diseño adaptable solo implica los objetos visuales de un programa, es una aplicación ideal de Visual State Manager.

Un ejemplo sencillo es una aplicación que muestra una pequeña colección de botones que afectan al contenido de la aplicación. En el modo vertical, estos botones se pueden mostrar en una fila horizontal en la parte superior de la página:

[![Diseño adaptable de VSM: vertical](vsm-images/VsmAdaptiveLayoutPortrait.png "Diseño adaptable de VSM: vertical")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En el modo horizontal, la matriz de botones puede moverse a un lado y mostrarse en una columna:

[![Diseño adaptable de VSM: horizontal](vsm-images/VsmAdaptiveLayoutLandscape.png "Diseño adaptable de VSM: horizontal")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Desde la parte superior a la inferior, el programa se ejecuta en el Plataforma universal de Windows, Android e iOS.

La página de **diseño adaptable de VSM** en el ejemplo [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) define un grupo denominado "OrientationStates" con dos Estados visuales denominados "vertical" y "horizontal". (Un enfoque más complejo podría basarse en varios anchos de página o de ventana diferentes).

El marcado de VSM se produce en cuatro lugares en el archivo XAML. El `StackLayout` denominado `mainStack` contiene el menú y el contenido, que es un `Image` elemento. `StackLayout`Debe tener una orientación vertical en modo vertical y una orientación horizontal en modo horizontal:

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

El `ScrollView` denominado interno `menuScroll` y el `StackLayout` denominado `menuStack` implementan el menú de botones. La orientación de estos diseños es opuesta a `mainStack` . El menú debe ser horizontal en modo vertical y vertical en modo horizontal.

La cuarta sección del marcado de VSM está en un estilo implícito para los propios botones. Este marcado establece `VerticalOptions` `HorizontalOptions` `Margin` las propiedades, y específicas de las orientaciones vertical y horizontal.

El archivo de código subyacente establece la `BindingContext` propiedad de `menuStack` para implementar `Button` comandos y también adjunta un controlador al `SizeChanged` evento de la página:

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

El `SizeChanged` controlador llama a `VisualStateManager.GoToState` para los `StackLayout` dos `ScrollView` elementos y y, a continuación, recorre en bucle los elementos secundarios de `menuStack` para llamar a `VisualStateManager.GoToState` para los `Button` elementos.

Puede parecer que si el archivo de código subyacente puede controlar los cambios de orientación más directamente estableciendo las propiedades de los elementos en el archivo XAML, pero el administrador de estado visual es definitivamente un enfoque más estructurado. Todos los objetos visuales se guardan en el archivo XAML, donde resultan más fáciles de examinar, mantener y modificar.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Visual State Manager con Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Forms3,0 vídeo de Visual State Manager**

## <a name="related-links"></a>Vínculos relacionados

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Desencadenadores de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)
