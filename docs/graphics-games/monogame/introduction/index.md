---
title: Introducción al desarrollo de juegos con monogame
description: En este tutorial de varias partes se muestra cómo crear una sencilla aplicación 2D con monogame.  Trata conceptos comunes de programación de juegos, como gráficos, entradas, entidades de juegos y física.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8ffb62c8715ff60e9b0ea3b2bc536f3441fb8765
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724679"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introducción al desarrollo de juegos con monogame

_En este tutorial de varias partes se muestra cómo crear una sencilla aplicación 2D con monogame.  Trata conceptos comunes de programación de juegos, como gráficos, entradas, entidades de juegos y física._

En este artículo se describe la tecnología de API monogame para crear juegos multiplataforma. Para obtener una lista completa de las plataformas, vea el [sitio web de monogame](http://www.monogame.net/). Este tutorial se usará C# para los ejemplos de código, aunque monogame también es F# totalmente funcional.

Monogame es una API con aceleración de hardware multiplataforma que proporciona gráficos, audio, administración de Estados de juegos, entrada y una canalización de contenido para la importación de recursos. A diferencia de la mayoría de los motores de juegos, monogame no proporciona ni impone ningún patrón o estructura de proyecto.  Aunque esto significa que los desarrolladores pueden organizar el código como deseen, también significa que se necesita un poco de código de instalación al iniciar un proyecto nuevo.

La primera sección de este tutorial se centra en la configuración de un proyecto vacío. La última sección cubre la escritura de todo el contenido y la lógica del juego, la mayoría de las cuales serán multiplataforma.

Al final de este tutorial, se creará un juego sencillo en el que el reproductor puede controlar un carácter animado con entrada táctil.  Aunque técnicamente no es un juego completo (ya que no tiene condiciones de uso o pérdida), muestra numerosos conceptos de desarrollo de juegos y se puede usar como base para muchos tipos de juegos.

A continuación se muestra el resultado de este tutorial:

![Animación del carácter de juego de ejemplo después del mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame y XNA

La biblioteca de monogame está diseñada para imitar la biblioteca XNA de Microsoft en la sintaxis y la funcionalidad.  Todos los objetos monogame existen en el espacio de nombres Microsoft. XNA, lo que permite usar la mayoría del código XNA en monogame sin ninguna modificación.

Los desarrolladores familiarizados con XNA ya estarán familiarizados con la sintaxis de monogame y los desarrolladores que busquen información adicional sobre cómo trabajar con monogame podrán hacer referencia a los tutoriales de XNA en línea existentes, a la documentación de API y a las discusiones.

## <a name="walkthrough-parts"></a>Partes del tutorial

- [Parte 1: creación de un proyecto monogame multiplataforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementación de WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto monogame de WalkingGame (ejemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
- [Android de monogame en NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS de monogame en NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentación de la API de monogame](http://www.monogame.net/documentation/?page=main)
