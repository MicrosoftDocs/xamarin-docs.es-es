---
title: 'Parte 5: Estrategias prácticas de uso compartido de código'
description: Este documento describen estrategias para escenarios como el código asincrónico, acceso a archivos, las operaciones de red y las bases de datos de uso compartido de código práctico.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e7739642bdb9a10046286035c4aa1a3699bb75b0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526781"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5: Estrategias prácticas de uso compartido de código

En esta sección se proporciona ejemplos de cómo compartir código para escenarios de aplicación comunes.



## <a name="data-layer"></a>Capa de datos

La capa de datos consta de un motor de almacenamiento y los métodos para leer y escribir información. Para la compatibilidad multiplataforma, flexibilidad y rendimiento del SQLite motor de base de datos se recomienda para las aplicaciones multiplataforma de Xamarin.
Se ejecuta en una amplia variedad de plataformas, como Windows, Android, iOS y Mac.


### <a name="sqlite"></a>SQLite

SQLite es una implementación de la base de datos de código abierto. El código fuente y documentación que pueden encontrarse en [SQLite.org](http://www.sqlite.org/). SQLite es compatible con cada plataforma móvil:

- **iOS** – integrado el sistema operativo.
- **Android** – integrado en el sistema operativo desde Android 2.2 (nivel de API 10).
- **Windows** : consulte la [SQLite para la extensión de la plataforma Universal de Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).


Incluso con el motor de base de datos disponible en todas las plataformas, los métodos nativos para tener acceso a la base de datos son diferentes. Tanto iOS y Android ofrecen API integradas para acceder a SQLite que podría usarse desde Xamarin.iOS o Xamarin.Android, sin embargo utilizando los métodos SDK nativos no ofrece ninguna capacidad de compartir código (excepto quizás las consultas SQL por sí mismos, suponiendo que se almacenan como cadenas) . Para obtener más información sobre la búsqueda de funcionalidad de base de datos nativa para `CoreData` en iOS o Android `SQLiteOpenHelper` clase; dado que estas opciones no son multiplataforma están fuera del ámbito de este documento.



### <a name="adonet"></a>ADO.NET

Compatibilidad con Xamarin.iOS y Xamarin.Android `System.Data` y `Mono.Data.Sqlite` (consulte Xamarin.iOS [documentación](~/ios/data-cloud/system.data.md) para obtener más información).
Uso de estos espacios de nombres le permite escribir código de ADO.NET que funciona en ambas plataformas. Editar las referencias del proyecto para incluir `System.Data.dll` y `Mono.Data.Sqlite.dll` y agregue las siguientes instrucciones using a su código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

El código de ejemplo siguiente funcionará:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

Las implementaciones reales de ADO.NET obviamente se dividirá entre los distintos métodos y clases (en este ejemplo es solo con fines de demostración).



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-ORM multiplataforma

Un ORM (o un asignador relacional de objetos) intenta simplificar el almacenamiento de datos que se modela en clases. En lugar de manualmente escribiendo consultas SQL que cree las tablas o seleccione, campos de datos INSERT y DELETE que se extraen manualmente desde la clase y propiedades, un ORM agrega una capa de código que lo hace por usted. Usar la reflexión para examinar la estructura de las clases, un ORM puede crear automáticamente las tablas y columnas que coinciden con una clase y generan consultas para leer y escribir los datos. Esto permite que el código de aplicación simplemente enviar y recuperar las instancias de objeto a la ORM, que se encarga de todas las operaciones de SQL bajo el capó.

SQLite-NET actúa como un simple ORM que le permitirá guardar y recuperar las clases de SQLite. Oculta la complejidad de cross platform SQLite acceso con una combinación de directivas de compilador y otros trucos.

Características de SQLite-NET:

- Las tablas se definen mediante la adición de atributos a clases de modelo.
- Una instancia de base de datos está representada por una subclase de `SQLiteConnection` , la clase principal de la biblioteca de red de SQLite.
- Datos se pueden insertar, consultar y eliminar con objetos. Ninguna de las instrucciones SQL es necesaria (aunque puede escribir instrucciones SQL si es necesario).
- Consultas básicas de Linq se pueden realizar en las colecciones devueltas por SQLite-NET.


Está disponible en el código fuente y documentación de SQLite-NET [SQLite-Net en github](https://github.com/praeclarum/sqlite-net) y se ha implementado en ambos casos de estudio. Un ejemplo sencillo de código de SQLite-NET (desde el *Tasky Pro* caso práctico) se muestra a continuación.

En primer lugar, el `TodoItem` clase utiliza atributos para definir un campo para que sea una clave principal de la base de datos:

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Esto permite un `TodoItem` tabla que se crearán con la siguiente línea de código (y no hay instrucciones SQL) en un `SQLiteConnection` instancia:

```csharp
CreateTable<TodoItem> ();
```

Datos de la tabla también se pueden manipular con otros métodos en el `SQLiteConnection` (nuevamente, sin necesidad de instrucciones SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Vea el código fuente de caso práctico para obtener ejemplos completos.



## <a name="file-access"></a>Acceso a archivos

Acceso de archivo tiene la certeza de que una parte fundamental de cualquier aplicación. Algunos ejemplos comunes de archivos que pueden formar parte de una aplicación se incluyen:

- Archivos de base de datos de SQLite.
- Datos de generados por el usuario (texto, imágenes, sonido, vídeo).
- Datos descargados para almacenar en caché (imágenes, html o los archivos PDF).




### <a name="systemio-direct-access"></a>Acceso directo de System.IO

Xamarin.iOS y Xamarin.Android permiten al sistema de archivos mediante las clases en el `System.IO` espacio de nombres.

Cada plataforma tiene restricciones de acceso diferentes que deben tenerse en cuenta:

- las aplicaciones de iOS se ejecutan en un espacio aislado con acceso de sistema de archivos muy restringido. Apple más dicta cómo debe usar el sistema de archivos mediante la especificación de ciertas ubicaciones de copia de seguridad (y otros que no son). Hacer referencia a la [trabajar con el sistema de archivos en Xamarin.iOS](~/ios/app-fundamentals/file-system.md) guía para obtener más detalles.
- Android también restringe el acceso a determinados directorios relacionados con la aplicación, pero también admite medios externos (p ej. Las tarjetas SD) y el acceso a datos compartidos.
- Windows Phone 8 (Silverlight) no permiten el acceso directo del archivo: solo se pueden manipular archivos utilizando `IsolatedStorage`.
- Los proyectos de Windows 8.1 WinRT y Windows 10 UWP solo proporcionan operaciones de archivo asincrónicas a través de `Windows.Storage` API, que son diferentes de las otras plataformas.

#### <a name="example-for-ios-and-android"></a>Ejemplo de iOS y Android

A continuación, se muestra un ejemplo trivial que escribe y lee un archivo de texto.
Uso de `Environment.GetFolderPath` permite el mismo código se ejecute en iOS y Android, que devuelven un directorio válido según las convenciones del sistema de archivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Hacer referencia a Xamarin.iOS [trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md) documento para obtener más información sobre la funcionalidad del sistema de archivos específicos de iOS. Al escribir código de acceso de archivo multiplataforma, recuerde que algunos sistemas de archivos distinguen mayúsculas de minúsculas y tienen separadores de directorio diferente. Es recomendable usar siempre el mismo uso de mayúsculas y minúsculas para nombres de archivo y la `Path.Combine()` método al construir rutas de acceso de archivo o directorio.



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows.Storage en Windows 8 y Windows 10

El *Creating Mobile Apps with Xamarin.Forms* [libro](https://developer.xamarin.com/r/xamarin-forms/book/)
[capítulo 20. E/S de archivo y Async](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluye [ejemplos para Windows 8.1 y Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Mediante un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) es posible leer y archivos en estas plataformas usando las API admitidas:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Hacer referencia a la [capítulo del libro](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) para obtener más detalles.


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Almacenamiento aislado en Windows Phone 7 y 8 (Silverlight)

El almacenamiento aislado es una API común para guardar y cargar archivos a través de todas las plataformas antiguas de Windows Phone, Android y iOS.

Es el mecanismo predeterminado para el acceso de archivo en Windows Phone (Silverlight) que se ha implementado en Xamarin.iOS y Xamarin.Android para permitir que el código de acceso a archivos comunes que se va a escribir. El `System.IO.IsolatedStorage` clase puede hacer referencia a las tres plataformas en una [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Hacer referencia a la [las información general de almacenamiento aislado para Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) para obtener más información.

Las API de almacenamiento aislado no están disponibles en [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md). Una alternativa para PCL es la [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>Acceso de archivo entre plataformas en las PCL

También es compatible con PCL Nuget – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – ese acceso a los archivos de desarrollo multiplataforma instalaciones para plataformas compatibles con Xamarin y las API de Windows más recientes.


## <a name="network-operations"></a>Operaciones de red

Mayoría de aplicaciones móviles tendrán componente de red, por ejemplo:

- Descarga de imágenes, audio y vídeos (p ej. vistas en miniatura, fotografías, música).
- Descarga de documentos (p ej. HTML, PDF).
- Cargando los datos de usuario (por ejemplo, fotografías o texto).
- Acceso a servicios web o parte 3ª API (incluidos SOAP, XML o JSON).


.NET Framework proporciona algunas clases diferentes para tener acceso a los recursos de red: `HttpClient`, `WebClient`, y `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

El `HttpClient` clase en el `System.Net.Http` espacio de nombres está disponible en la mayoría de las plataformas Windows, Xamarin.Android y Xamarin.iOS. Hay un [Nuget de la biblioteca de cliente de Microsoft HTTP](https://www.nuget.org/packages/Microsoft.Net.Http/) que se puede utilizar para poner esta API en bibliotecas de clases portables (y Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La `WebClient` clase proporciona una API sencilla para recuperar datos remotos desde servidores remotos.

Operaciones de universal Windows Platform *debe* ser asincrónico, aunque Xamarin.iOS y Xamarin.Android admiten las operaciones sincrónicas (lo que pueden realizarse en subprocesos en segundo plano).

El código para una simple asincrónico `WebClient` operación es:

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` También tiene `DownloadFileCompleted` y `DownloadFileAsync` para recuperar datos binarios.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>httpWebRequest

`HttpWebRequest` ofrece mayor personalización que `WebClient` y como resultado requiere más código para que use.

El código de un simple sincrónico `HttpWebRequest` operación es:

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Hay un ejemplo en nuestro [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />


### <a name="reachability"></a>Accesibilidad

Los dispositivos móviles funcionan bajo diversas condiciones de red de Wi-Fi rápida o las conexiones de 4G a áreas de recepción deficiente y vínculos de datos de ARISTA lentos. Por este motivo, es recomendable para detectar si la red está disponible y si es así, qué tipo de red está disponible, antes de intentar conectarse a servidores remotos.

Las acciones que una aplicación móvil puede tardar en estas situaciones son:

- Si la red no está disponible, avisar al usuario. Si ha deshabilitado (p ej. manualmente. Modo de avión o desactivar Wi-Fi), a continuación, se puede resolver el problema.
- Si la conexión 3G, las aplicaciones pueden comportarse de forma diferente (por ejemplo, Apple no permite las aplicaciones de más de 20Mb a descargarse en 3G). Aplicaciones podrían utilizar esta información para advertir al usuario sobre descarga excesivo veces cuando se recuperan archivos de gran tamaño.
- Incluso si la red está disponible, es recomendable comprobar la conectividad con el servidor de destino antes de iniciar otras solicitudes. Esto impedirá que las operaciones de red de la aplicación en el tiempo de espera de forma repetida y también permiten un mensaje de error más informativo que se mostrará al usuario.


Hay un [Xamarin.iOS ejemplo](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) disponibles (que se basa en Apple [código de ejemplo de accesibilidad](https://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) ) para ayudar a detectar la disponibilidad de la red.


## <a name="webservices"></a>Servicios Web

Consulte la documentación sobre [trabajar con servicios Web](~/cross-platform/data-cloud/web-services/index.md), que incluye acceso a REST, extremos SOAP y WCF con Xamarin.iOS. Es posible que las solicitudes de creación web service y analizar las respuestas, sin embargo, hay bibliotecas disponibles para hacer esto mucho más sencillo, incluidos Azure, RestSharp y ServiceStack. Pueden tener acceso a operaciones incluso básicas de WCF en aplicaciones de Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure es una plataforma de nube que proporciona una amplia variedad de servicios para aplicaciones móviles, incluidos el almacenamiento de datos y sincronización y notificaciones de inserción.

Visite [azure.microsoft.com](https://azure.microsoft.com/) a probarlo gratis.

### <a name="restsharp"></a>RestSharp

RestSharp es una biblioteca de .NET que puede incluirse en las aplicaciones móviles para proporcionar a un cliente REST que simplifica el acceso a los servicios web. Le ayuda a proporcionando una API sencilla para solicitar datos y analizar la respuesta REST. RestSharp puede ser útil

El [sitio Web de RestSharp](http://restsharp.org/) contiene [documentación](https://github.com/restsharp/RestSharp/wiki) sobre cómo implementar un cliente de REST con RestSharp.
RestSharp proporciona ejemplos de Xamarin.iOS y Xamarin.Android en [github](https://github.com/restsharp/RestSharp/).

También es un fragmento de código de Xamarin.iOS en nuestra [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

A diferencia de RestSharp, ServiceStack es tanto una solución de servidor para hospedar un servicio web, así como una biblioteca de cliente que se puede implementar en aplicaciones móviles para tener acceso a esos servicios.

El [sitio Web de ServiceStack](http://servicestack.net/) explica el propósito del proyecto y vínculos a documentos y ejemplos de código. Los ejemplos incluyen una implementación de servidor completa de un servicio web, así como diversas aplicaciones del lado cliente que pueden tener acceso a él.

Hay un [Xamarin.iOS ejemplo](http://www.servicestack.net/monotouch/remote-info/) en el sitio Web de ServiceStack y un fragmento de código en nuestro [documentación de servicios Web](~/cross-platform/data-cloud/web-services/index.md).


### <a name="wcf"></a>WCF

Las herramientas de Xamarin pueden ayudarle a usar algunos servicios de Windows Communication Foundation (WCF). En general, Xamarin es compatible con el mismo subconjunto de lado cliente de WCF que se incluye con el tiempo de ejecución de Silverlight. Esto incluye las implementaciones más comunes de codificación y protocolo de WCF: texto con codificación de mensajes SOAP a través de HTTP utilizando el protocolo de transporte la `BasicHttpBinding`.

Debido al tamaño y complejidad de la plataforma WCF, es posible que las implementaciones de servicio actuales y futuras que estarán fuera del ámbito admitido por dominio de subconjunto de cliente de Xamarin. Además, compatibilidad con WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.

 <a name="Threading" />


## <a name="threading"></a>Subprocesos

La capacidad de respuesta de la aplicación es importante para las aplicaciones móviles: los usuarios esperan que las aplicaciones para cargar y realizar rápidamente. Una pantalla "inmovilizada" que va a aparecer deja de aceptar la entrada del usuario para indicar que la aplicación se ha bloqueado, por lo que es importante no mantener ocupado el subproceso de interfaz de usuario con las llamadas de bloqueo de larga ejecución, como las solicitudes de red o de operaciones lentas de locales (por ejemplo, al descomprimir un archivo). En concreto, el proceso de inicio no debe contener tareas de larga duración: todas las plataformas móviles terminará una aplicación que tarda demasiado tiempo en cargar.

Esto significa que debe implementar la interfaz de usuario de un indicador de progreso' ' o una interfaz de usuario en caso contrario, "utilizable" que es más rápido mostrar y las tareas asincrónicas para llevar a cabo operaciones en segundo plano. La ejecución de tareas en segundo plano, requiere el uso de subprocesos, lo que significa que las necesidades de las tareas en segundo plano una manera de comunicarse con el subproceso principal para indicar el progreso o cuándo se han completado.

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>Biblioteca de tareas paralelas

Las tareas creadas con la biblioteca de tareas de procesamiento paralelo pueden ejecutar de forma asincrónica y devolver en su subproceso de llamada, que sean muy útiles para desencadenar las operaciones de ejecución prolongada sin bloquear la interfaz de usuario.

Una operación de tareas en paralelo sencilla podría parecerse a esto:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La clave es `TaskScheduler.FromCurrentSynchronizationContext()` que volverá a usar el SynchronizationContext.Current del subproceso de la llamada al método (aquí el subproceso principal que se está ejecutando `MainThreadMethod`) como una manera de calcular las referencias de espera de las llamadas a ese subproceso. Esto significa que si se llama al método en el subproceso de interfaz de usuario, se ejecutará el `ContinueWith` operación de nuevo en el subproceso de interfaz de usuario.

Si el código está iniciando las tareas de otros subprocesos, use el siguiente patrón para crear una referencia al subproceso de interfaz de usuario y la tarea puede seguir llamando a volver a él:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>Invocación en el subproceso de interfaz de usuario

Para el código que no usa la biblioteca de tareas paralelas, cada plataforma tiene su propia sintaxis para las operaciones de cálculo de referencias al subproceso de la interfaz de usuario:

- **iOS** : `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** : `owner.RunOnUiThread(action)`
- **Xamarin.Forms** : `Device.BeginInvokeOnMainThread(action)`
- **Windows** : `Deployment.Current.Dispatcher.BeginInvoke(action)`



IOS y Android sintaxis requiere una clase 'context' esté disponible, lo que significa que el código debe pasar este objeto a cualquier método que requiera una devolución de llamada en el subproceso de interfaz de usuario.

Para realizar llamadas de subproceso de interfaz de usuario en código compartido, siga el [IDispatchOnUIThread ejemplo](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (cortesía de [ @follesoe ](http://jonas.follesoe.no/)). Declarar y un programa para un `IDispatchOnUIThread` interfaz en el código compartido y, a continuación, implementar las clases específicas de la plataforma, como se muestra aquí:

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Deben usar los desarrolladores de Xamarin.Forms [ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) en el código común (proyectos compartidos o PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>Plataforma y las funcionalidades de dispositivo y una degradación

Más ejemplos específicos de trabajar con distintas capacidades se proporcionan en la documentación de funciones de la plataforma. Trata de detectar diferentes capacidades y cómo degradar una aplicación para proporcionar una buena experiencia del usuario, incluso cuando la aplicación no puede funcionar a su máximo potencial de forma correcta.
