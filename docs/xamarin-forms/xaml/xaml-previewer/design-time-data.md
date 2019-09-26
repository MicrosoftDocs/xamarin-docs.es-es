---
title: Usar datos de tiempo de diseño con el vista previa de XAML
description: En este artículo se explica cómo usar los datos de tiempo de diseño para mostrar los diseños con gran cantidad de datos en el visor de vista previa de XAML sin ejecutar la aplicación.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: a6a34615adc9cf290ff6bf9dd344487e5f29cfa2
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "69887849"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usar datos de tiempo de diseño con el vista previa de XAML

_Algunos diseños son difíciles de visualizar sin datos. Use estas sugerencias para sacar el máximo partido de las páginas con gran cantidad de datos en el visor de vistas previas de XAML._

## <a name="design-time-data-basics"></a>Aspectos básicos de los datos de tiempo de diseño

Los datos en tiempo de diseño son datos falsos que se establecen para que los controles sean más fáciles de visualizar en el visor de vista previa de XAML. Para empezar, agregue las siguientes líneas de código al encabezado de la página XAML:

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Después de agregar los espacios de nombres, puede `d:` colocar delante de cualquier atributo o control para mostrarlo en el controlador de vista previa de XAML. Los elementos `d:` con no se muestran en tiempo de ejecución.

Por ejemplo, puede Agregar texto a una etiqueta que normalmente tiene datos enlazados a él.

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![Datos de tiempo de diseño con texto en una etiqueta](xaml-previewer-images/designtimedata-label-sm.png "Datos de tiempo de diseño con texto como etiqueta")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

En este ejemplo, sin `d:Text`, el previsor de XAML no mostraría nada para la etiqueta. En su lugar, muestra "nombre". donde la etiqueta tendrá datos reales en tiempo de ejecución.

Puede usar `d:` con cualquier atributo de un control de Xamarin. Forms, como colores, tamaños de fuente y espaciado. Incluso puede agregarlo al propio control:

```xaml
<d:Button Text="Design Time Button" />
```

[![Datos de tiempo de diseño con un control de botón](xaml-previewer-images/designtimedata-controls-sm.png "Datos de tiempo de diseño con un control de botón")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

En este ejemplo, el botón solo aparece en tiempo de diseño. Use este método para colocar un marcador de posición en para un [control personalizado no admitido por el controlador de vista previa de XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Vista previa de imágenes en tiempo de diseño

Puede establecer un origen de tiempo de diseño para las imágenes enlazadas a la página o cargadas dinámicamente. En el proyecto de Android, agregue la imagen que quiere mostrar en el vista previa de XAML a los **recursos > carpeta drawable** . En el proyecto de iOS, agregue la imagen a la carpeta **recursos** . Después, puede mostrar esa imagen en la vista previa de XAML en tiempo de diseño:

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![Datos de tiempo de diseño con imágenes](xaml-previewer-images/designtimedata-image-sm.png "Datos de tiempo de diseño con iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Datos de tiempo de diseño para ListView

Los controles ListView son una manera popular de mostrar los datos en una aplicación móvil. Sin embargo, son difíciles de visualizar sin datos reales. Para usar los datos de tiempo de diseño con ellos, tiene que crear una matriz de tiempo de diseño para usarla como ItemsSource. El objeto de vista previa de XAML muestra lo que hay en esa matriz en el objeto ListView en tiempo de diseño.

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Datos de tiempo de diseño con un control ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "Datos de tiempo de diseño con un control ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

En este ejemplo se muestra un control ListView de tres TextCells en el objeto de vista previa de XAML. Puede cambiar `x:String` a un modelo de datos existente en el proyecto.

Consulte [la aplicación Hanselman. Forms de James Montemagno](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L26-L47) para obtener un ejemplo más complejo.

## <a name="alternative-hardcode-a-static-viewmodel"></a>Lugar Codificar un ViewModel estático

Si no desea agregar datos de tiempo de diseño a controles individuales, puede configurar un almacén de datos ficticios para enlazar a la página. Consulte la entrada de blog de James Montemagno [sobre cómo agregar datos en tiempo de diseño](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) para ver cómo enlazar a un ViewModel estático en XAML.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="requirements"></a>Requisitos

Los datos de tiempo de diseño requieren una versión mínima de Xamarin. Forms 3,6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense muestra líneas onduladas en los datos de tiempo de diseño

Se trata de un problema conocido y se corregirá en una próxima versión de Visual Studio. El proyecto se compilará sin errores.

### <a name="the-xaml-previewer-stopped-working"></a>El previsor de XAML dejó de funcionar

Intente cerrar y volver a abrir el archivo XAML, y limpie y recompile el proyecto.
