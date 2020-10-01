---
title: 'Tutorial: Enlace de una biblioteca de Kotlin en Android'
description: En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin.Android para una biblioteca de Kotlin existente, BubblePicker. Trata temas como la compilación de una biblioteca de Kotlin, su enlace y el uso de este enlace en una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: d75ec48bf9736297c31e0bb5af5a71de4332c66b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454278"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Tutorial: Enlace de una biblioteca de Kotlin en Android

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

Xamarin permite a los desarrolladores de dispositivos móviles crear aplicaciones móviles nativas multiplataforma con Visual Studio y C#. Puede usar los componentes del SDK de la plataforma Android de forma integrada, pero en muchos casos también preferirá usar los SDK de terceros escritos para esa plataforma: Xamarin permite hacerlo por medio de enlaces. Para incorporar un marco de Android de terceros a la aplicación de Xamarin.Android, debe crear un enlace de Xamarin.Android para poder usarlo en sus aplicaciones.

La plataforma Android, junto con sus lenguajes y herramientas nativos, están evolucionando constantemente; sirva de ejemplo la reciente introducción del lenguaje Kotlin, que se establece finalmente para reemplazar a Java. Hay varios SDK de terceros que ya se han migrado de Java a Kotlin y nos plantean nuevos desafíos. Aunque el proceso de enlace Kotlin es similar a Java, requiere pasos y ajustes de configuración adicionales para compilarse y ejecutarse correctamente como parte de una aplicación de Xamarin.Android.  

El objetivo de este documento es describir un enfoque de alto nivel para abordar este escenario y brindar una exhaustiva guía paso a paso con un ejemplo sencillo.

## <a name="background"></a>Fondo

Kotlin se presentó en febrero 2016 y se posicionó como alternativa al compilador de Java estándar en Android Studio para 2017. Más adelante, en 2019, Google anunció que el lenguaje de programación Kotlin se convertía en su lenguaje preferido para los desarrolladores de aplicaciones Android. El enfoque de enlace de alto nivel es similar al [proceso de enlace de las bibliotecas normales de Java](../binding-java-library/index.md), aunque hay unos cuantos pasos importantes específicos de Kotlin.

## <a name="prerequisites"></a>Requisitos previos

Para poder completar este tutorial, necesitará:

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Compilación de una biblioteca nativa

El primer paso consiste en crear una biblioteca nativa de Kotlin mediante Android Studio. La biblioteca normalmente la proporciona un desarrollador de terceros o está disponible en [el repositorio de Maven de Google](https://maven.google.com/web/index.html) y otros repositorios remotos. Como ejemplo, en este tutorial se crea un enlace para la biblioteca BubblePicker de Kotlin:

![Demostración de BubblePicker de GitHub](walkthrough-images/github-bubblepicker-demo.png)

1. Descargue [el código fuente](https://github.com/igalata/Bubble-Picker/archive/develop.zip) de GitHub para la biblioteca y desempaquételo en una carpeta local **Bubble-Picker**.

1. Inicie Android Studio y seleccione la opción de menú **Abrir un proyecto existente de Android Studio** eligiendo la carpeta local de Bubble-Picker:

    ![Abrir proyecto de Android Studio](walkthrough-images/android-studio-open-project.png)

1. Compruebe que Android Studio esté actualizado, incluido Gradle. El código fuente se puede compilar correctamente en Android Studio v3.5.3, Gradle v5.4.1. Encontrará instrucciones sobre cómo actualizar Gradle a la versión más reciente de Gradle [aquí](https://gradle.org/install/).

1. Compruebe que está instalado el Android SDK requerido. El código fuente requiere Android SDK v25. Abra la opción de menú **Herramientas > Administrador de SDK** para instalar los componentes del SDK.

1. Actualice y sincronice el archivo de configuración principal **build.gradle** ubicado en la raíz de la carpeta del proyecto:

    - Configuración de la versión de Kotlin en 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registre el repositorio de Maven de Google predeterminado para que se pueda resolver la dependencia de la biblioteca de compatibilidad:

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - Una vez actualizado el archivo de configuración, este no está sincronizado y Gradle muestra el botón **Sync Now** (Sincronizar ahora). Presione y espere a que se complete el proceso de sincronización:

        ![Sincronizar ahora de Gradle en Android Studio](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > La caché de dependencias de Gradle puede estar dañada, en ocasiones se produce después de un tiempo de espera de conexión de red. Vuelva a descargar las dependencias y sincronice el proyecto (requiere red).

        > [!TIP]
        > El estado de un proceso de compilación de Gradle (demonio) puede estar dañado. La detención de todos los demonios de Gradle puede resolver este problema. Detenga los procesos de compilación de Gradle (requiere reinicio). En el caso de los procesos de Gradle dañados, también puede intentar cerrar el IDE y, a continuación, terminar todos los procesos de Java.

        > [!TIP]
        > Es posible que el proyecto esté usando un complemento de terceros, que no es compatible con los demás complementos del proyecto o la versión de Gradle solicitada por el proyecto.

1. Abra el menú de Gradle a la derecha, desplácese hasta el menú **bubblepicker > Tasks** (bubblepicker > Tareas) y ejecute la tarea **compilar** (compilar). para ello, haga doble clic en ella y espere a que finalice el proceso de compilación:

    ![Ejecutar tarea de Gradle en Android Studio](walkthrough-images/android-studio-gradle-execute-task.png)

1. Abra el explorador de archivos de la carpeta raíz y navegue hasta la carpeta de compilación: **Bubble-Picker -> bubblepicker ->  build -> outputs -> aar** (Bubble-Picker -> bubblepicker ->  compilar -> salidas -> aar) y guarde el archivo **bubblepicker-release.aar** como **bubblepicker-v1.0.aar**. Este archivo se usará más adelante en el proceso de enlace:

    ![Salida AAR de Android Studio](walkthrough-images/android-studio-aar-output.png)

El archivo AAR es un archivo Android, que contiene el código fuente y los recursos compilados de Kotlin, requeridos por Android para ejecutar una aplicación con este SDK.  

## <a name="prepare-metadata"></a>Preparación de metadatos

El segundo paso consiste en preparar el archivo de transformación de metadatos, que usa Xamarin.Android para generar las clases de C# respectivas. Un proyecto de enlace de Xamarin.Android detectará todas las clases y miembros nativos de un archivo Android determinado y, posteriormente, generará un archivo XML con los metadatos adecuados. El archivo de transformación de metadatos creado manualmente se aplica a la línea de base generada anteriormente para crear el archivo de definición XML utilizado para generar el código de C#.

Los metadatos usan sintaxis de  [XPath](https://www.w3.org/TR/xpath/) , y los utiliza el generador de enlaces para influir en la creación del ensamblado de enlace. En el artículo sobre [metadatos de enlace de Java](../binding-java-library/customizing-bindings/java-bindings-metadata.md) se proporciona más información sobre las transformaciones, que se podría aplicar:

1. Cree un archivo **Metadata.xml** vacío:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Defina las transformaciones XML:

- La biblioteca nativa de Kotlin tiene dos dependencias, que no desea exponer al mundo C#, defina dos transformaciones para ignorarlas por completo. Es importante decir que los miembros nativos no se eliminarán del archivo binario resultante, únicamente no se generarán las clases de C#. El [descompilador de Java](http://java-decompiler.github.io/) se puede usar para identificar las dependencias. Ejecute la herramienta y abra el archivo AAR creado anteriormente; como resultado, se mostrará la estructura del archivo Android, que refleja todas las dependencias, los valores, los recursos, el manifiesto y las clases:  

    ![Dependencias del descompilador de Java](walkthrough-images/java-decompiler-dependencies.png)

    Las transformaciones para omitir el procesamiento de estos paquetes se definen mediante instrucciones de XPath:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La clase `BubblePicker` nativa tiene dos métodos `getBackgroundColor` y `setBackgroundColor`, y la transformación siguiente la cambiará a una propiedad `BackgroundColor` de C#:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Los tipos `UInt, UShort, ULong, UByte` sin signo requieren tratamiento especial. Para estos tipos, Kotlin cambia los nombres de método y los tipos de parámetros automáticamente, lo cual se refleja en el código generado:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    Este código se compila en el siguiente código de bytes de Java:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    Además, los tipos relacionados como `UIntArray, UShortArray, ULongArray, UByteArray` también se ven afectados por Kotlin. El nombre del método se cambia para incluir un sufijo adicional y los parámetros se cambian a una matriz de elementos de versiones firmadas de los mismos tipos. En el ejemplo siguiente, se convierte automáticamente un parámetro de tipo `UIntArray` en `int[]` y el nombre del método cambia de `fooUIntArrayMethod` a `fooUIntArrayMethod--ajY-9A`. El último lo detectan las herramientas de Xamarin.Android y se genera como un nombre de método válido:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    Este código se compila en el siguiente código de bytes de Java:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    Para darle un nombre descriptivo, se pueden agregar los siguientes metadatos a **Metadata.xml**, que actualizará el nombre de nuevo al definido originalmente en el código Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    En el ejemplo BubblePicker, no hay ningún miembro que use tipos sin signo, por lo que no se requiere ningún cambio adicional.

- Los miembros de Kotlin con parámetros genéricos se transforman de forma predeterminada en parámetros de Java.`Lang.Object` . Por ejemplo, un método Kotlin tiene un parámetro genérico \<T>:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Una vez que se genera un enlace de Xamarin.Android, el método se expone a C# como se indica a continuación:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Los genéricos de Java y Kotlin no son compatibles con los enlaces de Xamarin.Android, por lo que se crea un método generalizado de C# para acceder a la API genérica. Como solución alternativa, puede crear una biblioteca de Kotlin de contenedor y exponer las API necesarias con establecimiento inflexible de tipos sin genéricos. Como alternativa, puede crear aplicaciones auxiliares en el lado C# para solucionar el problema de la misma manera a través de las API con establecimiento inflexible de tipos.

    > [!TIP]
    > Al transformar los metadatos, se pueden aplicar los cambios al enlace generado. En el artículo sobre el [enlace de la biblioteca de Java](../binding-java-library/index.md) se explica en detalle cómo se generan y procesan los metadatos.

## <a name="build-a-binding-library"></a>Compilación de una biblioteca de enlaces

El siguiente paso consiste en crear un proyecto de enlace de Xamarin.Android con la plantilla de enlace de Visual Studio, agregar los metadatos necesarios y las referencias nativas y luego compilar el proyecto para generar una biblioteca consumible:

1. Abra Visual Studio para Mac y cree un nuevo proyecto de biblioteca de enlaces de Xamarin.Android, asígnele un nombre, en este caso **testBubblePicker.Binding**, y complete el asistente. La plantilla de enlace de Xamarin.Android se encuentra en la siguiente ruta de acceso: **Android > Biblioteca > Biblioteca de enlaces**:

    ![Creación de un enlace en Visual Studio](walkthrough-images/visual-studio-create-binding.png)

    En la carpeta de transformaciones hay tres archivos de transformación principales:

    - **Metadata.xml**: permite realizar cambios en la API final, como modificar el espacio de nombres del enlace generado.
    - **EnumFields.xml**: contiene la asignación entre las constantes int de Java y las enumeraciones de C#.
    - **EnumMethods.xml**: permite cambiar los parámetros de método y los tipos de valor devueltos de las constantes int de Java a las enumeraciones de C#.

    Mantenga vacíos los archivos **EnumFields.xml** y **EnumMethods.xml** y actualice **Metadata.xml** para definir las transformaciones.

1. Reemplace el archivo **Transformations/Metadata.xml** existente por el archivo **Metadata.xml** creado en el paso anterior. En la ventana de propiedades, compruebe que la **acción de compilación** está establecida en **TransformationFile**:

    ![Metadatos de Visual Studio](walkthrough-images/visual-studio-metadata.png)

1. Agregue el archivo **bubblepicker-v1.0.aar** que creó en el paso 1 al proyecto de enlace como una referencia nativa. Para agregar referencias de biblioteca nativa, abra el buscador y navegue hasta la carpeta con el archivo Android. Arrastre y coloque el archivo en la carpeta de archivos jar del Explorador de soluciones. Como alternativa, puede usar la opción del menú contextual **Agregar** de la carpeta de archivos jar y elegir **Archivos existentes...** . Elija copiar el archivo en el directorio para los fines de este tutorial. Asegúrese de comprobar que la **acción de compilación** está establecida en **LibraryProjectZip**:

    ![Referencia nativa de Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Agregue una referencia al paquete de [NuGet Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/). Este paquete es un enlace para la biblioteca estándar de Kotlin. Sin este paquete, el enlace solo funcionará si la biblioteca de Kotlin no usa ningún tipo específico de Kotlin; de lo contrario, no se expondrán todos estos miembros a C# y cualquier aplicación que intente consumir el enlace se bloqueará en el tiempo de ejecución.

    > [!TIP]
    > Debido a una limitación de Xamarin.Android, las herramientas de enlace solo se puede agregar en cada proyecto de enlace un solo archivo Android (AAR). Si es necesario incluir varios archivos AAR, se necesitan varios proyectos de Xamarin.Android, uno por cada AAR. Si este es el caso de este tutorial, las cuatro acciones anteriores de este paso tendrían que repetirse para cada archivo. Como opción alternativa, es posible combinar manualmente varios archivos Android como un solo archivo y, como resultado, podría usar un solo proyecto de enlace Xamarin.Android.

1. La acción final es compilar la biblioteca y no tener ningún error de compilación. En caso de que se produzcan errores de compilación, se pueden solucionar y controlar mediante el archivo Metadata.xml, que se ha creado anteriormente mediante la adición de metadatos de transformación xml, que agregan o quitan miembros de la biblioteca o les cambian el nombre.

## <a name="consume-the-binding-library"></a>Consumo de la biblioteca de enlaces

El último paso consiste en usar la biblioteca de enlaces de Xamarin.Android en una aplicación de Xamarin.Android. Cree un nuevo proyecto de Xamarin.Android, agregue una referencia a la biblioteca de enlaces y la interfaz de usuario y represente la interfaz de usuario de Bubble Picker:

1. Cree el proyecto de Xamarin.Android. Use **Android > App > Aplicación de Android** como punto de partida y seleccione **Más reciente y más grande** como opción de plataformas de destino para evitar problemas de compatibilidad. Todos los pasos siguientes se destinan a este proyecto:

    ![Creación de aplicación en Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Agregue una referencia de proyecto al proyecto de enlace o agregue una referencia al archivo DLL creado previamente:

    ![Agregar Reference.png de enlace en Visual Studio](walkthrough-images/visual-studio-add-binding-reference.png)

1. Agregue una referencia al paquete de [NuGet Xamarin. Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/), que ha agregado anteriormente al proyecto de enlace de Xamarin.Android. Agrega compatibilidad con cualquier tipo específico de Kotlin que necesite entregarse en el tiempo de ejecución.  Sin este paquete, la aplicación se puede compilar, pero se bloqueará en el tiempo de ejecución:

    ![Agregar StdLib NuGet en Visual Studio](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Agregue el control `BubblePicker` al diseño de Android para `MainActivity`. Abra el archivo **testBubblePicker/Resources/layout/content_main.xml** y anexe el nodo de control de BubblePicker como el último elemento del control RelativeLayout raíz:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. Actualice el código fuente de la aplicación y agregue la lógica de inicialización a `MainActivity`, que activa el SDK de Bubble Picker:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter` y `BubblePickerListener` son dos clases que se van a crear de cero, que controlan los datos de las burbujas y controlan la interacción:

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. Ejecute la aplicación, que debe representar la interfaz de usuario de Bubble Picker:

    ![Demostración de BubblePicker](walkthrough-images/bubble-picker-demo.png)

    El ejemplo requiere código adicional para representar el estilo de los elementos y controlar las interacciones, aunque el control de `BubblePicker` se ha creado y activado correctamente.

¡Enhorabuena! Ha creado correctamente una aplicación Xamarin.Android y una biblioteca de enlaces, que consume una biblioteca de Kotlin.  

Ahora debería tener una aplicación de Xamarin.Android básica que use una biblioteca de Kotlin nativa a través de una biblioteca de enlaces de Xamarin.Android. En este tutorial se usa intencionadamente un ejemplo básico para resaltar mejor los conceptos clave que se introducen. En escenarios reales, es probable que tenga que exponer un número mayor de API y aplicarle transformaciones de metadatos.

## <a name="related-links"></a>Vínculos relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalación de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca de Kotlin de BubblePicker](https://github.com/igalata/Bubble-Picker)
- [Enlace de una biblioteca Java](../binding-java-library/index.md)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadatos de enlaces de Java](../binding-java-library/customizing-bindings/java-bindings-metadata.md)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositorio del proyecto de ejemplo](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)