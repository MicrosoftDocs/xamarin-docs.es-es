---
title: ''
description: Xamarin.FormsLas soluciones anteriores (o las creadas en MacOS) no tendrán proyectos de plataforma universal de Windows, por lo que en este artículo se explica cómo agregar un nuevo proyecto de UWP a una Xamarin.Forms solución existente.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87ad78e97046eef7fd6c2e062fa9f84d92c11b38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132215"
---
# <a name="setup-windows-projects"></a>Configurar proyectos de Windows

_Agregar nuevos proyectos de Windows a una Xamarin.Forms solución existente_

Xamarin.FormsLas soluciones anteriores (o las creadas en MacOS) no tendrán proyectos de aplicación plataforma universal de Windows (UWP). Por lo tanto, tendrá que agregar manualmente un proyecto de UWP para compilar una aplicación de Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Agregar una aplicación Plataforma universal de Windows

Se recomienda **Visual Studio 2019** en **Windows 10** para compilar aplicaciones para UWP. Para obtener más información sobre el Plataforma universal de Windows, consulte [Introducción al plataforma universal de Windows](/windows/uwp/get-started/universal-application-platform-guide/).

UWP está disponible en Xamarin.Forms 2,1 y versiones posteriores, y Xamarin.Forms . Maps se admite en Xamarin.Forms 2,2 y versiones posteriores.

Consulte la sección de <a href="#troubleshooting">solución de problemas</a> para obtener sugerencias útiles.

Siga estas instrucciones para agregar una aplicación para UWP que se ejecutará en teléfonos, tabletas y escritorios de Windows 10:

 dimensional. Haga clic con el botón derecho en la solución y seleccione **agregar > nuevo proyecto...** y agregue un proyecto **aplicación vacía (Windows universal)** :

  ![](universal-images/add-wu.png "Add New Project Dialog")

 dos. En el cuadro de diálogo **nuevo proyecto de plataforma universal de Windows** , seleccione las versiones mínima y de destino de Windows 10 en las que se ejecutará la aplicación:

  ![](universal-images/target-version.png "New Universal Windows Platform Project Dialog")

 3. Haga clic con el botón derecho en el proyecto UWP y seleccione **administrar paquetes NuGet...** y agregue el **Xamarin.Forms** paquete. Asegúrese de que los otros proyectos de la solución también se actualizan a la misma versión del Xamarin.Forms paquete.

 4. Asegúrese de que el nuevo proyecto de UWP se generará en la **> de compilación Configuration Manager** ventana (probablemente no se haya producido de forma predeterminada). Marque las casillas **compilar** e **implementar** del proyecto universal:

  [![](universal-images/configuration-sml.png "Configuration Manager Window")](universal-images/configuration.png#lightbox "Configuration Manager Window")

 5. Haga clic con el botón derecho en el proyecto y seleccione **agregar > referencia** y cree una referencia al Xamarin.Forms proyecto de aplicación (.net Standard o proyecto compartido).

  ![](universal-images/addref-sml.png "Reference Manager Dialog")

 1,8. En el proyecto de UWP, edite **app.Xaml.CS** para incluir la `Init` llamada de método dentro del método en torno a la `OnLaunched` línea 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. En el proyecto de UWP, edite **mainpage. Xaml** quitando el `Grid` contenido dentro del `Page` elemento.

 203. En **mainpage. Xaml**, agregue una nueva `xmlns` entrada para `Xamarin.Forms.Platform.UWP` :

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. En **mainpage. Xaml**, cambie el `<Page` elemento raíz a `<forms:WindowsPage` :

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 7. En el proyecto de UWP, edite **mainpage.Xaml.CS** para quitar el `: Page` especificador de herencia para el nombre de clase (dado que ahora heredará de `WindowsPage` debido al cambio realizado en el paso anterior):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 279. En **mainpage.Xaml.CS**, agregue la `LoadApplication` llamada en el `MainPage` constructor para iniciar la Xamarin.Forms aplicación:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

> [!NOTE]
> El argumento para el `LoadApplication` método es la `Xamarin.Forms.Application` instancia definida en el proyecto de .net Standard.

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

305. Agregue los recursos locales (por ejemplo, archivos de imagen) de los proyectos de plataforma existentes que son necesarios.

## <a name="troubleshooting"></a>Solución de problemas

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Excepción de invocación de destino" cuando se usa "compilar con .NET Native cadena de herramientas"

Si la aplicación de UWP hace referencia a varios ensamblados (por ejemplo, bibliotecas de control de terceros o si la propia aplicación se divide en varias bibliotecas), Xamarin.Forms es posible que no pueda cargar objetos de esos ensamblados (como los representadores personalizados).

Esto puede ocurrir cuando se usa la **cadena de herramientas compilar con .net Native** , que es una opción para las aplicaciones UWP en las **propiedades > compilar > ventana general** del proyecto.

Puede corregirlo mediante una sobrecarga específica de UWP de la `Forms.Init` llamada en **app.Xaml.CS** como se muestra en el código siguiente (debe reemplazar `ClassInOtherAssembly` por una clase real a la que las referencias de código):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Agregue una entrada para cada ensamblado que ha agregado como referencia en el Explorador de soluciones, ya sea mediante una referencia directa o un NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Servicios de dependencia y compilación de .NET Native

Las compilaciones de versión que usan la compilación de .NET Native pueden no resolver servicios de dependencia definidos fuera del archivo ejecutable de la aplicación principal (por ejemplo, en un proyecto o biblioteca independiente).

Use el `DependencyService.Register<T>()` método para registrar manualmente las clases de servicio de dependencia. Según el ejemplo anterior, agregue el método de registro de la siguiente manera:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
