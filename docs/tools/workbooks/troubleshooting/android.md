---
title: Solución de problemas de Xamarin Workbooks en Android
description: En este documento se proporcionan sugerencias para la solución de problemas para trabajar con Xamarin Workbooks en Android. Describe la compatibilidad con el emulador, los libros que no se cargan y otros temas.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: be19005ab1125c060ab0111e9f37568d5f4abe45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029588"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Solución de problemas de Xamarin Workbooks en Android

## <a name="emulator-support"></a>Compatibilidad con el emulador

Para ejecutar un libro de Android, debe haber un emulador de Android disponible para su uso. No se admiten dispositivos Android físicos.

Se recomienda el emulador de Google junto con HAXM si el equipo lo admite.
Si debe tener Hyper-V habilitado en el sistema, vaya a la Android Emulator de Visual Studio en su lugar.

Debe tener un emulador que ejecute Android 5,0 o posterior. No se admiten los emuladores de ARM. Usar solo dispositivos `x86` o `x86_64`.

Lea [nuestra documentación sobre la configuración de los emuladores de Android][android-emu] si no está familiarizado con el proceso.

> [!NOTE]
> Los libros 1,1 y anteriores intentarán (y se producirá un error) para usar los emuladores ARM si están disponibles. Para solucionar este error, inicie el emulador x86 que prefiera antes de abrir o crear un libro Android. Los libros siempre preferirán conectarse a un emulador en ejecución, siempre y cuando sea compatible.

## <a name="workbooks-wont-load"></a>Los libros no se cargan

### <a name="workbook-window-spins-forever-never-loads-windows"></a>La ventana del libro se gira siempre, nunca se carga (Windows)

En primer lugar, compruebe que el emulador tiene acceso de red totalmente operativo; para ello, pruebe cualquier sitio web en el explorador Web del emulador.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Android Emulator de Visual Studio no se puede conectar a Internet

Si el emulador no tiene acceso de red, puede que tenga que seguir estos pasos para corregir el conmutador de red de Hyper-V. Si cambia entre redes Wi-Fi con frecuencia, es posible que tenga que repetir esta operación periódicamente:

1. **Asegúrese de que se completen las operaciones de red críticas, ya que esto puede desconectar temporalmente Windows de Internet.**
1. Cierre los emuladores.
1. Abra `Hyper-V Manager`.
1. En `Actions`, abra `Virtual Switch Manager...`.
1. Elimine todos los conmutadores virtuales.
1. Haga clic en `OK`.
1. Inicie VS Android Emulator. Probablemente se le pedirá que vuelva a crear el conmutador de red virtual.
1. Pruebe que el explorador de VS Android Emulator puede tener acceso a Internet.

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>Vínculos relacionados

- [Informes de errores](~/tools/workbooks/install.md#reporting-bugs)
