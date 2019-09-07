---
title: Solución de problemas de enlaces
description: En este artículo se resumen los errores comunes de servidor que pueden producirse al generar enlaces, junto con las posibles causas y las formas sugeridas de resolverlos.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: dfbcb1a6f502d6d7a5b03dc03278fc21e57806bf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756610"
---
# <a name="troubleshooting-bindings"></a>Solución de problemas de enlaces

_En este artículo se resumen los errores comunes de servidor que pueden producirse al generar enlaces, junto con las posibles causas y las formas sugeridas de resolverlos._

## <a name="overview"></a>Información general

Enlazar una biblioteca de Android (un archivo **. AAR** o **. jar**) rara vez es un suerte sencillo; normalmente requiere un esfuerzo adicional para mitigar los problemas derivados de las diferencias entre Java y .NET.
Estos problemas impedirán que Xamarin. Android enlace la biblioteca de Android y se presente como mensajes de error en el registro de compilación. En esta guía se ofrecen algunas sugerencias para solucionar los problemas, se enumeran algunos de los problemas o escenarios más comunes y se proporcionan posibles soluciones para enlazar correctamente la biblioteca de Android.

Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los puntos siguientes:

- **Dependencias externas de la biblioteca** Todas las dependencias de Java que requiere la biblioteca de Android deben incluirse en el proyecto de Xamarin. Android como **ReferenceJar** o como **EmbeddedReferenceJar.** &ndash;

- **El nivel de API de Android que es destino de la biblioteca de Android** &ndash; No es posible "degradar" el nivel de API de Android; Asegúrese de que el proyecto de enlace de Xamarin. Android tenga como destino el mismo nivel de API (o superior) que la biblioteca de Android.

- **La versión del JDK de Android que se usó para empaquetar la biblioteca de Android** &ndash; Pueden producirse errores de enlace si la biblioteca de Android se compiló con una versión de JDK diferente de la que usa Xamarin. Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión del JDK que usa la instalación de Xamarin. Android.

El primer paso para la solución de problemas con el enlace de una biblioteca de Xamarin. Android es habilitar la [salida de MSBuild de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Después de habilitar la salida de diagnóstico, vuelva a compilar el proyecto de enlace de Xamarin. Android y examine el registro de compilación para buscar pistas sobre cuál es la causa del problema.

También puede resultar útil descompilar la biblioteca de Android y examinar los tipos y métodos que Xamarin. Android está intentando enlazar. Esto se explica con más detalle más adelante en esta guía.

## <a name="decompiling-an-android-library"></a>Descompilación de una biblioteca de Android

Inspeccionar las clases y los métodos de las clases de Java puede proporcionar información valiosa que le ayudará en el enlace de una biblioteca.
[JD-GUI](http://jd.benow.ca/) es una utilidad gráfica que puede mostrar el código fuente de Java de los archivos de **clase** contenidos en un archivo jar. Se puede ejecutar como una aplicación independiente o como un complemento para IntelliJ o Eclipse.

Para descompilar una biblioteca de Android, abra el **. Archivo JAR** con la descompilador de Java. Si la biblioteca es una **. Archivo AAR** , es necesario extraer el archivo **classes. jar** del archivo de almacenamiento. A continuación se muestra una captura de pantalla de ejemplo del uso de JD-GUI para analizar el archivo jar de [Picasso](http://square.github.io/picasso/) :

![Uso de la descompilador de Java para analizar Picasso-2.5.2. jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Una vez que haya descompilado la biblioteca de Android, examine el código fuente. En términos generales, busque:

- **Clases que tienen características de ofuscación** &ndash; Las características de las clases ofuscadas incluyen:

  - El nombre de clase incluye **$** una clase, es decir, **una $.**
  - El nombre de clase está totalmente comprometido por caracteres en minúsculas, es decir, **a.**      

- las instrucciones &ndash; **de las bibliotecas sin referencia identifican la biblioteca sin referencia y agregan esas dependencias al proyecto de enlace de Xamarin. Android con una acción de compilación de ReferenceJar o `import`**   **EmbedddedReferenceJar**.

> [!NOTE]
> La descompilación de una biblioteca de Java puede estar prohibida o sujeta a restricciones legales basadas en leyes locales o en la licencia en la que se publicó la biblioteca de Java. Si es necesario, dé de alta los servicios de un profesional legal antes de intentar descompilar una biblioteca de Java e inspeccionar el código fuente.

## <a name="inspect-apixml"></a>Inspeccione la API. LENGUAJE

Como parte de la compilación de un proyecto de enlace, Xamarin. Android generará un nombre de archivo XML **obj/Debug/API. XML**:

![API. XML generada en obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Este archivo proporciona una lista de todas las API de Java que está intentando enlazar Xamarin. Android. El contenido de este archivo puede ayudar a identificar los tipos o métodos que faltan, el enlace duplicado. Aunque la inspección de este archivo es tediosa y lenta, puede proporcionar pistas sobre lo que puede estar causando problemas de enlace. Por ejemplo, **API. XML** podría revelar que una propiedad devuelve un tipo inadecuado o que hay dos tipos que comparten el mismo nombre administrado.

## <a name="known-issues"></a>Problemas conocidos

En esta sección se enumeran algunos de los mensajes de error comunes o síntomas que se producen al intentar enlazar una biblioteca de Android.

### <a name="problem-java-version-mismatch"></a>Problema: Versión de Java no coincidente

A veces, los tipos no se generan o se pueden producir bloqueos inesperados porque se usa una versión más reciente o anterior de Java en comparación con la compilación de la biblioteca. Vuelva a compilar la biblioteca de Android con la misma versión del JDK que usa el proyecto de Xamarin. Android.

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: Se requiere al menos una biblioteca de Java

Recibe el error "se requiere al menos una biblioteca de Java", aunque. Se ha agregado el archivo JAR.

#### <a name="possible-causes"></a>Causas posibles:

Asegúrese de que la acción de compilación está `EmbeddedJar`establecida en. Dado que hay varias acciones de compilación para. Archivos JAR ( `InputJar`como `EmbeddedJar` `ReferenceJar` , y`EmbeddedReferenceJar`), el generador de enlaces no puede adivinar automáticamente cuál debe usarse de forma predeterminada. Para obtener más información sobre las acciones de compilación, vea [acciones de compilación](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Las herramientas de enlace no pueden cargar. Biblioteca JAR

El generador de la biblioteca de enlaces no puede cargar. Biblioteca JAR.

#### <a name="possible-causes"></a>Causas posibles

Existen. Las bibliotecas de JAR que usan la ofuscación de código (a través de herramientas como ProGuard) no se pueden cargar con las herramientas de Java. Dado que nuestra herramienta hace uso de la reflexión de Java y la biblioteca de ingeniería de código de bytes ASM, esas herramientas dependientes pueden rechazar las bibliotecas ofuscadas, mientras que las herramientas de tiempo de ejecución de Android pueden pasar. La solución alternativa para esto es enlazar a mano estas bibliotecas en lugar de usar el generador de enlaces.

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Faltan C# tipos en la salida generada.

Binding **. dll** se compila pero se pierden algunos tipos de Java o el origen C# generado no se compila debido a un error que indica que faltan tipos.

#### <a name="possible-causes"></a>Causas posibles:

Este error puede producirse por varias razones, como se indica a continuación:

- La biblioteca que se está enlazando puede hacer referencia a una segunda biblioteca de Java. Si la API pública de la biblioteca enlazada usa tipos de la segunda biblioteca, también debe hacer referencia a un enlace administrado para la segunda biblioteca.

- Es posible que se haya insertado una biblioteca debido a la reflexión de Java, similar a la causa del error de carga de la biblioteca anterior, lo que provocaría la carga inesperada de metadatos. Actualmente, las herramientas de Xamarin. Android no pueden resolver esta situación. En tal caso, la biblioteca se debe enlazar manualmente.

- Error en el tiempo de ejecución de .NET 4,0 que no pudo cargar los ensamblados cuando debería tener. Este problema se ha corregido en el tiempo de ejecución de .NET 4,5.

- Java permite derivar una clase pública de una clase no pública, pero esto no se admite en .NET. Dado que el generador de enlaces no genera enlaces para clases no públicas, las clases derivadas como estas no se pueden generar correctamente. Para corregirlo, quite la entrada de metadatos para las clases derivadas mediante el uso de Remove-node en **Metadata. XML**o corrija los metadatos que hacen que la clase no pública sea pública. Aunque la última solución creará el enlace para que se C# compile el origen, no se debe usar la clase no pública.

  Por ejemplo:

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Las herramientas que ofuscan las bibliotecas de Java pueden interferir con el generador de enlaces de Xamarin. Android C# y su capacidad de generar clases contenedoras. En el fragmento de código siguiente se muestra cómo actualizar **Metadata. XML** para no ofuscar un nombre de clase:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: El C# origen generado no se compila debido a un error de coincidencia de tipos de parámetro

El origen C# generado no se compila. Los tipos de parámetros del método invalidado no coinciden.

#### <a name="possible-causes"></a>Causas posibles:

Xamarin. Android incluye una variedad de campos de Java que se asignan a las enumeraciones en los C# enlaces. Pueden provocar incompatibilidades de tipos en los enlaces generados. Para resolver esto, las signaturas de método creadas desde el generador de enlaces deben modificarse para usar las enumeraciones. Para obtener más información, vea [corregir enumeraciones](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError en empaquetado

`java.lang.NoClassDefFoundError`se produce en el paso de empaquetado.

#### <a name="possible-causes"></a>Causas posibles:

La razón más probable de este error es que es necesario agregar una biblioteca Java obligatoria al proyecto de aplicación ( **. csproj**). . Los archivos JAR no se resuelven automáticamente. Un enlace de la biblioteca de Java no se genera siempre en un ensamblado de usuario que no existe en el emulador o dispositivo de destino (como Google Maps **Maps. jar**). Este no es el caso de la compatibilidad con proyectos de biblioteca Android, como la biblioteca. JAR se incrusta en el archivo dll de biblioteca. Por ejemplo:  [Error 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Tipos de EventArgs personalizados duplicados

Se produce un error en la compilación debido a tipos EventArgs personalizados duplicados. Se produce un error similar al siguiente:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causas posibles:

Esto se debe a que hay algún conflicto entre los tipos de eventos que proceden de más de un tipo de "agente de escucha" de la interfaz que comparte métodos que tienen nombres idénticos. Por ejemplo, si hay dos interfaces Java como se muestra en el ejemplo siguiente, el generador crea `DismissScreenEventArgs` `MediationBannerListener` para y `MediationInterstitialListener`, lo que produce el error.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Esto es así por diseño para evitar los nombres largos en los tipos de argumento de evento. Para evitar estos conflictos, se requiere una transformación de metadatos. Edite [**Transforms\Metadata.XML**](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) y agregue `argsType` un atributo en cualquiera de las interfaces (o en el método de interfaz):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: La clase no implementa el método de interfaz

Se genera un mensaje de error que indica que una clase generada no implementa un método necesario para una interfaz que implementa la clase generada. Sin embargo, si examina el código generado, puede ver que el método está implementado.

Este es un ejemplo del error:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Causas posibles:

Se trata de un problema que se produce al enlazar métodos de Java con tipos de valor devuelto covariante. En este ejemplo, el método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` debe devolver. `Java.Lang.Object` Sin embargo, el `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` método tiene un tipo de `HttpURLConnection`valor devuelto de. Hay dos maneras de corregir este problema:

- Agregue una declaración de clase parcial `HttpURLConnectionRequestAdapter` para y implemente `IHttpRequest.Unwrap()`explícitamente:

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Quite la covarianza del código generado C# . Esto implica agregar la siguiente transformación a **Transforms\Metadata.XML** , lo que hará que C# el código generado tenga un tipo de `Java.Lang.Object`valor devuelto de:

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Colisiones de nombres en clases o propiedades internas

Visibilidad en conflicto en los objetos heredados.

En Java, no es necesario que una clase derivada tenga la misma visibilidad que su elemento primario. Java solo lo corregirá por usted. En C#, eso debe ser explícito, por lo que debe asegurarse de que todas las clases de la jerarquía tengan la visibilidad adecuada. En el ejemplo siguiente se muestra cómo cambiar el nombre de un `com.evernote.android.job` paquete `Evernote.AndroidJob`de Java de a:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: A **. por lo tanto** , la biblioteca requerida por el enlace no se está cargando

Algunos proyectos de enlace también pueden depender de la funcionalidad de una biblioteca **.** Es posible que Xamarin. Android no cargue automáticamente la biblioteca **. so** . Cuando se ejecuta el código de Java ajustado, Xamarin. Android no realizará la llamada JNI y el mensaje _de error Java. lang. UnsatisfiedLinkError: Método nativo no encontrado:_ aparecerá en el logcat de salida de la aplicación.

La solución para esto es cargar manualmente la biblioteca **. so** con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo, suponiendo que un proyecto de Xamarin. Android tiene libpocketsphinx_jni de biblioteca compartida **. por tanto** , se incluye en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (ejecutado antes de usar la biblioteca compartida) cargará la biblioteca **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumen

En este artículo se enumeran los problemas comunes de solución de problemas asociados a los enlaces de Java y se explica cómo resolverlos.

## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de biblioteca](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitar salida de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin para desarrolladores de Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
