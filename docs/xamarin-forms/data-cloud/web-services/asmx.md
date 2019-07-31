---
title: Consumo de un servicio Web ASP.NET (ASMX)
description: En este artículo se muestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 124cffaf827ebeb8109f3cd4ac4dfd2701e43f9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656655"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>Consumo de un servicio Web ASP.NET (ASMX)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX proporciona la capacidad de crear servicios web que envían mensajes mediante el protocolo Simple de acceso de objetos (SOAP). SOAP es un protocolo independiente de la plataforma y lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms._

Un mensaje SOAP es un documento XML que contiene los siguientes elementos:

- Un elemento raíz denominado *sobres* que identifica el documento XML como un mensaje SOAP.
- Opcional *encabezado* elemento que contiene información específica de la aplicación, como datos de autenticación. Si el *encabezado* elemento está presente debe ser el primer elemento secundario de la *sobres* elemento.
- Obligatoria *cuerpo* elemento que contiene el mensaje SOAP, diseñado para el destinatario.
- Opcional *error* elemento que se usa para indicar los mensajes de error. Si el *error* elemento está presente, debe ser un elemento secundario de la *cuerpo* elemento.

SOAP puede operar en muchos protocolos de transporte, incluidos HTTP, SMTP, TCP y UDP. Sin embargo, un servicio de ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin es compatible con las implementaciones estándar de SOAP 1.1 a través de HTTP, y esto incluye compatibilidad con muchas de las configuraciones estándar de servicio ASMX.

En este ejemplo se incluyen las aplicaciones móviles que se ejecutan en dispositivos físicos o emulados, y un servicio de ASMX que proporciona métodos para obtener, agregar, editar y eliminar datos. Cuando se ejecutan las aplicaciones móviles, se conectan al servicio ASMX hospedado localmente, tal como se muestra en la siguiente captura de pantalla:

![](asmx-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumo del servicio Web

El servicio ASMX proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/web-services/introduction.md).

## <a name="create-the-todoservice-proxy"></a>Crear el proxy de TodoService

Una clase de proxy, `TodoService`denominada, `SoapHttpClientProtocol` extiende y proporciona métodos para comunicarse con el servicio asmx a través de http. El proxy se genera agregando una referencia Web a cada proyecto específico de la plataforma en Visual Studio 2019 o Visual Studio 2017. La referencia Web genera métodos y eventos para cada acción definida en el documento del lenguaje de descripción de servicios web (WSDL) del servicio.

Por ejemplo, la `GetTodoItems` acción de servicio produce un `GetTodoItemsAsync` método y un `GetTodoItemsCompleted` evento en el proxy. El método generado tiene un tipo de valor devuelto void e `GetTodoItems` invoca la acción en `SoapHttpClientProtocol` la clase primaria. Cuando el método invocado recibe una respuesta del servicio, activa el `GetTodoItemsCompleted` evento y proporciona los datos de respuesta dentro de la propiedad del `Result` evento.

## <a name="create-the-isoapservice-implementation"></a>Crear la implementación de ISoapService

Para permitir que el proyecto compartido y multiplataforma funcione con el servicio, el ejemplo define la `ISoapService` interfaz, que sigue el [modelo de programación asincrónica de tareas en C# ](/dotnet/csharp/programming-guide/concepts/async/). Cada plataforma implementa `ISoapService` para exponer el proxy específico de la plataforma. En el ejemplo `TaskCompletionSource` se usan objetos para exponer el proxy como una interfaz asincrónica de tarea. Los detalles sobre `TaskCompletionSource` el uso de se encuentran en las implementaciones de cada tipo de acción en las secciones siguientes.

El ejemplo `SoapService`:

1. Crea una instancia de comounainstanciadeniveldeclase.`TodoService`
1. Crea una colección denominada `Items` para almacenar `TodoItem` objetos.
1. Especifica un extremo personalizado para la propiedad `Url` opcional en el`TodoService`

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

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se consigue mediante la `ToASMXServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

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

De forma similar, cuando se recuperan datos desde el servicio web, se debe convertir desde el proxy generado `TodoItem` tipo a un `TodoItem` instancia. Esto se consigue con la `FromASMXServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

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

Este método recupera los datos del tipo generado `TodoItem` por el proxy y lo establece en la instancia que se acaba de crear. `TodoItem`

### <a name="retrieve-data"></a>Recuperación de datos

La `ISoapService` interfaz espera que el `RefreshDataAsync` método devuelva un `Task` con la colección de elementos. Sin embargo, `TodoService.GetTodoItemsAsync` el método devuelve void. Para satisfacer el patrón de interfaz, debe llamar `GetTodoItemsAsync`a, esperar a `GetTodoItemsCompleted` que se desencadene el evento y rellenar la colección. Esto le permite devolver una colección válida a la interfaz de usuario.

En el ejemplo siguiente se crea `TaskCompletionSource`un nuevo, se inicia la llamada `RefreshDataAsync` asincrónica en el método y se `Task` espera el proporcionado `TaskCompletionSource`por. Cuando se `TodoService_GetTodoItemsCompleted` invoca el controlador de eventos, rellena la `Items` colección y actualiza `TaskCompletionSource`:

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

Al crear o editar datos, debe implementar el `ISoapService.SaveTodoItemAsync` método. Este método detecta si `TodoItem` es un elemento nuevo o actualizado y llama al método adecuado en el `todoService` objeto. Los `CreateTodoItemCompleted` controladores `EditTodoItemCompleted` de eventos y también deben implementarse `todoService` para que sepa cuándo ha recibido una respuesta del servicio asmx (estos se pueden combinar en un único controlador porque realizan la misma operación). En el ejemplo siguiente se muestran las implementaciones de interfaces y controladores de eventos, `TaskCompletionSource` así como el objeto que se usa para operar de forma asincrónica:

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

### <a name="delete-data"></a>Eliminar datos

La eliminación de datos requiere una implementación similar. Defina un `TaskCompletionSource`, implemente un controlador de eventos y `ISoapService.DeleteTodoItemAsync` el método:

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

## <a name="test-the-web-service"></a>Prueba del servicio Web

La prueba de dispositivos físicos o emulados con un servicio hospedado localmente requiere la configuración personalizada de IIS, las direcciones de extremo y las reglas de Firewall. Para obtener más información sobre cómo configurar el entorno para las pruebas, consulte [configurar el acceso remoto a IIS Express](wcf.md#configure-remote-access-to-iis-express). La única diferencia entre probar WCF y ASMX es el número de puerto de TodoService.

## <a name="related-links"></a>Vínculos relacionados

- [TodoASMX (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
