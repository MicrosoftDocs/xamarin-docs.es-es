---
title: Kit de desarrollo de Xamarin. Android y Java 9
description: En este artículo se explica cómo resolver errores del kit de desarrollo de Java (JDK) 9 o posterior en Xamarin. Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 3f1f49a5f63d8159644bd2bb247287a91c27023a
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225553"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android y el kit de desarrollo de Java 9 o posterior

_En este artículo se explica cómo resolver errores del kit de desarrollo de Java (JDK) 9 o posterior en Xamarin. Android._


## <a name="overview"></a>Información general

Xamarin. Android usa el kit de desarrollo de Java (JDK) para integrarlo con la Android SDK para compilar aplicaciones de Android y ejecutar Android Designer. Las versiones más recientes del Android SDK (API 24 y versiones posteriores) requieren JDK 8 (1,8) o la versión preliminar de Microsoft Mobile OpenJDK. **Dado que las herramientas de Android SDK disponibles en Google todavía no son compatibles con JDK 9, Xamarin. Android no funciona con JDK 9 o posterior.**

## <a name="jdk-errors"></a>Errores de JDK

Si intenta compilar un proyecto de Xamarin. Android con una versión del JDK posterior a JDK 8, obtendrá un error explícito que indica que no se admite esta versión de JDK. Por ejemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Para resolver estos errores, debe instalar JDK 8 (1,8), tal como se explica en [Cómo actualizar la versión del kit de desarrollo de Java (JDK)](~/android/troubleshooting/questions/update-jdk.md).
Como alternativa, puede instalar la [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md) . Microsoft Mobile OpenJDK reemplazará finalmente JDK 8 para el desarrollo de Xamarin. Android.


## <a name="checking-the-jdk-version"></a>Comprobando la versión de JDK

Puede comprobar la versión de Java que ha instalado escribiendo el siguiente comando (el directorio JDK `bin` debe estar `PATH`en):

```shell
java -version
```

Si está instalado JDK 9, verá un mensaje similar al siguiente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si está instalado JDK 9 o posterior, debe instalar Java JDK 8 (1,8) o la versión preliminar de Microsoft Mobile OpenJDK. Para obtener información sobre cómo instalar JDK 8, consulte [Cómo actualizar la versión del kit de desarrollo de Java (JDK)](~/android/troubleshooting/questions/update-jdk.md). Para obtener información sobre cómo instalar Microsoft Mobile OpenJDK, vea [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md).

Tenga en cuenta que no tiene que desinstalar una versión posterior del JDK; sin embargo, debe asegurarse de que Xamarin usa JDK 8 en lugar de una versión posterior de JDK. En Visual Studio, haga clic en **herramientas > opciones > Xamarin > configuración de Android**. Si la **Ubicación del kit de desarrollo de Java** no está establecida en una ubicación de JDK 8 (por ejemplo, **C:\\archivos\\de programa Java\\JDK 1.8.0 _111**), haga clic en **cambiar** y establézcala en la ubicación donde está instalado JDK 8. En Visual Studio para Mac, vaya a **preferencias > proyectos > ubicaciones del sdk > SDK de Java > Android (JDK)** y haga clic en **examinar** para actualizar esta ruta de acceso.

## <a name="known-issues-with-jdk-9"></a>Problemas conocidos de JDK 9

### <a name="apksigner"></a>apksigner

Existe un problema conocido con apksigner y JDK 9 en el que el `apksigner.bat` archivo invoca `apksigner.jar` con `-Djava.ext.dirs` en lugar de `-classpath` lo que se espera en JDK 9. Se recomienda usar JDK 8 (1,8). Para obtener información sobre cómo instalar JDK 8, consulte [Cómo actualizar la versión del kit de desarrollo de Java (JDK)](~/android/troubleshooting/questions/update-jdk.md) .

Si ha instalado JDK 9, asegúrese de que la ruta de acceso siguiente no esté establecida `PATH` en la variable de entorno, ya que seguirá señalando `C:\ProgramData\Oracle\Java\javapath`a JDK 9:. Después de quitarlo `java-version` , en una línea de comandos debería mostrar JDK 8.
