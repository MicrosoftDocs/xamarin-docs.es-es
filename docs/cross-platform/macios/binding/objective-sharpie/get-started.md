---
title: Introducción con Objective Sharpie
description: En este documento se proporciona información general de alto nivel sobre Objective Sharpie, la herramienta que se usa para automatizar la creación de enlaces de C# a código de Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 5bee8df72290cab3ed6d5c23fef6c2ae2f1a2559
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928521"
---
# <a name="getting-started-with-objective-sharpie"></a>Introducción con Objective Sharpie

> [!IMPORTANT]
> Objective Sharpie es una herramienta para desarrolladores de Xamarin experimentados con conocimiento avanzado de Objective-C (y por extensión, C). Antes de intentar enlazar una biblioteca de Objective-C, debe tener un conocimiento sólido de cómo crear la biblioteca nativa en la línea de comandos (y una buena comprensión de cómo funciona la biblioteca nativa).

<a name="installing"></a>

## <a name="installing-objective-sharpie"></a>Instalación del objetivo Sharpie

Objective Sharpie es actualmente una herramienta de línea de comandos independiente para Mac OS X 10,10 y versiones más recientes, y _no es un producto de Xamarin totalmente compatible_ . Solo la pueden usar desarrolladores avanzados para ayudar a crear un proyecto de enlace a una biblioteca de Objective-C de terceros.

Objective Sharpie se puede descargar como un instalador de paquetes estándar de OS X.
Ejecute el instalador y siga todas las indicaciones en pantalla del Asistente para la instalación:

- **Versión actual: 3,4**
  - [Descargar la última versión](https://aka.ms/objective-sharpie)
  - [Anuncio del Foro](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Use el `sharpie update` comando para actualizar a la versión más reciente.

## <a name="basic-walkthrough"></a>Tutorial básico

Objective Sharpie es una herramienta de línea de comandos proporcionada por Xamarin que ayuda a crear las definiciones necesarias para enlazar una biblioteca de Objective-C de terceros con C#.
Incluso cuando *se* usa Objective Sharpie, el desarrollador deberá modificar los archivos generados después de que el objetivo Sharpie termine para solucionar cualquier problema que no se pueda controlar automáticamente con la herramienta.

Siempre que sea posible, Objective Sharpie anotará las API con las que tiene dudas sobre cómo enlazar correctamente (muchas construcciones del código nativo son ambiguas).
Estas anotaciones aparecerán como [ `[Verify]` atributos](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

La salida de Objective Sharpie es un par de archivos, [ `ApiDefinition.cs` y `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que se puede usar para crear un proyecto de enlace que se compila en una biblioteca que se puede usar en las aplicaciones de Xamarin.

> [!IMPORTANT]
> Objective Sharpie incluye una regla **principal** para el uso correcto: debe pasarle los argumentos correctos de la línea de comandos del compilador Clang para garantizar el análisis correcto. Esto se debe a que la fase de análisis de objetivos Sharpie es simplemente una herramienta [implementada en la API de Clang libtooling](https://clang.llvm.org/docs/LibTooling.html).

Esto significa que el objetivo Sharpie tiene toda la potencia de Clang (el compilador de C/Objective-C/C++ que compila realmente la biblioteca nativa que se enlazaría) y todo su conocimiento interno de los archivos de encabezado para el enlace.
En lugar de traducir el [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) analizado al código objeto, Objective Sharpie traduce el AST a un "scaffolding" de enlace de C# adecuado para la entrada a las `bmac` herramientas de `btouch` enlace de Xamarin y.

Si se producen errores en el objetivo Sharpie durante el análisis, significa que Clang ha producido un error durante su fase de análisis al intentar construir el AST y debe averiguar por qué.

**¡Nuevo!** la versión 3,0 intenta resolver parte de esta complejidad al admitir proyectos de Xcode directamente. Si una biblioteca nativa tiene un proyecto de Xcode válido, Objective Sharpie puede evaluar el proyecto para un destino y una configuración especificados para deducir los archivos de encabezado de entrada y las marcas de compilador necesarios.

Si no hay ningún proyecto de Xcode disponible, deberá estar más familiarizado con el proyecto deducendo los archivos de encabezado de entrada correctos, las rutas de acceso de búsqueda de archivos de encabezado y otras marcas de compilador necesarias. Es importante tener en cuentan que las marcas de compilador usadas para compilar la biblioteca nativa son las mismas que se deben pasar al objetivo Sharpie. Este es un proceso más manual y otro que requiere un poco de familiaridad con la compilación de código nativo en la línea de comandos con la cadena de herramientas Clang.

**¡Nuevo!** la versión 3,0 también incorpora una herramienta para enlazar [CocoaPods](https://cocoapods.org) fácilmente mediante el `sharpie pod` comando.
Si la biblioteca que le interesa está disponible como CocoaPod, le recomendamos que empiece por el intento de enlazar el CocoaPod con Sharpie objetivo (frente al intento de enlazar directamente con el origen).
