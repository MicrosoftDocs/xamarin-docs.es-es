---
title: Texto en Xamarin.Forms
description: Xamarin.Forms tiene tres vistas principales para trabajar con texto y, en este artículo se explica cómo usarlas para escribir y mostrar texto en las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
ms.openlocfilehash: 7b7cd9445ec9f28211037608ab04ffc45bec705c
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227976"
---
# <a name="text-in-xamarinforms"></a>Texto en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Uso de Xamarin.Forms para escribir o mostrar texto._

Xamarin.Forms tiene tres vistas principales para trabajar con texto:

- **[Etiqueta](#Label)**  &mdash; para presentar un o varias líneas de texto. Puede mostrar texto con varias opciones de formato en la misma línea.
- **[Entrada](#Entry)**  &mdash; para escribir texto que es solo una línea. Entrada tiene un modo de contraseña.
- **[Editor](#Editor)**  &mdash; para escribir texto que podría tardar más de una línea.

Apariencia del texto se puede cambiar mediante integradas o personalizadas [estilos](#Styles) y algunos controles admiten personalizado [fuentes](#Fonts).

<a name="Label" />

## <a name="labellabelmd"></a>[Label](label.md)

El `Label` vista se utiliza para mostrar texto. Puede mostrar varias líneas de texto o una sola línea de texto. `Label` puede presentar el texto con varias opciones de formato usadas en línea. La vista de la etiqueta puede ajustar o truncar el texto cuando no caben en una sola línea.

![Ejemplo de etiqueta](images/label.png)

Consulte la [etiqueta](label.md) artículo para obtener más información.

Para obtener información acerca de cómo personalizar la fuente utilizada en una etiqueta, vea [fuentes](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Entrada](entry.md)

`Entry` se usa para aceptar la entrada de texto de una línea. `Entry` ofrece el control sobre los colores y fuentes. `Entry` tiene un modo de contraseña y puede mostrar texto de marcador de posición hasta que se escribe texto.

![Ejemplo de entrada](images/entry.png)

Consulte la [entrada](entry.md) artículo para obtener más información.

Tenga en cuenta que, a diferencia de `Label`, `Entry` no puede tener la configuración de fuente personalizados.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` se usa para aceptar la entrada de texto de varias líneas. `Editor` ofrece el control sobre los colores y fuentes.

![Ejemplo de editor](images/editor.png)

Consulte la [Editor](editor.md) artículo para obtener más información.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Fuentes](fonts.md)

Muchos controles admiten diferentes configuraciones de fuente mediante las fuentes integradas en cada plataforma o las fuentes personalizadas que se incluyen con la aplicación. Consulte la [fuentes](fonts.md) artículo para obtener más información.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Estilos](styles.md)

Consulte [trabajar con estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener información sobre cómo configurar la fuente, [color](~/xamarin-forms/user-interface/colors.md)y otras propiedades de presentación que se aplican a través de varios controles.

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
