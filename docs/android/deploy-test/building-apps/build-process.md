---
title: Proceso de compilación
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2019
ms.openlocfilehash: 06e40fce69ee6d614bcf27bd563d9452595bd6ab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028148"
---
# <a name="build-process"></a>Proceso de compilación

## <a name="overview"></a>Información general

El proceso de compilación de Xamarin.Android es responsable de pegarlo todo junto y [generar `Resource.designer.cs`](~/android/internals/api-design.md), admitir `AndroidAsset`, `AndroidResource` y otras [acciones de compilación](#Build_Actions), generar [contenedores Android invocables](~/android/platform/java-integration/android-callable-wrappers.md) y generar un archivo `.apk` para su ejecución en dispositivos Android.

## <a name="application-packages"></a>Paquetes de aplicación

En términos generales, hay dos tipos de paquetes de aplicación de Android (archivos `.apk`) que puede generar el sistema de compilación de Xamarin.Android:

- Compilaciones de **versión**, que son totalmente autocontenidas y no requieren la ejecución de paquetes adicionales. Estos son los paquetes que se proporcionarían a una tienda de aplicaciones.

- Compilaciones de **depuración**, los que no.

No es por casualidad que estos correspondan al elemento de MSBuild `Configuration` que genera el paquete.

### <a name="shared-runtime"></a>Entorno de tiempo de ejecución compartido

El *entorno de tiempo de ejecución compartido* es un par de paquetes de Android adicionales que proporcionan la biblioteca de clases base (`mscorlib.dll`, etc.) y la biblioteca de enlaces de Android (`Mono.Android.dll`, etc.). Las compilaciones de depuración dependen del entorno de tiempo de ejecución compartido en lugar de incluir los ensamblados de enlace y de biblioteca de clases base en el paquete de aplicaciones Android, lo que permite que el paquete de depuración sea más pequeño.

El entorno de tiempo de ejecución compartido se puede deshabilitar en las compilaciones de depuración si se establece la propiedad `$(AndroidUseSharedRuntime)` en `False`.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>Implementación rápida

La *implementación rápida* funciona junto con el entorno de tiempo de ejecución compartido para reducir incluso más el tamaño del paquete de aplicaciones Android. Para lograr esto, los ensamblados de la aplicación no se incluyen dentro del paquete. En su lugar, se copian en el destino mediante `adb push`. Este proceso acelera el ciclo de compilación, implementación y depuración dado que *solo* cambian los ensamblados; el paquete no se vuelve a instalar. Solo los ensamblados actualizados se vuelven a sincronizar con el dispositivo de destino.

Se sabe que la implementación rápida genera errores en los dispositivos que impiden que `adb` se sincronicen con el directorio `/data/data/@PACKAGE_NAME@/files/.__override__`.

La implementación rápida está habilitada de forma predeterminada y puede deshabilitarse en las compilaciones de depuración si se establece la propiedad `$(EmbedAssembliesIntoApk)` en `True`.

## <a name="msbuild-projects"></a>Proyectos de MSBuild

El proceso de compilación de Xamarin.Android se basa en MSBuild, que también es el formato de archivo de proyecto usado por Visual Studio para Mac y Visual Studio.
Normalmente, no es necesario que los usuarios editen manualmente los archivos de MSBuild, dado que el IDE crea proyectos totalmente funcionales, los actualiza con los cambios realizados e invoca destinos de compilación según sea necesario.

Puede que los usuarios avanzados quieran realizar operaciones que no se admiten en la interfaz gráfica de usuario del IDE, así que el proceso de compilación se puede personalizar mediante la edición directa del archivo de proyecto.
En esta página solo se documentan las características y personalizaciones específicas de Xamarin.Android; se pueden realizar muchas más cosas con los elementos, las propiedades y los destinos normales de MSBuild.

<a name="Build_Targets" />

## <a name="build-targets"></a>Compilar destinos

Los siguientes destinos de compilación se definen para proyectos de Xamarin.Android:

- **Build**: compila el paquete.

- **Clean**: elimina todos los archivos generados por el proceso de compilación.

- **Install**: instala el paquete en el dispositivo predeterminado o el dispositivo virtual.

- **Uninstall**: desinstala el paquete del dispositivo predeterminado o el dispositivo virtual.

- **SignAndroidPackage**: crea y firma el paquete (`.apk`). Úselo con `/p:Configuration=Release` para generar paquetes de comerciales.

- **UpdateAndroidResources**: actualiza el archivo `Resource.designer.cs`. El IDE llama normalmente a este destino cuando se agregan nuevos recursos al proyecto.

## <a name="build-extension-points"></a>Puntos de extensión de compilación

El sistema de compilación de Xamarin.Android expone algunos puntos de extensión públicos para los usuarios que quieran usar nuestro proceso de compilación. Para usar uno de estos puntos de extensión, deberá agregar su destino personalizado a la propiedad de MSBuild correspondiente en un `PropertyGroup`. Por ejemplo:

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

Tenga en cuenta esta recomendación sobre la extensión del proceso de compilación: Si no se escriben correctamente, las extensiones de compilación pueden afectar al rendimiento de la compilación, especialmente si se ejecutan en cada compilación. Es muy recomendable que lea la [documentación](https://docs.microsoft.com/visualstudio/msbuild/msbuild) de MSBuild antes de implementar estas extensiones.

- **AfterGenerateAndroidManifest**: los destinos enumerados en esta propiedad se ejecutarán directamente después del destino de `_GenerateJavaStubs` interno. Aquí es donde se genera el archivo `AndroidManifest.xml` en el `$(IntermediateOutputPath)`. Por lo tanto, si quiere realizar modificaciones en el archivo `AndroidManifest.xml` generado, puede hacerlo mediante este punto de extensión.

  Se ha agregado en Xamarin.Android 9.4.

- **BeforeGenerateAndroidManifest**: los destinos enumerados en esta propiedad se ejecutarán directamente antes de `_GenerateJavaStubs`.

  Se ha agregado en Xamarin.Android 9.4.

## <a name="build-properties"></a>Propiedades de compilación

Las propiedades de MSBuild controlan el comportamiento de los destinos. Se especifican en el archivo de proyecto, por ejemplo, **MyApp.csproj**, dentro de un [elemento MSBuild PropertyGroup](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **Configuration**: especifica la configuración de compilación que se usará, como "Debug" o "Release". La propiedad Configuration se usa para determinar los valores predeterminados de otras propiedades que determinan el comportamiento de destino. Se pueden crear configuraciones adicionales dentro del IDE.

  *De forma predeterminada*, la configuración `Debug` dará lugar a los destinos `Install` y `SignAndroidPackage` que crean un paquete de Android más pequeño que, para funcionar, requiere la presencia de otros archivos y paquetes.

  La configuración `Release` predeterminada dará lugar a los destinos `Install` y `SignAndroidPackage` que crean un paquete de Android que es *independiente*, y se puede usar sin instalar ningún otro paquete o archivo.

- **DebugSymbols**: un valor booleano que determina si el paquete de Android es depurable (tiene el atributo *debuggable*), en combinación con la propiedad `$(DebugType)`. Un paquete depurable contiene símbolos de depuración, establece el atributo `//application/@android:debuggable` en `true` y agrega automáticamente el permiso `INTERNET` para que el depurador pueda asociarlo al proceso. Una aplicación es depurable si `DebugSymbols` es `True` *y* `DebugType` es la cadena vacía o `Full`.

- **DebugType**: especifica el [tipo de símbolos de depuración](https://docs.microsoft.com/visualstudio/msbuild/csc-task) para generar como parte de la compilación, que también afecta a si la aplicación es depurable. Entre los posibles valores se incluyen:

  - **Full**: se generan símbolos completos. Si la propiedad de MSBuild `DebugSymbols` es también `True`, entonces el paquete de aplicación es depurable.

  - **PdbOnly**: se generan símbolos "PDB". El paquete de aplicación *no* será depurable.

  Si `DebugType` no está establecido o es la cadena vacía, la propiedad `DebugSymbols` controla si la aplicación es o no depurable.

  - **AndroidGenerateLayoutBindings**: permite la generación de [diseño de código subyacente](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) si se establece en `true` o lo deshabilita por completo si se establece en `false`. El valor predeterminado es `false`.

### <a name="install-properties"></a>Propiedades de instalación

Las propiedades de instalación controlan el comportamiento de los destinos `Install` y `Uninstall`.

- **AdbTarget**: especifica el dispositivo de destino de Android en el que se puede instalar o del que se puede quitar el paquete Android. El valor de esta propiedad es el mismo que el de la opción del dispositivo de destino [ `adb`](https://developer.android.com/tools/help/adb.html#issuingcommands):

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>Propiedades de empaquetado

Las propiedades de empaquetado controlan la creación del paquete de Android y se usan con los destinos `Install` y `SignAndroidPackage`.
Las [propiedades de firma](#Signing_Properties) también son importantes al empaquetar aplicaciones de versión.

- **AndroidApkDigestAlgorithm**: un valor de cadena que especifica el algoritmo de síntesis del mensaje que se usará con `jarsigner -digestalg`.

  El valor predeterminado es `SHA1` para los APK y `SHA-256` para los paquetes de aplicaciones.

  Se ha agregado en Xamarin.Android 9.4.

- **AndroidApkSignerAdditionalArguments** &ndash; Una propiedad de cadena que permite al desarrollador proporcionar argumentos adicionales a la herramienta `apksigner`.

  Agregado en Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; Un valor de cadena que especifica el algoritmo de firma para utilizarlo con `jarsigner -sigalg`.

  El valor predeterminado es `md5withRSA` para los APK y `SHA256withRSA` para los paquetes de aplicaciones.

  Agregado en Xamarin.Android 8.2.

- **AndroidApplication**: un valor booleano que indica si el proyecto es para una aplicación Android (`True`) o para un proyecto de la biblioteca de Android (`False` o no existe).

  Solo puede existir un proyecto con `<AndroidApplication>True</AndroidApplication>` en un paquete de Android. (Por desgracia, esto no se ha comprobado aún, lo que puede dar lugar a errores sutiles y extraños respecto a los recursos de Android).

- **AndroidApplicationJavaClass** &ndash; el nombre completo de clase de Java para usar en lugar de `android.app.Application` cuando una clase hereda de [Android.App.Application](xref:Android.App.Application).

  Esta propiedad se establece generalmente mediante *otras* propiedades, como la propiedad de MSBuild `$(AndroidEnableMultiDex)`.

  Agregado en Xamarin.Android 6.1.

- **AndroidBuildApplicationPackage**: un valor booleano que indica si crear y firmar el paquete (.apk). Establecer este valor en `True` es equivalente a usar el destino de compilación [SignAndroidPackage](#Build_Targets).

  Después de Xamarin.Android 7.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `False`.

- **AndroidDexTool**: propiedad de estilo de la enumeración con los valores válidos de `dx` o `d8`. Indica el compilador de [dex][dex] que se usa durante el proceso de compilación de Xamarin.Android.
  Actualmente tiene como valor predeterminado `dx`. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar**: propiedad booleana que determina si `desugar` está habilitado. Android no admite actualmente todas las características de Java 8 y la cadena de herramientas predeterminada implementa las nuevas características de lenguaje ejecutando transformaciones de código de bytes, llamadas `desugar`, en la salida del compilador `javac`. El valor predeterminado es `False` si se usa `AndroidDexTool=dx` y `True` si se usa `AndroidDexTool=d8`.

- **AndroidEnableGooglePlayStoreChecks**: una propiedad booleana que permite a los desarrolladores deshabilitar las siguientes comprobaciones de Google Play Store: XA1004, XA1005 y XA1006. Esto resulta útil para los desarrolladores que no tienen como destino Google Play Store y que no quieren ejecutar las comprobaciones mencionadas.

  Se ha agregado en Xamarin.Android 9.4.

- **AndroidEnableMultiDex**: una propiedad booleana que determina si se usará la compatibilidad con Multi-Dex en el archivo `.apk` final.

  En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `False`.

- **AndroidEnablePreloadAssemblies** &ndash; Una propiedad booleana que controla si todos los ensamblados administrados que se incluyen en el paquete de aplicación se cargan o no durante el inicio del proceso.

  Cuando se establece en `True`, todos los ensamblados que se incluyen en el paquete de aplicación se cargan durante el inicio del proceso, antes de que se invoque cualquier código de aplicación.
  Esta acción es coherente con lo que hacía Xamarin.Android en las versiones anteriores a Xamarin.Android 9.2.

  Cuando se establece en `False`, los ensamblados solo se cargan cuando es necesario.
  De esta forna, las aplicaciones se inician más rápido y también hay una mayor coherencia con la semántica de .NET de escritorio.  Para ver los ahorros de tiempo, establezca la propiedad del sistema `debug.mono.log` en `timing` y busque el mensaje `Finished loading assemblies: preloaded` en `adb logcat`.

  Puede que las aplicaciones o bibliotecas que usan inserción de dependencias *necesiten* que esta propiedad se establezca en `True` si estas requieren a su vez que `AppDomain.CurrentDomain.GetAssemblies()` devuelva todos los ensamblados del conjunto de aplicación, incluso si el ensamblado no hubiera sido necesario.

  De forma predeterminada, este valor se establecerá en `True`.

  Se ha agregado en Xamarin.Android 9.2.

- **AndroidEnableProfiledAot**: una propiedad booleana que determina si los perfiles de AOT se usarán durante la compilación Ahead-of-Time.

  Los perfiles se muestran en el grupo de elementos `AndroidAotProfile`. Este ItemGroup contiene perfiles predeterminados. Se puede reemplazar quitando los perfiles de AOT existentes y agregando los suyos.

  La compatibilidad con esta propiedad se ha agregado en Xamarin.Android 9.4.

  De forma predeterminada, esta propiedad es `False`.

- **AndroidEnableSGenConcurrent**: una propiedad booleana que determina si se usará el [recolector de elementos no utilizados simultáneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen).

  Se agregó compatibilidad con esta propiedad en Xamarin.Android 7.2.

  De forma predeterminada, esta propiedad es `False`.

- **AndroidErrorOnCustomJavaObject** &ndash; Una propiedad booleana que determina si los tipos pueden implementar `Android.Runtime.IJavaObject`
  *sin* heredando también de `Java.Lang.Object` o `Java.Lang.Throwable`:

  ```csharp
  class BadType : IJavaObject {
      public IntPtr Handle {
          get {return IntPtr.Zero;}
      }

      public void Dispose()
      {
      }
  }
  ```

  Cuando es True, estos tipos generarán un error de XA4212, en caso contrario, una advertencia XA4212.

  En Xamarin.Android 8.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `True`.

- **AndroidFastDeploymentType**: lista de valores separada por `:` (dos puntos) para controlar qué tipos se pueden implementar en el [directorio de implementación rápida](#Fast_Deployment) del dispositivo de destino cuando la propiedad de MSBuild `$(EmbedAssembliesIntoApk)` es `False`. Si un recurso se implementa rápido, *no* se inserta en el archivo `.apk` generado, lo que puede acelerar los tiempos de desarrollo. (Cuanto más rápido se implementa, con menos frecuencia es necesario recompilar el archivo `.apk`, y el proceso de instalación puede ser más rápido). Los valores válidos son:

  - `Assemblies`: implementa los ensamblados de aplicación.

  - `Dexes`: implementa archivos `.dex`, recursos de Android y activos de Android. **Este valor *solo* se puede usar en dispositivos que ejecutan Android 4.4 o posterior (API-19).**

  El valor predeterminado es `Assemblies`.

  **Experimental**. Agregado en Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods**: se trata de una propiedad bool que permite la generación de métodos de serialización JNI como parte del proceso de compilación. Reduce en gran medida el uso de System.Reflection en el código de aplicación auxiliar de enlace.

  De forma predeterminada, se establecerá en False. Si los desarrolladores quieren usar la nueva característica de métodos de serialización JNI, pueden establecer

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  en su csproj. Especifique también la propiedad en la línea de comandos mediante

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **Experimental**. Se ha agregado en Xamarin.Android 9.2.
  El valor predeterminado es False.

- **AndroidGenerateJniMarshalMethodsAdditionalArguments**: propiedad de cadena que se puede usar para agregar parámetros adicionales a la invocación de `jnimarshalmethod-gen.exe`.  Resulta útil para la depuración, por lo que se pueden usar opciones tales como `-v`, `-d` o `--keeptemp`.

  El valor predeterminado es una cadena vacía. Se puede establecer en el archivo csproj o en la línea de comandos. Por ejemplo:

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  O bien

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  Se ha agregado en Xamarin.Android 9.2.

- **AndroidHttpClientHandlerType** &ndash; Controla la implementación de `System.Net.Http.HttpMessageHandler` predeterminada que se va a utilizar mediante el constructor predeterminado `System.Net.Http.HttpClient`. El valor es un nombre calificado con el ensamblado de una subclase `HttpMessageHandler`, adecuada para usarse con [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).
  Los valores más comunes para esta propiedad son los siguientes:

  - `Xamarin.Android.Net.AndroidClientHandler`: Use las API de Java de Android para realizar solicitudes de red. Esto permite tener acceso a las direcciones URL TLS 1.2, cuando la versión de Android subyacente admita dicho protocolo. Solo Android 5.0 y versiones posteriores proporcionan compatibilidad confiable con TLS 1.2 a través de Java.

    Esto corresponde a la opción **Android** de las páginas de propiedades de Visual Studio y la opción **AndroidClientHandler** de las páginas de propiedades de Visual Studio para Mac.

    El asistente para la creación de proyectos selecciona esta opción para los nuevos proyectos cuando la **versión de Android mínima** está configurada en **Android 5.0 (Lollipop)** o versiones posteriores en Visual Studio, o bien cuando **Target Platforms** está establecido en **Latest and Greatest** en Visual Studio para Mac.

  - Anular la cadena vacía: Esto equivale a `System.Net.Http.HttpClientHandler, System.Net.Http`.

    Esto corresponde a la opción **Default** de las páginas de propiedades de Visual Studio.

    El asistente para la creación de proyectos selecciona esta opción para los nuevos proyectos cuando la **versión de Android mínima** está configurada en **Android 4.4.87** o versiones anteriores en Visual Studio, o bien cuando **Target Platforms** está establecido en **Modern Development** o **Maximum Compatibility** en Visual Studio para Mac.

  - `System.Net.Http.HttpClientHandler, System.Net.Http`: Use el `HttpMessageHandler` administrado.

    Esto corresponde a la opción **Managed** de las páginas de propiedades de Visual Studio.

  > [!NOTE]
  > Si la compatibilidad con TLS 1.2 es necesaria en las versiones de Android anteriores a la 5.0, *o* si se requiere con `System.Net.WebClient` y las API relacionadas, se debe usar `$(AndroidTlsProvider)`.

  > [!NOTE]
  > La compatibilidad con esta propiedad funciona estableciendo la [variable de entorno `XA_HTTP_CLIENT_HANDLER_TYPE`](~/android/deploy-test/environment.md).
  > Tendrá prioridad un valor `$XA_HTTP_CLIENT_HANDLER_TYPE` en un archivo con una acción de compilación de `@(AndroidEnvironment)`.

  Agregado en Xamarin.Android 6.1.

- **AndroidLinkMode**: especifica qué tipo de [vinculación](~/android/deploy-test/linker.md) debe realizarse en los ensamblados contenidos dentro del paquete de Android. Solo se usa en proyectos de aplicación de Android. El valor predeterminado es *SdkOnly*. Los valores válidos son:

  - **None**: no se intenta la vinculación.

  - **SdkOnly**: la vinculación se realizará solo en las bibliotecas de clase base, no en los ensamblados del usuario.

  - **Full**: la vinculación se realizará en las bibliotecas de clase base y en los ensamblados del usuario.

    > [!NOTE]
    > El uso de un valor para `AndroidLinkMode` de *Full* da lugar con frecuencia a aplicaciones interrumpidas, en especial cuando se usa la reflexión. Evítelo a menos que sepa *realmente* lo que está haciendo.

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip**: especifica una lista delimitada por punto y coma (`;`) de nombres de ensamblado, sin extensiones de archivos, que no se deben vincular. Solo se usa dentro de proyectos de aplicaciones Android.

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool**: propiedad de estilo de la enumeración con los valores válidos de `proguard` o `r8`. Indica el reductor de código que se usa con el código Java. Actualmente el valor predeterminado es una cadena vacía o `proguard` si `$(AndroidEnableProguard)` es `True`. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled**: propiedad booleana que permite que el desarrollador ejecute la herramienta `lint` de Android como parte del proceso de empaquetado.

  - **AndroidLintEnabledIssues**: lista separada por comas de problemas de Lint que se va a habilitar.

  - **AndroidLintEnabledIssues**: lista separada por comas de problemas de Lint que se va a deshabilitar.

  - **AndroidLintCheckIssues**: lista separada por comas de problemas de Lint que hay que comprobar.
    Nota: Solo se comprobarán estos problemas.

  - **AndroidLintConfig**: se trata de una acción de compilación para un archivo de configuración de tipo lint. Puede usarse para habilitar o deshabilitar la comprobación de problemas. Varios archivos pueden usar esta acción de compilación ya que su contenido se va a combinar.

  Consulte la [Ayuda de Lint](https://developer.android.com/studio/write/lint) para más información sobre las herramientas `lint` de Android.

- **AndroidManagedSymbols**: una propiedad booleana que controla si se generan puntos de secuencia para que la información de nombre de archivo y número de línea se pueda extraer de los seguimientos de pila de `Release`.

  Agregado en Xamarin.Android 6.1.

- **AndroidManifest**: especifica un nombre de archivo para usar como plantilla para el archivo [`AndroidManifest.xml` de la aplicación ](~/android/platform/android-manifest.md).
  Durante la compilación, cualquier otro valor necesario se combina para producir el archivo `AndroidManifest.xml` real.
  `$(AndroidManifest)` debe contener el nombre de paquete en el atributo `/manifest/@package`.

- **AndroidMultiDexClassListExtraArgs**: propiedad de cadena que permite a los desarrolladores pasar argumentos adicionales a `com.android.multidex.MainDexListBuilder` al generar el archivo `multidex.keep`.

  Un caso específico es si se obtiene el siguiente error durante la compilación de `dx`.

  ```
  com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
  ```

  Si recibe este error, puede agregar lo siguiente al elemento .csproj.

  ```xml
  <DxExtraArguments>--force-jumbo </DxExtraArguments>
  <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
  ```

  Esto debe permitir que el paso `dx` se realice correctamente.

  Se ha agregado en Xamarin.Android 8.3.

- **AndroidPackageFormat**: una propiedad de estilo de la enumeración con los valores válidos de `apk` o `aab`. Esto indica si quiere empaquetar la aplicación Android como un [archivo APK][apk] o un [paquete de aplicaciones Android][bundle]. Los grupos de aplicaciones son un nuevo formato para las compilaciones de `Release` que se quieran enviar a Google Play. Actualmente, este valor es `apk` de forma predeterminada.

  Cuando `$(AndroidPackageFormat)` se establece en `aab`, se establecen otras propiedades de MSBuild necesarias para los paquetes de aplicaciones de Android:

  - `$(AndroidUseAapt2)` es `True`.
  - `$(AndroidUseApkSigner)` es `False`.
  - `$(AndroidCreatePackagePerAbi)` es `False`.

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidD8JarPath**: ruta de acceso a `r8.jar` para su uso con el compilador y reductor de dex de d8. El valor predeterminado es una ruta de acceso en la instalación de Xamarin.Android. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

- **AndroidSdkBuildToolsVersion**: el paquete de compilación de herramientas de Android SDK proporciona, entre otras, las herramientas **aapt** y **zipalign**. Se pueden instalar varias versiones diferentes del paquete de herramientas de compilación al mismo tiempo. Para realizar el paquete de herramientas de compilación elegido para empaquetar, se comprueba y usa una versión de herramientas de compilación "preferida", si existe; *en caso contrario*, se usa el paquete de herramientas de compilación instalado con la versión más alta.

  La propiedad de MSBuild `$(AndroidSdkBuildToolsVersion)` contiene la versión de herramientas de compilación preferida. El sistema de compilación de Xamarin.Android proporciona un valor predeterminado en `Xamarin.Android.Common.targets`, y el valor predeterminado se puede reemplazar dentro del archivo de proyecto para elegir una versión alternativa de las herramientas de compilación si, por ejemplo, la herramienta aapt más reciente se bloquea mientras se sabe que una versión anterior de aapt funciona.

- **AndroidSupportedAbis**: una propiedad de cadena que contiene una lista delimitada por punto y coma (`;`) de ABI que se deberían incluir en el archivo `.apk`.

  Los valores admitidos son:

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a`: requiere Xamarin.Android 5.1 y versiones posteriores.
  - `x86_64`: requiere Xamarin.Android 5.1 y versiones posteriores.

- **AndroidTlsProvider**: un valor de cadena que especifica qué proveedor de TLS se debe usar en una aplicación. Los valores posibles son:

  - Anular la cadena vacía: En Xamarin.Android 7.3 y versiones posteriores, esto equivale a `btls`.

    En Xamarin.Android 7.1, esto equivale a `legacy`.

    Esto corresponde a la configuración **Default** de las páginas de propiedades de Visual Studio.

  - `btls`: usa [Boring SSL](https://boringssl.googlesource.com/boringssl) para la comunicación TLS con [HttpWebRequest](xref:System.Net.HttpWebRequest).

    Esto permite el uso de TLS 1.2 en todas las versiones de Android.

    Esto corresponde a la configuración **Native TLS 1.2+** de las páginas de propiedades de Visual Studio.

  - `legacy`: usa la implementación de SSL administrada histórica para la interacción de red. Aquí *no* se admite TLS 1.2.

    Esto corresponde a la configuración **Managed TLS 1.0** de las páginas de propiedades de Visual Studio.

  - `default`: Es poco probable que este valor se use en proyectos de Xamarin.Android. El valor recomendado que se usará en su lugar es la cadena vacía, que corresponde a la configuración **Default** de las páginas de propiedades de Visual Studio.

    El valor `default` no se ofrece en las páginas de propiedades de Visual Studio.

    Actualmente, esto equivale a `legacy`.

  Agregado en Xamarin.Android 7.1.

- **AndroidUseApkSigner**: propiedad booleana que permite que el desarrollador use la herramienta `apksigner` en lugar de `jarsigner`.

    Agregado en Xamarin.Android 8.2.

- **AndroidUseLegacyVersionCode**: una propiedad booleana permite al desarrollador revertir el cálculo de versionCode al comportamiento anterior Xamarin.Android 8.2. SOLO debe usarse para los desarrolladores con aplicaciones en Google Play Store. Se recomienda utilizar la nueva propiedad `$(AndroidVersionCodePattern)`.

  Agregado en Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; Una propiedad booleana que cambiará las compilaciones en tiempo de diseño para usar el analizador de recurso administrado en lugar de `aapt`.

  Agregado en Xamarin.Android 8.1.

- **AndroidUseSharedRuntime**: una propiedad booleana que determina si se requieren los *paquetes de entorno de tiempo de ejecución compartido* para ejecutar la aplicación en el dispositivo de destino. Confiar en los paquetes de entorno de tiempo de ejecución compartido permite que el paquete de aplicación sea más pequeño y acelera el proceso de creación y desarrollo del paquete, lo que da lugar a un ciclo de respuesta de compilación, implementación y depuración más rápido.

  Esta propiedad debe ser `True` para las compilaciones de depuración y `False` para los proyectos de versión.

- **AndroidVersionCodePattern**: una propiedad de cadena que permite al desarrollador personalizar el elemento `versionCode` en el manifiesto.
  Consulte [Crear el código de la versión para el APK](~/android/deploy-test/building-apps/abi-specific-apks.md) para más información sobre cómo decidir el valor de `versionCode`.

  Algunos ejemplos: si `abi` es `armeabi` y `versionCode` en el manifiesto es `123`, `{abi}{versionCode}` producirá un código de versión de `1123` cuando `$(AndroidCreatePackagePerAbi)` sea True; de lo contrario, producirá un valor de 123.
  Si `abi` es `x86_64` y `versionCode` en el manifiesto es `44`, se generará `544` cuando `$(AndroidCreatePackagePerAbi)` sea True; de lo contrario, se producirá un valor de `44`.

  Si se incluye una cadena con formato de relleno a la izquierda, `{abi}{versionCode:0000}`, produciría `50044` porque estamos rellenando a la izquierda `versionCode` con `0`. Como alternativa, puede usar el relleno decimal, como `{abi}{versionCode:D4}`,
  que hace lo mismo que el ejemplo anterior.

  Solo se admiten cadenas con formato de relleno "0" y "Dx", dado que el valor DEBE ser un entero.

  Elementos de clave predefinidos

  - **abi**: inserta la ABI de destino para la aplicación.
    - 2 &ndash; `armeabi-v7a`
    - 3 &ndash; `x86`
    - 4 &ndash; `arm64-v8a`
    - 5 &ndash; `x86_64`

  - **minSDK**: inserta el valor de SDK mínimo admitido del archivo `AndroidManifest.xml` o `11` si no se define ninguno.

  - **versionCode**: usa el código de versión directamente de `Properties\AndroidManifest.xml`.

  Puede definir elementos personalizados con la propiedad `$(AndroidVersionCodeProperties)`, que se define a continuación.

  De forma predeterminada, el valor se establece en `{abi}{versionCode:D6}`. Si un programador quiere mantener el comportamiento anterior, se puede reemplazar el valor predeterminado mediante el establecimiento de la propiedad `$(AndroidUseLegacyVersionCode)` en `true`.

  Agregado en Xamarin.Android 7.2.

- **AndroidVersionCodeProperties**: propiedad de cadena que permite al desarrollador definir elementos personalizados para usar con `AndroidVersionCodePattern`. Tienen el formato de un par `key=value`. Todos los elementos de `value` deben ser valores enteros. Por ejemplo: `screen=23;target=$(_AndroidApiLevel)`. Como puede ver, pueden hacer uso de propiedades de MSBuild existentes o personalizadas en la cadena.

  Agregado en Xamarin.Android 7.2.

- **AotAssemblies**: una propiedad booleana que determina si se los ensamblados se compilarán en modo Ahead-of-Time en código nativo y se incluirán en el archivo `.apk`.

  En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `False`.

- **EmbedAssembliesIntoApk**: una propiedad booleana que determina si los ensamblados de la aplicación se deben insertar o no en el paquete de aplicación.

  Esta propiedad debe ser `True` para compilaciones de versión y `False` para compilaciones de depuración. *Puede* que deba ser `True` en las compilaciones de depuración si la implementación rápida no admite el dispositivo de destino.

  Cuando esta propiedad es `False`, la propiedad de MSBuild `$(AndroidFastDeploymentType)` también controla lo que se insertará en el archivo `.apk`, lo que puede afectar a los tiempos de desarrollo y recompilación.

- **EnableLLVM**: propiedad booleana que determina si se usará o no LLVM al realizar la compilación Ahead Of Time de los ensamblados en código nativo.

  En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `False`.

  Esta propiedad se omite a menos que la propiedad de MSBuild `$(AotAssemblies)` sea `True`.

- **EnableProguard**: una propiedad booleana que determina si se ejecuta o no [proguard](https://developer.android.com/tools/help/proguard.html) como parte del proceso de empaquetado para vincular código Java.

  En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

  De forma predeterminada, esta propiedad es `False`.

  Si es `True`, los archivos de [ProguardConfiguration](#ProguardConfiguration) se usarán para controlar la ejecución de `proguard`.

- **JavaMaximumHeapSize**: especifica el valor del parámetro **java**
  `-Xmx` que se usará al compilar el archivo `.dex` como parte del proceso de empaquetado. Si no se especifica, la opción `-Xmx` suministra **java** con un valor de `1G`. Se ha detectado que esto suele ser necesario en Windows en comparación con otras plataformas.

  Es necesario especificar esta propiedad si el destino [`_CompileDex` produce un error `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

  Personalice el valor cambiando lo siguiente:

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions**: especifica opciones de línea de comando adicionales que se pasarán a **java** al compilar el archivo `.dex`.

- **LinkerDumpDependencies**: propiedad bool que permite la generación del archivo de dependencias del enlazador. Este archivo se puede usar como entrada para la herramienta [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

  El valor predeterminado es False.

- **MandroidI18n**: especifica la compatibilidad con la internacionalización que se incluye con la aplicación, como tablas de intercalación y ordenación. El valor es una lista separada por comas o punto y coma de uno o varios de los siguientes valores sin distinción entre mayúsculas y minúsculas:

  - **None**: no incluye codificaciones adicionales.

  - **All**: incluye todas las codificaciones disponibles.

  - **CJK**: incluye las codificaciones china, japonesa y coreana, como *Japonés (EUC)* \[enc-jp, CP51932\], *Japonés (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *Japonés (JIS)* \[CP50220\], *Chino simplificado (GB2312)* \[gb2312, CP936\], *Coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *Coreano (EUC)* \[euc-kr, CP51949\], *Chino tradicional (Big5)* \[big5, CP950\] y *Chino simplificado (GB18030)* \[GB18030, CP54936\].

  - **MidEast**: incluye las codificaciones de Oriente Medio, como *Turco (Windows)* \[iso-8859-9, CP1254\], *Hebreo (Windows)* \[windows-1255, CP1255\], *Árabe (Windows)* \[windows-1256, CP1256\], *Árabe (ISO)* \[iso-8859-6, CP28596\], *Hebreo (ISO)* \[iso-8859-8, CP28598\], *Latín 5 (ISO)* \[iso-8859-9, CP28599\] y *Hebreo (alternativa a Iso)* \[iso-8859-8, CP38598\].

  - **Other**: incluye otras codificaciones, como *Cirílico(Windows)* \[CP1251\], *Báltico (Windows)* \[iso-8859-4, CP1257\], *Vietnamita (Windows)* \[CP1258\], *Cirílico (KOI8-R)* \[koi8-r, CP1251\], *Ucraniano (KOI8-U)* \[koi8-u, CP1251\], *Báltico (ISO)* \[iso-8859-4, CP1257\], *Cirílico (ISO)* \[iso-8859-5, CP1251\], *ISCII Davenagari*\[x-iscii-de, CP57002\], *ISCII Bengalí*\[x-iscii-be, CP57003\], *ISCII Tamil*\[x-iscii-ta, CP57004\], *ISCII Telugu*\[x-iscii-te, CP57005\], *ISCII Asamés*\[x-iscii-as, CP57006\], *ISCII Oriya*\[x-iscii-or, CP57007\], *ISCII Canarés*\[x-iscii-ka, CP57008\], *ISCII Malayalam*\[x-iscii-ma, CP57009\], *ISCII Gujarati*\[x-iscii-gu, CP57010\], *ISCII Punjabi*\[x-iscii-pa, CP57011\] y *Thai (Windows)* \[CP874\].

  - **Rare**: incluye las codificaciones raras, como *IBM EBCDIC (Turco)* \[CP1026\], *IBM EBCDIC (Latín 1, sistemas abiertos)* \[CP1047\], *IBM EBCDIC (EE. UU. y Canadá con Euro)* \[CP1140\], *IBM EBCDIC (Alemania con Euro)* \[CP1141\], *IBM EBCDIC (Dinamarca/Noruega con Euro)* \[CP1142\], *IBM EBCDIC (Finlandia/Suecia con Euro)* \[CP1143\], *IBM EBCDIC (Italia con Euro)* \[CP1144\], *IBM EBCDIC (Latinoamérica/España con Euro)* \[CP1145\], *IBM EBCDIC (Reino Unido con Euro)* \[CP1146\], *IBM EBCDIC (Francia con Euro)* \[CP1147\], *IBM EBCDIC (Internacional con Euro)* \[CP1148\], *IBM EBCDIC (Islandés con Euro)* \[CP1149\], *IBM EBCDIC (Alemania)* \[CP20273\], *IBM EBCDIC (Dinamarca/Noruega)* \[CP20277\], *IBM EBCDIC (Finlandia/Suecia)* \[CP20278\], *IBM EBCDIC (Italia)* \[CP20280\], *IBM EBCDIC (Latinoamérica/España)* \[CP20284\], *IBM EBCDIC (Reino Unido)* \[CP20285\], *IBM EBCDIC (Katakana japonés extendido)* \[CP20290\], *IBM EBCDIC (Francia)* \[CP20297\], *IBM EBCDIC (Árabe)* \[CP20420\], *IBM EBCDIC (Hebreo)* \[CP20424\], *IBM EBCDIC (Islandés)* \[CP20871\], *IBM EBCDIC (Cirílico: serbio, búlgaro)* \[CP21025\], *IBM EBCDIC (EE. UU. y Canadá)* \[CP37\], *IBM EBCDIC (Internacional)* \[CP500\], *Árabe (ASMO 708)* \[CP708\], *Centroeuropeo (DOS)* \[CP852\] *, Cirílico (DOS)* \[CP855\], *Turco (DOS)* \[CP857\], *Europeo Occidental (DOS con Euro)* \[CP858\], *Hebreo (DOS)* \[CP862\], *Árabe (DOS)* \[CP864\], *Ruso (DOS)* \[CP866\], *Griego (DOS)* \[CP869\], *IBM EBCDIC (Latín 2)* \[CP870\] y *IBM EBCDIC (Griego)* \[CP875\].

  - **West**: incluye las codificaciones occidentales, como *Europeo Occidental (Mac)* \[macintosh, CP10000\], *Islandés (Mac)* \[x-mac-icelandic, CP10079\], *Centroeuropeo (Windows)* \[iso-8859-2, CP1250\], *Europeo Occidental (Windows)* \[iso-8859-1, CP1252\], *Griego (Windows)* \[iso-8859-7, CP1253\], *Centroeuropeo (ISO)* \[iso-8859-2, CP28592\], *Latín 3 (ISO)* \[iso-8859-3, CP28593\], *Griego (ISO)* \[iso-8859-7, CP28597\], *Latín 9 (ISO)* \[iso-8859-15, CP28605\], *Estados Unidos OEM* \[CP437\], *Europeo Occidental (DOS)* \[CP850\], *Portugués (DOS)* \[CP860\], *Islandés (DOS)* \[CP861\], *Francés canadiense (DOS)* \[CP863\] y *Nórdico (DOS)* \[CP865\].

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive**: una propiedad booleana que controla si los artefactos de `.mSYM` para usarlos más adelante con `mono-symbolicate`, a fin de extraer información &ldquo;real&rdquo; de nombre de archivo y número de línea de los seguimientos de pila de versión.

  Este valor es true de forma predeterminada para aplicaciones de &ldquo;versión&rdquo; que tienen habilitados símbolos de depuración: `$(EmbedAssembliesIntoApk)` es true, `$(DebugSymbols)` es true y `$(Optimize)` es true.

  Agregado en Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Propiedades de compilación de proyectos de enlace

Las siguientes propiedades de MSBuild se usan con [proyectos de enlace](~/android/platform/binding-java-library/index.md):

- **AndroidClassParser**: una propiedad de cadena que controla cómo se analizan los archivos `.jar`. Entre los posibles valores se incluyen:

  - **class-parse**: usa `class-parse.exe` para analizar el código de bytes de Java directamente, sin ayuda de una JVM. Este valor es experimental.

  - **jar2xml**: usa `jar2xml.jar` para utilizar la reflexión de Java y extraer tipos y miembros de un archivo `.jar`.

  Las ventajas de `class-parse` sobre `jar2xml` son las siguientes:

  - `class-parse` puede extraer nombres de parámetro del código de bytes de Java que contiene símbolos de *depuración* (por ejemplo, código de bytes compilado con `javac -g`).

  - `class-parse` no "omite" las clases que se heredan de miembros de tipos que no se pueden resolver o que los contienen.

  **Experimental**. Agregado en Xamarin.Android 6.0.

  El valor predeterminado es `jar2xml`.

  El valor predeterminado cambiará en futuras versiones.

- **AndroidCodegenTarget**: una propiedad de cadena que controla la ABI de destino de generación de código. Entre los posibles valores se incluyen:

  - **XamarinAndroid**: usa la API de enlace de JNI presente desde Mono para Android 1.0. Los ensamblados de enlace compilados con Xamarin.Android 5.0 o posterior solo se pueden ejecutar en Xamarin.Android 5.0 o posterior (adiciones de API/ABI), pero el *origen* es compatible con versiones de producto anteriores.

  - **XAJavaInterop1**: usa Java.Interop de uso para las invocaciones de JNI. Los ensamblados de enlace que usan `XAJavaInterop1` solo se pueden compilar y ejecutar con Xamarin.Android 6.1 o posterior. Xamarin.Android 6.1 y versiones posteriores enlazan `Mono.Android.dll` con este valor.

    Las ventajas de `XAJavaInterop1` son las siguientes:

    - Ensamblados más pequeños.

    - Almacenamiento en caché de `jmethodID` para invocaciones de método `base`, mientras que todos los demás enlaces de la jerarquía de herencia se compilan con `XAJavaInterop1` o versiones posteriores.

    - Almacenamiento en caché de `jmethodID` de los constructores de JCW (Java Callable Wrapper) para subclases administradas.

    El valor predeterminado es `XAJavaInterop1`.

### <a name="resource-properties"></a>Propiedades del recurso

Las propiedades del recurso controlan la generación del archivo `Resource.designer.cs`, que proporciona acceso a recursos de Android.

- **AndroidAapt2CompileExtraArgs**: especifica opciones adicionales de la línea de comandos que se pasan al comando **aapt2 compile** al procesar recursos de Android.

  Se ha agregado en Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs**: especifica opciones adicionales de la línea de comandos que se pasan al comando **aapt2 link** al procesar recursos de Android.

  Se ha agregado en Xamarin.Android 9.1.

- **AndroidExplicitCrunch**: si va a compilar una aplicación con un gran número de recursos Drawable locales, una compilación inicial (o recompilación) puede tardar minutos en completarse. Para acelerar el proceso de compilación, intente incluir esta propiedad y establecerla en `True`. Cuando se establece esta propiedad, el proceso de compilación analiza previamente los archivos .png.

  Nota: Esta opción no es compatible con la opción `$(AndroidUseAapt2)`. Si `$(AndroidUseAapt2)` está habilitada, se deshabilitará esta funcionalidad. Si quiere seguir usando esta característica, establezca `$(AndroidUseAapt2)` en `False`.

  **Experimental**. Agregado en Xamarin.Android 7.0.

- **AndroidResgenExtraArgs**: especifica opciones adicionales de la línea de comandos para pasar al comando **aapt** al procesar activos y recursos de Android.

- **AndroidResgenFile**: especifica el nombre del archivo de recursos que se generará. La plantilla predeterminada establece esta propiedad en `Resource.designer.cs`.

- **AndroidUseAapt2**: propiedad bool que permite al desarrollador controlar el uso de la herramienta `aapt2` para el empaquetado.
  De forma predeterminada, este valor se establecerá en False y usaremos `aapt`.
  Si el desarrollador quiere usar la nueva funcionalidad de `aapt2` puede establecerlo

  ```xml
  <AndroidUseAapt2>True</AndroidUseAapt2>
  ```

  en su csproj. Especifique también la propiedad en la línea de comandos mediante

  ```
  /p:AndroidUseAapt2=True
  ```

  Se ha agregado en Xamarin.Android 8.3.

- **MonoAndroidResourcePrefix**: especifica un *prefijo de ruta de acceso* que se quita del principio de los nombres de archivo con una acción de compilación de `AndroidResource`. Esto permite cambiar la ubicación de los recursos.

  El valor predeterminado es `Resources`. Cambie este valor a `res` con estructuras de proyectos de Java.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>Propiedades de firma

Las propiedades de firma controlan cómo se firma el paquete de aplicación para que se pueda instalar en un dispositivo Android. Para permitir una iteración de compilación más rápida, las tareas de Xamarin.Android no firman paquetes durante el proceso de compilación, porque es un proceso bastante lento. En su lugar, se firman (si es necesario) antes de la instalación o durante la exportación, por medio del IDE o del destino de compilación *Install*. Al invocar el destino *SignAndroidPackage* se produce un paquete con el sufijo `-Signed.apk` en el directorio de salida.

De forma predeterminada, el destino de firma genera una nueva clave de firma de depuración, si es necesario. Si desea usar una clave específica, por ejemplo, en un servidor de compilación, se pueden usar las siguientes propiedades de MSBuild:

- **AndroidDebugKeyAlgorithm** &ndash; Especifica el algoritmo predeterminado que se usará para `debug.keystore`. El valor predeterminado es `RSA`.

- **AndroidDebugKeyValidity** &ndash; Especifica la validez predeterminada para usarla con `debug.keystore`. El valor predeterminado es `10950`, `30 * 365` o `30 years`.

- **AndroidKeyStore**: un valor booleano que indica si se debe usar información de firma personalizada. El valor predeterminado es `False`, lo que significa que se usará la clave de firma de depuración predeterminada para firmar paquetes.

- **AndroidSigningKeyAlias**: especifica el alias de la clave en el almacén de claves. Es el valor de **keytool -alias** usado al crear el almacén de claves.

- **AndroidSigningKeyPass**: especifica la contraseña de la clave en el archivo de almacén de claves. Este es el valor especificado cuando `keytool` pide que se **escriba la contraseña de clave para $(AndroidSigningKeyAlias)** .

- **AndroidSigningKeyStore**: especifica el nombre de archivo del archivo de almacén de claves creado por `keytool`. Corresponde al valor proporcionado a la opción **keytool - keystore**.

- **AndroidSigningStorePass**: especifica la contraseña de `$(AndroidSigningKeyStore)`. Es el valor proporcionado a `keytool` al crear el archivo de almacén de claves y cuando se pide que se **escriba la contraseña del almacén de claves**.

Por ejemplo, considere la siguiente invocación de `keytool`:

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

Para usar el almacén de claves generado anteriormente, use el grupo de propiedades:

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

<a name="Build_Actions" />

## <a name="build-actions"></a>Acciones de compilación

Las *acciones de compilación* se [aplican a archivos](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items) dentro del proyecto y controlan cómo se procesa el archivo.

### <a name="androidaarlibrary"></a>AndroidAarLibrary

La acción de compilación de `AndroidAarLibrary` se debe utilizar para hacer referencia directamente a los archivos .aar. Esta acción de compilación la utilizan normalmente componentes de Xamarin. Es decir, para incluir referencias a archivos .aar que son necesarios para que funcionen Google Play y otros servicios.

Los archivos con esta acción de compilación se tratarán de un modo bastante similar a los recursos incrustados en proyectos de biblioteca. El archivo .aar se extrae en el directorio intermedio. Después, los recursos y los archivos .jar se incluirán en los grupos de elementos pertinentes.

### <a name="androidboundlayout"></a>AndroidBoundLayout

Indica que el archivo de diseño debe tener código subyacente generado en caso de que la propiedad `AndroidGenerateLayoutBindings` se establezca en `false`. En todos los demás aspectos es idéntica a `AndroidResource`, descrito anteriormente. Esta acción **solo** se puede usar con archivos de diseño:

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

Los archivos con una acción de compilación de `AndroidEnvironment` se usan para [inicializar variables de entorno y propiedades del sistema durante el inicio del proceso](~/android/deploy-test/environment.md).
La acción de compilación `AndroidEnvironment` se puede aplicar a varios archivos, y estos se evalúan sin ningún orden determinado (así que no especifique la misma variable de entorno o propiedad de sistema en varios archivos).

### <a name="androidfragmenttype"></a>AndroidFragmentType

Especifica el tipo completo predeterminado que se usará para todos los elementos de diseño de `<fragment>` al generar el código de los enlaces de diseño. El valor predeterminado de la propiedad es el tipo estándar `Android.App.Fragment` de Android.

### <a name="androidjavalibrary"></a>AndroidJavaLibrary

Los archivos con una acción de compilación de `AndroidJavaLibrary` son archivos de Java (archivos `.jar`) que se incluirán en el paquete final de Android.

### <a name="androidjavasource"></a>AndroidJavaSource

Los archivos con una acción de compilación de `AndroidJavaSource` son el código fuente de Java que se incluirá en el paquete final de Android.

### <a name="androidlintconfig"></a>AndroidLintConfig

La acción de compilación "AndroidLintConfig" se debe usar con la propiedad de compilación `AndroidLintEnabled`. Los archivos con esta acción de compilación se combinan y se pasan a las herramientas `lint` de Android. Deben ser archivos XML que contengan información sobre qué pruebas se habilitan o deshabilitan.

Vea la [documentación de lint](https://developer.android.com/studio/write/lint) para obtener más información.

### <a name="androidnativelibrary"></a>AndroidNativeLibrary

Las [bibliotecas nativas](~/android/platform/native-libraries.md) se agregan a la compilación mediante el establecimiento de su acción de compilación en `AndroidNativeLibrary`.

Tenga en cuenta que puesto que Android admite varias interfaces binarias de aplicación (ABI), el sistema de compilación debe saber para qué ABI está compilada la biblioteca nativa. Hay dos maneras de hacerlo:

1. Examinando la ruta de acceso.
2. Mediante el atributo de elemento `Abi`.

Con el examen de la ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI a la que se dirige la biblioteca. Por lo tanto, si agrega `lib/armeabi-v7a/libfoo.so` a la compilación, la ABI se examina como `armeabi-v7a`.

#### <a name="item-attribute-name"></a>Nombre del atributo de elemento

**Abi**: especifica la ABI de la biblioteca nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

### <a name="androidresource"></a>AndroidResource

Todos los archivos con una acción de compilación de *AndroidResource* se compilan en recursos de Android durante el proceso de compilación y se vuelven accesibles mediante `$(AndroidResgenFile)`.

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

Quizás los usuarios más avanzados deseen que recursos diferentes se usen en distintas configuraciones, pero con la misma ruta de acceso efectiva. Para lograr esto, se pueden tener varios directorios de recursos y tener archivos con las mismas rutas de acceso relativas dentro de estos diferentes directorios, y usar condiciones de MSBuild para incluir condicionalmente diferentes archivos en diferentes configuraciones. Por ejemplo:

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName**: especifica la ruta de acceso a los recursos de manera explícita. Permite la creación de &ldquo;alias&rdquo; de archivos para que estén disponibles con varios nombres de recursos distintos.

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

### <a name="content"></a>Contenido

La acción de compilación `Content` no se admite (dado que no hemos descubierto cómo admitirla sin un paso seguramente costoso de primera ejecución).

A partir de Xamarin.Android 5.1, si se intenta usar la acción de compilación `@(Content)` se producirá una advertencia `XA0101`.

### <a name="linkdescription"></a>LinkDescription

Los archivos con una acción de compilación *LinkDescription* se usan para [controlan el comportamiento del enlazador](~/cross-platform/deploy-test/linker.md).

<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

Los archivos con una acción de compilación *ProguardConfiguration* contienen opciones que se usan para controlar el comportamiento de `proguard`. Para más información sobre esta acción de compilación, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Estos archivos se omiten a menos que la propiedad de MSBuild `$(EnableProguard)` sea `True`.

## <a name="target-definitions"></a>Definiciones de destino

Las partes del proceso de compilación específicas de Xamarin.Android se definen en `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`, pero también se requieren los destinos normales específicos del lenguaje, como *Microsoft.CSharp.targets*, para compilar el ensamblado.

Las siguientes propiedades de compilación deben establecerse antes de importar los destinos de lenguaje:

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

Todos estos destinos y propiedades se pueden incluir para C# mediante la importación de *Xamarin.Android.CSharp.targets*:

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

Este archivo se puede adaptar fácilmente para otros lenguajes.
