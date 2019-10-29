---
title: Crear un servicio
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 658bb65c9f9dea2c68b782736de02d95df368dd3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024862"
---
# <a name="creating-a-service"></a>Crear un servicio

Los servicios de Xamarin. Android deben obedecer dos reglas inviolable de servicios Android:

- Deben extender el [`Android.App.Service`](xref:Android.App.Service).
- Deben decorarse con el [`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute).

Otro requisito de los servicios de Android es que se deben registrar en **archivo AndroidManifest. XML** y asignar un nombre único. Xamarin. Android registrará automáticamente el servicio en el manifiesto en tiempo de compilación con el atributo XML necesario.

Este fragmento de código es el ejemplo más sencillo de creación de un servicio en Xamarin. Android que cumple estos dos requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

En tiempo de compilación, Xamarin. Android registrará el servicio insertando el siguiente elemento XML en **archivo AndroidManifest. XML** (Observe que Xamarin. Android generó un nombre aleatorio para el servicio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Es posible compartir un servicio con otras aplicaciones de Android _exportándolos_ . Esto se logra estableciendo la propiedad `Exported` en el `ServiceAttribute`. Al exportar un servicio, también se debe establecer la propiedad `ServiceAttribute.Name` para proporcionar un nombre público significativo para el servicio. Este fragmento de código muestra cómo exportar y asignar un nombre a un servicio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

El elemento **archivo AndroidManifest. XML** para este servicio tendrá un aspecto similar al siguiente:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Los servicios tienen su propio ciclo de vida con métodos de devolución de llamada que se invocan cuando se crea el servicio. Exactamente qué métodos se invocan depende del tipo de servicio. Un servicio iniciado debe implementar distintos métodos de ciclo de vida que un servicio enlazado, mientras que un servicio híbrido debe implementar los métodos de devolución de llamada para un servicio iniciado y un servicio enlazado. Estos métodos son todos miembros de la clase `Service`; la forma en que se inicia el servicio determinará qué métodos de ciclo de vida se invocarán. Estos métodos de ciclo de vida se tratarán con más detalle más adelante.

De forma predeterminada, un servicio se iniciará en el mismo proceso que una aplicación de Android. Es posible iniciar un servicio en su propio proceso estableciendo la propiedad `ServiceAttribute.IsolatedProcess` en true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

El siguiente paso consiste en examinar cómo iniciar un servicio y, a continuación, pasar a examinar cómo implementar los tres tipos de servicios diferentes.

> [!NOTE]
> Un servicio se ejecuta en el subproceso de la interfaz de usuario, por lo que si se va a realizar algún trabajo que bloquea la interfaz de usuario, el servicio debe usar subprocesos para realizar el trabajo.

## <a name="starting-a-service"></a>Iniciar un servicio

La forma más básica de iniciar un servicio en Android es enviar una `Intent` que contiene metadatos para ayudar a identificar qué servicio debe iniciarse. Hay dos estilos diferentes de intenciones que se pueden usar para iniciar un servicio:

- La **intención explícita** &ndash; un _intento explícito_ identificará exactamente qué servicio se debe usar para completar una acción determinada. Un intento explícito puede considerarse como una letra que tiene una dirección específica; Android enrutará la intención al servicio que se identifica explícitamente. Este fragmento de código es un ejemplo del uso de un intento explícito para iniciar un servicio denominado `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

- **Intención implícita** &ndash; este tipo de intención identifica de manera flexible la de acción que el usuario desea realizar, pero se desconoce el servicio exacto para completar esa acción. Una intención implícita puede considerarse como una carta que se dirige "a quién puede afectar...".
    Android examinará el contenido de la intención y determinará si hay un servicio existente que coincida con la intención.

    Un _filtro de intención_ se utiliza para ayudar a hacer coincidir la intención implícita con un servicio registrado. Un filtro de intención es un elemento XML que se agrega a **archivo AndroidManifest. XML** , que contiene los metadatos necesarios para ayudar a que un servicio coincida con una intención implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Si Android tiene más de una coincidencia posible para una intención implícita, puede pedirle al usuario que seleccione el componente para controlar la acción:

![Captura de pantalla de un cuadro de diálogo de desambiguación](images/creating-a-service-01.png "Captura de pantalla de un cuadro de diálogo de desambiguación")

> [!IMPORTANT]
> A partir de Android 5,0 (nivel de AP 21), no se puede usar una intención implícita para iniciar un servicio.

Siempre que sea posible, las aplicaciones deben usar intentos explícitos para iniciar un servicio. Una intención implícita no pide que se inicie un servicio específico &ndash; es una solicitud de algún servicio instalado en el dispositivo para controlar la solicitud. Esta solicitud ambigua puede dar lugar a que el servicio equivocado controle la solicitud o que otra aplicación se inicie innecesariamente (lo que aumenta la presión de los recursos en el dispositivo).

La forma en que se envía el intento depende del tipo de servicio y se tratará con más detalle más adelante en las guías específicas de cada tipo de servicio.

### <a name="creating-an-intent-filter-for-implicit-intents"></a>Creación de un filtro de intención para intenciones IMPLÍCITAS

Para asociar un servicio con una intención implícita, una aplicación Android debe proporcionar algunos metadatos para identificar las capacidades del servicio. Los _filtros de intención_proporcionan estos metadatos. Los filtros de intención contienen información, como una acción o un tipo de datos, que deben estar presentes para iniciar un servicio. En Xamarin. Android, el filtro de intención se registra en **archivo AndroidManifest. XML** al decorar un servicio con el [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute). Por ejemplo, el código siguiente agrega un filtro de intención con una acción asociada de `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Como resultado, se incluye una entrada en el archivo **archivo AndroidManifest. xml** &ndash; una entrada que se empaqueta con la aplicación de forma análoga al ejemplo siguiente:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Con los aspectos básicos de un servicio de Xamarin. Android fuera de camino, vamos a examinar los distintos subtipos de servicios con más detalle.

## <a name="related-links"></a>Vínculos relacionados

- [Android. app. Service](xref:Android.App.Service)
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android. app. intención](xref:Android.Content.Intent)
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)
