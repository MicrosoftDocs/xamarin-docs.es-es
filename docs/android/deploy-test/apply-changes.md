---
title: Aplicar cambios
description: Procedimientos para empezar a usar Aplicar cambios en los proyectos de Xamarin.Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "80215317"
---
# <a name="apply-changes"></a>Aplicar cambios

La característica Aplicar cambios permite enviar cambios de recursos a la aplicación en ejecución sin necesidad de reiniciarla. Esto ayuda a controlar la parte de la aplicación que se reinicia cuando se quieren implementar y probar pequeños cambios incrementales manteniendo el estado actual del emulador o dispositivo.

La característica Aplicar cambios usa funcionalidades de la [implementación de Android JVMTI](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci), la cual se admite en dispositivos o emuladores que ejecuten Android 8.0 (nivel de API 26) o posterior.

## <a name="requirements"></a>Requisitos

En la lista siguiente se muestran los requisitos para usar Aplicar cambios:

- **Visual Studio**: en Windows, actualice a Visual Studio 2019 versión 16.5 o posterior. En macOS, actualice a Visual Studio 2019 para Mac versión 8.5 o posterior.
- **Xamarin.Android**: Xamarin.Android 10.2 o posterior debe instalarse con Visual Studio (Xamarin.Android se instala automáticamente como parte de la carga de trabajo **Desarrollo móvil con .NET** en Windows y se instala como parte del **instalador de Visual Studio para Mac**).
- **Android SDK**: la API 28 o superior de Android debe estar instalada con el Administrador de Android SDK.
- **Dispositivo o emulador de destino**: su dispositivo o emulador debe ejecutar Android 8.0 (nivel de API 26) o posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Primeros pasos

Para empezar a usar la característica Aplicar cambios, debe asegurarse de que un dispositivo o un emulador ejecute Android 8.0 (nivel de API 26) o posterior. Después, ejecute la aplicación de Android con o sin depuración.

Puede interactuar con Aplicar cambios siguiendo las siguientes estrategias:

1. **Icono de la barra de herramientas.** Puede hacer clic en el icono de la barra de herramientas de la característica Aplicar cambios para aplicar cambios en el dispositivo o emulador de destino.

    [![Aplicar cambios: icono de la barra de herramientas](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Método abreviado de teclado.** Puede usar el método abreviado de teclado **Mayús + Alt + F5** para aplicar cambios en el dispositivo o emulador de destino.
3. **Menú Depurar.** Puede usar el elemento de menú **Depurar > Aplicar cambios** para aplicar cambios en el dispositivo o emulador de destino.

    [![Aplicar cambios: menú Depurar](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="get-started"></a>Primeros pasos

Para empezar a usar la característica Aplicar cambios, debe asegurarse de que un dispositivo o un emulador ejecute Android 8.0 (nivel de API 26) o posterior. Después, ejecute la aplicación de Android con o sin depuración.

Puede interactuar con Aplicar cambios siguiendo las siguientes estrategias:

1. **Método abreviado de teclado.** Puede usar el método abreviado de teclado **⌥ + ⇧ + R** para aplicar cambios en el dispositivo o emulador de destino.
2. **Menú Ejecutar.** Puede usar el elemento de menú **Ejecutar > Aplicar cambios** para aplicar cambios en el dispositivo o emulador de destino.

    [![Aplicar cambios: menú Depurar](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limitaciones

Los siguientes cambios requieren un reinicio de la aplicación:

- Cambiar el código C#.
- Agregar o quitar un recurso.
- Modificar AndroidManifest.xml.
- Cambiar bibliotecas nativas (archivos .so).

## <a name="related-links"></a>Vínculos relacionados

- [Aplicar cambios](https://developer.android.com/studio/run#apply-changes)
