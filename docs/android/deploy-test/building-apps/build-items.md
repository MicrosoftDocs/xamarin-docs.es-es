---
title: Elementos de compilación
description: En este documento se muestran todos los grupos de elementos admitidos en el proceso de compilación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/23/2020
ms.openlocfilehash: 90efe2533f971180124d044ec39ddcf1591b9d36
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455045"
---
# <a name="build-items"></a>Elementos de compilación

Los elementos de compilación controlan el modo en que se compila un proyecto de biblioteca o de aplicación de Xamarin.Android.

## <a name="androidasset"></a>AndroidAsset

Admite [recursos de Android](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles), archivos que se incluirán en la carpeta `assets` en un proyecto de Java Android.

## <a name="androidaarlibrary"></a>AndroidAarLibrary

La acción de compilación de `AndroidAarLibrary` se debe utilizar para hacer referencia directamente a los archivos `.aar`. Esta acción de compilación la utilizan normalmente componentes de Xamarin. Es decir, para incluir referencias a archivos `.aar` que son necesarios para que funcionen Google Play y otros servicios.

Los archivos con esta acción de compilación se tratarán de un modo bastante similar a los recursos incrustados en proyectos de biblioteca. El archivo `.aar` se extrae en el directorio intermedio. Después, los recursos y los archivos `.jar` se incluirán en los grupos de elementos pertinentes.

## <a name="androidaotprofile"></a>AndroidAotProfile

Se usa para proporcionar un perfil de AOT, para su uso con el AOT guiado por perfiles.

## <a name="androidboundlayout"></a>AndroidBoundLayout

Indica que el archivo de diseño debe tener código subyacente generado en caso de que la propiedad `AndroidGenerateLayoutBindings` se establezca en `false`. En todos los demás aspectos es idéntica a `AndroidResource`, descrito anteriormente. Esta acción **solo** se puede usar con archivos de diseño:

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

## <a name="androidenvironment"></a>AndroidEnvironment

Los archivos con una acción de compilación de `AndroidEnvironment` se usan para [inicializar variables de entorno y propiedades del sistema durante el inicio del proceso](~/android/deploy-test/environment.md).
La acción de compilación `AndroidEnvironment` se puede aplicar a varios archivos, y estos se evalúan sin ningún orden determinado (así que no especifique la misma variable de entorno o propiedad de sistema en varios archivos).

## <a name="androidfragmenttype"></a>AndroidFragmentType

Especifica el tipo completo predeterminado que se usará para todos los elementos de diseño de `<fragment>` al generar el código de los enlaces de diseño. El valor predeterminado de la propiedad es el tipo estándar `Android.App.Fragment` de Android.

## <a name="androidjavalibrary"></a>AndroidJavaLibrary

Los archivos con una acción de compilación de `AndroidJavaLibrary` son archivos de Java (archivos `.jar`) que se incluirán en el paquete final de Android.

## <a name="androidjavasource"></a>AndroidJavaSource

Los archivos con una acción de compilación de `AndroidJavaSource` son el código fuente de Java que se incluirá en el paquete final de Android.

## <a name="androidlintconfig"></a>AndroidLintConfig

La acción de compilación "AndroidLintConfig" se debe usar con la propiedad de compilación [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled).
propiedad. Los archivos con esta acción de compilación se combinan y se pasan a las herramientas `lint` de Android. Deben ser archivos XML que contengan información sobre qué pruebas se habilitan o deshabilitan.

Vea la [documentación de lint](https://developer.android.com/studio/write/lint) para obtener más información.

## <a name="androidnativelibrary"></a>AndroidNativeLibrary

Las [bibliotecas nativas](~/android/platform/native-libraries.md) se agregan a la compilación mediante el establecimiento de su acción de compilación en `AndroidNativeLibrary`.

Tenga en cuenta que puesto que Android admite varias interfaces binarias de aplicación (ABI), el sistema de compilación debe saber para qué ABI está compilada la biblioteca nativa. Hay dos maneras de hacerlo:

1. Examinando la ruta de acceso.
2. Mediante el atributo de elemento `Abi`.

Con el examen de la ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI a la que se dirige la biblioteca. Por lo tanto, si agrega `lib/armeabi-v7a/libfoo.so` a la compilación, la ABI se examina como `armeabi-v7a`.

### <a name="item-attribute-name"></a>Nombre del atributo de elemento

**** Abi&ndash;: especifica la ABI de la biblioteca nativa.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

## <a name="androidresource"></a>AndroidResource

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

**** LogicalName&ndash;: especifica la ruta de acceso a los recursos de manera explícita. Permite la creación de &ldquo;alias&rdquo; de archivos para que estén disponibles con varios nombres de recursos distintos.

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

## <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

La acción de compilación `AndroidResourceAnalysisConfig` marca un archivo como un archivo de configuración de nivel de gravedad para la herramienta de diagnóstico de diseño de Xamarin Android Designer. Actualmente solo se usa en el editor de diseño y no en los mensajes de compilación.

Para más información, consulte la [documentación de análisis de recursos de Android](../../user-interface/android-designer/diagnostics.md).

Se ha agregado en Xamarin.Android 10.2.

## <a name="content"></a>Contenido

La acción de compilación `Content` no se admite (dado que no hemos descubierto cómo admitirla sin un paso seguramente costoso de primera ejecución).

A partir de Xamarin.Android 5.1, si se intenta usar la acción de compilación `@(Content)` se producirá una advertencia `XA0101`.

## <a name="linkdescription"></a>LinkDescription

Los archivos con una acción de compilación *LinkDescription* se usan para [controlan el comportamiento del enlazador](~/cross-platform/deploy-test/linker.md).

## <a name="proguardconfiguration"></a>ProguardConfiguration

Los archivos con una acción de compilación *ProguardConfiguration* contienen opciones que se usan para controlar el comportamiento de `proguard`. Para más información sobre esta acción de compilación, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Estos archivos se omiten a menos que la propiedad de MSBuild [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
sea `True`.