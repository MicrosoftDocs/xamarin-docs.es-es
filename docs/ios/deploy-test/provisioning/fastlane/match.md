---
title: 'fastlane para iOS: match'
description: En este documento se describe el comando match de fastlane, que se usa para la creación y el mantenimiento de certificados de firma de código y perfiles de aprovisionamiento para el desarrollo de iOS.
ms.prod: xamarin
ms.assetid: C4A2A67E-0643-4CED-B1A9-79D65054F3CA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 92631fa50dc4826e70df4333bb55f7f69937d053
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526546"
---
# <a name="fastlane-for-ios---match"></a>fastlane para iOS: match

Tradicionalmente, cada miembro de un equipo de desarrollo realiza el aprovisionamiento de dispositivos mediante Xcode o en el portal para desarrolladores de Apple. Consta de varios pasos:

- Solicitar un certificado de desarrollo
- Agregar un dispositivo al portal
- Creación de un identificador de aplicación
- Crear un perfil de aprovisionamiento
- Descargar perfiles y certificados

En la guía [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) (Aprovisionamiento de dispositivos) encontrará más información sobre los pasos necesarios para configurar un dispositivo para el desarrollo, ya sea manualmente o mediante Xcode.

En esta guía se describe el uso de las herramientas de Fastlane como alternativa a Xcode.

## <a name="installation"></a>Instalación

Para más información sobre la instalación de fastlane, consulte la introducción de la guía de [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatismatch" />

## <a name="what-is-match"></a>¿Qué es match?

match se encarga de la creación y el mantenimiento de los certificados de firma de código y los perfiles de aprovisionamiento, y permite que un equipo de desarrollo de iOS comparta una identidad de firma de código entre todos los desarrolladores.

Al implementar una aplicación en la Tienda de aplicaciones, realizar pruebas beta o instalar la aplicación en un dispositivo, cada miembro de un equipo de desarrollo tiene su propia identidad de firma. Esto puede dar lugar a conflictos de identidades y perfiles, y obliga a crear, girar y administrar manualmente los perfiles y los identificadores de aplicaciones.

En su lugar, Match crea y mantiene automáticamente todos los certificados y los perfiles y los almacena en un repositorio Git privado. Esto permite que todos los desarrolladores de un equipo tengan acceso a estas credenciales y puedan usarlas. A su vez, esto proporciona una mayor seguridad para los certificados: además de encontrarse en un repositorio Git privado, también están cifrados con una [frase de contraseña](#passphrase). El almacenamiento de los artefactos de firma de código en un repositorio permite que los administradores y los agentes de equipo actualicen y giren los certificados según sea necesario, con lo que se reduce el tiempo que se invierte en distribuir los certificados nuevos a cada desarrollador.

> [!IMPORTANT]
> Actualmente match no admite los perfiles empresariales internos.

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>Inicializar el proyecto con match

Si es el administrador del equipo, cree un repositorio Git privado a través de github.com o bitbucket.com. Asegúrese de que agrega todos los miembros del equipo como colaboradores en el repositorio.

Mediante el terminal, cambie el directorio al directorio del proyecto y ejecute lo siguiente:

```
fastlane match init
```

Cuando se le solicite, escriba la dirección URL del repositorio Git:

 [![](match-images/fastlane-image7.png "Escribir la dirección URL del repositorio GIT")](match-images/fastlane-image7.png#lightbox)

Para encontrar y copiar la dirección URL, haga clic en el botón **Clone or Download** (Clonar o descargar) situado en github.com, como se muestra a continuación:

[![](match-images/fastlane-image6.png "Dirección URL del botón Clonar o Descargar de github.com")](match-images/fastlane-image6.png#lightbox)

Cuando se inicializa el proyecto, se crea un matchfile, es decir, un archivo de texto que puede modificarse para pasar variables de entorno a la herramienta match. Aquí se muestra un ejemplo de matchfile:

[![](match-images/fastlane-image8.png "Ejemplo de matchfile")](match-images/fastlane-image8.png#lightbox)

<a name="running" />

## <a name="running-match"></a>Ejecutar match

> [!NOTE]
> fastlane recomienda que antes de ejecutar match por primera vez debe considerarse la posibilidad de desactivar los perfiles y los certificados existentes mediante el [comando nuke de match](#using).

En función del entorno que requiera, puede usar cualquiera de los comandos siguientes para crear un certificado y un perfil de aprovisionamiento y almacenarlos en el nuevo repositorio Git:

```
fastlane match appstore

fastlane match adhoc

fastlane match development
```

Además de crear certificados y perfiles, mediante el uso de estos comandos se puede agregar (o actualizar, si ya existen) los elementos siguientes en el repositorio Git:

- Carpeta de certificados
- Carpeta de perfiles
- Un archivo Léame con instrucciones básicas
- Una versión de Match

[![](match-images/fastlane-image9.png "Estructura del proyecto en el repositorio GIT")](match-images/fastlane-image9.png#lightbox)

Los perfiles de aprovisionamiento se instalan en `~/Library/MobileDevice/Provisioning Profiles`. Los certificados y las claves privadas se instalan directamente en la cadena de claves.

<a name="using" />

### <a name="using-the-nuke-command"></a>Usar el comando `nuke`

Si sus certificados no están bien organizados, puede usar `nuke` para revocar los certificados y los perfiles de cada entorno mediante los comandos siguientes:

```
fastlane match nuke
```

Para revocar todos los certificados y los perfiles de aprovisionamiento de un entorno concreto:

```
fastlane match nuke development
```

 o

```
fastlane match nuke distribution
```

fastlane confirmará los archivos que se van a quitar antes de eliminar nada.

<a name="passphrase" />

### <a name="passphrase"></a>Frase de contraseña

Cuando ejecute `match` por primera vez, le pedirá que establezca una frase de contraseña para el repositorio Git. Asegúrese de que recuerda la contraseña, ya que la necesitará cuando ejecute Match en otra máquina. Se trata de una capa de seguridad adicional: cada uno de los archivos se cifrará con OpenSSL. Después, cada vez que ejecute `match` en una máquina nueva, se le solicitará esta frase de contraseña, que se agregará a la cadena de claves local cuando la escriba por primera vez.

Para establecer la frase de contraseña de modo que descifre los perfiles mediante una variable de entorno, use `MATCH_PASSWORD`.

<a name="options" />

## <a name="additional-options"></a>Opciones adicionales

Las siguientes opciones se pueden usar para dar soporte técnico adicional cuando se usa Match:

- Use la marca `-–help` para obtener una lista de todos los comandos disponibles:

    ```
    fastlane match cert --help
    ```

- Use la marca `-–verbose` para aumentar el nivel de detalle de la salida:

    ```
    fastlane match --development --verbose
    ```

- Use la marca `--force_for_new_devices` para obligar a que los perfiles de aprovisionamiento se renueven si ha cambiado el número de dispositivos en el portal de desarrolladores:

    ```
    fastlane match development --force_for_new_devices
    ```

## <a name="related-links"></a>Vínculos relacionados

- [fastlane: match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
