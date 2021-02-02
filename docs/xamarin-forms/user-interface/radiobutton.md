---
title: Xamarin.Forms Índices
description: Xamarin.FormsRadioButton es un tipo de botón que permite a los usuarios seleccionar una opción de un conjunto. Cada opción está representada por un botón de radio y solo puede seleccionar un botón de radio de un grupo.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1f36329c36dfae3f2ca0754ecbb867520d4c530
ms.sourcegitcommit: bd8ce0ae64698246db92a6c4396983290dc54a22
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427751"
---
# <a name="xamarinforms-radiobutton"></a>Xamarin.Forms Índices

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin.Forms [`RadioButton`](xref:Xamarin.Forms.RadioButton) Es un tipo de botón que permite a los usuarios seleccionar una opción de un conjunto. Cada opción está representada por un botón de radio y solo puede seleccionar un botón de radio de un grupo. De forma predeterminada, cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) muestra texto:

![Captura de pantalla de los RadioButtons predeterminados](radiobutton-images/radiobuttons-default.png "Botones de radiovalores predeterminados")

Sin embargo, en algunas plataformas [`RadioButton`](xref:Xamarin.Forms.RadioButton) puede mostrar [`View`](xref:Xamarin.Forms.View) y en todas las plataformas la apariencia de cada una de ellas `RadioButton` se puede volver a definir con un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) :

![Captura de pantalla de RadioButtons redefinidos](radiobutton-images/radiobuttons-controltemplate.png "Botones de radiose redefinidos")

El [`RadioButton`](xref:Xamarin.Forms.RadioButton) control define las siguientes propiedades:

- `Content`, de tipo `object` , que define `string` o [`View`](xref:Xamarin.Forms.View) que va a mostrar `RadioButton` .
- `IsChecked`, de tipo `bool` , que define si `RadioButton` está activado. Esta propiedad usa un `TwoWay` enlace y tiene un valor predeterminado de `false` .
- `GroupName`, de tipo `string` , que define el nombre que especifica qué `RadioButton` controles se excluyen mutuamente. Esta propiedad tiene un valor predeterminado de `null` .
- `Value`, de tipo `object` , que define un valor único opcional asociado a `RadioButton` .
- `BorderColor`, de tipo [`Color`](xref:Xamarin.Forms.Color) , que define el color del trazo del borde.
- `BorderWidth`, de tipo `double` , que define el ancho del `RadioButton` borde.
- `CharacterSpacing`, de tipo `double` , que define el espaciado entre los caracteres de cualquier texto mostrado.
- `CornerRadius`, de tipo `int` , que define el radio de redondeo de `RadioButton` .
- `FontAttributes`, de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que determina el estilo del texto.
- `FontFamily`, de tipo `string` , que define la familia de fuentes.
- `FontSize`, de tipo `double` , que define el tamaño de fuente.
- `TextColor`, de tipo [`Color`](xref:Xamarin.Forms.Color) , que define el color de cualquier texto mostrado.
- `TextTransform`, de tipo `TextTransform` , que define las mayúsculas y minúsculas de cualquier texto mostrado.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

El [`RadioButton`](xref:Xamarin.Forms.RadioButton) control también define un `CheckedChanged` evento que se desencadena cuando `IsChecked` cambia la propiedad, ya sea a través de la manipulación de usuario o mediante programación. El `CheckedChangedEventArgs` objeto que acompaña al `CheckedChanged` evento tiene una propiedad única denominada `Value` , de tipo `bool` . Cuando se desencadena el evento, el valor de la `CheckedChangedEventArgs.Value` propiedad se establece en el nuevo valor de la `IsChecked` propiedad.

[`RadioButton`](xref:Xamarin.Forms.RadioButton) la `RadioButtonGroup` clase, que define las siguientes propiedades adjuntas, puede administrar la agrupación:

-   `GroupName`, de tipo `string` , que define el nombre de grupo para los `RadioButton` objetos de un objeto `Layout<View>` .
- `SelectedValue`, de tipo `object` , que representa el valor del objeto activado `RadioButton` dentro de un `Layout<View>` grupo. Esta propiedad adjunta usa un `TwoWay` enlace de forma predeterminada.

Para obtener más información acerca de la `GroupName` propiedad adjunta, consulte [grupos RadioButtons](#group-radiobuttons). Para obtener más información sobre la `SelectedValue` propiedad adjunta, vea [responder a los cambios de estado de RadioButton](#respond-to-radiobutton-state-changes).

## <a name="create-radiobuttons"></a>Crear RadioButtons

La apariencia de una [`RadioButton`](xref:Xamarin.Forms.RadioButton) se define mediante el tipo de datos asignado a la `RadioButton.Content` propiedad:

- Cuando `RadioButton.Content` se asigna a la propiedad un `string` , se mostrará en cada plataforma, alineada horizontalmente junto al círculo del botón de radio.
- Cuando `RadioButton.Content` se asigna un [`View`](xref:Xamarin.Forms.View) , se mostrará en las plataformas admitidas (iOS, UWP), mientras que las plataformas no compatibles se reservarán en una representación de cadena del `View` objeto (Android). En ambos casos, el contenido se muestra alineado horizontalmente junto al círculo del botón de radio.
- Cuando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se aplica un a un [`RadioButton`](xref:Xamarin.Forms.RadioButton) , [`View`](xref:Xamarin.Forms.View) se puede asignar a la `RadioButton.Content` propiedad en todas las plataformas. Para obtener más información, vea [redefinir el aspecto de RadioButton](#redefine-radiobutton-appearance).

### <a name="display-string-based-content"></a>Mostrar contenido basado en cadena

Un [`RadioButton`](xref:Xamarin.Forms.RadioButton) muestra texto cuando `Content` se asigna a la propiedad un `string` :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat" />
    <RadioButton Content="Dog" />
    <RadioButton Content="Elephant" />
    <RadioButton Content="Monkey"
                 IsChecked="true" />
</StackLayout>
```

En este ejemplo, [`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos se agrupan implícitamente dentro del mismo contenedor primario. Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de RadioButtons basados en texto](radiobutton-images/radiobuttons-text.png "RadioButtons basados en texto")

### <a name="display-arbitrary-content"></a>Mostrar contenido arbitrario

En iOS y UWP, un [`RadioButton`](xref:Xamarin.Forms.RadioButton) puede mostrar contenido arbitrario cuando `Content` se asigna a la propiedad un [`View`](xref:Xamarin.Forms.View) :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton>
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

En este ejemplo, [`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos se agrupan implícitamente dentro del mismo contenedor primario. Este código XAML da como resultado la apariencia que se muestra en las siguientes capturas de pantallas:

![Captura de pantalla de RadioButtons basados en vista](radiobutton-images/radiobuttons-view.png "RadioButtons basados en la vista")

En Android, [`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos mostrarán una representación basada en cadenas del [`View`](xref:Xamarin.Forms.View) objeto que se ha establecido como contenido:

![Captura de pantalla del RadioButton basado en vistas en Android](radiobutton-images/radiobutton-view-android.png "RadioButton basado en vistas en Android")

> [!NOTE]
> Cuando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se aplica un a un [`RadioButton`](xref:Xamarin.Forms.RadioButton) , [`View`](xref:Xamarin.Forms.View) se puede asignar a la `RadioButton.Content` propiedad en todas las plataformas. Para obtener más información, vea [redefinir el aspecto de RadioButton](#redefine-radiobutton-appearance).

## <a name="associate-values-with-radiobuttons"></a>Asociar valores con RadioButtons

Cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto tiene una `Value` propiedad, de tipo `object` , que define un valor único opcional que se va a asociar al botón de radio. Esto permite que el valor de un `RadioButton` sea diferente al contenido, y es especialmente útil cuando los `RadioButton` objetos muestran [`View`](xref:Xamarin.Forms.View) objetos.

El siguiente código XAML muestra el establecimiento de las `Content` `Value` propiedades y en cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto:

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Value="Cat">
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Dog">
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Elephant">
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Monkey">
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

En este ejemplo, cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) tiene un [`Image`](xref:Xamarin.Forms.Image) como su contenido, a la vez que define un valor basado en cadena. Esto permite identificar fácilmente el valor del botón de radio activado.

## <a name="group-radiobuttons"></a>RadioButtons de grupo

Los botones de radio funcionan en grupos y hay tres enfoques para agrupar los botones de radio:

- Colóquelos en el mismo contenedor primario. Esto se conoce como agrupación *implícita* .
- Establezca la `GroupName` propiedad de cada botón de radio del grupo en el mismo valor. Esto se conoce como agrupación *explícita* .
- Establezca la `RadioButtonGroup.GroupName` propiedad adjunta en un contenedor primario que, a su vez, establece la `GroupName` propiedad de los [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos del contenedor. Esto también se conoce como agrupación *explícita* .

> [!IMPORTANT]
> [`RadioButton`](xref:Xamarin.Forms.RadioButton) no es necesario que los objetos pertenezcan al mismo elemento primario que se va a agrupar. Se excluyen mutuamente, siempre y cuando compartan un nombre de grupo.

### <a name="explicit-grouping-with-the-groupname-property"></a>Agrupación explícita con la propiedad GroupName

En el siguiente ejemplo de XAML se muestra cómo agrupar objetos explícitamente estableciendo [`RadioButton`](xref:Xamarin.Forms.RadioButton) sus `GroupName` propiedades:

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Content="Red"
             GroupName="colors" />
<RadioButton Content="Green"
             GroupName="colors" />
<RadioButton Content="Blue"
             GroupName="colors" />
<RadioButton Content="Other"
             GroupName="colors" />
```

En este ejemplo, cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) es mutuamente excluyente porque comparte el mismo `GroupName` valor.

### <a name="explicit-grouping-with-the-radiobuttongroupgroupname-attached-property"></a>Agrupación explícita con la propiedad adjunta RadioButtonGroup. GroupName

La `RadioButtonGroup` clase define una `GroupName` propiedad adjunta, de tipo `string` , que se puede establecer en un `Layout<View>` objeto. Esto permite convertir cualquier diseño en un grupo de botones de radio:

```xaml
<StackLayout RadioButtonGroup.GroupName="colors">
    <Label Text="What's your favorite color?" />
    <RadioButton Content="Red" />
    <RadioButton Content="Green" />
    <RadioButton Content="Blue" />
    <RadioButton Content="Other" />
</StackLayout>
```

En este ejemplo, cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) de tendrá [`StackLayout`](xref:Xamarin.Forms.StackLayout) su `GroupName` propiedad establecida en `fruits` y será mutuamente excluyente.

> [!NOTE]
> Cuando un `Layout<View` objeto> que establece la `RadioButtonGroup.GroupName` propiedad adjunta contiene un [`RadioButton`](xref:Xamarin.Forms.RadioButton) que establece su `GroupName` propiedad, el valor de la `RadioButton.GroupName` propiedad tendrá prioridad.

## <a name="respond-to-radiobutton-state-changes"></a>Responder a los cambios de estado de RadioButton

Un botón de radio tiene dos estados: seleccionado o sin seleccionar. Cuando se activa un botón de radio, su `IsChecked` propiedad es `true` . Cuando se desactiva un botón de radio, su `IsChecked` propiedad es `false` . Para borrar un botón de radio, puntee en otro botón de radio del mismo grupo, pero no se puede borrar; para ello, vuelva a puntear en él. Sin embargo, para desactivar un botón de radio mediante programación puede establecer su propiedad `IsChecked` en `false`.

### <a name="respond-to-an-event-firing"></a>Responder a una activación de eventos

Cuando la `IsChecked` propiedad cambia, ya sea a través de la manipulación de usuario o mediante programación, se `CheckedChanged` desencadena el evento. Se puede registrar un controlador de eventos para este evento para responder al cambio:

```xaml
<RadioButton Content="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

El código subyacente contiene el controlador del `CheckedChanged` evento:

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

El `sender` argumento es el [`RadioButton`](xref:Xamarin.Forms.RadioButton) responsable de este evento. Puede utilizar esto para tener acceso al [`RadioButton`](xref:Xamarin.Forms.RadioButton) objeto o para distinguir entre varios `RadioButton` objetos que comparten el mismo `CheckedChanged` controlador de eventos.

### <a name="respond-to-a-property-change"></a>Responder a un cambio de propiedad

La `RadioButtonGroup` clase define una `SelectedValue` propiedad adjunta, de tipo `object` , que se puede establecer en un `Layout<View>` objeto. Esta propiedad adjunta representa el valor de la comprobación [`RadioButton`](xref:Xamarin.Forms.RadioButton) dentro de un grupo definido en un diseño.

Cuando la `IsChecked` propiedad cambia, ya sea a través de la manipulación de usuario o mediante programación, `RadioButtonGroup.SelectedValue` también cambia la propiedad adjunta. Por lo tanto, la `RadioButtonGroup.SelectedValue` propiedad adjunta puede estar enlazada a datos de una propiedad que almacena la selección del usuario:

```xaml
<StackLayout RadioButtonGroup.GroupName="{Binding GroupName}"
             RadioButtonGroup.SelectedValue="{Binding Selection}">
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat"
                 Value="Cat" />
    <RadioButton Content="Dog"
                 Value="Dog" />
    <RadioButton Content="Elephant"
                 Value="Elephant" />
    <RadioButton Content="Monkey"
                 Value="Monkey"/>
    <Label x:Name="animalLabel">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="You have chosen:" />
                <Span Text="{Binding Selection}" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
</StackLayout>
```

En este ejemplo, el valor de la `RadioButtonGroup.GroupName` propiedad adjunta se establece mediante la `GroupName` propiedad en el contexto de enlace. Del mismo modo, el valor de la `RadioButtonGroup.SelectedValue` propiedad adjunta se establece mediante la `Selection` propiedad en el contexto de enlace. Además, la `Selection` propiedad se actualiza a la `Value` propiedad de la comprobación [`RadioButton`](xref:Xamarin.Forms.RadioButton) .

## <a name="radiobutton-visual-states"></a>Estados visuales de RadioButton

[`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos `Checked` tienen `Unchecked` Estados visuales y que se pueden usar para iniciar un cambio visual cuando `RadioButton` se activa o desactiva una.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para los `Checked` `Unchecked` Estados y:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CheckedStates">
                        <VisualState x:Name="Checked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Unchecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Content="Car" />
        <RadioButton Content="Bike" />
        <RadioButton Content="Train" />
        <RadioButton Content="Walking" />
    </StackLayout>
</ContentPage>
```

En este ejemplo, la clase [`Style`](xref:Xamarin.Forms.Style) implícita se destina a objetos [`RadioButton`](xref:Xamarin.Forms.RadioButton). `Checked` [`VisualState`](xref:Xamarin.Forms.VisualState) Especifica que cuando `RadioButton` se comprueba una, su `TextColor` propiedad se establecerá en verde con un `Opacity` valor de 1. `Unchecked` `VisualState` Especifica que cuando un `RadioButton` está en un estado desactivado, su `TextColor` propiedad se establecerá en rojo con un `Opacity` valor de 0,5. Por lo tanto, el efecto general es que cuando una `RadioButton` está desactivada, es roja y parcialmente transparente, y es verde sin transparencia cuando está activada:

![Captura de pantalla de Estados visuales de RadioButton](radiobutton-images/radiobuttons-visualstates.png "Estados visuales de RadioButton")

Para obtener más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="redefine-radiobutton-appearance"></a>Redefinición de la apariencia de RadioButton

De forma predeterminada, [`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos usan representadores de plataforma para usar controles nativos en plataformas admitidas. Sin embargo, `RadioButton` la estructura visual se puede redefinir con un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , para que `RadioButton` los objetos tengan una apariencia idéntica en todas las plataformas. Esto es posible porque la [`RadioButton`](xref:Xamarin.Forms.RadioButton) clase hereda de la [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) clase.

En el código XAML siguiente [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) se muestra un objeto que se puede utilizar para volver a definir la estructura visual de los [`RadioButton`](xref:Xamarin.Forms.RadioButton) objetos:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="RadioButtonTemplate">
            <Frame BorderColor="#F3F2F1"
                   BackgroundColor="#F3F2F1"
                   HasShadow="False"
                   HeightRequest="100"
                   WidthRequest="100"
                   HorizontalOptions="Start"
                   VerticalOptions="Start"
                   Padding="0">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CheckedStates">
                            <VisualState x:Name="Checked">
                                <VisualState.Setters>
                                    <Setter Property="BorderColor"
                                            Value="#FF3300" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="1" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Unchecked">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="#F3F2F1" />
                                    <Setter Property="BorderColor"
                                            Value="#F3F2F1" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="0" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </VisualStateManager.VisualStateGroups>
                <Grid Margin="4"
                      WidthRequest="100">
                    <Grid WidthRequest="18"
                          HeightRequest="18"
                          HorizontalOptions="End"
                          VerticalOptions="Start">
                        <Ellipse Stroke="Blue"
                                 Fill="White"
                                 WidthRequest="16"
                                 HeightRequest="16"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                        <Ellipse x:Name="check"
                                 Fill="Blue"
                                 WidthRequest="8"
                                 HeightRequest="8"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                    </Grid>
                    <ContentPresenter />
                </Grid>
            </Frame>
        </ControlTemplate>

        <Style TargetType="RadioButton">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource RadioButtonTemplate}" />
        </Style>
    </ContentPage.Resources>
    <!-- Page content -->
</ContentPage>
```

En este ejemplo, el elemento raíz de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) es un [`Frame`](xref:Xamarin.Forms.Frame) objeto que define `Checked` `Unchecked` los Estados visuales y. El `Frame` objeto utiliza una combinación de [`Grid`](xref:Xamarin.Forms.Grid) [`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse) objetos, y [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) para definir la estructura visual de un objeto [`RadioButton`](xref:Xamarin.Forms.RadioButton) . En el ejemplo también se incluye un estilo *implícito* que asignará `RadioButtonTemplate` a la `ControlTemplate` propiedad de cualquier `RadioButton` objeto de la página.

> [!NOTE]
> El [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) objeto marca la ubicación en la estructura visual donde se [`RadioButton`](xref:Xamarin.Forms.RadioButton) mostrará el contenido.

En el siguiente código XAML se muestran [`RadioButton`](xref:Xamarin.Forms.RadioButton) los objetos que consumen [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a través del estilo *implícito* :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <StackLayout RadioButtonGroup.GroupName="animals"
                 Orientation="Horizontal">
        <RadioButton Value="Cat">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="cat.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Cat"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Dog">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="dog.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Dog"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Elephant">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="elephant.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Elephant"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Monkey">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="monkey.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Monkey"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
    </StackLayout>
</StackLayout>
```

En este ejemplo, la estructura visual definida para cada [`RadioButton`](xref:Xamarin.Forms.RadioButton) se reemplaza por la estructura visual definida en [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , y, en tiempo de ejecución, los objetos de `ControlTemplate` se convierten en parte del árbol visual para cada uno `RadioButton` . Además, el contenido de cada `RadioButton` se sustituye por el [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) definido en la plantilla de control. Esto da como resultado la `RadioButton` apariencia siguiente:

![Captura de pantalla de RadioButtons con plantilla](radiobutton-images/radiobuttons-templated.png "RadioButtons con plantilla")

Para obtener más información acerca de las plantillas de control, consulte [ Xamarin.Forms plantillas de control](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="disable-a-radiobutton"></a>Deshabilitar un RadioButton

A veces, una aplicación entra en un estado en el que un `RadioButton` que se está comprobando no es una operación válida. En tales casos, `RadioButton` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false` .

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de RadioButton (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
