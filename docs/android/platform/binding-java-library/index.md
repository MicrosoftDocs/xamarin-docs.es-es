---
title: Enlace de una biblioteca Java
description: La comunidad de Android tiene muchas bibliotecas de Java que puede que quiera usar en la aplicación; en esta guía se explica cómo incorporar bibliotecas de Java a la aplicación de Xamarin. Android mediante la creación de una biblioteca de enlaces.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: b795a53fc78adee19e1e2d1c57c9c4344aa4281b
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119640"
---
# <a name="binding-a-java-library"></a>Enlace de una biblioteca Java

_La comunidad de Android tiene muchas bibliotecas de Java que puede que quiera usar en la aplicación; en esta guía se explica cómo incorporar bibliotecas de Java a la aplicación de Xamarin. Android mediante la creación de una biblioteca de enlaces._

## <a name="overview"></a>Información general

El ecosistema de bibliotecas de terceros para Android es masivo. Por este motivo, a menudo tiene sentido usar una biblioteca de Android existente que crear una nueva. Xamarin. Android ofrece dos maneras de usar estas bibliotecas:

- Cree una *biblioteca de enlaces* que ajuste automáticamente la biblioteca con contenedores C# para que pueda invocar código Java a través C# de llamadas.

- Use la *interfaz nativa de Java* (*JNI*) para invocar llamadas en el código de la biblioteca de Java directamente. JNI es un marco de programación que permite a código Java llamar a las aplicaciones o bibliotecas nativas y llamarlas.

En esta guía se explica la primera opción: Cómo crear una *biblioteca de enlaces* que incluya una o más bibliotecas de Java existentes en un ensamblado al que se pueda vincular en la aplicación. Para obtener más información sobre el uso de JNI, consulte [trabajar con JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin. Android implementa enlaces mediante contenedores a los que se *puede llamar* (*MCW*) administrados. MCW es un puente de JNI que se usa cuando el código administrado necesita invocar código Java. Los contenedores a los que se puede llamar administrados también proporcionan compatibilidad para subclases de tipos de Java y para invalidar métodos virtuales en tipos de Java. Del mismo modo, cada vez que el código de tiempo de ejecución (ART) de Android quiere invocar código administrado, lo hace a través de otro puente de JNI conocido como contenedores de Android Calling (ACW). Esta [arquitectura](~/android/internals/architecture.md) se ilustra en el diagrama siguiente:

[![Arquitectura de puente de Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Una biblioteca de enlaces es un ensamblado que contiene contenedores a los que se puede llamar administrados para tipos de Java. Por ejemplo, este es un tipo de Java `MyClass`,, que queremos encapsular en una biblioteca de enlaces:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Después de generar una biblioteca de enlaces para el archivo **. jar** que `MyClass`contiene, podemos crear una instancia de él y llamar a métodos C#en él desde:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Para crear esta biblioteca de enlaces, use la plantilla *biblioteca de enlaces de Java* de Xamarin. Android. El proyecto de enlace resultante crea un ensamblado .NET con las clases de MCW, los archivos **. jar** y los recursos para los proyectos de biblioteca Android insertados en él. También puede crear bibliotecas de enlaces para el archivo Android (. AAR) y los proyectos de biblioteca de Android para Eclipse. Al hacer referencia al ensamblado de DLL de la biblioteca de enlaces resultante, puede volver a usar una biblioteca de Java existente en el proyecto de Xamarin. Android.

Al hacer referencia a los tipos de la biblioteca de enlaces, debe usar el espacio de nombres de la biblioteca de enlaces. Normalmente, se agrega una `using` Directiva en la parte superior de C# los archivos de código fuente que es la versión del espacio de nombres .net del nombre del paquete java. Por ejemplo, si el nombre del paquete de Java para el archivo Bound **. jar** es el siguiente:

```csharp
com.company.package
```

A continuación, colocaría la `using` siguiente instrucción en la parte superior C# de los archivos de código fuente para obtener acceso a los tipos del archivo **. jar** enlazado:

```csharp
using Com.Company.Package;
```


Al enlazar una biblioteca de Android existente, es necesario tener en cuenta los puntos siguientes:

- **¿Hay dependencias externas para la biblioteca?** &ndash;Todas las dependencias de Java que requiere la biblioteca de Android deben incluirse en el proyecto de Xamarin. Android como **ReferenceJar** o como **EmbeddedReferenceJar**. Los ensamblados nativos deben agregarse al proyecto de enlace como un **EmbeddedNativeLibrary**.  

- **¿Qué versión de la API de Android es el destino de la biblioteca de Android?** &ndash;No es posible "degradar" el nivel de API de Android; Asegúrese de que el proyecto de enlace de Xamarin. Android tenga como destino el mismo nivel de API (o superior) que la biblioteca de Android.

- **¿Qué versión del JDK se usó para compilar la biblioteca?** &ndash;Pueden producirse errores de enlace si la biblioteca de Android se compiló con una versión de JDK diferente de la que usa Xamarin. Android. Si es posible, vuelva a compilar la biblioteca de Android con la misma versión del JDK que usa la instalación de Xamarin. Android.


## <a name="build-actions"></a>Acciones de compilación

Cuando se crea una biblioteca de enlaces, se establecen *acciones de compilación* en el archivo **. jar** o. Los archivos AAR que se incorporan en el proyecto &ndash; de biblioteca de enlaces cada acción de compilación determinan cómo **. jar** o. El archivo AAR se incrustará en la biblioteca de enlaces (o hará referencia a ella). En la lista siguiente se resumen estas acciones de compilación:

- `EmbeddedJar`Inserta el archivo **. jar** en la dll de la biblioteca de enlaces resultante como un recurso incrustado. &ndash; Esta es la acción de compilación más sencilla y usada con más frecuencia. Use esta opción cuando desee que el archivo **. jar** se compile automáticamente en el código de bytes y se empaquete en la biblioteca de enlaces.

- `InputJar`No inserta el archivo **. jar** en la biblioteca de enlaces resultante. &ndash; DLL. La biblioteca de enlaces. DLL tendrá una dependencia de este archivo **. jar** en tiempo de ejecución. Utilice esta opción si no desea incluir el archivo **. jar** en la biblioteca de enlaces (por ejemplo, por motivos de licencia). Si usa esta opción, debe asegurarse de que el archivo INPUT **. jar** está disponible en el dispositivo que ejecuta la aplicación.

- `LibraryProjectZip`&ndash; Inserta un. Archivo AAR en la biblioteca de enlaces resultante. DLL. Esto es similar a EmbeddedJar, salvo que puede tener acceso a los recursos (así como al código) en el enlazado. Archivo AAR. Utilice esta opción si desea incrustar un. AAR en la biblioteca de enlaces.

- `ReferenceJar` Especifica un archivo Reference. jar: un archivo Reference. jar es un archivo. jar que es un archivo. jar enlazado. &ndash; Los archivos AAR dependen de. Esta referencia **. jar** solo se utiliza para satisfacer las dependencias de tiempo de compilación. Cuando se usa esta acción de compilación C# , no se crean enlaces para Reference **. jar** y no se insertan en la biblioteca de enlaces resultante. DLL. Use esta opción cuando vaya a crear una biblioteca de enlaces para el archivo Reference **. jar** pero aún no lo haya hecho. Esta acción de compilación es útil para empaquetar varios **. jar**s (y/o. AARs) en varias bibliotecas de enlaces interdependientes.

- `EmbeddedReferenceJar`Inserta un archivo Reference **. jar** en la biblioteca de enlaces resultante. &ndash; DLL. Use esta acción de compilación cuando desee crear C# enlaces para INPUT **. jar** (o. AAR) y todas sus referencias **. jar**(s) en la biblioteca de enlaces.

- `EmbeddedNativeLibrary`Inserta un objeto nativo **. así** en el enlace. &ndash; Esta acción de compilación se usa para **. por tanto,** los archivos que requiere el archivo **. jar** que se está enlazando. Puede que sea necesario cargar manualmente la biblioteca **. so** antes de ejecutar el código de la biblioteca de Java. Esto se describe a continuación.

Estas acciones de compilación se explican con más detalle en las siguientes guías.

Además, las siguientes acciones de compilación se usan para ayudar a importar la documentación de la API C# de Java y convertirlas en documentación XML:

- `JavaDocJar`se usa para apuntar al archivo jar de archivo Javadoc para una biblioteca Java que se ajusta a un estilo de paquete `FOOBAR-javadoc**.jar**`Maven (normalmente).
- `JavaDocIndex`se usa para apuntar `index.html` al archivo en el HTML de documentación de referencia de la API.
- `JavaSourceJar`se utiliza para complementar `JavaDocJar`, para generar primero Javadoc a partir de orígenes y, a continuación, tratar los resultados como `JavaDocIndex`, para una biblioteca Java que se ajusta a un `FOOBAR-sources**.jar**`estilo de paquete Maven (normalmente).

La documentación de la API debe ser el valor predeterminado de doclet de Java8, Java7 o java6 SDK (tienen el mismo formato) o el estilo DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Incluir una biblioteca nativa en un enlace

Puede que sea necesario incluir una biblioteca **. so** en un proyecto de enlace de Xamarin. Android como parte del enlace de una biblioteca de Java. Cuando se ejecuta el código de Java ajustado, Xamarin. Android no realizará la llamada JNI y el mensaje _de error Java. lang. UnsatisfiedLinkError: Método nativo no encontrado:_ aparecerá en el logcat de salida de la aplicación.

La solución para esto es cargar manualmente la biblioteca **. so** con una llamada a `Java.Lang.JavaSystem.LoadLibrary`. Por ejemplo, suponiendo que un proyecto de Xamarin. Android tiene libpocketsphinx_jni de biblioteca compartida **. por tanto** , se incluye en el proyecto de enlace con una acción de compilación de **EmbeddedNativeLibrary**, el siguiente fragmento de código (ejecutado antes de usar la biblioteca compartida) cargará la biblioteca **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptación de API de Java a C&eparsl;

El generador de enlaces de Xamarin. Android cambiará algunas expresiones y patrones de Java para que se correspondan con los patrones de .NET. En la siguiente lista se describe cómo se asigna C#Java a/.net:

- _Los métodos Setter/getter_ de Java son _propiedades_ de .net.

- _Los campos_ de Java son _propiedades_ de .net.

- Los agentes de escucha _o las interfaces de escucha_ en Java son _eventos_ en .net. Los parámetros de los métodos de las interfaces de devolución de llamada se `EventArgs` representarán mediante una subclase.

- Una _clase anidada estática_ en Java es una _clase anidada_ en .net.

- Una _clase interna_ en Java es una _clase anidada_ con un constructor de instancia C#en.



## <a name="binding-scenarios"></a>Escenarios de enlace

Las siguientes guías de escenarios de enlace pueden ayudarle a enlazar una biblioteca de Java (o bibliotecas) para su incorporación a su aplicación:

- [Enlazar un. JAR](~/android/platform/binding-java-library/binding-a-jar.md) es un tutorial para crear bibliotecas de enlaces para archivos **. jar** .

- [Enlazar un. AAR](~/android/platform/binding-java-library/binding-an-aar.md) es un tutorial para crear bibliotecas de enlaces para. Archivos AAR. Lea este tutorial para aprender a enlazar bibliotecas de Android Studio.

- [Enlazar un proyecto de biblioteca Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) es un tutorial para crear bibliotecas de enlace desde proyectos de biblioteca de Android. Lea este tutorial para aprender a enlazar proyectos de biblioteca de Android de Eclipse.

- La personalización de [enlaces](~/android/platform/binding-java-library/customizing-bindings/index.md) explica cómo realizar modificaciones manuales en el enlace para resolver errores de compilación y dar forma a la API resultante para que seaC#más "similar".

- [Solución de problemas de enlaces](~/android/platform/binding-java-library/troubleshooting-bindings.md) muestra escenarios comunes de error de enlace, explica las posibles causas y ofrece sugerencias para resolver estos errores.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Metadatos de GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Uso de bibliotecas nativas](~/android/platform/native-libraries.md)
