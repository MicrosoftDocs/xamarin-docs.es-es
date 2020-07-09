---
title: Solución de problemas de enlaces
description: En este artículo se resumen varios errores comunes que pueden producirse al generar enlaces, junto con posibles causas y formas sugeridas de resolverlos.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3ec01d9322d4b6706fccd0959ae5944f71e82664
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853021"
---
# <a name="troubleshooting-bindings"></a>Solución de problemas de enlaces

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

_En este artículo se resumen varios errores comunes que pueden producirse al generar enlaces, junto con posibles causas y formas sugeridas de resolverlos._

## <a name="overview"></a>Información general

Enlazar una biblioteca de Android (un archivo **.aar** o **.jar**) rara vez resulta sencillo; normalmente requiere el trabajo adicional de mitigar los problemas derivados de las diferencias entre Java y .NET.
Estos problemas impedirán que Xamarin.Android enlace la biblioteca de Android y se presentarán como mensajes de error en el registro de compilación. En esta guía se ofrecen algunas sugerencias para solucionar los problemas, se enumeran algunos de los problemas o escenarios más comunes y se proporcionan posibles soluciones para enlazar correctamente la biblioteca de Android.

Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los puntos siguientes:

- **Las dependencias externas de la biblioteca**: las dependencias de Java que necesita la biblioteca de Android deben incluirse en el proyecto de Xamarin.Android como **ReferenceJar** o como **EmbeddedReferenceJar**.

- **El nivel de API de Android al que se dirige la biblioteca de Android**: no es posible "degradar" el nivel de API de Android; asegúrese de que el proyecto de enlace de Xamarin.Android se dirija al mismo nivel de API (o superior) que la biblioteca de Android.

- **La versión del JDK de Android que se usó para empaquetar la biblioteca de Android**: se pueden producir errores de enlace si la biblioteca de Android se compiló con una versión del JDK distinta de la que se usa en Xamarin.Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión del JDK que se usa para la instalación de Xamarin.Android.

El primer paso para la solución de problemas con el enlace de una biblioteca de Xamarin.Android es habilitar la [salida de MSBuild de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Después de habilitar la salida de diagnóstico, vuelva a compilar el proyecto de enlace de Xamarin.Android y examine el registro de compilación para buscar pistas sobre cuál es la causa del problema.

También puede resultar útil descompilar la biblioteca de Android y examinar los tipos y métodos que Xamarin.Android intenta enlazar. Este tema se trata con mayor detalle en esta guía.

## <a name="decompiling-an-android-library"></a>Descompilación de una biblioteca de Android

La inspección de las clases y los métodos de las clases de Java puede proporcionar información valiosa que le ayudará en el enlace de una biblioteca.
[JD-GUI](http://jd.benow.ca/) es una utilidad gráfica que puede mostrar el código fuente de Java de los archivos **CLASS** contenidos en un archivo JAR. Se puede ejecutar como una aplicación independiente o como un complemento para IntelliJ o Eclipse.

Para descompilar una biblioteca de Android, abra el archivo **.JAR** con el descompilador de Java. Si la biblioteca es un archivo **.AAR**, es necesario extraer el archivo **classes.jar** del archivo de almacenamiento. A continuación se muestra una captura de pantalla de ejemplo del uso de JD-GUI para analizar el archivo JAR de [Picasso](https://square.github.io/picasso/):

![Uso del descompilador de Java para analizar picasso-2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Cuando haya descompilado la biblioteca de Android, examine el código fuente. En términos generales, busque:

- **Clases que tengan características de ofuscación**: las características de las clases ofuscadas incluyen:

  - El nombre de clase incluye un símbolo **$** , es decir, **a$.class**.
  - El nombre de clase está formado totalmente por caracteres en minúsculas, es decir, **a.class**.      

- Instrucciones **`import` para bibliotecas sin referencias**: identifique la biblioteca sin referencias y agregue esas dependencias al proyecto de enlace de Xamarin.Android con una **acción de compilación** de **ReferenceJar** o **EmbedddedReferenceJar**.

> [!NOTE]
> La descompilación de una biblioteca de Java puede estar prohibida o sujeta a restricciones legales según las leyes locales o la licencia bajo la que esta se publica. Si es necesario, inscriba los servicios de un profesional jurídico antes de intentar descompilar una biblioteca de Java e inspeccionar su código fuente.

## <a name="inspect-apixml"></a>Inspección de API.XML

Como parte de la compilación de un proyecto de enlace, Xamarin.Android generará un nombre de archivo XML **obj/Debug/api.XML**:

![Archivo api.xml generado en obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Este archivo proporciona una lista de todas las API de Java que está intentando enlazar Xamarin.Android. El contenido de este archivo puede ayudar a identificar los tipos o métodos que faltan o los enlaces duplicados. Aunque la inspección de este archivo es tediosa y lleva tiempo, puede proporcionar pistas sobre lo que puede estar causando los problemas de enlace. Por ejemplo, **api.xml** podría revelar que una propiedad devuelve un tipo inadecuado o que hay dos tipos que comparten el mismo nombre administrado.

## <a name="known-issues"></a>Problemas conocidos

En esta sección se enumeran algunos de los mensajes de error o síntomas comunes que se producen al intentar enlazar una biblioteca de Android.

### <a name="problem-java-version-mismatch"></a>Problema: Incoherencia de la versión de Java

A veces, los tipos no se generan o se bloquean de manera inesperada porque se usa una versión más reciente o antigua de Java en comparación con aquella con la que se compiló la biblioteca. Vuelva a compilar la biblioteca de Android con la misma versión del JDK que la que usa el proyecto de Xamarin.Android.

### <a name="problem-at-least-one-java-library-is-required"></a>Problema: Se necesita al menos una biblioteca de Java

Recibe un error que indica que se necesita al menos una biblioteca de Java, aunque se ha agregado un archivo .JAR.

#### <a name="possible-causes"></a>Causas posibles:

Asegúrese de que la acción de compilación está establecida en `EmbeddedJar`. Dado que hay varias acciones de compilación para archivos .JAR (como `InputJar`, `EmbeddedJar`, `ReferenceJar` y `EmbeddedReferenceJar`), el generador de enlaces no puede adivinar automáticamente cuál usar de forma predeterminada. Para más información sobre las acciones de compilación, consulte [Acciones de compilación](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problema: Las herramientas de enlace no pueden cargar la biblioteca .JAR

El generador de bibliotecas de enlace no puede cargar la biblioteca .JAR.

#### <a name="possible-causes"></a>Causas posibles

Algunas bibliotecas .JAR que usan la ofuscación de código (mediante herramientas como Proguard) no se pueden cargar con las herramientas de Java. Dado que nuestra herramienta hace uso de la reflexión de Java y de la biblioteca de ingeniería de código de byte de ASM, esas herramientas dependientes pueden rechazar las bibliotecas ofuscadas mientras que las herramientas de tiempo de ejecución de Android pueden aceptarlas. La solución para ello es enlazar a mano estas bibliotecas en lugar de usar el generador de enlaces.

### <a name="problem-missing-c-types-in-generated-output"></a>Problema: Tipos de C# que faltan en la salida generada.

La **.dll** de enlace compila algunos tipos Java pero hay otros que faltan, o el origen de C# generado no se compila debido a un error que dice que hay tipos que faltan.

#### <a name="possible-causes"></a>Causas posibles:

Este error puede producirse por varias razones, como se indica a continuación:

- La biblioteca que se está enlazando puede hacer referencia a una segunda biblioteca de Java. Si la API pública de la biblioteca enlazada usa tipos de la segunda biblioteca, también debe hacer referencia a un enlace administrado para la segunda biblioteca.

- Es posible que se haya insertado una biblioteca debido a la reflexión de Java, de forma parecida a la causa del error anterior de carga de la biblioteca, lo que provocaría la carga inesperada de los metadatos. Actualmente, las herramientas de Xamarin.Android no pueden resolver esta situación. En tal caso, la biblioteca se debe enlazar manualmente.

- Hubo un error en el entorno de ejecución de .NET 4.0 por el que no se pudieron cargar los ensamblados cuando se debería haber hecho. Este problema se ha corregido en el entorno de ejecución de .NET 4.5.

- Java permite derivar una clase pública de una clase no pública, pero esto no se admite en .NET. Dado que el generador de enlaces no genera enlaces para clases no públicas, las clases derivadas como estas no se pueden generar correctamente. Para corregirlo, quite la entrada de metadatos de esas clases derivadas mediante remove-node en **Metadata.xml**, o bien corrija los metadatos que hacen que la clase no pública sea pública. Aunque la última solución creará el enlace para que se compile el origen de C#, no se debe usar la clase no pública.

  Por ejemplo:

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Las herramientas que ofuscan las bibliotecas de Java pueden interferir con el generador de enlaces de Xamarin.Android y su capacidad para generar clases contenedoras de C#. El siguiente fragmento de código muestra cómo actualizar **Metadata.xml** para no ofuscar un nombre de clase:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problema: El origen de C# generado no se compila debido a un error de coincidencia de los tipos de parámetro

El origen de C# generado no se compila. Los tipos de parámetros del método invalidado no coinciden.

#### <a name="possible-causes"></a>Causas posibles:

Xamarin.Android incluye diversos campos de Java que se asignan a enumeraciones en los enlaces de C#. Estos campos pueden provocar incompatibilidades de tipos en los enlaces generados. Para resolver este problema, las signaturas de método creadas desde el generador de enlaces deben modificarse para usar las enumeraciones. Para más información, consulte [Corrección de enumeraciones](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problema: NoClassDefFoundError en el empaquetado

Se produce el error `java.lang.NoClassDefFoundError` en el paso de empaquetado.

#### <a name="possible-causes"></a>Causas posibles:

La razón más probable de este error es que sea necesario agregar una biblioteca de Java obligatoria al proyecto de aplicación ( **.csproj**). Los archivos JAR no se resuelven automáticamente. Un enlace de la biblioteca de Java no se genera siempre en un ensamblado de usuario que no exista en el emulador o dispositivo de destino (como **Maps.jar** de Google Maps). Esto no ocurre en el caso de la compatibilidad con proyectos de la biblioteca de Android, dado que la biblioteca JAR está incrustada en la DLL de la biblioteca. Por ejemplo: [Error 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problema: Tipos de EventArgs personalizados duplicados

Se produce un error en la compilación debido a tipos EventArgs personalizados duplicados. Verá un error como este:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causas posibles:

Esto se debe a que hay conflictos entre los tipos de eventos que proceden de más de un tipo de "cliente de escucha" de la interfaz que comparte métodos que tienen nombres idénticos. Por ejemplo, si hay dos interfaces Java como se muestra en el ejemplo siguiente, el generador crea `DismissScreenEventArgs` para `MediationBannerListener` y `MediationInterstitialListener`, lo que produce el error.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Esto es así por diseño para evitar nombres largos en los tipos de argumentos de eventos. Para evitar estos conflictos, es necesario transformar los metadatos. Edite **Transforms\Metadata.xml** y agregue un atributo `argsType` en cualquiera de las interfaces (o en el método de la interfaz):

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

### <a name="problem-class-does-not-implement-interface-method"></a>Problema: La clase no implementa el método de la interfaz

Se produce un mensaje de error que indica que una clase generada no implementa un método que sea necesario para una interfaz que implementa la clase generada. Sin embargo, si examina el código generado, puede ver que el método está implementado.

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

Este es un problema que se produce con los métodos Java de enlace con tipos de valor devuelto de covariante. En este ejemplo, el método `Oauth.Signpost.Http.IHttpRequest.UnWrap()` debe devolver `Java.Lang.Object`. Sin embargo, el método `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` tiene un tipo de valor devuelto de `HttpURLConnection`. Existen dos formas de solucionar este problema:

- Agregar una declaración de clase parcial para `HttpURLConnectionRequestAdapter` e implementar `IHttpRequest.Unwrap()` de manera explícita:

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Quitar la covarianza del código de C# generado. Esto supone agregar la siguiente transformación a **Transforms\Metadata.xml**, lo que hará que el código de C# generado tenga un tipo de valor devuelto de `Java.Lang.Object`:

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problema: Colisiones de nombres en clases y propiedades internas

Conflicto de visibilidad en objetos heredados.

En Java, no es necesario que una clase derivada tenga la misma visibilidad que su elemento principal. Java lo corregirá automáticamente. En C#, eso ha de ser explícito, por lo que debe asegurarse de que todas las clases de la jerarquía tengan la visibilidad adecuada. En el siguiente ejemplo se muestra cómo cambiar el nombre de un paquete de Java de `com.evernote.android.job` a `Evernote.AndroidJob`.

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problema: Una biblioteca **.so** que requiere el enlace no se está cargando

Algunos proyectos de enlace también pueden depender de la funcionalidad de una biblioteca **.so**. Es posible que Xamarin.Android no cargue automáticamente la biblioteca **.so**. Cuando se ejecuta el código de Java encapsulado, Xamarin.Android no podrá realizar la llamada JNI y el mensaje de error _java.lang.UnsatisfiedLinkError: Native method not found:_ (Método nativo no encontrado) aparecerá en el registro logcat fuera de la aplicación.

Para solucionar el error, cargue manualmente la biblioteca **.so** con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo, suponiendo que en un proyecto de Xamarin.Android esté incluida la biblioteca **libpocketsphinx_jni.so** en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (ejecutado antes de usar la biblioteca compartida) cargará la biblioteca **.so**:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Resumen

En este artículo se enumera la solución de problemas comunes asociados a los enlaces de Java y se explica cómo resolverlos.

## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de biblioteca](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Habilitación de la salida de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Desarrolladores de Xamarin para Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
