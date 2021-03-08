---
title: Proceso de compilación
description: En este documento se proporciona información general sobre el proceso de compilación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2021
ms.openlocfilehash: bc47c9a939188a8bf4d70e11e81e9c43c948f8f4
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749309"
---
# <a name="build-process"></a>Proceso de compilación

El proceso de compilación de Xamarin.Android es responsable de pegarlo todo junto y [generar `Resource.designer.cs`](~/android/internals/api-design.md), admitir [`@(AndroidAsset)`](~/android/deploy-test/building-apps/build-items.md#androidasset), [`@(AndroidResource)`](~/android/deploy-test/building-apps/build-items.md#androidresource) y otras [acciones de compilación](~/android/deploy-test/building-apps/build-items.md), generar [contenedores Android invocables](~/android/platform/java-integration/android-callable-wrappers.md) y generar un archivo `.apk` para su ejecución en dispositivos Android.

## <a name="application-packages"></a>Paquetes de aplicación

En términos generales, hay dos tipos de paquetes de aplicación de Android (archivos `.apk`) que puede generar el sistema de compilación de Xamarin.Android:

- Compilaciones de **versión**, que son totalmente independientes y no requieren la ejecución de paquetes adicionales. Se trata de los paquetes que se ofrecen en una tienda de aplicaciones.

- Compilaciones de **depuración**, los que no.

Estos tipos de paquete coinciden con el objeto MSBuild `Configuration` que genera el paquete.

## <a name="shared-runtime"></a>Entorno de tiempo de ejecución compartido

Antes de Xamarin.Android 11.2, el *entorno de ejecución compartido* consistía en un par de paquetes de Android adicionales que proporcionaban la biblioteca de clases base (`mscorlib.dll`, etc.) y la biblioteca de enlaces de Android (`Mono.Android.dll`, etc.). Las compilaciones de depuración dependen del entorno de tiempo de ejecución compartido en lugar de incluir los ensamblados de enlace y de biblioteca de clases base en el paquete de aplicaciones Android, lo que permite que el paquete de depuración sea más pequeño.

El entorno de ejecución compartido se podía deshabilitar en las compilaciones de depuración estableciendo la propiedad [`$(AndroidUseSharedRuntime)`](~/android/deploy-test/building-apps/build-properties.md#androidusesharedruntime)
en `False`.

La compatibilidad con el entorno de ejecución compartido se quitó en Xamarin.Android 11.2.

<a name="Fast_Deployment"></a>

## <a name="fast-deployment"></a>Implementación rápida

La *implementación rápida* funciona reduciendo aún más el tamaño del paquete de aplicaciones Android. Para ello, se excluyen los ensamblados de la aplicación del paquete y, en su lugar, se implementan los ensamblados de la aplicación directamente en el directorio interno de la aplicación `files`, que normalmente se encuentra en `/data/data/com.some.package`. El directorio interno `files` no es una carpeta de escritura global, por lo que se usa la herramienta `run-as` para ejecutar todos los comandos para copiar los archivos en ese directorio.

Este proceso acelera el ciclo de compilación, implementación y depuración porque el paquete no se vuelve a instalar cuando *solo* se cambian ensamblados.
Solo los ensamblados actualizados se vuelven a sincronizar con el dispositivo de destino.

> [!ADVERTENCIA> Se sabe que la implementación rápida no funciona en los dispositivos que bloquean `run-as`, lo que suele incluir dispositivos anteriores a Android 5.0.

La implementación rápida está habilitada de forma predeterminada y puede deshabilitarse en las compilaciones de depuración si se establece la propiedad `$(EmbedAssembliesIntoApk)` en `True`.

Se puede usar el modo de [implementación rápida mejorada](~/android/deploy-test/building-apps/build-properties.md#androidfastdeploymenttype) con esta característica para acelerar aún más las implementaciones.
Así se implementarán tanto ensamblados, como bibliotecas nativas, typemaps y dexes en el directorio `files`. Sin embargo, realmente solo debe habilitarlo si va a cambiar bibliotecas nativas, enlaces o código Java.

## <a name="msbuild-projects"></a>Proyectos de MSBuild

El proceso de compilación de Xamarin.Android se basa en MSBuild, que también es el formato de archivo de proyecto usado por Visual Studio para Mac y Visual Studio.
Normalmente, no es necesario que los usuarios editen manualmente los archivos de MSBuild, dado que el IDE crea proyectos totalmente funcionales, los actualiza con los cambios realizados e invoca destinos de compilación según sea necesario.

Puede que los usuarios avanzados quieran realizar operaciones que no se admiten en la interfaz gráfica de usuario del IDE, así que el proceso de compilación se puede personalizar mediante la edición directa del archivo de proyecto.
En esta página solo se documentan las características y personalizaciones específicas de Xamarin.Android; se pueden realizar muchas más cosas con los elementos, las propiedades y los destinos normales de MSBuild.

<a name="Build_Targets"></a>

## <a name="binding-projects"></a>Proyectos de enlace

Las siguientes propiedades de MSBuild se usan con [proyectos de enlace](~/android/platform/binding-java-library/index.md):

- [`$(AndroidClassParser)`](~/android/deploy-test/building-apps/build-properties.md#androidclassparser)
- [`$(AndroidCodegenTarget)`](~/android/deploy-test/building-apps/build-properties.md#androidcodegentarget)

## <a name="resourcedesignercs-generation"></a>Generación de `Resource.designer.cs`

Las propiedades de MSBuild siguientes se utilizan para controlar la generación del archivo `Resource.designer.cs`:

- [`$(AndroidAapt2CompileExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2compileextraargs)
- [`$(AndroidAapt2LinkExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2linkextraargs)
- [`$(AndroidExplicitCrunch)`](~/android/deploy-test/building-apps/build-properties.md#androidexplicitcrunch)
- [`$(AndroidR8IgnoreWarnings)`](~/android/deploy-test/building-apps/build-properties.md#androidr8ignorewarnings)
- [`$(AndroidResgenExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenextraargs)
- [`$(AndroidResgenFile)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenfile)
- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2)
- [`$(MonoAndroidResourcePrefix)`](~/android/deploy-test/building-apps/build-properties.md#monoandroidresourceprefix)

## <a name="signing-properties"></a>Propiedades de firma

Las propiedades de firma controlan cómo se firma el paquete de aplicación para que se pueda instalar en un dispositivo Android. Para permitir una iteración de compilación más rápida, las tareas de Xamarin.Android no firman paquetes durante el proceso de compilación, porque es un proceso bastante lento. En su lugar, se firman (si es necesario) antes de la instalación o durante la exportación, por medio del IDE o del destino de compilación *Install*. Al invocar el destino *SignAndroidPackage* se produce un paquete con el sufijo `-Signed.apk` en el directorio de salida.

De forma predeterminada, el destino de firma genera una nueva clave de firma de depuración, si es necesario. Si desea usar una clave específica, por ejemplo, en un servidor de compilación, se usan las propiedades siguientes de MSBuild:

- [`$(AndroidDebugKeyAlgorithm)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyalgorithm)
- [`$(AndroidDebugKeyValidity)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyvalidity)
- [`$(AndroidDebugStoreType)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugstoretype)
- [`$(AndroidKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidkeystore)
- [`$(AndroidSigningKeyAlias)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeyalias)
- [`$(AndroidSigningKeyPass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeypass)
- [`$(AndroidSigningKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeystore)
- [`$(AndroidSigningStorePass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningstorepass)
- [`$(JarsignerTimestampAuthorityCertificateAlias)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthoritycertificatealias)
- [`$(JarsignerTimestampAuthorityUrl)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthorityurl)

### <a name="keytool-option-mapping"></a>Asignación de opciones `keytool`

Considere la invocación de `keytool` siguiente:

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

Los puntos de extensión incluyen lo siguiente:

- [`$(AfterGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#aftergenerateandroidmanifest)
- [`$(BeforeGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#beforegenerateandroidmanifest)

Tenga en cuenta esta recomendación sobre la extensión del proceso de compilación: Si no se escriben correctamente, las extensiones de compilación pueden afectar al rendimiento de la compilación, especialmente si se ejecutan en cada compilación. Es muy recomendable que lea la [documentación](/visualstudio/msbuild/msbuild) de MSBuild antes de implementar estas extensiones.

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
