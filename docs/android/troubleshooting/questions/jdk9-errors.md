---
title: Xamarin.Android y Java Development Kit 9
description: En este artículo se explica cómo resolver los errores de Java Development Kit (JDK) 9 o versiones posteriores en Xamarin.Android.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026833"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android y Java Development Kit 9 o versión posterior

_En este artículo se explica cómo resolver los errores de Java Development Kit (JDK) 9 o versiones posteriores en Xamarin.Android._

## <a name="overview"></a>Información general

Xamarin.Android usa Java Development Kit (JDK) para la integración con Android SDK a fin de compilar aplicaciones de Android y ejecutar Android Designer. Las versiones más recientes de Android SDK (API 24 y superiores) requieren JDK 8 (1.8) o la versión preliminar de Microsoft Mobile OpenJDK. **Dado que las herramientas de Android SDK disponibles en Google todavía no son compatibles con JDK 9, Xamarin.Android no funciona con JDK 9 o versiones posteriores.**

## <a name="jdk-errors"></a>Errores de JDK

Si intenta compilar un proyecto de Xamarin.Android con una versión del JDK posterior a JDK 8, obtendrá un error explícito que indicará que no se admite esta versión de JDK. Por ejemplo:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

Para resolver estos errores, debe instalar JDK 8 (1.8) tal y como se explica en [¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)
Como alternativa, puede instalar la [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Con el tiempo, Microsoft Mobile OpenJDK reemplazará a JDK 8 para el desarrollo de Xamarin.Android.

## <a name="checking-the-jdk-version"></a>Comprobación de la versión de JDK

Puede comprobar la versión de Java que ha instalado escribiendo el siguiente comando (el directorio `bin` del JDK debe estar en su `PATH`):

```shell
java -version
```

Si JDK 9 está instalado, verá un mensaje similar al siguiente:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

Si se ha instalado JDK 9 o una versión posterior, debe instalar Java JDK 8 (1.8) o la versión preliminar de Microsoft Mobile OpenJDK. Para obtener información sobre cómo instalar JDK 8, consulte [¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md) Para obtener información sobre como instalar Microsoft Mobile OpenJDK, consulte [Vista previa de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md).

Tenga en cuenta que no tiene que desinstalar la versión posterior de JDK; sin embargo, debe asegurarse de que Xamarin usa JDK 8 en lugar de la versión posterior. En Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > Configuración de Android**. Si **Ubicación de Java Development Kit** no está establecida en una ubicación de JDK 8 (como **C:\\Archivos de programa\\Java\\jdk1.8.0_111**), haga clic en **Cambiar** y establézcala en la ubicación donde está instalado JDK 8. En Visual Studio para Mac, vaya a **Preferencias > Proyectos > Ubicaciones de SDK > Android > Java SDK (JDK)** y haga clic en **Examinar** para actualizar esta ruta de acceso.

## <a name="known-issues-with-jdk-9"></a>Problemas conocidos de JDK 9

### <a name="apksigner"></a>apksigner

Existe un problema conocido con apksigner y JDK 9 en el que el archivo `apksigner.bat` invoca `apksigner.jar` con `-Djava.ext.dirs` en lugar de `-classpath` que se espera en JDK 9. Se recomienda utilizar JDK 8 (1.8). Para obtener información sobre cómo instalar JDK 8, consulte [¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?](~/android/troubleshooting/questions/update-jdk.md)

Si ha instalado JDK 9, asegúrese de que la siguiente ruta de acceso no esté establecida en la variable de entorno `PATH`, ya que seguirá apuntando a JDK 9: `C:\ProgramData\Oracle\Java\javapath`. Después de quitarlo, `java-version` en una línea de comandos debe mostrar JDK 8.
