---
title: Recarga activa de XAML para Xamarin. Forms
description: Vuelva a cargar los cambios en el archivo XAML al instante en la aplicación en ejecución, por lo que no tiene que compilar el proyecto de Xamarin. Forms después de cada cambio de XAML.
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 59b72daecdc1159494d069bec044ee1891916d11
ms.sourcegitcommit: fd349a121164c85545f4b0881940eefdd0cf3b65
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896407"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>Recarga activa de XAML para Xamarin. Forms (versión preliminar)

![Características en versión preliminar](~/media/shared/preview.png)

La recarga activa de XAML se conecta al flujo de trabajo existente para aumentar su productividad y ahorrar tiempo. Sin la recarga activa de XAML, debe compilar e implementar la aplicación cada vez que quiera ver un cambio de XAML. Con la recarga activa, al guardar el archivo XAML, los cambios se reflejan en directo en la aplicación en ejecución. Además, se mantendrán el estado y los datos de navegación, lo que le permitirá iterar rápidamente en la interfaz de usuario sin perder su lugar en la aplicación. Por lo tanto, con la recarga activa de XAML, pasará menos tiempo recompilando e implementando sus aplicaciones para validar los cambios de la interfaz de usuario.

## <a name="system-requirements"></a>Requisitos del sistema

| IDE/marco de trabajo | Versión necesaria |
|------|------------------|
|Visual Studio 2019 | 16,3 o superior
Visual Studio 2019 para Mac | 8,3 o superior
Xamarin.Forms | 4,1 o superior

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>Usar la recarga activa de XAML para Xamarin. Forms

No se requiere ninguna instalación o configuración adicional para usar la recarga activa de XAML. Está integrado en Visual Studio y puede habilitarse en la configuración del IDE. Una vez habilitada, puede empezar a usar la recarga activa de XAML mediante la depuración de la aplicación en un emulador, un simulador o un dispositivo físico. Actualmente, la recarga activa de XAML solo funciona durante la depuración en iOS o Android.

En Windows, la recarga en caliente de XAML se puede habilitar activando la casilla **Habilitar la recarga activa de Xamarin** en **herramientas** > **Opciones** > **Xamarin** > **Hot Reload**.

En un equipo Mac, la recarga activa de XAML se puede habilitar activando la casilla **Habilitar la recarga activa de Xamarin** en los proyectos de**preferencias** > de **Visual Studio** > **proyectos** > de la**recarga activa de Xamarin**.

## <a name="resilient-reloading"></a>Recarga resistente

Si realiza un cambio que no se puede volver a cargar la recarga activa de XAML, se mostrará un mensaje de error con IntelliSense. Estos cambios, conocidos como ediciones forzadas, incluyen escribir indirectamente el código XAML o conectar un control a un controlador de eventos que no existe. Incluso con una edición forzada, puede seguir recargando sin reiniciar la aplicación: haga otro cambio en otra parte del archivo XAML y presione guardar. La edición forzada no se recargará, pero se seguirán aplicando los demás cambios.

## <a name="known-limitations"></a>Limitaciones conocidas

- No se pueden agregar, quitar ni cambiar el nombre de archivos o paquetes NuGet durante una sesión de recarga activa de XAML. Si agrega o quita un archivo o un paquete de NuGet, vuelva a compilar y volver a implementar la aplicación para seguir usando la recarga activa de XAML.
- Establezca el enlazador en **vincular ninguno** para obtener la mejor experiencia. La configuración de **Link SDK solo** funciona la mayor parte del tiempo, pero puede producir un error en ciertos casos.
- La depuración en un iPhone físico requiere que el intérprete use la recarga activa de XAML. Agregue **--interprete** al campo **argumentos de Mtouch adicionales** en la configuración de compilación de iOS para usar la recarga activa de XAML.
- Las referencias creadas mediante la asignación de un control a otro campo o propiedad utilizando su `x:Name` valor no se volverán a cargar.
- La actualización de la jerarquía visual de la aplicación de Shell en **AppShell. Xaml** puede producir problemas al mantener el estado de la aplicación. Vuelva a compilar la aplicación para continuar con la recarga.
- La recarga activa de XAML no puede C# volver a cargar el código, incluidos los controladores de eventos, los controles personalizados, el código subyacente de la página y las clases adicionales.

## <a name="migrate-from-the-private-preview"></a>Migración desde la versión preliminar privada

Si forma parte de la versión preliminar privada, la extensión de recarga activa de XAML se actualizará automáticamente cuando se actualice Visual Studio. Si decide no actualizar Visual Studio, puede seguir usando la versión actual de la recarga activa de XAML, pero no recibirá ninguna actualización adicional a través de la fuente de extensión de la versión preliminar privada.

## <a name="troubleshooting"></a>Solución de problemas

- Si no se puede inicializar la recarga activa de XAML:
  - Actualice la versión de Xamarin. Forms.
  - Asegúrese de que se encuentre en la versión más reciente del IDE.
  - Establezca la configuración del enlazador de Android o iOS en **no vincular** en la configuración de compilación del proyecto.
- Si no ocurre nada al guardar el archivo XAML, asegúrese de que la recarga activa está habilitada en el IDE.
- Si realiza la depuración en un iPhone físico y la aplicación deja de responder, compruebe que el intérprete está habilitado. Para activarla, agregue **--interprete** al campo **argumentos de Mtouch adicionales** en la configuración de compilación de iOS.

Para notificar un error, use la herramienta de comentarios en el menú **ayuda** > **Enviar comentarios** > **notificar un problema** de Windows y el menú **ayuda** > **notificar un problema** en un equipo Mac.
