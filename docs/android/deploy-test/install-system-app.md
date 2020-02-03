---
title: Instalar Xamarin.Android como una aplicación del sistema
description: En esta guía se analizan las diferencias entre una aplicación del sistema y una aplicación de usuario, y se describe cómo instalar una aplicación Xamarin.Android como una aplicación del sistema. Esta guía se aplica a los autores de imágenes ROM personalizadas de Android. No se explica cómo crear una ROM personalizada.
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 72cddde86708b5573dc578165354d137c4dc35b6
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723905"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>Instalar Xamarin.Android como una aplicación del sistema

_En esta guía se analizan las diferencias entre una aplicación del sistema y una aplicación de usuario, y se describe cómo instalar una aplicación Xamarin.Android como una aplicación del sistema. Esta guía se aplica a los autores de imágenes ROM personalizadas de Android. No se explica cómo crear una ROM personalizada._

## <a name="system-app"></a>Aplicación del sistema

Los autores de imágenes ROM de Android personalizadas o los fabricantes de dispositivos Android pueden querer incluir una aplicación Xamarin.Android como una _aplicación del sistema_ al distribuir una ROM o un dispositivo. Una aplicación del sistema es una aplicación que se considera importante para el funcionamiento del dispositivo o que proporciona funcionalidad que el autor de la ROM personalizada siempre desea que esté disponible.

Las aplicaciones del sistema se instalan en la carpeta **/system/app/** (un directorio de solo lectura en el sistema de archivos) y el usuario no puede eliminarla ni moverla a menos que el usuario tenga acceso a la raíz. En cambio, una aplicación que instala el usuario (normalmente desde Google Play o al transferir localmente la aplicación) se conoce como una _aplicación de usuario_. Las aplicaciones de usuario pueden ser eliminadas por el usuario y, en muchos casos, pueden moverse a una ubicación diferente del dispositivo (por ejemplo, algún tipo de almacenamiento externo).

Las aplicaciones del sistema se comportan exactamente igual que las aplicaciones de usuario, pero tienen las siguientes notables excepciones:

- Las aplicaciones del sistema son actualizables igual que una _aplicación de usuario_ normal. Sin embargo, como siempre existe una copia de la aplicación en **/system/app/** , siempre es posible revertir la aplicación a la versión original.

- Las aplicaciones del sistema pueden tener concedidos determinados permisos solo del sistema que no están disponibles para una aplicación de usuario. Un ejemplo de un permiso solo del sistema es [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED), que permite que las aplicaciones se emparejen con dispositivos Bluetooth sin ninguna interacción del usuario.

Una aplicación Xamarin.Android se puede distribuir como una aplicación del sistema. Además de proporcionar un APK a la ROM personalizada, hay dos bibliotecas compartidas, **libmonodroid.so** y **libmonosgen 2.0.so** que deben copiarse manualmente del APK en el sistema de archivos de la imagen ROM. En esta guía se explican los pasos implicados.

## <a name="restrictions"></a>Restricciones

Esta guía se aplica a los autores de imágenes ROM personalizadas de Android. No se explica cómo crear una ROM personalizada.

En esta guía se supone que está familiarizado con el [empaquetado de un APK de lanzamiento de una aplicación Xamarin.Android](~/android/deploy-test/publishing/index.md) y que conoce las [arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md) de las aplicaciones Android.

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>Instalar una aplicación Xamarin.Android como una aplicación del sistema

En los pasos siguientes se describe cómo instalar una aplicación Xamarin.Android como una aplicación del sistema.

1. **Empaquete un APK de lanzamiento de la aplicación Xamarin.Android**: este paso se describe con más detalle en la guía [Publicación de una aplicación](~/android/deploy-test/publishing/index.md).

2. **Extraiga las bibliotecas compartidas del APK**: mediante cualquier utilidad de compresión, abra el archivo APK y examine el contenido de la carpeta **/lib/** . Esta carpeta tendrá un subdirectorio para cada _interfaz binaria de aplicación_ (ABI) que sea compatible con la aplicación; el contenido de esta carpeta incluirá todas las bibliotecas compartidas que requiere la aplicación en esa ABI en particular:

    ![Captura de pantalla de archivos .so en la carpeta armeabi-v7a de taskypro.zip](install-system-app-images/install-system-app-01.png)

   En la captura de pantalla anterior, solo hay una ABI admitida (**armeabi-v7a**) que contiene dos archivos **.so** que requiere la aplicación. Tenga en cuenta que solo es necesario extraer los archivos de ABI que son adecuados para el dispositivo o la arquitectura de destino de la ROM del dispositivo; es decir, no copie los archivos **.so** de la carpeta **x86** a un dispositivo o ROM **armeabi-v7a**.

3. **Copie los archivos .so en /system/lib**: copie los archivos **.so** que se extrajeron del APK en el paso anterior en la carpeta **/system/lib/** de la ROM personalizada.

4. **Copie el archivo APK en /system/app**: el paso final consiste en copiar el archivo APK en la carpeta **/system/app** en la ROM.

## <a name="summary"></a>Resumen

En esta guía se describe la diferencia entre una _aplicación del sistema_ y una _aplicación de usuario_, y se explica cómo instalar una aplicación Xamarin.Android como una aplicación del sistema.

## <a name="related-links"></a>Vínculos relacionados

- [Publicar una aplicación](~/android/deploy-test/publishing/index.md)
- [Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [ABI Management](https://developer.android.com/ndk/guides/abis)
