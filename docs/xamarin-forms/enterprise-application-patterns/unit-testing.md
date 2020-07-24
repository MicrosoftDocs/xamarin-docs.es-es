---
title: Pruebas unitarias de aplicaciones empresariales
description: En este capítulo se explica cómo se realizan las pruebas unitarias en la aplicación móvil eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f30b8be762e23f84a90595a56912587cc3aa838
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934256"
---
# <a name="unit-testing-enterprise-apps"></a>Pruebas unitarias de aplicaciones empresariales

Las aplicaciones móviles tienen problemas únicos que no tienen que preocuparse por el escritorio y las aplicaciones basadas en Web. Los usuarios móviles variarán en función de los dispositivos que usan, la conectividad de red, la disponibilidad de los servicios y otros factores. Por lo tanto, las aplicaciones móviles deben probarse ya que se usarán en el mundo real para mejorar su calidad, confiabilidad y rendimiento. Hay muchos tipos de pruebas que deben realizarse en una aplicación, incluidas las pruebas unitarias, las pruebas de integración y las pruebas de interfaz de usuario, con pruebas unitarias que son la forma más común de realizar pruebas.

Una prueba unitaria toma una unidad pequeña de la aplicación, normalmente un método, la aísla del resto del código y comprueba que se comporta de la manera esperada. Su objetivo es comprobar que cada unidad de funcionalidad funciona según lo previsto, de modo que los errores no se propaguen a lo largo de la aplicación. Detectar un error en el que se produce es más eficaz que observar el efecto de un error indirectamente en un punto secundario de error.

Las pruebas unitarias tienen el mayor efecto en la calidad del código cuando es una parte integral del flujo de trabajo de desarrollo de software. En cuanto se escribe un método, se deben escribir pruebas unitarias que comprueben el comportamiento del método en respuesta a casos estándar, límite e incorrectos de datos de entrada, y que comprueben cualquier suposición explícita o implícita realizada por el código. Como alternativa, con el desarrollo controlado por pruebas, las pruebas unitarias se escriben antes del código. En este escenario, las pruebas unitarias actúan como documentación de diseño y como especificaciones funcionales.

> [!NOTE]
> Las pruebas unitarias son muy eficaces contra la regresión, es decir, las funciones que solían funcionar pero que se han alterado por una actualización defectuosa.

Normalmente, las pruebas unitarias usan el patrón Arrange-Act-Assert:

- La sección *Arrange* del método de prueba unitaria inicializa objetos y establece el valor de los datos que se pasan al método en pruebas.
- La sección *Act* invoca al método en pruebas con los argumentos necesarios.
- La sección *Assert* comprueba que la acción del método en pruebas se comporta según lo esperado.

El siguiente patrón garantiza que las pruebas unitarias son legibles y coherentes.

## <a name="dependency-injection-and-unit-testing"></a>Inserción de dependencias y pruebas unitarias

Una de las motivaciones para adoptar una arquitectura de acoplamiento flexible es que facilita las pruebas unitarias. Uno de los tipos registrados con Autofac es la `OrderService` clase. En el ejemplo de código siguiente se muestra un esquema de esta clase:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

La `OrderDetailViewModel` clase tiene una dependencia en el `IOrderService` tipo que el contenedor resuelve cuando crea una instancia de un `OrderDetailViewModel` objeto. Sin embargo, en lugar de crear un `OrderService` objeto para la prueba unitaria de la `OrderDetailViewModel` clase, en su lugar, reemplace el `OrderService` objeto por un simulacro para el propósito de las pruebas. En la figura 10-1 se muestra esta relación.

![Clases que implementan la interfaz IOrderService](unit-testing-images/unittesting.png)

**Figura 10-1:** Clases que implementan la interfaz IOrderService

Este enfoque permite `OrderService` pasar el objeto a la clase en tiempo de `OrderDetailViewModel` ejecución y, en aras de la capacidad de prueba, permite `OrderMockService` pasar la clase a la `OrderDetailViewModel` clase en el momento de la prueba. La principal ventaja de este enfoque es que permite que las pruebas unitarias se ejecuten sin necesidad de recursos difíciles de manejar, como los servicios web o las bases de datos.

## <a name="testing-mvvm-applications"></a>Prueba de aplicaciones MVVM

Probar modelos y ver modelos de aplicaciones MVVM es idéntico a probar cualquier otra clase y se pueden usar las mismas herramientas y técnicas, como pruebas unitarias y simulacros. Sin embargo, hay algunos patrones que son típicos para modelar y ver las clases de modelo, que pueden beneficiarse de las técnicas de prueba unitaria específicas.

> [!TIP]
> Pruebe una cosa con cada prueba unitaria. No esté tentado de hacer que una prueba unitaria ejerza más de un aspecto del comportamiento de la unidad. Esto conduce a las pruebas que son difíciles de leer y actualizar. También puede generar confusión al interpretar un error.

La aplicación móvil eShopOnContainers usa [xUnit](https://xunit.github.io/) para realizar pruebas unitarias, que admite dos tipos diferentes de pruebas unitarias:

- Los hechos son pruebas que siempre son verdaderas, que prueban las condiciones invariables.
- Las teorías son pruebas que solo son verdaderas para un conjunto determinado de datos.

Las pruebas unitarias que se incluyen con la aplicación móvil eShopOnContainers son pruebas de hechos y, por lo tanto, cada método de prueba unitaria se decora con el `[Fact]` atributo.

> [!NOTE]
> las pruebas xUnit se ejecutan en un ejecutor de pruebas. Para ejecutar el ejecutor de pruebas, ejecute el proyecto eShopOnContainers. TestRunner para la plataforma necesaria.

### <a name="testing-asynchronous-functionality"></a>Probar la funcionalidad asincrónica

Al implementar el patrón MVVM, los modelos de vista normalmente invocan operaciones en los servicios, a menudo de forma asincrónica. Las pruebas de código que invoca estas operaciones suelen usar simulacros como reemplazos para los servicios reales. En el ejemplo de código siguiente se muestra cómo probar la funcionalidad asincrónica pasando un servicio ficticio a un modelo de vista:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Esta prueba unitaria comprueba que la `Order` propiedad de la `OrderDetailViewModel` instancia tendrá un valor después de que se haya `InitializeAsync` invocado el método. El `InitializeAsync` método se invoca cuando se navega a la vista correspondiente del modelo de vista. Para obtener más información sobre la navegación, consulte [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Cuando `OrderDetailViewModel` se crea la instancia, espera que `OrderService` se especifique una instancia como argumento. Sin embargo, `OrderService` recupera datos de un servicio Web. Por consiguiente, una `OrderMockService` instancia de, que es una versión ficticia de la `OrderService` clase, se especifica como argumento para el `OrderDetailViewModel` constructor. A continuación, cuando se invoca el método del modelo de vista `InitializeAsync` , que invoca `IOrderService` las operaciones, se recuperan los datos ficticios en lugar de comunicarse con un servicio Web.

### <a name="testing-inotifypropertychanged-implementations"></a>Probar implementaciones de INotifyPropertyChanged

La implementación de la `INotifyPropertyChanged` interfaz permite a las vistas reaccionar a los cambios que se originan en modelos de vista y modelos. Estos cambios no se limitan a los datos que se muestran en los controles; también se usan para controlar la vista, como los Estados del modelo de vista que hacen que las animaciones se inicien o se deshabiliten los controles.

Las propiedades que se pueden actualizar directamente mediante la prueba unitaria se pueden probar asociando un controlador de eventos al `PropertyChanged` evento y comprobando si el evento se genera después de establecer un nuevo valor para la propiedad. En el ejemplo de código siguiente se muestra este tipo de prueba:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Esta prueba unitaria invoca el `InitializeAsync` método de la `OrderViewModel` clase, que hace que `Order` se actualice su propiedad. Se superará la prueba unitaria, siempre que `PropertyChanged` se produzca el evento para la `Order` propiedad.

### <a name="testing-message-based-communication"></a>Prueba de la comunicación basada en mensajes

Los modelos de vista que utilizan la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) clase para la comunicación entre las clases de acoplamiento flexible pueden ser pruebas unitarias suscritos al mensaje enviado por el código sometido a prueba, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Esta prueba unitaria comprueba que el `CatalogViewModel` publica el `AddProduct` mensaje en respuesta a su `AddCatalogItemCommand` ejecución. Dado que la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) clase admite las suscripciones de mensajes de multidifusión, la prueba unitaria se puede suscribir al `AddProduct` mensaje y ejecutar un delegado de devolución de llamada en respuesta a recibirlo. Este delegado de devolución de llamada, especificado como una expresión lambda, establece un `boolean` campo que se usa en la `Assert` instrucción para comprobar el comportamiento de la prueba.

### <a name="testing-exception-handling"></a>Probar el control de excepciones

También se pueden escribir pruebas unitarias que comprueben que se producen excepciones específicas para acciones o entradas no válidas, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Esta prueba unitaria producirá una excepción, porque el [`ListView`](xref:Xamarin.Forms.ListView) control no tiene un evento denominado `OnItemTapped` . El `Assert.Throws<T>` método es un método genérico, donde `T` es el tipo de la excepción esperada. El argumento pasado al `Assert.Throws<T>` método es una expresión lambda que producirá la excepción. Por lo tanto, la prueba unitaria se superará siempre que la expresión lambda produzca una excepción `ArgumentException` .

> [!TIP]
> Evite escribir pruebas unitarias que examinen cadenas de mensajes de excepción. Las cadenas de mensajes de excepción pueden cambiar con el tiempo, por lo que las pruebas unitarias que dependen de su presencia se consideran frágiles.

### <a name="testing-validation"></a>Probar la validación

Hay dos aspectos para probar la implementación de la validación: probar que las reglas de validación se implementan correctamente y probar que la `ValidatableObject<T>` clase funciona como se esperaba.

Normalmente, la lógica de validación es sencilla de probar, ya que normalmente es un proceso independiente en el que la salida depende de la entrada. Debe haber pruebas sobre los resultados de invocar el `Validate` método en cada propiedad que tiene al menos una regla de validación asociada, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Esta prueba unitaria comprueba que la validación se realiza correctamente cuando las dos `ValidatableObject<T>` propiedades de la `MockViewModel` instancia tienen datos.

Además de comprobar que la validación se realiza correctamente, las pruebas unitarias de validación también deben comprobar los valores de las `Value` `IsValid` propiedades, y `Errors` de cada `ValidatableObject<T>` instancia, para comprobar que la clase funciona según lo esperado. En el ejemplo de código siguiente se muestra una prueba unitaria que hace lo siguiente:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Esta prueba unitaria comprueba que se produce un error de validación cuando la `Surname` propiedad de `MockViewModel` no tiene ningún dato y las `Value` `IsValid` propiedades, y `Errors` de cada `ValidatableObject<T>` instancia están establecidas correctamente.

## <a name="summary"></a>Resumen

Una prueba unitaria toma una unidad pequeña de la aplicación, normalmente un método, la aísla del resto del código y comprueba que se comporta de la manera esperada. Su objetivo es comprobar que cada unidad de funcionalidad funciona según lo previsto, de modo que los errores no se propaguen a lo largo de la aplicación.

El comportamiento de un objeto en pruebas se puede aislar reemplazando objetos dependientes con objetos ficticios que simulan el comportamiento de los objetos dependientes. Esto permite que las pruebas unitarias se ejecuten sin necesidad de recursos difíciles de manejar, como servicios Web, o bases de datos.

Probar modelos y ver modelos de aplicaciones MVVM es idéntico a probar cualquier otra clase y se pueden usar las mismas herramientas y técnicas.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
