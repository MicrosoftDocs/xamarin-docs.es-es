---
title: Compilación para el Mac App Store
description: En este documento se describe el proceso de empaquetado de una aplicación Xamarin.Mac para su publicación en el Mac App Store. Se explican también las opciones de firma de código y la compilación.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 129ba01a41f9e5f58802c4d4da65d1662a103adc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770121"
---
# <a name="bundling-for-the-mac-app-store"></a>Compilación para el Mac App Store

En esta sección se describen los aspectos básicos de la compilación de una aplicación para el lanzamiento en Mac App Store mediante Visual Studio para Mac. En función de las características adicionales (como el acceso a iCloud y las notificaciones de inserción), podría requerirse una configuración más exhaustiva que no se trata este artículo.

> [!NOTE]
> Antes de empezar esta sección, el desarrollador debe haber creado un perfil de aprovisionamiento de producción para poder compilar para Mac App Store. Consulte las instrucciones que aparecen anteriormente en este documento sobre cómo crear los perfiles de aprovisionamiento necesarios.

## <a name="code-signing-options"></a>Opciones de firma de código

Cambie la **Configuración** a **Lanzamiento** antes de actualizar la firma de código y las opciones de empaquetado. El programador debe asegurarse de que se usen la **identidad** de la empresa y el perfil de aprovisionamiento que se crearon anteriormente al firmar la aplicación para el lanzamiento en App Store.

 [![Modificar las opciones de firma de código](bundling-images/config02.png "Modificar las opciones de firma de código")](bundling-images/config02-large.png#lightbox)

Asegúrese de que esté activada la opción para crear un paquete de instalador en la configuración de **Mac Build** (Compilación de Mac):

[![Modificar las opciones de compilación](bundling-images/config03.png "Modificar las opciones de compilación")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>Compilar

Antes de compilar, asegúrese de que se ha seleccionado la configuración **Lanzamiento**. Cuando el desarrollador compile la aplicación, se le solicitará que use los dos certificados:

 ![Permitir que la aplicación use el certificado](bundling-images/image62.png "Permitir que la aplicación use el certificado")

 ![Permitir que la aplicación use el certificado](bundling-images/image63.png "Permitir que la aplicación use el certificado")

Una vez que se ha compilado la aplicación, el desarrollador puede hacer clic con el botón derecho en el proyecto y seleccionar **Abrir carpeta contenedora** para buscar el archivo de paquete (en el directorio `bin/x86/AppStore` en el ejemplo que se muestra a continuación).  Este archivo de paquete incluye un instalador para la aplicación que se puede enviar a Apple para que se incluya en Mac App Store.

 ![Seleccionar el paquete de compilación en el buscador](bundling-images/image64.png "Seleccionar el paquete de compilación en el buscador")

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
