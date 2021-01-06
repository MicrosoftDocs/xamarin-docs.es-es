---
title: Xamarin.Forms Preguntas más frecuentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e520d222029e0d29a25f16aacfb6273da52cbf9e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940218"
---
# <a name="no-locxamarinforms-frequently-asked-questions"></a>Xamarin.Forms Preguntas más frecuentes

## <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>[¿Cómo migrar mi aplicación a Xamarin.Forms 5,0?](forms5-migration.md)

La migración de una aplicación a Xamarin.Forms 5,0 requiere conocer los cambios más importantes.

## <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>[¿Puedo actualizar la Xamarin.Forms plantilla predeterminada a un paquete de NuGet más reciente?](update-forms-template.md)

En esta guía se usa la Xamarin.Forms plantilla de biblioteca de .net Standard como ejemplo, pero el mismo método general también funcionará para la Xamarin.Forms plantilla de proyecto compartido.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-no-locxamarinforms-xaml-files"></a>[¿Por qué no funciona el diseñador XAML de Visual Studio para Xamarin.Forms los archivos XAML?](forms-xaml-designer.md)

Xamarin.Forms no admite actualmente los diseñadores visuales para los archivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Error de compilación de Android: error inesperado en la tarea "LinkAssemblies"](android-linkassemblies-error.md)

Es posible que vea un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al compilar un proyecto de Xamarin. Android que use formularios. Esto sucede cuando el enlazador está activo (normalmente en una compilación de *versión* para reducir el tamaño del paquete de la aplicación). y se produce porque los destinos de Android no se actualizan al marco de trabajo más reciente.

## <a name="why-does-my-no-locxamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-error"></a>["¿Por qué es mi Xamarin.Forms . Se produce un ERROR en el proyecto de Android de Maps con COMPILETODALVIK: ERROR de nivel superior inesperado? "](maps-compiletodalvik-error.md)

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos de Android con Xamarin.Forms . Maps. Normalmente se resuelve aumentando el tamaño del montón de Java para el proyecto de Xamarin. Android.
