---
title: Introducción a los servicios web
description: En esta guía se muestra cómo consumir diferentes tecnologías de servicios Web. Entre los temas tratados se incluyen la comunicación con servicios REST, servicios SOAP y servicios de Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: f914a158135d34b59fa3d1b95972c988a44dd36b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765981"
---
# <a name="introduction-to-web-services"></a>Introducción a los servicios web

_En esta guía se muestra cómo consumir diferentes tecnologías de servicios Web. Entre los temas tratados se incluyen la comunicación con servicios REST, servicios SOAP y servicios de Windows Communication Foundation._

Para que funcione correctamente, muchas aplicaciones móviles dependen de la nube y, por tanto, la integración de servicios web en aplicaciones móviles es un escenario común. La plataforma Xamarin admite el consumo de distintas tecnologías de servicios web e incluye compatibilidad integrada y de terceros para consumir servicios RESTful, ASMX y Windows Communication Foundation (WCF).

En el caso de los clientes que usan Xamarin. Forms, hay ejemplos completos que usan cada una de estas tecnologías en la documentación de [servicios Web de Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> En iOS 9, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial.
> Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Puede optar por ATS si no es posible usar el protocolo y proteger la `HTTPS` comunicación de los recursos de Internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se usan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **OBTENER** : esta operación se usa para recuperar datos desde el servicio web.
- **POST** : esta operación se usa para crear un nuevo elemento de datos en el servicio web.
- **COLOCAR** : esta operación se usa para actualizar un elemento de datos en el servicio web.
- **REVISIÓN** : esta operación se usa para actualizar un elemento de datos en el servicio web con la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. No se utiliza este verbo en la aplicación de ejemplo.
- **ELIMINAR** : esta operación se usa para eliminar un elemento de datos en el servicio web.

Se llama a las API de RESTful API que se adhieren a REST del servicio Web y se definen mediante:

- Un URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Tipo de medio de los datos, como JavaScript Object Notation (JSON).

La simplicidad de REST ha ayudado a hacer el método principal para tener acceso a servicios web en aplicaciones móviles.

## <a name="consuming-rest-services"></a>Consumo de servicios REST

Hay una serie de bibliotecas y clases que se pueden usar para consumir servicios REST, y en las subsecciones siguientes se describen. Para obtener más información sobre cómo consumir un servicio REST, consulte [consumo de un servicio web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Las [bibliotecas de cliente http](https://www.nuget.org/packages/Microsoft.Net.Http) de Microsoft `HttpClient` proporcionan la clase, que se utiliza para enviar y recibir solicitudes a través de http. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido del servicio web después de realizar una solicitud HTTP. Contiene información sobre la respuesta, incluido el código de estado, los encabezados y el cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

Para obtener más información sobre `HttpClient` la clase, vea [crear el objeto HTTPClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

La llamada a servicios `HTTPWebRequest` web con implica:

- Crear la instancia de solicitud para un URI determinado.
- Establecer varias propiedades HTTP en la instancia de la solicitud.
- Recuperar un `HttpWebResponse` de la solicitud.
- Lectura de datos de la respuesta.

Por ejemplo, el código siguiente recupera datos de la Servicio Web de la Biblioteca Nacional de medicina:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
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

En el ejemplo anterior se `HttpWebRequest` crea un que devolverá datos con formato JSON. Los datos se devuelven `HttpWebResponse`en un, desde `StreamReader` el que se puede obtener para leer los datos.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Otro enfoque para consumir servicios REST es usar la biblioteca [RestSharp](http://restsharp.org/) . RestSharp encapsula las solicitudes HTTP, incluida la compatibilidad para recuperar los resultados como contenido de cadena sin formato o como un C# objeto deserializado. Por ejemplo, el código siguiente realiza una solicitud a la Servicio Web de la Biblioteca Nacional de medicina y recupera los resultados como una cadena con formato JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm`es un método que tomará la cadena JSON sin formato de `RestSharp.RestResponse.Content` la propiedad y la convertirá C# en un objeto. La deserialización de los datos devueltos de los servicios web se describe más adelante en este artículo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Además de las clases disponibles en la biblioteca de clases base (BCL) mono, `HttpWebRequest`como y las C# bibliotecas de terceros, como RestSharp, las clases específicas de la plataforma también están disponibles para consumir servicios Web. Por ejemplo, en iOS, se `NSUrlConnection` pueden `NSMutableUrlRequest` usar las clases y.

En el ejemplo de código siguiente se muestra cómo llamar a la Servicio Web de la Biblioteca Nacional de medicina mediante clases de iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
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

Otra opción para llamar a los servicios web es la biblioteca de [pila de servicios](http://www.servicestack.net/) . Por ejemplo, el código siguiente muestra cómo usar el método de la `IServiceClient.GetAsync` pila de servicio para emitir una solicitud de servicio:

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

Servicios web rESTful normalmente utilizan mensajes JSON para devolver datos al cliente. JSON es un formato de intercambio de datos basado en texto que genera cargas compactos, lo que reduce los requisitos de ancho de banda cuando se envían datos. En esta sección, se examinarán los mecanismos para consumir respuestas RESTful en JSON y el código XML (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La plataforma Xamarin incluye compatibilidad con JSON fuera de la caja. Mediante el uso `JsonObject`de, los resultados se pueden recuperar como se muestra en el ejemplo de código siguiente:

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

Sin embargo, es importante tener en cuenta que las `System.Json` herramientas de cargan la totalidad de los datos en la memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

La [biblioteca de NewtonSoft JSON.net](http://www.newtonsoft.com/json) es una biblioteca ampliamente usada para serializar y deserializar mensajes JSON. En el ejemplo de código siguiente se muestra cómo usar JSON.NET para deserializar un mensaje JSON C# en un objeto:

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

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack. Text es una biblioteca de serialización de JSON diseñada para trabajar con la biblioteca de ServiceStack. En el ejemplo de código siguiente se muestra cómo analizar JSON `ServiceStack.Text.JsonObject`mediante un:

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

ASMX proporciona la capacidad de compilar servicios web que envían mensajes mediante el Protocolo simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma y lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP.

Un mensaje SOAP es un documento XML que contiene los siguientes elementos:

- Un elemento raíz denominado *sobres* que identifica el documento XML como un mensaje SOAP.
- Opcional *encabezado* elemento que contiene información específica de la aplicación, como datos de autenticación. Si el *encabezado* elemento está presente debe ser el primer elemento secundario de la *sobres* elemento.
- Obligatoria *cuerpo* elemento que contiene el mensaje SOAP, diseñado para el destinatario.
- Opcional *error* elemento que se usa para indicar los mensajes de error. Si el *error* elemento está presente, debe ser un elemento secundario de la *cuerpo* elemento.

SOAP puede operar en muchos protocolos de transporte, incluidos HTTP, SMTP, TCP y UDP. Sin embargo, un servicio de ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin es compatible con las implementaciones estándar de SOAP 1.1 a través de HTTP, y esto incluye compatibilidad con muchas de las configuraciones estándar de servicio ASMX.

### <a name="generating-a-proxy"></a>Generación de un proxy

Se debe generar un *proxy* para consumir un servicio asmx, lo que permite que la aplicación se conecte al servicio. El proxy se construye por consumir los metadatos del servicio que define los métodos y la configuración del servicio asociado. Estos metadatos se exponen como un documento de lenguaje de descripción de servicios web (WSDL) generado por el servicio Web. El proxy se compila con Visual Studio para Mac o Visual Studio para agregar una referencia Web para el servicio Web a los proyectos específicos de la plataforma.

La dirección URL del servicio web puede ser un recurso de origen remoto hospedado o del sistema de `file:///` archivos local accesible a través del prefijo de la ruta de acceso, por ejemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "La dirección URL del servicio web puede ser un recurso de origen remoto hospedado o del sistema de archivos local accesible a través del prefijo de la ruta de acceso del archivo")](images/add-webreference-dialog.png#lightbox)

Esto genera el proxy en la carpeta de referencias de servicio o web del proyecto. Dado que un proxy se genera código, no debe modificarse.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Agregar manualmente un proxy a un proyecto

Si tiene un proxy existente que se ha generado mediante herramientas compatibles, esta salida se puede consumir cuando se incluye como parte del proyecto. En Visual Studio para Mac, use la **.** . opción de menú para agregar el proxy. Además, esto requiere que se haga referencia a *System. Web. Services. dll* explícitamente mediante la **adición de referencias.** . diálogo.

### <a name="consuming-the-proxy"></a>Consumir el proxy

Las clases de proxy generadas proporcionan métodos para consumir el servicio web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón de una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. El ejemplo de código siguiente muestra un ejemplo:

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

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `Task.Factory.FromAsync` método. Este método crea un `Task` que ejecuta el `TodoService.EndGetTodoItems` método una vez que `TodoService.BeginGetTodoItems` se completa el método, con `null` el parámetro que indica que no se pasa `BeginGetTodoItems` ningún dato al delegado. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación asincrónica .NET Framework tradicional](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

Para obtener más información sobre cómo consumir un servicio ASMX, consulte [consumo de un servicio Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF es el marco unificado de Microsoft para la creación de aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables.

WCF describe un servicio con una variedad de contratos diferentes que se incluyen los siguientes:

- **Los contratos de datos** : definir las estructuras de datos que forman la base para el contenido dentro de un mensaje.
- **Contratos de mensaje** – redactar mensajes de los contratos de datos existente.
- **Contratos de error** : permitir que los errores de SOAP personalizados que se especifique.
- **Contratos de servicio** : especificar las operaciones que admiten servicios y los mensajes necesarios para interactuar con cada operación. También especifique ningún comportamiento de error personalizado que se puede asociar con las operaciones en cada servicio.

Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas funcionalidades que proporciona ASMX: los mensajes SOAP a través de HTTP.

> [!IMPORTANT]
> La compatibilidad con la plataforma de Xamarin para WCF se limita a los mensajes SOAP con codificación de texto a través de `BasicHttpBinding` http/https mediante la clase. Además, compatibilidad con WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.

### <a name="generating-a-proxy"></a>Generación de un proxy

Un *proxy* debe generarse para consumir un servicio WCF, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumir los metadatos del servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera el servicio web. El proxy se puede compilar mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio Web a una biblioteca de .NET Standard.

Una alternativa para crear al proxy con el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 consiste en utilizar ServiceModel Metadata Utility Tool (svcutil.exe). Para obtener más información, consulte [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configuración del proxy

La configuración del proxy generado suele tomar dos argumentos de configuración (según SOAP 1.1/asmx o WCF) durante la inicialización: `EndpointAddress` el y/o la información de enlace asociada, como se muestra en el ejemplo siguiente:

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

Un enlace se utiliza para especificar el transporte, codificación y detalles protocolares requeridos para que las aplicaciones y servicios para comunicarse entre sí. El `BasicHttpBinding` especifica que se enviarán los mensajes codificados en texto SOAP a través del protocolo de transporte HTTP. Especificación de una dirección de punto de conexión permite a la aplicación para conectarse a distintas instancias del servicio WCF, siempre que hay varias instancias publicadas.

### <a name="consuming-the-proxy"></a>Consumir el proxy

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. El ejemplo de código siguiente muestra un ejemplo:

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

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `Task.Factory.FromAsync` método. Este método crea un `Task` que ejecuta el `TodoServiceClient.EndGetTodoItems` método una vez que `TodoServiceClient.BeginGetTodoItems` se completa el método, con `null` el parámetro que indica que no se pasa `BeginGetTodoItems` ningún dato al delegado. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

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

Los servicios WCF también pueden requerir que los clientes del servicio se autentiquen con las credenciales. La plataforma Xamarin no admite el protocolo WS-Security, que permite a los clientes enviar credenciales dentro del sobre del mensaje SOAP. Sin embargo, la plataforma de Xamarin admite la posibilidad de enviar credenciales de autenticación HTTP Basic al servidor especificando lo `ClientCredentialType`adecuado:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

A continuación, se pueden especificar las credenciales de autenticación básica:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

En el ejemplo anterior, si recibe el mensaje "se ha quedado sin trampolines de tipo 0", puede aumentar el número de tipo 0 trampolines agregando el `–aot “trampolines={number of trampolines}”` argumento a la compilación. Para más información, consulte [Solución de problemas](~/ios/troubleshooting/troubleshooting.md#trampolines).

Para obtener más información acerca de la autenticación HTTP Basic, aunque en el contexto de un servicio Web REST, consulte [autenticación de un servicio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Vínculos relacionados

- [Servicios web en Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml)
- [Herramienta de utilidad de metadatos de ServiceModel (SvcUtil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
