---
title: Libros interactivos
description: En este documento se describe cómo usar Xamarin Workbooks para crear documentos activos C# que contengan código para experimentar, enseñar, entrenar o explorar.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7b3c356460d9427821843dc084b3f306c026ffa0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293084"
---
# <a name="interactive-workbooks"></a>Libros interactivos

Puede usar libros como aplicación independiente, independientes del IDE.

Para empezar a crear un nuevo libro, ejecute la aplicación Workbooks. Si todavía no lo ha instalado, visite la página de [instalación](~/tools/workbooks/install.md#install) . Se le pedirá que cree un libro en la plataforma de su elección, que se conectará automáticamente a una aplicación de agente, lo que le permitirá visualizar el documento en tiempo real.

Si ya se está ejecutando la aplicación Workbooks, puede crear un nuevo documento examinando el **archivo > nuevo**.

Los libros se pueden guardar y abrir de nuevo más adelante dentro de la aplicación. Después, puede compartirlos con otros usuarios para mostrar ideas, explorar nuevas API o enseñar nuevos conceptos.

## <a name="code-editing"></a>Edición de código

La ventana de edición de código proporciona la finalización del código, el color de la sintaxis, el diagnóstico en directo en línea y la compatibilidad con la instrucción de varias líneas.

[![](workbook-images/inspector-0.6.0-repl-small.png "La ventana de edición de código proporciona la finalización del código, el color de la sintaxis, el diagnóstico en directo en línea y la compatibilidad con la instrucción de varias líneas.")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks se guardan en `.workbook` un archivo, que es un archivo CommonMark con algunos metadatos en la parte superior (consulte [tipos de archivo de libros](#workbooks-files-types) para obtener más detalles sobre cómo se pueden guardar los libros).

### <a name="nuget-package-support"></a>Compatibilidad con paquetes NuGet

Muchos paquetes de NuGet populares se admiten directamente en Xamarin Workbooks. Puede buscar paquetes examinando el **archivo > Agregar paquete**. Al agregar un paquete, se incorporarán automáticamente las instrucciones que hacen referencia a los ensamblados del paquete, lo que le permite usarlos de `#r` inmediato.

Al guardar un libro con referencias de paquete, esas referencias también se guardan. Si comparte el libro con otra persona, se descargarán automáticamente los paquetes a los que se hace referencia.

Hay algunas limitaciones conocidas con la compatibilidad con paquetes NuGet en los libros de:

- Las bibliotecas nativas solo se admiten en iOS y solo cuando se vinculan a la biblioteca administrada.
- Los paquetes que dependen `.targets` de archivos o scripts de PowerShell probablemente no funcionarán según lo previsto.
- Para quitar o modificar una dependencia del paquete, edite el manifiesto del libro con un editor de texto. La administración correcta de paquetes está en camino.

### <a name="xamarinforms-support"></a>Compatibilidad con Xamarin. Forms

Si hace referencia al paquete de NuGet de Xamarin. Forms en el libro, la aplicación del libro cambiará la vista principal para que esté basada en Xamarin. Forms. Puede tener acceso a ella `Xamarin.Forms.Application.Current.MainPage`a través de.

La pestaña ver inspector también tiene compatibilidad especial para mostrar la jerarquía de vistas de Xamarin. Forms para ayudarle a comprender los diseños.

## <a name="rich-text-editing"></a>Edición de texto enriquecido

Puede editar el texto alrededor del código mediante el editor de texto enriquecido incluido, como se muestra a continuación:

![](workbook-images/inspector-0.6.2-editing.gif "Editar el texto alrededor del código mediante el editor de texto enriquecido integrado")

### <a name="markdown-authoring"></a>Creación de Markdown

A veces, es posible que los autores de libros le resulten más fáciles de editar directamente el "origen" de CommonMark del libro con su editor favorito.

Tenga en cuenta que si después edita y guarda el libro en el cliente de los libros, es posible que se vuelva a dar formato al texto de CommonMark.

Tenga en cuenta que, debido a la extensión CommonMark que usamos para habilitar los metadatos de `---` yaml en los archivos de libro, está reservada para ese propósito. Si desea crear [saltos temáticos](http://spec.commonmark.org/0.27/#thematic-break) en el texto, debe usar `***` o `___` en su lugar. Tales interrupciones deben evitarse en los libros 1,2 y anteriores debido a un error durante el guardado.

### <a name="improvements-in-workbooks-13"></a>Mejoras en los libros 1,3

También hemos ampliado ligeramente la sintaxis de la cotización de bloques de Markdown para mejorar la presentación. Al agregar un Emoji como primer carácter de la comilla de bloque, puede influir en el color de fondo de la oferta:

- `> [!NOTE]`
    > se representará como una nota con un fondo azul
- `> [!IMPORTANT]`
    > se representará como una advertencia con un fondo amarillo
- `> [!WARNING]`
    > se representará como un problema con un fondo rojo

También puede vincular a los encabezados del documento del libro. Se generan delimitadores para cada encabezado, donde el identificador de delimitador es el texto del encabezado, que se procesa como se indica a continuación:

1. El encabezado está en minúsculas.
1. Se quitan todos los caracteres excepto los alfanuméricos y los guiones.
1. Todos los espacios se sustituyen por guiones.

Esto significa que un encabezado como "importante header" obtiene un identificador de `important-header` y al que se puede vincular mediante la inserción de `#important-header` un vínculo a en el libro.

## <a name="document-structure"></a>Estructura del documento

### <a name="cell"></a>Móvil

Unidad de contenido discreta que representa el código ejecutable o Markdown. Una celda de código consta de hasta cuatro subcomponentes:

- Editor
  - Buffer
- Diagnóstico del compilador
- Salida de la consola
- Resultados de la ejecución

### <a name="editor"></a>Editor

Componente de texto interactivo de una celda. En el caso de las celdas de código, este es el editor de código real con resaltado de sintaxis, etc. En el caso de las celdas de Markdown, se trata de un editor de contenido de texto enriquecido con una barra de herramientas de creación y formato sensible al contexto.

### <a name="buffer"></a>Buffer

Contenido de texto real de un editor.

### <a name="compiler-diagnostics"></a>Diagnóstico del compilador

Los diagnósticos que se producen al compilar el código, se representan solo cuando se solicita la ejecución explícita. Se muestra inmediatamente debajo del editor de celdas.

### <a name="console-output"></a>Salida de la consola

Cualquier salida escrita como salida estándar o error estándar durante la ejecución de la celda. La salida estándar se representará en texto en negro y el error estándar se representará en texto rojo.

### <a name="execution-results"></a>Resultados de la ejecución

Las representaciones enriquecidas y potencialmente interactivas de los resultados de una celda se representarán cuando la compilación se realice correctamente, siempre que se produzca un resultado en la ejecución. Las excepciones se consideran resultados en este contexto, ya que se generan como resultado de la ejecución real de la compilación.

## <a name="workbooks-files-types"></a>Tipos de archivos de libros

### <a name="plain-files"></a>Archivos sin formato

De forma predeterminada, un libro se guarda como un `.workbook` archivo de texto sin formato que contiene texto con formato CommonMark.

### <a name="packages"></a>Paquetes

Un paquete de libro es un directorio denominado con la `.workbook` extensión.
En el buscador de Mac y en el menú de Xamarin Workbooks abrir cuadro de diálogo y archivos recientes, este directorio se reconocerá como si fuera un archivo.

El directorio debe contener un `index.workbook` archivo, que es el libro de texto sin formato real que se cargará en Xamarin Workbooks. El directorio también puede contener recursos necesarios para `index.workbook`, incluidas imágenes u otros archivos.

Si un archivo de `.workbook` texto sin formato que hace referencia a recursos de su mismo directorio se abre en los libros 0.99.3 o posterior, cuando se guarda, se convertirá `.workbook` en un paquete. Esto es cierto en Mac y Windows.

> [!NOTE]
> Los usuarios de Windows abrirán el `package.workbook\index.workbook` archivo directamente, pero de lo contrario el paquete se comportará igual que en Mac.

### <a name="archives"></a>Archivos

Los paquetes de libros, que son directorios, pueden ser difíciles de distribuir fácilmente a través de Internet. La solución es archivos de libro. Un archivo de libro es un paquete de libro comprimido con zip, denominado `.workbook` con la extensión.

A partir de los libros 1,1, al guardar un paquete de libro, el cuadro de diálogo Guardar ofrece la opción de guardar como archivo en su lugar. Los libros 1,0 no tenían forma integrada de crear o guardar archivos.

En los libros 1,0, cuando se abre un archivo de libro, se convierte de forma transparente en un paquete de libro y se perdió el archivo zip. En los libros 1,1, el archivo zip permanece. Cuando el usuario guarda el archivo, se reemplaza por un nuevo archivo zip.

Puede crear un archivo de libro manualmente haciendo clic con el botón secundario en un paquete de libro y seleccionando **comprimir** en Mac o **enviando a > carpeta comprimida (en zip)** en Windows. A continuación, cambie el nombre del archivo zip `.workbook` para que tenga una extensión de archivo. Esto solo funciona con paquetes de libro, no con archivos de libro sin formato.

## <a name="related-links"></a>Vínculos relacionados

- [Libros de bienvenida](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
