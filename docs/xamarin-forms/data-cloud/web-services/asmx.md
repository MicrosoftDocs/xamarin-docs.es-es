---
title: Consumo de un servicio web ASP.NET (ASMX)
description: En este artículo se muestra cómo consumir un servicio SOAP de ASMX desde una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 316f79d72ade21fd068112e2d01660502d0ed0d2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366768"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Consumo de un servicio web ASP.NET (ASMX)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX proporciona la capacidad de compilar servicios web que envían mensajes mediante el Protocolo simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independiente del lenguaje para crear y obtener acceso a servicios Web. No es necesario que los consumidores de un servicio ASMX sepan nada sobre la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio SOAP de ASMX desde una Xamarin.Forms aplicación._

Un mensaje SOAP es un documento XML que contiene los elementos siguientes:

- Un elemento raíz denominado *Envelope* que identifica el documento XML como un mensaje SOAP.
- Un elemento de *encabezado* opcional que contiene información específica de la aplicación, como datos de autenticación. Si el elemento de *encabezado* está presente, debe ser el primer elemento secundario del elemento de *sobre* .
- Un elemento *Body* necesario que contiene el mensaje SOAP destinado al destinatario.
- Un elemento *Fault* opcional que se usa para indicar mensajes de error. Si el elemento *Fault* está presente, debe ser un elemento secundario del elemento *Body* .

SOAP puede operar a través de varios protocolos de transporte, incluidos HTTP, SMTP, TCP y UDP. Sin embargo, un servicio ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin admite implementaciones estándar de SOAP 1,1 a través de HTTP y esto incluye compatibilidad con muchas de las configuraciones estándar del servicio ASMX.

En este ejemplo se incluyen las aplicaciones móviles que se ejecutan en dispositivos físicos o emulados, y un servicio de ASMX que proporciona métodos para obtener, agregar, editar y eliminar datos. Cuando se ejecutan las aplicaciones móviles, se conectan al servicio ASMX hospedado localmente, tal como se muestra en la siguiente captura de pantalla:

![Aplicación de ejemplo](asmx-images/portal.png)

> [!NOTE]
> En iOS 9 y versiones posteriores, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación, lo que evita la divulgación accidental de información confidencial. Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> ATS puede no participar en si no es posible usar el `HTTPS` Protocolo y proteger la comunicación de los recursos de Internet. Esto se puede lograr actualizando el archivo **info. plist** de la aplicación. Para obtener más información, vea [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumo del servicio web

El servicio ASMX proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtención de una lista de tareas pendientes|
|CreateTodoItem|Crear un nuevo elemento de tareas pendientes|Un TodoItem serializado en XML|
|EditTodoItem|Actualizar una tarea pendiente|Un TodoItem serializado en XML|
|DeleteTodoItem|Eliminar una tarea pendiente|Un TodoItem serializado en XML|

Para obtener más información sobre el modelo de datos que se usa en la aplicación, vea [modelar los datos](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Crear el proxy de TodoService

Una clase de proxy, denominada `TodoService` , extiende `SoapHttpClientProtocol` y proporciona métodos para comunicarse con el servicio asmx a través de http. El proxy se genera agregando una referencia Web a cada proyecto específico de la plataforma en Visual Studio 2019 o Visual Studio 2017. La referencia Web genera métodos y eventos para cada acción definida en el documento del lenguaje de descripción de servicios web (WSDL) del servicio.

Por ejemplo, la `GetTodoItems` acción de servicio produce un `GetTodoItemsAsync` método y un `GetTodoItemsCompleted` evento en el proxy. El método generado tiene un tipo de valor devuelto void e invoca la `GetTodoItems` acción en la `SoapHttpClientProtocol` clase primaria. Cuando el método invocado recibe una respuesta del servicio, activa el `GetTodoItemsCompleted` evento y proporciona los datos de respuesta dentro de la propiedad del evento `Result` .

## <a name="create-the-isoapservice-implementation"></a>Crear la implementación de ISoapService

Para permitir que el proyecto compartido y multiplataforma funcione con el servicio, el ejemplo define la `ISoapService` interfaz, que sigue el [modelo de programación asincrónica de tareas en C#](/dotnet/csharp/programming-guide/concepts/async/). Cada plataforma implementa `ISoapService` para exponer el proxy específico de la plataforma. En el ejemplo `TaskCompletionSource` se usan objetos para exponer el proxy como una interfaz asincrónica de tarea. Los detalles sobre `TaskCompletionSource` el uso de se encuentran en las implementaciones de cada tipo de acción en las secciones siguientes.

El ejemplo `SoapService` :

1. Crea `TodoService` una instancia de como una instancia de nivel de clase.
1. Crea una colección denominada `Items` para almacenar `TodoItem` objetos.
1. Especifica un extremo personalizado para la `Url` propiedad opcional en el `TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>Crear objetos de transferencia de datos

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio Web, primero debe convertirse en el tipo generado por el proxy `TodoItem` . Esto se logra mediante el `ToASMXServiceTodoItem` método, tal y como se muestra en el ejemplo de código siguiente:

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
    return new ASMXService.TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

Este método crea una nueva `ASMService.TodoItem` instancia de y establece cada propiedad en la propiedad idéntica de la `TodoItem` instancia de.

Del mismo modo, cuando se recuperan datos del servicio Web, se deben convertir del tipo generado por el proxy `TodoItem` a una `TodoItem` instancia de. Esto se logra con el `FromASMXServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
    return new TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

Este método recupera los datos del tipo generado por el proxy `TodoItem` y lo establece en la instancia que se acaba de crear `TodoItem` .

### <a name="retrieve-data"></a>Recuperación de datos

La `ISoapService` interfaz espera que el `RefreshDataAsync` método devuelva un `Task` con la colección de elementos. Sin embargo, el `TodoService.GetTodoItemsAsync` método devuelve void. Para satisfacer el patrón de interfaz, debe llamar a `GetTodoItemsAsync` , esperar a `GetTodoItemsCompleted` que se desencadene el evento y rellenar la colección. Esto le permite devolver una colección válida a la interfaz de usuario.

En el ejemplo siguiente se crea un nuevo `TaskCompletionSource` , se inicia la llamada asincrónica en el `RefreshDataAsync` método y se espera el `Task` proporcionado por `TaskCompletionSource` . Cuando `TodoService_GetTodoItemsCompleted` se invoca el controlador de eventos, rellena la `Items` colección y actualiza `TaskCompletionSource` :

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

Para obtener más información, vea [modelo de programación asincrónica](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) y [TPL y la programación asincrónica .NET Framework tradicional](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Crear o editar datos

Al crear o editar datos, debe implementar el `ISoapService.SaveTodoItemAsync` método. Este método detecta si `TodoItem` es un elemento nuevo o actualizado y llama al método adecuado en el `todoService` objeto. Los `CreateTodoItemCompleted` `EditTodoItemCompleted` controladores de eventos y también deben implementarse para que sepa cuándo `todoService` ha recibido una respuesta del servicio asmx (estos se pueden combinar en un único controlador porque realizan la misma operación). En el ejemplo siguiente se muestran las implementaciones de interfaces y controladores de eventos, así como el `TaskCompletionSource` objeto que se usa para operar de forma asincrónica:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>Eliminación de datos

La eliminación de datos requiere una implementación similar. Defina un `TaskCompletionSource` , implemente un controlador de eventos y el `ISoapService.DeleteTodoItemAsync` método:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>Prueba del servicio web

La prueba de dispositivos físicos o emulados con un servicio hospedado localmente requiere la configuración personalizada de IIS, las direcciones de extremo y las reglas de Firewall. Para obtener más información sobre cómo configurar el entorno para las pruebas, consulte [configurar el acceso remoto a IIS Express](wcf.md#configure-remote-access-to-iis-express). La única diferencia entre probar WCF y ASMX es el número de puerto de TodoService.

## <a name="related-links"></a>Vínculos relacionados

- [TodoASMX (ejemplo)](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](/dotnet/api/system.iasyncresult)