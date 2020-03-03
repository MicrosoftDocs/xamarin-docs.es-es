---
title: Orientación del dispositivo
description: Este artículo se explica cómo las aplicaciones de Xamarin.Forms de diseño que tienen un aspecto excelente en orientación vertical y horizontal.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: f7526b6cecebadd30e95718b7e537026a6557adf
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2020
ms.locfileid: "78214999"
---
# <a name="device-orientation"></a>Orientación del dispositivo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

Es importante tener en cuenta cómo se utilizará la aplicación y cómo se puede incorporar la orientación horizontal para mejorar la experiencia del usuario. Diseños individuales pueden diseñarse para dar cabida a varias orientaciones y mejor usa el espacio disponible. En el nivel de aplicación, rotación puede habilitar o deshabilitar.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controlar la orientación

Cuando se usa Xamarin.Forms, el método admitido para controlar la orientación del dispositivo es usar la configuración para cada proyecto individual.

### <a name="ios"></a>iOS

En iOS, la orientación del dispositivo se configura para las aplicaciones que usan el archivo **info. plist** . Este archivo incluirá la configuración de orientación para iPhone y iPod, así como la configuración para iPad si la aplicación incluye como un destino. Las siguientes son instrucciones específicas para su IDE. Use las opciones del IDE en la parte superior de este documento para seleccionar qué instrucciones que gustaría ver:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Visual Studio, abra el proyecto de iOS y Abra **info. plist**. El archivo se abrirá en un panel de configuración, a partir de la pestaña de información de implementación de iPhone:

![iPhone información de implementación en Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Para configurar la orientación de iPad, seleccione la pestaña **información de implementación de iPad** en la parte superior izquierda del panel y, a continuación, seleccione una de las orientaciones disponibles:

![Orientaciones de dispositivos compatibles en Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, abra el proyecto de iOS y Abra **info. plist**. En la pestaña **aplicación** , las secciones estarán disponibles para establecer la orientación:

![iPhone información de implementación en Visual Studio para Mac](device-orientation-images/orientation-xam-ui.png)

Si prefiere editar los valores mediante una interfaz de editor de valores clave, seleccione la pestaña **origen**> en la parte inferior de la pantalla:

![Admite las orientaciones de dispositivos en Visual Studio para Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Para controlar la orientación en Android, Abra **MainActivity.CS** y establezca la orientación mediante el atributo que decora la clase `MainActivity`:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android admite varias opciones para especificar la orientación:

- **Horizontal** &ndash; fuerza que la orientación de la aplicación sea horizontal, independientemente de los datos del sensor.
- El &ndash; **vertical** obliga a la orientación de la aplicación a ser vertical, independientemente de los datos del sensor.
- **User** &ndash; hace que la aplicación se presente con la orientación preferida del usuario.
- **Detrás** de &ndash; hace que la orientación de la aplicación sea la misma que la orientación de la [actividad](xref:Android.App.Activity) subyacente.
- **Sensor** &ndash; hace que el sensor determine la orientación de la aplicación, incluso si el usuario ha deshabilitado la rotación automática.
- **SensorLandscape** &ndash; hace que la aplicación use la orientación horizontal mientras usa datos de sensor para cambiar la dirección de cara de la pantalla (de modo que la pantalla no se vea como boca abajo).
- **SensorPortrait** &ndash; hace que la aplicación use la orientación vertical al usar los datos del sensor para cambiar la dirección en la que está orientada la pantalla (de modo que la pantalla no se vea en sentido descendente).
- **ReverseLandscape** &ndash; hace que la aplicación utilice la orientación horizontal, orientada a la dirección opuesta de habitual, de modo que aparezca "al revés".
- **ReversePortrait** &ndash; hace que la aplicación use la orientación vertical, orientada a la dirección opuesta de habitual, de modo que aparezca "al revés".
- **FullSensor** &ndash; hace que la aplicación se base en datos de sensor para seleccionar la orientación correcta (fuera del posible 4).
- **FullUser** &ndash; hace que la aplicación use las preferencias de orientación del usuario. Si está habilitada la rotación automática, pueden usar todas las 4 orientaciones.
- **UserLandscape** &ndash; _\[no se admite\]_ hace que la aplicación utilice la orientación horizontal, a menos que el usuario tenga habilitada la rotación automática, en cuyo caso usará el sensor para determinar la orientación. Esta opción interrumpirá la compilación.
- **UserPortrait** &ndash; _\[no se admite\]_ hace que la aplicación use la orientación vertical, a menos que el usuario tenga habilitada la rotación automática, en cuyo caso usará el sensor para determinar la orientación. Esta opción interrumpirá la compilación.
- &ndash; **bloqueada** _\[no se admite\]_ hace que la aplicación use la orientación de la pantalla, sea cual sea el inicio, sin tener que responder a los cambios en la orientación física del dispositivo. Esta opción interrumpirá la compilación.

Tenga en cuenta que las API nativas de Android proporcionan un gran control sobre cómo se administra la orientación, incluidas las opciones que contradicen explícitamente al usuario expresan las preferencias.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En el Plataforma universal de Windows (UWP), las orientaciones admitidas se establecen en el archivo **Package. appxmanifest** . Abriendo el manifiesto se mostrará un panel de configuración donde se pueden seleccionar orientaciones admitidas.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reaccionar a los cambios de orientación

Xamarin.Forms no ofrece los eventos nativos para notificar a la aplicación de los cambios de orientación en código compartido. Sin embargo,[Xamarin. Essentials](~/essentials/index.md) contiene una clase [`DeviceDisplay`] que proporciona notificaciones de cambios de orientación.

Para detectar las orientaciones sin Xamarin. Essentials, supervise el `SizeChanged` evento del `Page`, que se activa cuando cambia el ancho o el alto de la `Page`. Cuando el ancho del `Page` es mayor que el alto, el dispositivo está en modo horizontal. Para obtener más información, vea [Mostrar una imagen en función de la orientación de la pantalla](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

Como alternativa, es posible invalidar el método de [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) en un `Page`, insertando allí cualquier lógica de cambio de diseño. Se llama al método `OnSizeAllocated` cada vez que se asigna un nuevo tamaño a un `Page`, lo que sucede siempre que se gira el dispositivo. Tenga en cuenta que la implementación base de `OnSizeAllocated` realiza funciones de diseño importantes, por lo que es importante llamar a la implementación base en la invalidación:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Error al dar ese paso dará como resultado una página que no funciona.

Tenga en cuenta que se puede llamar al método `OnSizeAllocated` muchas veces cuando se gira un dispositivo. Cambiar el diseño cada vez es un desperdicio de recursos y puede provocar parpadeos. Considere el uso de una variable de instancia dentro de la página para seguir si es la orientación en horizontal o vertical y solo vuelve a dibujar cuando se produce un cambio:

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Una vez que se ha detectado un cambio en la orientación del dispositivo, es posible que desee agregar o quitar vistas adicionales desde la interfaz de usuario para reaccionar ante el cambio en el espacio disponible. Por ejemplo, considere la calculadora integrada en cada plataforma en vertical:

![](device-orientation-images/calculator-portrait.png "Calculator Application in Portrait")

y horizontal:

![](device-orientation-images/calculator-landscape.png "Calculator Application in Landscape")

Tenga en cuenta que las aplicaciones aprovechar el espacio disponible mediante la adición de más funcionalidad en horizontal.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Diseño dinámico

Es posible a las interfaces de diseño mediante los diseños integrados para que hagan el cambio correctamente cuando se gira el dispositivo. Al diseñar las interfaces que seguirán siendo atractivo al responder a los cambios de orientación, tenga en cuenta las siguientes reglas generales:

- **Preste atención a las proporciones** &ndash; los cambios de orientación pueden ocasionar problemas cuando se realizan determinadas suposiciones con respecto a las proporciones. Por ejemplo, una vista que tendría una gran cantidad de espacio en 1/3 del espacio vertical de una pantalla en posición vertical no cabrá en 1/3 del espacio vertical en horizontal.
- **Tenga cuidado con los valores absolutos** &ndash; los valores absolutos (píxel) que tengan sentido en vertical pueden no tener sentido en el panorama. Cuando se necesitan valores absolutos, utilice los diseños anidados para aislar su impacto. Por ejemplo, sería razonable usar valores absolutos en un `TableView` `ItemTemplate` cuando la plantilla de elemento tiene un alto uniforme garantizado.

Las reglas anteriores también se aplican al implementar las interfaces para varios tamaños de pantalla y son generalmente considera mejores prácticas. El resto de esta guía explica ejemplos específicos de diseños con capacidad de respuesta con cada uno de los diseños principales en Xamarin.Forms.

> [!NOTE]
> Para mayor claridad, en las secciones siguientes se muestra cómo implementar diseños que respondan con solo un tipo de `Layout` cada vez. En la práctica, a menudo es más sencillo mezclar `Layout`s para lograr un diseño deseado mediante el `Layout` más sencillo o intuitivo para cada componente.

### <a name="stacklayout"></a>StackLayout

Tenga en cuenta la siguiente aplicación, se muestra en vertical:

![](device-orientation-images/photo-stack-portrait.png "Photo Application in Portrait")

y horizontal:

![](device-orientation-images/photo-stack-landscape.png "Photo Application in Landscape")

Que se logra con el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Algunos C# se usan para cambiar la orientación de `outerStack` en función de la orientación del dispositivo:

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Observe lo siguiente:

- `outerStack` se ajusta para presentar la imagen y los controles como una pila horizontal o vertical en función de la orientación, para aprovechar mejor el espacio disponible.

### <a name="absolutelayout"></a>AbsoluteLayout

Tenga en cuenta la siguiente aplicación, se muestra en vertical:

![](device-orientation-images/photo-abs-portrait.png "Photo Application in Portrait")

y horizontal:

![](device-orientation-images/photo-abs-landscape.png "Photo Application in Landscape")

Que se logra con el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Observe lo siguiente:

- Debido al modo en que se ha diseñado la página, no hay ninguna necesidad de código de procedimientos introducir la capacidad de respuesta.
- El `ScrollView` se usa para permitir que la etiqueta esté visible incluso cuando el alto de la pantalla sea menor que la suma de la altura fija de los botones y la imagen.

### <a name="relativelayout"></a>RelativeLayout

Tenga en cuenta la siguiente aplicación, se muestra en vertical:

![](device-orientation-images/photo-rel-portrait.png "Photo Application in Portrait")

y horizontal:

![](device-orientation-images/photo-rel-landscape.png "Photo Application in Landscape")

Que se logra con el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Observe lo siguiente:

- Debido al modo en que se ha diseñado la página, no hay ninguna necesidad de código de procedimientos introducir la capacidad de respuesta.
- El `ScrollView` se usa para permitir que la etiqueta esté visible incluso cuando el alto de la pantalla sea menor que la suma de la altura fija de los botones y la imagen.

### <a name="grid"></a>Grid

Tenga en cuenta la siguiente aplicación, se muestra en vertical:

![](device-orientation-images/photo-grid-portrait.png "Photo Application in Portrait")

y horizontal:

![](device-orientation-images/photo-grid-landscape.png "Photo Application in Landscape")

Que se logra con el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Junto con el siguiente código de procedimientos para controlar los cambios de rotación:

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Observe lo siguiente:

- Debido al modo en que se ha diseñado la página, hay un método para cambiar la posición de cuadrícula de los controles.

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Diseño con capacidad de respuesta (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Mostrar una imagen en función de la orientación de la pantalla](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
