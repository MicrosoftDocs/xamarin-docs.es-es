---
title: Aspectos básicos de la aplicación de Xamarin. Mac
description: En este documento se proporcionan vínculos a guías que describen los distintos conceptos necesarios para entender el desarrollo de aplicaciones de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 12/17/2015
ms.openlocfilehash: 73ec847b697c2d588d0c217bcbf12d4f0b6aa817
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291345"
---
# <a name="xamarinmac-application-fundamentals"></a>Aspectos básicos de la aplicación de Xamarin. Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Expresiones y patrones comunes](~/mac/app-fundamentals/patterns.md)

En las API de Apple que C#se exponen a través de, ciertas expresiones y patrones se vuelven a poner en marcha una y otra vez. Si tiene experiencia con la programación con Xamarin. iOS, puede que le resulte familiar. La documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener una comprensión sólida de ellos le ayudará a tener sentido la documentación que encuentre.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Descripción de las API de Mac](~/mac/app-fundamentals/mac-apis.md)

Para gran parte del tiempo que desarrolla con Xamarin. Mac, puede pensar, leer y escribir C# sin tener que preocuparse por las API de Objective-C subyacentes. Sin embargo, a veces tendrá que leer la documentación de la API de Apple, traducir una respuesta de Stack Overflow a una solución para el problema o comparar con un ejemplo existente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Aplicaciones de consola](~/mac/app-fundamentals/console.md)

También puede compilar aplicaciones de consola "sin periféricos" que tengan acceso a las API de macOS nativas con Xamarin. Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Trabajar con archivos. Xib](~/mac/app-fundamentals/xib.md)

En este artículo se explica cómo trabajar con archivos. Xib creados en la Interface Builder de Xcode para crear y mantener interfaces de usuario para una aplicación de Xamarin. Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[. Storyboard/. Xib menos diseño de la interfaz de usuario](~/mac/app-fundamentals/xibless-ui.md)

En este artículo se explica cómo crear una interfaz de usuario de una aplicación C# de Xamarin. Mac directamente desde el código sin usar los Interface Builder de Xcode con archivos. Storyboard o. Xib.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Trabajar con imágenes](~/mac/app-fundamentals/image.md)

En este artículo se explica cómo trabajar con imágenes e iconos en una aplicación de Xamarin. Mac. Se trata la creación y el mantenimiento de las imágenes necesarias para crear el icono de la aplicación y C# el uso de imágenes tanto en el código como en el Interface Builder de Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Enlace de datos y codificación de valores de clave](~/mac/app-fundamentals/databinding.md)

En este artículo se describe el uso del código clave-valor y la observación del valor de clave para permitir el enlace de datos a los elementos de la interfaz de usuario en la Interface Builder de Xcode. Con esta técnica, se reduce en gran medida la C# cantidad de código que debe escribirse para la aplicación de Xamarin. Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Trabajar con bases de datos](~/mac/app-fundamentals/databases.md)

En este artículo se describe el uso del código clave-valor y la observación del valor de clave para permitir el enlace de datos con acceso directo a las bases de datos de SQLite a elementos de la interfaz de usuario en la Interface Builder de Xcode. También se describe el uso del ORM de SQLite.NET para proporcionar acceso a los datos de SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Trabajar con copiar y pegar](~/mac/app-fundamentals/copy-paste.md)

En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar una copia y pegar en una aplicación de Xamarin. Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados en una aplicación.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Espacio aislado de una aplicación de Xamarin. Mac](~/mac/app-fundamentals/sandboxing.md)

En este artículo se describe el espacio aislado de una aplicación de Xamarin. Mac para su lanzamiento en App Store. Abarca todos los elementos que entran en un espacio aislado: directorios de contenedor, derechos, permisos definidos por el usuario, separación de privilegios y cumplimiento del kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Reproducir sonido con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

En este artículo se muestra cómo usar una clase auxiliar para controlar la reproducción de sonido mediante un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Informes de errores](~/mac/app-fundamentals/troubleshooting.md)

A veces, todos se bloquean mientras se trabaja en un proyecto, ya sea en la imposibilidad de obtener una API para trabajar de la forma deseada o para intentar solucionar un error. Nuestro objetivo en Xamarin es que tenga éxito al escribir las aplicaciones móviles y de escritorio, y que hemos proporcionado algunos recursos para ayudarle.
