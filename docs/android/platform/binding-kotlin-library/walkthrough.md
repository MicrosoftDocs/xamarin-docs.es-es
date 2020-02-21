---
title: 'Tutorial: enlazar una biblioteca Kotlin de Android'
description: En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. Android para una biblioteca Kotlin existente, BubblePicker. Trata temas como compilar una biblioteca de Kotlin, enlazarla y usar el enlace en una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519689"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>Tutorial: enlazar una biblioteca Kotlin de Android

Xamarin permite a los desarrolladores de dispositivos móviles crear aplicaciones móviles nativas multiplataforma con C#Visual Studio y. Puede usar los componentes del SDK de la plataforma Android de forma integrada, pero en muchos casos también quiere usar los SDK de terceros escritos para esa plataforma y Xamarin le permite hacerlo a través de los enlaces. Para incorporar un marco de Android de terceros a la aplicación de Xamarin. Android, debe crear un enlace de Xamarin. Android para poder usarlo en sus aplicaciones.

La plataforma Android, junto con sus lenguajes y herramientas nativos, están evolucionando constantemente, incluida la reciente introducción del lenguaje Kotlin, que se establece finalmente para reemplazar a Java. Hay una serie de SDK para la fiesta 3D, que ya se han migrado de Java a Kotlin y nos presenta nuevos desafíos. Aunque el proceso de enlace Kotlin es similar a Java, requiere pasos adicionales y valores de configuración para compilar y ejecutar correctamente como parte de una aplicación de Xamarin. Android.  

El objetivo de este documento es describir un enfoque de alto nivel para abordar este escenario y proporcionar una guía paso a paso detallada con un ejemplo sencillo.

## <a name="background"></a>Información previa

Kotlin se lanzó en febrero 2016 y se colocó como alternativa al compilador de Java estándar en Android Studio por 2017. Más adelante en 2019, Google anunció que el lenguaje de programación Kotlin se convirtió en su lenguaje preferido para los desarrolladores de aplicaciones Android. El enfoque de enlace de alto nivel es similar al [proceso de enlace de las bibliotecas de Java normales](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) con unos pocos pasos específicos de Kotlin importantes.

## <a name="prerequisites"></a>Prerequisites

Para completar esta visita guiada, necesitará:

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>Crear una biblioteca nativa

El primer paso es crear una biblioteca nativa de Kotlin mediante Android Studio. La biblioteca normalmente la proporciona un desarrollador de terceros o está disponible en [el repositorio de Maven de Google](https://maven.google.com/web/index.html) y en otros repositorios remotos. Como ejemplo, en este tutorial se crea un enlace para la biblioteca Kotlin selector de burbujas:

![Demostración de GitHub BubblePicker](walkthrough-images/github-bubblepicker-demo.png)

1. Descargue [el código fuente](https://github.com/igalata/Bubble-Picker/archive/develop.zip) de github para la biblioteca y desempaquete en una carpeta local **selector de burbujas**.

1. Inicie el Android Studio y seleccione la opción de menú **abrir un proyecto existente Android Studio** elegir la carpeta local del selector de burbujas:

    ![Android Studio abrir proyecto](walkthrough-images/android-studio-open-project.png)

1. Compruebe que el Android Studio esté actualizado, incluido Gradle. El código fuente se puede crear correctamente en Android Studio v 3.5.3, Gradle v 5.4.1. [Aquí](https://gradle.org/install/)encontrará instrucciones sobre cómo actualizar Gradle a la versión más reciente de Gradle.

1. Compruebe que está instalado el Android SDK requerido. El código fuente requiere Android SDK V25. Abra **herramientas >** la opción de menú administrador de SDK para instalar los componentes del SDK.

1. Actualice y sincronice el archivo de configuración principal **Build. Gradle** situado en la raíz de la carpeta del proyecto:

    - Establezca la versión de Kotlin en 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - Registre el repositorio de Maven de Google predeterminado para que se pueda resolver la dependencia de la biblioteca de soporte:

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

    - Una vez actualizado el archivo de configuración, no está sincronizado y Gradle muestra el botón **sincronizar ahora** , lo presiona y espera a que se complete el proceso de sincronización:

        ![Android Studio sincronización de Gradle ahora](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > La caché de dependencias de Gradle puede estar dañada, en ocasiones se produce después de un tiempo de espera de conexión de red. Redescargue las dependencias y el proyecto de sincronización (requiere red).

        > [!TIP]
        > El estado de un proceso de compilación de Gradle (daemon) puede estar dañado. La detención de todos los demonios de Gradle puede resolver este problema. Detener procesos de compilación de Gradle (requiere reinicio). En el caso de los procesos de Gradle dañados, también puede intentar cerrar el IDE y, a continuación, eliminar todos los procesos de Java.

        > [!TIP]
        > Es posible que el proyecto esté usando un complemento de terceros, que no es compatible con los demás complementos del proyecto o la versión de Gradle solicitada por el proyecto.

1. Abra el menú Gradle a la derecha, desplácese hasta el **bubblepicker > menú tareas** , ejecute la tarea de **compilación** haciendo doble clic en ella y espere a que finalice el proceso de compilación:

    ![Android Studio tarea ejecutar Gradle](walkthrough-images/android-studio-gradle-execute-task.png)

1. Abra el explorador de archivos de la carpeta raíz y navegue hasta la carpeta de compilación: **Bubble-Picker-> bubblepicker-> Build-> Outputs-> AAR**, guarde el archivo **bubblepicker-Release. AAR** como **bubblepicker-v 1.0. AAR**, este archivo se usará más adelante en el proceso de enlace:

    ![Android Studio salida de AAR](walkthrough-images/android-studio-aar-output.png)

El archivo AAR es un archivo Android, que contiene el código fuente y los recursos compilados de Kotlin, requeridos por Android para ejecutar una aplicación con este SDK.  

## <a name="prepare-metadata"></a>Preparar metadatos

El segundo paso es preparar el archivo de transformación de metadatos, que usa Xamarin. Android para generar las C# clases respectivas. Un proyecto de enlace de Xamarin. Android detectará todas las clases y miembros nativos de un archivo Android determinado y, posteriormente, generará un archivo XML con los metadatos adecuados. El archivo de transformación de metadatos creado manualmente se aplica a la línea base generada anteriormente para crear el archivo de definición XML C# final que se usa para generar el código.

Los metadatos utilizan la sintaxis de  [XPath](https://www.w3.org/TR/xpath/) y el generador de enlaces lo usa para influir en la creación del ensamblado de enlace. En el artículo sobre los [metadatos de enlace de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata) se proporciona más información sobre las transformaciones, que se pueden aplicar:

1. Cree un archivo **Metadata. XML** vacío:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. Definir transformaciones XML:

- La biblioteca Kotlin nativa tiene dos dependencias, que no desea exponer a C# todo el mundo, definir dos transformaciones para omitirlas por completo. Es importante decir que los miembros nativos no se eliminarán del binario resultante, solo C# se generarán las clases. Los [descompilador de Java](http://java-decompiler.github.io/) se pueden usar para identificar las dependencias. Ejecute la herramienta y abra el archivo AAR creado anteriormente, como resultado, se mostrará la estructura del archivo Android, que refleja todas las dependencias, los valores, los recursos, el manifiesto y las clases:  

    ![Dependencias de descompilador de Java](walkthrough-images/java-decompiler-dependencies.png)

    Las transformaciones para omitir el procesamiento de estos paquetes se definen mediante instrucciones de XPath:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- La clase `BubblePicker` nativa tiene dos métodos `getBackgroundColor` y `setBackgroundColor` y la transformación siguiente lo cambiará a una C# propiedad `BackgroundColor`:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- Los tipos sin signo `UInt, UShort, ULong, UByte` requieren un tratamiento especial. Para estos tipos, Kotlin cambia los nombres de método y los tipos de parámetros automáticamente, que se refleja en el código generado:

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

    Además, los tipos relacionados como `UIntArray, UShortArray, ULongArray, UByteArray` también se ven afectados por Kotlin. El nombre del método se cambia para incluir un sufijo adicional y los parámetros se cambian a una matriz de elementos de versiones firmadas de los mismos tipos. En el ejemplo siguiente, se convierte automáticamente un parámetro de tipo `UIntArray` en `int[]` y el nombre del método cambia de `fooUIntArrayMethod` a `fooUIntArrayMethod--ajY-9A`. El último lo detectan las herramientas de Xamarin. Android y se generan como un nombre de método válido:

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

    Para darle un nombre descriptivo, se pueden agregar los siguientes metadatos a **Metadata. XML**, que actualizará el nombre de nuevo a definido originalmente en el código Kotlin:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    En el ejemplo BubblePicker, no hay ningún miembro que use tipos sin signo, por lo que no se requiere ningún cambio adicional.

- Los miembros de Kotlin con parámetros genéricos se transforman de forma predeterminada en parámetros de Java.`Lang.Object` automáticamente. Por ejemplo, un método Kotlin tiene un parámetro genérico \<T >:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    Una vez que se genera un enlace de Xamarin. Android, el método C# se expone a como se indica a continuación:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Los genéricos Java y Kotlin no son compatibles con los enlaces de Xamarin. Android, por lo C# que se crea un método generalizado para acceder a la API genérica. Como solución alternativa, puede crear una biblioteca Kotlin de contenedor y exponer las API necesarias de una manera fuertemente tipada sin genéricos. Como alternativa, puede crear aplicaciones auxiliares en C# el lado para solucionar el problema de la misma manera a través de las API de tipo seguro.

    > [!TIP]
    > Al transformar los metadatos, se pueden aplicar los cambios al enlace generado. En el artículo enlace de la [biblioteca de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) se explica en detalle cómo se generan y procesan los metadatos.

## <a name="build-a-binding-library"></a>Crear una biblioteca de enlaces

El siguiente paso consiste en crear un proyecto de enlace de Xamarin. Android con la plantilla de enlace de Visual Studio, agregar los metadatos necesarios, las referencias nativas y luego compilar el proyecto para generar una biblioteca consumible:

1. Abra Visual Studio para Mac y cree un nuevo proyecto de biblioteca de enlace de Xamarin. Android, asígnele un nombre, en este caso **testBubblePicker. Binding** y complete el asistente. La plantilla de enlace de Xamarin. Android se encuentra en la siguiente ruta de acceso: **biblioteca android > > biblioteca de enlace**:

    ![Crear enlace de Visual Studio](walkthrough-images/visual-studio-create-binding.png)

    En la carpeta transformaciones hay tres archivos de transformación principales:

    - **Metadata. XML** : permite realizar cambios en la API final, como cambiar el espacio de nombres del enlace generado.
    - **EnumFields. XML** : contiene la asignación entre las constantes y C# las enumeraciones de Java int.
    - **Enummethods (. XML** : permite cambiar los parámetros de método y los tipos de valor devueltos de las constantes int de Java a C# las enumeraciones.

    Mantenga vacíos los archivos **EnumFields. XML** y **enummethods (. XML** y actualice **Metadata. XML** para definir las transformaciones.

1. Reemplace el archivo **Transformations. XML** existente por el archivo **Metadata. XML** creado en el paso anterior. En la ventana Propiedades, compruebe que la **acción de compilación** del archivo está establecida en **TransformationFile**:

    ![Metadatos de Visual Studio](walkthrough-images/visual-studio-metadata.png)

1. Agregue el archivo **bubblepicker-v 1.0. AAR** que creó en el paso 1 al proyecto de enlace como una referencia nativa. Para agregar referencias de biblioteca nativa, abra el buscador y navegue hasta la carpeta con el archivo Android. Arrastre y coloque el archivo en la carpeta jar de Explorador de soluciones. Como alternativa, puede usar la opción de menú contextual **Agregar** en la carpeta jar y elegir **archivos existentes.** . Elija copiar el archivo en el directorio para los fines de este tutorial. Asegúrese de comprobar que la **acción de compilación** está establecida en **LibraryProjectZip**:

    ![Referencia nativa de Visual Studio](walkthrough-images/visual-studio-native-reference.png)

1. Agregue una referencia al paquete de [NuGet Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) . Este paquete es un enlace para la biblioteca estándar de Kotlin. Sin este paquete, el enlace solo funcionará si la biblioteca Kotlin no usa ningún tipo específico de Kotlin; de lo contrario, no se expondrán todos estos miembros a C# y cualquier aplicación que intente consumir el enlace se bloqueará en tiempo de ejecución.

    > [!TIP]
    > Debido a una limitación de Xamarin. Android, las herramientas de enlace solo se pueden agregar en cada proyecto de enlace un solo archivo Android (AAR). Si es necesario incluir varios archivos AAR, se necesitan varios proyectos de Xamarin. Android, uno por cada AAR. Si este es el caso de este tutorial, las cuatro acciones anteriores de este paso tendrían que repetirse para cada archivo. Como opción alternativa, es posible combinar manualmente varios archivos Android como un solo archivo y, como resultado, podría usar un solo proyecto de enlace Xamarin. Android.

1. La acción final es generar la biblioteca y no tener ningún error de compilación. En caso de que se produzcan errores de compilación, se pueden solucionar y controlar mediante el archivo Metadata. XML, que se ha creado anteriormente mediante la adición de metadatos de transformación XML, que agregan, quitan o cambia el nombre de los miembros de la biblioteca.

## <a name="consume-the-binding-library"></a>Usar la biblioteca de enlaces

El último paso es usar la biblioteca de enlace de Xamarin. Android en una aplicación de Xamarin. Android. Cree un nuevo proyecto de Xamarin. Android, agregue una referencia a la biblioteca de enlace y la interfaz de usuario del selector de burbujas de representación:

1. Cree el proyecto de Xamarin. Android. Use la aplicación Android **> app > Android** como punto de partida y seleccione la opción **más reciente y más** importante como destino de las plataformas para evitar problemas de compatibilidad. Todos los pasos siguientes se destinan a este proyecto:

    ![Crear aplicación de Visual Studio](walkthrough-images/visual-studio-create-app.png)

1. Agregue una referencia de proyecto al proyecto de enlace o agregue una referencia al archivo DLL creado previamente:

    ![Agregar referencia de enlace. png de Visual Studio](walkthrough-images/visual-studio-add-binding-reference.png)

1. Agregue una referencia al paquete de [NuGet Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) , que ha agregado anteriormente al proyecto de enlace de Xamarin. Android. Agrega compatibilidad con cualquier tipo específico de Kotlin que necesite entregarse en tiempo de ejecución.  Sin este paquete, la aplicación se puede compilar, pero se bloqueará en tiempo de ejecución:

    ![Agregar StdLib NuGet de Visual Studio](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. Agregue el control `BubblePicker` al diseño de Android para `MainActivity`. Abra el archivo **testBubblePicker/Resources/layout/content_main. XML** y Anexe el nodo de control BubblePicker como el último elemento del control RelativeLayout raíz:

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

1. Actualice el código fuente de la aplicación y agregue la lógica de inicialización al `MainActivity`, que activa el SDK del selector de burbujas:

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

    `BubblePickerAdapter` y `BubblePickerListener` son dos clases que se van a crear desde el principio, que controlan los datos de las burbujas y controlan la interacción:

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

1. Ejecute la aplicación, que debe representar la interfaz de usuario del selector de burbujas:

    ![Demostración de BubblePicker](walkthrough-images/bubble-picker-demo.png)

    El ejemplo requiere código adicional para representar el estilo de los elementos y controlar las interacciones, pero el control de `BubblePicker` se ha creado y activado correctamente.

Felicidades. Ha creado correctamente una aplicación Xamarin. Android y una biblioteca de enlaces, que consume una biblioteca Kotlin.  

Ahora debería tener una aplicación de Xamarin. Android básica que use una biblioteca de Kotlin nativa a través de una biblioteca de enlace de Xamarin. Android. En este tutorial se usa intencionadamente un ejemplo básico para resaltar mejor los conceptos clave que se introducen. En escenarios reales, es probable que tenga que exponer un número mayor de API y aplicarle transformaciones de metadatos.

## <a name="related-links"></a>Vínculos relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalación de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca de BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Enlazar biblioteca de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadatos de enlace de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [NuGet de Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositorio del proyecto de ejemplo](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
