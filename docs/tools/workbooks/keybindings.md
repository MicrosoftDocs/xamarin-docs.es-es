---
title: Métodos abreviados de teclado del editor de Xamarin Workbooks
description: En este documento se describen los métodos abreviados de teclado disponibles para su uso en el editor de Xamarin Workbooks. En concreto, examina varias formas de usar la tecla retorno.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: f8c76f96ea16b9341d349ca110a3eb39207a4d30
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019105"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Métodos abreviados de teclado del editor de Xamarin Workbooks

## <a name="the-return-key-and-its-nuances"></a>La tecla RETURN y sus matices

En la tabla siguiente se describen los distintos enlaces de teclado para ejecutar Code y crear Markdown. Hemos tomado una gran atención para elegir enlaces de teclado razonables y coherentes que están tanto familiares como fluidos.

|Enlace de teclado|Celda de código|Celda de Markdown|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Si el símbolo de intercalación está al final del búfer de celda y la celda se puede analizar correctamente, se ejecutará y los resultados se mostrarán debajo del búfer, y se insertará una nueva celda de código y la celda con el foco después de la celda ejecutada.</p><p>Si el análisis no se realiza correctamente, se insertará una nueva línea en el búfer. No se producirán diagnósticos del compilador si el análisis no se realiza correctamente.</p>|<p>La <kbd>devolución</kbd> presenta un comportamiento diferente en función del contexto de Markdown en el símbolo de intercalación.</p><ul><li>Si el símbolo de intercalación está en un bloque de código de Markdown, se inserta una nueva línea literal.</li><li>Si el símbolo de intercalación está en un bloque de lista de Markdown, cree un nuevo elemento de lista o divida el elemento de lista actual.</li><li>Si el símbolo de intercalación está en cualquier otro tipo de bloque Markdown, cree un nuevo bloque de párrafo o divida el bloque actual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Comando-devolver</kbd></dd><dt>Suerte</dt><dd><kbd>Control-devolver</kbd></dd></dl>|<p>Siempre intenta analizar y ejecutar el contenido de la celda. Si la compilación se realiza correctamente, los resultados (incluidas las excepciones de ejecución) se mostrarán debajo del búfer y, si no hay ninguna celda posterior, se creará una nueva y se le centrará.</p><p>Si hay algún error de compilación, se mostrarán los diagnósticos y el búfer seguirá centrado con la posición del símbolo de intercalación sin cambios.</p>|Inserta y centra una nueva celda de código después de la celda de Markdown actual.|
|<dl><dt>Mac</dt><dd><kbd>Comando-Shift-Return</kbd><dd><dt>Suerte</dt><dd><kbd>Control-Shift-Return</kbd></dd></dl>|Inserta y centra una nueva celda de Markdown después de la celda actual.|Mismo comportamiento que el <kbd>valor devuelto</kbd>|
|<kbd>Shift-Return</kbd>|Siempre inserta una nueva línea, independientemente de la ubicación o el contenido del símbolo de intercalación.|Inserta un salto de línea rígido en el bloque Markdown actual.|
