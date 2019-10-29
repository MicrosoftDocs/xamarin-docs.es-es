---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF frente a Xamarin. Forms: similitudes & diferencias'
description: En este documento se comparan y contrastan WPF con Xamarin. Forms. Se describen las plantillas de control, XAML, la infraestructura de enlace, las plantillas de datos, ItemsControl, UserControl, la navegación y la navegación de direcciones URL.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 798839457a418d457bac83e6e20397722423dbac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016486"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF frente a Xamarin. Forms: similitudes & diferencias

## <a name="control-templates"></a>Plantillas de control

WPF admite el concepto de *plantillas de control* que proporcionan las instrucciones de visualización de un Control (`Button`, `ListBox`, etc.). Como se mencionó anteriormente, Xamarin. Forms usa clases de _representación_ concretas para esto que interactúan con la plataforma nativa (iOS, Android, etc.) para visualizar el control.

Sin embargo, Xamarin. _Forms tiene un_ tipo `ControlTemplate`: se usa para los objetos `Page`. Proporciona una definición para una `Page` que proporciona contenido coherente, pero permite al usuario de la página cambiar los colores, las fuentes, etc. e incluso agregar elementos para que sea único para la aplicación.

Los usos comunes de esto son cosas como cuadros de diálogo de autenticación, mensajes y para proporcionar una apariencia de página estandarizada pero que se puede personalizar en la aplicación. Como parte de esta compatibilidad, se usan muchos controles conocidos con nombre de WPF:

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Pero es importante saber que _no_ están atendiendo al mismo propósito en Xamarin. Forms. Para obtener más información sobre esta característica, consulte la [Página de documentación](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML se usa como lenguaje de marcado declarativo para WPF y Xamarin. Forms. En su mayor parte, la sintaxis es idéntica: la principal diferencia es que los objetos definidos/creados por los gráficos XAML.

- Xamarin. Forms admite la [especificación de XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Esto facilita la definición de datos, como `string`s, `int`s, etc., así como la definición de tipos genéricos y el paso de argumentos a los constructores.

- Actualmente no hay ninguna manera de cargar dinámicamente XAML, como WPF, con `XamlReader`. Sin embargo, puede obtener la misma funcionalidad básica con un [paquete de NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Extensiones de marcado

Xamarin. Forms admite la extensión de XAML a través de extensiones de marcado, como WPF. De la caja, tiene los mismos bloques de creación básicos:

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

Además, incluye `{x:Reference}` de la especificación de XAML 2009 y una extensión de marcado `{TemplateBinding}` que se usa para la versión especializada de `ControlTemplate` compatible con Xamarin. Forms.

> [!WARNING]
> La compatibilidad con `ControlTemplate` no es la misma, aunque tenga el mismo nombre.

Xamarin. Forms admite también extensiones de marcado personalizadas, pero la implementación es ligeramente diferente. En WPF, debe derivar de `MarkupExtension`-una clase base abstracta. En Xamarin. Forms, que se reemplaza con una interfaz `IMarkupExtension` o `IMarkupExtension<T>` que es más flexible.

Al igual que WPF, el único método necesario es un método `ProvideValue` para devolver el valor de la extensión de marcado.

## <a name="binding-infrastructure"></a>Infraestructura de enlace

Uno de los conceptos principales que se llevan a cabo es una infraestructura de enlace de datos para conectar las propiedades visuales a las propiedades de datos de .NET. Esto permite patrones arquitectónicos como MVVM. El diseño básico es idéntico: tiene una clase base enlazable [BindableObject](xref:Xamarin.Forms.BindableObject), en WPF, es la clase [DependencyObject](xref:System.Windows.DependencyObject) . Esta clase base se usa como antecesor raíz para todos los objetos que participarán como destinos en el enlace de datos. A continuación, las clases derivadas exponen objetos [BindableProperty](xref:Xamarin.Forms.BindableProperty) que actúan como almacenamiento de seguridad para los valores de propiedad (se definen como objetos [DependencyProperty](xref:System.Windows.DependencyProperty) en WPF).

### <a name="defining-bindable-properties"></a>Definir propiedades enlazables

La definición de una propiedad enlazable en Xamarin. Forms es la misma que la de WPF:

1. El objeto debe derivarse de `BindableObject`.
2. Debe haber un campo estático público de tipo `BindableProperty` declarado para definir la clave de almacenamiento de respaldo para la propiedad.
3. Debe haber un contenedor de propiedades de instancia público que use `GetValue` y `SetValue` para recuperar y cambiar el valor de las propiedades.

Para obtener un ejemplo completo, vea [propiedades enlazables en Xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propiedades adjuntas

Las propiedades adjuntas son un subconjunto de la propiedad enlazable y funcionan de la misma manera que en WPF. La principal diferencia es que el contenedor de la propiedad se se omite en este caso y se reemplaza por un conjunto de métodos Get/Set estáticos en la clase propietaria. Consulte [propiedades adjuntas en Xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) para obtener más información.

### <a name="using-the-binding-engine"></a>Usar el motor de enlace

El proceso para utilizar el motor de enlace es el mismo que en WPF. Se puede utilizar en el código subyacente si se crea un objeto de `Binding` vinculado a un objeto de origen (cualquier tipo .NET) y un valor de propiedad opcional (si se omite, trata el objeto de origen como la propiedad en sí, como WPF). A continuación, puede usar `SetBinding` en cualquier `BindableObject` para asociar el enlace a un `BindableProperty`.

Como alternativa, puede definir la relación de enlace en XAML mediante el `BindingExtension`. Tiene los mismos valores básicos que la extensión en WPF.

La compatibilidad y el motor de enlace son más similares a la implementación de Silverlight que WPF. Hay varias características que faltan que no se implementaron en Xamarin. Forms:

- No se admiten las siguientes características en los enlaces:
  - BindingGroupName
  - BindsDirectlyToSource
  - IsAsync
  - MultiBinding
  - NotifyOnSourceUpdated
  - NotifyOnTargetUpdated
  - NotifyOnValidationError
  - UpdateSourceTrigger
  - UpdateSourceExceptionFilter
  - ValidatesOnDataErrors
  - ValidatesOnExceptions
  - ValidationRules (colección)
  - XPath
  - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

No se admiten enlaces de `RelativeSource`. En WPF, permiten enlazar a otros elementos visuales definidos en XAML. En Xamarin. Forms, esta misma funcionalidad se puede lograr mediante el `{x:Reference}` extensión de marcado. Por ejemplo, suponiendo que tenemos un control con el nombre "otherControl" que tiene una propiedad Text, se puede enlazar a él de la siguiente manera:

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

Se puede usar la misma capacidad para la característica `{RelativeSource Self}`. Sin embargo, no se admite la búsqueda de antecesores por tipo (`{RelativeSource FindAncestor}`).

#### <a name="binding-context"></a>Contexto de enlace

En WPF, puede definir un valor de propiedad `DataContext` que reprents el origen de enlace predeterminado. Si no se define el origen de un enlace, se utiliza este valor de propiedad. El valor se hereda en el árbol visual, lo que permite que se defina en un nivel superior y, a continuación, lo usen los elementos secundarios.

En Xamarin. Forms, esta misma característica es disponibles, pero el nombre de la propiedad es `BindingContext`.

#### <a name="value-converters"></a>Convertidores de valores

Los convertidores de valores son totalmente compatibles con Xamarin. Forms, al igual que WPF. Se usa la misma forma de interfaz, pero Xamarin. Forms tiene la interfaz definida en el espacio de nombres `Xamarin.Forms`.

### <a name="model-view-viewmodel"></a>Modelo-vista-ViewModel

MVVM es totalmente compatible con WPF y Xamarin. Forms.

WPF incluye una `RoutedCommand` integrada que se usa a veces; Xamarin. Forms no tiene compatibilidad integrada con comandos más allá de la definición de la interfaz de `ICommand`. Puede incluir una variedad de marcos de MVVM para agregar las clases base necesarias para implementar MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged e INotifyCollectionChanged

Ambas interfaces son totalmente compatibles con los enlaces de Xamarin. Forms. A diferencia de muchos marcos de trabajo basados en XAML, las notificaciones de cambio de propiedad se pueden generar en subprocesos en segundo plano de Xamarin. Forms (al igual que WPF) y el motor de enlace realizará correctamente la transición al subproceso de interfaz de usuario.

Además, ambos entornos admiten `SynchronziationContext` y `async` / `await` para realizar la serialización correcta de subprocesos. WPF incluye la clase `Dispatcher` en todos los elementos visuales, Xamarin. Forms tiene un método estático `Device.BeginInvokeOnMainThread` que también se puede utilizar (aunque se prefiere `SynchronizationContext` para la codificación multiplataforma).

- Xamarin. Forms incluye una `ObservableCollection<T>` que admite las notificaciones de cambio de colección.
- Puede usar `BindingBase.EnableCollectionSynchronization` para habilitar las actualizaciones entre subprocesos para una colección. La API es ligeramente diferente de la variación de WPF. [consulte los documentos para obtener detalles de uso](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Plantillas de datos

Las plantillas de datos se admiten en Xamarin. Forms para personalizar la representación de una fila de `ListView` (celda). A diferencia de WPF, que puede usar `DataTemplate`s para cualquier control orientado a contenido, Xamarin. Forms solo los usa actualmente para `ListView`. La definición de plantilla se puede definir en línea (asignada a la propiedad `ItemTemplate`) o como un recurso en un `ResourceDictionary`.

Además, no son tan flexibles como sus homólogos de WPF.

1. El elemento raíz del `DataTemplate` _siempre_ debe ser un objeto `ViewCell`.
2. Los desencadenadores de datos se admiten por completo en una plantilla de datos, pero deben incluir una propiedad `DataType` que indique el tipo de la propiedad a la que está asociado el desencadenador.
3. también se admite `DataTemplateSelector`, pero se deriva de `DataTemplate` y, por tanto, simplemente se asigna directamente a la propiedad `ItemTemplate` (vs.  `ItemTemplateSelector` en WPF).

## <a name="itemscontrol"></a>ItemsControl

No hay ningún equivalente integrado a un `ItemsControl` de Xamarin. Forms; pero hay una [personalizada para Xamarin. Forms disponible aquí](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Controles de usuario

En WPF, `UserControl`s se usan para proporcionar una sección de la interfaz de usuario que tiene un comportamiento asociado. En Xamarin. Forms, usamos el `ContentView` para el mismo propósito. Ambos admiten el enlace y la inclusión en XAML.

## <a name="navigation"></a>Navegación

WPF incluye un `NavigationService` que rara vez se usa, que se puede usar para proporcionar una característica de navegación de tipo "explorador". Sin embargo, la mayoría de las aplicaciones no se molestan y, en su lugar, usan distintos elementos de `Window`, o secciones diferentes de la ventana para mostrar los datos.

En los dispositivos de teléfono, las _pantallas_ diferentes suelen ser la solución y, por tanto, Xamarin. Forms incluye compatibilidad con varias formas de navegación:

| Estilo de navegación | Tipo de página |
|--- |--- |
|Basado en pila (Inserte/pop)|NavigationPage|
|Maestro y detalles|MasterDetailPage|
|Tabulaciones|TabbedPage|
|Deslice el dedo hacia la izquierda o la derecha|CarouselView|

El `NavigationPage` es el enfoque más común, y cada página tiene una propiedad `Navigation` que se puede usar para enviar o cerrar páginas en la pila de navegación. Este es el equivalente más cercano al `NavigationService` que se encuentra en WPF.

### <a name="url-navigation"></a>Navegación de direcciones URL

WPF es una tecnología orientada al escritorio y puede aceptar parámetros de línea de comandos para el comportamiento de Inicio directo. Xamarin. Forms puede usar la [vinculación de URL profunda](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) para saltar a una página en el inicio.
