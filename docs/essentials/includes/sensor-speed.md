---
ms.topic: include
ms.openlocfilehash: b82ebeaeb28195e3ec0f5a0200c0448b6b76196a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259488"
---
## <a name="sensor-speed"></a>[Velocidad de sensor](xref:Xamarin.Essentials.SensorSpeed)

- **Más rápido**: obtener los datos del sensor tan rápido como sea posible (no se garantiza la devolución en el subproceso de interfaz de usuario).
- **Juego**: velocidad adecuada para juegos (no se garantiza la devolución en el subproceso de interfaz de usuario).
- **Predeterminado**: velocidad predeterminada adecuada para los cambios de orientación de pantalla.
- **Interfaz de usuario**: velocidad adecuada para la interfaz de usuario general.

Si no se garantiza la ejecución del controlador de eventos en el subproceso de interfaz de usuario y si el controlador de eventos necesita tener acceso a elementos de la interfaz de usuario, use el método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para ejecutar ese código en el subproceso de interfaz de usuario.
