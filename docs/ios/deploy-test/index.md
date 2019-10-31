---
title: Implementar y probar aplicaciones Xamarin.iOS
description: Este documento contiene vínculos a diversas guías en las que se describen temas relacionados con la implementación y las pruebas de una aplicación Xamarin.iOS. Por ejemplo, distribución de aplicaciones, archivos .ipa, aprovisionamiento, implementación inalámbrica, TestFlight y depuración.
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: f3f5e27e97b7b62ade66ea2dc50a79ac03d51f90
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026454"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>Implementar y probar aplicaciones Xamarin.iOS

Esta sección contiene temas que se usan para probar una aplicación, además de información sobre cómo distribuirla. Estos temas incluyen aspectos como las herramientas usadas para la depuración, la implementación en evaluadores y el proceso para publicar una aplicación en App Store.

## <a name="app-distributioniosdeploy-testapp-distributionindexmd"></a>[App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)

En este artículo se muestra cómo configurar, compilar y publicar una aplicación de Xamarin.iOS para su distribución a través de varias formas diferentes, incluidas las siguientes:

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribución interna (Enterprise)](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deploymentiosdeploy-testapp-distributionipa-supportmd"></a>[IPA Deployment](~/ios/deploy-test/app-distribution/ipa-support.md) (Implementación de IPA)

Las implementaciones ad hoc y Enterprise permiten a los desarrolladores crear paquetes que se pueden distribuir para la realización de pruebas o para los usuarios internos de la empresa. En este documento se explica cómo crear un IPA que se puede sincronizar con un dispositivo iOS mediante iTunes.

## <a name="provisioningprovisioningindexmd"></a>[Aprovisionamiento](provisioning/index.md)

En este conjunto de guías se tratan conceptos básicos de aprovisionamiento y firma de código tales como trabajar con listas de propiedades y cómo aprovisionar la aplicación con servicios de aplicaciones. 

## <a name="wireless-deploymentwireless-deploymentmd"></a>[Implementación inalámbrica](wireless-deployment.md)

 Xcode 9 introdujo la opción de implementación en un dispositivo iOS o Apple TV a través de una red, en lugar de tener que conectar directamente los dispositivos cada vez que quiera implementar y depurar la aplicación. Esta característica se encuentra actualmente en versión preliminar.

## <a name="testflightiosdeploy-testtestflightmd"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight es ahora propiedad de Apple y se considera la principal manera de realizar pruebas beta de las aplicaciones de Xamarin.iOS. En este artículo se explican todos los pasos del proceso de TestFlight, desde cargar la aplicación hasta trabajar con iTunes Connect.

## <a name="debugging-in-xamariniosiosdeploy-testdebugging-in-xamarin-iosmd"></a>[Debugging in Xamarin.iOS](~/ios/deploy-test/debugging-in-xamarin-ios.md) (Depurar en Xamarin.iOS)

Tanto el IDE de Visual Studio como el de Visual Studio para Mac incluyen compatibilidad con la depuración de aplicaciones de Xamarin.iOS en el simulador de iOS y en dispositivos iOS. En este artículo se muestra cómo se usa el depurador y cómo se configuran las diversas opciones que admite.

## <a name="touchunitiosdeploy-testtouchunitmd"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

En este documento se describe cómo se crean pruebas unitarias para los proyectos de Xamarin.iOS.
Las pruebas unitarias con Xamarin.iOS se realizan mediante el marco Touch.Unit, que incluye tanto un ejecutor de pruebas de iOS como una versión modificada del marco [NUnitLite](http://www.nunitlite.com/), que proporciona un conjunto conocido de API para escribir pruebas unitarias.

## <a name="using-instruments-to-detect-native-leaks-using-markheapiosdeploy-testusing-instruments-to-detect-native-leaks-using-markheapmd"></a>[Uso de Instruments para detectar pérdidas nativas mediante MarkHeap](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

En este artículo se describe cómo usar Instruments en cualquier dispositivo iOS y aplicación de Xamarin.iOS. También se explica cómo generar perfiles de aplicaciones en el simulador.

## <a name="walkthrough---using-apples-instrument-tooliosdeploy-testwalkthrough-apples-instrumentmd"></a>[Tutorial: usar la herramienta Instruments de Apple](~/ios/deploy-test/walkthrough-apples-instrument.md)

Este artículo es una guía de uso de la herramienta Instruments de Apple para diagnosticar problemas de memoria en una aplicación de iOS compilada con Xamarin. Muestra cómo iniciar Instruments, tomar instantáneas del montón y analizar el aumento de la memoria. También muestra cómo usar Instruments para mostrar e identificar las líneas de código exactas que provocan el problema de memoria.

## <a name="linking-on-ioslinkermd"></a>[Vincular en iOS](linker.md)

Se explica cómo funciona el vinculador para garantizar el paquete de aplicación más pequeño posible, y cómo modificar su configuración y uso.

## <a name="xamarinios-performanceperformancemd"></a>[Rendimiento de Xamarin.iOS](performance.md)

Existen muchas técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.iOS. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación.

## <a name="mtouchmtouchmd"></a>[mtouch](mtouch.md)

Notas e información sobre mtouch.exe, la herramienta de la línea de comandos que compila el proyecto en una aplicación que se puede usar con iOS.

## <a name="ios-build-mechanicsios-build-mechanicsmd"></a>[Mecanismos de compilación de iOS](ios-build-mechanics.md)

En esta guía se examina cómo medir el tiempo de las aplicaciones y cómo usar métodos que se pueden emplear para compilaciones rápidas en todas las configuraciones de compilación.
