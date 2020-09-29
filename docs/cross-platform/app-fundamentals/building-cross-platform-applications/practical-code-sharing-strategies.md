---
title: 'Parte 5: Estrategias prácticas de uso compartido de código'
description: En este documento se describen estrategias prácticas de uso compartido de código para escenarios como bases de datos, acceso a archivos, operaciones de red y código asincrónico.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: d10917471c37d91fa02db2585895f5694a5a4a60
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457450"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5: Estrategias prácticas de uso compartido de código

En esta sección se proporcionan ejemplos de cómo compartir código para escenarios de aplicaciones comunes.

## <a name="data-layer"></a>Capa de datos

La capa de datos consta de un motor de almacenamiento y métodos para leer y escribir información. En cuanto al rendimiento, la flexibilidad y la compatibilidad entre plataformas, se recomienda el motor de base de datos de SQLite para las aplicaciones multiplataforma de Xamarin.
Se ejecuta en una amplia variedad de plataformas, como Windows, Android, iOS y Mac.

### <a name="sqlite"></a>SQLite

SQLite es una implementación de base de datos de código abierto. El origen y la documentación se pueden encontrar en [SQLite.org](https://www.sqlite.org/). La compatibilidad con SQLite está disponible en cada plataforma móvil:

- **iOS** : integrado en el sistema operativo.
- **Android** : integrado en el sistema operativo desde Android 2,2 (nivel de API 10).
- **Windows** : vea la [extensión SQLite para plataforma universal de Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Incluso con el motor de base de datos disponible en todas las plataformas, los métodos nativos para tener acceso a la base de datos son diferentes. Tanto iOS como Android ofrecen API integradas para acceder a SQLite que podría usarse desde Xamarin. iOS o Xamarin. Android. sin embargo, el uso de los métodos del SDK nativo no ofrece la posibilidad de compartir código (excepto las propias consultas SQL, suponiendo que se almacenan como cadenas). Para obtener más información sobre la búsqueda en la funcionalidad de la base de datos nativa `CoreData` en la clase de iOS o Android `SQLiteOpenHelper` ; debido a que estas opciones no son multiplataforma, quedan fuera del ámbito de este documento.

### <a name="adonet"></a>ADO.NET

Tanto la compatibilidad con Xamarin. iOS como Xamarin. Android `System.Data` y `Mono.Data.Sqlite` (consulte la [documentación](~/ios/data-cloud/system.data.md) de Xamarin. iOS para obtener más información).
El uso de estos espacios de nombres permite escribir código ADO.NET que funcione en ambas plataformas. Edite las referencias del proyecto para incluir `System.Data.dll` y `Mono.Data.Sqlite.dll` y agregue estas instrucciones using al código:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

A continuación, el código de ejemplo siguiente funcionará:

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

Las implementaciones del mundo real de ADO.NET se dividirían obviamente entre diferentes métodos y clases (este ejemplo solo es para fines de demostración).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET: ORM multiplataforma

Un ORM (u asignador relacional de objetos) intenta simplificar el almacenamiento de los datos modelados en clases. En lugar de escribir manualmente las consultas SQL que crean tablas o SELECCIONAn, INSERTAn y ELIMINAn datos que se extraen manualmente de los campos y propiedades de clase, un ORM agrega una capa de código que lo hace automáticamente. Mediante el uso de la reflexión para examinar la estructura de las clases, un ORM puede crear automáticamente tablas y columnas que coincidan con una clase y generar consultas para leer y escribir los datos. Esto permite al código de la aplicación simplemente enviar y recuperar instancias de objeto al ORM, lo que se encarga de todas las operaciones SQL en el capó.

SQLite-NET actúa como un ORM simple que le permitirá guardar y recuperar sus clases en SQLite. Oculta la complejidad del acceso de SQLite entre plataformas con una combinación de directivas de compilador y otros trucos.

Características de SQLite-NET:

- Las tablas se definen agregando atributos a las clases de modelo.
- Una instancia de base de datos se representa mediante una subclase de  `SQLiteConnection` , la clase principal de la biblioteca de SQLite-net.
- Los datos se pueden insertar, consultar y eliminar mediante objetos. No se requieren instrucciones SQL (aunque se pueden escribir instrucciones SQL si es necesario).
- Las consultas LINQ básicas se pueden realizar en las colecciones devueltas por SQLite-NET.

El código fuente y la documentación de SQLite-NET están disponibles en [SQLite-net en github](https://github.com/praeclarum/sqlite-net) y se han implementado en ambos casos prácticos. A continuación se muestra un ejemplo sencillo de código de red SQLite (del caso práctico de *Pro con tareas* ).

En primer lugar, la `TodoItem` clase utiliza atributos para definir que un campo sea una clave principal de base de datos:

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

Esto permite `TodoItem` crear una tabla con la siguiente línea de código (y sin instrucciones SQL) en una `SQLiteConnection` instancia:

```csharp
CreateTable<TodoItem> ();
```

Los datos de la tabla también se pueden manipular con otros métodos en `SQLiteConnection` (de nuevo, sin necesidad de instrucciones SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Vea el código fuente del caso práctico para ver ejemplos completos.

## <a name="file-access"></a>Acceso a archivos

Es cierto que el acceso a archivos es una parte fundamental de cualquier aplicación. Algunos ejemplos comunes de archivos que pueden formar parte de una aplicación son:

- Archivos de base de datos de SQLite.
- Datos generados por el usuario (texto, imágenes, sonido, vídeo).
- Datos descargados para el almacenamiento en caché (imágenes, archivos HTML o PDF).

### <a name="systemio-direct-access"></a>Acceso directo de System.IO

Tanto Xamarin. iOS como Xamarin. Android permiten el acceso al sistema de archivos mediante las clases del `System.IO` espacio de nombres.

Cada plataforma tiene restricciones de acceso diferentes que se deben tener en cuenta:

- las aplicaciones de iOS se ejecutan en un espacio aislado con acceso de sistema de archivos muy restringido. Apple dicta además cómo debe usar el sistema de archivos especificando ciertas ubicaciones de las que se realiza una copia de seguridad (y otras que no lo están). Para más información, consulte la guía  [trabajar con el sistema de archivos de Xamarin. iOS](~/ios/app-fundamentals/file-system.md) .
- Android también restringe el acceso a ciertos directorios relacionados con la aplicación, pero también admite medios externos (por ejemplo, Tarjetas SD) y acceso a datos compartidos.
- Windows Phone 8 (Silverlight) no permiten el acceso directo a archivos: los archivos solo se pueden manipular mediante `IsolatedStorage` .
- Windows 8.1 los proyectos de WinRT y Windows 10 UWP solo ofrecen operaciones de archivo asincrónicas a través `Windows.Storage` de API, que son diferentes de las otras plataformas.

#### <a name="example-for-ios-and-android"></a>Ejemplo de iOS y Android

A continuación se muestra un ejemplo trivial que escribe y lee un archivo de texto.
El uso de `Environment.GetFolderPath` permite que el mismo código se ejecute en iOS y Android, que devuelven un directorio válido basado en sus convenciones del sistema de archivos.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

Consulte el documento Xamarin. iOS [Working with The File System](~/ios/app-fundamentals/file-system.md) para más información sobre la funcionalidad de sistema de archivos específica de iOS. Al escribir código de acceso a archivos entre plataformas, recuerde que algunos sistemas de archivos distinguen mayúsculas de minúsculas y tienen distintos separadores de directorio. Es recomendable usar siempre el mismo uso de mayúsculas y minúsculas para los nombres de archivo y el `Path.Combine()` método al construir rutas de acceso de archivos o directorios.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. Storage para Windows 8 y Windows 10

El capítulo 20 sobre *creación de Mobile Apps con Xamarin. Forms* [book](https://developer.xamarin.com/r/xamarin-forms/book/) 
 [. Async y e/s de archivos](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluyen [ejemplos para Windows 8.1 y Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Mediante el uso de [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) , es posible leer y archivos de archivo en estas plataformas mediante las API admitidas:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Consulte el [capítulo del libro](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) para obtener más detalles.

<a name="Isolated_Storage"></a>

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Almacenamiento aislado en Windows Phone 7 & 8 (Silverlight)

El almacenamiento aislado es una API común para guardar y cargar archivos en todas las plataformas de Windows Phone de iOS, Android y anteriores.

Es el mecanismo predeterminado para el acceso a archivos en Windows Phone (Silverlight) que se ha implementado en Xamarin. iOS y Xamarin. Android para permitir la escritura de código de acceso a archivos común. `System.IO.IsolatedStorage`Se puede hacer referencia a la clase en las tres plataformas de un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Consulte [información general sobre el almacenamiento aislado para](/previous-versions/windows/apps/ff402541(v=vs.105)) obtener más información sobre Windows Phone.

Las API de almacenamiento aislado no están disponibles en las [bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md). Una alternativa para PCL es el [NuGet PCLStorage](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Acceso a archivos multiplataforma en PCL

También hay una NuGet compatible con PCL, [PCLStorage](https://www.nuget.org/packages/PCLStorage/) , que permite el acceso a archivos multiplataforma para plataformas compatibles con Xamarin y las API de Windows más recientes.

## <a name="network-operations"></a>Operaciones de red

La mayoría de las aplicaciones móviles tendrán componente de red, por ejemplo:

- Descargar imágenes, vídeo y audio (por ejemplo, miniaturas, fotos, música).
- Descargar documentos (por ejemplo, HTML, PDF).
- Cargar datos de usuario (como fotos o texto).
- Acceso a servicios web o API de terceros (incluidos SOAP, XML o JSON).

El .NET Framework proporciona algunas clases diferentes para tener acceso a los recursos de red: `HttpClient` , `WebClient` y `HttpWebRequest` .

### <a name="httpclient"></a>HttpClient

La `HttpClient` clase del `System.Net.Http` espacio de nombres está disponible en Xamarin. iOS, Xamarin. Android y la mayoría de las plataformas Windows. Hay una [biblioteca de cliente http de Microsoft NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) que se puede usar para incorporar esta API en bibliotecas de clases portables (y Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La `WebClient` clase proporciona una API simple para recuperar datos remotos de servidores remotos.

Plataforma universal de Windows operaciones *deben* ser asincrónicas, aunque Xamarin. iOS y Xamarin. Android admitan operaciones sincrónicas (que se pueden realizar en subprocesos en segundo plano).

El código para una operación asincrónica simple `WebClient` es:

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

 `WebClient` también tiene `DownloadFileCompleted` y `DownloadFileAsync` para recuperar datos binarios.

<a name="HttpWebRequest"></a>

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` ofrece más personalización que `WebClient` y, como resultado, requiere más código para usar.

El código de una operación sincrónica simple `HttpWebRequest` es:

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

Hay un ejemplo en nuestra [documentación de servicios web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability"></a>

### <a name="reachability"></a>Reachability

Los dispositivos móviles funcionan en una gran variedad de condiciones de red desde conexiones Wi-Fi o 4G rápidas a áreas de recepción deficientes y vínculos de datos PERIMETRALes lentos. Por este motivo, es recomendable detectar si la red está disponible y, en caso afirmativo, qué tipo de red está disponible, antes de intentar conectarse a los servidores remotos.

Entre las acciones que una aplicación móvil podría llevar a cabo en estas situaciones se incluyen las siguientes:

- Si la red no está disponible, aconseje al usuario. Si se ha deshabilitado manualmente (por ejemplo, El modo avión o la desactivación de Wi-Fi), pueden resolver el problema.
- Si la conexión es 3G, las aplicaciones pueden comportarse de forma diferente (por ejemplo, Apple no permite la descarga de aplicaciones de más de 20 MB a través de 3G). Las aplicaciones podrían usar esta información para advertir al usuario sobre los tiempos de descarga excesivos al recuperar archivos grandes.
- Incluso si la red está disponible, se recomienda comprobar la conectividad con el servidor de destino antes de iniciar otras solicitudes. Esto impedirá que las operaciones de red de la aplicación agoten el tiempo de espera de forma repetida y también permitirá que se muestre un mensaje de error más informativo al usuario.

## <a name="webservices"></a>WebServices

Consulte nuestra documentación sobre [Cómo trabajar con servicios web](~/cross-platform/data-cloud/web-services/index.md), que trata el acceso a los puntos de conexión de REST, SOAP y WCF mediante Xamarin. iOS. Es posible crear a mano solicitudes de servicio Web y analizar las respuestas; sin embargo, hay bibliotecas disponibles para simplificar esto, como Azure, RestSharp y ServiceStack. También se puede acceder a las operaciones básicas de WCF en aplicaciones de Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure es una plataforma en la nube que proporciona una amplia variedad de servicios para aplicaciones móviles, como el almacenamiento y la sincronización de datos, y notificaciones de envío.

Visite [Azure.Microsoft.com](https://azure.microsoft.com/) para probarlo de forma gratuita.

### <a name="restsharp"></a>RestSharp

RestSharp es una biblioteca de .NET que se puede incluir en aplicaciones móviles para proporcionar un cliente REST que simplifica el acceso a los servicios Web. Ayuda a proporcionar una API simple para solicitar datos y analizar la respuesta de REST. RestSharp puede ser útil

El [sitio web de RestSharp](http://restsharp.org/) contiene [documentación](https://github.com/restsharp/RestSharp/wiki) sobre cómo implementar un cliente de REST con RestSharp.
RestSharp proporciona ejemplos de Xamarin. iOS y Xamarin. Android en [GitHub](https://github.com/restsharp/RestSharp/).

También hay un fragmento de código de Xamarin. iOS en nuestra [documentación de servicios web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack"></a>

### <a name="servicestack"></a>ServiceStack

A diferencia de RestSharp, ServiceStack es una solución de servidor para hospedar un servicio Web, así como una biblioteca de cliente que se puede implementar en aplicaciones móviles para obtener acceso a esos servicios.

En el [sitio web de ServiceStack](http://servicestack.net/) se explica el propósito del proyecto y los vínculos a los ejemplos de código y de documento. Entre los ejemplos se incluye una implementación completa del lado servidor de un servicio Web, así como diversas aplicaciones del lado cliente que pueden tener acceso a él.

### <a name="wcf"></a>WCF

Las herramientas de Xamarin pueden ayudarle a consumir algunos servicios Windows Communication Foundation (WCF). En general, Xamarin admite el mismo subconjunto de WCF del lado cliente que se incluye con Silverlight Runtime. Esto incluye las implementaciones de protocolo y codificación más comunes de WCF: mensajes SOAP codificados por texto a través del Protocolo de transporte HTTP mediante `BasicHttpBinding` .

Debido al tamaño y la complejidad del marco de trabajo de WCF, puede haber implementaciones de servicios actuales y futuras que se encuentren fuera del ámbito admitido por el dominio de subconjunto de cliente de Xamarin. Además, la compatibilidad con WCF requiere el uso de herramientas que solo están disponibles en un entorno de Windows para generar el proxy.

 <a name="Threading"></a>

## <a name="threading"></a>Subprocesos

La capacidad de respuesta de la aplicación es importante para las aplicaciones móviles: los usuarios esperan que las aplicaciones se carguen y realicen rápidamente. Aparecerá una pantalla "inmovilizada" que deja de aceptar la entrada de datos para indicar que la aplicación se ha bloqueado, por lo que es importante no asociar el subproceso de interfaz de usuario con llamadas de bloqueo de ejecución prolongada como solicitudes de red o operaciones locales lentas (por ejemplo, descomprimir un archivo). En concreto, el proceso de inicio no debe contener tareas de ejecución prolongada; todas las plataformas móviles eliminarán una aplicación que tarda demasiado tiempo en cargarse.

Esto significa que la interfaz de usuario debe implementar una interfaz de usuario de tipo "indicador de progreso" o "utilizable" que se muestre rápidamente y que las tareas asincrónicas realicen operaciones en segundo plano. La ejecución de tareas en segundo plano requiere el uso de subprocesos, lo que significa que las tareas en segundo plano necesitan una manera de comunicarse de nuevo con el subproceso principal para indicar el progreso o Cuándo se han completado.

 <a name="Parallel_Task_Library"></a>

### <a name="parallel-task-library"></a>Biblioteca de tareas paralela

Las tareas creadas con la biblioteca de tareas paralelas se pueden ejecutar de forma asincrónica y devuelven en su subproceso de llamada, lo que hace que sean muy útiles para desencadenar operaciones de ejecución prolongada sin bloquear la interfaz de usuario.

Una operación de tarea paralela simple podría tener el siguiente aspecto:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La clave es la `TaskScheduler.FromCurrentSynchronizationContext()` que reutilizará el SynchronizationContext. Current del subproceso que llama al método (aquí el subproceso principal que se está ejecutando `MainThreadMethod` ) como una manera de calcular las referencias de las llamadas a ese subproceso. Esto significa que si se llama al método en el subproceso de la interfaz de usuario, se ejecutará de `ContinueWith` nuevo la operación en el subproceso de la interfaz de usuario.

Si el código está iniciando tareas desde otros subprocesos, use el siguiente patrón para crear una referencia al subproceso de interfaz de usuario y la tarea todavía puede volver a llamar a ella:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread"></a>

### <a name="invoking-on-the-ui-thread"></a>Invocar en el subproceso de la interfaz de usuario

Para el código que no use la biblioteca de tareas paralela, cada plataforma tiene su propia sintaxis para serializar las operaciones de nuevo en el subproceso de la interfaz de usuario:

- **iOS** : `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** : `owner.RunOnUiThread(action)`
- **Xamarin. Forms** : `Device.BeginInvokeOnMainThread(action)`
- **Windows**:`Deployment.Current.Dispatcher.BeginInvoke(action)`

La sintaxis de iOS y Android requiere que haya una clase ' context ' disponible, lo que significa que el código debe pasar este objeto a todos los métodos que requieran una devolución de llamada en el subproceso de la interfaz de usuario.

Para realizar llamadas de subprocesos de interfaz de usuario en código compartido, siga el [ejemplo de IDispatchOnUIThread](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (cortesía de [@follesoe](https://twitter.com/follesoe) ). Declare y programe en una `IDispatchOnUIThread` interfaz en el código compartido y, a continuación, implemente las clases específicas de la plataforma como se muestra aquí:

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

Los desarrolladores de Xamarin. Forms deben usar [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) en código común (proyectos compartidos o PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation"></a>

## <a name="platform-and-device-capabilities-and-degradation"></a>Funcionalidad y degradación de la plataforma y el dispositivo

En la documentación sobre las capacidades de la plataforma se proporcionan más ejemplos específicos de cómo tratar con diferentes capacidades. Se ocupa de la detección de distintas funcionalidades y de cómo degradar correctamente una aplicación para proporcionar una buena experiencia de usuario, incluso cuando la aplicación no puede funcionar con todo su potencial.