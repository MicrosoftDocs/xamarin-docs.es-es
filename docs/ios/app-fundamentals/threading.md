---
title: Subprocesamiento en Xamarin. iOS
description: En este documento se describe cómo usar las API de System. Threading en una aplicación de Xamarin. iOS. Describe la biblioteca TPL, la compilación de aplicaciones con capacidad de respuesta y la recolección de elementos no utilizados.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 1c9282c790aa5436667b37e1861a96afffcaa668
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009438"
---
# <a name="threading-in-xamarinios"></a>Subprocesamiento en Xamarin. iOS

El tiempo de ejecución de Xamarin. iOS proporciona a los desarrolladores acceso a las API de subprocesos de .NET, tanto explícitamente al usar subprocesos (`System.Threading.Thread, System.Threading.ThreadPool`) como implícitamente cuando se usan los patrones de delegado asincrónico o los métodos BeginXXX, así como toda la gama de API que admiten la tarea. Biblioteca paralela.

Xamarin recomienda encarecidamente que use la [biblioteca de procesamiento paralelo de tareas](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) para crear aplicaciones por varias razones:

- El programador de TPL predeterminado delegará la ejecución de la tarea en el grupo de subprocesos, que a su vez aumentará dinámicamente el número de subprocesos necesarios mientras se produce el proceso, a la vez que evita un escenario en el que hay demasiados subprocesos que acaban compitiendo en el tiempo de CPU. 
- Es más fácil pensar en las operaciones en cuanto a las tareas de TPL. Puede manipularlos fácilmente, programarlos, serializar su ejecución o iniciar muchos en paralelo con un amplio conjunto de API. 
- Es la base para programar con las nuevas C# extensiones de lenguaje asincrónico. 

El grupo de subprocesos aumentará lentamente el número de subprocesos según sea necesario en función del número de núcleos de CPU disponibles en el sistema, la carga del sistema y la aplicación. Puede usar este grupo de subprocesos mediante la invocación de métodos en `System.Threading.ThreadPool` o mediante el `System.Threading.Tasks.TaskScheduler` predeterminado (parte de las *plataformas paralelas*).

Normalmente, los desarrolladores usan subprocesos cuando necesitan crear aplicaciones con capacidad de respuesta y no desean bloquear el bucle de ejecución de la interfaz de usuario principal.

 <a name="Developing_Responsive_Applications" />

## <a name="developing-responsive-applications"></a>Desarrollo de aplicaciones con capacidad de respuesta

El acceso a los elementos de la interfaz de usuario se debe limitar al mismo subproceso que está ejecutando el bucle principal de la aplicación. Si desea realizar cambios en la interfaz de usuario principal desde un subproceso, debe poner en cola el código mediante [NSObject. InvokeOnMainThread](xref:Foundation.NSObject), de la siguiente manera:

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

Lo anterior invoca el código dentro del delegado en el contexto del subproceso principal, sin producir condiciones de carrera que podrían bloquear la aplicación.

 <a name="Threading_and_Garbage_Collection" />

## <a name="threading-and-garbage-collection"></a>Subprocesamiento y recolección de elementos no utilizados

En el transcurso de la ejecución, el tiempo de ejecución de Objective-C creará y liberará objetos. Si los objetos se marcan para "versión automática", el entorno de tiempo de ejecución de Objective-C liberará esos objetos en la `NSAutoReleasePool`actual del subproceso. Xamarin. iOS crea un grupo de `NSAutoRelease` para cada subproceso de la `System.Threading.ThreadPool` y para el subproceso principal. Esta extensión cubre todos los subprocesos creados con el TaskScheduler predeterminado en System. Threading. Tasks.

Si crea sus propios subprocesos mediante `System.Threading` tendrá que proporcionar su propio grupo de `NSAutoRelease` para evitar que los datos se pierdan. Para ello, simplemente ajuste el subproceso en el siguiente fragmento de código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: como Xamarin. iOS 5,2 no tiene que proporcionar su propia `NSAutoReleasePool` ya que se proporcionará automáticamente una.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con el subproceso de la interfaz de usuario](~/ios/user-interface/ios-ui/ui-thread.md)
