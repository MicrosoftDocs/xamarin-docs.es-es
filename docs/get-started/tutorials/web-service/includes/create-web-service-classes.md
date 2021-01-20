---
ms.openlocfilehash: 7a5acca4169b1f763e178e0a05b01548765ff45d
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570811"
---
Las solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que utilizan los exploradores web para recuperar páginas y enviar datos a los servidores. En este ejercicio, creará una clase que use el verbo GET para recuperar datos de repositorio para .NET de la API web de GitHub.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `Constants` al proyecto **WebServiceTutorial**. Después, en **Constants.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    Este código define una única constante, que es el punto de conexión en el que se efectuarán las solicitudes web.

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `Repository` al proyecto **WebServicesTutorial**. Después, en **Repository.cs**, quite todo el código de la plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    Este código define una clase `Repository` que se usa para modelar los datos JSON recuperados del servicio web. Cada propiedad está decorada con un atributo `JsonProperty`, que contiene un nombre de campo JSON. Newtonsoft.Json usará esta asignación de nombres de campo JSON a las propiedades CLR al deserializar los datos JSON en objetos de modelo.

    > [!NOTE]
    > La definición de clase anterior se ha simplificado, y no modela completamente los datos JSON recuperados del servicio web.

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `RestService` al proyecto **WebServiceTutorial**. Después, en **RestService.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    Este código define un único método, `GetRepositoriesAsync`, que recupera datos de repositorio para .NET de la API web de GitHub. Este método utiliza el método `HttpClient.GetAsync` para enviar una solicitud GET a la API web especificada mediante el argumento `uri`. La API web envía una respuesta que se almacena en un objeto `HttpResponseMessage`. La respuesta incluye un código de estado HTTP, que indica si la solicitud HTTP se realizó correctamente o no. Siempre que la solicitud se realice correctamente, la API web responde con el código de estado HTTP 200 (OK) y una respuesta JSON, que se encuentra en la propiedad `HttpResponseMessage.Content`. Estos datos JSON se leen en un elemento `string` mediante el método `HttpContent.ReadAsStringAsync` antes de que se deserialicen en un objeto `List<Repository>` mediante el método `JsonConvert.DeserializeObject`. Este método usa las asignaciones entre los nombres de campo JSON y las propiedades CLR, que se definen en la clase `Repository`, para realizar la deserialización.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, agregue una nueva clase denominada `Constants` al proyecto **WebServiceTutorial**. Después, en **Constants.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    Este código define una única constante, que es el punto de conexión en el que se efectuarán las solicitudes web.

1. En el **Panel de solución**, agregue una nueva clase denominada `Repository` al proyecto **WebServicesTutorial**. Después, en **Repository.cs**, quite todo el código de la plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    Este código define una clase `Repository` que se usa para modelar los datos JSON recuperados del servicio web. Cada propiedad está decorada con un atributo `JsonProperty`, que contiene un nombre de campo JSON. Newtonsoft.Json usará esta asignación de nombres de campo JSON a las propiedades CLR al deserializar los datos JSON en objetos de modelo.

    > [!NOTE]
    > La definición de clase anterior se ha simplificado, y no modela completamente los datos JSON recuperados del servicio web.

1. En el **Panel de solución**, agregue una nueva clase denominada `RestService` al proyecto **WebServiceTutorial**. Después, en **RestService.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    Este código define un único método, `GetRepositoriesAsync`, que recupera datos de repositorio para .NET de la API web de GitHub. Este método utiliza el método `HttpClient.GetAsync` para enviar una solicitud GET a la API web especificada mediante el argumento `uri`. La API web envía una respuesta que se almacena en un objeto `HttpResponseMessage`. La respuesta incluye un código de estado HTTP, que indica si la solicitud HTTP se realizó correctamente o no. Siempre que la solicitud se realice correctamente, la API web responde con el código de estado HTTP 200 (OK) y una respuesta JSON, que se encuentra en la propiedad `HttpResponseMessage.Content`. Estos datos JSON se leen en un elemento `string` mediante el método `HttpContent.ReadAsStringAsync` antes de que se deserialicen en un objeto `List<Repository>` mediante el método `JsonConvert.DeserializeObject`. Este método usa las asignaciones entre los nombres de campo JSON y las propiedades CLR, que se definen en la clase `Repository`, para realizar la deserialización.

1. Compile la solución para asegurarse de que no haya errores.
