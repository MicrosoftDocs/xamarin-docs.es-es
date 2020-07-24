---
title: Problemas conocidos & soluciones alternativas
description: En este documento se describen los problemas conocidos y las soluciones alternativas para Xamarin Workbooks. Se describen los problemas de CultureInfo, los problemas de JSON y mucho más.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: aa6bbf9336acbff8558744220b9f4b8634f46421
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997233"
---
# <a name="known-issues--workarounds"></a>Problemas conocidos & soluciones alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistencia de CultureInfo entre celdas

La configuración o no se `System.Threading.CurrentThread.CurrentCulture` `System.Globalization.CultureInfo.CurrentCulture` mantiene entre las celdas del libro en los destinos de los libros basados en mono (Mac, iOS y Android) debido a un [error en la implementación de `AppContext.SetSwitch` mono][appcontext-bug] .

### <a name="workarounds"></a>Soluciones alternativas

- Establezca la aplicación-dominio-local `DefaultThreadCurrentCulture` :

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- O bien, actualice a los libros 1.2.1 o versiones más recientes, que volverán a escribir `System.Threading.CurrentThread.CurrentCulture` las asignaciones en y `System.Globalization.CultureInfo.CurrentCulture` para proporcionar el comportamiento deseado (con lo que se soluciona el error de mono).

## <a name="unable-to-use-newtonsoftjson"></a>No se puede usar Newtonsoft.Jsen

### <a name="workaround"></a>Solución alternativa

- Actualice a los libros 1.2.1, donde se instalará Newtonsoft.Jsen 9.0.1.
  Los libros 1,3, actualmente en el canal alfa, admiten las versiones 10 y posteriores.

### <a name="details"></a>Detalles

Se liberó Newtonsoft.Jsen 10, lo que hizo que se desplazara su dependencia de Microsoft. CSharp, lo que entra en conflicto con los libros de la versión que admite `dynamic` . Esto se trata en la versión de vista previa de los libros 1,3, pero por ahora hemos solucionado este paso anclando Newtonsoft.Jsespecíficamente a la versión 9.0.1.

Los paquetes de NuGet en función de Newtonsoft.Jsde 10 o más recientes solo se admiten en los libros 1,3, actualmente en el canal alfa.

## <a name="code-tooltips-are-blank"></a>La información sobre herramientas de código está en blanco

Hay un [error en el editor de Mónaco][monaco-bug] en Safari/WebKit, que se usa en la aplicación libros de Mac, que hace que la información sobre herramientas de código se represente sin texto.

![Información sobre herramientas de Mónaco en representación sin texto](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solución alternativa

- Al hacer clic en la información sobre herramientas después de que aparezca, se forzará el procesamiento del texto.

- O actualizar a libros 1.2.1 o posteriores

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Faltan los representadores de SkiaSharp en los libros 1,3

A partir de los libros 1,3, hemos quitado los representadores de SkiaSharp que se enviaron en los libros 0.99.0, en favor de SkiaSharp proporcionando los representadores en sí, con nuestro [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solución alternativa

- Actualice SkiaSharp a la versión más reciente de NuGet. En el momento de escribir este documento, es 1.57.1.

## <a name="related-links"></a>Vínculos relacionados

- [Informes de errores](~/tools/workbooks/install.md#reporting-bugs)
