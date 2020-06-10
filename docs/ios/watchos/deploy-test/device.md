---
title: Pruebas en dispositivos Apple Watch
description: En este documento se describe cómo implementar una aplicación de watchos compilada con Xamarin para realizar pruebas en un Apple Watch real. Se describen los dispositivos, el aprovisionamiento de perfiles, las pruebas y se proporcionan algunas sugerencias para la solución de problemas.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: cbbf8194505e9caa09587471020026d495f9f99b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569715"
---
# <a name="testing-on-apple-watch-devices"></a>Pruebas en dispositivos Apple Watch

Después de haber seguido los [pasos de implementación](~/ios/watchos/deploy-test/index.md) para crear los identificadores de aplicación y los grupos de aplicaciones (si es necesario), siga las instrucciones de esta página para:

- [Configure los dispositivos](#devices) en el centro de desarrollo de Apple y
- [Cree perfiles de aprovisionamiento de desarrollo](#profiles)y, a continuación,
- [Implementar y probar](#testing) en una Apple Watch.

<a name="devices"></a>

## <a name="devices"></a>Dispositivos

Probar las aplicaciones de iOS en un iPhone o iPad real siempre requiere que el dispositivo esté registrado en el centro de desarrollo. La lista de dispositivos tiene el siguiente aspecto (haga clic en el signo más **+** para agregar un nuevo dispositivo):

![](device-images/devices-sml.png "The device list looks like this")

Los relojes no son diferentes; ahora debe agregar el dispositivo Apple Watch antes de implementar las aplicaciones en él. Busque el UDID del reloj con **Xcode** (lista de**dispositivos de Windows >** ). Cuando se conecte el teléfono emparejado, también se mostrará la información del reloj:

[![](device-images/xcode-devices-sml.png "Paired Watch Information")](device-images/xcode-devices.png#lightbox)

Cuando conozca el UDID del reloj, agréguelo a la lista de dispositivos en el centro de desarrollo:

![](device-images/devices-watch-sml.png "The Watch's UDID in the device list")

Una vez que se ha agregado el dispositivo de inspección, asegúrese de que está seleccionado en cualquier perfil de aprovisionamiento nuevo o existente o en los perfiles de aprovisionamiento ad hoc que cree:

![](device-images/devices-provisioning.png "Available device list")

No olvide Si edita un perfil de aprovisionamiento existente para descargarlo y volver a instalarlo.

<a name="profiles"></a>

## <a name="development-provisioning-profiles"></a>Perfiles de aprovisionamiento de desarrollo

Para compilar para realizar pruebas en el dispositivo, debe crear un **Perfil de aprovisionamiento de desarrollo** para cada identificador de aplicación de la solución.

Si tiene un identificador de aplicación comodín, *solo se necesitará un perfil de aprovisionamiento*; pero si tiene un identificador de aplicación independiente para cada proyecto, necesitará un perfil de aprovisionamiento para cada identificador de aplicación:

![](device-images/provisioningprofile-development.png "The Development Provisioning Profile")

Una vez que haya creado los tres perfiles, aparecerán en la lista. No olvide descargar e instalar cada uno de ellos:

![](device-images/provisioningprofiles.png "The available Development Provisioning Profiles")

Puede comprobar el perfil de aprovisionamiento en las opciones del **proyecto** seleccionando la pantalla **compilar > la firma del lote de iOS** y seleccionando la configuración de **versión** o **depuración de iPhone** .

La lista de perfiles de **aprovisionamiento** mostrará todos los perfiles coincidentes. debería ver los perfiles coincidentes que ha creado en esta lista desplegable:

![](device-images/options-selectprofile.png "The Provisioning Profile list")

<a name="testing"></a>

## <a name="testing-on-a-watch-device"></a>Pruebas en un dispositivo de inspección

Una vez que haya configurado el dispositivo, los ID. de aplicación y los perfiles de aprovisionamiento, está listo para realizar la prueba.

1. Asegúrese de que el iPhone está enchufado y de que el reloj ya está emparejado con el iPhone.

2. Asegúrese de que la configuración esté establecida en **liberar** o **depurar**.

3. Asegúrese de que el dispositivo iPhone conectado está seleccionado en la lista destino.

4. Haga clic con el botón derecho en el proyecto de aplicación de iOS (no en el monitor o la extensión) y elija **establecer como proyecto de inicio**.

5. Haga clic en el botón **Ejecutar** (o elija una opción **iniciar** en el menú **Ejecutar** ).

6. La solución se compilará y la aplicación de iOS se implementará en el iPhone.
  Si el aprovisionamiento de la aplicación iOS o de la extensión de inspección no se establece correctamente, se producirá un error en la implementación en el iPhone.

7. Si la implementación se completa correctamente, el iPhone intentará enviar la aplicación de inspección automáticamente a la inspección emparejada. El icono de la aplicación aparecerá en la pantalla de inspección con un indicador de progreso de *instalación* circular.

8. Si la aplicación de inspección se instala correctamente, el icono permanecerá en la pantalla de inspección y la tocará para empezar a probar la aplicación.

## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error durante la implementación, use la **vista > paneles > registro del dispositivo** para ver más información sobre el error. A continuación se enumeran algunos errores y sus causas:

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Error MT3001: no se pudo el ensamblado AOT

Esto puede ocurrir al compilar en modo de depuración para implementar en un dispositivo Apple Watch.

Para solucionar *temporalmente* este problema, deshabilite las **compilaciones incrementales** en las opciones de proyecto de extensión de inspección > ventana compilar compilación de **> watchos** :

[![](device-images/disable-incremental-sml.png "The Incremental Builds checkbox")](device-images/disable-incremental.png#lightbox)

Esto se corregirá en una versión futura, después de la cual se pueden volver a habilitar las compilaciones incrementales para aprovechar las ventajas de tiempos de compilación más rápidos.

### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>No se puede iniciar la aplicación de inspección durante la depuración en el dispositivo

Al intentar depurar una aplicación de inspección en un dispositivo físico, solo se muestra el icono & cargar el control de número (y, finalmente, se agota el tiempo de espera). Esto se solucionará en una versión futura; una solución consiste en ejecutar una compilación de versión (que no permitirá la depuración).

### <a name="invalid-application-executable-or-application-verification-failed"></a>Error de comprobación de aplicación o archivo ejecutable de aplicación no válido

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Invalid Application Executable alert")

Si estos mensajes aparecen *en la pantalla de inspección* una vez que la aplicación ha intentado instalarse, podría haber un par de problemas:

- El dispositivo de inspección en sí no se ha agregado como un dispositivo en el centro de desarrollo de Apple. Siga las instrucciones para [configurar los dispositivos correctamente](#devices).

- Los perfiles de aprovisionamiento de desarrollo que se usan para las pruebas no incluían el dispositivo de inspección. o bien, una vez que se ha agregado el reloj a los perfiles de aprovisionamiento, no se han vuelto a descargar ni volver a instalar. Siga las instrucciones para [configurar correctamente los perfiles de aprovisionamiento](#profiles).

- Si el **registro del dispositivo iOS** contiene `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` , el valor de **info. plist** de la aplicación Watch **MinimumOSVersion** es incorrecto.
  Debe ser **8,2** : Si ha instalado Xcode 6,3, es posible que tenga que editar manualmente el origen para insertar establézcalo en 8,2.

- El archivo **contitles. plist** de la aplicación Watch tiene un derecho habilitado (por ejemplo, grupos de aplicaciones) que no debe tener.

- El **identificador de aplicación** de la aplicación Watch tiene un derecho habilitado (por ejemplo, grupos de aplicaciones) en el centro de desarrollo que no debe tener.

### <a name="install-never-finished"></a>Instalación nunca finalizada

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Este error puede indicar claves innecesarias (y no válidas) en el archivo **info. plist** de la aplicación de inspección. No debe incluir las claves diseñadas para la aplicación iOS o la extensión Watch en la aplicación Watch.

<!--eg. NSLocationAlwaysUsageDescription -->

### <a name="waiting-for-debugger-to-connect"></a>"esperando a que se conecte el depurador"

Si la ventana de salida de la **aplicación** se bloquea.

```csharp
waiting for debugger to connect
```

Compruebe si alguno de los paquetes Nuget que se han incluido en el proyecto tiene una dependencia de **Microsoft. BCL. Build**. Esto se agrega automáticamente con algunas bibliotecas publicadas por Microsoft, incluidas las conocidas [bibliotecas de cliente http de Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http/).

El archivo **Microsoft. BCL. Build. targets** que se agrega a **. csproj** puede interferir con el empaquetado de las extensiones de iOS durante la implementación. Puede realizar un seguimiento del [error](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Una posible solución es editar el archivo. csproj y trasladar manualmente **Microsoft. BCL. Build. targets** para que sea el último elemento.
