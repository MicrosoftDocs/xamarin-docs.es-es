---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b2ee9fb02a8fd18d69e93424dc76bfd54fafc86
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130902"
---
# <a name="enterprise-app-navigation"></a>Navegación de aplicación empresarial

Xamarin.Formsincluye compatibilidad con la navegación de páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario o desde la propia aplicación como consecuencia de los cambios internos en el estado controlado por lógica. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón Model-View-ViewModel (MVVM), ya que se deben cumplir los siguientes desafíos:

- Cómo identificar la vista a la que se va a navegar, utilizando un enfoque que no introduce el acoplamiento estrecho y las dependencias entre las vistas.
- Cómo coordinar el proceso por el que se crea una instancia de la vista a la que se va a navegar y se inicializa. Al usar MVVM, es necesario crear instancias del modelo de vista y vista y asociarlos entre sí a través del contexto de enlace de la vista. Cuando una aplicación usa un contenedor de inserción de dependencias, la creación de instancias de vistas y modelos de vista podría requerir un mecanismo de construcción específico.
- Indica si se va a realizar la navegación por la vista primero o la navegación del modelo primero. Con la navegación por la vista en primer lugar, la página a la que se va a desplazar hace referencia al nombre del tipo de vista. Durante la navegación, se crea una instancia de la vista especificada, junto con el modelo de vista correspondiente y otros servicios dependientes. Un enfoque alternativo es usar la navegación del modelo de vista primero, donde la página a la que se va a desplazar hace referencia al nombre del tipo de modelo de vista.
- Cómo separar sin problemas el comportamiento de navegación de la aplicación en las vistas y los modelos de vista. El patrón MVVM proporciona una separación entre la interfaz de usuario de la aplicación y su presentación y lógica de negocios. Sin embargo, el comportamiento de navegación de una aplicación suele abarcar las partes de la interfaz de usuario y las presentaciones de la aplicación. A menudo, el usuario iniciará la navegación desde una vista y la vista se reemplazará como resultado de la navegación. Sin embargo, a menudo es necesario iniciar la navegación o coordinarla desde el modelo de vista.
- Cómo pasar parámetros durante la navegación con fines de inicialización. Por ejemplo, si el usuario navega a una vista para actualizar los detalles del pedido, los datos del pedido tendrán que pasarse a la vista para que pueda mostrar los datos correctos.
- Cómo hacer la navegación de forma coordinada para asegurarse de que se respetan ciertas reglas de negocios. Por ejemplo, es posible que se pregunte a los usuarios antes de salir de una vista para que puedan corregir los datos no válidos o se les pida que envíen o descarten los cambios de datos que se realizaron en la vista.

En este capítulo se abordan estos desafíos presentando una `NavigationService` clase que se usa para realizar la navegación por la primera página del modelo de vista.

> [!NOTE]
> El que `NavigationService` usa la aplicación está diseñado solo para realizar una navegación jerárquica entre instancias de ContentPage. El uso del servicio para navegar entre otros tipos de páginas podría producir un comportamiento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

La lógica de navegación puede residir en el código subyacente de una vista o en un modelo de vista enlazada a datos. Aunque colocar la lógica de navegación en una vista podría ser el enfoque más sencillo, no se puede probar fácilmente a través de pruebas unitarias. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas unitarias. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios. Por ejemplo, es posible que una aplicación no permita que el usuario navegue fuera de una página sin asegurarse primero de que los datos escritos sean válidos.

`NavigationService`Normalmente, una clase se invoca desde los modelos de vista para promover la capacidad de prueba. Sin embargo, si se navega a las vistas de los modelos de vista, sería necesario que los modelos de vista hagan referencia a vistas, especialmente las vistas con las que el modelo de vista activo no está asociado, lo que no se recomienda. Por lo tanto, el `NavigationService` presentado aquí especifica el tipo de modelo de vista como el destino al que navegar.

La aplicación móvil eShopOnContainers usa la `NavigationService` clase para proporcionar la navegación del modelo de vista primero. Esta clase implementa la `INavigationService` interfaz, que se muestra en el ejemplo de código siguiente:

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

|Método|Finalidad|
|--- |--- |
|`InitializeAsync`|Realiza la navegación a una de las dos páginas cuando se inicia la aplicación.|
|`NavigateToAsync`|Realiza una navegación jerárquica a una página especificada.|
|`NavigateToAsync(parameter)`|Realiza una navegación jerárquica a una página especificada, pasando un parámetro.|
|`RemoveLastFromBackStackAsync`|Quita la página anterior de la pila de navegación.|
|`RemoveBackStackAsync`|Quita todas las páginas anteriores de la pila de navegación.|

Además, la `INavigationService` interfaz especifica que una clase de implementación debe proporcionar una `PreviousPageViewModel` propiedad. Esta propiedad devuelve el tipo de modelo de vista asociado a la página anterior en la pila de navegación.

> [!NOTE]
> `INavigationService`Normalmente, una interfaz también especificaría un `GoBackAsync` método, que se usa para volver a la página anterior de la pila de navegación mediante programación. Sin embargo, este método falta en la aplicación móvil eShopOnContainers porque no es necesario.

### <a name="creating-the-navigationservice-instance"></a>Crear la instancia de NavigationService

La `NavigationService` clase, que implementa la `INavigationService` interfaz, se registra como singleton con el contenedor de inserción de dependencias Autofac, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La `INavigationService` interfaz se resuelve en el `ViewModelBase` constructor de clase, tal y como se muestra en el ejemplo de código siguiente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Esto devuelve una referencia al `NavigationService` objeto almacenado en el contenedor de inserción de dependencias de Autofac, que se crea mediante el `InitNavigation` método en la `App` clase. Para obtener más información, consulte [navegar cuando se inicia la aplicación](#navigating_when_the_app_is_launched).

La `ViewModelBase` clase almacena la `NavigationService` instancia en una `NavigationService` propiedad, de tipo `INavigationService` . Por lo tanto, todas las clases de modelo de vista, que derivan de la `ViewModelBase` clase, pueden utilizar la `NavigationService` propiedad para tener acceso a los métodos especificados por la `INavigationService` interfaz. Esto evita la sobrecarga de insertar el `NavigationService` objeto del contenedor de inyección de dependencia de Autofac en cada clase de modelo de vista.

### <a name="handling-navigation-requests"></a>Controlar las solicitudes de navegación

Xamarin.Formsproporciona la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) clase, que implementa una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas, hacia delante y hacia atrás, según desee. Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

En lugar de usar [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) directamente la clase, la aplicación eShopOnContainers encapsula la `NavigationPage` clase en la `CustomNavigationView` clase, como se muestra en el ejemplo de código siguiente:

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

El propósito de este ajuste es facilitar el estilo de la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instancia dentro del archivo XAML para la clase.

La navegación se realiza dentro de las clases del modelo de vista invocando uno de los `NavigateToAsync` métodos, especificando el tipo de modelo de vista para la página a la que se navega, tal y como se muestra en el ejemplo de código siguiente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

En el ejemplo de código siguiente se muestran los `NavigateToAsync` métodos proporcionados por la `NavigationService` clase:

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

Cada método permite que cualquier clase de modelo de vista que se derive de la `ViewModelBase` clase realice navegación jerárquica mediante la invocación del `InternalNavigateToAsync` método. Además, el segundo `NavigateToAsync` método permite especificar los datos de navegación como argumento que se pasa al modelo de vista al que se navega, donde normalmente se usa para realizar la inicialización. Para obtener más información, vea [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

El `InternalNavigateToAsync` método ejecuta la solicitud de navegación y se muestra en el ejemplo de código siguiente:

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

El `InternalNavigateToAsync` método realiza la navegación a un modelo de vista llamando primero al `CreatePage` método. Este método busca la vista que corresponde al tipo de modelo de vista especificado y crea y devuelve una instancia de este tipo de vista. La búsqueda de la vista que corresponde al tipo de modelo de vista utiliza un enfoque basado en convenciones, que presupone que:

- Las vistas están en el mismo ensamblado que los tipos de modelo de vista.
- Las vistas se encuentran en un. Muestra el espacio de nombres secundario.
- Los modelos de vista se encuentran en un. Espacio de nombres secundario ViewModels.
- Los nombres de vista se corresponden con los nombres de modelo de vista, con "Model" quitado.

Cuando se crea una instancia de una vista, se asocia con su modelo de vista correspondiente. Para obtener más información sobre cómo se produce esto, vea [crear automáticamente un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Si la vista que se va a crear es una `LoginView` , se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y se asigna a la [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) propiedad. De lo contrario, `CustomNavigationView` se recupera la instancia y siempre que no sea NULL, [`PushAsync`](xref:Xamarin.Forms.NavigationPage) se invoca el método para insertar la vista que se va a crear en la pila de navegación. Sin embargo, si la `CustomNavigationView` instancia recuperada es `null` , la vista que se está creando se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y se asigna a la `Application.Current.MainPage` propiedad. Este mecanismo garantiza que durante la navegación, las páginas se agregan correctamente a la pila de navegación cuando está vacía y cuando contienen datos.

> [!TIP]
> Considere la posibilidad de almacenar en caché las páginas. El almacenamiento en caché de páginas produce un consumo de memoria para las vistas que no se muestran actualmente. Sin embargo, sin el almacenamiento en caché de páginas, significa que el análisis y la construcción de XAML de la página y su modelo de vista se producirán cada vez que se navega a una nueva página, lo que puede afectar al rendimiento de una página compleja. En el caso de una página bien diseñada que no use un número excesivo de controles, el rendimiento debe ser suficiente. Sin embargo, el almacenamiento en caché de páginas podría ayudar si se producen tiempos de carga de página lentos.

Una vez creada la vista y navegada a, `InitializeAsync` se ejecuta el método del modelo de vista asociado de la vista. Para obtener más información, vea [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegar cuando se inicia la aplicación

Cuando se inicia la aplicación, `InitNavigation` se invoca el método en la `App` clase. El siguiente ejemplo de código muestra este método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

El método crea un nuevo `NavigationService` objeto en el contenedor de inserción de dependencias de Autofac y devuelve una referencia a él antes de invocar su `InitializeAsync` método.

> [!NOTE]
> Cuando la `INavigationService` clase resuelve la interfaz `ViewModelBase` , el contenedor devuelve una referencia al `NavigationService` objeto que se creó cuando se invoca el método InitNavigation.

El siguiente ejemplo de código muestra el método `NavigationService` `InitializeAsync`:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`Se navega a si la aplicación tiene un token de acceso almacenado en caché, que se usa para la autenticación. De lo contrario, `LoginView` se navega a.

Para obtener más información sobre el contenedor de inserción de dependencias de Autofac, vea [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Pasar parámetros durante la navegación

Uno de los `NavigateToAsync` métodos, especificado por la `INavigationService` interfaz, permite especificar los datos de navegación como un argumento que se pasa al modelo de vista al que se navega, donde normalmente se usa para realizar la inicialización.

Por ejemplo, la `ProfileViewModel` clase contiene un `OrderDetailCommand` que se ejecuta cuando el usuario selecciona un pedido en la `ProfileView` página. A su vez, esto ejecuta el `OrderDetailAsync` método, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca la navegación a `OrderDetailViewModel` , pasando una `Order` instancia de que representa el orden que el usuario seleccionó en la `ProfileView` página. Cuando la `NavigationService` clase crea el `OrderDetailView` , `OrderDetailViewModel` se crea una instancia de la clase y se asigna a la de la vista [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Después de navegar a `OrderDetailView` , el `InternalNavigateToAsync` método ejecuta el `InitializeAsync` método del modelo de vista asociado de la vista.

El `InitializeAsync` método se define en la `ViewModelBase` clase como un método que se puede invalidar. Este método especifica un `object` argumento que representa los datos que se van a pasar a un modelo de vista durante una operación de navegación. Por lo tanto, las clases de modelo de vista que desean recibir datos de una operación de navegación proporcionan su propia implementación del `InitializeAsync` método para realizar la inicialización requerida. En el ejemplo de código siguiente se muestra el `InitializeAsync` método de la `OrderDetailViewModel` clase:

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

Este método recupera la `Order` instancia que se pasó al modelo de vista durante la operación de navegación y la utiliza para recuperar los detalles de pedido completos de la `OrderService` instancia.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Invocar la navegación mediante comportamientos

La navegación normalmente se desencadena desde una vista por una interacción del usuario. Por ejemplo, `LoginView` realiza la navegación después de la autenticación correcta. En el ejemplo de código siguiente se muestra cómo se invoca la navegación por un comportamiento:

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

En tiempo de ejecución, `EventToCommandBehavior` responderá a la interacción con [`WebView`](xref:Xamarin.Forms.WebView) . Cuando `WebView` navega a una página web, [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) se activa el evento, que ejecutará `NavigateCommand` en `LoginViewModel` . De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten a medida que se pasan entre el origen y el destino mediante el convertidor especificado en la `EventArgsConverter` propiedad, que devuelve [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) desde [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) . Por lo tanto, cuando `NavigationCommand` se ejecuta, se pasa la dirección URL de la página web como un parámetro a la registrada `Action` .

A su vez, `NavigationCommand` ejecuta el `NavigateAsync` método, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Este método invoca la navegación a `MainViewModel` y, después de la navegación, quita la `LoginView` Página de la pila de navegación.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar o cancelar la navegación

Una aplicación podría necesitar interactuar con el usuario durante una operación de navegación, de modo que el usuario pueda confirmar o cancelar la navegación. Esto podría ser necesario, por ejemplo, cuando el usuario intenta navegar antes de haber completado completamente una página de entrada de datos. En esta situación, una aplicación debe proporcionar una notificación que permita al usuario salir de la página o cancelar la operación de navegación antes de que se produzca. Esto se puede lograr en una clase de modelo de vista mediante la respuesta de una notificación para controlar si se invoca o no la navegación.

## <a name="summary"></a>Resumen

Xamarin.Formsincluye compatibilidad para la navegación de páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario, o de la propia aplicación, como resultado de los cambios internos en el estado controlado por lógica. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón MVVM.

En este capítulo se presenta una `NavigationService` clase, que se usa para realizar la navegación del modelo de vista primero desde los modelos de vista. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas automatizadas. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
