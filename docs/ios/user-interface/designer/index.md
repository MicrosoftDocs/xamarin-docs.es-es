---
title: Creación de interfaces de usuario con el diseñador de iOS
description: En este documento se describe cómo usar el Xamarin Designer para iOS para compilar la interfaz de usuario de una aplicación con guiones gráficos y archivos. Xib. Proporciona vínculos a documentos que describen la disponibilidad de la herramienta, su funcionalidad básica, los controles que se deben diseñar y proporcionan tutoriales de su uso.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/31/2018
ms.openlocfilehash: cb0fb1e8d0deeb04c11870935275b7d9f211fa59
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603118"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Creación de interfaces de usuario con el diseñador de iOS

_El Xamarin Designer para iOS es un diseñador visual para los formatos de guion gráfico y Interface Builder de iOS que está totalmente integrado con Visual Studio para Mac y Visual Studio. IOS Designer mantiene la compatibilidad total con los formatos Storyboard y. Xib, de modo que los archivos se pueden editar en Visual Studio para Mac o Visual Studio además de los Interface Builder de Xcode. Además, el Xamarin Designer para iOS admite características avanzadas, como los controles personalizados que se representan en tiempo de diseño en el editor._

> [!WARNING]
> IOS Designer quedó en desuso en Visual Studio 2019 versión 16,8 y Visual Studio 2019 para Mac versión 8,8 y se quitó en Visual Studio 2019 versión 16,9 y Visual Studio para Mac versión 8,9.
> La manera recomendada de compilar interfaces de usuario de iOS es directamente en un equipo Mac con Xcode. Para obtener más información, consulte [diseñar interfaces de usuario con Xcode](../storyboards/index.md). 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![iOS Designer en Visual Studio para Mac](images/designer-vsmac-sml.png "Diseñador de iOS")](images/designer-vsmac.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![iOS Designer en Visual Studio](images/designer-vs.png "Diseñador de iOS")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilidad

El Xamarin Designer para iOS está disponible en Visual Studio para Mac y en Visual Studio 2017 en Windows.

En estas guías se supone que está familiarizado con el contenido que se incluye en las [guías de introducción de Xamarin. iOS](~/ios/get-started/index.md).

## <a name="ios-designer-basics"></a>[Conceptos básicos de iOS Designer](introduction.md)

En esta guía se tratan las características de Xamarin iOS Designer. Trata los aspectos básicos del diseñador, que muestran cómo usar el diseñador para diseñar controles visualmente y cómo editar propiedades.

## <a name="designable-controls-overview"></a>[Información general sobre los controles que se van a diseñar](ios-designable-controls-overview.md)

Esta guía se centra en los controles personalizados, en cómo se crean y en qué requisitos deben cumplirse para que se representen en la superficie de diseño. Además, muestra cómo depurar problemas comunes que pueden producirse al utilizar controles que se pueden diseñar.

## <a name="walkthrough---using-custom-controls-with-ios-designer"></a>[Tutorial: uso de controles personalizados con el diseñador de iOS](ios-designable-controls-walkthrough.md)

En este artículo se proporciona un tutorial paso a paso que muestra cómo crear un control personalizado y usarlo en el diseñador de iOS. Muestra cómo hacer que un control esté disponible en el cuadro de herramientas del diseñador para que se pueda arrastrar o colocar en una vista. Además, muestra cómo implementar un control para que se represente correctamente en tiempo de diseño y en tiempo de ejecución, y cómo crear propiedades que se pueden establecer en tiempo de diseño.

## <a name="auto-layout-with-the-xamarin-ios-designer"></a>[Diseño automático con Xamarin iOS Designer](designer-auto-layout.md)

En esta guía se presenta el diseño automático de iOS y el nuevo flujo de trabajo de restricciones disponible en el diseñador de iOS.
