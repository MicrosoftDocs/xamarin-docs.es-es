---
title: Solución de problemas de pruebas e implementación de Xamarin.iOS
description: En este documento se proporcionan sugerencias para solucionar problemas relacionados con la firma de código y el aprovisionamiento, TestFlight y la copia del lote de aplicaciones de iOS desde el host de compilación de Mac en Windows.
ms.prod: xamarin
ms.assetid: 65286D09-F74D-4F22-B6CD-D1BCD7FC7992
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/23/2017
ms.openlocfilehash: b27608642ed04216b10767d37d26699a0aef7f3e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573773"
---
# <a name="xamarinios-testing-and-deployment---troubleshooting"></a>Solución de problemas de pruebas e implementación de Xamarin.iOS

## <a name="code-signing--provisioning"></a>Aprovisionamiento y firma de código

El aprovisionamiento y la firma de código con iOS puede resultar bastante difícil, por lo que es importante asegurarse de que los certificados de firma de código y los perfiles de aprovisionamiento están en orden.

- Los equipos grandes deben abstenerse de usar el botón "Solucionar problema" en Xcode, como se muestra aquí:

    [![](troubleshooting-images/fixissue.png "The Fix Issues dialog")](troubleshooting-images/fixissue.png#lightbox)

    Esto creará certificados y perfiles de aprovisionamiento nuevos. En el mejor de los casos, esto creará un perfil de aprovisionamiento cada vez que un miembro del equipo haga clic en él, provocando una desorganización en los perfiles. En el peor caso, revocará los certificados del resto de personas de la empresa, provocando que sus aplicaciones dejen de funcionar.

- Mantenga la aplicación Acceso a llaves organizada y elimine los perfiles y certificados que han expirado. Los certificados de empresa duran tres años, mientras que otros solo uno. Los certificados no pueden renovarse, por lo que será necesario crear certificados nuevos antes de que expiren los antiguos. Asegúrese de revocar y eliminar los certificados antiguos, y volver a firmar aplicaciones con los certificados nuevos.

- Quite los perfiles de aprovisionamiento antiguos a medida que se instalen los nuevos. Es decir, que Visual Studio para Mac no se encuentre en una situación en la que tenga que decidir qué perfil usar. Para conseguir esto, primero asegúrese de eliminar el perfil en el Centro para desarrolladores de Apple y, después, vaya a *Preferencias > Su cuenta > Ver detalles...* Seleccione el perfil de aprovisionamiento y haga clic en **Mostrar en Finder**. Esto mostrará la ubicación del perfil en el sistema de archivos de Mac donde se puede eliminar con Finder.

- Asegúrese de que todos los certificados necesarios y las claves privadas correspondientes se encuentran disponibles. Para cada equipo necesitará un certificado del desarrollador (para instalar las aplicaciones en un dispositivo propio) y un certificado de distribución (para instalarlas en otros dispositivos).

- Vuelva a iniciar Xcode y Visual Studio para Mac o Visual Studio cuando se instale un nuevo certificado o perfil de aprovisionamiento.

## <a name="testflight"></a>TestFlight

A veces, las pruebas no funcionan tan bien como se había planeado.  Los siguientes pasos pueden ayudar en la resolución de problemas con TestFlight:

- TestFlight solo está disponible para las aplicaciones compatibles con iOS 8 y versiones anteriores.

- Debe existir un *perfil de distribución de App Store* con el derecho beta.

- La ventana **New iOS App submission (Nuevo envío de aplicación iOS)** debe contener exactamente la misma información que el archivo **Info.plist** de la aplicación, y todas las secciones deben rellenarse. Los iconos deben especificarse para la aplicación antes de cargarse en TestFlight.

- Al cargar una nueva compilación, tardará entre 1 y 5 minutos hasta que esta aparezca en iTunes Connect.

- El [conmutador de TestFlight Beta Testing](~/ios/deploy-test/testflight.md#beta-testing) debe estar activado para cada versión de la aplicación.

- Cada miembro del equipo de desarrolladores, que también es un evaluador interno, debe tener activado el conmutador **Evaluador interno**.

- Los usuarios que pertenecen a una cuenta de iTunes Connect, o que tienen otra, no pueden ser evaluadores internos. Solo pueden agregarse como evaluadores externos.

- Los usuarios internos y externos se agregan, se seleccionan y se invitan por separado. Cada lista debe administrarse de manera independiente.

- Apple debe aprobar cada compilación que se vaya a distribuir a los evaluadores externos. Si la versión de una compilación cambia, Apple necesitará una nueva revisión beta. Si cambia el número de compilación, la revisión es opcional.

- Los metadatos deben agregarse a las compilaciones que se distribuyen a evaluadores externos. Se puede obtener acceso haciendo clic en el número de compilación en **Mis aplicaciones > Versión preliminar**.

- Solo pueden enviarse dos compilaciones para cada revisión por día. Como el cambio de versión fuerza una revisión, esto significa que los números de versión solo pueden cambiarse dos veces al día.

<a name="Automatically_copy_app_bundles_back_to_Windows"></a>

## <a name="automatically-copy-app-bundles-back-to-windows"></a>Copia automática de lotes de aplicaciones .app en Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]
