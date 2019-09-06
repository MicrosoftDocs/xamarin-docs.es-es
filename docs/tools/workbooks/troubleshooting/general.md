---
title: Problemas conocidos & soluciones alternativas
description: En este documento se describen los problemas conocidos y las soluciones alternativas para Xamarin Workbooks. Se describen los problemas de CultureInfo, los problemas de JSON y mucho más.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: b7b73e214af6a5a45426b4e2d2d7e01a436b379e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292782"
---
# <a name="known-issues--workarounds"></a>Problemas conocidos & soluciones alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistencia de CultureInfo entre celdas

La `System.Threading.CurrentThread.CurrentCulture` configuración `System.Globalization.CultureInfo.CurrentCulture` o no se mantiene entre las celdas del libro en los destinos de los libros basados en mono (Mac, iOS y Android) debido a un [error en `AppContext.SetSwitch` la implementación de mono][appcontext-bug] .

### <a name="workarounds"></a>Soluciones

- Establezca la aplicación-dominio-local `DefaultThreadCurrentCulture`:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- O bien, actualice a los libros 1.2.1 o versiones más recientes, que volverán a escribir `System.Threading.CurrentThread.CurrentCulture` las `System.Globalization.CultureInfo.CurrentCulture` asignaciones en y para proporcionar el comportamiento deseado (con lo que se soluciona el error de mono).

## <a name="unable-to-use-newtonsoftjson"></a>No se puede usar Newtonsoft. JSON

### <a name="workaround"></a>Solución alternativa

- Actualice a los libros 1.2.1, donde se instalará Newtonsoft. JSON 9.0.1.
  Los libros 1,3, actualmente en el canal alfa, admiten las versiones 10 y posteriores.

### <a name="details"></a>Detalles

Newtonsoft. JSON 10 se lanzó, lo que sobrepasó su dependencia de Microsoft. CSharp que entra en conflicto con los libros de `dynamic`versiones que se incluyen en el soporte técnico. Esto se trata en la versión de vista previa de los libros 1,3, pero por ahora hemos solucionado este paso anclando Newtonsoft. JSON específicamente a la versión 9.0.1.

Los paquetes de NuGet que dependen de Newtonsoft. JSON 10 o versiones más recientes solo se admiten en los libros 1,3, actualmente en el canal alfa.

## <a name="code-tooltips-are-blank"></a>La información sobre herramientas de código está en blanco

Hay un [error en el editor de Mónaco][monaco-bug] en Safari/WebKit, que se usa en la aplicación libros de Mac, que hace que la información sobre herramientas de código se represente sin texto.

![](general-images/monaco-signature-help-bug.png)

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
