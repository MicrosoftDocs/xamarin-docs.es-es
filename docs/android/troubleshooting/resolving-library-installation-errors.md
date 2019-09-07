---
title: Resolución de errores de instalación de biblioteca
description: En algunos casos, es posible que obtenga errores al instalar las bibliotecas de compatibilidad de Android. En esta guía se proporcionan soluciones alternativas a algunos errores comunes.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/14/2018
ms.openlocfilehash: 8107a26e090aa920d71146d5f2af8b8365697d6b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757214"
---
# <a name="resolving-library-installation-errors"></a>Resolución de errores de instalación de biblioteca

_En algunos casos, es posible que obtenga errores al instalar las bibliotecas de compatibilidad de Android. En esta guía se proporcionan soluciones alternativas a algunos errores comunes._

## <a name="overview"></a>Información general

Al compilar un proyecto de aplicación de Xamarin. Android, es posible que obtenga errores de compilación cuando Visual Studio o Visual Studio para Mac intenten descargar e instalar las bibliotecas de dependencias. Muchos de estos errores se deben a problemas de conectividad de red, archivos dañados o problemas de control de versiones. En esta guía se describen los errores de instalación de la biblioteca de soporte técnico más comunes y se proporcionan los pasos para solucionar estos problemas y volver a compilar el proyecto de la aplicación. 

## <a name="errors-while-downloading-m2repository"></a>Errores al descargar m2Repository

Es posible que vea errores de **m2repository** al hacer referencia a un paquete NuGet de las bibliotecas de compatibilidad de Android o los servicios de Google Play. El mensaje de error es similar al siguiente:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Este ejemplo es para **Android\_m2repository\_R16**, pero puede ver el mismo mensaje de error para una versión diferente, como **Android\_m2repository\_R18** o **Android\_ . m2repository\_R25**. 

### <a name="automatic-recovery-from-m2repository-errors"></a>Recuperación automática de errores de m2repository 

A menudo, este problema se puede solucionar eliminando la biblioteca problemática y volviendo a generar según estos pasos: 

1. Navegue al directorio de la biblioteca de soporte técnico del equipo:

    - En Windows, las bibliotecas de compatibilidad se encuentran en **C\\:\\usuarios\\nombre\\de_usuario_\\AppData local Xamarin**. 

    - En Mac OS X, las bibliotecas de soporte se encuentran en **/users/_username_/.local/share/Xamarin**. 

2. Busque la biblioteca y la carpeta de versión correspondientes al mensaje de error. Por ejemplo, la biblioteca y la carpeta de la versión del mensaje de error anterior se encuentran en **Android.\\support. V4 22.2.1**:

    [![Ubicación de carpeta de ejemplo para la biblioteca de compatibilidad de 22.2.1](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Elimine el contenido de la carpeta de la versión. Asegúrese de quitar el archivo **. zip** , así como el **contenido** y los subdirectorios **incrustados** dentro de esta carpeta. Para el mensaje de error de ejemplo mostrado anteriormente, se eliminarán los archivos y subdirectorios que se muestran en esta captura de pantalla (**contenido**, **incrustado**y **android_m2repository_r16. zip**):

    [![Contenido de ejemplo de la carpeta de biblioteca de compatibilidad con 22.2.1](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Tenga en cuenta que es importante eliminar *todo* el contenido de esta carpeta. Aunque esta carpeta puede contener inicialmente el archivo "Missing **"\_de\_Android m2repository R16. zip** , es posible que este archivo se haya descargado o dañado parcialmente.

4. Volver a generar &ndash; el proyecto con esto hará que el proceso de compilación vuelva a descargar la biblioteca que falta.

En la mayoría de los casos, estos pasos resolverán el error de compilación y podrán continuar. Si al eliminar esta biblioteca no se resuelve el error de compilación, debe descargar e instalar manualmente el **archivo\_Android\_m2repository r_nn_. zip** como se describe en la sección siguiente. 

### <a name="manually-downloading-m2repository"></a>Descarga manual de m2repository

Si ha intentado usar los pasos de recuperación automática anteriores y sigue teniendo errores de compilación, puede descargar manualmente el **archivo\_Android\_m2repository r_nn_. zip** (mediante un explorador Web) e instalarlo de acuerdo con los pasos siguientes. . Este procedimiento también es útil si no tiene acceso a Internet en el equipo de desarrollo, pero puede descargar el archivo con un equipo diferente. 

1. Descargue el **archivo\_Android\_m2repository r_nn_. zip** que corresponde a los vínculos de &ndash; mensajes de error que se proporcionan en la lista siguiente (junto con el hash MD5 correspondiente de la dirección URL de cada vínculo):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [F16A3455987DBAE5783F058F19F7FCDF\_de\_Android m2repository R32. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash;

    - [99A8907CE2324316E754A95E4C2D786E\_de\_Android m2repository R31. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash;

    - [05AD180B8BDC7C21D6BCB94DDE7F2C8F\_de\_Android m2repository R30. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash;

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [17BE247580748F1EDB72E9F374AA0223\_de\_Android m2repository R28. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash;

    - [C9FD4FCD69D7D12B1D9DF076B7BE4E1C\_de\_Android m2repository R27. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash;

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [8E3C9EC713781EDFE1EFBC5974136BEA\_de\_Android m2repository R24. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash;

    - [D5BB66B3640FD9B9C6362C9DB5AB0FE7\_de\_Android m2repository R23. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash;

    - [96659D653BDE0FAEDB818170891F2BB0\_de\_Android m2repository R22. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash;

    - [Android\_m2repository\_R21. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [650E58DF02DB1A832386FA4A2DE46B1A\_de\_Android m2repository R20. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash;

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [25947AD38DCB4865ABEB61522FAFDA0E\_de\_Android m2repository R18. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash;

    - [49054774F44AE5F35A6BA9D3C117EFD8\_de\_Android m2repository R17. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash;

    - [0595E577D19D31708195A83087881EE6\_de\_Android m2repository R16. zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash;

    Si el archivo **m2repository** no se muestra en esta tabla, puede crear la dirección URL de descarga anteponiendo **https://dl-ssl.google.com/android/repository/** el nombre de la **m2repository** que se va a descargar. Por ejemplo, use **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** para descargar **Android\_ m2repository\_ R10. zip**.

2. Cambie el nombre del archivo por el hash MD5 correspondiente de la dirección URL de descarga, tal como se muestra en la tabla anterior. Por ejemplo, si descargó **Android\_m2repository\_R25. zip**, cambie su nombre a **0B3F1796C97C707339FB13AE8507AF50. zip**. Si el hash MD5 de la dirección URL de descarga del archivo descargado no se muestra en la tabla, puede usar un [generador de MD5 en línea](http://www.webconfs.com/online-md5-generator.php) para convertir la dirección URL a una cadena hash MD5. 

3. Copie el archivo en **la carpeta Xamarin** mozip: 

    - En Windows, esta carpeta se encuentra en **C:\\usuarios\\nombre de\\***usuario***\\AppData\\\\local Xamarin**mozip. 

    - En Mac OS X, esta carpeta se encuentra en **/users/***username***/.local/share/Xamarin/Zips**. 

    Por ejemplo, en la siguiente captura de pantalla se muestra el resultado cuando se descarga **Android\_m2repository\_R16. zip** y se le cambia el nombre al hash MD5 de su dirección URL de descarga en Windows:

    [![Ejemplo del repositorio R16. zip cuyo nombre se ha cambiado a 0595E577D19D31708195A83087881EE6. zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

Si este procedimiento no soluciona el error de compilación, debe descargar manualmente el archivo **m2repository\_\_r_nn_. zip de Android** , descomprimirlo e instalar su contenido tal y como se describe en la sección siguiente. 

### <a name="manually-downloading-and-installing-m2repository-files"></a>Descarga e instalación manuales de archivos m2repository

El proceso totalmente manual para la recuperación de errores de **m2repository** supone descargar el **archivo\_Android\_m2repository r_nn_. zip** (mediante un explorador Web), descomprimirlo y copiar su contenido al soporte técnico. directorio de biblioteca del equipo. En el ejemplo siguiente, se recuperará de este mensaje de error: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Siga estos pasos para descargar **m2repository** e instalar su contenido:

1. Elimine el contenido de la carpeta de la biblioteca correspondiente al mensaje de error. Por ejemplo, en el mensaje de error anterior, eliminaría el contenido **de C\\:\\usuarios nombre\\de\\***usuario***\\AppData local Xamarin\\Android. support. V423.1.1.0\\** . 
    Como se describió anteriormente, debe eliminar todo el contenido de este directorio:

    [![Eliminación de carpetas de contenido, incrustadas y android_m2repository de la carpeta 23.1.1.0](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. Descargue el **archivo\_Android\_m2repository r_nn_. zip** de Google que se corresponda con el mensaje de error (vea la tabla de la sección anterior para obtener vínculos).

3. Extraiga este archivo **. zip** en cualquier ubicación (como el escritorio). Esto debe crear un directorio que se corresponda con el nombre del archivo **. zip** . En este directorio, debe encontrar un subdirectorio denominado **m2repository**: 

    [![carpeta m2repository encontrada en el archivo zip extraído](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. En el directorio de la biblioteca con versión que purgó en el paso 1, vuelva a crear el **contenido** y los subdirectorios **incrustados** . Por ejemplo, en la siguiente captura de pantalla se muestra el **contenido** y los subdirectorios **incrustados** que se crean en la carpeta **23.1.1.0** para **Android\_m2repository\_R25. zip**: 

    [![Crear contenido y carpetas incrustadas en la carpeta 23.1.1.0](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. Copie **m2repository** del archivo extraído **. zip** en el directorio de **contenido** que creó en el paso anterior: 

    [![Captura de pantalla de m2repository copiada en la carpeta 23.1.1.0/Content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. En el directorio **. zip** extraído, vaya **a\\m2repository\\com\\Android\\support support-V4** y abra la carpeta correspondiente al número de versión creado anteriormente (en este ejemplo, **23.1.1**):

    [![Ejemplo de lista de archivos contenidos en la carpeta Support-V4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. Copie todos los archivos de esta carpeta en el directorio **insertado** creado en el paso 4:

    [![Ejemplo de archivos copiados en la carpeta 23.1.1.0/Embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. Compruebe que se copian todos los archivos. El directorio **incrustado** debe contener ahora archivos como **. jar**, **. AAR**y **. archivo POM**.

9. Descomprima el contenido de los archivos **. AAR** extraídos en el directorio **incrustado** . En Windows, anexe una extensión **. zip** al archivo **. AAR** , ábrala y copie el contenido en el directorio **incrustado** .
    En macOS, descomprima el archivo **. AAR** con el comando **Unzip** del terminal (por ejemplo, **Unzip File. AAR**).

En este punto, ha instalado manualmente los componentes que faltan y el proyecto se debe compilar sin errores. Si no es así, compruebe que ha descargado la versión del archivo **m2repository** **. zip** que se corresponde exactamente con la versión del mensaje de error y compruebe que ha instalado su contenido en las ubicaciones correctas, tal y como se describe en los pasos anteriores. 

## <a name="summary"></a>Resumen 

En este artículo se explica cómo recuperarse de los errores comunes que pueden producirse durante la descarga e instalación automáticas de las bibliotecas de dependencias. Se describe cómo eliminar la biblioteca problemática y volver a compilar el proyecto como una manera de volver a descargar y volver a instalar la biblioteca. En él se describe cómo descargar la biblioteca e instalarla en **la carpeta** mozip. También se describe un procedimiento más implicado para descargar e instalar manualmente los archivos necesarios como una manera de solucionar problemas que no se pueden resolver a través de medios automáticos. 
