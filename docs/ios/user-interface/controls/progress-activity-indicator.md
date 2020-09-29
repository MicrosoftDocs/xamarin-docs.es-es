---
title: Indicadores de progreso y actividad en Xamarin. iOS
description: En este documento se explica cómo usar los indicadores de progreso y actividad en Xamarin. iOS. Describe cómo utilizarlos mediante programación y con un guion gráfico.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: a2e8e6d1fdf3f51f015dccf12a6a3b455ceff741
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432743"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Indicadores de progreso y actividad en Xamarin. iOS

Es probable que la aplicación tenga que llevar a cabo tareas de ejecución prolongada como la carga o el procesamiento de datos y que este retraso pueda provocar un retraso en la actualización de la interfaz de usuario. Durante este tiempo, siempre debe usar un indicador de progreso para asegurarse de que el sistema está ocupado con el trabajo. Esto proporciona al control de usuario que la aplicación está funcionando en su solicitud, que no está esperando su entrada y puede proporcionar un medio para detallar exactamente cuánto tiempo tiene que esperar.

iOS proporciona dos maneras principales de proporcionar esta indicación de progreso en la aplicación: indicadores de actividad (incluido un indicador de actividad de _red_ específico) y barras de progreso.

## <a name="activity-indicator"></a>Indicador de actividad

Los indicadores de actividad deben mostrarse cuando la aplicación se ejecuta en un proceso largo, pero no conoce el tiempo exacto que requerirá la tarea.

Apple tiene las siguientes sugerencias para trabajar con los indicadores de actividad:

- **Siempre que sea posible, use barras de progreso en lugar de ello** , ya que un indicador de actividad proporciona al usuario ningún comentario sobre el tiempo que tardará el proceso que se está ejecutando, siempre debe usar una barra de progreso si la longitud se conoce (por ejemplo, cuántos bytes se van a descargar en un archivo).
- **Mantenga el indicador animado** : los usuarios relacionan un indicador de actividad estacional con una aplicación detenida, por lo que siempre debe tener el indicador animado mientras se muestra.
- **Describir la tarea que se está procesando** : si solo se muestra el indicador de actividad por sí solo es suficiente, se debe informar al usuario sobre el proceso en el que están esperando. Incluya una etiqueta significativa (normalmente una sola oración completa) que defina claramente la tarea.

### <a name="implementing-an-activity-indicator"></a>Implementar un indicador de actividad

Un indicador de actividad se implementa a través de la [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) clase para indicar que `UIActivity` está teniendo lugar.

### <a name="activity-indicators-and-storyboards"></a>Indicadores gráficos y de actividad

Si usa el diseñador de iOS para crear la interfaz de usuario, el indicador de actividad se puede Agregar al diseño desde el cuadro de herramientas. Las siguientes propiedades se pueden ajustar desde el Panel de propiedades:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Administrar el comportamiento del indicador de actividad

Use los `StartAnimating()` `StopAnimating()` métodos y para iniciar y detener la animación del indicador de actividad.

Establezca la `HidesWhenStopped` propiedad en `true` para que el indicador de actividad desaparezca después de `StopAnimating()` que se haya llamado a. Se establece en de `true` forma predeterminada. En cualquier momento puede ver si el indicador de actividad está ejecutando su animación giratoria comprobando la `IsAnimating` propiedad. 

### <a name="managing-activity-indicator-appearances"></a>Administrar aspectos de indicador de actividad

La `UIActivityIndicatorViewStyle` enumeración se puede pasar como un parámetro al crear una instancia del indicador de actividad. Puede utilizar esto para establecer el estilo visual en `Gray` , `White` o `WhiteLarge` , por ejemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Puede invalidar el color proporcionado por estableciendo `UIActivityIndicatorViewStyle`  la `Color` propiedad.

## <a name="progress-bar"></a>ProgressBar

Una barra de progreso presenta como una línea que se llena con color para indicar el estado y la duración de una tarea que consume mucho tiempo. Las barras de progreso siempre se deben usar cuando la duración de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con barras de progreso:

- **Informar de forma precisa** de las barras de progreso siempre debe ser una representación precisa del tiempo necesario para completar una tarea. No se tergiversa nunca el tiempo para que la aplicación parezca ocupada.
- **Usar para duraciones bien definidas** : la barra de progreso no solo debe mostrar que se está llevando a cabo una tarea larga, sino proporcionar al usuario e indicar la cantidad de la tarea que se ha completado y una estimación del tiempo restante.

### <a name="implementing-an-progress-bar"></a>Implementar una barra de progreso

Se crea una barra de progreso mediante la creación de una instancia de [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>Barras de progreso y guiones gráficos

También puede Agregar una barra de progreso a la interfaz de usuario al usar el diseñador de iOS. Busque **vista de progreso** en el **cuadro de herramientas** y arrástrela a la vista.

Las siguientes propiedades se pueden ajustar en el panel de propiedades:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>Administrar el comportamiento de la barra de progreso

El progreso de la barra se puede establecer inicialmente mediante la `Progress` propiedad:

```csharp
ProgressBar.Progress = 0f;
```

El progreso se puede ajustar mediante el `SetProgress` método y pasando una declaración booleana si se desea que el cambio se anime o no.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obtener más información sobre el uso de la barra de progreso, consulte la receta de progreso de los [informes](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) y el [ejemplo de UICatalog tvOS](/samples/xamarin/ios-samples/tvos-uicatalog).

### <a name="managing-progress-bar-appearance"></a>Administrar la apariencia de la barra de progreso

De forma similar a un indicador de actividad, la `UIProgressViewStyle` enumeración se puede pasar como un parámetro al crear una instancia de la barra de progreso.

La imagen de progreso y de seguimiento y el color de matiz se pueden ajustar con las siguientes propiedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```