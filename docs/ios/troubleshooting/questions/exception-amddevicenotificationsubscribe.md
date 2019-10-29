---
title: System.Exception AMDeviceNotificationSubscribe returned ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e1633989fc9b85d969f464857ab763153aea2e7d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031112"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe returned ...

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.

## <a name="fix"></a>Solución

1. Elimine el proceso de `usbmuxd` para que el sistema lo reinicie:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. Si eso no soluciona el problema, reinicie el equipo Mac.

## <a name="error-message"></a>Mensaje de error

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Este mensaje puede aparecer en un cuadro de diálogo de error al iniciar por primera vez Visual Studio para Mac o en el archivo de `mtbserver.log` en la aplicación host de compilación de Xamarin. iOS (**host de compilación de Xamarin. ios > ver el registro del host de compilación**).

Tenga en cuenta que se trata de un problema poco frecuente. Si Visual Studio tiene problemas para conectarse al host de compilación de Mac, hay otros errores que es más probable que aparezcan en el archivo de `mtbserver.log`.

### <a name="errors-in-systemlog"></a>Errores en System. log

En algunos casos, los dos mensajes de error siguientes también pueden aparecer repetidamente en `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Información adicional

Una estimación de la causa principal del error es que los servicios de sistema de OS X responsables de notificar la información del simulador y del dispositivo iOS pueden, en raras ocasiones, entrar en un estado inesperado. Xamarin no puede interactuar correctamente con los servicios del sistema en este estado. Al reiniciar el equipo, se reinician los servicios del sistema y se soluciona el problema.

En función de los errores de `system.log` parece que este problema puede estar relacionado con Bonjour (`mDNSResponder`). Cambiar entre diferentes redes WiFi parece aumentar las posibilidades de que se produzca el problema.

## <a name="references"></a>Referencias

* [Error 11789: MonoTouch. MobileDevice. MobileDeviceException: AMDeviceNotificationSubscribe devolvió: 0xe8000063 [RESOLVEd noresponse]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
