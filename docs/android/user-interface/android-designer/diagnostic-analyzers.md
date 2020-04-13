---
title: Analizadores de diagnóstico de diseño de Android
description: Esta guía enumera todos los analizadores de diagnóstico de diseño de Android admitidos actualmente en Xamarin.Android.
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 04/07/2020
ms.openlocfilehash: 6203ce444bb97fa453a912a724f7f5724558b32a
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987621"
---
# <a name="android-designer-diagnostic-analyzers"></a>Analizadores de diagnóstico de diseñador es de Android

Esta guía enumera todos los analizadores de diagnóstico de diseño de Android compatibles actualmente.

## <a name="accessibility"></a>Accesibilidad

Los siguientes analizadores ayudan a mejorar la compatibilidad de accesibilidad:

| id | Título | severity | Descripción |
|----|-------|----------|-------------|
| ContentDescription | Imagen sin`contentDescription` | Advertencia | Atributo `contentDescription` que falta en la imagen |

## <a name="correctness"></a>Corrección

Los siguientes analizadores ayudan a solucionar problemas de corrección en un diseño:

| id | Título | severity | Descripción | Ayuda |
|----|-------|----------|-------------|------|
| AdapterViewChildren | AdapterView con niños | Advertencia | AdapterViews no puede tener elementos secundarios en XML | [Vínculo](xref:Android.Widget.AdapterView) |
| MissingId | Los fragmentos `id` deben especificar un`tag` | Advertencia |Esta `<fragment>` etiqueta debe `id` especificar `tag` una o una para conservar el estado en los reinicios de actividad | [Vínculo](xref:Android.App.Fragment) |
| NestedScrollingVertical | Elementos de desplazamiento vertical anidados | Advertencia | Widgets de desplazamiento anidados |
| NestedScrollingHorizontal | Elementos de desplazamiento horizontal anidados | Advertencia | Widgets de desplazamiento anidados |
| ScrollViewSize | ScrollView niños con tamaños de fill_parent/match_parent incorrectos | Advertencia | ScrollView niños con tamaños de fill_parent/match_parent incorrectos |
| ScrollViewCount | ScrollViews solo puede tener un hijo | Advertencia | Una vista de desplazamiento solo puede tener un elemento secundario |
| MissingAndroidNamespace | Falta espacio de nombres androide en el atributo | Error | Falta espacio de nombres XML de Android; su atributo se interpretará como un atributo personalizado |
| DuplicateIDs | ID duplicados | Error | Ids duplicados dentro de un único diseño |
| IncludeLayoutParamsMissingWidthAndHeight | Falta tanto anchura como anchura | Error | Los parámetros de diseño ignorados incluyen | [Vínculo](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingWidth | Falta anchura | Error | Los parámetros de diseño ignorados incluyen | [Vínculo](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| IncludeLayoutParamsMissingHeight | Altura faltante | Error | Los parámetros de diseño ignorados incluyen | [Vínculo](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| Orientación | Falta orientación explícita | Error | Falta orientación explícita |
| Suspicious0dp | Dimensión 0dp sospechosa | Error | Dimensión 0dp sospechosa |
| RequiredSizeWidth | Atributo de ancho que falta | Error | Atributo que falta: layout_width |
| RequiredSizeHeight | Atributo de altura faltante | Error | Atributo que falta: layout_height |
| WebViewLayout | WebViews en wrap_content padres | Error |
| WrongCase | Caso incorrecto para la etiqueta de vista | Error | Caso incorrecto para la etiqueta de vista | [Vínculo](xref:Android.App.Fragment) |

## <a name="design"></a>Diseño

Los siguientes analizadores ayudan a mejorar la forma de unir archivos de diseño:

| id | Título | severity | Descripción |
|----|-------|----------|-------------|
| HardcodedColor | Color codificado | Información | El color codificado a menudo conduce a la incoherencia |
| HardcodedSize  | Tamaño codificado  | Información | El tamaño codificado a menudo conduce a la incoherencia  |
| HardcodedText  | Texto codificado  | Advertencia | Texto codificado |
| UnresolvedResource | URL de recursos sin resolver | Advertencia | Esta dirección URL de recurso no se puede resolver |
| XmlErrors | Error de sintaxis XML | Error | Error de sintaxis XML |

## <a name="performance"></a>Rendimiento

Los siguientes analizadores ayudan a mejorar el rendimiento de su diseño:

| id | Título | severity | Descripción |
|----|-------|----------|-------------|
| NestedWeights | Pesos de diseño anidados | Advertencia | Los pesos anidados son malos para el rendimiento |
| TooManyViews | Diseño tiene demasiadas vistas | Advertencia | Diseño tiene demasiadas vistas |
| TooDeepLayout | La jerarquía de diseño es demasiado profunda | Advertencia | La jerarquía de diseño es demasiado profunda |
| UselessParent | Diseño de padre inútil | Advertencia | Diseño de padre inútil |
| UselessLeaf | Diseño de hoja inútil | Advertencia | Esta `%1$s` vista es inútil (sin hijos, no, `background`no, `id`no `style`) |

## <a name="usability"></a>Facilidad de uso

Los siguientes analizadores ayudan a mejorar la facilidad de uso del diseño para sus clientes:

| id | Título | severity | Descripción |
|----|-------|----------|-------------|
| NegativeMargin | Márgenes negativos | Advertencia | Márgenes negativos |
| MissingInputType | EditText sin inputType | Advertencia | No se ha especificado ningún tipo de entrada |
| InputTypePhone | EditText parece ser un número de teléfono | Advertencia | El nombre de la vista sugiere que se `phone` trata de un número de teléfono, pero no incluye en el`inputType` |
| InputTypeNumber | EditText parece ser un número | Advertencia | El nombre de la vista sugiere que es un `inputType` número, `numberDecimal`pero no incluye un número (como ) |
| InputTypePassword | EditText parece ser una contraseña | Advertencia | El nombre de la vista sugiere que se `password` trata `inputType` de `textVisiblePassword`una contraseña, pero no se incluye en el (por ejemplo, ) |
| InputTypePIN | EditText parece ser un PIN | Advertencia | El nombre de la vista sugiere que se trata `numberPassword` de una contraseña (PIN), pero no se incluye en el`inputType` |
| InputTypeEmail | EditText parece ser un correo electrónico | Advertencia | El nombre de la vista sugiere que se trata `email` de `inputType` una dirección `textEmailAddress`de correo electrónico, pero no se incluye en el (como ) |
| InputTypeURI | EditText parece ser un URI | Advertencia | El nombre de la vista sugiere que se `textUri` trata de un URI, pero no se incluye en el`inputType` |
| InputTypeDate | EditText parece ser una fecha | Advertencia | El nombre de la vista sugiere que se `date` trata `inputType` de `datetime`una fecha, pero no se incluye en el (como ) |
