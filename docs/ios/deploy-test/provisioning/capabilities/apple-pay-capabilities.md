---
title: Capacidades de Apple Pay en Xamarin.iOS
description: Agregar capacidades a una aplicación a menudo requiere una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las capacidades de Apple Pay.
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 9d0ffbd0da99134dfae3e42fe8baf4111c80935f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763000"
---
# <a name="apple-pay-capabilities-in-xamarinios"></a>Capacidades de Apple Pay en Xamarin.iOS

_Agregar funcionalidades a una aplicación requiere con frecuencia una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las funcionalidades de Apple Pay._

Con Apple Pay, los usuarios pueden pagar bienes físicos con su dispositivo iOS. En esta sección se describe cómo crear todos los componentes necesarios para Apple Pay en el Centro para desarrolladores de Apple.

Al aprovisionar una nueva aplicación mediante el Centro para desarrolladores, hay tres pasos que debe seguir:

1. Cree un identificador de comerciante.
2. Cree un identificador de aplicación con la capacidad de Apple Pay y agregue el identificador de comerciante a este.
3. Genere un certificado para el identificador de comerciante.

En los siguientes pasos se le guiará por el proceso de creación de los elementos anteriores:

<a name="merchantid" />

## <a name="create-merchant-id"></a>Crear un identificador de comerciante

Gracias a los identificadores de comerciante, Apple Pay sabe que puede aceptar pagos; estos se pasan al método `PaymentRequest` de PassKit y se usan en el derecho de Apple Pay:

1. Vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/account/) y, luego, vaya a la sección Certificates, Identifiers & Profiles (Certificados, identificadores y perfiles): 

    ![Selección del identificador de comerciante del Centro para desarrolladores](apple-pay-capabilities-images/image57.png)

2. En **Identifiers** (Identificadores), seleccione **Merchant IDs** (Identificadores de comerciante) y, luego, seleccione **+** para crear un identificador de comerciante:  

3. Rellene el formulario que se muestra a continuación con una descripción y un identificador nuevos. La descripción, que se puede cambiar más tarde, le permite distinguir el identificador. El identificador debe ser único y debe comenzar con la cadena  `merchant`. Apple recomienda que el identificador tenga el formato `merchant.com.[Your-App-Name]`:
   
    ![Detalles del nuevo identificador de comerciante](apple-pay-capabilities-images/image58.png)

4. Confirme los detalles y presione  **Register** (Registrar) para registrar el identificador: 
    
    ![Confirmación del identificador de comerciante](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>Crear un identificador de aplicación con la capacidad de Apple Pay que incluya el identificador de comerciante

1. En el [Centro para desarrolladores](https://developer.apple.com/account/), haga clic en **App IDs** (Identificadores de aplicación), que se encuentra en **Identifiers** (Identificadores): 
    
    ![Selección del identificador de aplicación en el Centro para desarrolladores](apple-pay-capabilities-images/image6.png)

2. Haga clic en el botón **+** para agregar un nuevo identificador de aplicación: 
   
    ![Botón para agregar un nuevo identificador de aplicación](apple-pay-capabilities-images/image27.png)

3. Escriba un nombre para el identificador de aplicación y asígnele un identificador de aplicación explícito:    
   
    ![Pantalla de detalles del identificador de aplicación](apple-pay-capabilities-images/image35.png)

4. En App Services (Servicios de aplicaciones), seleccione Apple Pay:    
  
    ![Apple Pay en los servicios de aplicaciones](apple-pay-capabilities-images/image36.png)

5. Seleccione **Continue** (Continuar) y **Register** (Registrar). Tenga en cuenta que, en la pantalla de confirmación, Apple Pay aparecerá con la opción Configurable seleccionada con un símbolo amarillo: 
   
    ![Pantalla de confirmación de Apple Pay](apple-pay-capabilities-images/image37.png)

6. Vuelva a la lista de identificadores de aplicación y seleccione el que acaba de crear:  
   
    ![Edición del identificador de aplicación](apple-pay-capabilities-images/image38.png)

7. Desplácese hasta el final de esta sección expandida y haga clic en **Edit** (Editar).
8. Desplácese hacia abajo por la lista hasta llegar a Apple Pay y haga clic en el botón **Edit** (Editar):  
    
    ![Edición de los detalles del identificador de aplicación de Apple Pay](apple-pay-capabilities-images/image39.png)

9. Seleccione el identificador de comerciante que usará con este identificador de aplicación y haga clic en **Continue** (Continuar):  
    
    ![Selección del identificador de comerciante que se usará para el identificador de aplicación](apple-pay-capabilities-images/image40.png)

10. Confirme las asignaciones del identificador de comerciante y presione **Assign** (Asignar):  
    
    ![Pantalla de confirmación](apple-pay-capabilities-images/image41.png)

Este Id. de aplicación ya puede usarse para generar, o volver a generar, un nuevo perfil de aprovisionamiento, como se explica en la guía [Trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md). 

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>Crear un certificado para el identificador de comerciante

Apple solicita un certificado para cifrar los datos confidenciales asociados a la transacción. Cada identificador de comerciante creado debe tener su propio certificado. 

Para crear un certificado, siga estos pasos:

1. Seleccione el identificador de comerciante creado anteriormente y presione **Edit** (Editar): 
    
    ![Cuadro de diálogo de edición del identificador de comerciante](apple-pay-capabilities-images/image42.png)

2. En la pantalla iOS Merchant ID Settings (Configuración de identificador de comerciante de iOS), haga clic en **Create Certificate** (Crear certificado): 
   
    ![Creación de un certificado de procesamiento de pago](apple-pay-capabilities-images/image43.png)

3. Responda a la siguiente pregunta: 

    ![Pregunta sobre si los pagos se procesarán exclusivamente en China](apple-pay-capabilities-images/image44.png)

4. En este momento se le pedirá que cree una _solicitud de firma de certificado_: 

    ![Creación de una solicitud de firma de certificado](apple-pay-capabilities-images/image45.png)
    
    > [!IMPORTANT]
    > Si usa un proveedor de pagos para Apple Pay (como JudoPay o Stripe), estos le pueden proporcionar una CSR con el formato correcto para usarla en este punto. Encontrará información sobre cómo solicitarlo en los sitios de [JudoPay](https://www.judopay.com/docs/version-52/apple-pay/getting-started/#create-an-apple-pay-certificate) y de [Stripe](https://stripe.com/docs/apple-pay/apps#csr). Para crear su propia CSR, siga los pasos 5-8. Una vez que tenga su CSR, vaya al paso 9.

5. Abra la aplicación Keychain Access y vaya a **Keychain Access > Certificate Assistant > Request a Certificate from a Certificate Authority** (Keychain Access > Asistente de certificados > Solicitar un certificado de una entidad de certificación): 

     ![Creación de una CSR usando Keychain Access en un equipo Mac](apple-pay-capabilities-images/image46.png)

6. Escriba su dirección de correo electrónico, escriba un nombre para la clave privada, deje vacío el campo CA Email Address (Dirección de correo electrónico de la CA), seleccione la opción **Saved to disk** (Guardado en disco) y seleccione **Let me specify key pair information** (Especificar información de par de claves):

     ![Cuadro de diálogo de información del certificado](apple-pay-capabilities-images/image47.png)

7. Guarde la CSR en una ubicación adecuada: 

     ![Guardado de la CSR en el equipo local](apple-pay-capabilities-images/image48.png)

8. En la pantalla Key Pair Information (Información de par de claves), establezca **Key Size** (Tamaño de clave) en **256 bits** y **Algorithm** (Algoritmo) en **ECC**; luego, haga clic en **Continue** (Continuar):

     ![Cuadro de diálogo para introducir la información del par de claves](apple-pay-capabilities-images/image49.png)

9. En el Centro para desarrolladores, haga clic en **Continue** (Continuar) para cargar la CSR: 

     ![Preparación de la carga de la CSR en el Centro para desarrolladores](apple-pay-capabilities-images/image50.png)

10. Haga clic en **Choose File…** (Elegir archivo...) para seleccionar la CSR y presione **Continue** (Continuar) para cargarla en el portal para desarrolladores: 

     ![Carga de la CSR en el Centro para desarrolladores](apple-pay-capabilities-images/image51.png)

11. Una vez generado el certificado, descárguelo y haga doble clic en él para instalarlo en la cadena de claves.

Para más información sobre el uso de Apple Pay, consulte la siguiente guía:

* [Introduction to Apple Pay](~/ios/platform/apple-pay.md) (Introducción a Apple Pay)

## <a name="next-steps"></a>Pasos siguientes

En la siguiente lista se describen los pasos adicionales que se deben seguir:

* Use el espacio de nombres del marco en su aplicación.
* Agregue los derechos necesarios a la aplicación. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona información sobre los derechos necesarios y sobre cómo agregarlos.
* En la opción  **Firma de lote de iOS** de la aplicación, asegúrese de que  **Derechos personalizados** esté establecido en **Entitlements.plist**. Esta  _no_ es la configuración predeterminada para las compilaciones de depuración y del simulador de iOS.

Si experimenta problemas con servicios de aplicaciones, vea la sección [Solución de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) de la guía principal.
