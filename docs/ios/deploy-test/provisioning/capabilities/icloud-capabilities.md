---
title: Capacidades de iCloud en Xamarin.iOS
description: Agregar capacidades a una aplicación a menudo requiere una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las capacidades de iCloud.
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: ef36e79254a6d07ae6d23de7e86f6a43b2140b09
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065518"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Capacidades de iCloud en Xamarin.iOS

_Agregar funcionalidades a una aplicación requiere con frecuencia una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las funcionalidades de iCloud._

iCloud proporciona a los usuarios de iOS una forma sencilla y cómoda para almacenar su contenido y compartirlo entre dispositivos. Los desarrolladores disponen de cuatro formas de usar iCloud para proporcionar almacenamiento a sus usuarios: el almacenamiento de pares clave-valor, el almacenamiento de UIDocument, CoreData y el uso directo de CloudKit para proporcionar almacenamiento de archivos y directorios individuales. Para más información, consulte la guía [Introduction to iCloud](~/ios/data-cloud/introduction-to-icloud.md) (Introducción a iCloud).

Agregar la capacidad de iCloud a una aplicación es un poco más difícil que otros servicios de aplicación debido a los _contenedores_. Los contenedores se usan en iCloud para almacenar la información de una aplicación y permiten que toda la información incluida en una cuenta de iCloud se pueda segregar (al igual que el espacio aislado en un dispositivo iOS de un usuario). Para más información sobre los contenedores, consulte la guía [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introducción a CloudKit).

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="icloud-developer-center" />

## <a name="developer-center"></a>Centro para desarrolladores

Al aprovisionar una nueva aplicación mediante el Centro para desarrolladores, hay dos pasos que debe seguir:

1. Cree un contenedor.
2. Cree un identificador de aplicación con la capacidad de iCloud y agregue el contenedor a este.
3. Cree un perfil de aprovisionamiento que incluya este identificador de aplicación.

En los siguientes pasos se le guiará a través de este procedimiento:

1. Vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/account/) y, luego, vaya a la sección Certificates, Identifiers & Profiles (Certificados, identificadores y perfiles): 
    
     ![Página principal del Centro para desarrolladores de Apple](icloud-capabilities-images/image22.png)

2. En **Identifiers** (Identificadores), seleccione **iCloud Containers** (Contenedores de iCloud) y, luego, seleccione **+** para crear un contenedor:  
    
    ![Pantalla de contenedores de iCloud](icloud-capabilities-images/image23.png)

3. Escriba una **descripción** y un **identificador** único para el contenedor de iCloud: 
    
    ![Pantalla de registro del contenedor de iCloud](icloud-capabilities-images/image24.png)

4. Presione **Continue** (Continuar), asegúrese de que la información es correcta y presione **Register** (Registrar) para crear el contenedor de iCloud:  
    
    ![Pantalla de registro del contenedor de iCloud](icloud-capabilities-images/image25.png)

Para crear un nuevo identificador de aplicación y agregar un contenedor a este, haga lo siguiente:

1. En el [Centro para desarrolladores](https://developer.apple.com/account/), haga clic en **App IDs** (Identificadores de aplicación), que se encuentra en **Identifiers** (Identificadores): 
    
    ![Sección de identificadores del Centro para desarrolladores](icloud-capabilities-images/image26.png)

2. Haga clic en el botón **+** para agregar un nuevo identificador de aplicación: 
    
    ![Botón para agregar un nuevo identificador de aplicación](icloud-capabilities-images/image27.png)

3. Escriba un **nombre** para el identificador de aplicación y asígnele un **identificador de aplicación explícito**:
    
    ![Especificación de la información del nuevo identificador de aplicación](icloud-capabilities-images/image28.png)

4. En **App Services** (Servicios de aplicaciones), seleccione **iCloud** y, luego, seleccione **Include CloudKit support** (Incluir soporte de CloudKit):
    
    ![Selección de los servicios de aplicaciones de iCloud](icloud-capabilities-images/image29.png)

5. Seleccione **Continue** (Continuar) y **Register** (Registrar). Tenga en cuenta que, en la pantalla de confirmación, iCloud aparecerá con la opción Configurable seleccionada con un símbolo amarillo:   
    
    ![Pantalla de confirmación](icloud-capabilities-images/image30.png)

6. Vuelva a la lista de identificadores de aplicación y seleccione el que acaba de crear: 
    
    ![Pantalla para seleccionar el identificador de aplicación](icloud-capabilities-images/image31.png)

7. Desplácese hasta el final de esta sección expandida y haga clic en **Edit** (Editar):
    
    ![Edición del identificador de aplicación](icloud-capabilities-images/image32.png)

8. Desplácese hacia abajo por la lista hasta llegar a iCloud y haga clic en el botón **Edit** (Editar):  
    
    ![Edición del identificador de aplicación de iCloud](icloud-capabilities-images/image33.png)

9. Seleccione el contenedor que usará con este identificador de aplicación:  
    
    ![Pantalla para seleccionar el contenedor](icloud-capabilities-images/image34.png)

10. Confirme las asignaciones de contenedores y presione **Assign** (Asignar).
 
Este Id. de aplicación ya puede usarse para generar, o volver a generar, un nuevo perfil de aprovisionamiento, como se explica en la guía [Trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md). 

Para más información sobre el uso de iCloud, consulte las siguientes guías:

* [Introduction to iCloud](~/ios/data-cloud/introduction-to-icloud.md) (Introducción a iCloud)
* [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introducción a CloudKit)
* [Introduction to Document Picker](~/ios/platform/document-picker.md) (Introducción al selector de documentos)

## <a name="next-steps"></a>Pasos siguientes
 
En la siguiente lista se describen los pasos adicionales que se deben seguir:

* Use el espacio de nombres del marco en su aplicación.
* Agregue los derechos necesarios a la aplicación. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona información sobre los derechos necesarios y sobre cómo agregarlos.
* En la opción  **Firma de lote de iOS** de la aplicación, asegúrese de que  **Derechos personalizados** esté establecido en **Entitlements.plist**. Esta  _no_ es la configuración predeterminada para las compilaciones de depuración y del simulador de iOS.

Si experimenta problemas con servicios de aplicaciones, vea la sección [Solución de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) de la guía principal.
