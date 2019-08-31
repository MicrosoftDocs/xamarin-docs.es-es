---
title: Actualización de aplicaciones de Xamarin. Forms existentes
description: En este documento se describen los pasos que deben seguirse para actualizar una aplicación de Xamarin. Forms desde el Classic API al Unified API.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: dee3b04630ae9fc94548becdcc294427f9deb433
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199210"
---
# <a name="updating-existing-xamarinforms-apps"></a>Actualización de aplicaciones de Xamarin. Forms existentes

_Siga estos pasos para actualizar una aplicación de Xamarin. Forms existente para que use el Unified API y la actualización a la versión 1.3.1_

> [!IMPORTANT]
> Como Xamarin. Forms 1.3.1 es la primera versión que admite el Unified API, toda la solución debe actualizarse para usar la versión más reciente al mismo tiempo que la migración de la aplicación iOS a Unified. Esto significa que, además de actualizar el proyecto de iOS para ofrecer compatibilidad unificada, también necesitará editar el código en _todos_ los proyectos de la solución.

La actualización se realiza en dos pasos:

1. Migre la aplicación iOS a la Unified API mediante la herramienta de migración de la compilación de Visual Studio para Mac.

    - Use la herramienta de migración para actualizar automáticamente el proyecto.

    - Actualice las API nativas de iOS como se describe en las instrucciones para [actualizar las aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (específicamente en el código de representador personalizado o de servicio de dependencia).

2. Actualice toda la solución a la versión 1,3 de Xamarin. Forms.

    1. Instale el paquete de NuGet Xamarin. Forms 1.3.1.

    2. Actualice la `App` clase en el código compartido.

    3. `AppDelegate` Actualice en el proyecto de iOS.

    4. `MainActivity` Actualice en el proyecto de Android.

    5. `MainPage` Actualice en el proyecto de Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. aplicación iOS (migración unificada)

Parte de la migración requiere la actualización de Xamarin. Forms a la versión 1,3, que admite el Unified API. Para poder crear las referencias de ensamblado correctas, primero es necesario actualizar el proyecto de iOS para usar el Unified API.

### <a name="migration-tool"></a>Herramienta de migración

Haga clic en el proyecto de iOS para que esté seleccionado y, a continuación, elija **proyecto > migrar a Xamarin. iOS Unified API...** y acepte el mensaje de advertencia que aparece.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Elija proyecto > migrar a Xamarin. iOS Unified API... y acepten el mensaje de advertencia que aparece")

Esto lo hará automáticamente:

- Cambie el tipo de proyecto para que admita la API de 64 bits unificada.
- Cambie la referencia de Framework a **Xamarin. iOS** (reemplazando la referencia de MonoTouch anterior).
- Cambie las referencias de espacio de nombres en el código `MonoTouch` para quitar el prefijo.
- Actualice el archivo **csproj** para usar los destinos de compilación correctos para el Unified API.

**Limpie** y **compile** el proyecto para asegurarse de que no hay otros errores que corregir. No es necesario realizar ninguna otra acción. Estos pasos se explican con más detalle en los [documentos de Unified API](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Actualización de las API nativas de iOS (si es necesario)

Si ha agregado código nativo adicional de iOS (como representadores personalizados o servicios de dependencia), puede que necesite realizar correcciones de código manuales adicionales. Vuelva a compilar la aplicación y consulte las [instrucciones de actualización de aplicaciones existentes de iOS](~/cross-platform/macios/unified/updating-ios-apps.md) para obtener información adicional acerca de los cambios que pueden ser necesarios. [Estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) también ayudarán a identificar los cambios necesarios.

## <a name="2-xamarinforms-131-update"></a>2. Actualización de Xamarin. Forms 1.3.1

Una vez que la aplicación de iOS se ha actualizado al Unified API, el resto de la solución debe actualizarse a Xamarin. Forms versión 1.3.1. Esto incluye:

- Actualizar el paquete de NuGet de Xamarin. Forms en cada proyecto.
- Cambiar el código para usar las nuevas clases de Xamarin `Application`. `FormsApplicationDelegate` Forms, ( `FormsApplicationActivity` iOS), (Android `FormsApplicationPage` ) y (Windows Phone).

Estos pasos se explican a continuación:

### <a name="21-update-nuget-in-all-projects"></a>2,1 actualización de NuGet en todos los proyectos

Actualice la versión preliminar de Xamarin. Forms a 1.3.1 con el administrador de paquetes NuGet para todos los proyectos de la solución: PCL (si existe), iOS, Android y Windows Phone. Se recomienda **eliminar y volver a agregar** el paquete de NuGet de Xamarin. Forms para actualizar a la versión 1,3.

> [!NOTE]
> La versión 1.3.1 de Xamarin. Forms se encuentra actualmente en *versión preliminar*. Esto significa que debe seleccionar la opción de **versión preliminar** en NuGet (a través de un cuadro de marca en Visual Studio para Mac o una lista desplegable en Visual Studio) para ver la versión preliminar más reciente.

> [!IMPORTANT]
> Si usa Visual Studio, asegúrese de que está instalada la versión más reciente del administrador de paquetes NuGet. Las versiones anteriores de NuGet en Visual Studio no instalarán correctamente la versión unificada de Xamarin. Forms 1.3.1. Vaya a **herramientas > extensiones y actualizaciones...** y haga clic en la lista **instalado** para comprobar que el **Administrador de paquetes NuGet para Visual Studio** es al menos la versión 2.8.5. Si es anterior, haga clic en la lista de **actualizaciones** para descargar la versión más reciente.

Una vez que haya actualizado el paquete NuGet a Xamarin. Forms 1.3.1, realice los cambios siguientes en cada proyecto para actualizar a `Xamarin.Forms.Application` la nueva clase.

### <a name="22-portable-class-library-or-shared-project"></a>2,2 biblioteca de clases portable (o proyecto compartido)

Cambie el archivo **app.CS** para que:

- La `App` clase ahora hereda de `Application`.
- La `MainPage` propiedad se establece en la primera página de contenido que desea mostrar.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Hemos quitado completamente el `GetMainPage` método y, en su lugar, `MainPage` hemos establecido la `Application` *propiedad* en la subclase.

Esta nueva `Application` clase base también admite las `OnStart`invalidaciones `OnResume` , `OnSleep`y para ayudarle a administrar el ciclo de vida de la aplicación.

A `App` continuación, la clase se pasa a `LoadApplication` un nuevo método en cada proyecto de aplicación, tal y como se describe a continuación:

### <a name="23-ios-app"></a>Aplicación iOS 2,3

Cambie el archivo **AppDelegate.CS** para que:

- La clase hereda de `FormsApplicationDelegate` (en lugar de `UIApplicationDelegate` antes).
- `LoadApplication`se llama a con una nueva instancia `App`de.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>Aplicación Android 2,3

Cambie el archivo **MainActivity.CS** para que:

- La clase hereda de `FormsApplicationActivity` (en lugar de `FormsActivity` antes).
- `LoadApplication`se llama a con una nueva instancia de`App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2,4 Windows Phone aplicación

Necesitamos actualizar la **mainpage** , tanto el código XAML como el código subyacente.

Cambie el archivo **mainpage. Xaml** para que:

- El elemento XAML raíz debe ser `winPhone:FormsApplicationPage`.
- El `xmlns:phone` atributo debe *cambiarse* a`xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

A continuación se muestra un ejemplo actualizado: solo debe editar estos elementos (el resto de los atributos debe permanecer igual):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Cambie el archivo **mainpage.Xaml.CS** para que:

- La clase hereda de `FormsApplicationPage` (en lugar de `PhoneApplicationPage` antes).
- `LoadApplication`se llama a con una nueva instancia de la clase de `App` Xamarin. Forms. Es posible que tenga que calificar por completo esta referencia, ya que `App` Windows Phone tiene la propia clase ya definida.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>Solución de problemas

En ocasiones, verá un error similar al siguiente después de actualizar el paquete de NuGet de Xamarin. Forms. Se produce cuando el actualizador de NuGet no quita completamente las referencias a las versiones anteriores de los archivos **csproj** .

>Su\_proyecto. csproj: Error: Este proyecto hace referencia a los paquetes NuGet que faltan en este equipo. Habilite la restauración del paquete NuGet para descargarlos.  Para obtener más información, consulta http://go.microsoft.com/fwlink/?LinkID=322105. El archivo que falta es.. /.. /packages/Xamarin.Forms.1.2.3.6257/build/portable-win + net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin. Forms. targets. (EL\_PROYECTO)

Para corregir estos errores, abra el archivo **csproj** en un editor de texto y busque `<Target` los elementos que hacen referencia a versiones anteriores de Xamarin. Forms, como el elemento que se muestra a continuación. Debe eliminar manualmente todo este elemento del archivo **csproj** y guardar los cambios.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

El proyecto debe compilarse correctamente una vez que se hayan quitado las referencias anteriores.

## <a name="considerations"></a>Consideraciones

Se deben tener en cuenta las consideraciones siguientes al convertir un proyecto de Xamarin. Forms existente del Classic API al nuevo Unified API si esa aplicación se basa en uno o varios paquetes de componentes o NuGet.

### <a name="components"></a>Componentes

Cualquier componente que haya incluido en la aplicación también tendrá que actualizarse al Unified API o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual por una nueva versión del almacén de componentes de Xamarin que admita el Unified API y realice una compilación limpia. Cualquier componente que el autor no haya convertido todavía, mostrará una advertencia solo de 32 bits en el almacén de componentes.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Aunque hemos contribuido a los cambios a NuGet para que funcionen con el soporte de Unified API, no se ha producido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para reconocer las nuevas API.

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet que haya incluido en el proyecto a una versión que admita las API unificadas y, posteriormente, realizar una compilación limpia.

> [!IMPORTANT]
> Si tiene un error con el formato _"el error 3 no puede incluir ' MonoTouch. dll ' y ' Xamarin. iOS. dll ' en el mismo proyecto de Xamarin. iOS, se hace referencia explícitamente a ' Xamarin. iOS. dll ', mientras que ' XXX, version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ después de convertir la aplicación en las API unificadas, normalmente se debe a que hay un componente o un paquete NuGet en el proyecto que no se ha actualizado al Unified API. Deberá quitar el componente o NuGet existente, actualizar a una versión que admita las API unificadas y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitar compilaciones de 64 bits de aplicaciones de Xamarin. iOS

En el caso de una aplicación móvil de Xamarin. iOS que se ha convertido en el Unified API, el desarrollador todavía necesita habilitar la compilación de la aplicación para equipos de 64 bits a partir de las opciones de la aplicación. Consulte las compilaciones de **64 bits de las aplicaciones de Xamarin. iOS** del documento de consideraciones de la [plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) para obtener instrucciones detalladas sobre cómo habilitar las compilaciones de 64 bits.

## <a name="summary"></a>Resumen

La aplicación de Xamarin. Forms ahora debe actualizarse a la versión 1.3.1 y la aplicación iOS se migró al Unified API (que admite arquitecturas de 64 bits en la plataforma iOS).

Como se indicó anteriormente, si la aplicación de Xamarin. Forms incluye código nativo como representadores personalizados o servicios de dependencia, es posible que también necesiten actualizarse para usar los nuevos tipos [introducidos en el Unified API](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Actualización de aplicaciones de iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Actualización de aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Actualizar sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
