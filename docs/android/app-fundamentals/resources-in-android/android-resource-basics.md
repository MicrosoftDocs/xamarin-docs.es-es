---
title: Conceptos básicos de los recursos de Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: ac228e6f0c251ae6f0fcabe1be855c6ed4a85d35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025345"
---
# <a name="android-resource-basics"></a>Conceptos básicos de los recursos de Android

Casi todas las aplicaciones de Android tendrán algún tipo de recursos. como mínimo, suelen tener los diseños de la interfaz de usuario en forma de archivos XML. Cuando se crea una aplicación de Xamarin. Android por primera vez, la plantilla de proyecto de Xamarin. Android configura los recursos predeterminados:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Archivos de recursos](android-resource-basics-images/01-resource-files-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Archivos de recursos](android-resource-basics-images/01-resource-files-xs.png)

-----

Los cinco archivos que componen los recursos predeterminados se crearon en la carpeta recursos:

- **Icon. png** &ndash; el icono predeterminado de la aplicación

- **Main. axml** &ndash; el archivo de diseño de la interfaz de usuario predeterminado de una aplicación. Tenga en cuenta que mientras Android usa la extensión de archivo **. XML** , Xamarin. Android usa la extensión de archivo **. axml** .

- **Strings. xml** &ndash; una tabla de cadenas para ayudar con la localización de la aplicación

- **AboutResources. txt** &ndash; esto no es necesario y puede eliminarse de forma segura. Solo proporciona información general de alto nivel de la carpeta de recursos y los archivos que contiene.

- **Resource.designer.cs** &ndash; este archivo se genera y mantiene automáticamente en Xamarin. Android y contiene el identificador único asignado a cada recurso. Esto es muy similar y idéntico en lo que respecta al archivo R. Java que tendría una aplicación Android escrita en Java. Lo crean automáticamente las herramientas de Xamarin. Android y se volverán a generar de vez en cuando.

## <a name="creating-and-accessing-resources"></a>Crear y obtener acceso a recursos

La creación de recursos es tan sencilla como agregar archivos al directorio para el tipo de recurso en cuestión. En la captura de pantalla siguiente se muestran los recursos de cadena para las configuraciones regionales alemanas que se agregaron a un proyecto. Cuando se agregó **Strings. XML** al archivo, la **acción de compilación** se estableció automáticamente en **AndroidResource** con las herramientas de Xamarin. Android:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Acción de compilación para strings. XML establecida en AndroidResource](android-resource-basics-images/02-build-action-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Acción de compilación para strings. XML establecida en AndroidResource](android-resource-basics-images/02-build-action-xs.png)

-----

Esto permite a las herramientas de Xamarin. Android compilar e insertar correctamente los recursos en el archivo APK. Si, por alguna razón, la **acción de compilación** no está establecida en el **recurso de Android**, los archivos se excluirán del APK y cualquier intento de cargar o tener acceso a los recursos producirá un error en tiempo de ejecución y la aplicación se bloqueará.

Además, es importante tener en cuenta que aunque Android solo admite nombres de archivo en minúsculas para los elementos de recursos, Xamarin. Android es un poco más permisivo; será compatible con los nombres de archivo en mayúsculas y minúsculas. La Convención para los nombres de imagen es usar minúsculas con caracteres de subrayado como separadores (por ejemplo, **mi\_imagen\_nombre. png**). Tenga en cuenta que los nombres de recursos no se pueden procesar si los guiones o espacios se usan como separadores.

Una vez que se han agregado recursos a un proyecto, hay dos maneras de utilizarlos en una aplicación &ndash; mediante programación (dentro del código) o desde archivos XML.

## <a name="referencing-resources-programmatically"></a>Referencia a recursos mediante programación

Para tener acceso a estos archivos mediante programación, se les asigna un identificador de recurso único. Este identificador de recurso es un entero definido en una clase especial llamada `Resource`, que se encuentra en el archivo **Resource.Designer.CS**, y tiene un aspecto similar al siguiente:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Cada identificador de recurso se encuentra dentro de una clase anidada que corresponde al tipo de recurso. Por ejemplo, cuando se agregó el archivo **Icon. png** al proyecto, Xamarin. Android actualizó la clase `Resource`, creando una clase anidada llamada `Drawable` con una constante dentro de `Icon`denominada.
Esto permite hacer referencia al archivo **Icon. png** en el código como `Resource.Drawable.Icon`. La clase `Resource` no se debe editar manualmente, ya que Xamarin. Android sobrescribirá los cambios que se realicen en ella.

Al hacer referencia a los recursos mediante programación (en el código), se puede tener acceso a ellos a través de la jerarquía de clases Resources, que usa la sintaxis siguiente:

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

- **PackageName** &ndash; el paquete que proporciona el recurso y solo es necesario cuando se usan recursos de otros paquetes.

- **ResourceType** &ndash; este es el tipo de recurso anidado que se encuentra dentro de la clase de recursos que se ha descrito anteriormente.

- **Nombre del recurso** &ndash; este es el nombre de archivo del recurso (sin la extensión) o el valor del atributo Android: Name para los recursos que están en un elemento XML.

## <a name="referencing-resources-from-xml"></a>Referencia a recursos desde XML

Se obtiene acceso a los recursos de un archivo XML mediante una sintaxis especial:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

- **PackageName** &ndash; el paquete que proporciona el recurso y solo es necesario cuando se usan recursos de otros paquetes.

- **ResourceType** &ndash; este es el tipo de recurso anidado que se encuentra dentro de la clase de recurso.

- **Nombre del recurso** &ndash; este es el nombre de archivo del recurso (*sin* la extensión de tipo de archivo) o el valor del atributo `android:name` para los recursos que están en un elemento XML.

Por ejemplo, el contenido de un archivo de diseño, **Main. axml**, es el siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

Este ejemplo tiene una [`ImageView`](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) que requiere una **marca**de nombre de recurso dibujable. El `ImageView` tiene su atributo `src` establecido en `@drawable/flag`. Cuando se inicia la actividad, Android buscará en el **recurso de directorio o dibujable** para un archivo denominado **Flag. png** (la extensión de archivo podría ser otro formato de imagen, como **Flag. jpg**) y cargar ese archivo y mostrarlo en el `ImageView`.
Cuando se ejecuta esta aplicación, tendría un aspecto similar al de la siguiente imagen:

![ImageView localizadas](android-resource-basics-images/03-localized-screenshot.png)
