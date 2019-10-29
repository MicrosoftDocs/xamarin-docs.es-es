---
title: Introducción a los servicios web
description: En esta guía se muestra cómo consumir diferentes tecnologías de servicios Web. Entre los temas tratados se incluyen la comunicación con servicios REST, servicios SOAP y servicios de Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 50302b0b9cf96d211c704ab9e68d1c61d11e807a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016582"
---
# <a name="introduction-to-web-services"></a>Introducción a los servicios web

_En esta guía se muestra cómo consumir diferentes tecnologías de servicios Web. Entre los temas tratados se incluyen la comunicación con servicios REST, servicios SOAP y servicios de Windows Communication Foundation._

Para que funcione correctamente, muchas aplicaciones móviles dependen de la nube y, por tanto, la integración de servicios web en aplicaciones móviles es un escenario común. La plataforma Xamarin admite el consumo de distintas tecnologías de servicios web e incluye compatibilidad integrada y de terceros para consumir servicios RESTful, ASMX y Windows Communication Foundation (WCF).

En el caso de los clientes que usan Xamarin. Forms, hay ejemplos completos que usan cada una de estas tecnologías en la documentación de [servicios Web de Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> En iOS 9, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial.
> Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Puede optar por ATS si no es posible usar el protocolo de `HTTPS` y proteger la comunicación de los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

La transferencia de estado representacional (REST) es un estilo arquitectónico para la creación de servicios Web. Las solicitudes REST se realizan a través de HTTP usando los mismos verbos HTTP que los exploradores web utilizan para recuperar páginas web y enviar datos a los servidores. Los verbos son:

- **Get** : esta operación se usa para recuperar datos del servicio Web.
- **Post** : esta operación se usa para crear un nuevo elemento de datos en el servicio Web.
- **Put** : esta operación se usa para actualizar un elemento de datos en el servicio Web.
- **Patch** : esta operación se usa para actualizar un elemento de datos en el servicio Web mediante la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **Delete** : esta operación se usa para eliminar un elemento de datos en el servicio Web.

Las API de servicios web que se adhieren a REST se denominan API de RESTful y se definen mediante:

- URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Un tipo de medio para los datos, como notación de objetos JavaScript (JSON).

La simplicidad de REST ha ayudado a convertirlo en el método principal para tener acceso a los servicios web en aplicaciones móviles.

## <a name="consuming-rest-services"></a>Consumo de servicios REST

Hay una serie de bibliotecas y clases que se pueden usar para consumir servicios REST, y en las subsecciones siguientes se describen. Para obtener más información sobre cómo consumir un servicio REST, consulte [consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Las [bibliotecas de cliente de Microsoft http](https://www.nuget.org/packages/Microsoft.Net.Http) proporcionan la clase `HttpClient`, que se usa para enviar y recibir solicitudes a través de http. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

La clase `HttpResponseMessage` representa un mensaje de respuesta HTTP recibido del servicio Web después de que se haya realizado una solicitud HTTP. Contiene información sobre la respuesta, incluido el código de estado, los encabezados y el cuerpo. La clase `HttpContent` representa el cuerpo HTTP y los encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer mediante cualquiera de los métodos de `ReadAs`, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

Para obtener más información sobre la clase `HttpClient`, vea [crear el objeto HTTPClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

La llamada a servicios web con `HTTPWebRequest` implica:

- Crear la instancia de solicitud para un URI determinado.
- Establecer varias propiedades HTTP en la instancia de la solicitud.
- Recuperación de una `HttpWebResponse` de la solicitud.
- Lectura de datos de la respuesta.

Por ejemplo, en el código siguiente se recuperan los datos del servicio Web National National de medicina:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

En el ejemplo anterior se crea una `HttpWebRequest` que devolverá los datos con formato JSON. Los datos se devuelven en un `HttpWebResponse`, desde el que se puede obtener un `StreamReader` para leer los datos.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Otro enfoque para consumir servicios REST es usar la biblioteca [RestSharp](http://restsharp.org/) . RestSharp encapsula las solicitudes HTTP, incluida la compatibilidad para recuperar los resultados como contenido de cadena sin formato o como un C# objeto deserializado. Por ejemplo, el código siguiente realiza una solicitud al servicio Web National National of medicina de EE. UU. y recupera los resultados como una cadena con formato JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` es un método que tomará la cadena JSON sin formato de la propiedad `RestSharp.RestResponse.Content` y la convertirá C# en un objeto. La deserialización de los datos devueltos de los servicios web se describe más adelante en este artículo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Además de las clases disponibles en la biblioteca de clases base (BCL) mono, como `HttpWebRequest`, y C# bibliotecas de terceros, como RestSharp, las clases específicas de la plataforma también están disponibles para consumir servicios Web. Por ejemplo, en iOS, se pueden usar las clases `NSUrlConnection` y `NSMutableUrlRequest`.

En el ejemplo de código siguiente se muestra cómo llamar al servicio Web National Library of medicina de EE. UU. mediante clases de iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

Por lo general, las clases específicas de la plataforma para consumir servicios web se deben limitar a escenarios en los que se va C#a migrar código nativo. Siempre que sea posible, el código de acceso al servicio Web debe ser portable para que se pueda compartir entre plataformas.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Otra opción para llamar a los servicios web es la biblioteca de [pila de servicios](https://www.servicestack.net/) . Por ejemplo, en el código siguiente se muestra cómo usar el método `IServiceClient.GetAsync` de la pila de servicio para emitir una solicitud de servicio:

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Aunque herramientas como ServiceStack y RestSharp facilitan la llamada y el consumo de servicios de REST, a veces no es trivial usar XML o JSON que no se ajuste a las convenciones de serialización de _DataContract_ estándar. Si es necesario, invoque la solicitud y controle la serialización adecuada explícitamente mediante la biblioteca ServiceStack. Text que se describe a continuación.

<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Consumir datos de RESTful

Los servicios web RESTful normalmente usan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactos, lo que reduce los requisitos de ancho de banda cuando se envían datos. En esta sección, se examinarán los mecanismos para consumir respuestas RESTful en JSON y el código XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System. JSON

La plataforma Xamarin incluye compatibilidad con JSON fuera de la caja. Mediante el uso de un `JsonObject`, los resultados se pueden recuperar como se muestra en el ejemplo de código siguiente:

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

Sin embargo, es importante tener en cuenta que las herramientas de `System.Json` de cargan la totalidad de los datos en la memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

La [biblioteca de NewtonSoft JSON.net](https://www.newtonsoft.com/json) es una biblioteca ampliamente usada para serializar y deserializar mensajes JSON. En el ejemplo de código siguiente se muestra cómo usar JSON.NET para deserializar un mensaje JSON C# en un objeto:

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack. Text

ServiceStack. Text es una biblioteca de serialización de JSON diseñada para trabajar con la biblioteca de ServiceStack. En el ejemplo de código siguiente se muestra cómo analizar JSON mediante una `ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

En el caso de consumir un servicio Web REST basado en XML, LINQ to XML se puede utilizar para analizar el XML y rellenar C# un objeto insertado, tal y como se muestra en el ejemplo de código siguiente:

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Servicio Web ASP.NET (ASMX)

ASMX proporciona la capacidad de compilar servicios web que envían mensajes mediante el Protocolo simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independiente del lenguaje para crear y obtener acceso a servicios Web. No es necesario que los consumidores de un servicio ASMX sepan nada sobre la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP.

Un mensaje SOAP es un documento XML que contiene los elementos siguientes:

- Un elemento raíz denominado *Envelope* que identifica el documento XML como un mensaje SOAP.
- Un elemento de *encabezado* opcional que contiene información específica de la aplicación, como datos de autenticación. Si el elemento de *encabezado* está presente, debe ser el primer elemento secundario del elemento de *sobre* .
- Un elemento *Body* necesario que contiene el mensaje SOAP destinado al destinatario.
- Un elemento *Fault* opcional que se usa para indicar mensajes de error. Si el elemento *Fault* está presente, debe ser un elemento secundario del elemento *Body* .

SOAP puede operar a través de varios protocolos de transporte, incluidos HTTP, SMTP, TCP y UDP. Sin embargo, un servicio ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin admite implementaciones estándar de SOAP 1,1 a través de HTTP y esto incluye compatibilidad con muchas de las configuraciones estándar del servicio ASMX.

### <a name="generating-a-proxy"></a>Generación de un proxy

Se debe generar un *proxy* para consumir un servicio asmx, lo que permite que la aplicación se conecte al servicio. El proxy se construye mediante el consumo de metadatos de servicio que define los métodos y la configuración de servicio asociada. Estos metadatos se exponen como un documento de lenguaje de descripción de servicios web (WSDL) generado por el servicio Web. El proxy se compila con Visual Studio para Mac o Visual Studio para agregar una referencia Web para el servicio Web a los proyectos específicos de la plataforma.

La dirección URL del servicio web puede ser un recurso de origen remoto hospedado o del sistema de archivos local accesible mediante el `file:///` prefijo de ruta de acceso, por ejemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "The web service URL can either be a hosted remote source or local file system resource accessible via the file path prefix")](images/add-webreference-dialog.png#lightbox)

Esto genera el proxy en la carpeta de referencias de servicio o web del proyecto. Dado que un proxy se genera código, no debe modificarse.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Agregar manualmente un proxy a un proyecto

Si tiene un proxy existente que se ha generado mediante herramientas compatibles, esta salida se puede consumir cuando se incluye como parte del proyecto. En Visual Studio para Mac, use la **.** . opción de menú para agregar el proxy. Además, esto requiere que se haga referencia a *System. Web. Services. dll* explícitamente mediante la **adición de referencias.** . diálogo.

### <a name="consuming-the-proxy"></a>Consumir el proxy

Las clases de proxy generadas proporcionan métodos para consumir el servicio Web que utiliza el modelo de diseño del modelo de programación asincrónica (APM). En este patrón, se implementa una operación asincrónica como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y finalizan la operación asincrónica.

El método *BeginOperationName* comienza la operación asincrónica y devuelve un objeto que implementa la interfaz `IAsyncResult`. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras que la operación asincrónica tiene lugar en un subproceso del grupo de subprocesos.

Para cada llamada a *BeginOperationName*, la aplicación también debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio Web sincrónico. El ejemplo de código siguiente muestra un ejemplo:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La biblioteca TPL (Task Parallel Library) puede simplificar el proceso de consumo de un par de métodos Begin/end de APM encapsulando las operaciones asincrónicas en el mismo `Task` objeto. Esta encapsulación la proporcionan varias sobrecargas del método `Task.Factory.FromAsync`. Este método crea un `Task` que ejecuta el método `TodoService.EndGetTodoItems` una vez que se completa el método `TodoService.BeginGetTodoItems`, con el parámetro `null` que indica que no se pasa ningún dato al delegado `BeginGetTodoItems`. Por último, el valor de la enumeración `TaskCreationOptions` especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación asincrónica .NET Framework tradicional](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

Para obtener más información sobre cómo consumir un servicio ASMX, consulte [consumo de un servicio Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF es el marco unificado de Microsoft para la creación de aplicaciones orientadas a servicios. Permite a los desarrolladores compilar aplicaciones distribuidas seguras, con transacciones y de confianza, e interoperables.

WCF describe un servicio con varios contratos diferentes, entre los que se incluyen los siguientes:

- **Contratos de datos** : defina las estructuras de datos que forman la base del contenido de un mensaje.
- **Contratos de mensajes** : redacte mensajes a partir de contratos de datos existentes.
- **Contratos de error** : permite especificar los errores de SOAP personalizados.
- **Contratos de servicio** : especifique las operaciones que admiten los servicios y los mensajes necesarios para interactuar con cada operación. También especifican cualquier comportamiento de error personalizado que se puede asociar a las operaciones en cada servicio.

Hay diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas funcionalidades que ASMX proporciona: mensajes SOAP a través de HTTP.

> [!IMPORTANT]
> La compatibilidad con la plataforma de Xamarin para WCF se limita a los mensajes SOAP con codificación de texto a través de HTTP/HTTPS mediante la clase `BasicHttpBinding`. Además, la compatibilidad con WCF requiere el uso de herramientas que solo están disponibles en un entorno de Windows para generar el proxy.

### <a name="generating-a-proxy"></a>Generación de un proxy

Se debe generar un *proxy* para consumir un servicio WCF, lo que permite que la aplicación se conecte al servicio. El proxy se construye mediante el consumo de metadatos de servicio que definen los métodos y la configuración de servicio asociada. Estos metadatos se exponen en forma de un documento de lenguaje de descripción de servicios web (WSDL) generado por el servicio Web. El proxy se puede compilar mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio Web a una biblioteca de .NET Standard.

Una alternativa a la creación del proxy mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 es usar la herramienta de utilidad de metadatos de ServiceModel (SvcUtil. exe). Para obtener más información, vea [herramienta de utilidad de metadatos de ServiceModel (SvcUtil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configuración del proxy

La configuración del proxy generado suele tomar dos argumentos de configuración (según SOAP 1.1/ASMX o WCF) durante la inicialización: el `EndpointAddress` y/o la información de enlace asociada, como se muestra en el ejemplo siguiente:

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Un enlace se utiliza para especificar los detalles de transporte, codificación y protocolo necesarios para que las aplicaciones y los servicios se comuniquen entre sí. La `BasicHttpBinding` especifica que los mensajes SOAP codificados por texto se enviarán a través del Protocolo de transporte HTTP. La especificación de una dirección de extremo permite que la aplicación se conecte a diferentes instancias del servicio WCF, siempre que haya varias instancias publicadas.

### <a name="consuming-the-proxy"></a>Consumir el proxy

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el modelo de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y finalizan la operación asincrónica.

El método *BeginOperationName* comienza la operación asincrónica y devuelve un objeto que implementa la interfaz `IAsyncResult`. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras que la operación asincrónica tiene lugar en un subproceso del grupo de subprocesos.

Para cada llamada a *BeginOperationName*, la aplicación también debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio Web sincrónico. El ejemplo de código siguiente muestra un ejemplo:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La biblioteca TPL (Task Parallel Library) puede simplificar el proceso de consumo de un par de métodos Begin/end de APM encapsulando las operaciones asincrónicas en el mismo `Task` objeto. Esta encapsulación la proporcionan varias sobrecargas del método `Task.Factory.FromAsync`. Este método crea un `Task` que ejecuta el método `TodoServiceClient.EndGetTodoItems` una vez que se completa el método `TodoServiceClient.BeginGetTodoItems`, con el parámetro `null` que indica que no se pasa ningún dato al delegado `BeginGetTodoItems`. Por último, el valor de la enumeración `TaskCreationOptions` especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación asincrónica .NET Framework tradicional](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

Para obtener más información acerca de cómo consumir un servicio WCF, consulte [consumo de un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Usar la seguridad de transporte

Los servicios WCF pueden emplear la seguridad de nivel de transporte para protegerse frente a la interceptación de mensajes. La plataforma Xamarin admite enlaces que emplean la seguridad de nivel de transporte mediante SSL. Sin embargo, puede haber casos en los que la pila tenga que validar el certificado, lo que provoca un comportamiento imprevisto. La validación se puede invalidar registrando un `ServerCertificateValidationCallback` delegado antes de invocar el servicio, como se muestra en el ejemplo de código siguiente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Esto mantiene el cifrado de transporte y omite la validación de certificados del servidor. Sin embargo, este enfoque no tiene en cuenta los problemas de confianza asociados con el certificado y puede no ser adecuado. Para obtener más información, consulte [uso de raíces de confianza](https://www.mono-project.com/UsingTrustedRootsRespectfully) de forma respetuosa en [Mono-Project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Uso de la seguridad de credenciales de cliente

Los servicios WCF también pueden requerir que los clientes del servicio se autentiquen con las credenciales. La plataforma Xamarin no admite el protocolo WS-Security, que permite a los clientes enviar credenciales dentro del sobre del mensaje SOAP. Sin embargo, la plataforma Xamarin admite la posibilidad de enviar credenciales de autenticación HTTP Basic al servidor especificando el `ClientCredentialType`adecuado:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

A continuación, se pueden especificar las credenciales de autenticación básica:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

En el ejemplo anterior, si obtiene el mensaje "se ha quedado sin trampolines de tipo 0", puede aumentar el número de tipo 0 trampolines agregando el argumento `–aot “trampolines={number of trampolines}”` a la compilación. Para más información, consulte [Solución de problemas](~/ios/troubleshooting/troubleshooting.md#trampolines).

Para obtener más información acerca de la autenticación HTTP Basic, aunque en el contexto de un servicio Web REST, consulte [autenticación de un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Vínculos relacionados

- [Servicios web en Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml)
- [Herramienta de utilidad de metadatos de ServiceModel (SvcUtil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
