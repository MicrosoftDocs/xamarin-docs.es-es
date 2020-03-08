---
title: Navegación de aplicación empresarial
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers realiza la navegación del modelo de vista primero desde los modelos de vista.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f523c7149366cff85164f26f3f47b87801002cb
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916494"
---
# <a name="enterprise-app-navigation"></a>Navegación de aplicación empresarial

Xamarin. Forms incluye compatibilidad con la navegación por las páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario o desde la propia aplicación como consecuencia de los cambios internos en el estado controlado por lógica. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón Model-View-ViewModel (MVVM), ya que se deben cumplir los siguientes desafíos:

- Cómo identificar la vista a la que se va a navegar, utilizando un enfoque que no introduce el acoplamiento estrecho y las dependencias entre las vistas.
- Cómo coordinar el proceso por el que se crea una instancia de la vista a la que se va a navegar y se inicializa. Al usar MVVM, es necesario crear instancias del modelo de vista y vista y asociarlos entre sí a través del contexto de enlace de la vista. Cuando una aplicación usa un contenedor de inserción de dependencias, la creación de instancias de vistas y modelos de vista podría requerir un mecanismo de construcción específico.
- Indica si se va a realizar la navegación por la vista primero o la navegación del modelo primero. Con la navegación por la vista en primer lugar, la página a la que se va a desplazar hace referencia al nombre del tipo de vista. Durante la navegación, se crea una instancia de la vista especificada, junto con el modelo de vista correspondiente y otros servicios dependientes. Un enfoque alternativo es usar la navegación del modelo de vista primero, donde la página a la que se va a desplazar hace referencia al nombre del tipo de modelo de vista.
- Cómo separar sin problemas el comportamiento de navegación de la aplicación en las vistas y los modelos de vista. El patrón MVVM proporciona una separación entre la interfaz de usuario de la aplicación y su presentación y lógica de negocios. Sin embargo, el comportamiento de navegación de una aplicación suele abarcar las partes de la interfaz de usuario y las presentaciones de la aplicación. A menudo, el usuario iniciará la navegación desde una vista y la vista se reemplazará como resultado de la navegación. Sin embargo, a menudo es necesario iniciar la navegación o coordinarla desde el modelo de vista.
- Cómo pasar parámetros durante la navegación con fines de inicialización. Por ejemplo, si el usuario navega a una vista para actualizar los detalles del pedido, los datos del pedido tendrán que pasarse a la vista para que pueda mostrar los datos correctos.
- Cómo hacer la navegación de forma coordinada para asegurarse de que se respetan ciertas reglas de negocios. Por ejemplo, es posible que se pregunte a los usuarios antes de salir de una vista para que puedan corregir los datos no válidos o se les pida que envíen o descarten los cambios de datos que se realizaron en la vista.

En este capítulo se abordan estos desafíos mediante la presentación de una clase `NavigationService` que se usa para realizar la navegación por la primera página del modelo de vista.

> [!NOTE]
> La `NavigationService` que usa la aplicación está diseñada para realizar una navegación jerárquica entre instancias de ContentPage. El uso del servicio para navegar entre otros tipos de páginas podría producir un comportamiento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

La lógica de navegación puede residir en el código subyacente de una vista o en un modelo de vista enlazada a datos. Aunque colocar la lógica de navegación en una vista podría ser el enfoque más sencillo, no se puede probar fácilmente a través de pruebas unitarias. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas unitarias. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios. Por ejemplo, es posible que una aplicación no permita que el usuario navegue fuera de una página sin asegurarse primero de que los datos escritos sean válidos.

Normalmente, una clase `NavigationService` se invoca desde los modelos de vista para promover la capacidad de prueba. Sin embargo, si se navega a las vistas de los modelos de vista, sería necesario que los modelos de vista hagan referencia a vistas, especialmente las vistas con las que el modelo de vista activo no está asociado, lo que no se recomienda. Por lo tanto, el `NavigationService` presenta aquí especifica el tipo de modelo de vista como el destino al que navegar.

La aplicación móvil eShopOnContainers usa la clase `NavigationService` para proporcionar la navegación del modelo de vista primero. Esta clase implementa la interfaz `INavigationService`, que se muestra en el ejemplo de código siguiente:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Esta interfaz especifica que una clase de implementación debe proporcionar los métodos siguientes:

|Método|Propósito|
|--- |--- |
|`InitializeAsync`|Realiza la navegación a una de las dos páginas cuando se inicia la aplicación.|
|`NavigateToAsync`|Realiza una navegación jerárquica a una página especificada.|
|`NavigateToAsync(parameter)`|Realiza una navegación jerárquica a una página especificada, pasando un parámetro.|
|`RemoveLastFromBackStackAsync`|Quita la página anterior de la pila de navegación.|
|`RemoveBackStackAsync`|Quita todas las páginas anteriores de la pila de navegación.|

Además, la interfaz de `INavigationService` especifica que una clase de implementación debe proporcionar una propiedad `PreviousPageViewModel`. Esta propiedad devuelve el tipo de modelo de vista asociado a la página anterior en la pila de navegación.

> [!NOTE]
> Una interfaz `INavigationService` normalmente también especificaría un método `GoBackAsync`, que se usa para volver a la página anterior de la pila de navegación mediante programación. Sin embargo, este método falta en la aplicación móvil eShopOnContainers porque no es necesario.

### <a name="creating-the-navigationservice-instance"></a>Crear la instancia de NavigationService

La clase `NavigationService`, que implementa la interfaz de `INavigationService`, se registra como singleton con el contenedor de inserción de dependencias Autofac, tal y como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La interfaz de `INavigationService` se resuelve en el constructor de la clase `ViewModelBase`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Esto devuelve una referencia al `NavigationService` objeto almacenado en el contenedor de inserción de dependencias de Autofac, que se crea mediante el método `InitNavigation` de la clase `App`. Para obtener más información, consulte [navegar cuando se inicia la aplicación](#navigating_when_the_app_is_launched).

La clase `ViewModelBase` almacena la instancia de `NavigationService` en una propiedad `NavigationService` de tipo `INavigationService`. Por lo tanto, todas las clases de modelo de vista, que derivan de la clase `ViewModelBase`, pueden utilizar la propiedad `NavigationService` para tener acceso a los métodos especificados por la interfaz `INavigationService`. Esto evita la sobrecarga de insertar el objeto de `NavigationService` desde el contenedor de inyección de dependencia de Autofac en cada clase de modelo de vista.

### <a name="handling-navigation-requests"></a>Controlar las solicitudes de navegación

Xamarin. Forms proporciona la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , que implementa una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas, hacia delante y hacia atrás, según sea necesario. Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

En lugar de usar la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) directamente, la aplicación eShopOnContainers ajusta la clase `NavigationPage` en la clase `CustomNavigationView`, como se muestra en el ejemplo de código siguiente:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

El propósito de este ajuste es facilitar el estilo de la instancia de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) dentro del archivo XAML para la clase.

La navegación se realiza dentro de las clases del modelo de vista invocando uno de los métodos `NavigateToAsync`, especificando el tipo de modelo de vista para la página a la que se navega, como se muestra en el ejemplo de código siguiente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

En el ejemplo de código siguiente se muestran los métodos `NavigateToAsync` proporcionados por la clase `NavigationService`:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Cada método permite que cualquier clase de modelo de vista que deriva de la clase `ViewModelBase` realice una navegación jerárquica mediante la invocación del método `InternalNavigateToAsync`. Además, el segundo método `NavigateToAsync` permite especificar los datos de navegación como argumento que se pasa al modelo de vista al que se navega, donde normalmente se usa para realizar la inicialización. Para obtener más información, vea [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

El método `InternalNavigateToAsync` ejecuta la solicitud de navegación y se muestra en el ejemplo de código siguiente:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

El método `InternalNavigateToAsync` realiza la navegación a un modelo de vista llamando primero al método `CreatePage`. Este método busca la vista que corresponde al tipo de modelo de vista especificado y crea y devuelve una instancia de este tipo de vista. La búsqueda de la vista que corresponde al tipo de modelo de vista utiliza un enfoque basado en convenciones, que presupone que:

- Las vistas están en el mismo ensamblado que los tipos de modelo de vista.
- Las vistas se encuentran en un. Muestra el espacio de nombres secundario.
- Los modelos de vista se encuentran en un. Espacio de nombres secundario ViewModels.
- Los nombres de vista se corresponden con los nombres de modelo de vista, con "Model" quitado.

Cuando se crea una instancia de una vista, se asocia con su modelo de vista correspondiente. Para obtener más información sobre cómo se produce esto, vea [crear automáticamente un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Si la vista que se va a crear es una `LoginView`, se ajusta dentro de una nueva instancia de la clase `CustomNavigationView` y se asigna a la propiedad [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) . De lo contrario, se recupera la instancia de `CustomNavigationView` y, siempre que no sea NULL, se invoca el método [`PushAsync`](xref:Xamarin.Forms.NavigationPage) para insertar la vista que se va a crear en la pila de navegación. Sin embargo, si la instancia de `CustomNavigationView` recuperada es `null`, la vista que se está creando se ajusta dentro de una nueva instancia de la clase `CustomNavigationView` y se asigna a la propiedad `Application.Current.MainPage`. Este mecanismo garantiza que durante la navegación, las páginas se agregan correctamente a la pila de navegación cuando está vacía y cuando contienen datos.

> [!TIP]
> Considere la posibilidad de almacenar en caché las páginas. El almacenamiento en caché de páginas produce un consumo de memoria para las vistas que no se muestran actualmente. Sin embargo, sin el almacenamiento en caché de páginas, significa que el análisis y la construcción de XAML de la página y su modelo de vista se producirán cada vez que se navega a una nueva página, lo que puede afectar al rendimiento de una página compleja. En el caso de una página bien diseñada que no use un número excesivo de controles, el rendimiento debe ser suficiente. Sin embargo, el almacenamiento en caché de páginas podría ayudar si se producen tiempos de carga de página lentos.

Una vez creada la vista y navegada a, se ejecuta el método `InitializeAsync` del modelo de vista asociado de la vista. Para obtener más información, vea [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegar cuando se inicia la aplicación

Cuando se inicia la aplicación, se invoca el método `InitNavigation` de la clase `App`. El siguiente ejemplo de código muestra este método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

El método crea un nuevo objeto `NavigationService` en el contenedor de inserción de dependencias Autofac y devuelve una referencia a él antes de invocar su método `InitializeAsync`.

> [!NOTE]
> Cuando la clase `ViewModelBase` resuelve la interfaz `INavigationService`, el contenedor devuelve una referencia al objeto `NavigationService` que se creó cuando se invoca el método InitNavigation.

En el ejemplo de código siguiente se muestra el método de `InitializeAsync` `NavigationService`:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

Se navega a la `MainView` si la aplicación tiene un token de acceso almacenado en caché, que se usa para la autenticación. De lo contrario, se navega a la `LoginView`.

Para obtener más información sobre el contenedor de inserción de dependencias de Autofac, vea [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Pasar parámetros durante la navegación

Uno de los métodos de `NavigateToAsync`, que especifica la interfaz `INavigationService`, permite especificar los datos de navegación como un argumento que se pasa al modelo de vista al que se navega, donde normalmente se usa para realizar la inicialización.

Por ejemplo, la clase `ProfileViewModel` contiene un `OrderDetailCommand` que se ejecuta cuando el usuario selecciona un pedido en la página `ProfileView`. A su vez, ejecuta el método `OrderDetailAsync`, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca la navegación al `OrderDetailViewModel`, pasando una instancia `Order` que representa el orden que el usuario seleccionó en la página `ProfileView`. Cuando la clase `NavigationService` crea el `OrderDetailView`, se crea una instancia de la clase `OrderDetailViewModel` y se asigna a la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)de la vista. Después de desplazarse hasta el `OrderDetailView`, el método de `InternalNavigateToAsync` ejecuta el método `InitializeAsync` del modelo de vista asociado de la vista.

El método `InitializeAsync` se define en la clase `ViewModelBase` como un método que se puede invalidar. Este método especifica un argumento `object` que representa los datos que se van a pasar a un modelo de vista durante una operación de navegación. Por lo tanto, las clases de modelo de vista que desean recibir datos de una operación de navegación proporcionan su propia implementación del método `InitializeAsync` para realizar la inicialización requerida. En el ejemplo de código siguiente se muestra el método `InitializeAsync` de la clase `OrderDetailViewModel`:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Este método recupera la instancia de `Order` que se pasó al modelo de vista durante la operación de navegación y la utiliza para recuperar los detalles del pedido completo de la instancia de `OrderService`.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Invocar la navegación mediante comportamientos

La navegación normalmente se desencadena desde una vista por una interacción del usuario. Por ejemplo, el `LoginView` realiza la navegación después de la autenticación correcta. En el ejemplo de código siguiente se muestra cómo se invoca la navegación por un comportamiento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [`WebView`](xref:Xamarin.Forms.WebView). Cuando el `WebView` navega a una página web, se activará el evento [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) , que ejecutará el `NavigateCommand` en el `LoginViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten a medida que se pasan entre el origen y el destino mediante el convertidor especificado en la propiedad `EventArgsConverter`, que devuelve el [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) de la [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs). Por lo tanto, cuando se ejecuta el `NavigationCommand`, se pasa la dirección URL de la página web como un parámetro a la `Action`registrada.

A su vez, el `NavigationCommand` ejecuta el método `NavigateAsync`, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Este método invoca la navegación al `MainViewModel`y, después de la navegación, quita la página de `LoginView` de la pila de navegación.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar o cancelar la navegación

Una aplicación podría necesitar interactuar con el usuario durante una operación de navegación, de modo que el usuario pueda confirmar o cancelar la navegación. Esto podría ser necesario, por ejemplo, cuando el usuario intenta navegar antes de haber completado completamente una página de entrada de datos. En esta situación, una aplicación debe proporcionar una notificación que permita al usuario salir de la página o cancelar la operación de navegación antes de que se produzca. Esto se puede lograr en una clase de modelo de vista mediante la respuesta de una notificación para controlar si se invoca o no la navegación.

## <a name="summary"></a>Resumen

Xamarin. Forms incluye compatibilidad con la navegación de páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario o de la propia aplicación, como resultado de los cambios de estado de la lógica interna controlada. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón MVVM.

En este capítulo se ha presentado una `NavigationService` clase, que se usa para realizar la navegación del modelo de vista primero desde los modelos de vista. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas automatizadas. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
