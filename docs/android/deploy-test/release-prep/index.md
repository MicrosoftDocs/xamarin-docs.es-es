---
title: Preparar una aplicación para su lanzamiento
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/21/2018
ms.openlocfilehash: 8c21895918e4d4ac9a82804d4b140fbf7bf798fe
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303526"
---
# <a name="preparing-an-application-for-release"></a>Preparar una aplicación para su lanzamiento

Después de haber codificado y probado una aplicación, es necesario preparar un paquete para la distribución. La primera tarea en la preparación de este paquete es compilar la aplicación para el lanzamiento, lo que implica principalmente establecer algunos atributos de la aplicación.

Para compilar la aplicación para lanzamiento, siga estos pasos:

- **[Especificar el icono de la aplicación](#Specify_the_Application_Icon)** : todas las aplicaciones de Xamarin.Android deben tener un icono de aplicación especificado. Aunque técnicamente no es necesario, algunos mercados lo requieren, como Google Play.

- **[Versión de la aplicación](#Versioning)** : este paso consiste en inicializar o actualizar la información de control de versiones. Esto es importante para las actualizaciones futuras de la aplicación y para asegurarse de que los usuarios sean conscientes de qué versión de la aplicación han instalado.

- **[Reducir el APK](#shrink_apk)** : se puede reducir considerablemente el tamaño del APK final si se usa el enlazador de Xamarin.Android en el código administrado y ProGuard en el código de bytes de Java.

- **[Proteger la aplicación](#protect_app)** : impida que los usuarios o los atacantes depuren, alteren o usen técnicas de ingeniería inversa en la aplicación. Para ello, deshabilite la depuración, ofusque el código administrado, agregue protección contra la depuración y la alteración y use la compilación nativa.

- **[Establecer las propiedades de empaquetado](#Set_Packaging_Properties)** : las propiedades de empaquetado controlan la creación del paquete de aplicaciones Android (APK). En este paso se optimiza el APK, se protegen sus activos y se modulariza el empaquetado según sea necesario.

- **[Compilar](#Compile)** : en este paso se compila el código y los recursos para comprobar que se compila en el modo de lanzamiento.

- **[Archivar para la publicación](#archive)** : en este paso se compila la aplicación y se coloca en un archivo para la firma y la publicación.

Cada uno de estos pasos se describe con más detalle a continuación.

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>Especificar el icono de la aplicación

Se recomienda encarecidamente que cada aplicación de Xamarin.Android especifique un icono de aplicación. Algunos mercados de aplicaciones no permitirán que una aplicación de Android se publique sin uno. La propiedad `Icon` del atributo `Application` se usa para especificar el icono de la aplicación para un proyecto de Xamarin.Android.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Visual Studio 2017 y versiones posteriores, especifique el icono de aplicación en la sección **Manifiesto de Android** de las **Propiedades** del proyecto, como se muestra en la siguiente captura de pantalla:

[![Establecimiento del icono de aplicación](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, también es posible especificar el icono de la aplicación a través de la sección **Aplicación de Android** de **Opciones de proyecto**, como se muestra en la siguiente captura de pantalla:

[![Establecimiento del icono de aplicación](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png#lightbox)

-----

En estos ejemplos, `@drawable/icon` hace referencia a un archivo de icono que se encuentra en **Resources/drawable/icon.png** (tenga en cuenta que la extensión **.png** no está incluida en el nombre del recurso). Este atributo se puede declarar también en el archivo **Properties\AssemblyInfo.cs**, como se muestra en este fragmento de código de ejemplo:

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

Normalmente, `using Android.App` se declara en la parte superior de **AssemblyInfo.cs** (el espacio de nombres del atributo `Application` es `Android.App`); sin embargo, puede que necesite agregar esta instrucción `using` si todavía no está presente.

<a name="Versioning" />

## <a name="version-the-application"></a>Versión de la aplicación

El control de versiones es importante para el mantenimiento y la distribución de aplicaciones de Android. Sin ningún tipo de control de versiones, resulta difícil determinar si una aplicación ha de actualizarse o cómo ha de hacerse. Para ayudar con el control de versiones, Android reconoce dos tipos diferentes de información: 

- **Número de versión**: valor entero (usado internamente por la aplicación y Android) que representa la versión de la aplicación. La mayoría de las aplicaciones empieza con este valor establecido en 1, que luego se incrementa con cada versión. Este valor no tiene relación ni afinidad con el atributo de nombre de versión (ver abajo). Las aplicaciones y los servicios de publicación no deberían mostrar este valor a los usuarios. Este valor se almacena en el archivo **AndroidManifest.xml** como `android:versionCode`. 

- **Nombre de versión**: cadena que solo se usa para comunicar información al usuario sobre la versión de la aplicación (según esté instalada en un dispositivo concreto). El nombre de versión está pensado para mostrarse a los usuarios o en Google Play. Android no usa esta cadena internamente. El nombre de versión puede ser cualquier valor de cadena que ayude a un usuario a identificar la versión instalada en el dispositivo. Este valor se almacena en el archivo **AndroidManifest.xml** como `android:versionName`. 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Visual Studio, estos valores se pueden establecer en la sección **Manifiesto de Android** de las **Propiedades** del proyecto, como se muestra en la siguiente captura de pantalla:

[![Establecimiento del número de versión](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Estos valores se pueden establecer en la sección **Compilar > Aplicación de Android** de **Opciones de proyecto**, como se muestra en la siguiente captura de pantalla:

[![Establecimiento del número de versión](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png#lightbox)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>Reducir el APK

Es posible reducir el tamaño de los APK de Xamarin.Android. Para ello, use el enlazador de Xamarin.Android, que quita el código *administrado* innecesario, y la herramienta *ProGuard* de Android SDK, que elimina el *código de bytes de Java* que no se usa. El proceso de compilación usa primero el enlazador de Xamarin.Android para optimizar la aplicación a nivel de código administrado (C#) y, después, usa ProGuard (si está habilitado) para optimizar el APK a nivel de código de bytes de Java.

### <a name="configure-the-linker"></a>Configurar el enlazador

El modo de versión desactiva el tiempo de ejecución compartido y activa la vinculación, de modo que la aplicación solo envía las partes de Xamarin.Android necesarias en tiempo de ejecución. El *enlazador* de Xamarin.Android usa el análisis estático para determinar qué ensamblados, tipos y miembros de tipo se usan o a los que hace referencia una aplicación de Xamarin.Android. Después, el enlazador descarta todos los ensamblados, tipos y miembros que no se usan (o a los que no se hace referencia). De este modo, se puede producir una reducción significativa del tamaño del paquete. Por ejemplo, tenga en cuenta el ejemplo de [HelloWorld](~/android/deploy-test/linker.md), que experimenta una reducción del 83 % en el tamaño final de su APK: 

- Configuración: Ninguna &ndash; Tamaño de Xamarin.Android 4.2.5 = 17,4 MB.

- Configuración: Solo ensamblados de SDK &ndash; Tamaño de Xamarin.Android 4.2.5 = 3,0 MB.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Establezca las opciones del enlazador en la sección **Opciones de Android** de las **Propiedades** del proyecto:

[![Opciones del enlazador](images/vs/03-linking-sml.png)](images/vs/03-linking.png#lightbox)

El menú desplegable **Linking** (Vinculación) proporciona las siguientes opciones para controlar el enlazador:

- **Ninguno**: esta opción desactiva el enlazador, por lo que no se realizará la vinculación.

- **Solo ensamblados de SDK**: esta opción vinculará solo los ensamblados que [requiere Xamarin.Android](~/cross-platform/internals/available-assemblies.md). 
    No se vincularán otros ensamblados.

- **Ensamblados de SDK y usuario**: se vincularán todos los ensamblados que requiere la aplicación, y no solo los que requiere Xamarin.Android.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Establezca las opciones del enlazador en la pestaña **Enlazador** de la sección **Compilación de Android** de **Opciones de proyecto**, como se muestra en la siguiente captura de pantalla:

[![Opciones del enlazador](images/xs/03-linking-sml.png)](images/xs/03-linking.png#lightbox)

Las opciones para controlar el enlazador son las siguientes:

- **No vincular**: esta opción desactiva el enlazador, por lo que no se realizará la vinculación.

- **Vincular solo ensamblados de SDK**: esta opción vinculará solo los ensamblados que [requiere Xamarin.Android](~/cross-platform/internals/available-assemblies.md). No se vincularán otros ensamblados.

- **Vincular todos los ensamblados**: esta opción vinculará todos los ensamblados requeridos por la aplicación y no solo los requeridos por Xamarin.Android.

-----

La vinculación puede producir algunos efectos secundarios no deseados, por lo que es importante que la aplicación se vuelva a probar en el modo de versión en un dispositivo físico.

### <a name="proguard"></a>ProGuard

*ProGuard* es una herramienta de Android SDK que vincula y oculta el código de Java. ProGuard normalmente se utiliza para crear aplicaciones más pequeñas reduciendo el tamaño de las grandes bibliotecas incluidas, como Google Play Services, en el APK. ProGuard elimina el código de bytes de Java que no se utiliza, de modo que la aplicación resultante es más pequeña. Por ejemplo, el uso de ProGuard en pequeñas aplicaciones de Xamarin.Android suele llegar a reducir su tamaño hasta en un 24 %, mientras que el uso de ProGuard en aplicaciones más grandes con varias dependencias de bibliotecas suele alcanzar una reducción de tamaño aún mayor. 

ProGuard no es una alternativa al enlazador Xamarin.Android. El enlazador de Xamarin.Android vincula el código *administrado*, mientras que ProGuard vincula el código de bytes de Java. El proceso de compilación usa, en primer lugar, el enlazador de Xamarin.Android para optimizar el código administrado (C#) en la aplicación y, después, usa ProGuard (si está habilitado) para optimizar el APK a nivel del código de bytes de Java. 

Cuando se activa **Habilitar ProGuard**, Xamarin.Android ejecuta la herramienta ProGuard en el APK resultante. Se genera un archivo de configuración de ProGuard que ProGuard usa en tiempo de compilación. Xamarin.Android también admite las acciones de compilación personalizadas de *ProguardConfiguration*. Puede agregar un archivo de configuración de ProGuard personalizado al proyecto. Para ello, haga clic en él con el botón derecho y selecciónelo como una acción de compilación, tal como se muestra en este ejemplo: 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Acción de compilación Proguard](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Acción de compilación Proguard](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png#lightbox)

-----

ProGuard está deshabilitado de forma predeterminada. La opción **Habilitar ProGuard** solo está disponible cuando el proyecto se establece en modo de **Lanzamiento**. Se omiten todas las acciones de compilación de ProGuard a menos que **Enable ProGuard** (Habilitar ProGuard) esté activado. La configuración de ProGuard de Xamarin.Android no ofusca el APK y no es posible habilitar la ofuscación, ni siquiera con archivos de configuración personalizados. Si quiere usar la ofuscación, consulte [Application Protection with Dotfuscator](~/android/deploy-test/release-prep/index.md#dotfuscator) (Protección de aplicaciones con Dotfuscator). 

Para obtener más información sobre cómo usar la herramienta ProGuard, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

<a name="protect_app" />

## <a name="protect-the-application"></a>Proteger la aplicación

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>Deshabilitar la depuración

Durante el desarrollo de una aplicación Android, la depuración se realiza mediante el uso del *Protocolo de conexión de depuración de Java* (JDWP). Se trata de una tecnología que permite que herramientas como **adb** se comuniquen con una JVM para fines de depuración. JDWP está activado de forma predeterminada para las compilaciones de depuración de una aplicación Xamarin.Android. Aunque JDWP es importante durante el desarrollo, puede suponer un problema de seguridad para las aplicaciones lanzadas. 

> [!IMPORTANT]
> Deshabilite siempre el estado de depuración en una aplicación lanzada cuando sea posible (a través de JDWP) para obtener acceso completo al proceso de Java y ejecutar un código arbitrario en el contexto de la aplicación si no se deshabilita este estado de depuración.

El manifiesto de Android contiene el atributo `android:debuggable`, que controla si se puede depurar la aplicación o no. Se considera una buena práctica establecer el atributo `android:debuggable` en `false`. La manera más sencilla de hacerlo es mediante la adición de una instrucción de compilación condicional en **AssemblyInfo.cs**: 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

Tenga en cuenta que las compilaciones de depuración establecen automáticamente algunos permisos para que la depuración sea más sencilla (como **Internet** y **ReadExternalStorage**). Sin embargo, las compilaciones de versión solo utilizan los permisos que usted configure explícitamente. Si al cambiar a la compilación de versión, la aplicación pierde un permiso que estaba disponible en la compilación de depuración, compruebe que ha habilitado explícitamente este permiso en la lista **Permisos necesarios** como se describe en [Permisos](~/android/app-fundamentals/permissions.md). 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>Protección de aplicaciones con Dotfuscator

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Incluso con la [depuración deshabilitada](#Disable_Debugging), los atacantes siguen teniendo la posibilidad de volver a empaquetar una aplicación, así como de agregar o quitar opciones de configuración o permisos. Esto les permite usar técnicas de ingeniería inversa en la aplicación, depurarla o alterarla.
Puede usar [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) para ofuscar el código administrado e inyectar código de detección de estado de seguridad en tiempo de ejecución en una aplicación de Xamarin.Android en tiempo de compilación para detectar si la aplicación se está ejecutando en un dispositivo liberado y reaccionar en consecuencia.

Dotfuscator CE está incluido con Visual Studio 2017.
Para usar Dotfuscator, haga clic en **Herramientas > PreEmptive Protection - Dotfuscator**.

Para configurar Dotfuscator CE, consulte [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator) (Usar Dotfuscator Community Edition con Xamarin).
Una vez que lo haya configurado, Dotfuscator CE protegerá automáticamente todas las compilaciones que cree.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Incluso con la [depuración deshabilitada](#Disable_Debugging), los atacantes siguen teniendo la posibilidad de volver a empaquetar una aplicación, así como de agregar o quitar opciones de configuración o permisos. Esto les permite usar técnicas de ingeniería inversa en la aplicación, depurarla o alterarla.
Aunque no es compatible con Visual Studio para Mac, puede usar [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) con Visual Studio para ofuscar el código administrado e inyectar código de detección de estado de seguridad en tiempo de ejecución en una aplicación de Xamarin.Android en tiempo de compilación para detectar si la aplicación se está ejecutando en un dispositivo liberado y reaccionar en consecuencia.

Para configurar Dotfuscator CE, consulte [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator) (Usar Dotfuscator Community Edition con Xamarin).
Una vez que lo haya configurado, Dotfuscator CE protegerá automáticamente todas las compilaciones que cree.

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>Empaquetar ensamblados en código nativo

Cuando esta opción está habilitada, los ensamblados se agrupan en una biblioteca compartida nativa. Esta opción garantiza la seguridad del código y protege los ensamblados administrados incrustándolos en archivos binarios nativos.

Esta opción requiere una licencia empresarial y solo está disponible cuando está deshabilitada la opción **Use Fast Deployment (Utilizar la implementación rápida)** . La opción **Bundle assemblies into native code (Agrupar los ensamblados en el código nativo)** está deshabilitada de forma predeterminada.

Tenga en cuenta que la opción **Bundle into Native Code (Agrupar en código nativo)** *no* implica que los ensamblados se compilen en código nativo. No es posible utilizar [**Compilación AOT**](#aot) para compilar ensamblados en código nativo. Actualmente solo es una función experimental y no está destinada a la tareas de producción.

<a name="aot" />

### <a name="aot-compilation"></a>Compilación AOT

La opción **compilación AOT** (de la página [Propiedades de empaquetado](#Set_Packaging_Properties)) habilita la compilación Ahead Of Time (AOT) de los ensamblados. Cuando esta opción está habilitada, la sobrecarga de inicio Just-In-Time (JIT) se minimiza al precompilar ensamblados antes del tiempo de ejecución. El código nativo resultante se incluye en el APK junto con los ensamblados sin compilar. Esto da como resultado un tiempo de inicio de la aplicación más corto, pero a costa de tamaños APK ligeramente más grandes.

La opción **compilación AOT** necesita una licencia de Enterprise o superior. **Compilación AOT** solo está disponible cuando el proyecto se configura para el modo de versión, y está deshabilitado de manera predeterminada. Para obtener más información sobre la compilación AOT, consulte [AOT](https://www.mono-project.com/docs/advanced/aot/).

#### <a name="llvm-optimizing-compiler"></a>Compilador de optimización de LLVM

El _Compilador de optimización de LLVM_ creará un código compilado más pequeño y rápido y convertirá ensamblados compilados mediante AOT en código nativo, pero a costa de tiempos de compilación más lentos. El compilador de LLVM está deshabilitado de manera predeterminada. Para usar el compilador de LLVM, es necesario habilitar primero la opción **Compilación de AOT** en la página [Propiedades de empaquetado](#Set_Packaging_Properties).

> [!NOTE]
> La opción **Compilador de optimización de LLVM** necesita una licencia Enterprise.  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>Establecer las propiedades de empaquetado

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Las propiedades de empaquetado se pueden establecer en la sección **Android Options** (Opciones de Android) de las **Properties** (Propiedades) del proyecto, como se muestra en la siguiente captura de pantalla:

[![Propiedades de empaquetado](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Las propiedades de empaquetado se pueden establecer en **Opciones de proyecto**, como se muestra en la siguiente captura de pantalla:

[![Propiedades de empaquetado](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png#lightbox)

-----

Muchas de estas propiedades, como **Use Shared Runtime** (Usar tiempo de ejecución compartido) y **Use Fast Deployment** (Usar implementación rápida), están pensadas para el modo de depuración. Pero cuando la aplicación está configurada para el modo de versión, hay otras opciones que determinan cómo [se optimiza la aplicación para el tamaño y la velocidad de ejecución](#shrink_apk), [cómo se protege contra la alteración](#protect_app) y cómo puede empaquetarse para admitir distintas arquitecturas y restricciones de tamaño.

### <a name="specify-supported-architectures"></a>Especificar arquitecturas compatibles

Al preparar una aplicación Xamarin.Android para publicarla, es necesario especificar las arquitecturas de CPU que se admiten. Un APK único puede contener código máquina para admitir varias arquitecturas diferentes. Consulte [Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md) para obtener más información sobre la compatibilidad con varias arquitecturas de CPU.

### <a name="generate-one-package-apk-per-selected-abi"></a>Generar un paquete (.APK) por ABI seleccionado

Cuando esta opción está habilitada, se creará un APK para cada uno de los ABI admitidos (seleccionados en la pestaña **Opciones avanzadas**, como se describe en [Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)) en lugar de un único APK grande para todos los ABI que se admiten. Esta opción solo está disponible cuando el proyecto se configura para el modo de versión, y está deshabilitado de manera predeterminada.

### <a name="multi-dex"></a>Multi-Dex

Cuando la opción **Habilitar Multi-Dex** está habilitada, se usan herramientas del SDK de Android para omitir el límite de 65 000 métodos del formato de archivo **.dex**. La limitación de 65 000 métodos se basa en el número de métodos de Java a los que una aplicación _hace referencia_ (incluidos los de las bibliotecas de las que depende la aplicación) &ndash; no se basa en el número de métodos que se _escriben en el código fuente_. Si una aplicación solo define algunos métodos pero usa muchos (o bibliotecas grandes), es posible que se supere el límite de 65 000.

Es posible que una aplicación no use todos los métodos de todas las bibliotecas a los que se haga referencia, de modo que es posible que una herramienta como ProGuard (vea más arriba) pueda eliminar del código los métodos que no se usen. La práctica recomendada es habilitar **Habilitar Multi-Dex** solo si es absolutamente necesario, por ejemplo, si aplicación aún hace referencia a más de 65 000 métodos de Java incluso después de usar ProGuard.

Para más información sobre Multi-Dex, vea [Configurar aplicaciones con más de 64 000 métodos](https://developer.android.com/tools/building/multidex.html).

<a name="Compile" />

## <a name="compile"></a>Compile

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Una vez que haya completado todos los pasos anteriores, la aplicación estará lista para la compilación. Seleccione **Compilar > Recompilar solución** para comprobar que se compila correctamente en el modo de versión. Tenga en cuenta que en este paso todavía no se produce un APK.

En [Signing the App Package](~/android/deploy-test/signing/index.md) (Firmar el paquete de aplicación) se describe con más detalle el proceso de empaquetar y firmar.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Cuando haya concluido todos los pasos anteriores, compile la aplicación (seleccione **Compilar > Compilar todo**) para comprobar que se compila correctamente en el modo de versión. Tenga en cuenta que en este paso todavía no se produce un APK.

-----

<a name="archive" />

## <a name="archive-for-publishing"></a>Archivo para la publicación

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para empezar el proceso de publicación, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione el elemento de menú contextual **Archivo…** :

[![Aplicación Archive](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png#lightbox)

La opción **Archivo…** inicia **Archive Manager** y comienza el proceso de archivado del paquete de aplicaciones, como se muestra en esta captura de pantalla:

[![Archive Manager](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png#lightbox)

Otra forma de crear un archivo consiste en hacer clic con el botón derecho en la solución en el **Explorador de soluciones** y seleccionar **Archivar todo…** , con lo que se compila la solución y se archivan todos los proyectos de Xamarin que pueden generar un archivo:

[![Archivar todo](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png#lightbox)

Tanto la opción **Archivo** como la opción **Archivar todo** inician automáticamente **Archive Manager**. Para iniciar **Archive Manager** directamente, haga clic en el elemento de menú **Herramientas > Archive Manager…** :

[![Iniciar Archive Manager](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png#lightbox)

Puede ver los archivos de la solución en cualquier momento. Para ello, haga clic con el botón derecho en el nodo **Solución** y seleccione **Ver archivos**:

[![Ver archivos](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png#lightbox)

### <a name="the-archive-manager"></a>Archive Manager

**Archive Manager** está formado por una **lista de soluciones**, una **lista de archivos** y un **panel de detalles**:

[![Paneles de Archive Manager](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png#lightbox)

En la **lista de soluciones** se muestran todas las soluciones que tienen como mínimo un proyecto archivado. En la **lista de soluciones** se incluyen las secciones siguientes:

- **Solución actual**: muestra la solución actual. Tenga en cuenta que esta área puede estar vacía si la solución actual no tiene ningún archivo.
- **Todos los archivos**: muestra todas las soluciones que tienen un archivo.
- Cuadro de texto de **Búsqueda** (en la parte superior): filtra las soluciones incluidas en la lista **Todos los archivos** según la cadena de búsqueda especificada en el cuadro de texto.

En la **lista de archivos** se muestra una lista de todos los archivos de la solución seleccionada. En la **lista de archivos** se incluyen las secciones siguientes:

- **Nombre de la solución seleccionada**: muestra el nombre de la solución seleccionada en la **lista de soluciones**. Toda la información que se muestra en la **lista de archivos** hace referencia a esta solución seleccionada.
- **Filtro de plataformas**: este campo permite filtrar archivos por tipo de plataforma (por ejemplo, iOS o Android).
- **Elementos de archivo**: muestra los archivos de la solución seleccionada. Cada elemento de esta lista incluye el nombre del proyecto, la fecha de creación y la plataforma. También puede incluir información adicional, como el progreso cuando se está archivando o publicando un elemento.

En el **panel de detalles** se muestra información adicional sobre cada archivo. El panel también permite al usuario iniciar el flujo de trabajo de distribución o abrir la carpeta donde se ha creado la distribución. En la sección **Comentarios de la compilación** se pueden incluir comentarios de compilación en el archivo.

### <a name="distribution"></a>Distribución

Cuando una versión archivada de la aplicación esté lista para la publicación, seleccione el archivo en **Archive Manager** y haga clic en el botón **Distribuir…** :

[![Botón Distribuir](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png#lightbox)

En el cuadro de diálogo **Canal de distribución** se muestra información sobre la aplicación, una indicación del progreso del flujo de trabajo de distribución y las opciones de canales de distribución. En la primera ejecución, se ofrecen dos opciones:

[![Seleccionar el canal de distribución](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png#lightbox)

Es posible elegir uno de los siguientes canales de distribución:

- **Ad hoc**: guarda un APK firmado en el disco que se puede instalar como prueba en dispositivos Android. Vaya a [Signing the App Package](~/android/deploy-test/signing/index.md) (Firmar el paquete de aplicación) para obtener información sobre cómo crear una identidad de firma de Android, crear un certificado de firma para aplicaciones de Android y publicar una versión _ad hoc_ de la aplicación en disco. Esta es una buena forma de crear un APK para realizar pruebas.

- **Google Play**: publica un APK firmado en Google Play. Vaya a [Publicación en Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) para obtener información sobre cómo firmar y publicar un APK en Google Play Store.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para empezar el proceso de publicación, seleccione **Compilar > Archivo para publicar**:

[![Archivo para la publicación](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png#lightbox)

La opción **Archive for Publishing** (Archivo para la publicación) compila el proyecto y lo empaqueta en un archivo de almacenamiento. En la opción del menú **Archivar todo** se almacenan todos los proyectos archivables de la solución. Ambas opciones abren automáticamente **Archive Manager** cuando se completan las operaciones de compilación y empaquetado:

[![Vista Archive](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png#lightbox)

En este ejemplo, en **Archive Manager** solo se muestra una aplicación archivada, **MyApp**. Observe que el campo de comentario permite guardar un breve comentario con el archivo. Para publicar una versión archivada de una aplicación de Xamarin.Android, seleccione la aplicación en **Archive Manager** y haga clic en **Firmar y distribuir…** , como se muestra más arriba. En el cuadro de diálogo resultante **Sign and Distribute** (Firmar y distribuir), se presentan dos opciones:

[![Firmar y distribuir](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png#lightbox)

Desde aquí, es posible seleccionar el canal de distribución:

- **Ad hoc**: guarda un APK firmado en el disco para que se pueda transferir localmente a dispositivos Android. Vaya a [Signing the App Package](~/android/deploy-test/signing/index.md) (Firmar el paquete de aplicación) para obtener información sobre cómo crear una identidad de firma de Android, crear un certificado de firma para aplicaciones de Android y publicar una versión &ldquo;ad hoc&rdquo; de la aplicación en disco. Esta es una buena forma de crear un APK para realizar pruebas.

- **Google Play**: publica un APK firmado en Google Play.
    Vaya a [Publicación en Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) para obtener información sobre cómo firmar y publicar un APK en Google Play Store.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Dispositivos de varios núcleos y Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [Arquitecturas de CPU](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](https://www.mono-project.com/docs/advanced/aot/)
- [Reducción del código y los recursos](https://developer.android.com/tools/help/proguard.html)
- [Configuración de aplicaciones con más de 64 000 métodos](https://developer.android.com/tools/building/multidex.html)
