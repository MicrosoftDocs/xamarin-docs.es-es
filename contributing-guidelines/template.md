---
title: El título de este artículo
description: Una buena y sólida descripción de este artículo. El contenido que se guarda aquí es lo que devuelve el sistema Microsoft Docs con los resultados de búsqueda, por lo que es mejor que sea una descripción completa del propósito y el contenido de este archivo de documento.
keywords: palabra clave, lista, para, interno, búsqueda
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: 50bcdcc23d1291071a6045544faad9388755116b
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225466"
---
# <a name="metadata-and-markdown-template"></a>Plantilla de Markdown y metadatos

Esta plantilla ejemplos de la sintaxis Markdown, así como instrucciones sobre cómo establecer los metadatos. Para sacar el máximo provecho de ella, debe ver el **Markdown sin formato** y la **vista representada** (por ejemplo, el Markdown sin formato muestra el bloque de metadatos, mientras que la vista representada no).

Al crear un archivo de Markdown, debe copiar la plantilla en un archivo nuevo, rellenar los metadatos como se indica a continuación, configurar el encabezado H1 como se indica antes del título de este artículo y eliminar el contenido.

## <a name="metadata"></a>Metadatos

El bloque de metadatos completo, con un montón de anotaciones, se muestra a continuación:

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

Algunas notas claves:

- **Debe** haber un espacio entre los dos puntos (:) y el valor de un elemento de metadatos.
- Si un elemento de metadatos opcional no tiene un valor, elimínelo (no lo deje en blanco ni use "nd").
- Dos puntos en un valor (por ejemplo, un título) interrumpen el analizador de metadatos. En este caso, encierre el título con comillas dobles (por ejemplo, `title: "Writing .NET Core console apps: An advanced step-by-step guide"`).
- **title**: este título aparecerá en los resultados del motor de búsqueda. El título no debe ser idéntico al título en el encabezado H1 y debe contener 60 caracteres o menos.
- **author**, **manager** y **ms.reviewer**: el campo author debe contener el **nombre de usuario de GitHub** del autor, no su alias.  Los campos "administrador" y "ms.reviewer", por otro lado, deben contener los alias de Microsoft. ms.reviewer especifica el nombre del PM o desarrollador asociado con el artículo o característica.
- **ms.assetid**: este es el GUID del artículo que se utiliza para fines de seguimiento interno, como Inteligencia empresarial (BI). Al crear un nuevo archivo Markdown, se obtiene un GUID de [https://www.guidgenerator.com](https://www.guidgenerator.com).

## <a name="basic-markdown-gfm-and-special-characters"></a>Markdown básico, GFM y caracteres especiales

Todos los Markdown básicos y característicos de GitHub (GFM) son compatibles. Para obtener más información sobre este tema, consulte:

- [Sintaxis de Markdown de línea de base](https://daringfireball.net/projects/markdown/syntax)
- [Markdown característico de DocFX](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [Documentación de GFM](https://guides.github.com/features/mastering-markdown)

Markdown utiliza caracteres especiales como \*, \` y \# para dar formato. Si desea incluir uno de estos caracteres en el contenido, tiene dos opciones disponibles:

- Colocar una barra diagonal inversa antes del carácter especial para "escapar" (por ejemplo, `\*` para un \*)
- Utilizar el [código de entidad HTML](http://www.ascii.cl/htmlcodes.htm) para el carácter (por ejemplo, `&#42;` para un *).

## <a name="file-name"></a>Nombre del archivo

Los nombres de archivo utilizan las siguientes reglas:
- Solo contienen letras minúsculas, números y guiones.
- No incluyen espacios ni signos de puntuación. Se usan guiones para separar palabras y números en el nombre de archivo.
- Se usan verbos de acción de uso específicos, como desarrollar, comparar, compilar, solucionar problemas. Ninguna palabra en gerundio.
- No se incluye ninguna palabra pequeña. No incluya a y, el, en, o, etcétera.
- Debe estar en Markdown y utilizar la extensión de archivo .md.
- Los nombres de archivo deben ser razonablemente cortos. Forman parte de la dirección URL para los artículos.



## <a name="headings"></a>Encabezados

Utilice mayúsculas y minúsculas de estilo de oración. Utilice siempre mayúscula en:
- La primera palabra de un encabezado.
- La palabra que sigue a un signo de dos puntos en un título o un encabezado (por ejemplo, "Cómo: Ordenar una matriz").

Los encabezados deben realizarse con estilo atx, es decir, usar de uno a seis caracteres hash (#) al principio de la línea para indicar un título, correspondiente a los niveles de encabezados HTML de H1 a H6. Más arriba se pueden encontrar ejemplos de encabezados de primer y segundo nivel.

Solo **debe** haber un encabezado de primer nivel (H1) en el tema, que se mostrará como título en la página.

Si el encabezado finaliza con un carácter `#`, debe agregar un carácter `#` adicional al final para que el título se represente correctamente. Por ejemplo: `# Async Programming in F# #`.

Los encabezados de segundo nivel generarán la tabla de contenido en la página que aparece en la sección "En este artículo" debajo del título en la página.

### <a name="third-level-heading"></a>Encabezado de tercer nivel
#### <a name="fourth-level-heading"></a>Encabezado de cuarto nivel
##### <a name="fifth-level-heading"></a>Encabezado de quinto nivel
###### <a name="sixth-level-heading"></a>Encabezado de sexto nivel

## <a name="text-styling"></a>Estilo del texto

*Cursiva* Se usa para archivos, carpetas, rutas de acceso (para elementos largos, divididos en su propia línea), términos nuevos y direcciones URL (a menos que se representen como vínculos, que es el valor predeterminado).

**Negrita** Se utiliza para elementos de la UI.

## <a name="links"></a>vínculos

### <a name="internal-links"></a>Vínculos internos

Para vincular a un encabezado en el mismo archivo de Markdown (también conocido como vínculos de anclaje), debe obtener el identificador del encabezado al que está intentando vincular. Para confirmar el identificador, vea el origen del artículo representado, busque el identificador del encabezado (por ejemplo, `id="blockquote"`) y vincúlelo con # + identificador (por ejemplo, `#blockquote`).
El identificador se genera automáticamente basándose en el texto del encabezado. De esta manera, por ejemplo, dada una única sección denominada `## Step 2`, el identificador sería similar a `id="step-2"`.

- Ejemplo: `[Chapter 1](#chapter-1)`

Para vincular a un archivo de Markdown en el mismo repositorio, utilice los [vínculos relativos](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2), incluyendo el ".md" al final del nombre de archivo.

- Ejemplo: `[Readme file](../readme.md)`
- Ejemplo: `[Welcome to .NET](../docs/welcome.md)`

Para enlazar a un fichero de Markdown en el mismo repositorio, utilice el enlace relativo + enlace hashtag.

- Ejemplo: `[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>Vínculos externos

Para vincular a un archivo externo, utilice la dirección URL completa como vínculo.

- Ejemplo: `[GitHub](http://www.github.com)`

Si aparece una dirección URL en un archivo de Markdown, se transformará en un vínculo.

- Ejemplo: `http://www.github.com`

### <a name="links-to-apis"></a>Vínculos a las API

El sistema de compilación tiene algunas extensiones que permiten vincular a las API de .NET sin tener que usar los vínculos externos.
Al vincular a una API, puede usar su identificador único (UID) que se genera automáticamente desde el código fuente.

Puede utilizar alguna de las siguientes sintaxis:
1. Vínculo de Markdown: `[link_text](xref:UID)`
2. Vínculo automático: `<xref:UID>`
3. Forma abreviada: `@UID`

- Ejemplo: `@System.String`
- Ejemplo: `[String class](xref:System.String)`

Para obtener más información acerca de cómo utilizar esta notación, consulte [Utilizar referencias cruzadas](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference).

> En este momento, no hay ninguna manera fácil de buscar los UID. La mejor manera de encontrar el UID de una API es buscar en este repositorio: [docascode/coreapi](https://github.com/docascode/coreapi). Estamos trabajando para tener un sistema mejor en el futuro.

Cuando el UID contiene los caracteres especiales \` o \#, el valor UID debe ser HTML codificado como %60 y %23 respectivamente, como en los ejemplos siguientes:
- Ejemplo: @System.Threading.Tasks.Task\`1 se convierte en `@System.Threading.Tasks.Task%601`
- Ejemplo: @System.Exception.\#ctor se convierte en `@System.Exception.%23ctor`

## <a name="lists"></a>Listas

### <a name="ordered-lists"></a>Listas ordenadas

1. Esto
1. Es
1. Una
1. Por orden
1. Lista


#### <a name="ordered-list-with-an-embedded-list"></a>Lista ordenada con una lista insertada

1. Aquí
1. viene
1. an
1. insertada
    1. Señora Beatriz
    1. Profesor Alcalá
1. ordered
1. lista


### <a name="unordered-lists"></a>Listas desordenadas

- Esto
- is
- a
- con viñetas
- lista


##### <a name="unordered-list-with-an-embedded-list"></a>Lista desordenada con una lista insertada

- Esto
- con viñetas
- lista
  - Sr. Valladares
  - Sr. Tórrez
- contains
- otras
    1. Coronel Valentín
    1. Sra. Blanco
- listas


## <a name="horizontal-rule"></a>Regla horizontal

---

## <a name="tables"></a>Tablas

| Tablas        | Son           | Geniales  |
| ------------- |:-------------:| -----:|
| col 3 está      | alineado a la derecha | 1600 $ |
| col 2 está      | centrado      |   $12 |
| col 1 está de forma predeterminada | alineado a la izquierda     |    1 $ |

Puede usar una [herramienta de generador de tabla de Markdown](http://www.tablesgenerator.com/markdown_tables) para ayudarle a crearlas más fácilmente.


### <a name="inline-code-blocks-with-language-identifier"></a>Bloques de código alineados con el identificador de idioma

Utilice tres acentos graves (\`\`\`) + un identificador de lenguaje para aplicar la codificación de color específica del lenguaje a un bloque de código. Esta es la lista completa de [identificadores de lenguaje de GFM](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs).

##### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>Bloque de código genérico

Utilice tres acentos graves (```) para la codificación de bloques de código genéricos.

> El enfoque recomendado es utilizar los bloques de código con los identificadores de lenguaje, como se explica en la sección anterior, para garantizar el resaltado de sintaxis correcto en la documentación del sitio. Utilice bloques de código genéricos solo cuando sea necesario.

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>Código alineado

Use acentos graves (`) para `inline code`. Utilice el código alineado para comandos de línea de comandos, los nombres de columna y de tabla de base, y las palabras clave del lenguaje.

## <a name="blockquotes"></a>Blockquotes

> La sequía lleva existiendo diez millones de años y el reino de los terribles lagartos hace mucho que finalizó. Aquí en el Ecuador, en el continente que será un día conocido como África, la batalla por la existencia ha alcanzado un nuevo clímax de ferocidad, y el ganador aún no se ha mostrado. En este terreno estéril y desecado, solo los pequeños, los rápidos o los feroces podrían prosperar, o incluso esperar sobrevivir.

## <a name="images"></a>Imágenes

### <a name="static-image-or-animated-gif"></a>Gif animado o imagen estática

```
![this is the alt text](images/dotnet.png)
```

![Diseño con capacidad de respuesta](images/responsivedesign.gif)

### <a name="linked-image"></a>Imagen vinculada

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>Vídeos

### <a name="channel-9"></a>Channel 9

Utilice esta sintaxis especial de blockquote:

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

la dirección URL del vídeo de Channel 9 debe comenzar con `https` y terminar con `/player`. De lo contrario, se insertará toda la página en lugar de solo el vídeo.

### <a name="youtube"></a>YouTube

Utilice esta sintaxis especial de blockquote:

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

El vídeo de YouTube debe contener `/embed/` (es decir, siga el formato anterior). De lo contrario, intentará representar toda la página y puede que no funcione.

## <a name="docsmicrosoft-extensions"></a>Extensiones de docs.microsoft

docs.microsoft proporciona algunas extensiones adicionales a Markdown característico de GitHub.

### <a name="alerts"></a>Alertas

Es importante usar los siguientes estilos de alerta, por lo que se representan con el estilo correspondiente en el sitio de documentación. Sin embargo, el motor de representación en GitHub no los diferencia.

#### <a name="note"></a>Nota

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>Advertencia

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>Sugerencia

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>Importante

```
> [!IMPORTANT]
> This is IMPORTANT
```

Y se procesarán como esto:

![estilos de alerta](images/alerts.png)

### <a name="buttons"></a>Botones

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

Puede ver un ejemplo de botones en acción en los [documentos de Intune](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices).

### <a name="selectors"></a>Selectores

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

Puede ver un ejemplo de selectores en acción en los [documentos de Intune](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps).

### <a name="step-by-steps"></a>Paso a paso

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

Puede ver un ejemplo paso a paso en acción en los [documentos de Advanced Threat Analytics](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2).
