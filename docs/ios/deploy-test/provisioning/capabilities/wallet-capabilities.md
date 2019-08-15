---
title: Capacidades de Wallet en Xamarin.iOS
description: Agregar capacidades a una aplicación a menudo requiere una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las capacidades de Wallet.
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 25ebf101dfe0cc3d51e8411b7977e6dc0b061f2d
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68978405"
---
# <a name="wallet-capabilities-in-xamarinios"></a>Capacidades de Wallet en Xamarin.iOS

_Agregar funcionalidades a una aplicación requiere con frecuencia una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las funcionalidades de Wallet._

Wallet es una aplicación que almacena y muestra códigos de barras y otro contenido que permite a los usuarios mostrar billetes, tarjetas de embarque y cupones desde su dispositivo. Esta información se almacena en un _pass_ (pase, entrada). Por ejemplo, una tarjeta de embarque o un billete representarían un pase. 

Los desarrolladores pueden trabajar con Wallet de varias maneras:

*   Para crear un pase no es necesario compilar una aplicación. Un Passfile es un archivo comprimido que contiene algunos archivos JSON y archivos de metadatos opcionales. Para prepararlo necesitará un [identificador de tipo de pase](~/ios/platform/passkit.md) y un [certificado de pase](~/ios/platform/passkit.md). Luego, esta información se declara en un archivo JSON. En la guía [Introduction to PassKit](~/ios/platform/passkit.md) (Introducción a PassKit) encontrará más información sobre cómo aprovisionar un Passfile.

*   Las aplicaciones complementarias se escriben para distribuir pases. También tienen la funcionalidad para crear, editar y actualizar pases y agregarlos a la aplicación Wallet. Un buen ejemplo de este tipo de aplicación sería una aplicación de cine: una vez que un usuario compra una entrada con la aplicación, se puede agregar directamente a Wallet desde la aplicación. Para usar una aplicación complementaria, el perfil de aprovisionamiento debe incluir un Id. de aplicación con las características de Wallet, que se pueden establecer si se siguen los pasos siguientes. La aplicación también debe incluir los derechos necesarios.

*   Conduit Apps son aplicaciones que no manipulan los pases directamente. Tienen una interacción mínima con el pase más allá de recibirlo y de proporcionar al usuario la opción de agregarlo a Wallet. Estas aplicaciones no necesitan ningún aprovisionamiento o derecho especial, pero usan algunos métodos del marco de PassKit.

## <a name="developer-center"></a>Centro para desarrolladores

Para crear un perfil de aprovisionamiento para usarlo con Wallet, haga lo siguiente:

1. Vaya a la sección [Certificates, Identifiers & Profiles](https://developer.apple.com/account/ios/certificate/) (Certificados, identificadores y perfiles) del Portal para desarrolladores de Apple.
2. En **Identifiers** (Identificadores), vaya a **App IDs** (Identificadores de aplicación): 
    
    ![Selección del identificador de aplicación](wallet-capabilities-images/image17.png)

3. Haga clic en el icono **+** situado en la esquina superior derecha de la página.
4. Registre un nuevo identificador de aplicación asignándole un **nombre** y un identificador de paquete (tenga en cuenta que este identificador de paquete debe coincidir con el identificador de paquete del proyecto):
   
    ![Agregar detalles del identificador de aplicación](wallet-capabilities-images/image18.png)

5. Seleccione el servicio de aplicaciones **Wallet** de la lista de servicios:
    
    ![Pantalla de selección de servicios](wallet-capabilities-images/image19.png)

6. Presione **Continue** (Continuar) y **Register** (Registrar) para crear el identificador de aplicación.

Si es necesario, los identificadores de aplicación existentes se pueden editar para agregar la capacidad de Wallet.

Este Id. de aplicación ya puede usarse para generar, o volver a generar, un nuevo perfil de aprovisionamiento, como se explica en la guía [Trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md):

![Uso del identificador de aplicación recién creado para crear un perfil de aprovisionamiento](wallet-capabilities-images/image20.png)


Para más información sobre el uso de Wallet, consulte las siguientes guías:

*   [Introduction to PassKit](~/ios/platform/passkit.md) (Introducción a PassKit)
 
## <a name="next-steps"></a>Pasos siguientes
 
En la siguiente lista se describen los pasos adicionales que se deben seguir:

* Use el espacio de nombres del marco en su aplicación.
* Agregue los derechos necesarios a la aplicación. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona información sobre los derechos necesarios y sobre cómo agregarlos.
* En la opción **Firma de lote de iOS** de la aplicación, asegúrese de que **Derechos personalizados** esté establecido en **Entitlements.plist**. Esta _no_ es la configuración predeterminada para las compilaciones de depuración y del simulador de iOS.

Si experimenta problemas con servicios de aplicaciones, vea la sección [Solución de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) de la guía principal.
