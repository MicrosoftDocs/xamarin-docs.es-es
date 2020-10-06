---
title: Propiedades de compilación
description: En este documento se muestran todas las propiedades admitidas en el proceso de compilación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: FC0DBC08-EBCB-4D2D-AB3F-76B54E635C22
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/21/2020
ms.openlocfilehash: aeb0cca9ead1a0f0a3f5b1dec88b2470289cd589
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454941"
---
# <a name="build-properties"></a>Propiedades de compilación

Las propiedades de MSBuild controlan el comportamiento de los [destinos](~/android/deploy-test/building-apps/build-targets.md).
Se especifican en el archivo del proyecto, por ejemplo, **MyApp.csproj**, dentro de un elemento [MSBuild PropertyGroup](/visualstudio/msbuild/propertygroup-element-msbuild).

## <a name="adbtarget"></a>AdbTarget

La propiedad `$(AdbTarget)` especifica el dispositivo Android de destino en el que se puede instalar el paquete de Android o desde el que se puede quitar.
El valor de esta propiedad es el mismo que el de la [opción del dispositivo de destino `adb`](https://developer.android.com/tools/help/adb.html#issuingcommands).

## <a name="aftergenerateandroidmanifest"></a>AfterGenerateAndroidManifest

Destinos de MSBuild enumerados en esta propiedad se ejecutarán directamente después del destino de `_GenerateJavaStubs` interno, que es donde se generará el archivo `AndroidManifest.xml` en `$(IntermediateOutputPath)`. Si quiere realizar modificaciones en el archivo `AndroidManifest.xml` generado, puede hacerlo mediante este punto de extensión.

Se ha agregado en Xamarin.Android 9.4.

## <a name="androidaapt2compileextraargs"></a>AndroidAapt2CompileExtraArgs

Especifica opciones adicionales de la línea de comandos que se pasan al comando **aapt2 compile** al procesar recursos de Android.

Se ha agregado en Xamarin.Android 9.1.

## <a name="androidaapt2linkextraargs"></a>AndroidAapt2LinkExtraArgs

Especifica opciones adicionales de la línea de comandos que se pasan al comando **aapt2 link** al procesar recursos de Android.

Se ha agregado en Xamarin.Android 9.1.

## <a name="androidaotcustomprofilepath"></a>AndroidAotCustomProfilePath

Archivo que `aprofutil` debe crear para contener los datos del generador de perfiles.

## <a name="androidaotprofiles"></a>AndroidAotProfiles

Propiedad de cadena que permite al desarrollador agregar perfiles de AOT desde la línea de comandos. Es una lista de rutas de acceso absolutas separada por signos de coma o punto y coma.
Se ha agregado en Xamarin.Android 10.1.

## <a name="androidaotprofilerport"></a>AndroidAotProfilerPort

Puerto al que `aprofutil` debe conectarse al obtener datos de generación de perfiles.

## <a name="androidapkdigestalgorithm"></a>AndroidApkDigestAlgorithm

Valor de cadena que especifica el algoritmo de síntesis del mensaje que se usará con `jarsigner -digestalg`.

El valor predeterminado es `SHA-256`. En Xamarin.Android 10.0 y versiones anteriores, el valor predeterminado era `SHA1`.

Se ha agregado en Xamarin.Android 9.4.

## <a name="androidapksigneradditionalarguments"></a>AndroidApkSignerAdditionalArguments

Propiedad de cadena que permite al desarrollador proporcionar argumentos adicionales a la herramienta `apksigner`.

Agregado en Xamarin.Android 8.2.

## <a name="androidapksigningalgorithm"></a>AndroidApkSigningAlgorithm

Valor de cadena que especifica el algoritmo de firma que se usará con `jarsigner -sigalg`.

El valor predeterminado es `SHA256withRSA`. En Xamarin.Android 10.0 y versiones anteriores, el valor predeterminado era `md5withRSA`.

Agregado en Xamarin.Android 8.2.

## <a name="androidapplication"></a>AndroidApplication

Valor booleano que indica si el proyecto es para una aplicación Android (`True`) o para un proyecto de la biblioteca de Android (`False` o no existe).

Solo puede existir un proyecto con `<AndroidApplication>True</AndroidApplication>` en un paquete de Android. (Por desgracia, esto no se ha comprobado aún, lo que puede dar lugar a errores sutiles y extraños respecto a los recursos de Android).

## <a name="androidapplicationjavaclass"></a>AndroidApplicationJavaClass

Nombre completo de clase de Java que se va a usar en lugar de `android.app.Application` cuando una clase hereda de [Android.App.Application](xref:Android.App.Application).

Esta propiedad se establece generalmente mediante *otras* propiedades, como la propiedad de MSBuild `$(AndroidEnableMultiDex)`.

Agregado en Xamarin.Android 6.1.

## <a name="androidbinutilspath"></a>AndroidBinUtilsPath

Ruta de acceso a un directorio que contiene [binutils][binutils] de Android como `ld`, el enlazador nativo y `as`, el ensamblador nativo. Estas herramientas forman parte del NDK de Android y también se incluyen en la instalación de Xamarin.Android.

El valor predeterminado es `$(MonoAndroidBinDirectory)\ndk\`.

Se ha agregado en Xamarin.Android 10.0.

[binutils]: https://android.googlesource.com/toolchain/binutils/

## <a name="androidboundexceptiontype"></a>AndroidBoundExceptionType

Valor de cadena que especifica cómo se deben propagar las excepciones cuando un tipo proporcionado por Xamarin.Android implementa un tipo o una interfaz de .NET en términos de tipos Java, por ejemplo `Android.Runtime.InputStreamInvoker` y `System.IO.Stream`, o `Android.Runtime.JavaDictionary` y `System.Collections.IDictionary`.

- `Java`: El tipo de excepción de Java original se propaga tal cual.

  Esto significa que, por ejemplo, `InputStreamInvoker` no implementa correctamente la API de `System.IO.Stream` porque `Java.IO.IOException` se puede iniciar desde `Stream.Read()` en lugar de `System.IO.IOException`.

  Este es el comportamiento de propagación de excepciones en todas las versiones de Xamarin.Android anteriores a 11.1.

  Este es el valor predeterminado en Xamarin.Android 11.1.

- `System`: El tipo de excepción de Java original se detecta y se encapsula en un tipo de excepción de .NET adecuado.

  Esto significa que, por ejemplo, `InputStreamInvoker` implementa correctamente `System.IO.Stream` y `Stream.Read()` *no* iniciará instancias de `Java.IO.IOException`.  (En su lugar, puede iniciar un objeto `System.IO.IOException` que tenga `Java.IO.IOException` como el valor de `Exception.InnerException`).

  Se convertirá en el valor predeterminado en .NET 6.0.

Se ha agregado en Xamarin.Android 10.2.

## <a name="androidbuildapplicationpackage"></a>AndroidBuildApplicationPackage

Valor booleano que indica si crear y firmar el paquete (.apk). Establecer este valor en `True` es equivalente a usar [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#install)
como destino de compilación.

Después de Xamarin.Android 7.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `False`.

## <a name="androidbundleconfigurationfile"></a>AndroidBundleConfigurationFile

Especifica un nombre de archivo que se va a usar como [archivo de configuración][bundle-config-format] para `bundletool` al compilar un lote de aplicaciones de Android. Este archivo controla algunos aspectos de cómo se generan APK desde el lote, por ejemplo, en qué dimensiones se divide el lote para generar APK. Tenga en cuenta que Xamarin.Android configura algunos de estos valores automáticamente, incluida la lista de extensiones de archivo que se deben dejar sin comprimir.

Esta propiedad solo es pertinente si [`$(AndroidPackageFormat)`](#androidpackageformat) está establecido en `aab`.

Se ha agregado en Xamarin.Android 10.3.

[bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

## <a name="androidclassparser"></a>AndroidClassParser

Propiedad de cadena que controla cómo se analizan los archivos `.jar`. Los valores posibles son:

- **class-parse**: usa `class-parse.exe` para analizar el código de bytes directamente, sin ayuda de una JVM. Este valor es experimental.

- **jar2xml**: usa `jar2xml.jar` para utilizar la reflexión de Java y extraer tipos y miembros de un archivo `.jar`.

Las ventajas de `class-parse` sobre `jar2xml` son las siguientes:

- `class-parse` puede extraer nombres de parámetro del código de bytes de Java que contiene símbolos de *depuración* (código de bytes compilado con `javac -g`).

- `class-parse` no "omite" las clases que se heredan de miembros de tipos que no se pueden resolver o que los contienen.

**Experimental**. Agregado en Xamarin.Android 6.0.

El valor predeterminado es `jar2xml`.

La compatibilidad con `jar2xml` está obsoleta y se quitará la compatibilidad con `jar2xml` como parte de .NET 6.

## <a name="androidcodegentarget"></a>AndroidCodegenTarget

Propiedad de cadena que controla la ABI de destino de generación de código.
Los valores posibles son:

- **XamarinAndroid**: usa la API de enlace de JNI presente desde Mono para Android 1.0. Los ensamblados de enlace compilados con Xamarin.Android 5.0 o posterior solo se pueden ejecutar en Xamarin.Android 5.0 o posterior (adiciones de API/ABI), pero el *origen* es compatible con versiones de producto anteriores.

- **XAJavaInterop1**: usa Java.Interop de uso para las invocaciones de JNI. Los ensamblados de enlace que usan `XAJavaInterop1` solo se pueden compilar y ejecutar con Xamarin.Android 6.1 o posterior. Xamarin.Android 6.1 y versiones posteriores enlazan `Mono.Android.dll` con este valor.

Las ventajas de `XAJavaInterop1` son las siguientes:

- Ensamblados más pequeños.

- Almacenamiento en caché de `jmethodID` para invocaciones de método `base`, mientras que todos los demás enlaces de la jerarquía de herencia se compilan con `XAJavaInterop1` o versiones posteriores.

- Almacenamiento en caché de `jmethodID` de los constructores de JCW (Java Callable Wrapper) para subclases administradas.

El valor predeterminado es `XAJavaInterop1`.

## <a name="androidcreatepackageperabi"></a>AndroidCreatePackagePerAbi

Una propiedad booleana que determina si se debe crear un *conjunto* de archivos, por ABI especificado en [`$(AndroidSupportedAbis)`](#androidsupportedabis), en lugar de tener compatibilidad con todos los ABI de un solo archivo `.apk`.

Vea también la guía [Compilar APK específicos de ABI](~/android/deploy-test/building-apps/abi-specific-apks.md).

## <a name="androiddebugkeyalgorithm"></a>AndroidDebugKeyAlgorithm

Especifica el algoritmo predeterminado que se usará para `debug.keystore`. El valor predeterminado es `RSA`.

## <a name="androiddebugkeyvalidity"></a>AndroidDebugKeyValidity

Especifica la validez predeterminada que se usará para `debug.keystore`. El valor predeterminado es `10950`, `30 * 365` o `30 years`.

## <a name="androiddebugstoretype"></a>AndroidDebugStoreType

Especifica el formato de archivo de almacén de claves que se va a usar para `debug.keystore`. El valor predeterminado es `pkcs12`.

Se ha agregado en Xamarin.Android 10.2.

## <a name="androiddextool"></a>AndroidDexTool

Propiedad de estilo de la enumeración con los valores válidos de `dx` o `d8`. Indica el compilador de [dex][dex] que se usa durante el proceso de compilación de Xamarin.Android.
Actualmente tiene como valor predeterminado `dx`. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

[dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidenabledesugar"></a>AndroidEnableDesugar

Propiedad booleana que determina si `desugar` está habilitado. Android no admite actualmente todas las características de Java 8 y la cadena de herramientas predeterminada implementa las nuevas características de lenguaje ejecutando transformaciones de código de bytes, llamadas `desugar`, en la salida del compilador `javac`. El valor predeterminado es `False` si se usa `AndroidDexTool=dx` y `True` si se usa [`$(AndroidDexTool)`](#androiddextool)=`d8`.

## <a name="androidenablegoogleplaystorechecks"></a>AndroidEnableGooglePlayStoreChecks

Propiedad booleana que permite a los desarrolladores deshabilitar las siguientes comprobaciones de Google Play Store: XA1004, XA1005 y XA1006. Esto resulta útil para los desarrolladores que no tienen como destino Google Play Store y que no quieren ejecutar las comprobaciones mencionadas.

Se ha agregado en Xamarin.Android 9.4.

## <a name="androidenablemultidex"></a>AndroidEnableMultiDex

Propiedad booleana que determina si se usará la compatibilidad con Multi-Dex en el archivo `.apk` final.

En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `False`.

## <a name="androidenablepreloadassemblies"></a>AndroidEnablePreloadAssemblies

Propiedad booleana que controla si todos los ensamblados administrados que se incluyen en el paquete de aplicación se cargan o no durante el inicio del proceso.

Cuando se establece en `True`, todos los ensamblados que se incluyen en el paquete de aplicación se cargan durante el inicio del proceso, antes de que se invoque cualquier código de aplicación.
Esta acción es coherente con lo que hacía Xamarin.Android en las versiones anteriores a Xamarin.Android 9.2.

Cuando se establece en `False`, los ensamblados solo se cargan cuando es necesario.
De esta forna, las aplicaciones se inician más rápido y también hay una mayor coherencia con la semántica de .NET de escritorio.  Para ver los ahorros de tiempo, establezca la propiedad del sistema `debug.mono.log` en `timing` y busque el mensaje `Finished loading assemblies: preloaded` en `adb logcat`.

Puede que las aplicaciones o bibliotecas que usan inserción de dependencias *necesiten* que esta propiedad se establezca en `True` si estas requieren a su vez que `AppDomain.CurrentDomain.GetAssemblies()` devuelva todos los ensamblados del conjunto de aplicación, incluso si el ensamblado no hubiera sido necesario.

De forma predeterminada, este valor se establecerá en `True`.

Se ha agregado en Xamarin.Android 9.2.

## <a name="androidenableprofiledaot"></a>AndroidEnableProfiledAot

Propiedad booleana que determina si los perfiles de AOT se usarán o no durante la compilación Ahead Of Time.

Los perfiles se muestran en el grupo de elementos [`@(AndroidAotProfile)`](~/android/deploy-test/building-apps/build-items.md#androidaotprofile).
Grupo de elementos. Este ItemGroup contiene perfiles predeterminados. Se puede reemplazar quitando los perfiles de AOT existentes y agregando los suyos.

La compatibilidad con esta propiedad se ha agregado en Xamarin.Android 9.4.

De forma predeterminada, esta propiedad es `False`.

## <a name="androidenablesgenconcurrent"></a>AndroidEnableSGenConcurrent

Propiedad booleana que determina si se usará el [recolector de GC simultáneo](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) de Mono.

Se agregó compatibilidad con esta propiedad en Xamarin.Android 7.2.

De forma predeterminada, esta propiedad es `False`.

## <a name="androiderroroncustomjavaobject"></a>AndroidErrorOnCustomJavaObject

Propiedad booleana que determina si los tipos pueden implementar `Android.Runtime.IJavaObject`
*sin* heredarlo también de `Java.Lang.Object` o `Java.Lang.Throwable`:

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

Cuando es True, estos tipos generarán un error XA4212; en caso contrario, una advertencia XA4212.

En Xamarin.Android 8.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `True`.

## <a name="androidexplicitcrunch"></a>AndroidExplicitCrunch

Ya no se admite en Xamarin.Android 11.0.

## <a name="androidextraaotoptions"></a>AndroidExtraAotOptions

Propiedad de cadena que permite pasar opciones adicionales al compilador Mono durante la tarea `Aot` para los proyectos que tienen [`$(AndroidEnableProfiledAot)`](#androidenableprofiledaot) o [`$(AotAssemblies)`](#aotassemblies) establecidos en `true`.
El valor de cadena de la propiedad se agrega al archivo de respuesta cuando se llama al compilador cruzado Mono.

En general, esta propiedad debe dejarse en blanco, pero en ciertos escenarios especiales podría proporcionar una flexibilidad útil.

Tenga en cuenta que esta propiedad es diferente de la propiedad `$(AndroidAotAdditionalArguments)` relacionada. Esa propiedad coloca argumentos separados por comas en la opción `--aot` del compilador Mono. `$(AndroidExtraAotOptions)` pasa en su lugar opciones completas separadas por espacios independientes como `--verbose` o `--debug` al compilador.

Se ha agregado en Xamarin.Android 10.2.

## <a name="androidfastdeploymenttype"></a>AndroidFastDeploymentType

Lista de valores separada por `:` (dos puntos) para controlar qué tipos se pueden implementar en el [directorio de implementación rápida](~/android/deploy-test/building-apps/build-process.md#Fast_Deployment) del dispositivo de destino cuando la propiedad de MSBuild [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) es `False`. Si un recurso se implementa rápido, *no* se inserta en el archivo `.apk` generado, lo que puede acelerar los tiempos de desarrollo. (Cuanto más rápido se implementa, con menos frecuencia es necesario recompilar el archivo `.apk`, y el proceso de instalación puede ser más rápido). Los valores válidos son:

- `Assemblies`: implementa los ensamblados de aplicación.

- `Dexes`: implementa archivos `.dex`, recursos de Android y activos de Android. **Este valor *solo* se puede usar en dispositivos que ejecutan Android 4.4 o posterior (API-19).**

El valor predeterminado es `Assemblies`.

**Experimental**. Agregado en Xamarin.Android 6.1.

## <a name="androidgeneratejnimarshalmethods"></a>AndroidGenerateJniMarshalMethods

Propiedad bool que permite la generación de métodos de serialización JNI como parte del proceso de compilación. Reduce en gran medida el uso de `System.Reflection` en el código de aplicación asistente de enlace.

De forma predeterminada, se establecerá en False. Si los desarrolladores quieren usar la nueva característica de métodos de serialización JNI, pueden establecer

```xml
<AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
```

en su archivo `.csproj`. Especifique también la propiedad en la línea de comandos mediante

```shell
/p:AndroidGenerateJniMarshalMethods=True
```

**Experimental**. Se ha agregado en Xamarin.Android 9.2.
El valor predeterminado es False.

## <a name="androidgeneratejnimarshalmethodsadditionalarguments"></a>AndroidGenerateJniMarshalMethodsAdditionalArguments

Propiedad de cadena que se puede usar para agregar parámetros adicionales a la invocación de `jnimarshalmethod-gen.exe`.  Resulta útil para la depuración, por lo que se pueden usar opciones tales como `-v`, `-d` o `--keeptemp`.

El valor predeterminado es una cadena vacía. Se puede establecer en el archivo `.csproj` o en la línea de comandos. Por ejemplo:

```xml
<AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
```

O bien

```shell
/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
```

Se ha agregado en Xamarin.Android 9.2.

## <a name="androidgeneratelayoutbindings"></a>AndroidGenerateLayoutBindings

Permite la generación de [código subyacente de diseño](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) si se establece en `true` o lo deshabilita por completo si se establece en `false`. El valor predeterminado es `false`.

## <a name="androidhttpclienthandlertype"></a>AndroidHttpClientHandlerType

Controla la implementación de `System.Net.Http.HttpMessageHandler` predeterminada que se va a utilizar mediante el constructor predeterminado `System.Net.Http.HttpClient`. El valor es un nombre calificado con el ensamblado de una subclase `HttpMessageHandler`, adecuada para usarse con [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype#System_Type_GetType_System_String_).
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
> Si la compatibilidad con TLS 1.2 es necesaria en las versiones de Android anteriores a la 5.0, *o* si se requiere con `System.Net.WebClient` y las API relacionadas, se debe usar [`$(AndroidTlsProvider)`](#androidtlsprovider).

> [!NOTE]
> La compatibilidad con esta propiedad funciona estableciendo la [variable de entorno `XA_HTTP_CLIENT_HANDLER_TYPE`](~/android/deploy-test/environment.md).
> Un valor `$XA_HTTP_CLIENT_HANDLER_TYPE` encontrado en un archivo con una acción de compilación de [`@(AndroidEnvironment)`](~/android/deploy-test/building-apps/build-items.md#androidenvironment)
> tendrá prioridad.

Agregado en Xamarin.Android 6.1.

## <a name="androidkeystore"></a>AndroidKeyStore

Valor booleano que indica si se debe usar información de firma personalizada. El valor predeterminado es `False`, lo que significa que se usará la clave de firma de depuración predeterminada para firmar paquetes.

## <a name="androidlaunchactivity"></a>AndroidLaunchActivity

Actividad de Android que se va a iniciar.

## <a name="androidlinkmode"></a>AndroidLinkMode

Especifica qué tipo de [vinculación](~/android/deploy-test/linker.md) debe realizarse en los ensamblados contenidos dentro del paquete de Android. Solo se usa en proyectos de aplicación de Android. El valor predeterminado es *SdkOnly*. Los valores válidos son:

- **None**: no se intenta la vinculación.

- **SdkOnly**: la vinculación se realizará solo en las bibliotecas de clase base, no en los ensamblados del usuario.

- **Full**: la vinculación se realizará en las bibliotecas de clase base y en los ensamblados del usuario.

  > [!NOTE]
  > El uso de un valor para `AndroidLinkMode` de *Full* da lugar con frecuencia a aplicaciones interrumpidas, en especial cuando se usa la reflexión. Evítelo a menos que sepa *realmente* lo que está haciendo.

```xml
<AndroidLinkMode>SdkOnly</AndroidLinkMode>
```

## <a name="androidlinkskip"></a>AndroidLinkSkip

Especifica una lista de nombres de ensamblado delimitada por punto y coma (`;`), sin extensiones de archivos, que no se deben vincular. Solo se usa dentro de proyectos de aplicaciones Android.

```xml
<AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
```

## <a name="androidlinktool"></a>AndroidLinkTool

Propiedad de estilo de la enumeración con los valores válidos de `proguard` o `r8`. Indica el reductor de código que se usa con el código Java. Actualmente el valor predeterminado es una cadena vacía o `proguard` si `$(AndroidEnableProguard)` es `True`. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidlintenabled"></a>AndroidLintEnabled

Propiedad bool que permite que el desarrollador ejecute la herramienta `lint` de Android como parte del proceso de empaquetado.

Cuando `$(AndroidLintEnabled)`=True, se usan las propiedades siguientes:

- [`$(AndroidLintEnabledIssues)`](#androidlintenabledissues):
- [`$(AndroidLintDisabledIssues)`](#androidlintdisabledissues):
- [`$(AndroidLintCheckIssues)`](#androidlintcheckissues):

También se pueden usar las acciones de compilación siguientes:

- [`@(AndroidLintConfig)`](~/android/deploy-test/building-apps/build-items.md#androidlintconfig):

Consulte la [Ayuda de Lint](https://developer.android.com/studio/write/lint) para más información sobre las herramientas `lint` de Android.

## <a name="androidlintenabledissues"></a>AndroidLintEnabledIssues

Esta propiedad solo se utiliza cuando [`$(AndroidLintEnabled)`](#androidlintenabled)=True.

Lista de incidencias de Lint separada por comas que se van a habilitar.

## <a name="androidlintdisabledissues"></a>AndroidLintDisabledIssues

Esta propiedad solo se utiliza cuando [`$(AndroidLintEnabled)`](#androidlintenabled)=True.

Lista de incidencias de Lint separada por comas que se van a deshabilitar.

## <a name="androidlintcheckissues"></a>AndroidLintCheckIssues

Esta propiedad solo se utiliza cuando [`$(AndroidLintEnabled)`](#androidlintenabled)=True.

Lista de incidencias de Lint separada por comas que se van a comprobar.

Nota: Solo se comprobarán estos problemas.

## <a name="androidmanagedsymbols"></a>AndroidManagedSymbols

Propiedad booleana que controla si se generan puntos de secuencia para que la información de nombre de archivo y número de línea se pueda extraer de los seguimientos de la pila de `Release`.

Agregado en Xamarin.Android 6.1.

## <a name="androidmanifest"></a>AndroidManifest

Especifica un nombre de archivo para usar como plantilla para el archivo [`AndroidManifest.xml`](~/android/platform/android-manifest.md) de la aplicación.
Durante la compilación, cualquier otro valor necesario se combina para producir el archivo `AndroidManifest.xml` real.
`$(AndroidManifest)` debe contener el nombre de paquete en el atributo `/manifest/@package`.

## <a name="androidmanifestmerger"></a>AndroidManifestMerger

Especifica la implementación para combinar archivos de *AndroidManifest.xml*. Se trata de una propiedad de estilo de enumeración donde `legacy` selecciona la implementación original de C# y `manifestmerger.jar` selecciona la implementación de Java de Google.

El valor predeterminado es actualmente `legacy`. Esto cambiará a `manifestmerger.jar` en una versión futura para armonizar el comportamiento con Android Studio.

La fusión de Google permite la compatibilidad con `xmlns:tools="http://schemas.android.com/tools"` tal y como se describe en la [documentación de Android][manifest-merger].

Se ha introducido en Xamarin.Android 10.2.

[manifest-merger]: https://developer.android.com/studio/build/manifest-merge

## <a name="androidmanifestplaceholders"></a>AndroidManifestPlaceholders

Lista separada por punto y coma de pares de reemplazo clave-valor para *AndroidManifest.xml*, donde los pares tienen el formato `key=value`.

Por ejemplo, un valor de propiedad de `assemblyName=$(AssemblyName)` define un marcador de posición de `${assemblyName}` que puede aparecer en *AndroidManifest.xml*:

```xml
<application android:label="${assemblyName}"
```

Esto proporciona un método para insertar variables del proceso de compilación en el archivo *AndroidManifest.xml*.

## <a name="androidmultidexclasslistextraargs"></a>AndroidMultiDexClassListExtraArgs

Propiedad de cadena que permite a los desarrolladores pasar argumentos adicionales a `com.android.multidex.MainDexListBuilder` al generar el archivo `multidex.keep`.

Un caso específico es si se obtiene el siguiente error durante la compilación de `dx`.

```text
com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
```

Si recibe este error, puede agregar lo siguiente al elemento `.csproj`.

```xml
<DxExtraArguments>--force-jumbo </DxExtraArguments>
<AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
```

Esto debe permitir que el paso `dx` se realice correctamente.

Se ha agregado en Xamarin.Android 8.3.

## <a name="androidpackageformat"></a>AndroidPackageFormat

Propiedad de estilo de la enumeración con los valores válidos de `apk` o `aab`. Esto indica si quiere empaquetar la aplicación Android como un [archivo APK][apk] o un [paquete de aplicaciones Android][bundle]. Los grupos de aplicaciones son un nuevo formato para las compilaciones de `Release` que se quieran enviar a Google Play. Actualmente, este valor es `apk` de forma predeterminada.

Cuando `$(AndroidPackageFormat)` se establece en `aab`, se establecen otras propiedades de MSBuild necesarias para los paquetes de aplicaciones de Android:

- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2) es `True`.
- [`$(AndroidUseApkSigner)`](#androiduseapksigner) es `False`.
- [`$(AndroidCreatePackagePerAbi)`](#androidcreatepackageperabi) es `False`.

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

## <a name="androidpackagenamingpolicy"></a>AndroidPackageNamingPolicy

Propiedad de estilo de enumeración para especificar los nombres de paquete de Java del código fuente de Java generado.

En Xamarin.Android 10.2 y versiones posteriores, el único valor admitido es `LowercaseCrc64`.

En Xamarin.Android 10.1, también había disponible un valor de `LowercaseMD5` transitorio que permitía volver a cambiar al estilo de nombre del paquete Java original, tal y como se usaba en Xamarin.Android 10.0 y versiones anteriores. Esa opción se quitó en Xamarin.Android 10.2 para mejorar la compatibilidad con entornos de compilación que tienen la conformidad con FIPS aplicada.

Se ha agregado en Xamarin.Android 10.1.

## <a name="androidr8ignorewarnings"></a>AndroidR8IgnoreWarnings

Especifica la regla de ProGuard `-ignorewarnings` para `r8`. Esto permite a `r8` continuar con la compilación dex aunque se encuentren ciertas advertencias. El valor predeterminado es `True`, pero se puede establecer en `False` para exigir un comportamiento más estricto. Para obtener más información, consulte el[manual de ProGuard](https://www.guardsquare.com/products/proguard/manual/usage).

Se ha agregado en Xamarin.Android 10.3.

## <a name="androidr8jarpath"></a>AndroidR8JarPath

Ruta de acceso a `r8.jar` para su uso con el compilador y reductor de dex de r8. El valor predeterminado es una ruta de acceso en la instalación de Xamarin.Android. Para obtener más información, vea la documentación sobre [D8 y R8][d8-r8].

## <a name="androidresgenextraargs"></a>AndroidResgenExtraArgs

Especifica opciones adicionales de la línea de comandos que se van a pasar al comando **aapt2** al procesar recursos de Android.

## <a name="androidresgenfile"></a>AndroidResgenFile

Especifica el nombre del archivo de recursos que se va a generar. La plantilla predeterminada establece esta propiedad en `Resource.designer.cs`.

## <a name="androidsdkbuildtoolsversion"></a>AndroidSdkBuildToolsVersion

El paquete de herramientas de compilación de Android SDK proporciona, entre otras, las herramientas **aapt** y **zipalign**. Se pueden instalar varias versiones diferentes del paquete de herramientas de compilación al mismo tiempo. Para realizar el paquete de herramientas de compilación elegido para empaquetar, se comprueba y usa una versión de herramientas de compilación "preferida", si existe; *en caso contrario*, se usa el paquete de herramientas de compilación instalado con la versión más alta.

La propiedad de MSBuild `$(AndroidSdkBuildToolsVersion)` contiene la versión de herramientas de compilación preferida. El sistema de compilación de Xamarin.Android proporciona un valor predeterminado en `Xamarin.Android.Common.targets`, y el valor predeterminado se puede reemplazar dentro del archivo de proyecto para elegir una versión alternativa de las herramientas de compilación si, por ejemplo, la herramienta aapt más reciente se bloquea mientras se sabe que una versión anterior de aapt funciona.

## <a name="androidsigningkeyalias"></a>AndroidSigningKeyAlias

Especifica el alias de la clave en el almacén de claves. Es el valor de **keytool -alias** usado al crear el almacén de claves.

## <a name="androidsigningkeypass"></a>AndroidSigningKeyPass

Especifica la contraseña de la clave en el archivo de almacén de claves. Este es el valor especificado cuando `keytool` pide que se **escriba la contraseña de clave para $(AndroidSigningKeyAlias)**.

En Xamarin.Android 10.0 y versiones anteriores, esta propiedad solo admite contraseñas de texto sin formato.

En Xamarin.Android 10.1 y versiones posteriores, esta propiedad también admite los prefijos `env:` y `file:` que se pueden usar para especificar una variable de entorno o un archivo que contiene la contraseña. Estas opciones proporcionan una manera de evitar que la contraseña aparezca en los registros de compilación.

Por ejemplo, para usar una variable de entorno denominada *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
</PropertyGroup>
```

Para usar un archivo ubicado en `C:\Users\user1\AndroidSigningPassword.txt`:

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
</PropertyGroup>
```

> [!NOTE]
> El prefijo `env:` no se admite cuando [`$(AndroidPackageFormat)`](#androidpackageformat) está establecido en `aab`.

## <a name="androidsigningkeystore"></a>AndroidSigningKeyStore

Especifica el nombre del archivo del almacén de claves que crea `keytool`. Corresponde al valor proporcionado a la opción **keytool - keystore**.

## <a name="androidsigningstorepass"></a>AndroidSigningStorePass

Especifica la contraseña en [`$(AndroidSigningKeyStore)`](#androidsigningkeystore).
Es el valor proporcionado a `keytool` al crear el archivo de almacén de claves y cuando se pide que se **escriba la contraseña del almacén de claves**.

En Xamarin.Android 10.0 y versiones anteriores, esta propiedad solo admite contraseñas de texto sin formato.

En Xamarin.Android 10.1 y versiones posteriores, esta propiedad también admite los prefijos `env:` y `file:` que se pueden usar para especificar una variable de entorno o un archivo que contiene la contraseña. Estas opciones proporcionan una manera de evitar que la contraseña aparezca en los registros de compilación.

Por ejemplo, para usar una variable de entorno denominada *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
</PropertyGroup>
```

Para usar un archivo ubicado en `C:\Users\user1\AndroidSigningPassword.txt`:

```xml
<PropertyGroup>
    <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
</PropertyGroup>
```

> [!NOTE]
> El prefijo `env:` no se admite cuando [`$(AndroidPackageFormat)`](#androidpackageformat) está establecido en `aab`.

## <a name="androidsupportedabis"></a>AndroidSupportedAbis

Propiedad de cadena que contiene una lista delimitada por punto y coma (`;`) de ABI que se deberían incluir en el archivo `.apk`.

Los valores admitidos son:

- `armeabi-v7a`
- `x86`
- `arm64-v8a`: requiere Xamarin.Android 5.1 y versiones posteriores.
- `x86_64`: requiere Xamarin.Android 5.1 y versiones posteriores.

## <a name="androidtlsprovider"></a>AndroidTlsProvider

Valor de cadena que especifica qué proveedor de TLS se debe usar en una aplicación. Los valores posibles son:

- Anular la cadena vacía: En Xamarin.Android 7.3 y versiones posteriores, esto equivale a `btls`.

  En Xamarin.Android 7.1, esto equivale a `legacy`.

  Esto corresponde a la configuración **Default** de las páginas de propiedades de Visual Studio.

- `btls`: usa [Boring SSL](https://boringssl.googlesource.com/boringssl) para la comunicación TLS con [HttpWebRequest](xref:System.Net.HttpWebRequest).

  Esto permite el uso de TLS 1.2 en todas las versiones de Android.

  Esto corresponde a la configuración **Native TLS 1.2+** de las páginas de propiedades de Visual Studio.

- `legacy`: En Xamarin.Android 10.1 y versiones anteriores, use la implementación de SSL administrada histórica para la interacción de la red. Aquí *no* se admite TLS 1.2.

  Esto corresponde a la configuración **Managed TLS 1.0** de las páginas de propiedades de Visual Studio.

  En Xamarin.Android 10.2 y versiones posteriores, este valor se omite y se usa el valor `btls`.

- `default`: Es poco probable que este valor se use en proyectos de Xamarin.Android. El valor recomendado que se usará en su lugar es la cadena vacía, que corresponde a la configuración **Default** de las páginas de propiedades de Visual Studio.

  El valor `default` no se ofrece en las páginas de propiedades de Visual Studio.

  Actualmente, esto equivale a `legacy`.

Agregado en Xamarin.Android 7.1.

## <a name="androiduseaapt2"></a>AndroidUseAapt2

Propiedad booleana que permite al desarrollador controlar el uso de la herramienta `aapt2` para el empaquetado.
De forma predeterminada, será False y Xamarin.Android usará `aapt`.
Si el desarrollador quiere usar la nueva función de `aapt2`, tiene que agregar:

```xml
<AndroidUseAapt2>True</AndroidUseAapt2>
```

en su archivo `.csproj`. También puede proporcionar la propiedad en la línea de comandos:

```shell
/p:AndroidUseAapt2=True
```

Se ha agregado en Xamarin.Android 8.3.

## <a name="androiduseapksigner"></a>AndroidUseApkSigner

Propiedad bool que permite al desarrollador usar la herramienta `apksigner` en lugar de `jarsigner`.

Agregado en Xamarin.Android 8.2.

## <a name="androidusedefaultaotprofile"></a>AndroidUseDefaultAotProfile

Propiedad bool que permite al desarrollador suprimir el uso de los perfiles de AOT predeterminados.

Para suprimir los perfiles de AOT predeterminados, establezca la propiedad en `false`.

Se ha agregado en Xamarin.Android 10.1.

## <a name="androiduselegacyversioncode"></a>AndroidUseLegacyVersionCode

Propiedad booleana que permite al desarrollador revertir el cálculo de versionCode al comportamiento anterior de Xamarin.Android 8.2. SOLO debe usarse para los desarrolladores con aplicaciones en Google Play Store. Se recomienda encarecidamente utilizar la nueva propiedad [`$(AndroidVersionCodePattern)`](#androidversioncodepattern).

Agregado en Xamarin.Android 8.2.

## <a name="androidusemanageddesigntimeresourcegenerator"></a>AndroidUseManagedDesignTimeResourceGenerator

Propiedad booleana que cambiará las compilaciones en tiempo de diseño para usar el analizador de recursos administrados en lugar de `aapt`.

Agregado en Xamarin.Android 8.1.

## <a name="androidusesharedruntime"></a>AndroidUseSharedRuntime

Propiedad booleana que determina si se requieren los *paquetes en tiempo de ejecución compartido* para ejecutar la aplicación en el dispositivo de destino. Confiar en los paquetes de entorno de tiempo de ejecución compartido permite que el paquete de aplicación sea más pequeño y acelera el proceso de creación y desarrollo del paquete, lo que da lugar a un ciclo de respuesta de compilación, implementación y depuración más rápido.

Esta propiedad debe ser `True` para las compilaciones de depuración y `False` para los proyectos de versión.

## <a name="androidversioncodepattern"></a>AndroidVersionCodePattern

Propiedad de cadena que permite al desarrollador personalizar `versionCode` en el manifiesto.
Consulte [Crear el código de la versión para el APK](~/android/deploy-test/building-apps/abi-specific-apks.md) para más información sobre cómo decidir el valor de `versionCode`.

Algunos ejemplos: si `abi` es `armeabi` y `versionCode` en el manifiesto es `123`, `{abi}{versionCode}` producirá un código de versión de `1123` cuando `$(AndroidCreatePackagePerAbi)` sea True; de lo contrario, producirá un valor de 123.
Si `abi` es `x86_64` y `versionCode` en el manifiesto es `44`, se generará `544` cuando `$(AndroidCreatePackagePerAbi)` sea True; de lo contrario, se producirá un valor de `44`.

Si se incluye una cadena con formato de relleno a la izquierda, `{abi}{versionCode:0000}`, produciría `50044` porque estamos rellenando a la izquierda `versionCode` con `0`. Como alternativa, puede usar el relleno decimal, como `{abi}{versionCode:D4}`,
que hace lo mismo que el ejemplo anterior.

Solo se admiten cadenas con formato de relleno "0" y "Dx", dado que el valor DEBE ser un entero.

Elementos de clave predefinidos

- **** abi&ndash;: inserta la ABI de destino para la aplicación.
  - 2 &ndash; `armeabi-v7a`
  - 3: `x86`
  - 4: `arm64-v8a`
  - 5: `x86_64`

- **minSDK**: inserta el valor de SDK mínimo admitido del archivo `AndroidManifest.xml` o `11` si no se define ninguno.

- **versionCode**: usa el código de versión directamente de `Properties\AndroidManifest.xml`.

Puede definir elementos personalizados con la propiedad `$(AndroidVersionCodeProperties)`, que se define a continuación.

De forma predeterminada, el valor se establece en `{abi}{versionCode:D6}`. Si un programador quiere mantener el comportamiento anterior, se puede reemplazar el valor predeterminado mediante el establecimiento de la propiedad `$(AndroidUseLegacyVersionCode)` en `true`.

Agregado en Xamarin.Android 7.2.

## <a name="androidversioncodeproperties"></a>AndroidVersionCodeProperties

Propiedad de cadena que permite al desarrollador definir elementos personalizados para usar con [`$(AndroidVersionCodePattern)`](#androidversioncodepattern).
Tienen el formato de un par `key=value`. Todos los elementos de `value` deben ser valores enteros. Por ejemplo: `screen=23;target=$(_AndroidApiLevel)`. Como puede ver, pueden hacer uso de propiedades de MSBuild existentes o personalizadas en la cadena.

Agregado en Xamarin.Android 7.2.

## <a name="aotassemblies"></a>AotAssemblies

Propiedad booleana que determina si los ensamblados se compilarán en modo Ahead Of Time en código nativo y se incluirán en el archivo `.apk`.

En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `False`.

## <a name="aprofutilextraoptions"></a>AProfUtilExtraOptions

Opciones adicionales que se van a pasar a `aprofutil`.

## <a name="beforegenerateandroidmanifest"></a>BeforeGenerateAndroidManifest

Los destinos de MSBuild enumerados en esta propiedad se ejecutarán directamente antes de `_GenerateJavaStubs`.

Se ha agregado en Xamarin.Android 9.4.

## <a name="configuration"></a>Configuración

Especifica la configuración de compilación que se usará, como "Depuración" o "Versión". La propiedad Configuration se usa para determinar los valores predeterminados de otras propiedades que determinan el comportamiento de destino. Se pueden crear configuraciones adicionales dentro del IDE.

*De forma predeterminada*, la configuración `Debug` dará lugar a los destinos [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
y [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
que crean un paquete de Android más pequeño que, para funcionar, requiere la presencia de otros archivos y paquetes.

La configuración predeterminada `Release` dará lugar a los destinos [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
y [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
que crean un paquete de Android que es *independiente*, y se puede usar sin necesidad de instalar ningún otro paquete o archivo.

## <a name="debugsymbols"></a>DebugSymbols

Valor booleano que determina si el paquete de Android es *depurable*, en combinación con la propiedad [`$(DebugType)`](#debugtype).
Un paquete depurable contiene símbolos de depuración, establece el [atributo `//application/@android:debuggable`](https://developer.android.com/guide/topics/manifest/application-element#debug) en `true` y agrega automáticamente el permiso [`INTERNET`](https://developer.android.com/reference/android/Manifest.permission#INTERNET)
para que el depurador pueda adjuntarlo al proceso. Una aplicación es depurable si `DebugSymbols` es `True` *y* `DebugType` es la cadena vacía o `Full`.

## <a name="debugtype"></a>DebugType

Especifica el [tipo de símbolos de depuración](/visualstudio/msbuild/csc-task) que se van a generar como parte de la compilación, que también afecta a si la aplicación es depurable. Entre los posibles valores se incluyen:

- **Full**: se generan símbolos completos. Si la propiedad de MSBuild [`DebugSymbols`](#debugsymbols)
  es también `True`, el paquete de aplicación es depurable.

- **PdbOnly**: se generan símbolos "PDB". El paquete de aplicación no es depurable.

Si `DebugType` no está establecido o es la cadena vacía, la propiedad `DebugSymbols` controla si la aplicación es o no depurable.

## <a name="embedassembliesintoapk"></a>EmbedAssembliesIntoApk

Propiedad booleana que determina si los ensamblados de la aplicación se deben insertar o no en el paquete de aplicación.

Esta propiedad debe ser `True` para compilaciones de versión y `False` para compilaciones de depuración. *Puede* que deba ser `True` en las compilaciones de depuración si la implementación rápida no admite el dispositivo de destino.

Cuando esta propiedad es `False`, la propiedad de MSBuild [`$(AndroidFastDeploymentType)`](#androidfastdeploymenttype)
también controla lo que se insertará en el archivo `.apk`, lo que puede afectar a los tiempos de implementación y recompilación.

## <a name="enablellvm"></a>EnableLLVM

Propiedad booleana que determina si se usará o no LLVM al realizar la compilación Ahead Of Time de los ensamblados en código nativo.

El NDK de Android debe estar instalado para compilar un proyecto que tenga esta propiedad habilitada.

En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `False`.

Esta propiedad se omite a menos que la propiedad de MSBuild [`$(AotAssemblies)`](#aotassemblies) sea `True`.

## <a name="enableproguard"></a>EnableProguard

Propiedad booleana que determina si se ejecuta o no [ProGuard](https://developer.android.com/tools/help/proguard.html) como parte del proceso de empaquetado para vincular código Java.

En Xamarin.Android 5.1, se agregó compatibilidad con esta propiedad.

De forma predeterminada, esta propiedad es `False`.

Si es `True`, los archivos de [@(ProguardConfiguration)](~/android/deploy-test/building-apps/build-items.md#proguardconfiguration) se usarán para controlar la ejecución de `proguard`.

## <a name="javamaximumheapsize"></a>JavaMaximumHeapSize

Especifica el valor del parámetro de **java**
`-Xmx` que se usará al compilar el archivo `.dex` como parte del proceso de empaquetado. Si no se especifica, la opción `-Xmx` suministra **java** con un valor de `1G`. Se ha detectado que esto suele ser necesario en Windows en comparación con otras plataformas.

Es necesario especificar esta propiedad si el destino [`_CompileDex` produce un error `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

Personalice el valor cambiando lo siguiente:

```xml
<JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
```

## <a name="javaoptions"></a>JavaOptions

Especifica opciones de la línea de comandos adicionales que se pasarán a **Java** al compilar el archivo `.dex`.

## <a name="jarsignertimestampauthoritycertificatealias"></a>JarsignerTimestampAuthorityCertificateAlias

Esta propiedad permite especificar un alias en el almacén de claves para una autoridad de marca de tiempo.
Para obtener más información, consulte la documentación de [compatibilidad con la marca de tiempo de la firma](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) de Java.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
</PropertyGroup>
```

## <a name="jarsignertimestampauthorityurl"></a>JarsignerTimestampAuthorityUrl

Esta propiedad permite especificar una dirección URL para un servicio de autoridad de marca de tiempo. Se puede usar para asegurarse de que la firma de `.apk` incluye una marca de tiempo.
Para obtener más información, consulte la documentación de [compatibilidad con la marca de tiempo de la firma](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) de Java.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
</PropertyGroup>
```

## <a name="linkerdumpdependencies"></a>LinkerDumpDependencies

Propiedad bool que permite la generación del archivo de dependencias del enlazador. Este archivo se puede usar como entrada para la herramienta [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

El valor predeterminado es False.

## <a name="mandroidi18n"></a>MandroidI18n

Especifica la compatibilidad con la internacionalización que se incluye con la aplicación, como tablas de intercalación y ordenación. El valor es una lista separada por comas o punto y coma de uno o varios de los siguientes valores sin distinción entre mayúsculas y minúsculas:

- **None**: no incluye codificaciones adicionales.

- **All**: incluye todas las codificaciones disponibles.

- **CJK**: incluye las codificaciones china, japonesa y coreana, como *Japonés (EUC)* \[enc-jp, CP51932\], *Japonés (Shift-JIS)* \[iso-2022-jp, shift\_jis, CP932\], *Japonés (JIS)* \[CP50220\], *Chino simplificado (GB2312)* \[gb2312, CP936\], *Coreano (UHC)* \[ks\_c\_5601-1987, CP949\], *Coreano (EUC)* \[euc-kr, CP51949\], *Chino tradicional (Big5)* \[big5, CP950\] y *Chino simplificado (GB18030)* \[GB18030, CP54936\].

- **MidEast**: incluye las codificaciones de Oriente Medio, como *Turco (Windows)* \[iso-8859-9, CP1254\], *Hebreo (Windows)* \[windows-1255, CP1255\], *Árabe (Windows)* \[windows-1256, CP1256\], *Árabe (ISO)* \[iso-8859-6, CP28596\], *Hebreo (ISO)* \[iso-8859-8, CP28598\], *Latín 5 (ISO)* \[iso-8859-9, CP28599\] y *Hebreo (alternativa a Iso)* \[iso-8859-8, CP38598\].

- **Other**: incluye otras codificaciones, como *Cirílico(Windows)* \[CP1251\], *Báltico (Windows)* \[iso-8859-4, CP1257\], *Vietnamita (Windows)* \[CP1258\], *Cirílico (KOI8-R)* \[koi8-r, CP1251\], *Ucraniano (KOI8-U)* \[koi8-u, CP1251\], *Báltico (ISO)* \[iso-8859-4, CP1257\], *Cirílico (ISO)* \[iso-8859-5, CP1251\], *ISCII Davenagari* \[x-iscii-de, CP57002\], *ISCII Bengalí* \[x-iscii-be, CP57003\], *ISCII Tamil* \[x-iscii-ta, CP57004\], *ISCII Telugu* \[x-iscii-te, CP57005\], *ISCII Asamés* \[x-iscii-as, CP57006\], *ISCII Oriya* \[x-iscii-or, CP57007\], *ISCII Canarés* \[x-iscii-ka, CP57008\], *ISCII Malayalam* \[x-iscii-ma, CP57009\], *ISCII Gujarati* \[x-iscii-gu, CP57010\], *ISCII Punjabi* \[x-iscii-pa, CP57011\] y *Thai (Windows)* \[CP874\].

- **Rare**: incluye las codificaciones raras, como *IBM EBCDIC (Turco)* \[CP1026\], *IBM EBCDIC (Latín 1, sistemas abiertos)* \[CP1047\], *IBM EBCDIC (EE. UU. y Canadá con Euro)* \[CP1140\], *IBM EBCDIC (Alemania con Euro)* \[CP1141\], *IBM EBCDIC (Dinamarca/Noruega con Euro)* \[CP1142\], *IBM EBCDIC (Finlandia/Suecia con Euro)* \[CP1143\], *IBM EBCDIC (Italia con Euro)* \[CP1144\], *IBM EBCDIC (Latinoamérica/España con Euro)* \[CP1145\], *IBM EBCDIC (Reino Unido con Euro)* \[CP1146\], *IBM EBCDIC (Francia con Euro)* \[CP1147\], *IBM EBCDIC (Internacional con Euro)* \[CP1148\], *IBM EBCDIC (Islandés con Euro)* \[CP1149\], *IBM EBCDIC (Alemania)* \[CP20273\], *IBM EBCDIC (Dinamarca/Noruega)* \[CP20277\], *IBM EBCDIC (Finlandia/Suecia)* \[CP20278\], *IBM EBCDIC (Italia)* \[CP20280\], *IBM EBCDIC (Latinoamérica/España)* \[CP20284\], *IBM EBCDIC (Reino Unido)* \[CP20285\], *IBM EBCDIC (Katakana japonés extendido)* \[CP20290\], *IBM EBCDIC (Francia)* \[CP20297\], *IBM EBCDIC (Árabe)* \[CP20420\], *IBM EBCDIC (Hebreo)* \[CP20424\], *IBM EBCDIC (Islandés)* \[CP20871\], *IBM EBCDIC (Cirílico: serbio, búlgaro)* \[CP21025\], *IBM EBCDIC (EE. UU. y Canadá)* \[CP37\], *IBM EBCDIC (Internacional)* \[CP500\], *Árabe (ASMO 708)* \[CP708\], *Centroeuropeo (DOS)* \[CP852\]*, Cirílico (DOS)* \[CP855\], *Turco (DOS)* \[CP857\], *Europeo Occidental (DOS con Euro)* \[CP858\], *Hebreo (DOS)* \[CP862\], *Árabe (DOS)* \[CP864\], *Ruso (DOS)* \[CP866\], *Griego (DOS)* \[CP869\], *IBM EBCDIC (Latín 2)* \[CP870\], and *IBM EBCDIC (Griego)* \[CP875\].

- **West**: incluye las codificaciones occidentales, como *Europeo Occidental (Mac)* \[macintosh, CP10000\], *Islandés (Mac)* \[x-mac-icelandic, CP10079\], *Centroeuropeo (Windows)* \[iso-8859-2, CP1250\], *Europeo Occidental (Windows)* \[iso-8859-1, CP1252\], *Griego (Windows)* \[iso-8859-7, CP1253\], *Centroeuropeo (ISO)* \[iso-8859-2, CP28592\], *Latín 3 (ISO)* \[iso-8859-3, CP28593\], *Griego (ISO)* \[iso-8859-7, CP28597\], *Latín 9 (ISO)* \[iso-8859-15, CP28605\], *Estados Unidos OEM* \[CP437\], *Europeo Occidental (DOS)* \[CP850\], *Portugués (DOS)* \[CP860\], *Islandés (DOS)* \[CP861\], *Francés canadiense (DOS)* \[CP863\], and *Nórdico (DOS)* \[CP865\].

```xml
<MandroidI18n>West</MandroidI18n>
```

## <a name="monoandroidresourceprefix"></a>MonoAndroidResourcePrefix

Especifica un *prefijo de ruta de acceso* que se quita del principio de los nombres de archivo con una acción de compilación de `AndroidResource`. Esto permite cambiar la ubicación de los recursos.

El valor predeterminado es `Resources`. Cambie este valor a `res` con estructuras de proyectos de Java.

## <a name="monosymbolarchive"></a>MonoSymbolArchive

Propiedad booleana que controla si los artefactos de `.mSYM` se crean para usarlos más adelante con `mono-symbolicate`, a fin de extraer información &ldquo;real&rdquo; de nombre de archivo y número de línea de los seguimientos de la pila de versión.

Este valor es True de forma predeterminada para aplicaciones de &ldquo;lanzamiento&rdquo; que tienen habilitados los símbolos de depuración: [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) es True, [`$(DebugSymbols)`](~/android/deploy-test/building-apps/build-properties.md#debugsymbols)
es True y [`$(Optimize)`](/visualstudio/msbuild/common-msbuild-project-properties)
es True.

Agregado en Xamarin.Android 7.1.