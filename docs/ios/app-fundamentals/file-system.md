---
title: Acceso al sistema de archivos en Xamarin. iOS
description: En este documento se describe cómo trabajar con el sistema de archivos en Xamarin. iOS. Describe los directorios, la lectura de archivos, la serialización de XML y JSON, el espacio aislado de la aplicación, el uso compartido de archivos a través de iTunes, etc.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/12/2018
ms.openlocfilehash: e52f9abb31090f3acc361eb5a3f9ae2e12600b36
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653524"
---
# <a name="file-system-access-in-xamarinios"></a>Acceso al sistema de archivos en Xamarin. iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

Puede usar Xamarin. iOS y las `System.IO` clases de la biblioteca de *clases base (BCL) de .net* para tener acceso al sistema de archivos de iOS. La clase `File` le permite crear, eliminar y leer archivos, mientras que la clase `Directory` le permite crear, eliminar o enumerar el contenido de directorios. También puede utilizar `Stream` subclases, que pueden proporcionar un mayor grado de control sobre las operaciones de archivo (como la compresión o la búsqueda de posición dentro de un archivo).

iOS impone algunas restricciones sobre lo que una aplicación puede hacer con el sistema de archivos para preservar la seguridad de los datos de una aplicación y para proteger a los usuarios de las aplicaciones de Malignant. Estas restricciones forman parte del *espacio aislado* de la aplicación: un conjunto de reglas que limita el acceso de una aplicación a archivos, preferencias, recursos de red, hardware, etc. Una aplicación se limita a leer y escribir archivos dentro de su directorio particular (ubicación instalada). no puede tener acceso a los archivos de otra aplicación.

iOS también tiene algunas características específicas del sistema de archivos: ciertos directorios requieren un tratamiento especial con respecto a las copias de seguridad y las actualizaciones, y las aplicaciones también pueden compartir archivos entre sí y la aplicación de **archivos** (desde iOS 11) y a través de iTunes.

En este artículo se describen las características y restricciones del sistema de archivos de iOS e incluye una aplicación de ejemplo que muestra cómo usar Xamarin. iOS para ejecutar algunas operaciones sencillas del sistema de archivos:

[![Ejemplo de iOS que ejecuta algunas operaciones sencillas del sistema de archivos](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Acceso general a archivos

Xamarin. iOS permite usar las clases .net `System.IO` para las operaciones del sistema de archivos en iOS.

Los fragmentos de código siguientes ilustran algunas operaciones de archivo comunes. Los encontrará a continuación en el archivo **SampleCode.CS** , en la aplicación de ejemplo de este artículo.

### <a name="working-with-directories"></a>Trabajar con directorios

Este código enumera los subdirectorios del directorio actual (especificado por el parámetro "./"), que es la ubicación del ejecutable de la aplicación.
La salida será una lista de todos los archivos y carpetas que se implementan con la aplicación (que se muestra en la ventana de la consola durante la depuración).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Leer archivos

Para leer un archivo de texto, solo necesita una sola línea de código. En este ejemplo se muestra el contenido de un archivo de texto en la ventana de salida de la aplicación.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>serialización XML

Aunque trabajar con el espacio `System.Xml` de nombres completo queda fuera del ámbito de este artículo, puede deserializar fácilmente un documento XML desde el sistema de archivos mediante un StreamReader como este fragmento de código:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Para obtener más información, vea la documentación de [System. XML](xref:System.Xml) y la [serialización](xref:System.Xml.Serialization). Consulte la [documentación de Xamarin. iOS](~/ios/deploy-test/linker.md) en el vinculador; a menudo, tendrá que agregar `[Preserve]` el atributo a las clases que desea serializar.

### <a name="creating-files-and-directories"></a>Crear archivos y directorios

En este ejemplo se muestra cómo usar `Environment` la clase para tener acceso a la carpeta documentos donde se pueden crear archivos y directorios.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

La creación de un directorio es un proceso similar:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Para obtener más información, consulte la referencia de la [API de System.IO](xref:System.IO).

### <a name="serializing-json"></a>Serializar JSON

[JSON.net](http://www.newtonsoft.com/json) es un marco JSON de alto rendimiento que funciona con Xamarin. iOS y está disponible en NuGet. Agregue el paquete NuGet al proyecto de aplicación mediante **Agregar NuGet** en Visual Studio para Mac:

[![](file-system-images/json01.png "Agregar el paquete NuGet al proyecto de aplicaciones")](file-system-images/json01.png#lightbox)

A continuación, agregue una clase para que actúe como modelo de datos para la serialización o deserialización ( `Account.cs`en este caso):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

Por último, cree una instancia de `Account` la clase, serialice en datos JSON y escríbala en un archivo:

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

Para obtener más información sobre cómo trabajar con datos JSON en una aplicación .NET, vea la [documentación](http://www.newtonsoft.com/json/help)de JSON. net.

## <a name="special-considerations"></a>Consideraciones especiales

A pesar de las similitudes entre las operaciones de archivos de Xamarin. iOS y .NET, iOS y Xamarin. iOS difieren de .NET en algunos aspectos importantes.

### <a name="making-project-files-accessible-at-runtime"></a>Crear archivos de proyecto accesibles en tiempo de ejecución

De forma predeterminada, si agrega un archivo al proyecto, no se incluirá en el ensamblado final y, por tanto, no estará disponible para la aplicación. Para incluir un archivo en el ensamblado, debe marcarlo con una acción de compilación especial, denominada contenido.

Para marcar un archivo para su inclusión, haga clic con el botón derecho en los archivos y elija compilar **contenido de acción &gt;**  en Visual Studio para Mac. También puede cambiar la **acción de compilación** en la hoja de **propiedades** del archivo.

### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Es importante comprender que el sistema de archivos de iOS distingue *entre mayúsculas*y minúsculas. La distinción de mayúsculas y minúsculas significa que los nombres de archivos y directorios deben coincidir exactamente: **README. txt** y **README. txt** se considerarían nombres de archivo diferentes.

Esto puede resultar confuso para los desarrolladores de .NET que están más familiarizados con el sistema de archivos de Windows, que no distingue *entre mayúsculas* y minúsculas: los **archivos**, **archivos**y **archivos** harían todo en el mismo directorio.

> [!WARNING]
> El simulador de iOS no distingue entre mayúsculas y minúsculas.
> Si la grafía del nombre de archivo difiere entre el propio archivo y las referencias a él en el código, es posible que el código siga funcionando en el simulador, pero producirá un error en un dispositivo real. Esta es una de las razones por las que es importante implementar y probar en un dispositivo real de forma temprana y frecuente durante el desarrollo de iOS.

### <a name="path-separator"></a>Separador de ruta de acceso

iOS usa la barra diagonal "/" como separador de ruta de acceso (que es diferente de Windows, que usa la barra diagonal inversa "\").

Debido a esta diferencia confusa, se recomienda usar el `System.IO.Path.Combine` método, que se ajusta a la plataforma actual, en lugar de codificar un separador de ruta de acceso determinado. Este es un paso sencillo que hace que el código sea más portátil para otras plataformas.

## <a name="application-sandbox"></a>Espacio aislado de la aplicación

El acceso de la aplicación al sistema de archivos (y otros recursos, como la red y las características de hardware) está limitado por motivos de seguridad. Esta restricción se conoce como *espacio aislado*de la aplicación. En lo que respecta al sistema de archivos, la aplicación se limita a crear y eliminar archivos y directorios en su directorio particular.

El directorio particular es una ubicación única en el sistema de archivos donde se almacena la aplicación y todos sus datos. No puede elegir (o cambiar) la ubicación del directorio principal de la aplicación; sin embargo, iOS y Xamarin. iOS proporcionan propiedades y métodos para administrar los archivos y directorios dentro de.

## <a name="the-application-bundle"></a>La agrupación de aplicaciones

La *agrupación de aplicaciones* es la carpeta que contiene la aplicación.
Se distingue de otras carpetas con el sufijo. app agregado al nombre del directorio. El paquete de aplicación contiene el archivo ejecutable y todo el contenido (archivos, imágenes, etc.) necesario para el proyecto.

Al ir a la agrupación de aplicaciones en Mac OS, aparece con un icono diferente del que se ve en otros directorios (y el sufijo **. app** está oculto). sin embargo, es simplemente un directorio normal en el que el sistema operativo se muestra de forma diferente.

Para ver el paquete de aplicaciones para el código de ejemplo, haga clic con el botón derecho en el proyecto en **Visual Studio para Mac** y seleccione **Mostrar en Finder**. A continuación, navegue hasta el directorio **bin/** donde debe encontrar un icono de aplicación (similar a la siguiente captura de pantalla).

![Desplácese por el directorio bin para encontrar un icono de aplicación similar a esta captura de pantalla](file-system-images/40-bundle.png)

Haga clic con el botón derecho en este icono y elija **Mostrar contenido del paquete** para examinar el contenido del directorio de la agrupación de aplicaciones. El contenido aparece exactamente igual que el contenido de un directorio normal, como se muestra aquí:

[![Contenido del lote de aplicaciones](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

La agrupación de aplicaciones es lo que se instala en el simulador o en el dispositivo durante las pruebas y, en última instancia, es lo que se envía a Apple para su inclusión en el App Store.

## <a name="application-directories"></a>Directorios de aplicación

Cuando la aplicación se instala en un dispositivo, el sistema operativo crea un directorio particular para la aplicación y crea una serie de directorios en el directorio raíz de la aplicación que están disponibles para su uso. Desde iOS 8, los directorios accesibles para el usuario [no se encuentran](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) en la raíz de la aplicación, por lo que no puede derivar las rutas de acceso para la agrupación de aplicaciones desde los directorios de usuario, o viceversa.

A continuación se enumeran estos directorios, cómo determinar su ruta de acceso y sus propósitos:

&nbsp;

|Directorio|DESCRIPCIÓN|
|---|---|
|[ApplicationName].app/|**En iOS 7 y versiones anteriores**, este es `ApplicationBundle` el directorio donde se almacena el archivo ejecutable de la aplicación. La estructura de directorios que se crea en la aplicación existe en este directorio (por ejemplo, imágenes y otros tipos de archivo que se han marcado como recursos en el proyecto de Visual Studio para Mac).<br /><br />Si necesita tener acceso a los archivos de contenido dentro de la agrupación de aplicaciones, la ruta de acceso a `NSBundle.MainBundle.BundlePath` este directorio está disponible a través de la propiedad.|
|Documentos|Use este directorio para almacenar los documentos de usuario y los archivos de datos de la aplicación.<br /><br />El contenido de este directorio puede ponerse a disposición del usuario a través del uso compartido de archivos de iTunes (aunque esta opción está deshabilitada de forma predeterminada). Agregue una `UIFileSharingEnabled` clave booleana al archivo info. plist para permitir que los usuarios tengan acceso a estos archivos.<br /><br />Incluso si una aplicación no habilita el uso compartido de archivos de inmediato, debe evitar colocar los archivos que deben ocultarse de los usuarios en este directorio (por ejemplo, los archivos de base de datos, a menos que desee compartirlos). Siempre que los archivos confidenciales permanezcan ocultos, estos archivos no se expondrán (y, potencialmente, se moverán, modificarán o eliminarán en iTunes) si el uso compartido de archivos está habilitado en una versión futura.<br /><br /> Puede usar el `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` método para obtener la ruta de acceso al directorio de documentos de la aplicación.<br /><br />ITunes realiza una copia de seguridad del contenido de este directorio.|
|Biblioteca|El directorio Library es un buen lugar para almacenar archivos que no son creados directamente por el usuario, como bases de datos u otros archivos generados por la aplicación. El contenido de este directorio nunca se expone al usuario a través de iTunes.<br /><br />Puede crear sus propios subdirectorios en la biblioteca; sin embargo, aquí ya hay algunos directorios creados por el sistema que debe tener en cuenta, incluidas las preferencias y las cachés.<br /><br />ITunes realiza una copia de seguridad del contenido de este directorio (excepto el subdirectorio de caché). Se hará una copia de seguridad de los directorios personalizados que cree en la biblioteca.|
|Biblioteca/preferencias/|Los archivos de preferencias específicos de la aplicación se almacenan en este directorio. No cree estos archivos directamente. En su lugar, use `NSUserDefaults` la clase.<br /><br />ITunes realiza una copia de seguridad del contenido de este directorio.|
|Biblioteca/memorias caché/|El directorio de cachés es un buen lugar para almacenar archivos de datos que pueden ayudar a que se ejecute la aplicación, pero que se pueden volver a crear fácilmente. La aplicación debe crear y eliminar estos archivos según sea necesario y podrá volver a crear estos archivos si es necesario. iOS 5 también puede eliminar estos archivos (en situaciones de almacenamiento reducido), pero no lo hará mientras la aplicación se está ejecutando.<br /><br />ITunes no realiza una copia de seguridad del contenido de este directorio, lo que significa que no estará presente si el usuario restaura un dispositivo y es posible que no esté presente después de instalar una versión actualizada de la aplicación.<br /><br />Por ejemplo, en caso de que la aplicación no se pueda conectar a la red, puede usar el directorio de cachés para almacenar datos o archivos con el fin de proporcionar una buena experiencia sin conexión. La aplicación puede guardar y recuperar estos datos rápidamente mientras espera las respuestas de red, pero no es necesario realizar una copia de seguridad de ellos y se pueden recuperar o volver a crear fácilmente después de una restauración o una actualización de la versión.|
|gestión|Las aplicaciones pueden almacenar archivos temporales que solo son necesarios durante un breve período de tiempo en este directorio. Para ahorrar espacio, los archivos se deben eliminar cuando ya no se necesiten. El sistema operativo también puede eliminar archivos de este directorio cuando una aplicación no se está ejecutando.<br /><br />ITunes no realiza una copia de seguridad del contenido de este directorio.<br /><br />Por ejemplo, el directorio tmp podría usarse para almacenar archivos temporales que se descargan para su presentación al usuario (como avatars de Twitter o datos adjuntos de correo electrónico), pero que se pueden eliminar una vez que se han visualizado (y se han descargado de nuevo si son necesarios en el futuro). .|

En esta captura de pantalla se muestra la estructura de directorios en una ventana de buscador:

[![](file-system-images/08-library-directory.png "Esta captura de pantalla muestra la estructura de directorios en una ventana de Finder")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Obtener acceso a otros directorios mediante programación

El directorio y los ejemplos de archivos anteriores han `Documents` tenido acceso al directorio. Para escribir en otro directorio, debe construir una ruta de acceso con la sintaxis "..", como se muestra aquí:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Crear un directorio es similar:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Las rutas de `Caches` acceso `tmp` a los directorios y se pueden construir de la siguiente manera:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Compartir con la aplicación archivos

iOS 11 presentó la aplicación **archivos** : un explorador de archivos para iOS que permite al usuario ver e interactuar con sus archivos en iCloud y también almacenarlos con cualquier aplicación que lo admita. Para permitir que el usuario acceda directamente a los archivos de la aplicación, cree una nueva clave booleana en el archivo `LSSupportsOpeningDocumentsInPlace` **info. plist** `true`y establézcala en, como aquí:

![Establecer LSSupportsOpeningDocumentsInPlace en info. plist](file-system-images/51-supports-opening.png)

El directorio de **documentos** de la aplicación ahora estará disponible para examinar en la aplicación **archivos** . En la aplicación **archivos** , vaya a **en mi iPhone** y cada aplicación con archivos compartidos estará visible. Las capturas de pantallas siguientes muestran el aspecto de la [aplicación de ejemplo filesystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode) :

![aplicación de archivos de iOS 11](file-system-images/50-files-app-1-sml.png) ![Examinar mis archivos de iPhone](file-system-images/50-files-app-2-sml.png) ![Archivos de aplicación de ejemplo](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Compartir archivos con el usuario a través de iTunes

Los usuarios pueden tener acceso a los archivos del directorio de documentos de `Info.plist` la aplicación editando y creando una **aplicación que admita** la entrada de uso compartido de iTunes (`UIFileSharingEnabled`) en la vista de **código fuente** , como se muestra aquí:

[![Agregar la aplicación admite la propiedad de uso compartido de iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Se puede tener acceso a estos archivos en iTunes cuando el dispositivo está conectado y el usuario elige la `Apps` pestaña. Por ejemplo, en la siguiente captura de pantalla se muestran los archivos de la aplicación seleccionada compartida a través de iTunes:

[![Esta captura de pantalla muestra los archivos de la aplicación seleccionada compartida a través de iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Los usuarios solo pueden tener acceso a los elementos de nivel superior de este directorio a través de iTunes. No pueden ver el contenido de ningún subdirectorio (aunque pueden copiarlos en su equipo o eliminarlos). Por ejemplo, con GoodReader, se pueden compartir archivos PDF y EPUB con la aplicación para que los usuarios puedan leerlos en sus dispositivos iOS.

Los usuarios que modifiquen el contenido de la carpeta documentos pueden causar problemas si no son cuidados. La aplicación debe tener esto en cuenta y ser resistente a las actualizaciones destructivas de la carpeta documentos.

En el código de ejemplo de este artículo se crea un archivo y una carpeta en la carpeta documentos (en **SampleCode.CS**) y se habilita el uso compartido de archivos en el archivo **info. plist** . En esta captura de pantalla se muestra cómo aparecen en iTunes:

[![En esta captura de pantalla se muestra cómo aparecen los archivos en iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Consulte el artículo sobre cómo [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener información sobre cómo establecer iconos para la aplicación y para los tipos de documentos personalizados que cree.

Si la `UIFileSharingEnabled` clave es falsa o no está presente, el uso compartido de archivos está deshabilitado de forma predeterminada y los usuarios no podrán interactuar con el directorio de documentos.

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Cuando iTunes crea una copia de seguridad de un dispositivo, todos los directorios creados en el directorio principal de la aplicación se guardarán, excepto los siguientes directorios:

- **[ApplicationName]. app** : no escriba en este directorio, ya que está firmado y, por lo tanto, debe permanecer sin cambios después de la instalación. Puede contener recursos a los que se tiene acceso desde el código, pero no requieren copia de seguridad, ya que se restaurarán volviendo a descargar la aplicación.
- **Biblioteca/cachés** : el directorio de caché está diseñado para los archivos de trabajo que no es necesario incluir en la copia de seguridad.
- **TMP** : este directorio se usa para los archivos temporales que se crean y eliminan cuando ya no se necesitan, o para los archivos que Ios elimina cuando necesita espacio.

La copia de seguridad de una gran cantidad de datos puede tardar mucho tiempo. Si decide que necesita realizar una copia de seguridad de cualquier documento o dato determinado, la aplicación debe usar las carpetas documentos y biblioteca. En el caso de los datos transitorios o de los archivos que se pueden recuperar fácilmente de la red, use el directorio de almacenamiento en caché o tmp.

> [!NOTE]
> iOS "limpiará" el sistema de archivos cuando un dispositivo se ejecute de forma muy baja en el espacio en disco.
> Este proceso eliminará todos los archivos de la carpeta Library/caches y tmp de las aplicaciones que no se estén ejecutando actualmente.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Cumplimiento de las restricciones de copia de seguridad de iOS 5 iCloud

> [!NOTE]
> Aunque esta Directiva se presentó por primera vez con iOS 5 (que parece que hace mucho tiempo), la guía todavía es pertinente para las aplicaciones de hoy en día.

Apple presentó la funcionalidad de *copia de seguridad de icloud* con iOS 5. Cuando está habilitada la copia de seguridad de icloud, se realiza una copia de seguridad de todos los archivos del directorio principal de la aplicación (excluidos los directorios de `Caches`los que `tmp`normalmente no se realiza una copia de seguridad, por ejemplo, el paquete de aplicaciones, y) en los servidores de iCloud. Esta característica proporciona al usuario una copia de seguridad completa en caso de que su dispositivo se pierda, lo roben o dañen.

Dado que iCloud solo proporciona 5 GB de espacio libre para cada usuario y para evitar el uso innecesario de ancho de banda, Apple espera que las aplicaciones solo realicen copias de seguridad de los datos esenciales generados por el usuario. Para cumplir con las directrices de almacenamiento de datos de iOS, debe limitar la cantidad de datos de los que se realiza una copia de seguridad si cumple los siguientes elementos:

- Almacene únicamente datos generados por el usuario o datos que no se puedan volver a crear en el directorio documentos (del que se hace copia de seguridad).
- Almacene cualquier otro dato que se pueda volver a crear o volver a descargar fácilmente `Library/Caches` en `tmp` o (de los que no se realiza ninguna copia de seguridad y que se pueda "limpiar").
- Si tiene archivos que podrían ser adecuados para la `Library/Caches` carpeta o `tmp` pero no desea limpiarlos, almacénelos en otra parte (como `Library/YourData`) y aplique el atributo ' no hacer copia de seguridad ' para evitar que los archivos usen la copia de seguridad de icloud ancho de banda y espacio de almacenamiento. Estos datos todavía usan espacio en el dispositivo, por lo que debe administrarlos con cuidado y eliminarlos cuando sea posible.

El atributo ' no hacer copia de seguridad ' se establece mediante `NSFileManager` la clase. Asegúrese de que su `using Foundation` clase es `SetSkipBackupAttribute` y llame a de la siguiente manera:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Cuando no se realizará una copia de seguridad del archivo, independientemente del directorio en el que esté almacenado (incluso el `Documents` directorio). `SetSkipBackupAttribute` `true` Puede consultar el atributo mediante el `GetSkipBackupAttribute` método, y puede restablecerlo `SetSkipBackupAttribute` llamando al método con `false`, de la siguiente manera:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Compartir datos entre aplicaciones iOS y extensiones de aplicaciones

Puesto que las extensiones de aplicaciones se ejecutan como parte de una aplicación host (en lugar de la aplicación contenedora), el uso compartido de los datos no se incluye de forma automática, por lo que se requiere trabajo adicional. Los grupos de aplicaciones son el mecanismo que usa iOS para permitir que diferentes aplicaciones compartan datos. Si las aplicaciones se han configurado correctamente con los derechos y el aprovisionamiento correctos, pueden acceder a un directorio compartido fuera de su espacio aislado de iOS normal.

### <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en cada archivo **Entitlements.plist** del proyecto.

Para obtener información sobre cómo crear y configurar un grupo de aplicaciones, consulte la guía de [funcionalidades del grupo de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

### <a name="files"></a>Archivos

La aplicación iOS y la extensión también pueden compartir archivos mediante una ruta de acceso de archivo común (dado que se han configurado correctamente con los derechos y el aprovisionamiento correctos):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Si la ruta de acceso de `null`grupo devuelta es, Compruebe la configuración de los derechos y el perfil de aprovisionamiento y asegúrese de que son correctos.

## <a name="application-version-updates"></a>Actualizaciones de la versión de la aplicación

Cuando se descarga una nueva versión de la aplicación, iOS crea un nuevo directorio particular y almacena el nuevo paquete de aplicación en él. a continuación, iOS mueve las siguientes carpetas de la versión anterior del paquete de aplicaciones al nuevo directorio principal:

- **Documentos**
- **Library**

Otros directorios también se pueden copiar y colocar en el nuevo directorio particular, pero no se garantiza que se copien, por lo que la aplicación no debe basarse en este comportamiento del sistema.

## <a name="summary"></a>Resumen

En este artículo se mostraron que las operaciones del sistema de archivos con Xamarin. iOS son similares a cualquier otra aplicación de .NET. También se presentó el espacio aislado de la aplicación y se examinaron las implicaciones de seguridad que causa. A continuación, se ha explorado el concepto de agrupación de aplicaciones. Por último, se enumeran los directorios especializados disponibles para la aplicación y se han explicado sus roles durante las actualizaciones de la aplicación y las copias de seguridad.

## <a name="related-links"></a>Vínculos relacionados

- [Código de ejemplo de sistema de archivos](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)
- [Guía de programación del sistema de archivos](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registrar los tipos de archivo que admite la aplicación](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
