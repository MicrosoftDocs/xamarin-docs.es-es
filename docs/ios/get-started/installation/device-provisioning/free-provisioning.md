---
title: Aprovisionamiento gratuito para aplicaciones Xamarin.iOS
description: En este documento se describe la manera en que los desarrolladores de Xamarin.iOS pueden probar su aplicación en un dispositivo físico sin necesidad de iniciar sesión en el programa para desarrolladores de Apple de pago.
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/16/2018
ms.openlocfilehash: 951b019a4a0bf503629b6ac63853bd5b1c818bce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022756"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Aprovisionamiento gratuito para aplicaciones Xamarin.iOS

El aprovisionamiento gratuito permite a los desarrolladores de Xamarin.iOS implementar y probar sus aplicaciones en dispositivos iOS **sin** formar parte del **programa de Apple Developer**.
Aunque la prueba del simulador resulta útil y práctica, las aplicaciones también deben probarse en dispositivos iOS físicos para verificar su correcto funcionamiento con limitaciones reales de memoria, almacenamiento y conectividad de red.

Si quiere usar el aprovisionamiento gratuito para implementar una aplicación en un dispositivo, siga estos pasos:

- Use Xcode para crear la *identidad de firma* necesaria (el certificado de desarrollador y la clave privada), así como para *aprovisionar el perfil* (que contenga un id. de Apple explícito y el UDID de un dispositivo iOS conectado).
- Use la identidad de firma y el perfil de aprovisionamiento creados mediante Xcode en Visual Studio para Mac o Visual Studio 2019 para implementar su aplicación Xamarin.iOS.

> [!IMPORTANT]
> El [aprovisionamiento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) permite configurar automáticamente un dispositivo para pruebas de desarrollo en Visual Studio para Mac o Visual Studio 2019. Sin embargo, el aprovisionamiento automático no es compatible con el aprovisionamiento gratuito. Para poder usar el aprovisionamiento automático, debe tener una cuenta de pago del programa de Apple Developer.

## <a name="requirements"></a>Requisitos

Para implementar sus aplicaciones Xamarin.iOS en un dispositivo con el aprovisionamiento gratuito:

- El ID de Apple que se esté usando no debe estar conectado al programa de Apple Developer.
- La aplicación Xamarin.iOS debe usar un id. de aplicación explícito en lugar de un id. de aplicación de carácter comodín.
- El identificador de agrupación utilizado en su aplicación de Xamarin.iOS debe ser único y no puede haberse usado en ninguna otra aplicación anteriormente. Cualquier identificador de agrupación que se use con el aprovisionamiento gratuito **no podrá** volver a usarse.
- Si ya ha distribuido una aplicación, no podrá implementarla con el aprovisionamiento gratuito.
- Si su aplicación usa App Services, necesitará crear un perfil de aprovisionamiento como se detalla en la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services). 

Consulte la sección de [limitaciones](#limitations) de este documento para obtener más información acerca de las limitaciones asociadas al aprovisionamiento gratuito, así como las [guías de distribución de aplicaciones](~/ios/deploy-test/app-distribution/index.md) para obtener más información acerca de la distribución de aplicaciones iOS.

## <a name="testing-on-device-with-free-provisioning"></a>Pruebas en dispositivos con el aprovisionamiento gratuito

Siga estos pasos para probar su aplicación Xamarin.iOS con el aprovisionamiento gratuito.

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>Usar Xcode para crear una identidad de firma y un perfil de aprovisionamiento

1. Si no tiene ningún ID de Apple, [créelo](https://appleid.apple.com).
2. Abra Xcode y navegue hasta **Xcode > Preferencias**.
3. En **Cuentas**, use el botón **+** para agregar su id. de Apple existente. Debería tener un aspecto similar al de la siguiente captura de pantalla:

    ![Preferencias de Xcode: Cuentas](free-provisioning-images/launchapp1.png "Preferencias de Xcode: Cuentas")

4. Cierre las preferencias de Xcode.
5. Conecte el dispositivo iOS en el que quiera implementar su aplicación.
6. Cree un proyecto en Xcode. Elija **Archivo > Nuevo > Proyecto** y seleccione **Aplicación de una vista**.
7. En el cuadro de diálogo del nuevo proyecto, establezca el ID de Apple que acaba de agregar como el valor de **Equipo**. En la lista desplegable, debería tener un aspecto similar a **su nombre (equipo Personal)** :

    ![Creación de una aplicación](free-provisioning-images/launchapp2.png "Creación de una nueva aplicación")

8. Una vez creado el nuevo proyecto, elija un esquema de compilación de Xcode que tenga como destino el dispositivo iOS (en lugar de un simulador).

    ![Selección de un esquema de compilación de Xcode](free-provisioning-images/xcodescheme.png "Selección de un esquema de compilación de Xcode")

9. Abra la configuración de proyecto de la aplicación. Para ello, seleccione el nodo superior en el **navegador de proyectos** de Xcode.
10. En **General > Identidad**, asegúrese de que el **identificador de agrupación** _coincida exactamente_ con el identificador de agrupación de su aplicación Xamarin.iOS.

    ![Establecimiento de un identificador de lote](free-provisioning-images/launchapp5.png "Establecimiento de un identificador de lote")

    > [!IMPORTANT]
    > Xcode solo creará un perfil de aprovisionamiento para un id. de aplicación explícito. Además, debe ser idéntico al identificador de aplicación de su aplicación Xamarin.iOS.
    > Si difieren, no podrá usar el aprovisionamiento gratuito para implementar su aplicación Xamarin.iOS.

11. En **Información sobre la implementación**, asegúrese de que el destino de la implementación coincida con la versión de iOS instalada en su dispositivo iOS conectado, o bien que sea inferior a esta.
12. En **Firma**, seleccione **Administrar firma automáticamente** y seleccione su equipo en la lista desplegable:

    ![Administrar la firma automáticamente](free-provisioning-images/launchapp6.png "Administrar la firma automáticamente")

    Xcode generará automáticamente un perfil de aprovisionamiento y una identidad de firma. Puede verlo haciendo clic en el icono de información, situado junto al perfil de aprovisionamiento:

    ![Visualización del perfil de aprovisionamiento](free-provisioning-images/launchapp7.png "Visualización del perfil de aprovisionamiento")

    > [!TIP]
    > Si se produce un error cuando Xcode intente generar un perfil de aprovisionamiento, asegúrese de que el esquema de compilación seleccionado actualmente tenga como destino el dispositivo iOS conectado (en lugar de un simulador).

13. Para realizar pruebas en Xcode, implemente la aplicación vacía en su dispositivo haciendo clic en el botón Ejecutar.

### <a name="deploy-your-xamarinios-app"></a>Implementación de su aplicación Xamarin.iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Conecte su dispositivo iOS al host de compilación Mac mediante USB o de forma [inalámbrica](~/ios/deploy-test/wireless-deployment.md).
2. En el **Panel de solución** de Visual Studio para Mac, haga doble clic en **Info.plist**.
3. En **Firma**, seleccione **Aprovisionamiento manual**.
4. Haga clic en el botón **Firma de agrupación de iOS…** .
5. Para **Configuración**, seleccione **Depurar**.
6. Para **Plataforma**, seleccione **iPhone**.
7. Seleccione la **identidad de firma** creada por Xcode.
8. Seleccione el **perfil de aprovisionamiento** creado por Xcode.

    ![Establecimiento de la identidad de firma y el perfil de aprovisionamiento](free-provisioning-images/launchapp8.png "Establecimiento de la identidad de firma y el perfil de aprovisionamiento")

    > [!TIP]
    > Si no ve su identidad de firma o el perfil de aprovisionamiento correcto, puede que necesite reiniciar Visual Studio para Mac.

9. Haga clic en **Aceptar** para guardar y cerrar las **opciones del proyecto**.
10. Seleccione su dispositivo iOS y ejecute la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Asegúrese de que Visual Studio 2019 o Visual Studio 2017 están [emparejados con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Conecte su dispositivo iOS al host de compilación Mac mediante USB o de forma [inalámbrica](~/ios/deploy-test/wireless-deployment.md).
3. En el **Explorador de soluciones** de Visual Studio 2019 o Visual Studio 2017, haga clic con el botón derecho en su proyecto Xamarin.iOS y seleccione **Propiedades**.
4. Navegue hasta **Firma de agrupación de iOS**.
5. Para **Configuración**, seleccione **Depurar**.
6. Para **Plataforma**, seleccione **iPhone**.
7. Seleccione **Aprovisionamiento manual**.
8. Seleccione la **identidad de firma** creada por Xcode.
9. Seleccione el **perfil de aprovisionamiento** creado por Xcode.
    
    ![Establecimiento de la identidad de firma y el perfil de aprovisionamiento](free-provisioning-images/setprofile-w157.png "Establecimiento de la identidad de firma y el perfil de aprovisionamiento")

    > [!TIP]
    > Xcode crea la identidad de firma y el perfil de aprovisionamiento y los almacena en el host de compilación Mac. Puesto que Visual Studio 2019 o Visual Studio 2017 está [emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) con el host de compilación Mac, están disponibles para este programa. Si no aparecen, es posible que deba reiniciar Visual Studio 2019 o Visual Studio 2017.

10. Guarde y cierre las propiedades del proyecto.
11. Seleccione su dispositivo iOS y ejecute la aplicación.

-----

## <a name="limitations"></a>Limitaciones

Apple ha impuesto varias limitaciones sobre cuándo y cómo puede usar el aprovisionamiento gratuito para ejecutar su aplicación en un dispositivo iOS, garantizando que solo puede implementar en *su* dispositivo:

- El acceso a iTunes Connect está limitado y, por lo tanto, los servicios como la publicación en el App Store y TestFlight no están disponibles para desarrolladores que aprovisionan sus aplicaciones de manera gratuita. Se necesita una cuenta de Apple Developer (Enterprise o Personal) para distribuir mediante medios Ad Hoc e internos.
- Los perfiles de aprovisionamiento creados con el aprovisionamiento gratuito expirarán tras una semana y las identidades de firma lo harán tras un año. 
- Puesto que Xcode solo creará perfiles de aprovisionamiento para identificadores de aplicación explícitos, necesitará seguir las [instrucciones anteriores](#testing-on-device-with-free-provisioning) para cada aplicación que quiera instalar.
- El aprovisionamiento de la mayoría de los servicios de aplicación tampoco es posible con el aprovisionamiento gratuito. Esto incluye Apple Pay, Game Center, iCloud, las compras desde la aplicación, las notificaciones de inserción y Wallet. Apple proporciona una lista completa de las capacidades en su guía de [capacidades admitidas (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6). Para aprovisionar su aplicación para su uso con servicios de aplicación, consulte las guías para [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="summary"></a>Resumen

En esta guía se han explorado las ventajas y limitaciones de usar el aprovisionamiento gratuito para instalar aplicaciones en un dispositivo iOS. Se ha ofrecido un tutorial detallado que demuestra cómo usar el aprovisionamiento gratuito para instalar una aplicación Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
- [Aprovisionamiento de servicios de aplicación](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)
