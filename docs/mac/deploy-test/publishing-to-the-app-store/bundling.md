---
title: Compilación para el Mac App Store
description: En este documento se describe el proceso de empaquetado de una aplicación Xamarin.Mac para su publicación en el Mac App Store. Se explican también las opciones de firma de código y la compilación.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f4d38bb66a34257c1e0a27c5fbbfe16f59743e83
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725511"
---
# <a name="bundling-for-the-mac-app-store"></a>Compilación para el Mac App Store

En esta sección se describen los aspectos básicos de la compilación de una aplicación para el lanzamiento en Mac App Store mediante Visual Studio para Mac. En función de las características adicionales (como el acceso a iCloud y las notificaciones de inserción), podría requerirse una configuración más exhaustiva que no se trata este artículo.

> [!NOTE]
> Antes de empezar esta sección, el desarrollador debe haber creado un perfil de aprovisionamiento de producción para poder compilar para Mac App Store. Consulte las [instrucciones del perfil](profiles.md) para crear los perfiles de aprovisionamiento necesarios.

## <a name="code-signing-options"></a>Opciones de firma de código

Cambie la **Configuración** a **Lanzamiento** antes de actualizar la firma de código y las opciones de empaquetado. El programador debe asegurarse de que se usen la **identidad** de la empresa y el perfil de aprovisionamiento que se crearon anteriormente al firmar la aplicación para el lanzamiento en App Store.

[![Edición de las opciones de firma de código](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Asegúrese de que esté activada la opción para crear un paquete de instalador en la configuración de **Mac Build** (Compilación de Mac):

[![Edición de las opciones de compilación](bundling-images/build.png "Edición de las opciones de compilación")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Compilar

Antes de compilar, asegúrese de que se ha seleccionado la configuración **Lanzamiento**. Cuando el desarrollador compile la aplicación, se solicitarán _dos veces_ (usar el certificado de la aplicación y del instalador):

![Si permite que la aplicación use el certificado, aparecerá dos veces.](bundling-images/perms02.png)

Una vez que se ha compilado la aplicación, el desarrollador puede hacer clic con el botón derecho en el proyecto y seleccionar **Mostrar en Finder** para buscar el archivo de paquete (en el directorio `bin/Release/AppStore` en el ejemplo que se muestra a continuación).  Este archivo de paquete incluye un instalador para la aplicación que se puede enviar a Apple para que se incluya en Mac App Store.

> [!div class="mx-imgBorder"]
> ![Selección del paquete de compilación en Finder](bundling-images/path.png)

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/developer-id/)
