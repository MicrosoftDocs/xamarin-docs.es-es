---
title: Enlace de una biblioteca Java
description: La comunidad de Android tiene muchas bibliotecas de Java que puede usar en la aplicación; en esta guía se explica cómo incorporar las bibliotecas de Java a la aplicación de Xamarin.Android mediante la creación de una biblioteca de enlaces.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027606"
---
# <a name="binding-a-java-library"></a>Enlace de una biblioteca Java

_La comunidad de Android tiene muchas bibliotecas de Java que puede usar en la aplicación; en esta guía se explica cómo incorporar las bibliotecas de Java a la aplicación de Xamarin.Android mediante la creación de una biblioteca de enlaces._

## <a name="overview"></a>Información general

El ecosistema de bibliotecas de terceros para Android es inmenso. Por este motivo, a menudo tiene más sentido usar una biblioteca de Android existente que crear una nueva. Xamarin.Android ofrece dos maneras de usar estas bibliotecas:

- Crear una *biblioteca de enlaces* que ajuste automáticamente la biblioteca con contenedores C# para poder invocar código Java mediante llamadas de C#

- Usar la *interfaz nativa de Java* (*JNI*) para invocar llamadas en el código de la biblioteca Java directamente JNI es una plataforma de programación que permite a código Java llamar a las aplicaciones o bibliotecas nativas y también ser llamado por estas.

En esta guía se explica la primera opción: cómo crear una *biblioteca de enlaces* que contiene una o más bibliotecas de Java existentes en un ensamblado que se puede vincular en la aplicación. Para más información sobre cómo usar JNI, consulte [Uso de JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implementa enlaces mediante *contenedores que se pueden llamar administrados* (*MCW*). MCW es un puente de JNI que se usa cuando el código administrado necesita invocar código Java. Los contenedores que se pueden llamar administrados también proporcionan compatibilidad para subclases de tipos de Java y para invalidar métodos virtuales en tipos de Java. Del mismo modo, cada vez que el código de tiempo de ejecución (ART) de Android quiere invocar código administrado, lo hace a través de otro puente de JNI conocido como contenedores que se pueden llamar de Android (ACW). Esta [arquitectura](~/android/internals/architecture.md) se muestra en el diagrama siguiente:

[![Arquitectura de puente de Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una biblioteca de enlaces es un ensamblado que contiene contenedores que se pueden llamar administrados para tipos de Java. Por ejemplo, este es un tipo de Java, `MyClass`, que queremos encapsular en una biblioteca de enlaces:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Después de generar una biblioteca de enlaces para el archivo **.jar** que contiene `MyClass`, podemos crear una instancia de la biblioteca y llamar a métodos en ella desde C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para crear esta biblioteca de enlaces, use la plantilla *Biblioteca de enlaces de Java* de Xamarin.Android. El proyecto de enlace resultante crea un ensamblado .NET con las clases de MCW, los archivos **.jar** y los recursos para los proyectos de la biblioteca Android insertados en él. También puede crear bibliotecas de enlaces para los archivos de almacenamiento Android (.AAR) y los proyectos de biblioteca de Android de Eclipse. Al hacer referencia al ensamblado de DLL de la biblioteca de enlaces resultante, puede volver a usar una biblioteca Java existente en el proyecto de Xamarin.Android.

Al hacer referencia a los tipos de la biblioteca de enlaces, debe usar el espacio de nombres de la biblioteca de enlaces. Normalmente, se agrega una directiva `using` en la parte superior de los archivos de código fuente de C#, que es la versión del espacio de nombres .NET del nombre del paquete Java. Por ejemplo, si el nombre del paquete de Java para archivo **.jar** enlazado es el siguiente:

```csharp
com.company.package
```

Entonces colocaría la siguiente instrucción `using` en la parte superior de los archivos de código fuente de C# para acceder a los tipos del archivo **.jar** enlazado:

```csharp
using Com.Company.Package;
```

Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los puntos siguientes:

- **¿Hay dependencias externas para la biblioteca?** &ndash;Las dependencias de Java que necesita la biblioteca de Android deben incluirse en el proyecto de Xamarin.Android como **ReferenceJar** o como **EmbeddedReferenceJar**. Los ensamblados nativos deben agregarse al proyecto de enlace como una biblioteca **EmbeddedNativeLibrary**.  

- **¿Qué versión de la API de Android se dirige a la biblioteca de Android?** &ndash; No es posible cambiar a una versión anterior el nivel de API de Android; asegúrese de que el proyecto de enlace de Xamarin.Android se dirija al mismo nivel de API (o superior) que la biblioteca de Android.

- **¿Qué versión del JDK se usó para compilar la biblioteca?** &ndash; Pueden producirse errores de enlace si la biblioteca de Android se compiló con una versión de JDK diferente de la que usa Xamarin.Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión del JDK que se usa para la instalación de Xamarin.Android.

## <a name="build-actions"></a>Acciones de compilación

Cuando se crea una biblioteca de enlaces, se establecen *acciones de compilación* en el archivo **.jar** o .AAR que se incorpora al proyecto de biblioteca de enlaces &ndash; cada acción de compilación determina cómo el archivo **.jar** o .AAR se insertará en la biblioteca de enlaces (o hará referencia a ella). En la lista siguiente se resumen estas acciones de compilación:

- `EmbeddedJar` &ndash; Inserta el archivo **.jar** en el ensamblado .DLL de la biblioteca de enlaces resultante como un recurso insertado. Esta es la acción de compilación más sencilla y usada con más frecuencia. Utilice esta opción si desea que el archivo **.jar** se compile automáticamente en código de bytes y se empaquete en la biblioteca de enlaces.

- `InputJar` &ndash; No inserta el archivo **.jar** en el ensamblado .DLL de la biblioteca de enlaces resultante. El ensamblado .DLL de la biblioteca de enlaces tendrá una dependencia de este archivo **.jar** en tiempo de ejecución. Utilice esta opción si no desea incluir el archivo **.jar** en la biblioteca de enlaces (por ejemplo, por motivos de licencia). Si usa esta opción, debe asegurarse de que el archivo **.jar** de entrada esté disponible en el dispositivo que ejecuta la aplicación.

- `LibraryProjectZip` &ndash; Inserta un archivo .AAR en el ensamblado .DLL de la biblioteca de enlaces resultante Esto es similar a EmbeddedJar, salvo que puede tener acceso a los recursos (así como al código) en archivo .AAR enlazado. Utilice esta opción si desea insertar un archivo .AAR en la biblioteca de enlaces.

- `ReferenceJar` &ndash; Especifica un archivo **.jar** de referencia: un archivo **.jar** de referencia es un archivo **.jar** del que depende uno de los archivos **.jar** o .AAR enlazados. Este archivo **.jar** de referencia solo se utiliza para satisfacer las dependencias de tiempo de compilación. Cuando se usa esta acción de compilación, no se crean enlaces de C# para el archivo **.jar** de referencia y no se insertan en el ensamblado .DLL de la biblioteca de enlaces resultante. Use esta opción cuando vaya a crear una biblioteca de enlaces para el archivo **.jar** de referencia pero aún no lo ha hecho. Esta acción de compilación es útil para empaquetar varios archivos **.jar** (o .AAR) en varias bibliotecas de enlaces interdependientes.

- `EmbeddedReferenceJar` &ndash; Inserta un archivo **.jar** de referencia en el ensamblado .DLL de la biblioteca de enlaces resultante. Use esta acción de compilación si desea crear enlaces de C# para el archivo **.jar** (o .AAR) de entrada y todos sus archivos **.jar** de referencia en la biblioteca de enlaces.

- `EmbeddedNativeLibrary` &ndash; Inserta un archivo **.so** nativo en el enlace. Esta acción de compilación se usa para los archivos **.so** necesarios para el archivo **.jar** que se está enlazando. Puede que sea necesario cargar manualmente la biblioteca **.so** antes de ejecutar el código de la biblioteca Java. Este procedimiento se describe a continuación.

Estas acciones de compilación se explican más detalladamente en las guías siguientes.

Además, las siguientes acciones de compilación se usan para ayudar a importar la documentación de la API de Java y convertirla en documentación XML de C#:

- `JavaDocJar` se usa para apuntar al archivo jar de almacenamiento Javadoc para una biblioteca Java que se ajusta a un estilo de paquete Maven (normalmente `FOOBAR-javadoc**.jar**`).
- `JavaDocIndex` se usa para apuntar a un archivo `index.html` en el HTML de documentación de referencia de la API.
- `JavaSourceJar` se utiliza para complementar `JavaDocJar`, para generar primero un JavaDoc a partir del código fuente y, a continuación, tratar el resultado como `JavaDocIndex`, para una biblioteca Java que se ajusta a un estilo de paquete Maven (normalmente `FOOBAR-sources**.jar**`).

La documentación de la API debe ser el doclet predeterminado de los SDK de Java8, Java7 o Java6 (tienen el mismo formato) o el estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusión de una biblioteca nativa en un enlace

Puede que sea necesario incluir una biblioteca **.so** de un proyecto de enlace de Xamarin.Android como parte del enlace de una biblioteca Java. Cuando se ejecuta el código de Java encapsulado, Xamarin.Android no podrá realizar la llamada JNI y el mensaje de error _java.lang.UnsatisfiedLinkError: Native method not found:_ (Método nativo no encontrado) aparecerá en el registro logcat fuera de la aplicación.

Para solucionar el error, cargue manualmente la biblioteca **.so** con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo, suponiendo que en un proyecto de Xamarin.Android esté incluida la biblioteca **libpocketsphinx_jni.so** en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (ejecutado antes de usar la biblioteca compartida) cargará la biblioteca **.so**:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptación de las API de Java a C&eparsl;

El generador de enlaces de Xamarin.Android cambiará algunas expresiones y patrones de Java para que se correspondan con los patrones de .NET. En la lista siguiente se describe cómo se asigna Java a C# o .NET:

- _Los métodos Setter/Getter_ en Java son _propiedades_ en .NET.

- _Los campos_ en Java son _propiedades_ en .NET.

- _Los clientes de escucha o las interfaces de los clientes de escucha_ en Java son _eventos_ en .NET. Los parámetros de los métodos de las interfaces de devolución de llamada se representan mediante una subclase `EventArgs`.

- Una _clase anidada estática_ en Java es una _clase anidada_ en .NET.

- Una _clase interna_ en Java es una _clase anidada_ con un constructor de instancia en C#.

## <a name="binding-scenarios"></a>Escenarios de enlace de datos

Las siguientes guías de escenarios de enlace pueden ayudarle a enlazar una biblioteca (o bibliotecas) Java para su incorporación a su aplicación:

- [Enlace de un archivo .JAR](~/android/platform/binding-java-library/binding-a-jar.md) es un tutorial para crear bibliotecas de enlaces para archivos **.jar**.

- [Enlace de un archivo .AAR](~/android/platform/binding-java-library/binding-an-aar.md) es un tutorial para crear bibliotecas de enlaces para archivos .AAR. Lea este tutorial para aprender a enlazar bibliotecas de Android Studio.

- [Enlace de un proyecto de biblioteca de Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) es un tutorial para crear bibliotecas de enlace desde proyectos de biblioteca de Android. Lea este tutorial para aprender a enlazar proyectos de biblioteca de Android de Eclipse.

- [Personalización de enlaces](~/android/platform/binding-java-library/customizing-bindings/index.md) explica cómo realizar modificaciones manuales en el enlace para resolver errores de compilación y dar forma a la API resultante para que sea más "similar a C#".

- [Solución de problemas de enlaces](~/android/platform/binding-java-library/troubleshooting-bindings.md) enumera escenarios comunes de errores de los enlaces, explica las posibles causas y ofrece sugerencias para resolver estos errores.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadatos GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso de bibliotecas nativas](~/android/platform/native-libraries.md)
