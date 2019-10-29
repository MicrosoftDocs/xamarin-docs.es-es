---
title: Enlace de un .JAR
description: En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin. Android desde un dispositivo Android. Archivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027771"
---
# <a name="binding-a-jar"></a>Enlace de un .JAR

_En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin. Android desde un dispositivo Android. Archivo JAR._

## <a name="overview"></a>Información general

La comunidad de Android ofrece muchas bibliotecas de Java que puede usar en la aplicación. Estas bibliotecas de Java se suelen empaquetar en. Formato JAR (archivo Java), pero puede empaquetar un. JAR en una *biblioteca de enlaces de Java* para que su funcionalidad esté disponible para las aplicaciones de Xamarin. Android. El propósito de la biblioteca de enlaces de Java es crear las API en. Archivo JAR disponible para C# el código a través de contenedores de código generados automáticamente.

Las herramientas de Xamarin pueden generar una biblioteca de enlaces a partir de una o varias entradas. Archivos JAR. La biblioteca de enlaces (. Ensamblado DLL) contiene lo siguiente: 

- Contenido de la original. Archivos JAR.

- Contenedores a los que se puede llamar (MCW) C# administrados, que son tipos que contienen los tipos de Java correspondientes dentro de. Archivos JAR.

El código MCW generado usa JNI (Java Native Interface) para reenviar las llamadas API al subyacente. Archivo JAR. Puede crear bibliotecas de enlaces para cualquier. Archivo JAR que se destine originalmente para usarse con Android (tenga en cuenta que las herramientas de Xamarin no admiten actualmente el enlace de bibliotecas de Java que no son de Android). También puede optar por compilar la biblioteca de enlaces sin incluir el contenido de. Archivo JAR para que la DLL tenga una dependencia en. JAR en tiempo de ejecución.

En esta guía se detallan los conceptos básicos de la creación de una biblioteca de enlaces para un único. Archivo JAR. Veremos con un ejemplo en el que todo va bien &ndash; es decir, donde no se requiere ninguna personalización o depuración de los enlaces. 
La [creación de enlaces mediante metadatos](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) ofrece un ejemplo de un escenario más avanzado en el que el proceso de enlace no es totalmente automático y se requiere una cantidad de intervención manual. Para obtener información general sobre el enlace de la biblioteca de Java en general (con un ejemplo de código básico), vea [enlazar una biblioteca de Java](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Tutorial

En el siguiente tutorial, vamos a crear una biblioteca de enlaces para [Picasso](https://square.github.io/picasso/), un conocido Android. JAR que proporciona la funcionalidad de carga de imágenes y almacenamiento en caché. Usaremos los pasos siguientes para enlazar **Picasso-2. x. x. jar** para crear un nuevo ensamblado .net que se pueda usar en un proyecto de Xamarin. Android: 

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agregue el. Archivo JAR en el proyecto.

3. Establezca la acción de compilación adecuada para. Archivo JAR.

4. Elija una versión de .NET Framework de destino que. JAR admite.

5. Compile la biblioteca de enlaces.

Una vez que hemos creado la biblioteca de enlaces, desarrollaremos una pequeña aplicación Android que demuestra nuestra capacidad para llamar a las API en la biblioteca de enlaces. En este ejemplo, queremos acceder a los métodos de **Picasso-2. x. x. jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

Después de generar una biblioteca de enlaces para **Picasso-2. x. x. jar**, podemos llamar a estos métodos desde C#. Por ejemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Crear la biblioteca de enlaces

Antes de comenzar con los pasos siguientes, descargue [Picasso-2. x. x. jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

En primer lugar, cree un nuevo proyecto de biblioteca de enlaces. En Visual Studio para Mac o Visual Studio, cree una nueva solución y seleccione la plantilla *biblioteca de enlaces de Android* . (Las capturas de pantallas de este tutorial usan Visual Studio, pero Visual Studio para Mac es muy similar). Asigne a la solución el nombre **JarBinding**: 

[![crear un proyecto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

La plantilla incluye una carpeta **jar** donde se agrega el. JAR (s) en el proyecto de biblioteca de enlaces. Haga clic con el botón derecho en la carpeta **jar** y seleccione **Agregar > elemento existente**: 

[![Agregar elemento existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue hasta el archivo **Picasso-2. x. jar** descargado anteriormente, selecciónelo y haga clic en **Agregar**: 

[![seleccionar archivo jar y hacer clic en agregar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Compruebe que el archivo **Picasso-2. x. x. jar** se ha agregado correctamente al proyecto: 

[![jar agregado al proyecto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Al crear un proyecto de biblioteca de enlaces de Java, debe especificar si el. JAR se debe insertar en la biblioteca de enlaces o empaquetar por separado. Para ello, especifique una de las siguientes acciones de *compilación*: 

- **EmbeddedJar** &ndash;. JAR se incrustará en la biblioteca de enlaces.

- **InputJar** &ndash;. JAR se mantendrá separado de la biblioteca de enlaces.

Normalmente, se usa la acción de compilación **EmbeddedJar** para que. JAR se empaqueta automáticamente en la biblioteca de enlaces. Esta es la opción más sencilla &ndash; código de bytes de Java en. JAR se convierte en el código de bytes DEX y se incrusta (junto con los contenedores a los que se puede llamar administrados) en APK. Si desea conservar el. JAR independiente de la biblioteca de enlaces, puede usar la opción **InputJar** ; sin embargo, debe asegurarse de que el. El archivo JAR está disponible en el dispositivo que ejecuta la aplicación. 

Establezca la acción de compilación en **EmbeddedJar**: 

[![seleccionar acción de compilación de EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

A continuación, abra las propiedades del proyecto para configurar la versión de *.NET Framework de destino*. Si el. JAR usa cualquier API de Android, establezca la plataforma de destino en el nivel de API que el. JAR espera. Normalmente, el desarrollador de. El archivo JAR indicará qué nivel de API (o niveles). JAR es compatible con. (Para obtener más información sobre la configuración de la plataforma de destino y los niveles de la API de Android en general, consulte Descripción de los [niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)).

Establezca el nivel de API de destino de la biblioteca de enlaces (en este ejemplo, se usa el nivel de API 19): 

[![el nivel de API de destino establecido en API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Por último, compile la biblioteca de enlaces. Aunque se pueden mostrar algunos mensajes de advertencia, el proyecto de biblioteca de enlaces se debe compilar correctamente y generar una salida. DLL en la siguiente ubicación: **JarBinding/bin/Debug/JarBinding. dll**

### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir este. DLL en la aplicación de Xamarin. Android, haga lo siguiente:

1. Agregue una referencia a la biblioteca de enlaces.

2. Realice llamadas en. JAR a través de los contenedores a los que se puede llamar administrados. 

En los pasos siguientes, crearemos una aplicación mínima que use la biblioteca de enlaces para descargar y mostrar una imagen en un `ImageView`; el código que reside en el se encarga del "trabajo pesado". Archivo JAR. 

En primer lugar, cree una nueva aplicación de Xamarin. Android que consuma la biblioteca de enlaces. Haga clic con el botón derecho en la solución y seleccione **Agregar nuevo proyecto**. Asigne al nuevo proyecto el nombre **BindingTest**. Vamos a crear esta aplicación en la misma solución que la biblioteca de enlaces con el fin de simplificar este tutorial. sin embargo, en su lugar, la aplicación que usa la biblioteca de enlaces puede residir en una solución diferente: 

[![agregar nuevo proyecto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Haga clic con el botón secundario en el nodo **referencias** del proyecto **BindingTest** y seleccione **Agregar referencia..** .:

[![derecha Agregar referencia](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Compruebe el proyecto **JarBinding** creado anteriormente y haga clic en **Aceptar**:

[![seleccionar proyecto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra el nodo **References** del proyecto **BindingTest** y compruebe que la referencia **JarBinding** está presente: 

[![JarBinding aparece en referencias](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifique el diseño de **BindingTest** (**Main. axml**) para que tenga una única `ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Agregue la siguiente instrucción `using` a **MainActivity.cs** &ndash; esto permite acceder fácilmente a los métodos de la clase `Picasso` basada en Java que reside en la biblioteca de enlaces:

```csharp
using Com.Squareup.Picasso;
```

Modifique el método `OnCreate` para que use la clase `Picasso` para cargar una imagen de una dirección URL y mostrarla en el `ImageView`: 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compile y ejecute el proyecto **BindingTest** . La aplicación se iniciará y, después de un breve retraso (según las condiciones de la red), debe descargar y mostrar una imagen similar a la siguiente captura de pantalla:

[![captura de pantalla de BindingTest en ejecución](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

¡Enhorabuena! Ha enlazado correctamente una biblioteca de Java. JAR y usarlo en la aplicación Xamarin. Android.

## <a name="summary"></a>Resumen

En este tutorial, creamos una biblioteca de enlaces para un tercero. Archivo JAR, se ha agregado la biblioteca de enlaces a una aplicación de prueba mínima y se ha ejecutado la aplicación para C# comprobar que el código puede llamar al código de Java que reside en. Archivo JAR. 

## <a name="related-links"></a>Vínculos relacionados

- [Creación de una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
