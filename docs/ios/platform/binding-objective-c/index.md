---
title: Enlace de bibliotecas de iOS
description: En este documento se describe cómo C# crear enlaces a código de Objective-C, lo que permite consumir bibliotecas nativas y CocoaPods en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022223"
---
# <a name="binding-ios-libraries"></a>Enlace de bibliotecas de iOS

Siga estos vínculos para obtener información sobre el enlace de bibliotecas de Objective-C y CocoaPods para Xamarin. iOS y Xamarin. Mac:

- [**Información general**](~/cross-platform/macios/binding/overview.md) -
  describe cómo funciona el enlace.
- [**Enlace de bibliotecas de Objective-c**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  instrucciones sobre cómo enlazar bibliotecas de Objective-c para su uso en proyectos de Xamarin.
- [**Guía de referencia de definición de tipos**](~/cross-platform/macios/binding/binding-types-reference.md) -
  describe todos los atributos disponibles para enlazar autores para impulsar el proceso de generación de enlaces.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie es una herramienta de línea de comandos que ayuda a arrancar el primer paso de un enlace.
Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que, de otro modo, se realiza manualmente). Objective Sharpie no crea un enlace por sí solo, pero puede ayudarle a comenzar.

Objective Sharpie 3,0 presentó la capacidad de enlazar Cocoapods directamente.

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Tutorial: enlazar una biblioteca de Objective-C de iOS](walkthrough.md)

En esta página se proporciona un tutorial paso a paso para crear un proyecto de enlace de iOS con el proyecto de código abierto [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) Objective-C como ejemplo. La biblioteca **InfColorPicker** proporciona un controlador de vistas reutilizable que permite al usuario seleccionar un color en función de su representación HSB, lo que facilita la selección de color.
El objetivo Sharpie se usará para ayudar en el proceso de enlace.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Enlaces de iOS en C/C++ video**

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de Objective-C](~/cross-platform/macios/binding/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
