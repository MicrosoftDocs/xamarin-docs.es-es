---
ms.openlocfilehash: 3b1603b6af5ebb5558c3cd764f41fdbe24351b9b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "68669833"
---
Las solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que utilizan los exploradores web para recuperar páginas y enviar datos a los servidores. En este ejercicio, creará una clase que utiliza el verbo GET para recuperar datos desde la API web [OpenWeatherMap](https://openweathermap.org/). Esta API web puede utilizarse para recuperar datos de pronóstico meteorológico para una ubicación especificada. El uso de esta API web requiere registrarse para una clave de API.

> [!div class="nextstepaction"]
> [Registro para obtener una clave de API](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `Constants` al proyecto **WebServiceTutorial**. Después, en **Constants.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Este código define dos constantes. La constante `OpenWeatherMapEndpoint` define el punto de conexión en el que se realizarán solicitudes web, y la constante `OpenWeatherMapAPIKey` define la clave de API personal para el servicio [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Debe establecer la clave de API de OpenWeatherMap personal como el valor de la constante `OpenWeatherMapAPIKey`.

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `WeatherData` al proyecto **WebServicesTutorial**. Después, en **WeatherData.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Este código define cuatro clases que se usan para modelar los datos JSON recuperados del servicio web. Cada propiedad está decorada con un atributo `JsonProperty`, que contiene un nombre de campo JSON. Newtonsoft.Json usará esta asignación de nombres de campo JSON a las propiedades CLR al deserializar los datos JSON en objetos de modelo.

    > [!NOTE]
    > Las definiciones de clase anteriores se han simplificado y no modelan completamente los datos JSON recuperados del servicio web. Para obtener un ejemplo de modelo de datos completo, vea el ejemplo de [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. En el **Explorador de soluciones**, agregue una nueva clase denominada `RestService` al proyecto **WebServiceTutorial**. Después, en **RestService.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
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

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Este código define un único método, `GetWeatherDataAsync`, que recupera datos meteorológicos de una ubicación especificada de la API web [OpenWeatherMap](https://openweathermap.org/). Este método utiliza el método `HttpClient.GetAsync` para enviar una solicitud GET a la API web especificada mediante el argumento `uri`. La API web envía una respuesta que se almacena en un objeto `HttpResponseMessage`. La respuesta incluye un código de estado HTTP, que indica si la solicitud HTTP se realizó correctamente o no. Siempre que la solicitud se realice correctamente, la API web responde con el código de estado HTTP 200 (OK) y una respuesta JSON, que se encuentra en la propiedad `HttpResponseMessage.Content`. Estos datos JSON se leen en un elemento `string` mediante el método `HttpContent.ReadAsStringAsync` antes de que se deserialicen en un objeto `WeatherData` mediante el método `JsonConvert.DeserializeObject`. Este método usa las asignaciones entre los nombres de campo JSON y las propiedades CLR, que se definen en la clase `WeatherData`, para realizar la deserialización.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, agregue una nueva clase denominada `Constants` al proyecto **WebServiceTutorial**. Después, en **Constants.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Este código define dos constantes. La constante `OpenWeatherMapEndpoint` define el punto de conexión en el que se realizarán solicitudes web, y la constante `OpenWeatherMapAPIKey` define la clave de API personal para el servicio [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Debe establecer la clave de API de OpenWeatherMap personal como el valor de la constante `OpenWeatherMapAPIKey`.

1. En el **Panel de solución**, agregue una nueva clase denominada `WeatherData` al proyecto **WebServicesTutorial**. Después, en **WeatherData.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Este código define cuatro clases que se usan para modelar los datos JSON recuperados del servicio web. Cada propiedad está decorada con un atributo `JsonProperty`, que contiene un nombre de campo JSON. Newtonsoft.Json usará esta asignación de nombres de campo JSON a las propiedades CLR al deserializar los datos JSON en objetos de modelo.

    > [!NOTE]
    > Las definiciones de clase anteriores se han simplificado y no modelan completamente los datos JSON recuperados del servicio web. Para obtener un ejemplo de modelo de datos completo, vea el ejemplo de [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. En el **Panel de solución**, agregue una nueva clase denominada `RestService` al proyecto **WebServiceTutorial**. Después, en **RestService.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System;
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

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Este código define un único método, `GetWeatherDataAsync`, que recupera datos meteorológicos de una ubicación especificada de la API web [OpenWeatherMap](https://openweathermap.org/). Este método utiliza el método `HttpClient.GetAsync` para enviar una solicitud GET a la API web especificada mediante el argumento `uri`. La API web envía una respuesta que se almacena en un objeto `HttpResponseMessage`. La respuesta incluye un código de estado HTTP, que indica si la solicitud HTTP se realizó correctamente o no. Siempre que la solicitud se realice correctamente, la API web responde con el código de estado HTTP 200 (OK) y una respuesta JSON, que se encuentra en la propiedad `HttpResponseMessage.Content`. Estos datos JSON se leen en un elemento `string` mediante el método `HttpContent.ReadAsStringAsync` antes de que se deserialicen en un objeto `WeatherData` mediante el método `JsonConvert.DeserializeObject`. Este método usa las asignaciones entre los nombres de campo JSON y las propiedades CLR, que se definen en la clase `WeatherData`, para realizar la deserialización.

1. Compile la solución para asegurarse de que no haya errores.
