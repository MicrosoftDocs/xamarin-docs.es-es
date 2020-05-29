---
title: Texto enXamarin.Forms
description: Xamarin.Formstiene tres vistas principales para trabajar con texto, y en este artículo se explica cómo usarlas para escribir y mostrar texto en Xamarin.Forms aplicaciones.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 579ff44e9c58d7eea538d5478e99b4c480d44ac0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136193"
---
# <a name="text-in-xamarinforms"></a>Texto enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Usar Xamarin.Forms para escribir o mostrar texto._

Xamarin.Formstiene tres vistas principales para trabajar con texto:

- **[Etiqueta](#Label)** &mdash; de para presentar texto de una o varias líneas. Puede mostrar texto con varias opciones de formato en la misma línea.
- **[Entrada](#Entry)** &mdash; de para escribir texto que solo es una línea. La entrada tiene un modo de contraseña.
- **[Editor](#Editor)** &mdash; de para escribir texto que podría tomar más de una línea.

La apariencia del texto se puede cambiar mediante [estilos](#Styles) integrados o personalizados y algunos controles admiten [fuentes](#Fonts)personalizadas.

<a name="Label" />

## <a name="label"></a>[Label](label.md)

La `Label` vista se usa para mostrar texto. Puede mostrar varias líneas de texto o una sola línea de texto. `Label`puede presentar texto con varias opciones de formato utilizadas en línea. La vista de etiqueta puede ajustar o truncar el texto cuando no cabe en una línea.

![Ejemplo de etiqueta](images/label.png)

Vea el artículo de la [etiqueta](label.md) para obtener información más detallada.

Para obtener información sobre cómo personalizar la fuente utilizada en una etiqueta, vea [fuentes](fonts.md).

<a name="Entry" />

## <a name="entry"></a>[Entrada](entry.md)

`Entry`se usa para aceptar la entrada de texto de una sola línea. `Entry`ofrece control sobre colores y fuentes. `Entry`tiene un modo de contraseña y puede mostrar texto de marcador de posición hasta que se escriba texto.

![Ejemplo de entrada](images/entry.png)

Vea el artículo de [entrada](entry.md) para obtener más información.

Tenga en cuenta que, a diferencia `Label` de, `Entry` no puede tener una configuración de fuente personalizada.

<a name="Editor" />

## <a name="editor"></a>[Editor](editor.md)

`Editor`se usa para aceptar la entrada de texto de varias líneas. `Editor`ofrece control sobre colores y fuentes.

![Ejemplo de editor](images/editor.png)

Vea el artículo sobre el [Editor](editor.md) para obtener más información.

<a name="Fonts" />

## <a name="fonts"></a>[Fuentes](fonts.md)

Muchos controles admiten diferentes configuraciones de fuente mediante las fuentes integradas en cada plataforma o las fuentes personalizadas que se incluyen con la aplicación. Consulte el artículo [fuentes](fonts.md) para obtener información más detallada.

<a name="Styles" />

## <a name="styles"></a>[Estilos](styles.md)

Consulte [trabajar con estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener información sobre cómo configurar la fuente, el [color](~/xamarin-forms/user-interface/colors.md)y otras propiedades de presentación que se aplican en varios controles.

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
