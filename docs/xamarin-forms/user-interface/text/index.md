---
title: Texto en Xamarin.Forms
description: Xamarin.Forms tiene tres vistas principales para trabajar con texto, y en este artículo se explica cómo usarlas para escribir y mostrar texto en Xamarin.Forms aplicaciones.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1ad1d4fa9d5ce006373382e85398edac59a82a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557600"
---
# <a name="text-in-no-locxamarinforms"></a>Texto en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Usar Xamarin.Forms para escribir o mostrar texto._

Xamarin.Forms tiene tres vistas principales para trabajar con texto:

- **[Etiqueta](#label)** &mdash; de para presentar texto de una o varias líneas. Puede mostrar texto con varias opciones de formato en la misma línea.
- **[Entrada](#entry)** &mdash; de para escribir texto que solo es una línea. La entrada tiene un modo de contraseña.
- **[Editor](#editor)** &mdash; de para escribir texto que podría tomar más de una línea.

La apariencia del texto se puede cambiar mediante [estilos](#styles) integrados o personalizados y algunos controles admiten [fuentes](#fonts)personalizadas.

## <a name="label"></a>[Label](label.md)

La `Label` vista se usa para mostrar texto. Puede mostrar varias líneas de texto o una sola línea de texto. `Label` puede presentar texto con varias opciones de formato utilizadas en línea. La vista de etiqueta puede ajustar o truncar el texto cuando no cabe en una línea.

![Ejemplo de etiqueta](images/label.png)

Vea el artículo de la [etiqueta](label.md) para obtener información más detallada.

Para obtener información sobre cómo personalizar la fuente utilizada en una etiqueta, vea [fuentes](fonts.md).

## <a name="entry"></a>[Entrada](entry.md)

`Entry` se usa para aceptar la entrada de texto de una sola línea. `Entry` ofrece control sobre colores y fuentes. `Entry` tiene un modo de contraseña y puede mostrar texto de marcador de posición hasta que se escriba texto.

![Ejemplo de entrada](images/entry.png)

Vea el artículo de [entrada](entry.md) para obtener más información.

Tenga en cuenta que, a diferencia `Label` de, `Entry` no puede tener una configuración de fuente personalizada.

## <a name="editor"></a>[Editor](editor.md)

`Editor` se usa para aceptar la entrada de texto de varias líneas. `Editor` ofrece control sobre colores y fuentes.

![Ejemplo de editor](images/editor.png)

Vea el artículo sobre el [Editor](editor.md) para obtener más información.

## <a name="fonts"></a>[Fuentes](fonts.md)

Muchos controles admiten diferentes configuraciones de fuente mediante las fuentes integradas en cada plataforma o las fuentes personalizadas que se incluyen con la aplicación. Consulte el artículo [fuentes](fonts.md) para obtener información más detallada.

## <a name="styles"></a>[Estilos](styles.md)

Consulte [trabajar con estilos](~/xamarin-forms/user-interface/styles/index.md) para obtener información sobre cómo configurar la fuente, el [color](~/xamarin-forms/user-interface/colors.md)y otras propiedades de presentación que se aplican en varios controles.

## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)