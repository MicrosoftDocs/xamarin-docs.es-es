---
title: Enlace de un .JAR
description: En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android desde un archivo Android .JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 336462176813b9985e2c269273d08c4aacbea9b5
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853097"
---
# <a name="binding-a-jar"></a>Enlace de un .JAR

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

_En este tutorial se proporcionan instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android desde un archivo Android .JAR._

## <a name="overview"></a>Información general

La comunidad de Android ofrece muchas bibliotecas de Java que puede usar en la aplicación. Estas bibliotecas Java se suelen empaquetar en formato .JAR (archivo Java), pero puede empaquetar un archivo .JAR en una *biblioteca de enlaces de Java* para que su funcionalidad esté disponible para las aplicaciones de Xamarin.Android. El propósito de la biblioteca de enlaces de Java es crear las API en el archivo .JAR disponible para el código C# a través de contenedores de código generados automáticamente.

Las herramientas de Xamarin pueden generar una biblioteca de enlaces a partir de uno o varios archivos .JAR. La biblioteca de enlaces (ensamblado .DLL) contiene lo siguiente: 

- El contenido de los archivos .JAR originales.

- Contenedores que se puede llamar administrados (MCW), que son tipos de C# que contienen los tipos de Java correspondientes dentro de archivos .JAR.

El código MCW generado usa JNI (Java Native Interface) para reenviar las llamadas API al archivo .JAR subyacente. Puede crear bibliotecas de enlaces para cualquier archivo .JAR que se destine originalmente para usarse con Android (tenga en cuenta que las herramientas de Xamarin no admiten actualmente el enlace de bibliotecas de Java que no son de Android). También puede optar por compilar la biblioteca de enlaces sin incluir el contenido del archivo .JAR para que el ensamblado DLL tenga una dependencia en el archivo .JAR en tiempo de ejecución.

En esta guía se detallan los conceptos básicos de la creación de una biblioteca de enlaces para un único archivo .JAR. Lo ilustraremos con un ejemplo donde todo va bien &ndash; es decir, donde no se requiere personalización ni depuración de los enlaces. 
[La creación de enlaces mediante metadatos](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) ofrece un ejemplo de un escenario más avanzado en el que el proceso de enlace no es totalmente automático y se requiere una cantidad de intervención manual. Para obtener información general sobre el enlace de la biblioteca Java en general (con un ejemplo de código básico), consulte [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md). 

## <a name="walkthrough"></a>Tutorial

En el siguiente tutorial, vamos a crear una biblioteca de enlaces para [Picasso](https://square.github.io/picasso/), un conocido archivo .JAR de Android que proporciona la funcionalidad de carga de imágenes y almacenamiento en caché. Usaremos los pasos siguientes para enlazar **picasso-2.x.x.jar** para crear un nuevo ensamblado .NET que se pueda usar en un proyecto de Xamarin.Android: 

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agregue el archivo .JAR al proyecto.

3. Establezca la acción de compilación adecuada para el archivo .JAR.

4. Elija una plataforma de destino que admita .JAR.

5. Cree la biblioteca de enlaces.

Cuando hayamos creado la biblioteca de enlaces, desarrollaremos una pequeña aplicación Android que muestra nuestra capacidad para llamar a las API en la biblioteca de enlaces. En este ejemplo, queremos acceder a los métodos de **picasso-2.x.x.jar**:

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

Después de generar una biblioteca de enlaces para **picasso-2.x.x.jar**, podemos llamar a estos métodos desde C#. Por ejemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>Creación de la biblioteca de enlaces

Antes de comenzar con los pasos siguientes, descargue [picasso-2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

En primer lugar, cree un nuevo proyecto de biblioteca de enlaces. En Visual Studio para Mac o Visual Studio, cree una nueva solución y seleccione la plantilla *biblioteca de enlaces de Android*. (Las capturas de pantallas de este tutorial usan Visual Studio, pero Visual Studio para Mac es muy similar). Asigne el nombre **JarBinding** a la solución: 

[![Creación de un proyecto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

La plantilla incluye una carpeta **Jars** donde se agregan los archivos .JAR en el proyecto de biblioteca de enlaces. Haga clic con el botón derecho en la carpeta **Jars** y seleccione **Agregar > Elemento existente**: 

[![Agregar elemento existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Vaya al archivo **picasso-2.x.x.jar** descargado anteriormente, selecciónelo y haga clic en **Agregar**: 

[![Seleccionar el archivo jar y hacer clic en Agregar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Compruebe que el archivo **picasso-2.x.x.jar** se haya agregado correctamente al proyecto: 

[![Jar agregado al proyecto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Al crear un proyecto de biblioteca de enlaces de Java, debe especificar si el .JAR se debe insertar en la biblioteca de enlaces o empaquetar por separado. Para ello, especifique una de las siguientes *acciones de compilación*: 

- **EmbeddedJar** &ndash;el .JAR se insertará en la biblioteca de enlaces.

- **InputJar** &ndash; el .JAR se mantendrá separado de la biblioteca de enlaces.

Normalmente, se usa la acción de compilación **EmbeddedJar** para que .JAR se empaquete automáticamente en la biblioteca de enlaces. Esta es la opción más sencilla &ndash; el código de bytes de Java en .JAR se convierte en el código de bytes Dex y se inserta (junto con los contenedores que se puede llamar administrados) en APK. Si desea conservar el .JAR separado de la biblioteca de enlaces, puede usar la opción **InputJar**; sin embargo, debe asegurarse de que el archivo .JAR está disponible en el dispositivo que ejecuta la aplicación. 

Establezca la acción de compilación en **EmbeddedJar**: 

[![Selección de la acción de compilación EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

A continuación, abra las propiedades del proyecto para configurar la *plataforma de destino*. Si el .JAR usa cualquier API de Android, establezca la plataforma de destino en el nivel de API que el .JAR espera. Normalmente, el desarrollador del archivo .JAR indicará qué nivel (o niveles) de API es compatible con .JAR. (Para obtener más información sobre la configuración de la plataforma de destino y los niveles de la API de Android en general, consulte [Descripción de los niveles de la API de Android](~/android/app-fundamentals/android-api-levels.md)).

Establezca el nivel de la API de destino de la biblioteca de enlaces (en este ejemplo, se usa el nivel 19 de la API): 

[![Nivel de la API de destino establecido en API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

Por último, cree la biblioteca de enlaces. Aunque se pueden mostrar algunos mensajes de advertencia, el proyecto de biblioteca de enlaces debe crearse correctamente y generar un ensamblado .DLL de salida en la siguiente ubicación: **JarBinding/bin/Debug/JarBinding.dll**

### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir este ensamblado .DLL en la aplicación de Xamarin.Android, haga lo siguiente:

1. Agregue una referencia a la biblioteca de enlaces.

2. Realice llamadas al .JAR mediante los contenedores que se pueden llamar administrados. 

En los pasos siguientes, crearemos una aplicación mínima que use la biblioteca de enlaces para descargar y mostrar una imagen en un objeto `ImageView`; el código que reside en el archivo .JAR se encarga del "trabajo pesado". 

En primer lugar, cree una nueva aplicación de Xamarin.Android que consuma la biblioteca de enlaces. Haga clic con el botón derecho en la solución y seleccione **Agregar nuevo proyecto**; dele al proyecto el nombre de **BindingTest**. Vamos a crear esta aplicación en la misma solución que la biblioteca de enlaces con el fin de simplificar este tutorial. Sin embargo, en su lugar, la aplicación que usa la biblioteca de enlaces puede residir en una solución diferente: 

[![Adición de nuevo proyecto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Haga clic con el botón derecho en el nodo **Referencias** del proyecto **BindingTest** y seleccione **Agregar referencia...** :

[![Agregar referencia en el menú contextual](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Compruebe el proyecto **JarBinding** creado anteriormente y haga clic en **Aceptar**:

[![Selección del proyecto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra el nodo **Referencias** del proyecto **BindingTest** y compruebe que la referencia **JarBinding** está presente: 

[![JarBinding aparece en las referencias](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modifique el diseño de **BindingTest** (**Main.axml**) para que tenga un solo objeto `ImageView`:

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

Modifique el método `OnCreate` para que use la clase `Picasso` para cargar una imagen de una dirección URL y mostrarla en el objeto `ImageView`: 

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

Compile y ejecute el proyecto **BindingTest**. La aplicación se iniciará y, después de un breve retraso (según las condiciones de la red), debe descargar y mostrar una imagen similar a la siguiente captura de pantalla:

[![Captura de pantalla de BindingTest en ejecución](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

¡Enhorabuena! Ha enlazado correctamente un archivo .JAR de la biblioteca Java y usarlo en la aplicación Xamarin.Android.

## <a name="summary"></a>Resumen

En este tutorial, hemos creado una biblioteca de enlaces para un archivo .JAR de terceros, se ha agregado la biblioteca de enlaces a una aplicación de prueba mínima y se ha ejecutado la aplicación para comprobar que el código C# puede llamar al código de Java que reside en el archivo .JAR. 

## <a name="related-links"></a>Vínculos relacionados

- [Creación de una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
