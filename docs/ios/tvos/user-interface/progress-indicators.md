---
title: Trabajar con indicadores de progreso de tvOS en Xamarin
description: En este documento se describe cómo trabajar con indicadores de progreso en una aplicación de tvOS compilada con Xamarin. Describe las barras de progreso y los indicadores de actividad.
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 202ce8d674a39b06fd1b07460dff4bf573062592
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70291412"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>Trabajar con indicadores de progreso de tvOS en Xamarin

_En este artículo se describe el diseño y el trabajo con indicadores de progreso dentro de una aplicación Xamarin. tvOS._

Puede haber ocasiones en las que la aplicación Xamarin. tvOS necesite cargar contenido nuevo o realizar una operación de procesamiento prolongada. Durante estas ocasiones, debe presentar un indicador de actividad o una barra de progreso para que el usuario sepa que la aplicación todavía se está ejecutando y proporcionarles alguna indicación de la duración de la tarea que se está ejecutando.

![Indicadores de progreso de ejemplo](progress-indicators-images/intro01.png "Indicadores de progreso de ejemplo")

## <a name="about-activity-indicators"></a>Acerca de los indicadores de actividad

Un indicador de actividad se presenta como engranaje giratorio y se usa para representar una tarea de una longitud indeterminada. El indicador se presenta cuando la tarea se inicia y desaparece cuando se completa la tarea.

Apple tiene las siguientes sugerencias para trabajar con los indicadores de actividad:

- **Siempre que sea posible, use barras de progreso en lugar de ello** , ya que un indicador de actividad proporciona al usuario ningún comentario sobre el tiempo que tardará el proceso que se está ejecutando, siempre debe usar una barra de progreso si se conoce la longitud (por ejemplo, cuántos bytes se van a descargar en un archivo).
- **Mantenga el indicador animado** : los usuarios relacionan un indicador de actividad estacional con una aplicación detenida, por lo que siempre debe animar el indicador mientras se muestra.
- **Describir la tarea que se está procesando** : no basta con mostrar el indicador de actividad. se debe informar al usuario sobre el proceso en el que están esperando. Incluya una etiqueta significativa (normalmente una sola oración completa) que defina claramente la tarea.

## <a name="about-progress-bars"></a>Acerca de las barras de progreso

Una barra de progreso presenta como una línea que se llena con color para indicar el estado y la duración de una tarea que consume mucho tiempo. Las barras de progreso siempre se deben usar cuando se conoce la longitud de las tareas o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con barras de progreso:

- **Informar de forma precisa** de las barras de progreso siempre debe presentar una representación precisa del tiempo necesario para completar una tarea. No se tergiversa nunca el tiempo para que la aplicación parezca ocupada.
- **Usar para duraciones bien definidas** : las barras de progreso no solo deberían mostrar que se está llevando a cabo una tarea larga, sino proporcionar al usuario e indicar la cantidad de la tarea que se ha completado y una estimación del tiempo restante.

## <a name="progress-indicators-and-storyboards"></a>Indicadores de progreso y guiones gráficos

La manera más sencilla de trabajar con un indicador de progreso en una aplicación Xamarin. tvOS es agregarla a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en el archivo **Main. Storyboard** y ábralo para su edición.

2. Arrastre un **indicador de actividad** desde el **cuadro de herramientas** y colóquelo en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01.png "Un indicador de actividad")

3. En la pestaña **Widget** del **Panel de propiedades**, puede ajustar varias propiedades del indicador de actividad, como su **estilo**, **comportamiento**y **nombre**: 

    ![La pestaña widget para un indicador de actividad](progress-indicators-images/activity02.png "La pestaña widget para un indicador de actividad")
    
    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad C# en el código.

4. Arrastre una **vista de progreso** desde el **cuadro de herramientas** y colóquela en la vista: 

    ![Una vista de progreso](progress-indicators-images/activity03.png "Una vista de progreso")

5. En la pestaña **Widget** del **Explorador de propiedades**, puede ajustar varias propiedades de la vista de progreso, como su **estilo**, el **progreso** (porcentaje completado) y **el nombre**: 

    ![La pestaña widget para una vista de progreso](progress-indicators-images/activity04.png "La pestaña widget para una vista de progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista de progreso C# en el código.

6. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga doble clic en el archivo **Main. Storyboard** y ábralo para su edición.

2. Arrastre un **indicador de actividad** desde el **cuadro de herramientas** y colóquelo en la vista: 

    ![Un indicador de actividad](progress-indicators-images/activity01-vs.png
    "Un indicador de actividad")

3. En la pestaña **Widget** del **Explorador de propiedades**, puede ajustar varias propiedades del indicador de actividad, como su **estilo**, **comportamiento**y **nombre**: 

    ![La pestaña widget para un indicador de actividad](progress-indicators-images/activity02-vs.png "La pestaña widget para un indicador de actividad")

    El **nombre** determina el nombre de la propiedad que representa el indicador de actividad C# en el código.

4. Arrastre una **vista de progreso** desde el **cuadro de herramientas** y colóquela en la vista: 

   ![Una vista de progreso](progress-indicators-images/activity03-vs.png "Una vista de progreso")

5. En la pestaña **Widget** del **Explorador de propiedades**, puede ajustar varias propiedades de la vista de progreso, como su **estilo**, el **progreso** (porcentaje completado) y **el nombre**: 

    ![La pestaña widget para una vista de progreso](progress-indicators-images/activity04-vs.png "La pestaña widget para una vista de progreso")
    
    El **nombre** determina el nombre de la propiedad que representa la vista de progreso C# en el código.

6. Guarde los cambios.

-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

## <a name="working-with-activity-indicators"></a>Trabajar con indicadores de actividad

Como se indicó anteriormente, los indicadores de actividad deben mostrarse cuando la aplicación ejecuta un proceso largo de longitud indeterminada.

En cualquier momento, puede ver si un indicador de actividad se anima comprobando su propiedad `IsAnimating`. Si la propiedad `HidesWhenStopped` es `true`, el indicador de actividad se ocultará automáticamente cuando se detenga su animación.

Puede usar el siguiente código para iniciar la animación: 

```csharp
ActivityIndicator.StartAnimating();
```

Y lo siguiente detendrá la animación:

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> Estos fragmentos de código suponen que el **nombre** del indicador de actividad se estableció en **ActivityIndicator** en la pestaña **Widget** del diseñador de iOS.

## <a name="working-with-progress-bars"></a>Trabajar con barras de progreso

Una vez más, se debe usar una barra de progreso cada vez que la aplicación ejecute una tarea de ejecución prolongada de una duración conocida. 

La propiedad `Progress` se usa para establecer la cantidad de la tarea que se ha completado del 0% al 100% (de 0,0 a 1,0). Use la propiedad `ProgressTintColor` para establecer el color de la barra cantidad completada y la propiedad `TrackTintColor` para establecer el color de fondo (cantidad incompleta).

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con indicadores de progreso dentro de una aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
