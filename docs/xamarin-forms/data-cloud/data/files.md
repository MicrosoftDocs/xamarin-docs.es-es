---
title: Control de archivos en Xamarin.Forms
description: El control de archivos con Xamarin.Forms puede lograrse mediante código en una biblioteca de .NET Standard o con recursos incrustados.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0d4e32b7bf98758f12dc038e0b61ffa0132f234d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529225"
---
# <a name="file-handling-in-xamarinforms"></a>Control de archivos en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)

_El control de archivos con Xamarin.Forms puede lograrse mediante código en una biblioteca de .NET Standard o con recursos incrustados._

## <a name="overview"></a>Información general

El código de Xamarin.Forms se ejecuta en varias plataformas, cada una con su propio sistema de archivos. Anteriormente, esto significaba que las acciones de leer y escribir en archivos se realizaban con mayor facilidad mediante las API de archivo nativas de cada plataforma. Como alternativa, los recursos incrustados son una solución más sencilla para distribuir archivos de datos con una aplicación. Pero, con .NET Standard 2.0, se puede compartir el código de acceso a archivos en bibliotecas de .NET Standard.

Para obtener información sobre cómo controlar archivos de imagen, vea la página [Trabajar con imágenes](~/xamarin-forms/user-interface/images.md).

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Guardar y cargar archivos

Las clases `System.IO` pueden usarse para acceder al sistema de archivos en cada plataforma. La clase `File` le permite crear, eliminar y leer archivos, mientras que la clase `Directory` le permite crear, eliminar o enumerar el contenido de directorios. También puede usar la subclases `Stream`, que pueden proporcionar un mayor nivel de control sobre las operaciones de archivo (como la compresión o la búsqueda de ubicaciones en un archivo).

Un archivo de texto se puede escribir con el método `File.WriteAllText`:

```csharp
File.WriteAllText(fileName, text);
```

Un archivo de texto se puede leer con el método `File.ReadAllText`:

```csharp
string text = File.ReadAllText(fileName);
```

Además, el método `File.Exists` determina si existe el archivo especificado:

```csharp
bool doesExist = File.Exists(fileName);
```

La ruta del archivo en cada plataforma puede determinarse desde una biblioteca de .NET Standard mediante el uso de un valor de la enumeración [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) como el primer argumento para el método `Environment.GetFolderPath`. Después, esto puede combinarse con un nombre de archivo con el método `Path.Combine`:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Estas operaciones se demuestran en la aplicación de ejemplo, donde se incluye una página que guarda y carga texto:

[![Guardar y cargar texto](files-images/saveandload-sml.png "Guardar y cargar archivos en la aplicación")](files-images/saveandload.png#lightbox "Saving and Loading Files in App")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Cargar archivos insertados como recursos

Para insertar un archivo en un ensamblado de **.NET Standard**, cree o agregue un archivo y asegúrese de usar esta **acción de compilación: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configurar una acción de compilación de recurso incrustado](files-images/vs-embeddedresource-sml.png "Establecer BuildAction como EmbeddedResource")](files-images/vs-embeddedresource.png#lightbox "Setting EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Archivo de texto insertado en PCL, configurando la acción de compilación del recurso incrustado](files-images/xs-embeddedresource-sml.png "Establecer BuildAction como EmbeddedResource")](files-images/xs-embeddedresource.png#lightbox "Setting EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` se usa para acceder al archivo incrustado mediante su **identificador de recurso**. De forma predeterminada, el identificador de recurso es el nombre de archivo que tiene como prefijo el espacio de nombres predeterminado para el proyecto donde se inserta (en este caso, el ensamblado es **WorkingWithFiles** y el nombre de archivo es **PCLTextResource.txt**, por lo que el identificador de recurso es `WorkingWithFiles.PCLTextResource.txt`).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

Después, la variable `text` puede usarse para mostrar el texto o, en cualquier caso, usarlo en el código. En esta captura de pantalla de la [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles), se muestra el texto representado en un control `Label`.

 [![Archivo de texto insertado en PCL](files-images/pcltext-sml.png "Archivo de texto insertado en PCL mostrado en la aplicación")](files-images/pcltext.png#lightbox "Embedded Text File in PCL Displayed in App")

Cargar y deserializar código XML es igual de sencillo. En el código siguiente, se muestra un archivo XML que se carga y se deserializa desde un recurso y, después, se enlaza a `ListView` para mostrarlo. El archivo XML contiene una matriz de objetos `Monkey` (la clase se define en el código de ejemplo).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Archivo XML insertado en PCL, mostrado en ListView](files-images/pclxml-sml.png "Archivo XML insertado en PCL mostrado en ListView")](files-images/pclxml.png#lightbox "Embedded XML File in PCL Displayed in ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Insertar en proyectos compartidos

Los proyectos compartidos también pueden contener archivos como recursos incrustados; pero, como el contenido de un proyecto compartido se compila en los proyectos a los que se hace referencia, el prefijo usado para los identificadores de recursos del archivo incrustado puede cambiar. Esto quiere decir que el identificador de recurso para cada archivo incrustado puede ser distinto para cada plataforma.

Hay disponibles dos soluciones a este problema con los proyectos compartidos:

- **Sincronizar los proyectos**: edite las propiedades del proyecto para cada plataforma con el fin de usar el mismo nombre de ensamblado y el **mismo** espacio de nombres predeterminado. Después, este valor se puede “codificar de forma rígida” como el prefijo para los identificadores de recursos incrustados en el proyecto compartido.
- **Directivas de compilador #if**: use directivas de compilador para establecer el prefijo de identificador de recurso correcto y use ese valor para crear de forma dinámica el identificador de recurso correcto.


A continuación, se muestra un ejemplo de código de la segunda opción. Las directivas de compilador se usan para seleccionar el prefijo del recurso codificado de forma rígida (que suele ser el mismo que el espacio de nombres predeterminado del proyecto al que se hace referencia). Después, la variable `resourcePrefix` se usa para crear un identificador de recurso válido al concatenarlo con el nombre de archivo del recurso incrustado.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organización de recursos

En los ejemplos anteriores, se da por supuesto que el archivo está insertado en el directorio raíz del proyecto de la biblioteca de .NET Standard, por lo que el identificador de recurso tendría el formato **EspacioDeNombres.NombreDeArchivo.Extensión**, como `WorkingWithFiles.PCLTextResource.txt` y `WorkingWithFiles.iOS.SharedTextResource.txt`.

Los recursos incrustados se pueden organizar en carpetas. Cuando un recurso incrustado se coloca en una carpeta, el nombre de carpeta se convierte en parte del identificador de recurso (separado por puntos), de forma que el formato del identificador de recurso se convierte en **EspacioDeNombres.Carpeta.NombreDeArchivo.Extensión**. Al colocar los archivos usados en la aplicación de ejemplo en una carpeta **MyFolder**, los identificadores de recurso correspondientes serían `WorkingWithFiles.MyFolder.PCLTextResource.txt` y `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Depurar recursos incrustados

Como a veces es difícil comprender por qué no se carga un recurso específico, puede agregarse de forma temporal el siguiente código de depuración a una aplicación para ayudar a confirmar que los recursos se han configurado correctamente. En el panel **Errores**, se mostrarán todos los recursos incrustados conocidos en el ensamblado específico para ayudar a depurar los problemas de carga de recursos.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Resumen

En este artículo, se muestran algunas operaciones de archivo sencillas para guardar y cargar texto en el dispositivo y para cargar recursos incrustados. Con .NET Standard 2.0, se puede compartir el código de acceso a archivos en bibliotecas de .NET Standard.

## <a name="related-links"></a>Vínculos relacionados

- [FilesSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [Trabajar con el sistema de archivos en Xamarin.iOS](~/ios/app-fundamentals/file-system.md)

