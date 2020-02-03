---
title: 'Hello, Android: profundización'
description: Esta guía en dos partes le ayudará a compilar su primera aplicación de Xamarin.Android y a comprender los aspectos básicos del desarrollo de aplicaciones de Android con Xamarin. A lo largo del proceso, le presentaremos las herramientas, los conceptos y los pasos necesarios para compilar e implementar una aplicación de Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 10a46c916654f8421dc5a9af93de3abbbae5e934
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724371"
---
# <a name="hello-android-deep-dive"></a>Hello, Android: profundización

_Esta guía en dos partes le ayudará a compilar su primera aplicación de Xamarin.Android y a comprender los aspectos básicos del desarrollo de aplicaciones de Android con Xamarin. A lo largo del proceso, le presentaremos las herramientas, los conceptos y los pasos necesarios para compilar e implementar una aplicación de Xamarin.Android._

En [Hello, Android Quickstart](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md) (Guía de inicio rápido de Hello, Android), compiló y ejecutó su primera aplicación de Xamarin.Android. Ahora ha llegado el momento de que conozca mejor la manera en que funcionan las aplicaciones de Android para que pueda crear programas más sofisticados. En esta guía se repasan los pasos que llevó a cabo en el tutorial de Hello, Android para que entienda lo que hizo y empiece a asimilar los aspectos básicos del desarrollo de aplicaciones de Android.

En esta guía se tratan los temas siguientes:

::: zone pivot="windows"

- **Introducción a Visual Studio**: introducción a Visual Studio y creación de una aplicación de Xamarin.Android.

- **Anatomía de una aplicación de Xamarin.Android**: Paseo por las partes esenciales de una aplicación de Xamarin.Android.

- **Aspectos básicos de la arquitectura y la aplicación**: introducción a las actividades, el manifiesto de Android y la esencia del desarrollo de Android.

- **Interfaz de usuario (UI)** : creación de interfaces de usuario con Android Designer.

- **Actividades y ciclo de vida de la actividad**: una introducción al ciclo de vida de la actividad y a la inserción de la interfaz de usuario en el código.

- **Pruebas, implementación y toques finales**: finalización de la aplicación con consejos sobre pruebas, implementación, generación de material gráfico y mucho más.

::: zone-end
::: zone pivot="macos"

- **Introducción a Visual Studio para Mac**: introducción a Visual Studio para Mac y creación de una nueva aplicación de Xamarin.Android.

- **Anatomía de una aplicación de Xamarin.Android**: paseo por las partes esenciales de una aplicación de Xamarin.Android.

- **Aspectos básicos de la arquitectura y la aplicación**: introducción a las actividades, el manifiesto de Android y la esencia del desarrollo de Android.

- **Interfaz de usuario (UI)** : creación de interfaces de usuario con Android Designer.

- **Actividades y ciclo de vida de la actividad**: una introducción al ciclo de vida de la actividad y a la inserción de la interfaz de usuario en el código.

- **Pruebas, implementación y toques finales**: finalización de la aplicación con consejos sobre pruebas, implementación, generación de material gráfico y mucho más.

::: zone-end

Esta guía le ayuda a desarrollar las habilidades y los conocimientos necesarios para compilar una aplicación de Android de una sola pantalla. Cuando la haya finalizado, podrá comprender las diferentes partes de una aplicación de Xamarin.Android y cómo encajan entre sí.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introducción a Visual Studio

Visual Studio es un IDE muy completo de Microsoft. Incluye un diseñador visual completamente integrado, un editor de texto con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. Con esta guía, aprenderá a usar algunas características básicas de Visual Studio con el complemento Xamarin.

Visual Studio organiza el código en _soluciones_ y _proyectos_. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación (por ejemplo, para iOS o Android), una biblioteca auxiliar, una aplicación de prueba, etc. En la aplicación **Phoneword**, ha agregado un nuevo proyecto de Android con la plantilla **Aplicación de Android** a la solución **Phoneword** creada en la guía de [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

Visual Studio para Mac es un IDE gratuito y de código abierto similar a Visual Studio. Incluye un diseñador visual completamente integrado, un editor de texto completo con herramientas de refactorización, un explorador de ensamblados, integración de código fuente y mucho más. Con esta guía aprenderá a usar algunas características básicas de Visual Studio para Mac. Si no está familiarizado con Visual Studio para Mac, tal vez le interese consultar [Introduction to Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/) (Introducción a Visual Studio para Mac), que incluye información más detallada.

Visual Studio para Mac sigue la práctica de Visual Studio consistente en organizar el código en _soluciones_ y _proyectos_. Una solución es un contenedor que puede incluir uno o varios proyectos. Un proyecto puede ser una aplicación (por ejemplo, para iOS o Android), una biblioteca auxiliar, una aplicación de prueba, etc. En la aplicación **Phoneword**, ha agregado un nuevo proyecto de Android con la plantilla **Aplicación de Android** a la solución **Phoneword** creada en la guía de [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinandroid-application"></a>Anatomía de una aplicación de Xamarin.Android

::: zone pivot="windows"

En la captura de pantalla siguiente se muestra el contenido de la solución. Este es el Explorador de soluciones, que contiene la estructura de directorios y todos los archivos asociados a la solución:

[![Explorador de soluciones](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

En la captura de pantalla siguiente se muestra el contenido de la solución. Este es el Panel de solución, que contiene la estructura de directorios y todos los archivos asociados a la solución:

[![Panel de solución](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

Se ha creado una solución denominada **Phoneword** y se ha colocado dentro el proyecto de Android **Phoneword**.

Observe los elementos situados dentro del proyecto y fíjese en cada carpeta y su finalidad:

- **Propiedades**: contiene el archivo [AndroidManifest.xml](~/android/platform/android-manifest.md), que describe todos los requisitos de la aplicación de Xamarin.Android, incluidos el nombre, el número de versión y los permisos. La carpeta **Propiedades** también contiene [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo), un archivo de metadatos de ensamblado .NET. Se recomienda que incluya en este archivo información básica sobre la aplicación.

- **Referencias**: contiene los ensamblados necesarios para compilar y ejecutar la aplicación. Si expande el directorio Referencias, verá referencias a ensamblados .NET, como [System](xref:System), System.Core y [System.Xml](xref:System.Xml), así como una referencia al ensamblado Mono.Android de Xamarin.

- **Activos**: contiene los archivos que la aplicación necesita para ejecutarse, como fuentes, archivos de datos locales y archivos de texto. Se puede acceder a los archivos incluidos aquí mediante la clase generada `Assets`. Para obtener más información sobre los activos de Android, consulte la guía de Xamarin [Using Android Assets](~/android/app-fundamentals/resources-in-android/android-assets.md) (Uso de activos de Android).

- **Recursos**: contiene recursos de la aplicación, como cadenas, imágenes y diseños. Puede tener acceso a estos recursos en el código mediante la clase generada `Resource`. La guía [Android Resources](~/android/app-fundamentals/resources-in-android/index.md) (Recursos de Android) proporciona información detallada sobre el directorio **Recursos**. La plantilla de aplicación también incluye una guía concisa sobre los recursos en el archivo **AboutResources.txt**.

### <a name="resources"></a>Recursos

El directorio **Recursos** contiene cuatro carpetas denominadas **drawable**, **layout**, **mipmap** y **values**, así como un archivo llamado **Resource.designer.cs**.

Los elementos se resumen en la tabla siguiente:

- **drawable**: el directorio drawable contiene [recursos que se pueden dibujar](https://developer.android.com/guide/topics/resources/drawable-resource.html), como imágenes y mapas de bits.

- **mipmap**: el directorio mipmap contiene archivos drawable para distintas densidades de los iconos de iniciadores. En la plantilla predeterminada, el directorio drawable contiene el archivo del icono de la aplicación, **Icon.png**.

::: zone pivot="windows"

- **layout**: el directorio layout contiene _archivos de Android Designer_ (.axml), que definen la interfaz de usuario de cada pantalla o actividad. La plantilla crea un diseño predeterminado denominado **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

- **layout**: el directorio layout contiene _archivos de Android Designer_ (.axml), que definen la interfaz de usuario de cada pantalla o actividad. La plantilla crea un diseño predeterminado denominado **Main.axml**.

::: zone-end

- **values**: este directorio contiene archivos XML que almacenan valores simples, como cadenas, enteros y colores. La plantilla crea un archivo para almacenar valores de cadena denominado **Strings.xml**.

- **Resource.designer.cs**: también conocido como la clase `Resource`, este archivo es una clase parcial que contiene los identificadores únicos asignados a cada recurso. Lo crean automáticamente las herramientas de Xamarin.Android y se vuelve a generar según sea necesario. Este archivo no se debe editar manualmente, ya que Xamarin.Android sobrescribirá todos los cambios manuales que se realicen en él.

## <a name="app-fundamentals-and-architecture-basics"></a>Aspectos básicos de la arquitectura y la aplicación

Las aplicaciones de Android no tienen un solo punto de entrada, es decir, no hay una única línea de código en la aplicación que el sistema operativo llame para iniciar la aplicación. En su lugar, una aplicación se inicia cuando Android crea una instancia de una de sus clases, durante lo cual Android carga el proceso completo de la aplicación en la memoria.

Esta función exclusiva de Android puede ser muy útil al diseñar aplicaciones complicadas o interactuar con el sistema operativo Android. Pero estas opciones también hacen que Android sea complejo cuando se trabaja con un escenario básico como la aplicación **Phoneword**. Por este motivo, la exploración de la arquitectura de Android se divide en dos partes. En esta guía se analiza una aplicación que usa el punto de entrada más común de una aplicación de Android: la primera pantalla. En [Hello, Android Multiscreen](~/android/get-started/hello-android-multiscreen/index.md) se exploran todas las complejidades de la arquitectura de Android a medida que se describen las distintas formas de iniciar una aplicación.

### <a name="phoneword-scenario---starting-with-an-activity"></a>Escenario de Phoneword: empezar con una actividad

Cuando se abre la aplicación **Phoneword** por primera vez en un emulador o un dispositivo, el sistema operativo crea la primera *actividad*. Una actividad es una clase especial de Android que se corresponde con una sola pantalla de la aplicación y es responsable de dibujar y activar la interfaz de usuario. Cuando Android crea la primera actividad de la aplicación, carga toda la aplicación:

[![Carga de actividad](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

Dado que no existe una progresión lineal en una aplicación de Android (puede iniciar la aplicación desde varios puntos), Android tiene una sola manera de realizar un seguimiento de las clases y los archivos que componen una aplicación. En el ejemplo de **Phoneword**, todas las partes que componen la aplicación se registran con un archivo XML especial denominado **manifiesto de Android**. La función del **manifiesto de Android** consiste en realizar un seguimiento del contenido, las propiedades y los permisos de una aplicación y comunicárselos al sistema operativo Android. La aplicación **Phoneword** puede considerarse como una sola actividad (pantalla) y una colección de archivos del asistente y de recursos unidos mediante el archivo de manifiesto de Android, como se muestra en el diagrama siguiente:

[![Asistentes de recursos](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

En las secciones siguientes se exploran las relaciones entre las distintas partes de la aplicación **Phoneword**, para que entienda mejor el diagrama anterior. Esta exploración comienza con la interfaz de usuario y analiza cuestiones como los archivos de diseño y Android Designer.

## <a name="user-interface"></a>Interfaz de usuario

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

::: zone pivot="windows"

**activity_main.axml** es el archivo de diseño de la interfaz de usuario de la primera pantalla de la aplicación. La extensión .axml indica que se trata de un archivo de Android Designer (AXML significa *XML de Android*). El nombre *Main* es arbitrario desde el punto de vista de Android, es decir, el archivo de diseño podría tener otro nombre. Cuando se abre **activity_main.axml** en el IDE, aparece el editor visual de los archivos de diseño de Android, denominado *Android Designer*:

[![Android Designer](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android Designer")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

En la aplicación **Phoneword**, el identificador de **TranslateButton** está establecido en `@+id/TranslateButton`:

[![Configuración del identificador de TranslateButton](hello-android-deepdive-images/vs/04-translatebutton-sml.png "Configuración del identificador de TranslateButton")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

**Main.axml** es el archivo de diseño de la interfaz de usuario de la primera pantalla de la aplicación. La extensión .axml indica que se trata de un archivo de Android Designer (AXML significa *XML de Android*). El nombre *Main* es arbitrario desde el punto de vista de Android, es decir, el archivo de diseño podría tener otro nombre. Cuando se abre **Main.axml** en el IDE, aparece el editor visual de los archivos de diseño de Android, denominado *Android Designer*:

[![Android Designer](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

En la aplicación **Phoneword**, el identificador de **TranslateButton** está establecido en `@+id/TranslateButton`:

[![Configuración del identificador de TranslateButton](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

Al establecer la propiedad `id` de **TranslateButton**, Android Designer asigna el control **TranslateButton** a la clase `Resource` y le asigna un *identificador de recurso* de `TranslateButton`. Esta asignación de un control visual a una clase hace posible la ubicación y el uso de **TranslateButton** y otros controles en el código de la aplicación. Esto se explicará con más detalle cuando se analice el código que activa los controles. Lo único que necesita saber por ahora es que la representación del código de un control está vinculada a la representación visual del control en el diseñador mediante la propiedad `id`.

### <a name="source-view"></a>Vista Código fuente

Todos los elementos definidos en la superficie de diseño se traducen a XML para que Xamarin.Android los pueda usar. Android Designer proporciona una vista de código fuente que contiene el XML que se generó desde el diseñador visual. Para ver este código XML, cambie al panel de **código fuente** situado en la parte inferior izquierda de la vista del diseñador, como se muestra en la siguiente captura de pantalla:

::: zone pivot="windows"

[![Vista Código fuente de Designer](hello-android-deepdive-images/vs/05-source-view-sml.png "Vista Código fuente de Designer")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![Vista Código fuente de Designer](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

Este código fuente XML debe contener cuatro elementos de control: dos **TextView**, un **EditText** y un **Button**. Para obtener más información sobre Android Designer, consulte la guía [Designer Overview](~/android/user-interface/android-designer/index.md) (Introducción al diseñador) de Xamarin Android.

Ya hemos visto las herramientas y los conceptos relacionados con la parte visual de la interfaz de usuario. Ahora veremos el código que activa la interfaz de usuario mientras exploramos las actividades y el ciclo de vida de la actividad.

## <a name="activities-and-the-activity-lifecycle"></a>Actividades y ciclo de vida de actividad

La clase `Activity` contiene el código que activa la interfaz de usuario.
La actividad es responsable de responder a la interacción del usuario y de crear una experiencia de usuario dinámica.
En esta sección se presenta la clase `Activity`, se describe el ciclo de vida de la actividad y se analiza el código que activa la interfaz de usuario de la aplicación **Phoneword**.

### <a name="activity-class"></a>Clase de la actividad

La aplicación **Phoneword** tiene una sola pantalla (actividad). La clase que activa la pantalla se denomina `MainActivity` y se encuentra en el archivo **MainActivity.cs**. El nombre `MainActivity` no tiene especial importancia en Android: aunque la convención es que el nombre de la primera actividad de una aplicación sea `MainActivity`, a Android no le importa si se le asigna otro nombre.

Al abrir **MainActivity.cs**, puede ver que la clase `MainActivity` es una *subclase* de la clase `Activity` y que la actividad está adornada con el atributo [Activity](xref:Android.App.ActivityAttribute):

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

El atributo `Activity` registra la actividad con el manifiesto de Android, lo que permite a Android saber que esta clase forma parte de la aplicación **Phoneword** administrada por este manifiesto. La propiedad `Label` establece el texto que se mostrará en la parte superior de la pantalla.

La propiedad `MainLauncher` le indica a Android que debe mostrar esta actividad cuando se inicia la aplicación. Esta propiedad cobra importancia a medida que se agregan más actividades (pantallas) a la aplicación, como se explica en la guía [Hello, Android Multiscreen](~/android/get-started/hello-android-multiscreen/index.md).

Ahora que ya hemos visto los aspectos básicos de `MainActivity`, vamos a profundizar en el código de la actividad. Para ello, introduciremos el _ciclo de vida de la actividad_.

### <a name="activity-lifecycle"></a>Ciclo de vida de la actividad

En Android, las actividades pasan por distintas fases de un ciclo de vida en función de sus interacciones con el usuario. Las actividades se pueden crear, iniciar, pausar, reanudar, destruir, etc. La clase `Activity` contiene métodos que el sistema llama en determinados momentos del ciclo de vida de la pantalla. En el diagrama siguiente se ilustra la vida típica de una actividad, así como algunos de los métodos correspondientes del ciclo de vida:

[![Ciclo de vida de la actividad](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

Si invalida los métodos `Activity` del ciclo de vida, puede controlar cómo se carga la actividad, cómo reacciona al usuario e incluso qué sucede después de que desaparezca de la pantalla del dispositivo. Por ejemplo, puede invalidar los métodos del ciclo de vida del diagrama anterior para realizar algunas tareas importantes:

- **OnCreate** &ndash; Crea vistas, inicializa las variables y realiza otras tareas de preparación que se deben realizar antes de que el usuario vea la actividad. Solo se llama una vez a este método cuando la actividad se carga en memoria.

- **OnResume**: realiza las tareas que se deben llevar a cabo cada vez que la actividad vuelve a la pantalla del dispositivo.

- **OnPause**: realiza las tareas que se deben llevar a cabo cada vez que la actividad sale de la pantalla del dispositivo.

Al agregar código personalizado a un método del ciclo de vida en `Activity`, *invalida* la *implementación base* de dicho método. Pulse el método del ciclo de vida existente (que ya tiene código asociado) y amplíe dicho método con su propio código. Llame a la implementación base desde dentro del método para asegurarse de que el código original se ejecuta antes que el código nuevo. En la sección siguiente se muestra un ejemplo.

El ciclo de vida de la actividad es una parte importante y compleja de Android. Si quiere obtener más información sobre las actividades cuando finalice la serie de _introducción_, lea la guía [Activity Lifecycle](~/android/app-fundamentals/activity-lifecycle/index.md) (Ciclo de vida de la actividad). En esta guía, el aspecto siguiente en el que nos centraremos es la primera fase del ciclo de vida de la actividad, `OnCreate`.

### <a name="oncreate"></a>OnCreate

Android llama al método `OnCreate` de `Activity` cuando crea la actividad (antes de que se muestre la pantalla al usuario). Puede invalidar el método `OnCreate` del ciclo de vida para crear vistas y preparar la actividad para el usuario:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

En la aplicación **Phoneword**, lo primero que hay que hacer en `OnCreate` es cargar la interfaz de usuario creada en Android Designer. Para cargar la interfaz de usuario, llame a `SetContentView` y pásele el *nombre del diseño de recursos* para el archivo de diseño: **activity_main.axml**. El diseño se encuentra en `Resource.Layout.activity_main`:

```csharp
SetContentView (Resource.Layout.activity_main);
```

Cuando `MainActivity` se inicia, crea una vista que se basa en el contenido del archivo **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

En la aplicación **Phoneword**, lo primero que hay que hacer en `OnCreate` es cargar la interfaz de usuario creada en Android Designer. Para cargar la interfaz de usuario, llame a `SetContentView` y pásele el *nombre del diseño de recursos* para el archivo de diseño: **Main.axml**. El diseño se encuentra en `Resource.Layout.Main`:

```csharp
SetContentView (Resource.Layout.Main);
```

Cuando `MainActivity` se inicia, crea una vista que se basa en el contenido del archivo **Main.axml**. Tenga en cuenta que el nombre del archivo de diseño se hace coincidir con el nombre de la actividad (*Main*.axml es el diseño de *Main*Activity). Esto no es necesario desde el punto de vista de Android, pero a medida que agregue más pantallas a la aplicación, descubrirá que esta convención de nomenclatura permite que sea más fácil hacer coincidir el archivo de código con el archivo de diseño.

::: zone-end

Después de preparar el archivo de diseño, puede empezar a buscar los controles.
Para buscar un control, llame a `FindViewById` y pase el identificador de recurso del control:

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

Ahora que tiene referencias a los controles en el archivo de diseño, puede empezar a programarlos para que respondan a la interacción del usuario.

### <a name="responding-to-user-interaction"></a>Responder a la interacción del usuario

En Android, el evento `Click` escucha la entrada táctil del usuario. En esta aplicación, el evento `Click` se ha controlado con una expresión lambda, pero en su lugar se podría usar un delegado o un controlador de eventos con nombre. El código final de **TranslateButton** se parece a lo siguiente:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>Pruebas, implementación y toques finales

Tanto Visual Studio para Mac como Visual Studio ofrecen numerosas opciones para probar e implementar una aplicación. En esta sección se incluyen las opciones de depuración, se muestra cómo probar las aplicaciones en un dispositivo y se presentan herramientas para crear iconos de aplicación personalizados para densidades de pantalla diferentes.

### <a name="debugging-tools"></a>Herramientas de depuración

Los problemas relacionados con el código de la aplicación pueden ser difíciles de diagnosticar. Para ayudar a diagnosticar problemas de código complejo, puede [establecer un punto de interrupción](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [examinar el código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) o [enviar información a la ventana de registro](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Implementar en un dispositivo

El emulador es un buen punto de partida para implementar y probar una aplicación, pero los usuarios no usarán la aplicación final en un emulador. Conviene probar las aplicaciones en un dispositivo real desde una fase inicial y con frecuencia.

Para que un dispositivo Android se pueda usar para probar aplicaciones, debe estar configurado para el desarrollo. En la guía [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurar el dispositivo para el desarrollo) se proporcionan instrucciones precisas sobre cómo preparar un dispositivo para el desarrollo.

::: zone pivot="windows"

Una vez que haya configurado el dispositivo, puede implementar en él. Para ello, conéctelo, selecciónelo en el cuadro de diálogo **Seleccionar dispositivo** e inicie la aplicación:

![Selección del dispositivo de depuración](hello-android-deepdive-images/vs/06-select-device.png "Selección del dispositivo de depuración")

::: zone-end
::: zone pivot="macos"

Una vez que haya configurado el dispositivo, puede implementar en él. Para ello, conéctelo, pulse **Inicio (Reproducir)** , selecciónelo en el cuadro de diálogo **Seleccionar dispositivo** y pulse **Aceptar**:

[![Seleccionar dispositivo de depuración](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

De este modo se inicia la aplicación en el dispositivo:

[![Escribir Phoneword](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>Establecer iconos para densidades de pantalla diferentes

Los dispositivos Android tienen tamaños de pantalla y resoluciones diferentes, y no todas las imágenes se muestran correctamente en todas las pantallas. Por ejemplo, a continuación se muestra una captura de pantalla de un icono de baja densidad en un Nexus 5 de alta densidad. Observe lo borroso que se ve en comparación con los iconos que lo rodean:

[![Icono borroso](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

Para resolverlo, se recomienda agregar iconos de distintas resoluciones en la carpeta **Recursos**. Android proporciona versiones diferentes de la carpeta **mipmap** para gestionar los iconos de los iniciadores de densidades diferentes: *mdpi* para pantallas de densidad media, *hdpi* para pantallas de alta densidad y *xhdpi*, *xxhdpi* y *xxxhdpi* para pantallas de muy alta densidad. Los iconos de tamaños diferentes se almacenan en las carpetas **mipmap-** correspondientes:

::: zone pivot="windows"

![Carpetas mipmap](hello-android-deepdive-images/vs/07-mipmap-folders.png "Carpetas mipmap")

::: zone-end
::: zone pivot="windows"

[![Carpetas mipmap](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

Android seleccionará el icono de la densidad adecuada:

[![Iconos con la densidad adecuada](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>Generar iconos personalizados

No todos tienen a su disposición un diseñador para crear los iconos e imágenes de inicio personalizados que una aplicación necesita para destacar. A continuación se indican varios enfoques alternativos para generar material gráfico personalizado para la aplicación:

::: zone pivot="windows"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html): es un generador dentro del explorador y basado en web para todo tipo de iconos de Android, con vínculos a otras herramientas útiles de la comunidad. Funciona mejor en Google Chrome.

- Visual Studio: Puede usarlo para crear un conjunto de iconos sencillo para la aplicación directamente en el IDE.

- [Fiverr](https://www.fiverr.com/): elija entre diversos diseñadores para que creen para usted un conjunto de iconos a partir de 5 $. Probablemente elegirá un diseñador al azar, pero se trata de un buen recurso si necesita que le diseñen iconos sobre la marcha.

::: zone-end
::: zone pivot="macos"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html): es un generador dentro del explorador y basado en web para todo tipo de iconos de Android, con vínculos a otras herramientas útiles de la comunidad. Funciona mejor en Google Chrome.

- [Pixelmator](https://www.pixelmator.com/): una aplicación versátil de edición de imágenes para Mac que cuesta aproximadamente 30 $.

- [Fiverr](https://www.fiverr.com/): elija entre diversos diseñadores para que creen para usted un conjunto de iconos a partir de 5 $. Probablemente elegirá un diseñador al azar, pero se trata de un buen recurso si necesita que le diseñen iconos sobre la marcha.

::: zone-end

Para obtener más información sobre los tamaños de icono y los requisitos, consulte la guía [Android Resources](~/android/app-fundamentals/resources-in-android/index.md) (Recursos de Android).

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>Agregar paquetes de Google Play Services

_Google Play Services_ es un conjunto de bibliotecas de complementos que permite a los desarrolladores de Android aprovechar las características más recientes de Google, como Google Maps, Google Cloud Messaging e In-App Billing.
Antes, Xamarin proporcionaba enlaces a todas las bibliotecas de Google Play Services en forma de un único paquete, pero a partir de Visual Studio para Mac, hay un nuevo cuadro de diálogo de proyecto para seleccionar los paquetes de Google Play Services que se incluyen en la aplicación.

Para agregar una o más bibliotecas de Google Play Services, haga clic con el botón derecho en el nodo **Paquetes** del árbol del proyecto y haga clic en **Add Google Play Services…** (Agregar Google Play Services…):

[![Agregar Google Play Services](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

Cuando aparezca el cuadro de diálogo **Add Google Play Services…** (Agregar Google Play Services…), seleccione los paquetes (NuGet) que quiere agregar al proyecto:

[![Seleccionar paquetes](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

Al seleccionar un servicio y hacer clic en **Agregar paquete**, Visual Studio para Mac descarga e instala el paquete que seleccione, así como los paquetes dependientes de Google Play Services que requiere. En algunos casos, podría ver un cuadro de diálogo de **Aceptación de la licencia** que le solicita que haga clic en **Aceptar** para que se instalen los paquetes:

[![Aceptación de licencia](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>Resumen

¡Enhorabuena! Ahora ya debería conocer bien los componentes de una aplicación de Xamarin.Android, así como las herramientas necesarias para crearla.

En el siguiente tutorial de la serie de _introducción_, podrá ampliar la aplicación para que controle varias pantallas a medida que explora la arquitectura y conceptos más avanzados de Android.
