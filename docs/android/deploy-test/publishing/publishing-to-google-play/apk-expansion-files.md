---
title: Archivos de expansión de APK
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 712322435614966348fc5c10cabf724870c307e4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021294"
---
# <a name="apk-expansion-files"></a>Archivos de expansión de APK

Algunas aplicaciones, como es el caso de algunos juegos, por ejemplo, requieren más recursos y activos de los que se pueden proporcionar en el límite del tamaño máximo de la aplicación Android que impone Google Play. Este límite depende de la versión de Android para la que esté destinado el APK:

- 100 MB para APK destinadas a Android 4.0 o versiones posteriores (nivel de API 14 o superior).
- 50 MB para APK destinadas a Android 3.2 o versiones anteriores (nivel de API 13 o superior).

Para superar esta limitación, Google Play hospeda y distribuye dos *archivos de expansión* que acompañan a un APK, con lo cual una aplicación puede superar indirectamente este límite. 

En la mayoría de los dispositivos, cuando se instala una aplicación, los archivos de expansión se descargan junto con el APK y se guardan en la ubicación de almacenamiento compartido (la tarjeta SD o la partición que se puede montar en un USB) del dispositivo. En algunos dispositivos antiguos, los archivos de expansión no pueden instalarse automáticamente con el APK. En este caso, es necesario que la aplicación contenga el código que permite descargar los archivos de expansión cuando el usuario ejecute por primera vez las aplicaciones.

Los archivos de expansión se tratan como *blobs binarios opacos (obb)* que pueden tener un tamaño de hasta 2 GB. Android no realiza ningún procesamiento especial en estos archivos tras su descarga, y estos pueden estar en cualquier formato que sea adecuado para la aplicación. Desde el punto de vista conceptual, el enfoque recomendado para los archivos de expansión es el siguiente:

- **Expansión principal**: es el archivo de expansión principal para recursos y activos que no estarán dentro del límite de tamaño del APK. El archivo de expansión principal debe contener los activos principales que necesita una aplicación y rara vez debe actualizarse.
- **Expansión de revisión**: está diseñado para pequeñas actualizaciones del archivo de expansión principal. Este archivo se puede actualizar. La aplicación se encarga de realizar las revisiones o actualizaciones necesarias desde este archivo.

Los archivos de expansión deben cargarse al mismo tiempo que el APK.
Google Play no permite que se descargue un archivo de expansión en un APK existente ni para APK existentes que deban actualizarse. Si es necesario actualizar un archivo de expansión, se debe cargar un APK nuevo con `versionCode` actualizado.

## <a name="expansion-file-storage"></a>Almacenamiento de archivos de expansión

Cuando se descargan los archivos en un dispositivo, se almacenan en **_shared-store_/Android/obb/_package-name_** :

- **_shared-store_** : es el directorio especificado por `Android.OS.Environment.ExternalStorageDirectory`.
- **_package-name_** : es el nombre del paquete de estilo Java de la aplicación.

Una vez descargados, los archivos de expansión no se deben mover, modificar, cambiar de nombre ni eliminar de su ubicación en el dispositivo. Si no se respeta esta directriz, los archivos de expansión se descargarán de nuevo y se eliminarán los archivos antiguos. Además, el directorio del archivo de expansión solo debe contener los archivos del paquete de expansión.

Los archivos de expansión no ofrecen seguridad ni protección alguna de su contenido, es decir, otras aplicaciones u otros usuarios pueden tener acceso a los archivos guardados en el almacenamiento compartido.

Si es necesario desempaquetar un archivo de expansión, se deben almacenar archivos desempaquetados en un directorio independiente, como por ejemplo uno en `Android.OS.Environment.ExternalStorageDirectory`.

Como alternativa a la extracción de archivos desde un archivo de expansión, se pueden leer los activos o recursos directamente desde el archivo de expansión. El archivo de expansión tan solo es un archivo .zip que se puede usar con un `ContentProvider` adecuado. [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) contiene un ensamblado, [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip), que incluye un `ContentProvider` que permitirá a algunos archivos multimedia tener acceso directo a los archivos. Si los archivos multimedia se empaquetan en un archivo .zip, las llamadas de reproducción multimedia pueden utilizar directamente los archivos del archivo .zip sin tener que desempaquetarlo. Al agregar los archivos multimedia al .zip, estos no deben comprimirse. 

### <a name="filename-format"></a>Formato de nombre de archivo

Tras descargar los archivos de expansión, Google Play utiliza el siguiente esquema para asignar un nombre a la expansión:

```
[main|patch].<expansion-version>.<package-name>.obb
```

Los tres componentes de este esquema son:

- `main` o `patch`: especifica si este es el archivo de expansión principal o de revisiones. Solo puede ser uno de los dos.
- `<expansion-version>`: entero que coincide con el elemento `versionCode` del APK con el que se ha asociado el archivo en primer lugar.
- `<package-name>`: nombre del paquete de estilo Java de la aplicación.

Por ejemplo, si la versión del APK es la 21 y el nombre del paquete es `mono.samples.helloworld`, el archivo de expansión principal se llamará **main.21.mono.samples.helloworld**.

## <a name="download-process"></a>Proceso de descarga

Cuando se instala una aplicación desde Google Play, los archivos de expansión deben descargarse y guardarse junto con el APK. En determinadas situaciones es posible que esto no sea así o bien que se eliminen los archivos de expansión. Para resolver esta incidencia, una aplicación debe comprobar si existen los archivos de expansión y, luego, descargarlos, si es necesario. El siguiente diagrama muestra el flujo de trabajo recomendado para este proceso:

[![Diagrama de flujo de expansión del APK](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

Cuando se inicia una aplicación, debe comprobar si existen los archivos de expansión adecuados en el dispositivo actual. En caso contrario, la aplicación debe realizar una solicitud desde la [Administración de licencias de aplicaciones](https://developer.android.com/google/play/licensing/index.html) de Google Play. Esta comprobación se realiza mediante la *License Verification Library (LVL)* (Biblioteca de comprobación de licencias (BCL)) y debe efectuarse en el caso de las aplicaciones gratuitas y las que necesiten licencia. La BCL la utilizan principalmente aplicaciones de pago para aplicar restricciones de licencias. Sin embargo, Google ha ampliado la BCL de tal manera que pueda utilizarse también con las bibliotecas de expansión. Las aplicaciones gratuitas tienen que realizar la comprobación de la BCL, pero pueden ignorar las restricciones de licencias. La solicitud de la BCL se encarga de proporcionar la siguiente información sobre los archivos de expansión que necesita la aplicación: 

- **Tamaño del archivo**: los tamaños de archivo de los archivos de expansión se utilizan como parte de la comprobación que determina si ya se han descargado o no los archivos de expansión correctos.
- **Nombres de archivo**: nombre de archivo, en el dispositivo actual, en que deben guardarse los paquetes de expansión.
- **URL de descarga**: dirección URL que se debe utilizar para descargar los paquetes de expansión. Es única para cada descarga y expirará poco después de haberse proporcionado.

Una vez realizada la comprobación de la BCL, la aplicación debe descargar los archivos de expansión, teniendo en cuenta los puntos siguientes como parte de la descarga:

- Es posible que el dispositivo no tenga espacio suficiente para almacenar los archivos de expansión.
- Si no está disponible la Wi-Fi, el usuario debe poder pausar o cancelar la descarga para evitar cargos de datos no deseados.
- Los archivos de expansión se descargan en segundo plano para evitar el bloqueo de las interacciones del usuario.
- Mientras la descarga se realiza en segundo plano, se debe mostrar un indicador de progreso.
- Los errores que se produzcan durante la descarga se procesan correctamente y esta se puede recuperar.

## <a name="architectural-overview"></a>Información general sobre la arquitectura

Cuando se inicia la actividad principal, comprueba si se descargan los archivos de expansión. Si se descargan los archivos, debe comprobarse su validez.

Si no se han descargado los archivos de expansión o si los archivos actuales no son válidos, se deben descargar nuevos archivos de expansión. Se crea un servicio entrelazado como parte de la aplicación. Cuando se inicia la actividad principal de la aplicación, utiliza el servicio entrelazado para realizar una comprobación en los servicios de administración de licencias de Google con el fin de averiguar los nombres de los archivos de expansión y la dirección URL de los archivos que se deben descargar. A continuación, el servicio entrelazado descarga los archivos en un subproceso en segundo plano.

Para simplificar la tarea de integrar los archivos de expansión en una aplicación, Google ha creado varias bibliotecas en Java. Las bibliotecas en cuestión son las siguientes:

- **Biblioteca descargadora**: biblioteca que simplifica la integración de los archivos de expansión en una aplicación. La biblioteca descarga los archivos de expansión en un servicio en segundo plano, muestra las notificaciones de usuario, trata los problemas de conectividad de red, reanuda las descargas, etc.
- **Biblioteca de comprobación de licencias (BCL)** : biblioteca para realizar y procesar las llamadas a los servicios de administración de licencias de aplicaciones. También puede utilizarse para realizar comprobaciones de licencias y, así, ver si la aplicación está autorizada para su uso en el dispositivo.
- **Biblioteca del archivo .zip de expansión de APK (opcional)** : si los archivos de expansión están en un archivo ZIP, esta biblioteca actúa como un proveedor de contenido y permite que una aplicación lea los recursos y activos directamente desde el archivo ZIP sin tener que expandirlo.

Estas bibliotecas se han trasladado a C# y están disponibles bajo la licencia de Apache 2.0. Para integrar rápidamente los archivos de expansión en una aplicación existente, estas bibliotecas se pueden agregar a una aplicación existente de Xamarin.Android. El código está disponible en [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary), en GitHub.
