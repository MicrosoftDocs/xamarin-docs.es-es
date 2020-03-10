---
title: Patrón Model-View-ViewModel
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers usa el patrón MVVM para separar sin problemas la lógica empresarial y de presentación de la aplicación desde su interfaz de usuario.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6c9b74c9abc1a2c493c31699b52969a7d129429
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915323"
---
# <a name="the-model-view-viewmodel-pattern"></a>Patrón Model-View-ViewModel

La experiencia del desarrollador de Xamarin. Forms normalmente implica la creación de una interfaz de usuario en XAML y, a continuación, la adición de código subyacente que funciona en la interfaz de usuario. A medida que se modifican las aplicaciones y aumentan el tamaño y el ámbito, pueden surgir problemas de mantenimiento complejos. Estos problemas incluyen el acoplamiento estrecho entre los controles de interfaz de usuario y la lógica de negocios, lo que aumenta el costo de realizar modificaciones en la interfaz de usuario y la dificultad de las pruebas unitarias de este tipo de código.

El patrón Modelo-Vista-Modelo de vista (MVVM) ayuda a separar la lógica de negocios y presentación de una aplicación de su interfaz de usuario. Mantener una separación clara entre la lógica de aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que probar una aplicación, mantenerla y desarrollarla sea más fácil. También puede mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario colaborar con mayor facilidad al desarrollar sus respectivos elementos de una aplicación.

## <a name="the-mvvm-pattern"></a>Patrón MVVM

Hay tres componentes principales en el patrón MVVM: el modelo, la vista y el modelo de vista. Cada una de ellas sirve para un propósito distinto. En la figura 2-1 se muestran las relaciones entre los tres componentes.

![](mvvm-images/mvvm.png "The MVVM pattern")

**Figura 2-1**: patrón MVVM

Además de comprender las responsabilidades de cada componente, también es importante entender cómo interactúan entre sí. En un nivel alto, la vista "conoce" el modelo de vista y el modelo de vista "conoce" el modelo, pero el modelo no es consciente del modelo de vista y el modelo de vista no es consciente de la vista. Por lo tanto, el modelo de vista aísla la vista del modelo y permite que el modelo evolucione independientemente de la vista.

Las ventajas de usar el patrón MVVM son las siguientes:

- Si hay una implementación de modelo existente que encapsula la lógica de negocios existente, puede ser difícil o arriesgada cambiarla. En este escenario, el modelo de vista actúa como un adaptador para las clases de modelo y le permite evitar realizar cambios importantes en el código del modelo.
- Los desarrolladores pueden crear pruebas unitarias para el modelo de vista y el modelo, sin usar la vista. Las pruebas unitarias para el modelo de vista pueden ejercitar exactamente la misma funcionalidad que la vista.
- La interfaz de usuario de la aplicación se puede rediseñar sin tocar el código, siempre que la vista se implemente completamente en XAML. Por lo tanto, una nueva versión de la vista debe funcionar con el modelo de vista existente.
- Los diseñadores y desarrolladores pueden trabajar de forma independiente y simultánea en sus componentes durante el proceso de desarrollo. Los diseñadores pueden centrarse en la vista, mientras que los desarrolladores pueden trabajar en el modelo de vista y los componentes del modelo.

La clave para usar MVVM de forma eficaz radica en entender cómo factorizar el código de la aplicación en las clases correctas y en comprender cómo interactúan las clases. En las secciones siguientes se describen las responsabilidades de cada una de las clases del patrón MVVM.

### <a name="view"></a>Ver

La vista es responsable de definir la estructura, el diseño y la apariencia de lo que el usuario ve en la pantalla. Idealmente, cada vista se define en XAML, con un código subyacente limitado que no contiene la lógica de negocios. Sin embargo, en algunos casos, el código subyacente podría contener lógica de la interfaz de usuario que implementa el comportamiento visual que es difícil de expresar en XAML, como animaciones.

En una aplicación de Xamarin. Forms, una vista normalmente es una clase derivada de [`Page`](xref:Xamarin.Forms.Page)o derivada de [`ContentView`](xref:Xamarin.Forms.ContentView). Sin embargo, las vistas también se pueden representar mediante una plantilla de datos, que especifica los elementos de la interfaz de usuario que se van a usar para representar visualmente un objeto cuando se muestra. Una plantilla de datos como una vista no tiene código subyacente y está diseñada para enlazarse a un tipo de modelo de vista específico.

> [!TIP]
> Evite habilitar y deshabilitar elementos de la interfaz de usuario en el código subyacente. Asegúrese de que los modelos de vista sean responsables de definir cambios de estado lógicos que afecten a algunos aspectos de la presentación de la vista, por ejemplo, si un comando está disponible o una indicación de que hay una operación pendiente. Por consiguiente, habilite y deshabilite los elementos de la interfaz de usuario enlazando a las propiedades del modelo de vista, en lugar de habilitarlos y deshabilitarlos en el código subyacente.

Hay varias opciones para ejecutar código en el modelo de vista en respuesta a las interacciones de la vista, como un clic de botón o una selección de elementos. Si un control admite comandos, la propiedad `Command` del control se puede enlazar a datos a una propiedad `ICommand` en el modelo de vista. Cuando se invoca el comando del control, se ejecutará el código del modelo de vista. Además de los comandos, los comportamientos se pueden adjuntar a un objeto en la vista y pueden escuchar un comando que se va a invocar o que se va a generar el evento. En respuesta, el comportamiento puede invocar un `ICommand` en el modelo de vista o un método en el modelo de vista.

### <a name="viewmodel"></a>Modelo

El modelo de vista implementa las propiedades y los comandos a los que la vista puede enlazarse y notifica a la vista de cualquier cambio de estado a través de los eventos de notificación de cambios. Las propiedades y los comandos que proporciona el modelo de vista definen la funcionalidad que ofrece la interfaz de usuario, pero la vista determina cómo se mostrará esa funcionalidad.

> [!TIP]
> Mantenga la interfaz de usuario con capacidad de respuesta con operaciones asincrónicas. Las aplicaciones móviles deben tener el subproceso de interfaz de usuario desbloqueado para mejorar la percepción del rendimiento del usuario. Por lo tanto, en el modelo de vista, use métodos asincrónicos para las operaciones de e/s y genere eventos para notificar de forma asincrónica a las vistas de los cambios de propiedad.

El modelo de vista también es responsable de coordinar las interacciones de la vista con cualquier clase de modelo que sea necesaria. Normalmente, hay una relación de uno a varios entre el modelo de vista y las clases de modelo. El modelo de vista podría optar por exponer las clases de modelo directamente en la vista para que los controles de la vista puedan enlazarse directamente a ellas. En este caso, las clases de modelo deberán diseñarse para admitir el enlace de datos y los eventos de notificación de cambios.

Cada modelo de vista proporciona datos de un modelo en un formulario que la vista puede consumir fácilmente. Para ello, el modelo de vista realiza a veces la conversión de datos. Colocar esta conversión de datos en el modelo de vista es una buena idea porque proporciona las propiedades a las que se puede enlazar la vista. Por ejemplo, el modelo de vista podría combinar los valores de dos propiedades para que sea más fácil de mostrar en la vista.

> [!TIP]
> Centralice las conversiones de datos en una capa de conversión. También es posible usar convertidores como una capa de conversión de datos independiente que se encuentra entre el modelo de vista y la vista. Esto puede ser necesario, por ejemplo, cuando los datos requieren un formato especial que no proporciona el modelo de vista.

Para que el modelo de vista participe en el enlace de datos bidireccional con la vista, sus propiedades deben generar el evento `PropertyChanged`. Los modelos de vista satisfacen este requisito implementando la interfaz `INotifyPropertyChanged` y generando el evento `PropertyChanged` cuando se cambia una propiedad.

En el caso de las colecciones, se proporciona el `ObservableCollection<T>` compatible con las vistas. Esta colección implementa la notificación de cambios en la colección, lo que evita que el desarrollador tenga que implementar la interfaz `INotifyCollectionChanged` en las colecciones.

### <a name="model"></a>Modelo

Las clases de modelo son clases no visuales que encapsulan los datos de la aplicación. Por lo tanto, se puede considerar que el modelo representa el modelo de dominio de la aplicación, que normalmente incluye un modelo de datos junto con la lógica de validación y negocios. Los ejemplos de objetos de modelo incluyen objetos de transferencia de datos (DTO), objetos CLR antiguos sin formato (POCO) y objetos de entidad y proxy generados.

Las clases de modelo se usan normalmente junto con servicios o repositorios que encapsulan el acceso a datos y el almacenamiento en caché.

## <a name="connecting-view-models-to-views"></a>Conectar modelos de vista a vistas

Los modelos de vista se pueden conectar a las vistas mediante las funciones de enlace de datos de Xamarin. Forms. Existen muchos enfoques que se pueden usar para construir vistas y ver modelos y asociarlos en tiempo de ejecución. Estos enfoques se dividen en dos categorías, lo que se conoce como ver primera composición y ver primera composición del modelo. Elegir entre la primera composición de vista y la primera composición de modelo es un problema de preferencia y complejidad. Sin embargo, todos los enfoques comparten el mismo objetivo, que es para que la vista tenga un modelo de vista asignado a su propiedad BindingContext.

Con la primera composición de vista, la aplicación se compone conceptualmente de vistas que se conectan a los modelos de vista de los que dependen. La principal ventaja de este enfoque es que facilita la creación de aplicaciones comprobables de acoplamiento flexible, ya que los modelos de vista no dependen de las propias vistas. También es fácil comprender la estructura de la aplicación siguiendo su estructura visual, en lugar de tener que realizar un seguimiento de la ejecución del código para comprender cómo se crean y asocian las clases. Además, la primera construcción se alinea con el sistema de navegación de Xamarin. Forms, que es responsable de la creación de páginas cuando se produce la navegación, lo que hace que una vista del modelo de la primera composición sea compleja y esté mal alineada con la plataforma.

Con la primera composición del modelo de vista, la aplicación se compone conceptualmente de modelos de vista, con un servicio responsable de localizar la vista de un modelo de vista. La primera composición del modelo de la vista es más natural para algunos desarrolladores, ya que la creación de la vista se puede abstraer, lo que les permite centrarse en la estructura lógica de la interfaz de usuario que no es de la aplicación. Además, permite que otros modelos de vista puedan crear modelos de vista. Sin embargo, este enfoque suele ser complejo y puede resultar difícil entender cómo se crean y asocian las distintas partes de la aplicación.

> [!TIP]
> Mantenga los modelos de vista y las vistas de manera independiente. El enlace de vistas a una propiedad en un origen de datos debe ser la dependencia principal de la vista en su modelo de vista correspondiente. En concreto, no haga referencia a los tipos de vista, como [`Button`](xref:Xamarin.Forms.Button) y [`ListView`](xref:Xamarin.Forms.ListView), desde los modelos de vista. Siguiendo los principios descritos aquí, los modelos de vista se pueden probar de forma aislada, lo que reduce la probabilidad de que se presenten defectos de software mediante la limitación del ámbito.

En las secciones siguientes se describen los enfoques principales para conectar los modelos de vista a las vistas.

### <a name="creating-a-view-model-declaratively"></a>Crear un modelo de vista mediante declaración

El enfoque más sencillo consiste en que la vista cree mediante declaración instancias de su modelo de vista correspondiente en XAML. Cuando se construya la vista, también se construirá el objeto de modelo de vista correspondiente. Este método se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Cuando se crea el [`ContentPage`](xref:Xamarin.Forms.ContentPage) , se crea automáticamente una instancia del `LoginViewModel` y se establece como [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)de la vista.

Esta construcción declarativa y la asignación del modelo de vista por la vista tiene la ventaja de que es simple, pero tiene la desventaja de que requiere un constructor predeterminado (sin parámetros) en el modelo de vista.

### <a name="creating-a-view-model-programmatically"></a>Crear un modelo de vista mediante programación

Una vista puede tener código en el archivo de código subyacente, lo que hace que el modelo de vista se asigne a su [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad. Esto se suele realizar en el constructor de la vista, como se muestra en el ejemplo de código siguiente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La construcción y asignación mediante programación del modelo de vista en el código subyacente de la vista tiene la ventaja de que es simple. Sin embargo, el principal inconveniente de este enfoque es que la vista debe proporcionar el modelo de vista con las dependencias necesarias. El uso de un contenedor de inserción de dependencias puede ayudar a mantener el acoplamiento flexible entre el modelo de vista y vista. Para obtener más información, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Crear una vista definida como una plantilla de datos

Una vista se puede definir como una plantilla de datos y asociarse a un tipo de modelo de vista. Las plantillas de datos se pueden definir como recursos o se pueden definir en línea dentro del control que mostrará el modelo de vista. El contenido del control es la instancia del modelo de vista y la plantilla de datos se usa para representarla visualmente. Esta técnica es un ejemplo de una situación en la que se crea una instancia del modelo de vista en primer lugar, seguida de la creación de la vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Crear automáticamente un modelo de vista con un localizador de modelo de vista

Un localizador de modelo de vista es una clase personalizada que administra la creación de instancias de los modelos de vista y su asociación a las vistas. En la aplicación móvil eShopOnContainers, la clase `ViewModelLocator` tiene una propiedad adjunta, `AutoWireViewModel`, que se usa para asociar los modelos de vista con las vistas. En el XAML de la vista, esta propiedad adjunta está establecida en true para indicar que el modelo de vista debe conectarse automáticamente a la vista, tal y como se muestra en el ejemplo de código siguiente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

La propiedad `AutoWireViewModel` es una propiedad enlazable que se inicializa en false y, cuando su valor cambia, se llama al controlador de eventos `OnAutoWireViewModelChanged`. Este método resuelve el modelo de vista de la vista. En el ejemplo de código siguiente se muestra cómo se consigue esto:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

El método `OnAutoWireViewModelChanged` intenta resolver el modelo de vista mediante un enfoque basado en convenciones. Esta Convención presupone que:

- Los modelos de vista están en el mismo ensamblado que los tipos de vista.
- Las vistas se encuentran en un. Muestra el espacio de nombres secundario.
- Los modelos de vista se encuentran en un. Espacio de nombres secundario ViewModels.
- Los nombres de modelo de vista se corresponden con los nombres de vista y terminan con "ViewModel".

Por último, el método `OnAutoWireViewModelChanged` establece el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del tipo de vista en el tipo de modelo de vista resuelta. Para obtener más información sobre cómo resolver el tipo de modelo de vista, vea [resolución](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Este enfoque tiene la ventaja de que una aplicación tiene una sola clase que es responsable de la creación de instancias de los modelos de vista y su conexión con las vistas.

> [!TIP]
> Use un localizador de modelo de vista para facilitar la sustitución. Un localizador de modelo de vista también se puede usar como punto de sustitución para implementaciones alternativas de dependencias, como las pruebas unitarias o los datos de tiempo de diseño.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Actualizar vistas en respuesta a cambios en el modelo o modelo de vista subyacente

Todas las clases modelo de vista y modelo a las que se puede tener acceso en una vista deben implementar la interfaz `INotifyPropertyChanged`. La implementación de esta interfaz en un modelo de vista o una clase de modelo permite que la clase proporcione notificaciones de cambios a los controles enlazados a datos de la vista cuando cambia el valor de la propiedad subyacente.

Las aplicaciones deben diseñarse para el uso correcto de la notificación de cambios de propiedad, cumpliendo los siguientes requisitos:

- Genere siempre un evento `PropertyChanged` si cambia el valor de una propiedad pública. No suponga que se puede omitir la generación del evento `PropertyChanged` debido al conocimiento de cómo se produce el enlace XAML.
- Genere siempre un evento `PropertyChanged` para cualquier propiedad calculada cuyos valores utilicen otras propiedades del modelo o modelo de vista.
- Provoque siempre el evento `PropertyChanged` al final del método que realiza un cambio en la propiedad o cuando se sabe que el objeto está en un estado seguro. La generación del evento interrumpe la operación al invocar los controladores del evento de forma sincrónica. Si esto ocurre en medio de una operación, podría exponer el objeto a las funciones de devolución de llamada cuando está en un estado no seguro y parcialmente actualizado. Además, es posible que los eventos de `PropertyChanged` desencadenen los cambios en cascada. Normalmente, los cambios en cascada requieren que se completen las actualizaciones antes de que se pueda ejecutar de forma segura el cambio en cascada.
- Nunca se genera un evento `PropertyChanged` si la propiedad no cambia. Esto significa que debe comparar los valores antiguos y nuevos antes de generar el evento `PropertyChanged`.
- Nunca se provoca el evento `PropertyChanged` durante el constructor de un modelo de vista si se está inicializando una propiedad. Los controles enlazados a datos de la vista no se habrán suscrito para recibir notificaciones de cambios en este momento.
- Nunca se produce más de un evento `PropertyChanged` con el mismo argumento de nombre de propiedad dentro de una única invocación sincrónica de un método público de una clase. Por ejemplo, dada una propiedad `NumberOfItems` cuya memoria auxiliar es el campo `_numberOfItems`, si un método incrementa `_numberOfItems` 50 veces durante la ejecución de un bucle, solo debe generar una notificación de cambio de propiedad en la propiedad `NumberOfItems` una vez, una vez completado todo el trabajo. Para los métodos asincrónicos, se genera el evento `PropertyChanged` para un nombre de propiedad determinado en cada segmento sincrónico de una cadena de continuación asincrónica.

La aplicación móvil eShopOnContainers usa la clase `ExtendedBindableObject` para proporcionar notificaciones de cambios, que se muestra en el ejemplo de código siguiente:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

La clase [`BindableObject`](xref:Xamarin.Forms.BindableObject) de Xamarin. Forms implementa la interfaz `INotifyPropertyChanged` y proporciona un método [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) . La clase `ExtendedBindableObject` proporciona el método `RaisePropertyChanged` para invocar la notificación de cambio de propiedad y, al hacerlo, usa la funcionalidad proporcionada por la clase `BindableObject`.

Cada clase de modelo de vista de la aplicación móvil eShopOnContainers se deriva de la clase `ViewModelBase`, que a su vez se deriva de la clase `ExtendedBindableObject`. Por lo tanto, cada clase de modelo de vista usa el método `RaisePropertyChanged` de la clase `ExtendedBindableObject` para proporcionar la notificación de cambio de propiedad. En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers invoca la notificación de cambio de propiedad mediante una expresión lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Tenga en cuenta que el uso de una expresión lambda de esta manera implica un pequeño costo de rendimiento porque la expresión lambda debe evaluarse para cada llamada. Aunque el costo de rendimiento es pequeño y no afectaría normalmente a una aplicación, los costos pueden acumularse cuando hay muchas notificaciones de cambios. Sin embargo, la ventaja de este enfoque es que proporciona compatibilidad con la refactorización y la seguridad de tipos en tiempo de compilación al cambiar el nombre de las propiedades.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interacción con la interfaz de usuario mediante comandos y comportamientos

En Mobile Apps, las acciones se invocan normalmente en respuesta a una acción del usuario, como un clic de botón, que se puede implementar creando un controlador de eventos en el archivo de código subyacente. Sin embargo, en el patrón MVVM, la responsabilidad de implementar la acción se basa en el modelo de vista y se debe evitar la colocación del código en el código subyacente.

Los comandos proporcionan una manera cómoda de representar acciones que se pueden enlazar a controles en la interfaz de usuario. Encapsulan el código que implementa la acción y ayudan a mantenerla desacoplada de su representación visual en la vista. Xamarin. Forms incluye controles que se pueden conectar mediante declaración a un comando, y estos controles invocarán el comando cuando el usuario interactúe con el control.

Los comportamientos también permiten que los controles se conecten mediante declaración a un comando. Sin embargo, los comportamientos se pueden usar para invocar una acción asociada a un intervalo de eventos generados por un control. Por lo tanto, los comportamientos abordan muchos de los mismos escenarios que los controles habilitados para comandos, a la vez que proporcionan un mayor grado de flexibilidad y control. Además, los comportamientos también se pueden usar para asociar objetos de comando o métodos con controles que no se diseñaron específicamente para interactuar con los comandos.

### <a name="implementing-commands"></a>Implementar comandos

Los modelos de vista normalmente exponen las propiedades del comando, para el enlace desde la vista, que son instancias de objeto que implementan la interfaz `ICommand`. Varios controles de Xamarin. Forms proporcionan una propiedad `Command`, que puede ser datos enlazados a un objeto `ICommand` proporcionado por el modelo de vista. La interfaz de `ICommand` define un método `Execute`, que encapsula la propia operación, un método `CanExecute`, que indica si se puede invocar el comando y un evento `CanExecuteChanged` que se produce cuando se producen cambios que afectan a si se debe ejecutar el comando. Las clases [`Command`](xref:Xamarin.Forms.Command) y [`Command<T>`](xref:Xamarin.Forms.Command) , proporcionadas por Xamarin. Forms, implementan la interfaz `ICommand`, donde `T` es el tipo de los argumentos que se van a `Execute` y `CanExecute`.

Dentro de un modelo de vista, debe haber un objeto de tipo [`Command`](xref:Xamarin.Forms.Command) o [`Command<T>`](xref:Xamarin.Forms.Command) para cada propiedad pública en el modelo de vista de tipo `ICommand`. El constructor `Command` o `Command<T>` requiere un objeto de devolución de llamada de `Action` al que se llama cuando se invoca el método de `ICommand.Execute`. El método `CanExecute` es un parámetro de constructor opcional y es un `Func` que devuelve una `bool`.

En el código siguiente se muestra cómo una instancia de [`Command`](xref:Xamarin.Forms.Command) , que representa un comando de registro, se construye especificando un delegado en el método de modelo de vista `Register`:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

El comando se expone a la vista a través de una propiedad que devuelve una referencia a un `ICommand`. Cuando se llama al método `Execute` en el objeto [`Command`](xref:Xamarin.Forms.Command) , simplemente reenvía la llamada al método en el modelo de vista a través del delegado que se especificó en el constructor `Command`.

Un comando puede invocar un método asincrónico mediante el uso de las palabras clave `async` y `await` al especificar el delegado de `Execute` del comando. Esto indica que la devolución de llamada es una `Task` y se debe esperar. Por ejemplo, el código siguiente muestra cómo una instancia de [`Command`](xref:Xamarin.Forms.Command) , que representa un comando de inicio de sesión, se construye especificando un delegado para el método de modelo de vista `SignInAsync`:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Los parámetros se pueden pasar a las acciones `Execute` y `CanExecute` mediante la clase [`Command<T>`](xref:Xamarin.Forms.Command) para crear una instancia del comando. Por ejemplo, el código siguiente muestra cómo se usa una instancia de `Command<T>` para indicar que el método de `NavigateAsync` necesitará un argumento de tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

En las clases [`Command`](xref:Xamarin.Forms.Command) y [`Command<T>`](xref:Xamarin.Forms.Command) , el delegado para el método `CanExecute` en cada constructor es opcional. Si no se especifica un delegado, el `Command` devolverá `true` para `CanExecute`. Sin embargo, el modelo de vista puede indicar un cambio en el estado de `CanExecute` del comando llamando al método `ChangeCanExecute` en el objeto `Command`. Esto hace que se genere el evento `CanExecuteChanged`. Los controles de la interfaz de usuario que estén enlazados al comando actualizarán su estado habilitado para reflejar la disponibilidad del comando enlazado a datos.

#### <a name="invoking-commands-from-a-view"></a>Invocar comandos desde una vista

En el ejemplo de código siguiente se muestra cómo un [`Grid`](xref:Xamarin.Forms.Grid) del `LoginView` se enlaza a la `RegisterCommand` en la clase `LoginViewModel` mediante una instancia de [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) :

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

También se puede definir opcionalmente un parámetro de comando mediante la propiedad [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) . El tipo del argumento esperado se especifica en los métodos de destino `Execute` y `CanExecute`. El [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) invocará automáticamente el comando de destino cuando el usuario interactúe con el control adjunto. El parámetro de comando, si se proporciona, se pasará como argumento al delegado de `Execute` del comando.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementar comportamientos

Los comportamientos permiten agregar funcionalidad a los controles de interfaz de usuario sin tener que crear una subclase. En su lugar, la función se implementa en una clase de comportamiento y se asocia al control como si fuera parte de este. Los comportamientos permiten implementar código que normalmente tendría que escribir como código subyacente, ya que interactúa directamente con la API del control, de tal forma que se puede adjuntar de manera concisa al control y empaquetarse para reutilizarlo en más de una vista o aplicación. En el contexto de MVVM, los comportamientos son un enfoque útil para la conexión de controles a comandos.

Un comportamiento que se adjunta a un control a través de las propiedades adjuntas se conoce como un *comportamiento adjunto*. Después, el comportamiento puede usar la API expuesta del elemento al que está adjunto para agregar funcionalidad a ese control, u otros controles, en el árbol visual de la vista. La aplicación móvil eShopOnContainers contiene la clase `LineColorBehavior`, que es un comportamiento adjunto. Para obtener más información sobre este comportamiento, vea [Mostrar errores de validación](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamiento de Xamarin. Forms es una clase que se deriva de la clase [`Behavior`](xref:Xamarin.Forms.Behavior) o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , donde `T` es el tipo del control al que se debe aplicar el comportamiento. Estas clases proporcionan `OnAttachedTo` y `OnDetachingFrom` métodos, que se deben invalidar para proporcionar lógica que se ejecutará cuando el comportamiento se adjunte a los controles y se desasocie de ellos.

En la aplicación móvil eShopOnContainers, la clase `BindableBehavior<T>` se deriva de la clase [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) . El propósito de la clase `BindableBehavior<T>` es proporcionar una clase base para los comportamientos de Xamarin. Forms que requieren que el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento se establezca en el control adjunto.

La clase `BindableBehavior<T>` proporciona un método de `OnAttachedTo` reemplazable que establece la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento y un método de `OnDetachingFrom` reemplazable que limpia el `BindingContext`. Además, la clase almacena una referencia al control adjunto en la propiedad `AssociatedObject`.

La aplicación móvil eShopOnContainers incluye una clase `EventToCommandBehavior`, que ejecuta un comando en respuesta a un evento que se está produciendo. Esta clase se deriva de la clase `BindableBehavior<T>` para que el comportamiento pueda enlazar y ejecutar una `ICommand` especificada por una propiedad `Command` cuando se consume el comportamiento. En el ejemplo de código siguiente se muestra la clase `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

Los métodos `OnAttachedTo` y `OnDetachingFrom` se utilizan para registrar y anular el registro de un controlador de eventos para el evento definido en la propiedad `EventName`. A continuación, cuando se desencadena el evento, se invoca el método de `OnFired`, que ejecuta el comando.

La ventaja de usar el `EventToCommandBehavior` para ejecutar un comando cuando se activa un evento es que los comandos se pueden asociar a controles que no se diseñaron para interactuar con los comandos. Además, mueve el código de control de eventos para ver los modelos, donde se puede probar la unidad.

#### <a name="invoking-behaviors-from-a-view"></a>Invocar comportamientos desde una vista

La `EventToCommandBehavior` es especialmente útil para adjuntar un comando a un control que no admite comandos. Por ejemplo, el `ProfileView` usa el `EventToCommandBehavior` para ejecutar el `OrderDetailCommand` cuando el evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se activa en el [`ListView`](xref:Xamarin.Forms.ListView) que enumera los pedidos del usuario, tal como se muestra en el código siguiente:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [`ListView`](xref:Xamarin.Forms.ListView). Cuando se selecciona un elemento en el `ListView`, se activará el evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , que ejecutará el `OrderDetailCommand` en el `ProfileViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten a medida que se pasan entre el origen y el destino mediante el convertidor especificado en la propiedad `EventArgsConverter`, que devuelve el [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) de la `ListView` del [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs). Por lo tanto, cuando se ejecuta el `OrderDetailCommand`, el `Order` seleccionado se pasa como parámetro a la acción registrada.

Para obtener más información sobre los comportamientos, vea [Behaviors](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumen

El patrón Modelo-Vista-Modelo de vista (MVVM) ayuda a separar la lógica de negocios y presentación de una aplicación de su interfaz de usuario. Mantener una separación clara entre la lógica de aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que probar una aplicación, mantenerla y desarrollarla sea más fácil. También puede mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario colaborar con mayor facilidad al desarrollar sus respectivos elementos de una aplicación.

Con el patrón de MVVM, la interfaz de usuario de la aplicación y la presentación subyacente y la lógica de negocios se dividen en tres clases independientes: la vista, que encapsula la interfaz de usuario y la lógica de la interfaz de usuario. el modelo de vista, que encapsula la lógica de presentación y el estado; y el modelo, que encapsula la lógica de negocios y los datos de la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
