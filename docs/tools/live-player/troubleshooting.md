---
title: Solución de problemas Xamarin Live Player
description: En este documento se describen los problemas conocidos del Xamarin Live Player y las posibles correcciones. Se describen los problemas de conexión, los problemas de configuración y mucho más.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: 86cc5e30efd16be5826dfd641549dca47cfbc0e9
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602286"
---
# <a name="troubleshooting-xamarin-live-player"></a>Solución de problemas Xamarin Live Player

![Características en versión preliminar](~/media/shared/preview.png)

> [!WARNING]
> Finalizó la vista previa de Xamarin Live Player. La aplicación ya no está disponible. Las instrucciones siguientes se proporcionan para que los clientes sigan usando la versión preliminar con Visual Studio 2017.

> [!TIP]
> Puede usar [~/Xamarin-Forms/XAML/Hot-Reload/index.MD (recarga activa) en Visual Studio 2019 o Visual Studio para Mac para ver los diseños de pantalla a medida que los edita.

En este artículo se explican las limitaciones de Live Player y algunos problemas comunes con los pasos para corregirlos.

## <a name="limitations-of-xamarin-live-player"></a>Limitaciones de Xamarin Live Player

### <a name="ide-requirements"></a>Requisitos del IDE

La versión preliminar de Live Player solo está disponible en Visual Studio 2017.

### <a name="device-requirements"></a>Requisitos de los dispositivos

La aplicación Xamarin Live Player admite los siguientes dispositivos Android:

- Android 4,2 o posterior.
- ARM-v7a, ARM-v8a, ARM64-v8a, x86 o x86_64 procesador.

### <a name="ios-limitations"></a>limitaciones de iOS

Live Player no está disponible para iOS.

### <a name="xamarinforms-limitations"></a>Limitaciones de Xamarin. Forms

- No se admiten los representadores personalizados.
- No se admiten efectos.
- No se admiten controles personalizados con propiedades enlazables personalizadas.
- No se admiten los recursos incrustados (es decir, incrustar imágenes u otros recursos en una PCL).
- No se admiten los marcos de trabajo de MVVM de terceros (es decir, Prisma, cruzado de MVVM, luz de MVVM, etc.).

### <a name="other-project-type-limitations"></a>Otras limitaciones de tipo de proyecto

- Live Player no está diseñado para proyectos de Android nativos (que usan Android XML para la interfaz de usuario).

### <a name="miscellaneous-limitations"></a>Limitaciones diversas

- Compatibilidad limitada para la reflexión (actualmente afecta a algunos paquetes Nuget populares, como SQLite y Json.NET). Es posible que aún se admitan otras paquetes Nuget.
- Algunas clases del sistema no se pueden invalidar (por ejemplo, no puede implementar una subclase).
- Algunas características de la plataforma que requieren aprovisionamiento no funcionan en la aplicación Xamarin Live Player (sin embargo, se ha configurado para operaciones comunes como el acceso a la Galería fotográfica).
- Se omiten los destinos personalizados y los pasos de compilación. Por ejemplo, las herramientas como Fody, alajustar, AutoFac y automapper no se pueden incorporar.
- No se admiten proyectos de F #.
- Es posible que no se admitan escenarios avanzados con interfaces y clases genéricas personalizadas.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>El dispositivo móvil no se conecta después de examinar el código de barras (o de escribir código)

Se produce cuando el dispositivo móvil que ejecuta Xamarin Live Player no está en la misma red que el equipo que ejecuta el IDE. Consulte lo siguiente:

- Confirme que tanto el dispositivo como el equipo están en la misma red Wi-Fi.
  - Si el equipo también está conectado a una red cableada, intente desconectar la conexión con cable.
- Es posible que la red esté totalmente protegida (como algunas redes corporativas) y bloquee los puertos necesarios para Xamarin Live Player.
- Cierre la aplicación Xamarin Live Player y reiníciela.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Mensaje de error al intentar implementarlo en el IDE

**"IOException: no se pueden leer los datos de la conexión de transporte: la operación en un socket que no es de bloqueo bloquearía"**

Este error suele producirse cuando el dispositivo móvil que ejecuta Xamarin Live Player no está en la misma red que el equipo que ejecuta Visual Studio; Esto suele ocurrir cuando se conecta a un dispositivo que se ha emparejado anteriormente correctamente.

- Compruebe que tanto el dispositivo como el equipo están en la misma red Wi-Fi.
- Es posible que la red esté totalmente protegida (como algunas redes corporativas) y bloquee los puertos necesarios para Xamarin Live Player. Los puertos siguientes son necesarios para el Xamarin Live Player:
  - 37847: acceso a la red interna 
  - 8090: acceso a la red externa

## <a name="manually-configure-device"></a>Configurar manualmente el dispositivo

Si no puede conectarse a su dispositivo a través de Wi-Fi puede intentar configurar manualmente el dispositivo a través del archivo de configuración, con los siguientes pasos:

**Paso 1: abrir el archivo de configuración**

Vaya a la carpeta de datos de la aplicación:

- Windows: **%userprofile%\AppData\Roaming**
- macOS: **~/Users/$User/.config**

En esta carpeta, encontrará **PlayerDeviceList.xml** si no existe, tendrá que crear uno.

**Paso 2: obtener la dirección IP**

En la aplicación Xamarin Live Player, vaya a **acerca de > prueba de conexión > iniciar la prueba de conexión**.

Tome nota de la dirección IP; necesitará la dirección IP que se muestra al configurar el dispositivo.

**Paso 3: obtener el código de emparejamiento**

En el interior del Xamarin Live Player **pareja** o pareja de **nuevo**, presione **entrar manualmente**. Se mostrará un código numérico, que tendrá que actualizar el archivo de configuración.

**Paso 4: generar el GUID**

Vaya a: https://www.guidgenerator.com/online-guid-generator.aspx y genere un nuevo GUID y asegúrese de que mayúsculas esté activado.

**Paso 5: configurar el dispositivo**

Abra el **PlayerDeviceList.xml** en un editor como Visual Studio o Visual Studio Code. Debe configurar el dispositivo manualmente en este archivo. De forma predeterminada, el archivo debe contener el siguiente `Devices` elemento XML vacío:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Agregar dispositivo Android:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Cierre y vuelva a abrir Visual Studio.** El dispositivo debería aparecer en la lista.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Mensaje "no se encuentra el tipo o espacio de nombres" en el IDE

Compruebe que ha seleccionado un **proyecto de inicio** que coincide con el tipo de dispositivo (por ejemplo, Android) y que la configuración coincida con el tipo de dispositivo (por ejemplo, **Depurar** para Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>"Constructor del tipo ' InterpretedXamarin. Forms. Button ' no encontrado ' en el reproductor

Algunas clases del sistema no se pueden invalidar, por ejemplo:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: ' Resource. layout ' no contiene una definición para ' Main '"

Este error se produce en los proyectos de Android con las interfaces de usuario definidas en los archivos AXML.
Los archivos AXML no se admiten actualmente en Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Las pestañas y la barra de herramientas de Android se representan incorrectamente con Xamarin. Forms

Los proyectos de Xamarin. Forms Android deben usar "Toolbar. axml" y "Tab. axml" para los nombres de los archivos de diseño pertinentes. La plantilla predeterminada utiliza estos nombres; cambiar el nombre provocará problemas de representación.

## <a name="related-links"></a>Vínculos relacionados

- [Configuración](~/tools/live-player/install.md)
