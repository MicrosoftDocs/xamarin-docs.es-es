---
ms.openlocfilehash: 14d4f5de500c8e8ced6cbeb67019f9152ed63df3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118906"
---
# <a name="contributing"></a>Contribución

Le agradecemos el interés por colaborar con la documentación de Xamarin.

En esta página se describe el proceso básico para actualizar contenido en la [documentación de Xamarin](https://docs.microsoft.com/xamarin).

- [Contrato de licencia del colaborador](LICENSE)

## <a name="process-for-contributing"></a>Proceso de contribución

### <a name="small-changes--edits"></a>Pequeños cambios y ediciones

Para realizar correcciones y actualizaciones menores, puede hacer clic en el botón **Editar** situado en cualquier página y usar el sitio web de GitHub para colaborar, o bien siga estos pasos:

1. Bifurque el repositorio `MicrosoftDocs/xamarin-docs`.

2. Cree una `branch` para los cambios.

3. Escriba el contenido. Consulte la [plantilla](contributing-guidelines/template.md) y la [guía de estilo](contributing-guidelines/voice-tone.md).

4. Envíe una solicitud de incorporación de cambios (PR) desde su rama a `MicrosoftDocs/xamarin-docs/live`.

5. Realice las actualizaciones necesarias que haya acordado con el equipo en la rama a través de la PR.

6. Los encargados del mantenimiento combinarán la solicitud una vez que se hayan aplicado los comentarios y el cambio no presente problemas. Aparecerá en docs.microsoft.com poco después.


> [!NOTE]
> Si la solicitud aborda un asunto existente, agregue la palabra clave `Fixes #Issue_Number` al mensaje de confirmación o a la descripción de la solicitud de incorporación de cambios. De este modo, el asunto se podrá cerrar automáticamente cuando se combine la solicitud. Para obtener más información, vea [Closing issues via commit messages](https://help.github.com/articles/closing-issues-via-commit-messages/) (Cierre de asuntos mediante mensajes de confirmación).


### <a name="big-changes-or-new-content"></a>Cambios importantes o contenido nuevo

Para colaboraciones grandes y contenido nuevo, [abra un asunto](https://github.com/MicrosoftDocs/xamarin-docs/issues) en el que se describa el artículo que quiere redactar y su relación con el contenido existente. El contenido de la carpeta docs se organiza en secciones por área de producto (por ejemplo, iOS yAndroid). Determine en qué carpeta deberá incluirse el contenido nuevo. 

**Obtenga comentarios sobre la propuesta mediante el asunto antes de comenzar a escribir.**

Si es un tema nuevo, puede usar el [archivo de plantilla](../contributing-guidelines/template.md) como punto de partida. Contiene las instrucciones de escritura y la explicación de los metadatos necesarios para cada artículo, como la información del autor.

Agregue las imágenes y otros recursos estáticos a la subcarpeta denominada **\<mypage>-images**. Si va a crear una carpeta para el contenido, agregue una carpeta de imágenes a la carpeta nueva.

#### <a name="example-structure"></a>Estructura de ejemplo

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

Asegúrese de seguir la sintaxis de Markdown adecuada. Vea la [guía de estilo](../contributing-guidelines/template.md) para obtener más información.

Los pasos de envío reales son los mismos que para los cambios menores ([anteriormente](#process-for-contributing)).

El equipo de Xamarin revisará la solicitud y le notificará (mediante comentarios de la PR) si el cambio es correcto o si se necesitan otras actualizaciones o cambios para aprobarlo.

Después, los encargados del mantenimiento combinarán la solicitud una vez que se hayan aplicado los comentarios y el cambio no presente problemas.

Las confirmaciones de la rama principal se insertan en el sitio activo con un ritmo determinado, y después podrá ver su contribución en https://docs.microsoft.com/xamarin/.

## <a name="dos-and-donts"></a>Qué hacer y qué no hacer

En la lista siguiente se incluye una serie de reglas orientativas que se deben tener en cuenta a la hora de colaborar en la documentación de .NET.

- **NO** solicite la incorporación de grandes cambios. En su lugar, registre un asunto e inicie una discusión para que podamos definir un rumbo antes de dedicar una gran cantidad de tiempo.
- **LEA** la [guía de estilo](contributing-guidelines/template.md) y las instrucciones de [voz y tono](contributing-guidelines/voice-tone.md).
- **USE** el archivo de [plantilla](contributing-guidelines/template.md) como punto de partida del trabajo.
- **CREE** una rama independiente en la bifurcación antes de trabajar en los artículos.
- **SIGA** el [flujo de trabajo de GitHub](https://guides.github.com/introduction/flow/).
- **HABLE** de sus contribuciones con frecuencia en blogs o Twitter (o cualquier otra red social).

> [!NOTE]
> Es posible que observe que algunos de los temas no siguen actualmente todas las instrucciones que se especifican aquí y en la [guía de estilo](contributing-guidelines/template.md), pero estamos trabajando para dar coherencia a todo el sitio. 


