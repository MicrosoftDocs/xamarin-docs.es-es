---
title: Resolución de errores de instalación de biblioteca
description: En algunos casos, puede obtener errores durante la instalación de bibliotecas de compatibilidad de Android. En esta guía se proporcionan soluciones alternativas a algunos errores habituales.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "76724304"
---
# <a name="resolving-library-installation-errors"></a>Resolución de errores de instalación de biblioteca

_En algunos casos, puede obtener errores durante la instalación de bibliotecas de compatibilidad de Android. En esta guía se proporcionan soluciones alternativas a algunos errores habituales._

## <a name="overview"></a>Información general

Al compilar un proyecto de aplicación Xamarin.Android, puede obtener errores de compilación cuando Visual Studio o Visual Studio para Mac intenten descargar e instalar bibliotecas de dependencia. Muchos de estos errores se deben a problemas de conectividad de red, archivos dañados o problemas de versiones. En esta guía se describen los errores de instalación de biblioteca de compatibilidad más habituales y se proporcionan los pasos para solucionar estos problemas y obtener de nuevo su compilación de proyectos de aplicación.

## <a name="errors-while-downloading-m2repository"></a>Errores al descargar m2Repository

Es posible que vea errores **m2repository** al hacer referencia a un paquete NuGet de las bibliotecas de compatibilidad de Android o los servicios de Google Play. El mensaje de error es similar al siguiente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este ejemplo es para **android\_m2repository\_r16**, pero es posible que vea este mismo mensaje de error para una versión diferente como, por ejemplo, **android\_m2repository\_r18** o **android\_m2repository\_r25**.

### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperación automática de errores m2repository

A menudo, este problema se puede solucionar eliminando la biblioteca problemática y recompilando según estos pasos:

1. Vaya al directorio de bibliotecas de compatibilidad del equipo:

    - En Windows, las bibliotecas de compatibilidad se encuentran en **C:\\Users\\_username_\\AppData\\Local\\Xamarin**.

    - En Mac OS X, las bibliotecas de compatibilidad se encuentran en **/Users/_username_/.local/share/Xamarin**.

2. Busque la carpeta de versión y biblioteca correspondiente al mensaje de error. Por ejemplo, la carpeta de versión y biblioteca del mensaje de error anterior se encuentra en **Android.Support.v4\\22.2.1**:

    [![Ubicación de la carpeta de ejemplo para la biblioteca de compatibilidad 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Elimine el contenido de la carpeta de versión. Asegúrese de quitar el archivo **.zip**, así como los subdirectorios **content** y **embedded** de esta carpeta. Para obtener el mensaje de error de ejemplo mostrado anteriormente, los archivos y subdirectorios mostrados en esta captura de pantalla (**content**, **embedded** y **android_m2repository_r16.zip**) se van a eliminar:

    [![Contenido de ejemplo de la carpeta de biblioteca de compatibilidad 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Tenga en cuenta que es importante eliminar *todo* el contenido de esta carpeta. Aunque es posible que esta carpeta incluya inicialmente el archivo **android\_m2repository\_r16.zip** "que falta", este archivo puede haberse descargado o haber sufrido daños parcialmente.

4. Recompilar el proyecto &ndash; así hará que el proceso de compilación vuelva a descargar la biblioteca que falta.

En la mayoría de los casos, estos pasos resolverán el error de compilación y le permitirán continuar. Si al eliminar esta biblioteca no se resuelve el error de compilación, debe descargar e instalar manualmente el archivo **android\_m2repository\_r_nn_.zip**, tal como se describe en la sección siguiente.

### <a name="manually-downloading-m2repository"></a>Descarga manual de m2repository

Si ha intentado usar los pasos de recuperación automática anteriores y sigue teniendo errores de compilación, puede descargar manualmente el archivo **android\_m2repository\_r_nn_.zip** (mediante un explorador web) e instalarlo según los pasos siguientes. Este procedimiento también es útil si no tiene acceso a Internet en su equipo de desarrollo, pero puede descargar el archivo con otro equipo.

1. Descargue el archivo **android\_m2repository\_r_nn_.zip** que corresponde al mensaje de error &ndash; se proporcionan vínculos en la lista siguiente (junto con el hash MD5 correspondiente de cada dirección URL del vínculo):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Si el archivo **m2repository** no se muestra en esta tabla, puede crear la dirección URL de descarga anteponiendo `https://dl-ssl.google.com/android/repository/` al nombre del archivo **m2repository** que se va a descargar. Por ejemplo, use **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** para descargar **android\_ m2repository\_ r10.zip**.

2. Cambie el nombre del archivo por el hash MD5 correspondiente de la dirección URL de descarga como se muestra en la tabla anterior. Por ejemplo, si ha descargado **android\_m2repository\_r25.zip**, cambie su nombre por **0B3F1796C97C707339FB13AE8507AF50.zip**. Si el hash MD5 de la dirección URL de descarga no se muestra en la tabla, puede usar un [generador de MD5 en línea](http://www.webconfs.com/online-md5-generator.php) para convertir la dirección URL en una cadena de hash MD5.

3. Copie el archivo en la carpeta **zips** de Xamarin:

    - En Windows, esta carpeta se encuentra en **C:\\Users\\***username***\\AppData\\Local\\Xamarin\\zips**.

    - En Mac OS X, esta carpeta se encuentra en **/Users/***username***/.local/share/Xamarin/zips**.

    Por ejemplo, en la siguiente captura de pantalla se muestra el resultado cuando **android\_m2repository\_r16.zip** se descarga y su nombre se cambia por el hash MD5 de su dirección URL de descarga en Windows:

    [![Ejemplo del repositorio r16.zip cuyo nombre se cambia por 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Si este procedimiento no resuelve el error de compilación, debe descargar manualmente el archivo **android\_m2repository\_r_nn_.zip**, descomprimirlo e instalar su contenido, tal como se describe en la sección siguiente.

### <a name="manually-downloading-and-installing-m2repository-files"></a>Descarga e instalación manuales de archivos m2repository

El proceso totalmente manual para recuperarse de errores **m2repository** implica descargar el archivo **android\_m2repository\_r_nn_.zip** (mediante un explorador web), descomprimirlo y copiar su contenido en el directorio de bibliotecas de compatibilidad del equipo. En el ejemplo siguiente, nos recuperaremos de este mensaje de error:

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Siga estos pasos para descargar **m2repository** e instale su contenido:

1. Elimine el contenido de la carpeta de biblioteca correspondiente al mensaje de error. Por ejemplo, en el mensaje de error anterior eliminaría el contenido de **C:\\Users\\***username***\\AppData\\Local\\Xamarin\\Android.Support.v4\\23.1.1.0**.
    Tal como se describió anteriormente, debe eliminar todo el contenido de este directorio:

    [![Eliminación de las carpetas content, embedded y android_m2repository de la carpeta 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Descargue el archivo **android\_m2repository\_r_nn_.zip** de Google correspondiente al mensaje de error (consulte la tabla en la sección anterior para ver los vínculos).

3. Extraiga este archivo **.zip** en cualquier ubicación (como el escritorio). De este modo debe crearse un directorio correspondiente al nombre del archivo **.zip**. En este directorio, debe encontrar un subdirectorio llamado **m2repository**:

    [![Carpeta m2repository encontrada en el archivo zip extraído](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. En el directorio de bibliotecas con versiones que purgó en el paso 1, vuelva a crear los subdirectorios **content** y **embedded**. Por ejemplo, en la siguiente captura de pantalla se muestran los subdirectorios **content** y **embedded** que se crean en la carpeta **23.1.1.0** para **android\_m2repository\_r25.zip**:

    [![Creación de las carpetas content y embedded en la carpeta 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copie **m2repository** desde el archivo **.zip** extraído en el directorio **content** que creó en el paso anterior:

    [![Captura de pantalla de m2repository copiada en la carpeta 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. En el directorio de archivos **.zip** extraídos, vaya a **m2repository\\com\\android\\support\\support-v4** y abra la carpeta correspondiente al número de versión creado anteriormente (en este ejemplo, **23.1.1**):

    [![Listado de ejemplo de archivos incluidos en la carpeta support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copie todos los archivos de esta carpeta en el directorio **embedded** creado en el paso 4:

    [![Ejemplo de archivos copiados en la carpeta 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Compruebe que se copian todos los archivos. El directorio **embedded** ahora debe incluir archivos como **.jar**, **.aar** y **.pom**.

9. Descomprima el contenido de cualquier archivo **.aar** extraído en el directorio **embedded**. En Windows, anexe una extensión **.zip** al archivo **.aar**, ábralo y copie el contenido en el directorio **embedded**.
    En macOS, descomprima el archivo **.aar** mediante el comando **unzip** en el terminal (por ejemplo, **unzip file.aar**).

En este momento, ha instalado manualmente los componentes que faltan y su proyecto debe compilarse sin errores. En caso contrario, compruebe que ha descargado la versión de archivo **m2repository** **.zip** que se corresponde exactamente con la versión del mensaje de error. Asimismo, compruebe que ha instalado su contenido en las ubicaciones correctas, tal como se describe en los pasos anteriores.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo recuperarse de los errores habituales que se pueden producir durante la descarga e instalación automáticas de las bibliotecas de dependencia. Se ha descrito cómo eliminar la biblioteca problemática y recompilar el proyecto como una forma de volver a descargar y volver a instalar la biblioteca.
Se ha descrito cómo descargar la biblioteca e instalarla en la carpeta **zips**. También se ha descrito un procedimiento con mayor implicación para descargar e instalar manualmente los archivos necesarios como una forma de solucionar los problemas que no pueden resolverse a través de medios automáticos.
