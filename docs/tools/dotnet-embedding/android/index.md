---
title: Inserción de .NET en Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007335"
---
# <a name="net-embedding-on-android"></a>Inserción de .NET en Android

En algunos casos, puede que desee agregar una biblioteca de Xamarin .NET a un proyecto de Android nativo existente. Para ello, puede usar la herramienta [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/) para convertir la biblioteca de .net en una biblioteca nativa que se puede incorporar en una aplicación Android basada en Java nativa.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin. Android

Para que Xamarin. Android funcione con la inserción de .NET, necesita lo siguiente:

- **Xamarin. android** &ndash;   [xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

- Se debe instalar **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) o posterior.

- El **Kit para desarrolladores de java** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.

## <a name="using-embeddinator-4000"></a>Uso de Embeddinator-4000

Para usar una biblioteca de .NET en un proyecto de Android nativo, siga estos pasos:

1. Cree un C# proyecto de biblioteca de Android.

2. Instale [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Busque **Embeddinator-4000. exe** y agréguelo a su **ruta de acceso**. Por ejemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. Ejecute Embeddinator-4000 en el ensamblado de la biblioteca. Por ejemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Use el archivo AAR generado en un proyecto de Java en Android Studio.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisitos de Xamarin. Android

Para que Xamarin. Android funcione con la inserción de .NET, necesita lo siguiente:

- **Xamarin. android** &ndash;   [xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o posterior debe estar instalado.

- Se debe instalar **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) o posterior.

- El **Kit para desarrolladores de java** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior debe estar instalado.

- **Mono** &ndash;   [mono 5,0](https://www.mono-project.com/download/) o posterior debe estar instalado (mono se instala con Visual Studio para Mac).

## <a name="using-embeddinator-4000"></a>Uso de Embeddinator-4000

Para usar una biblioteca de .NET en un proyecto de Android nativo, siga estos pasos:

1. Cree un C# proyecto de biblioteca de Android.

2. Instale [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Busque **Embeddinator-4000. exe** y agregue **mono** a la ruta de acceso. Por ejemplo:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. Ejecute Embeddinator-4000 en el ensamblado de la biblioteca. Por ejemplo:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Use el archivo AAR generado en un proyecto de Java en Android Studio.

-----

Las opciones de uso y línea de comandos se describen en la documentación de [Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) .

## <a name="callbacks"></a>Devoluciones

Obtenga información sobre cómo [realizar C# llamadas entre y Java](callbacks.md).

## <a name="samples"></a>Ejemplos

- [Aplicación de ejemplo Weather](https://github.com/jamesmontemagno/embeddinator-weather)
