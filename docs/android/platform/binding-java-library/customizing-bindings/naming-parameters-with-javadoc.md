---
title: Nomenclatura de parámetros con javadoc
description: En este artículo se explica cómo recuperar nombres de parámetros en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027663"
---
# <a name="naming-parameters-with-javadoc"></a>Nomenclatura de parámetros con javadoc

_En este artículo se explica cómo recuperar nombres de parámetros en un proyecto de enlace de Java mediante el uso de Javadoc generado a partir del proyecto de Java._

## <a name="overview"></a>Información general

Al enlazar una biblioteca de Java existente, se pierden algunos metadatos sobre la API enlazada. En particular, los nombres de los parámetros de los métodos. Los nombres de parámetro aparecerán como `p0`, `p1`, etc. Esto se debe a que los archivos de `.class` de Java no conservan los nombres de parámetro que se usaron en el código fuente Java. 

Un proyecto de enlace de Java de Xamarin. Android puede proporcionar los nombres de parámetro si tiene acceso al HTML Javadoc desde la biblioteca original. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integración de Javadoc HTML en un proyecto de enlace de Java

La integración de Javadoc HTML en un proyecto de enlace de Java es un proceso manual que consta de los pasos siguientes: 

1. Descargar Javadoc para la biblioteca
2. Edite el archivo de `.csproj` y agregue una propiedad `<JavaDocPaths>`:
3. Limpiar y volver a generar el proyecto

Una vez hecho esto, los nombres de parámetros de Java originales deben estar presentes en las API enlazadas por un proyecto de enlace de Java. 

> [!NOTE]
> Hay una gran cantidad de varianza en la salida de JavaDoc. El. La cadena de herramientas de enlace JAR no admite todas las permutaciones posibles y, por lo tanto, es posible que algún parámetro no tenga un nombre correcto.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Javadoc en un proyecto de enlace de Java para proporcionar los nombres de parámetro de significado para las API enlazadas. 
