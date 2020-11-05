---
title: Acceso a datos remotos
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers accede a los datos desde los microservicios en contenedores.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b21fecf139262a88fbbe3de3ea8129cc0f9b211e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373359"
---
# <a name="accessing-remote-data"></a>Acceso a datos remotos

> [!NOTE]
> Este libro electrónico se publicó en el muelle de 2017 y no se ha actualizado desde entonces. Hay mucho en el libro que sigue siendo útil, pero parte del material es obsoleto.

Muchas soluciones modernas basadas en Web hacen uso de servicios Web, hospedados por servidores Web, para proporcionar funcionalidad a las aplicaciones cliente remotas. Las operaciones que expone un servicio web constituyen una API web.

Las aplicaciones cliente deben ser capaces de usar la API Web sin saber cómo se implementan los datos o las operaciones que expone la API. Esto requiere que la API se atiene a los estándares comunes que permiten a una aplicación cliente y a un servicio web aceptar los formatos de datos que se van a usar y la estructura de los datos que se intercambian entre las aplicaciones cliente y el servicio Web.

## <a name="introduction-to-representational-state-transfer"></a>Introducción a la transferencia de estado representacional

La transferencia de estado representacional (REST) es un estilo arquitectónico para la creación de sistemas distribuidos basados en Hypermedia. Una ventaja principal del modelo REST es que se basa en estándares abiertos y no enlaza la implementación del modelo o las aplicaciones cliente que acceden a ella a cualquier implementación específica. Por lo tanto, un servicio Web REST podría implementarse mediante Microsoft ASP.NET MVC y las aplicaciones cliente podrían desarrollarse con cualquier lenguaje y conjunto de herramientas que pueda generar solicitudes HTTP y analizar las respuestas HTTP.

El modelo REST usa un esquema de navegación para representar objetos y servicios a través de una red, denominados recursos. Los sistemas que implementan REST normalmente usan el protocolo HTTP para transmitir solicitudes para tener acceso a estos recursos. En estos sistemas, una aplicación cliente envía una solicitud en forma de un URI que identifica un recurso y un método HTTP (como GET, POST, PUT o DELETE) que indica la operación que se va a realizar en ese recurso. El cuerpo de la solicitud HTTP contiene los datos necesarios para realizar la operación.

> [!NOTE]
> REST define un modelo de solicitud sin estado. Por lo tanto, las solicitudes HTTP deben ser independientes y pueden producirse en cualquier orden.

La respuesta de una solicitud de REST hace uso de códigos de Estado HTTP estándar. Por ejemplo, una solicitud que devuelve datos válidos debe incluir el código de respuesta HTTP 200 (correcto), mientras que una solicitud que no encuentra o elimina un recurso especificado debe devolver una respuesta que incluya el código de estado HTTP 404 (no encontrado).

Una API Web de RESTful expone un conjunto de recursos conectados y proporciona las operaciones básicas que permiten a una aplicación manipular esos recursos y desplazarse fácilmente entre ellos. Por esta razón, los URI que constituyen una API web RESTful típica están orientados a los datos que expone y usan los recursos proporcionados por HTTP para operar en estos datos.

Los datos que incluye una aplicación cliente en una solicitud HTTP, y los mensajes de respuesta correspondientes del servidor Web, se pueden presentar en diversos formatos, conocidos como tipos de medios. Cuando una aplicación cliente envía una solicitud que devuelve datos en el cuerpo de un mensaje, puede especificar los tipos de medios que puede controlar en el `Accept` encabezado de la solicitud. Si el servidor web es compatible con este tipo de medio, puede responder con una respuesta que incluye el `Content-Type` encabezado que especifica el formato de los datos en el cuerpo del mensaje. Después, es responsabilidad de la aplicación cliente analizar el mensaje de respuesta e interpretar los resultados en el cuerpo del mensaje de forma adecuada.

Para obtener más información acerca de REST, consulte [API Design](/azure/architecture/best-practices/api-design/) e [implementación de API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Consumo de API de RESTful

La aplicación móvil eShopOnContainers usa el patrón Model-View-ViewModel (MVVM) y los elementos de modelo del patrón representan las entidades de dominio que se usan en la aplicación. Las clases del controlador y del repositorio de la aplicación de referencia eShopOnContainers aceptan y devuelven muchos de estos objetos de modelo. Por lo tanto, se usan como objetos de transferencia de datos (DTO) que contienen todos los datos que se pasan entre la aplicación móvil y los microservicios en contenedores. La principal ventaja de usar dto para pasar datos a un servicio Web y recibir datos de este es que, al transmitir más datos en una única llamada remota, la aplicación puede reducir el número de llamadas remotas que se deben realizar.

### <a name="making-web-requests"></a>Realización de las solicitudes web

La aplicación móvil eShopOnContainers usa la `HttpClient` clase para hacer solicitudes a través de http, con JSON que se usa como tipo de medio. Esta clase proporciona la funcionalidad para enviar de forma asincrónica solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI. La `HttpResponseMessage` clase representa un mensaje de respuesta http recibido de una API de REST después de realizar una solicitud HTTP. Contiene información sobre la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. La `HttpContent` clase representa el cuerpo http y los encabezados de contenido, como `Content-Type` y `Content-Encoding` . El contenido se puede leer mediante cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync` , dependiendo del formato de los datos.

#### <a name="making-a-get-request"></a>Realización de una solicitud GET

La `CatalogService` clase se utiliza para administrar el proceso de recuperación de datos desde el microservicio de catálogo. En el `RegisterDependencies` método de la `ViewModelLocator` clase, la `CatalogService` clase se registra como una asignación de tipo en el `ICatalogService` tipo con el contenedor de inyección de dependencia de Autofac. A continuación, cuando se crea una instancia de la `CatalogViewModel` clase, su constructor acepta un `ICatalogService` tipo, que Autofac resuelve, y devuelve una instancia de la `CatalogService` clase. Para obtener más información sobre la inserción de dependencias, vea [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection).

En la figura 10-1 se muestra la interacción de las clases que leen los datos del catálogo del microservicio de catálogo para que los muestre el `CatalogView` .

[![Recuperación de datos del microservicio de catálogo](accessing-remote-data-images/catalogdata.png)](accessing-remote-data-images/catalogdata-large.png#lightbox "Recuperación de datos del microservicio de catálogo")

**Figura 10-1** : recuperación de datos del microservicio de catálogo

Cuando `CatalogView` se navega a, `OnInitialize` se llama al método de la `CatalogViewModel` clase. Este método recupera los datos del catálogo del microservicio de catálogo, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Este método llama al `GetCatalogAsync` método de la `CatalogService` instancia que se ha insertado en el `CatalogViewModel` Autofac. El siguiente ejemplo de código muestra el método `GetCatalogAsync`:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Este método genera el URI que identifica el recurso al que se enviará la solicitud y utiliza la `RequestProvider` clase para invocar el método HTTP GET en el recurso, antes de devolver los resultados a `CatalogViewModel` . La `RequestProvider` clase contiene funcionalidad que envía una solicitud en forma de un URI que identifica un recurso, un método HTTP que indica la operación que se va a realizar en ese recurso y un cuerpo que contiene los datos necesarios para realizar la operación. Para obtener información sobre cómo `RequestProvider` se inserta la clase en `CatalogService class` , vea [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection).

En el ejemplo de código siguiente se muestra el `GetAsync` método en la `RequestProvider` clase:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama al `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuado. A continuación, envía una solicitud GET asincrónica al recurso identificado por el URI, con la respuesta almacenada en la `HttpResponseMessage` instancia. `HandleResponse`A continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado http correcto. A continuación, la respuesta se lee como una cadena, se convierte de JSON a un `CatalogRoot` objeto y se devuelve a `CatalogService` .

El `CreateHttpClient` método se muestra en el ejemplo de código siguiente:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Este método crea una nueva instancia de la `HttpClient` clase y establece el `Accept` encabezado de las solicitudes realizadas por la `HttpClient` instancia en `application/json` , lo que indica que espera que el contenido de cualquier respuesta esté formateado mediante JSON. Después, si se pasa un token de acceso como argumento al `CreateHttpClient` método, se agrega al `Authorization` encabezado de las solicitudes realizadas por la `HttpClient` instancia, con el prefijo de la cadena `Bearer` . Para obtener más información sobre la autorización, vea [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Cuando el `GetAsync` método de la `RequestProvider` clase llama a `HttpClient.GetAsync` , `Items` se invoca el método de la `CatalogController` clase en el proyecto Catalog. API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Este método recupera los datos del catálogo de SQL Database mediante EntityFramework y lo devuelve como un mensaje de respuesta que incluye un código de Estado HTTP correcto y una colección de instancias con formato JSON `CatalogItem` .

#### <a name="making-a-post-request"></a>Realización de una solicitud POST

La `BasketService` clase se utiliza para administrar el proceso de recuperación y actualización de datos con el microservicio basket. En el `RegisterDependencies` método de la `ViewModelLocator` clase, la `BasketService` clase se registra como una asignación de tipo en el `IBasketService` tipo con el contenedor de inyección de dependencia de Autofac. A continuación, cuando se crea una instancia de la `BasketViewModel` clase, su constructor acepta un `IBasketService` tipo, que Autofac resuelve, y devuelve una instancia de la `BasketService` clase. Para obtener más información sobre la inserción de dependencias, vea [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection).

En la figura 10-2 se muestra la interacción de las clases que envían los datos de la cesta mostrados por el `BasketView` microservicio de la cesta.

[![Envío de datos al microservicio de la cesta](accessing-remote-data-images/basketdata.png)](accessing-remote-data-images/basketdata-large.png#lightbox "Envío de datos al microservicio de la cesta")

**Figura 10-2** : envío de datos al microservicio de la cesta

Cuando se agrega un elemento a la cesta de la compra, `ReCalculateTotalAsync` se llama al método de la `BasketViewModel` clase. Este método actualiza el valor total de los elementos de la cesta y envía los datos de la cesta al microservicio de la cesta, tal como se muestra en el ejemplo de código siguiente:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Este método llama al `UpdateBasketAsync` método de la `BasketService` instancia que se ha insertado en el `BasketViewModel` Autofac. El método siguiente muestra el `UpdateBasketAsync` método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Este método genera el URI que identifica el recurso al que se enviará la solicitud y utiliza la `RequestProvider` clase para invocar el método http post en el recurso, antes de devolver los resultados a `BasketViewModel` . Tenga en cuenta que se requiere un token de acceso, Obtenido de IdentityServer durante el proceso de autenticación, para autorizar las solicitudes al microservicio de la cesta. Para obtener más información sobre la autorización, vea [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

En el ejemplo de código siguiente se muestra uno de los `PostAsync` métodos de la `RequestProvider` clase:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama al `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuado. A continuación, envía una solicitud POST asincrónica al recurso identificado por el URI, con los datos serializados de la cesta que se envían en formato JSON y la respuesta que se almacena en la `HttpResponseMessage` instancia. `HandleResponse`A continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado http correcto. A continuación, la respuesta se lee como una cadena, se convierte de JSON a un `CustomerBasket` objeto y se devuelve a `BasketService` . Para obtener más información sobre el `CreateHttpClient` método, vea [realizar una solicitud GET](#making-a-get-request).

Cuando el `PostAsync` método de la `RequestProvider` clase llama a `HttpClient.PostAsync` , `Post` se invoca el método de la `BasketController` clase en el proyecto basket. API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Este método usa una instancia de la `RedisBasketRepository` clase para conservar los datos de la cesta en la caché en Redis y lo devuelve como un mensaje de respuesta que incluye un código de estado http correcto y una instancia con formato JSON `CustomerBasket` .

#### <a name="making-a-delete-request"></a>Realización de una solicitud de eliminación

En la figura 10-3 se muestran las interacciones de las clases que eliminan los datos de la cesta del microservicio de la cesta para `CheckoutView` .

![Eliminación de datos del microservicio de cesta](accessing-remote-data-images/checkoutdata.png)

**Figura 10-3** : eliminación de datos del microservicio de la cesta

Cuando se invoca el proceso de desprotección, `CheckoutAsync` se llama al método de la `CheckoutViewModel` clase. Este método crea un pedido nuevo antes de borrar la cesta de la compra, tal y como se muestra en el ejemplo de código siguiente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Este método llama al `ClearBasketAsync` método de la `BasketService` instancia que se ha insertado en el `CheckoutViewModel` Autofac. El método siguiente muestra el `ClearBasketAsync` método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Este método genera el URI que identifica el recurso al que se enviará la solicitud y utiliza la `RequestProvider` clase para invocar el método HTTP DELETE en el recurso. Tenga en cuenta que se requiere un token de acceso, Obtenido de IdentityServer durante el proceso de autenticación, para autorizar las solicitudes al microservicio de la cesta. Para obtener más información sobre la autorización, vea [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

En el ejemplo de código siguiente se muestra el `DeleteAsync` método en la `RequestProvider` clase:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Este método llama al `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuado. A continuación, envía una solicitud DELETE asincrónica al recurso identificado por el URI. Para obtener más información sobre el `CreateHttpClient` método, vea [realizar una solicitud GET](#making-a-get-request).

Cuando el `DeleteAsync` método de la `RequestProvider` clase llama a `HttpClient.DeleteAsync` , `Delete` se invoca el método de la `BasketController` clase en el proyecto basket. API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Este método usa una instancia de la `RedisBasketRepository` clase para eliminar los datos de la cesta de la caché en Redis.

## <a name="caching-data"></a>Almacenar datos en caché

El rendimiento de una aplicación se puede mejorar mediante el almacenamiento en caché de los datos de acceso frecuente a un almacenamiento rápido que se encuentra cerca de la aplicación. Si el almacenamiento rápido se encuentra más cerca de la aplicación que el origen original, el almacenamiento en caché puede mejorar considerablemente los tiempos de respuesta al recuperar los datos.

La forma más común de almacenamiento en caché es el almacenamiento en caché de lectura, donde una aplicación recupera datos haciendo referencia a la memoria caché. Si los datos no se están en la caché, se recuperan del almacén de datos y se agregan a la caché. Las aplicaciones pueden implementar el almacenamiento en caché de lectura a través con el patrón de reserva de caché. Este modelo determina si el elemento se encuentra actualmente en la memoria caché. Si el elemento no está en la memoria caché, se lee desde el almacén de datos y se agrega a la memoria caché. Para obtener más información, consulte el patrón de [reserva de caché](/azure/architecture/patterns/cache-aside/) .

> [!TIP]
> Almacenar en caché los datos que se leen con frecuencia y que cambian con poca frecuencia. Estos datos se pueden agregar a la memoria caché a petición la primera vez que una aplicación los recupera. Esto significa que la aplicación debe capturar los datos solo una vez desde el almacén de datos y que el acceso posterior se puede satisfacer mediante la memoria caché.

Las aplicaciones distribuidas, como la aplicación de referencia eShopOnContainers, deben proporcionar una o las dos cachés siguientes:

- Una memoria caché compartida, a la que se puede tener acceso a través de varios procesos o equipos.
- Una caché privada, donde los datos se mantienen localmente en el dispositivo que ejecuta la aplicación.

La aplicación móvil eShopOnContainers usa una caché privada, donde los datos se mantienen localmente en el dispositivo que ejecuta una instancia de la aplicación. Para obtener información sobre la memoria caché utilizada por la aplicación de referencia eShopOnContainers, consulte [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

> [!TIP]
> Piense en la memoria caché como un almacén de datos transitorios que podría desaparecer en cualquier momento. Asegúrese de que los datos se mantienen en el almacén de datos original, así como en la memoria caché. Las posibilidades de perder datos se minimizan si la memoria caché deja de estar disponible.

### <a name="managing-data-expiration"></a>Administrar la expiración de los datos

No resulta práctico esperar que los datos almacenados en caché siempre sean coherentes con los datos originales. Los datos del almacén de datos original podrían cambiar después de que se haya almacenado en caché, lo que hace que los datos almacenados en caché queden obsoletos. Por lo tanto, las aplicaciones deben implementar una estrategia que ayude a garantizar que los datos de la memoria caché están tan actualizados como sea posible, pero también pueden detectar y controlar las situaciones que surgen cuando los datos de la memoria caché se han quedado obsoletos. La mayoría de los mecanismos de almacenamiento en caché permiten configurar la memoria caché para que expiren los datos y, por tanto, reducir el período de validez de los datos.

> [!TIP]
> Establecer una hora de expiración predeterminada al configurar una caché. Muchas cachés implementan la expiración, que invalida los datos y los quita de la memoria caché si no se tiene acceso a ellos durante un período especificado. Sin embargo, se debe tener cuidado al elegir el período de expiración. Si se hace demasiado corto, los datos expirarán demasiado rápidamente y se reducirán las ventajas del almacenamiento en caché. Si es demasiado largo, los riesgos de los datos se vuelven obsoletos. Por lo tanto, el tiempo de expiración debe coincidir con el patrón de acceso para las aplicaciones que usan los datos.

Cuando expiren los datos almacenados en caché, deben quitarse de la memoria caché y la aplicación debe recuperar los datos del almacén de datos original y volver a colocarlos en la memoria caché.

También es posible que se llene una memoria caché si se permite que los datos permanezcan durante un período de tiempo demasiado largo. Por lo tanto, es posible que se necesiten solicitudes para agregar nuevos elementos a la memoria caché para quitar algunos elementos de un proceso conocido como *expulsión*. Normalmente, los servicios de almacenamiento en caché expulsan los datos en una base de uso menos reciente. Sin embargo, hay otras directivas de expulsión, como las usadas más recientemente y las primeras en salir. Para obtener más información, consulte la [Guía de almacenamiento en caché](/azure/architecture/best-practices/caching/).

### <a name="caching-images"></a>Almacenar imágenes en caché

La aplicación móvil eShopOnContainers consume imágenes de producto remotas que se benefician de ser almacenadas en caché. El control muestra estas imágenes [`Image`](xref:Xamarin.Forms.Image) y el `CachedImage` control proporcionado por la biblioteca [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) .

El Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) control admite el almacenamiento en caché de imágenes descargadas. El almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen localmente durante 24 horas. Además, la hora de expiración se puede configurar con la [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) propiedad. Para obtener más información, vea el [almacenamiento en caché de imágenes descargado](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

`CachedImage`El control de FFImageLoading es un reemplazo del Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) control que proporciona propiedades adicionales que permiten la funcionalidad complementaria. Entre esta funcionalidad, el control proporciona el almacenamiento en caché configurable, al tiempo que admite el error y la carga de marcadores de posición de imagen. En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers usa el `CachedImage` control en `ProductTemplate` , que es la plantilla de datos que usa el [`ListView`](xref:Xamarin.Forms.ListView) control en el `CatalogView` :

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

El `CachedImage` control establece las `LoadingPlaceholder` `ErrorPlaceholder` propiedades y en imágenes específicas de la plataforma. La `LoadingPlaceholder` propiedad especifica la imagen que se va a mostrar mientras se recupera la imagen especificada por la `Source` propiedad y la `ErrorPlaceholder` propiedad especifica la imagen que se va a mostrar si se produce un error al intentar recuperar la imagen especificada por la `Source` propiedad.

Como su nombre implica, el `CachedImage` control almacena en caché las imágenes remotas en el dispositivo durante el tiempo especificado por el valor de la `CacheDuration` propiedad. Cuando este valor de propiedad no se establece explícitamente, se aplica el valor predeterminado de 30 días.

## <a name="increasing-resilience"></a>Aumento de la resistencia

Todas las aplicaciones que se comunican con servicios y recursos remotos deben ser sensibles a errores transitorios. Los errores transitorios incluyen la pérdida momentánea de conectividad de red con los servicios, la falta de disponibilidad temporal de un servicio o los tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser de corrección automática y, si la acción se repite después de un retraso adecuado, es probable que tenga éxito.

Los errores transitorios pueden tener un gran impacto en la calidad percibida de una aplicación, incluso si se ha probado exhaustivamente en todas las circunstancias previsibles. Para asegurarse de que una aplicación que se comunica con los servicios remotos funciona de forma confiable, debe ser capaz de hacer lo siguiente:

- Detectar errores cuando se producen y determinar si es probable que los errores sean transitorios.
- Vuelva a intentar la operación si determina que es probable que el error sea transitorio y realice un seguimiento del número de veces que se ha reintentado la operación.
- Use una estrategia de reintento adecuada, que especifica el número de reintentos, el retraso entre cada intento y las acciones que se deben realizar después de un intento fallido.

Este control de errores transitorios se puede lograr ajustando todos los intentos de acceso a un servicio remoto en el código que implementa el patrón de reintento.

### <a name="retry-pattern"></a>Patrón de reintento

Si una aplicación detecta un error al intentar enviar una solicitud a un servicio remoto, puede controlar el error de cualquiera de las maneras siguientes:

- Reintentando la operación. La aplicación podría reintentar la solicitud de error inmediatamente.
- Volver a intentar la operación después de un retraso. La aplicación debe esperar una cantidad de tiempo adecuada antes de reintentar la solicitud.
- Cancelando la operación. La aplicación debe cancelar la operación y notificar una excepción.

La estrategia de reintentos debe ajustarse para que coincida con los requisitos empresariales de la aplicación. Por ejemplo, es importante optimizar el número de reintentos y el intervalo de reintento para la operación que se está intentando. Si la operación forma parte de una interacción del usuario, el intervalo de reintentos debe ser corto y solo unos pocos reintentos intentan evitar que los usuarios tengan que esperar una respuesta. Si la operación forma parte de un flujo de trabajo de ejecución prolongada, donde la cancelación o el reinicio del flujo de trabajo es costoso o requiere mucho tiempo, es adecuado esperar más tiempo entre los intentos y volver a intentarlo más veces.

> [!NOTE]
> Una estrategia de reintento agresiva con un retraso mínimo entre los intentos y un gran número de reintentos podría degradar un servicio remoto que se está ejecutando cerca o por la capacidad. Además, esta estrategia de reintento también podría afectar a la capacidad de respuesta de la aplicación si está intentando realizar una operación con errores continuamente.

Si todavía se produce un error en una solicitud después de un número de reintentos, es mejor para la aplicación evitar que las solicitudes posteriores vayan al mismo recurso y notifique un error. Después, después de un período establecido, la aplicación puede realizar una o varias solicitudes al recurso para ver si son correctas. Para obtener más información, consulte [patrón de disyuntor](#circuit-breaker-pattern).

> [!TIP]
> Nunca implemente un mecanismo de reintento infinito. Use un número finito de reintentos o implemente [el patrón de disyuntor para](/azure/architecture/patterns/circuit-breaker/) permitir que un servicio se recupere.

La aplicación móvil eShopOnContainers no implementa actualmente el patrón de reintento al realizar solicitudes Web de RESTful. Sin embargo, el `CachedImage` control, proporcionado por la biblioteca [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) , admite el control de errores transitorios mediante el reintento de carga de imágenes. Si se produce un error al cargar la imagen, se realizarán más intentos. La propiedad especifica el número de intentos `RetryCount` y los reintentos se producirán después de un retraso especificado por la `RetryDelay` propiedad. Si estos valores de propiedad no se establecen explícitamente, se aplican los valores predeterminados – 3 para la `RetryCount` propiedad y 250 ms para la `RetryDelay` propiedad. Para obtener más información sobre el `CachedImage` control, vea [almacenamiento en caché de imágenes](#caching-images).

La aplicación de referencia eShopOnContainers implementa el patrón de reintento. Para obtener más información, incluida una explicación de cómo combinar el patrón de reintento con la `HttpClient` clase, consulte [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

Para obtener más información sobre el patrón de reintento, vea el patrón de [reintento](/azure/architecture/patterns/retry/) .

### <a name="circuit-breaker-pattern"></a>Patrón de disyuntor

En algunas situaciones, pueden producirse errores debido a eventos anticipados que tardan más tiempo en corregirse. Estos errores pueden abarcar desde una pérdida parcial de conectividad hasta el error completo de un servicio. En estas situaciones, no es necesario que una aplicación vuelva a intentar una operación que es improbable que se realice correctamente y, en su lugar, debe aceptar que se ha producido un error en la operación y controlar este error en consecuencia.

El patrón de disyuntor puede impedir que una aplicación intente ejecutar repetidamente una operación que es probable que produzca un error, a la vez que permite que la aplicación detecte si el error se ha resuelto.

> [!NOTE]
> El propósito del patrón de disyuntor es diferente del patrón de reintento. El patrón de reintento permite que una aplicación vuelva a intentar una operación en la expectativa de que se realice correctamente. El patrón de disyuntor impide que una aplicación realice una operación que es probable que produzca un error.

Un disyuntor actúa como un proxy para las operaciones que podrían producir errores. El proxy debe supervisar el número de errores recientes que se han producido y utilizar esta información para decidir si permitir que la operación continúe o devolver una excepción inmediatamente.

La aplicación móvil eShopOnContainers no implementa actualmente el patrón de disyuntor. Sin embargo, el eShopOnContainers sí lo hace. Para obtener más información, vea [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

> [!TIP]
> Combine los patrones de reintento y de disyuntor. Una aplicación puede combinar los patrones de reintento y de disyuntor mediante el patrón de reintento para invocar una operación a través de un disyuntor. Sin embargo, la lógica de reintento debe tener en cuenta las excepciones devueltas por el disyuntor y dejar de reintentar la operación si este indica que un error no es transitorio.

Para obtener más información sobre el patrón de disyuntor, [consulte el patrón de disyuntor.](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>Resumen

Muchas soluciones modernas basadas en Web hacen uso de servicios Web, hospedados por servidores Web, para proporcionar funcionalidad a las aplicaciones cliente remotas. Las operaciones que expone un servicio web constituyen una API Web y las aplicaciones cliente deben ser capaces de usar la API Web sin saber cómo se implementan los datos o las operaciones que expone la API.

El rendimiento de una aplicación se puede mejorar mediante el almacenamiento en caché de los datos de acceso frecuente a un almacenamiento rápido que se encuentra cerca de la aplicación. Las aplicaciones pueden implementar el almacenamiento en caché de lectura a través con el patrón de reserva de caché. Este modelo determina si el elemento se encuentra actualmente en la memoria caché. Si el elemento no está en la memoria caché, se lee desde el almacén de datos y se agrega a la memoria caché.

Al comunicarse con las API Web, las aplicaciones deben tener en cuenta los errores transitorios. Los errores transitorios incluyen la pérdida momentánea de conectividad de red con los servicios, la falta de disponibilidad temporal de un servicio o los tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser de corrección automática y, si la acción se repite después de un retraso adecuado, es probable que se realice correctamente. Por lo tanto, las aplicaciones deben ajustar todos los intentos de acceso a una API Web en el código que implementa un mecanismo de control de errores transitorios.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
