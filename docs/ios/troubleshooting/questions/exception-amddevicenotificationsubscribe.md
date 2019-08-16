---
title: System.Exception AMDeviceNotificationSubscribe returned ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d834c06c5fa5ee55e5e3b91bd16b5b4d326c42ee
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528158"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe returned ...

> [!IMPORTANT]
> Este problema se ha resuelto en las versiones recientes de Xamarin. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con la información de control de versiones completa y el resultado del registro de compilación completo.


## <a name="fix"></a>Solución

1. Elimine `usbmuxd` el proceso para que el sistema lo reinicie:

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

Este mensaje puede aparecer en un cuadro de diálogo de error al iniciar por primera vez Visual Studio para Mac `mtbserver.log` o en el archivo en la aplicación host de compilación de Xamarin. iOS (**host de compilación de Xamarin. iOS > ver el registro del host de compilación**).

Tenga en cuenta que se trata de un problema poco frecuente. Si Visual Studio tiene problemas para conectarse al host de compilación de Mac, hay otros errores que es más probable que aparezcan en `mtbserver.log` el archivo.

### <a name="errors-in-systemlog"></a>Errores en System. log

En algunos casos, los dos mensajes de error siguientes también pueden aparecer `/var/log/system.log`repetidamente en:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Información adicional

Una estimación de la causa principal del error es que los servicios de sistema de OS X responsables de notificar la información del simulador y del dispositivo iOS pueden, en raras ocasiones, entrar en un estado inesperado. Xamarin no puede interactuar correctamente con los servicios del sistema en este estado. Al reiniciar el equipo, se reinician los servicios del sistema y se soluciona el problema.

En función de los errores `system.log` que se produzcan, parece que este problema puede estar`mDNSResponder`relacionado con Bonjour (). Cambiar entre diferentes redes WiFi parece aumentar las posibilidades de que se produzca el problema.

## <a name="references"></a>Referencias

* [Error 11789: MonoTouch. MobileDevice. MobileDeviceException: AMDeviceNotificationSubscribe devuelto: 0xe8000063 [RESOLVEd noresponse]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
