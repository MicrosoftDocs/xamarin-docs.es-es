---
title: Xamarin.FormsPreguntas más frecuentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edd6cfefe18ff3d5cc97ec58f3bce867f11df7c8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135881"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.FormsPreguntas más frecuentes

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[¿Puedo actualizar la Xamarin.Forms plantilla predeterminada a un paquete de NuGet más reciente?](update-forms-template.md)
En esta guía se usa la Xamarin.Forms plantilla de biblioteca de .net Standard como ejemplo, pero el mismo método general también funcionará para la Xamarin.Forms plantilla de proyecto compartido.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[¿Por qué no funciona el diseñador XAML de Visual Studio para Xamarin.Forms los archivos XAML?](forms-xaml-designer.md)
Xamarin.Formsno admite actualmente los diseñadores visuales para los archivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Error de compilación de Android: error inesperado en la tarea "LinkAssemblies"](android-linkassemblies-error.md)
Es posible que vea un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al compilar un proyecto de Xamarin. Android que use formularios. Esto sucede cuando el enlazador está activo (normalmente en una compilación de *versión* para reducir el tamaño del paquete de la aplicación). y se produce porque los destinos de Android no se actualizan al marco de trabajo más reciente. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["¿Por qué es mi Xamarin.Forms . Se produce un ERROR en el proyecto de Android de Maps con COMPILETODALVIK: ERROR de nivel superior inesperado? "](maps-compiletodalvik-error.md)
Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos de Android con Xamarin.Forms . Maps. Normalmente se resuelve aumentando el tamaño del montón de Java para el proyecto de Xamarin. Android.
