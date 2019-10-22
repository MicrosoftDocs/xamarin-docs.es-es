---
title: Espacio aislado de una aplicación de Xamarin. Mac
description: En este artículo se describe el espacio aislado de una aplicación de Xamarin. Mac para su lanzamiento en App Store. Abarca todos los elementos que se incluyen en el espacio aislado, como directorios de contenedor, derechos, permisos definidos por el usuario, separación de privilegios y aplicación de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 4558a9bd19810f8759010861d8a2e4b8cab09c56
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770299"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Espacio aislado de una aplicación de Xamarin. Mac

_En este artículo se describe el espacio aislado de una aplicación de Xamarin. Mac para su lanzamiento en App Store. Abarca todos los elementos que se incluyen en el espacio aislado, como directorios de contenedor, derechos, permisos definidos por el usuario, separación de privilegios y aplicación de kernel._

## <a name="overview"></a>Información general

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene la misma capacidad de usar un espacio aislado para una aplicación cuando se trabaja con Objective-C o SWIFT.

[![Un ejemplo de la aplicación en ejecución](sandboxing-images/intro01.png "Un ejemplo de la aplicación en ejecución")](sandboxing-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con el espacio aislado en una aplicación de Xamarin. Mac y de todos los elementos que se incluyen en el espacio aislado: directorios de contenedor, derechos, permisos definidos por el usuario, separación de privilegios y cumplimiento del kernel. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican los atributos `Register` y `Export` que se usan para C# conectar las clases a Objective-C. objetos y elementos de la interfaz de usuario.

## <a name="about-the-app-sandbox"></a>Acerca del espacio aislado de la aplicación

El espacio aislado de la aplicación proporciona una sólida defensa contra los daños causados por una aplicación malintencionada que se ejecuta en un equipo Mac limitando el acceso que una aplicación tiene a los recursos del sistema.

Una aplicación no en espacio aislado tiene los derechos completos del usuario que ejecuta la aplicación y puede tener acceso o hacer todo lo que el usuario puede. Si la aplicación contiene carencias de seguridad (o cualquier marco que esté usando), un pirata informático podría aprovechar esos puntos débiles y usar la aplicación para tomar el control del equipo Mac en el que se está ejecutando.

Al limitar el acceso a los recursos por aplicación, una aplicación en espacio aislado proporciona una línea de defensa contra robos, daños o intenciones maliciosas en la parte de una aplicación que se ejecuta en el equipo del usuario.

El espacio aislado de la aplicación es una tecnología de control de acceso integrada en macOS (aplicada en el nivel de kernel) que proporciona una estrategia doble:

1. El espacio aislado de la aplicación permite al desarrollador describir _Cómo_ interactuará una aplicación con el sistema operativo y, de esta manera, solo se conceden los derechos de acceso necesarios para realizar el trabajo y no más.
2. El espacio aislado de la aplicación permite al usuario conceder sin problemas más acceso al sistema a través de los cuadros de diálogo abrir y guardar, las operaciones de arrastrar y colocar y otras interacciones de usuario comunes.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparación de la implementación del espacio aislado de la aplicación

Los elementos del espacio aislado de la aplicación que se tratarán en detalle en el artículo son los siguientes:

- Directorios de contenedor
- Derechos
- Permisos determinados por el usuario
- Separación de privilegios
- Aplicación del kernel

Una vez que comprenda estos detalles, podrá crear un plan para adoptar el espacio aislado de la aplicación en la aplicación de Xamarin. Mac.

En primer lugar, deberá determinar si la aplicación es una buena candidata para el espacio aislado (la mayoría de las aplicaciones son). A continuación, tendrá que resolver las incompatibilidades de API y determinar qué elementos del espacio aislado de la aplicación necesitará. Por último, consulte uso de la separación de privilegios para maximizar el nivel de defensa de la aplicación.

Al adoptar el espacio aislado de la aplicación, algunas ubicaciones del sistema de archivos que usa la aplicación serán diferentes. En particular, la aplicación tendrá un directorio de contenedor que se utilizará para los archivos de compatibilidad de aplicaciones, las bases de datos, las cachés y otros archivos que no sean documentos de usuario. MacOS y Xcode proporcionan compatibilidad para migrar este tipo de archivos desde sus ubicaciones heredadas al contenedor.

## <a name="sandboxing-quick-start"></a>Inicio rápido de espacio aislado

En esta sección, vamos a crear una aplicación sencilla de Xamarin. Mac que usa una vista Web (que requiere una conexión de red restringida en el espacio aislado, a menos que se solicite específicamente) como ejemplo de introducción al espacio aislado de la aplicación.

Comprobaremos que la aplicación está en un espacio aislado y aprenderá a solucionar los errores comunes de espacio aislado de la aplicación.

### <a name="creating-the-xamarinmac-project"></a>Crear el proyecto de Xamarin. Mac

Vamos a hacer lo siguiente para crear nuestro proyecto de ejemplo:

1. Inicie Visual Studio para Mac y haga clic en la **nueva solución.** .
2. En el cuadro de diálogo **nuevo proyecto** , seleccione **Mac**  > **App**  > **aplicación de coco**:

    [![Creación de una nueva aplicación de cacao](sandboxing-images/sample01.png "Creación de una nueva aplicación de cacao")](sandboxing-images/sample01-large.png#lightbox)
3. Haga clic en el botón **siguiente** , escriba `MacSandbox` del nombre del proyecto y haga clic en el botón **crear** :

    [![Escribir el nombre de la aplicación](sandboxing-images/sample02.png "Escribir el nombre de la aplicación")](sandboxing-images/sample02-large.png#lightbox)
4. En el **Panel de solución**, haga doble clic en el archivo **Main. Storyboard** para abrirlo para su edición en Xcode:

    [![Edición del guión gráfico principal](sandboxing-images/sample03.png "Edición del guión gráfico principal")](sandboxing-images/sample03-large.png#lightbox)
5. Arrastre una **vista Web** a la ventana, cambie su tamaño para rellenar el área de contenido y establecerla para que crezca y se reduzca con la ventana:

    [![Agregar una vista Web](sandboxing-images/sample04.png "Agregar una vista Web")](sandboxing-images/sample04-large.png#lightbox)
6. Cree una salida para la vista web denominada `webView`:

    [![Creación de una nueva salida](sandboxing-images/sample05.png "Creación de una nueva salida")](sandboxing-images/sample05-large.png#lightbox)
7. Vuelva a Visual Studio para Mac y haga doble clic en el archivo **ViewController.CS** en el **Panel de solución** para abrirlo para su edición.
8. Agregue la siguiente instrucción using: `using WebKit;`
9. Haga que el método `ViewDidLoad` tenga el aspecto siguiente:

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Guarde los cambios.

Ejecute la aplicación y asegúrese de que el sitio web de Apple se muestra en la ventana de la siguiente manera:

[![Mostrar una ejecución de aplicación de ejemplo](sandboxing-images/sample06.png "Mostrar una ejecución de aplicación de ejemplo")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Firma y aprovisionamiento de la aplicación

Antes de poder habilitar el espacio aislado de la aplicación, primero debemos aprovisionar y firmar nuestra aplicación de Xamarin. Mac.

Haga lo siguiente:

1. Inicie sesión en el portal para desarrolladores de Apple:

    [![Iniciar sesión en el portal para desarrolladores de Apple](sandboxing-images/sign01.png "Iniciar sesión en el portal para desarrolladores de Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Seleccione **certificados, identificadores & perfiles**:

    [![Seleccionar certificados, identificadores & perfiles](sandboxing-images/sign02.png "Seleccionar certificados, identificadores & perfiles")](sandboxing-images/sign02-large.png#lightbox)
3. En **aplicaciones Mac**, seleccione **Identifiers (identificadores**):

    [![Seleccionar identificadores](sandboxing-images/sign03.png "Seleccionar identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Cree un nuevo identificador para la aplicación:

    [![Creación de un nuevo identificador de aplicación](sandboxing-images/sign04.png "Creación de un nuevo identificador de aplicación")](sandboxing-images/sign04-large.png#lightbox)
5. En **perfiles de aprovisionamiento**, seleccione **desarrollo**:

    [![Seleccionar desarrollo](sandboxing-images/sign05.png "Seleccionar desarrollo")](sandboxing-images/sign05-large.png#lightbox)
6. Cree un nuevo perfil y seleccione **desarrollo de aplicaciones Mac**:

    [![Crear un nuevo perfil](sandboxing-images/sign06.png "Crear un nuevo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Seleccione el ID. de aplicación que hemos creado anteriormente:

    [![Selección del identificador de la aplicación](sandboxing-images/sign07.png "Selección del identificador de la aplicación")](sandboxing-images/sign07-large.png#lightbox)
8. Seleccione los desarrolladores de este perfil:

    [![Agregar desarrolladores](sandboxing-images/sign08.png "Agregar desarrolladores")](sandboxing-images/sign08-large.png#lightbox)
9. Seleccione los equipos para este perfil:

    [![Selección de los equipos permitidos](sandboxing-images/sign09.png "Selección de los equipos permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Asigne un nombre al perfil:

    [![Asignar un nombre al perfil](sandboxing-images/sign10.png "Asignar un nombre al perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Haga clic en el botón **listo** .

> [!IMPORTANT]
> En algunos casos, es posible que tenga que descargar el nuevo perfil de aprovisionamiento del portal para desarrolladores de Apple directamente y hacer doble clic en él para instalarlo. También es posible que necesite detener y reiniciar Visual Studio para Mac antes de poder acceder al nuevo perfil.

A continuación, es necesario cargar el nuevo ID. de aplicación y el perfil en el equipo de desarrollo. Vamos a hacer lo siguiente:

1. Inicie Xcode y seleccione **preferencias** en el menú de **Xcode** :

    ![Editar cuentas en Xcode](sandboxing-images/sign11.png "Editar cuentas en Xcode")
2. Haga clic en el botón **Ver detalles..** .:

    ![Hacer clic en el botón Ver detalles](sandboxing-images/sign12.png "Hacer clic en el botón Ver detalles")
3. Haga clic en el botón **Actualizar** (en la esquina inferior izquierda).
4. Haga clic en el botón **listo** .

A continuación, es necesario seleccionar el nuevo identificador de aplicación y el perfil de aprovisionamiento en nuestro proyecto de Xamarin. Mac. Vamos a hacer lo siguiente:

1. En el **Panel de solución**, haga doble clic en el archivo **info. plist** para abrirlo para su edición.
2. Asegúrese de que el **identificador de paquete** coincide con el identificador de aplicación que hemos creado anteriormente (ejemplo: `com.appracatappra.MacSandbox`):

    [![Editar el identificador de paquete](sandboxing-images/sign13.png "Editar el identificador de paquete")](sandboxing-images/sign13-large.png#lightbox)
3. A continuación, haga doble clic en el archivo **contitles. plist** y asegúrese de que el **almacén de clave-valor de icloud** y los **contenedores de ICLOUD** coinciden con el identificador de aplicación que hemos creado anteriormente (ejemplo: `com.appracatappra.MacSandbox`):

    [![Edición del archivo de derechos. plist](sandboxing-images/sign17.png "Edición del archivo de derechos. plist")](sandboxing-images/sign17-large.png#lightbox)
4. Guarde los cambios.
5. En el **Panel de solución**, haga doble clic en el archivo de proyecto para abrir sus opciones de edición:

    ![Editign las opciones de la solución](sandboxing-images/sign14.png "Editign las opciones de la solución")
6. Seleccione **firma de Mac**y, a continuación, active la casilla **firmar el** paquete de aplicación y **firme el paquete del instalador**. En **Perfil de aprovisionamiento**, seleccione el que hemos creado anteriormente:

    ![Configuración del perfil de aprovisionamiento](sandboxing-images/sign15.png "Configuración del perfil de aprovisionamiento")
7. Haga clic en el botón **listo** .

> [!IMPORTANT]
> Es posible que tenga que salir y reiniciar Visual Studio para Mac para que reconozca el nuevo identificador de aplicación y el perfil de aprovisionamiento que instaló Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solución de problemas de aprovisionamiento

En este momento, debe intentar ejecutar la aplicación y asegurarse de que todo esté firmado y aprovisionado correctamente. Si la aplicación todavía se ejecuta como antes, todo es correcto. En caso de que se produzca un error, es posible que aparezca un cuadro de diálogo similar al siguiente:

[![Cuadro de diálogo de un problema de aprovisionamiento de ejemplo](sandboxing-images/sign16.png "Cuadro de diálogo de un problema de aprovisionamiento de ejemplo")](sandboxing-images/sign16-large.png#lightbox)

Estas son las causas más comunes de los problemas de aprovisionamiento y firma:

- El identificador del lote de aplicaciones no coincide con el identificador de la aplicación del perfil seleccionado.
- El identificador de desarrollador no coincide con el identificador de desarrollador del perfil seleccionado.
- El UUID del equipo Mac en el que se está probando no se registra como parte del perfil seleccionado.

En caso de un problema, corrija el problema en el portal para desarrolladores de Apple, actualice los perfiles en Xcode y realice una compilación limpia en Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar el espacio aislado de la aplicación

Para habilitar el espacio aislado de la aplicación, seleccione una casilla en las opciones de los proyectos. Haga lo siguiente:

1. En el **Panel de solución**, haga doble clic en el archivo **contitles. plist** para abrirlo para su edición.
2. Active **Habilitar derechos** y **habilitar espacio aislado de aplicaciones**:

    [![Edición de derechos y habilitación del espacio aislado](sandboxing-images/sign17.png "Edición de derechos y habilitación del espacio aislado")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios.

En este punto, ha habilitado el espacio aislado de la aplicación, pero no ha proporcionado el acceso a la red necesario para la vista Web. Si ejecuta la aplicación ahora, debería obtener una ventana en blanco:

[![Mostrando el acceso web que se está bloqueando](sandboxing-images/sample08.png "Mostrando el acceso web que se está bloqueando")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Comprobando que la aplicación está en un espacio aislado

Aparte del comportamiento de bloqueo de recursos, hay tres formas principales de saber si una aplicación de Xamarin. Mac se ha aislado correctamente:

1. En el buscador, compruebe el contenido de la carpeta `~/Library/Containers/`: Si la aplicación está en un espacio aislado, habrá una carpeta denominada "identificador de lote de la aplicación" (ejemplo: `com.appracatappra.MacSandbox`):

    [![Abrir el paquete de la aplicación](sandboxing-images/sample09.png "Abrir el paquete de la aplicación")](sandboxing-images/sample09-large.png#lightbox)
2. El sistema ve la aplicación como espacio aislado en el monitor de actividad:
    - Inicie el monitor de actividad (en `/Applications/Utilities`).
    - Elija **ver**  > **columnas** y asegúrese de que el elemento de menú **espacio aislado** está activado.
    - Asegúrese de que la columna de espacio aislado Lee `Yes` para la aplicación:

    [![Comprobando la aplicación en el monitor de actividad](sandboxing-images/sample10.png "Comprobando la aplicación en el monitor de actividad")](sandboxing-images/sample10-large.png#lightbox)
3. Compruebe que el archivo binario de la aplicación está en un espacio aislado:
    - Inicie la aplicación terminal.
    - Vaya al directorio Applications `bin`.
    - Ejecute este comando: `codesign -dvvv --entitlements :- executable_path` (donde `executable_path` es la ruta de acceso a la aplicación):

    [![Comprobar la aplicación en la línea de comandos](sandboxing-images/sample11.png "Comprobar la aplicación en la línea de comandos")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Depurar una aplicación en espacio aislado

El depurador se conecta a las aplicaciones de Xamarin. Mac a través de TCP, lo que significa que, de forma predeterminada, cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, se obtiene un error que indica que *no se puede conectar al depurador.* .

[![Establecer las opciones necesarias](sandboxing-images/debug01.png "Establecer las opciones necesarias")](sandboxing-images/debug01-large.png#lightbox)

El permiso **permitir conexiones de red salientes (cliente)** es el requerido para el depurador, lo que permite la depuración de forma normal. Dado que no se puede depurar sin él, se ha actualizado el `CompileEntitlements` destino de `msbuild` para agregar automáticamente ese permiso a los derechos de cualquier aplicación que esté en un espacio aislado para las compilaciones de depuración únicamente. Las compilaciones de versión deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="resolving-an-app-sandbox-violation"></a>Resolución de una infracción de espacio aislado de la aplicación

Se produce una infracción del espacio aislado de la aplicación si una aplicación de Xamarin. Mac en espacio aislado intentó acceder a un recurso que no se ha permitido explícitamente. Por ejemplo, nuestra vista Web ya no puede mostrar el sitio web de Apple.

La fuente más común de infracciones de espacio aislado de la aplicación se produce cuando la configuración de derechos especificada en Visual Studio para Mac no coincide con los requisitos de la aplicación. De nuevo, vuelva a nuestro ejemplo, los derechos de conexión de red que faltan y que evitan que la vista Web funcione.

#### <a name="discovering-app-sandbox-violations"></a>Detección de infracciones de espacio aislado de la aplicación

Si sospecha que se está produciendo una infracción de espacio aislado en la aplicación de Xamarin. Mac, la manera más rápida de detectar el problema es mediante la aplicación de **consola** .

Haga lo siguiente:

1. Compile la aplicación en cuestión y ejecútela desde Visual Studio para Mac.
2. Abra la aplicación de **consola** (desde `/Applications/Utilties/`).
3. Seleccione **todos los mensajes** en la barra lateral y escriba `sandbox` en la búsqueda:

    [![Un ejemplo de un problema de espacio aislado en la consola](sandboxing-images/resolve01.png "Un ejemplo de un problema de espacio aislado en la consola")](sandboxing-images/resolve01-large.png#lightbox)

En la aplicación de ejemplo anterior, puede ver que el kerning está bloqueando el tráfico `network-outbound` debido al espacio aislado de la aplicación, ya que no hemos solicitado ese derecho.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Corrección de infracciones de espacio aislado de la aplicación con derechos

Ahora que hemos aprendido a encontrar infracciones de espacio aislado de aplicaciones, veamos cómo se pueden resolver ajustando los derechos de la aplicación.

Haga lo siguiente:

1. En el **Panel de solución**, haga doble clic en el archivo **contitles. plist** para abrirlo para su edición.
2. En la sección **derechos** , active la casilla **permitir conexiones de red salientes (cliente)** :

    [![Editar los derechos](sandboxing-images/sign17.png "Editar los derechos")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios en la aplicación.

Si hacemos lo anterior para nuestra aplicación de ejemplo, compilarlo y ejecutarlo, el contenido web ahora se mostrará según lo previsto.

## <a name="the-app-sandbox-in-depth"></a>El espacio aislado de la aplicación en profundidad

Los mecanismos de control de acceso proporcionados por el espacio aislado de la aplicación son pocos y fáciles de entender. Sin embargo, la manera en que cada aplicación adoptará el espacio aislado de la aplicación es única y se basa en los requisitos de la aplicación.

Dado el mejor esfuerzo para proteger la aplicación de Xamarin. Mac contra la vulnerabilidad de código malintencionado, solo debe existir una vulnerabilidad en la aplicación (o en una de las bibliotecas o marcos que consume) para obtener el control de las interacciones de la aplicación con el integrado.

El espacio aislado de la aplicación se diseñó para evitar la adquisición (o limitar el daño que puede provocar) al permitirle especificar las interacciones de la aplicación con el sistema. El sistema solo concederá acceso al recurso que la aplicación necesita para realizar su trabajo y nada más.

Al diseñar el espacio aislado de la aplicación, está diseñando para un escenario en el peor de los casos. Si la aplicación se ve comprometida por código malintencionado, se limita a acceder solo a los archivos y recursos del espacio aislado de la aplicación.

### <a name="entitlements-and-system-resource-access"></a>Derechos y acceso a recursos del sistema

Como vimos anteriormente, se concede a una aplicación de Xamarin. Mac que no está en un espacio aislado los derechos completos y el acceso del usuario que ejecuta la aplicación. Si se ve comprometido por código malintencionado, una aplicación no protegida puede actuar como agente para un comportamiento hostil, con un gran potencial para hacer daño.

Al habilitar el espacio aislado de la aplicación, se quitan todos los privilegios menos un conjunto mínimo de privilegios, que luego se vuelven a habilitar en función de los derechos de la aplicación de Xamarin. Mac.

Modifique los recursos de espacio aislado de la aplicación de la aplicación editando su archivo **contitles. plist** y comprobando o seleccionando los derechos necesarios en los cuadros desplegables editores:

[![Editar los derechos](sandboxing-images/sign17.png "Editar los derechos")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Directorios de contenedor y acceso al sistema de archivos

Cuando la aplicación de Xamarin. Mac adopta el espacio aislado de la aplicación, tiene acceso a las siguientes ubicaciones:

- **El directorio del contenedor de la aplicación** : en la primera ejecución, el sistema operativo crea un _Directorio de contenedor_ especial al que van todos sus recursos, a los que solo puede tener acceso. La aplicación tendrá acceso total de lectura y escritura a este directorio.
- **Directorios de contenedores de grupos de aplicaciones** : se puede conceder acceso a la aplicación a uno o varios _contenedores de grupos_ que se comparten entre las aplicaciones del mismo grupo.
- **Archivos especificados** por el usuario: la aplicación obtiene automáticamente acceso a los archivos que el usuario abre o arrastra explícitamente a la aplicación.
- **Elementos relacionados** : con los derechos adecuados, la aplicación puede tener acceso a un archivo con el mismo nombre pero con una extensión diferente. Por ejemplo, un documento que se ha guardado como un archivo de `.txt` y un `.pdf`.
- **Directorios temporales, directorios de la herramienta de línea de comandos y ubicaciones específicas** de lectura internacional: la aplicación tiene distintos grados de acceso a los archivos de otras ubicaciones bien definidas, tal y como se especifica en el sistema.

#### <a name="the-app-container-directory"></a>Directorio del contenedor de la aplicación

El directorio del contenedor de la aplicación de una aplicación de Xamarin. Mac tiene las siguientes características:

- Está en una ubicación oculta en el directorio principal del usuario (normalmente `~Library/Containers`) y se puede tener acceso a ella con la función `NSHomeDirectory` (consulte a continuación) dentro de la aplicación. Dado que se encuentra en el directorio particular, cada usuario obtendrá su propio contenedor para la aplicación.
- La aplicación tiene acceso de lectura y escritura sin restricciones al directorio del contenedor y a todos sus subdirectorios y archivos dentro de él.
- La mayoría de las API de búsqueda de rutas de macOS están relacionadas con el contenedor de la aplicación. Por ejemplo, el contenedor tendrá su propia **biblioteca** (a la que se accede a través de `NSLibraryDirectory`), los subdirectorios de **compatibilidad de aplicaciones** y **preferencias** .
- macOS establece y aplica la conexión entre y la aplicación y su contenedor a través de la firma de código. Incluso si otra aplicación intenta suplantar la aplicación mediante su **identificador de lote**, no podrá tener acceso al contenedor debido a la firma de código.
- El contenedor no es para los archivos generados por el usuario. En su lugar, es para los archivos que utiliza la aplicación, como bases de datos, memorias caché u otros tipos de datos específicos.
- En _el caso de_ los tipos de las aplicaciones (por ejemplo, la aplicación fotográfica de Apple), el contenido del usuario pasará al contenedor.

> [!IMPORTANT]
> Desafortunadamente, Xamarin. Mac no tiene todavía una cobertura de API del 100% (a diferencia de Xamarin. iOS). como resultado, la API de `NSHomeDirectory` no se ha asignado en la versión actual de Xamarin. Mac.

Como solución temporal, puede usar el código siguiente:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>Directorio del contenedor del grupo de aplicaciones

A partir de Mac macOS 10.7.5 (y versiones posteriores), una aplicación puede usar el `com.apple.security.application-groups` derecho de acceso a un contenedor compartido que es común a todas las aplicaciones del grupo. Puede usar este contenedor compartido para el contenido que no está orientado al usuario, como la base de datos u otros tipos de archivos auxiliares (como las memorias caché).

Los contenedores de grupo se agregan automáticamente al contenedor de espacio aislado de cada aplicación (si forman parte de un grupo) y se almacenan en `~/Library/Group Containers/<application-group-id>`. El identificador de grupo _debe_ comenzar con el identificador del equipo de desarrollo y un punto, por ejemplo:

```plist
<team-id>.com.company.<group-name>
```

Para más información, consulte la referencia de la [clave](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)de Apple [Agregar una aplicación a un grupo de aplicaciones](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) .

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Powerbox y el acceso al sistema de archivos fuera del contenedor de la aplicación

Una aplicación de Xamarin. Mac en espacio aislado puede tener acceso a las ubicaciones del sistema de archivos fuera de su contenedor de las siguientes maneras:

- En la dirección específica del usuario (a través de los cuadros de diálogo abrir y guardar u otros métodos, como arrastrar y colocar).
- Mediante el uso de derechos para ubicaciones específicas del sistema de archivos (como `/bin` o `/usr/lib`).
- Cuando la ubicación del sistema de archivos se encuentra en determinados directorios que son de lectura mundial (como el uso compartido).

_Powerbox_ es la tecnología de seguridad de MacOS que interactúa con el usuario para ampliar los derechos de acceso a archivos de la aplicación de Xamarin. Mac en espacio aislado. Powerbox no tiene ninguna API, pero se activa de forma transparente cuando la aplicación llama a un `NSOpenPanel` o `NSSavePanel`. El acceso a Powerbox se habilita a través de los derechos que se establecen para la aplicación de Xamarin. Mac.

Cuando una aplicación en espacio aislado muestra un cuadro de diálogo abrir o guardar, la ventana aparece Powerbox (y no AppKit) y, por lo tanto, tiene acceso a cualquier archivo o directorio al que el usuario tenga acceso.

Cuando un usuario selecciona un archivo o un directorio desde el cuadro de diálogo abrir o guardar (o arrastra hasta el icono de la aplicación), POWERBOX agrega la ruta de acceso asociada al espacio aislado de la aplicación.

Además, el sistema permite automáticamente lo siguiente a una aplicación en espacio aislado:

- Conexión a un método de entrada del sistema.
- Llame a servicios seleccionados por el usuario desde el menú **servicios** (solo para los servicios marcados como _seguros para aplicaciones de espacio aislado_ por el proveedor de servicios).
- Abra los archivos elegido por el usuario en el menú **Abrir recientes** .
- Use copiar & pegar entre otras aplicaciones.
- Leer archivos de las siguientes ubicaciones de lectura universal:
  - `/bin`
  - `/sbin`
  - `/usr/bin`
  - `/usr/lib`
  - `/usr/sbin`
  - `/usr/share`
  - `/System`
- Leer y escribir archivos en los directorios creados por `NSTemporaryDirectory`.

De forma predeterminada, los archivos abiertos o guardados por una aplicación de Xamarin. Mac en espacio aislado permanecen accesibles hasta que finalice la aplicación (a menos que el archivo se haya abierto cuando se cierre la aplicación). Los archivos abiertos se restaurarán automáticamente en el espacio aislado de la aplicación a través de la característica de reanudación de macOS la próxima vez que se inicie la aplicación.

Para proporcionar persistencia a los archivos que se encuentran fuera de un contenedor de la aplicación de Xamarin. Mac, use marcadores de ámbito de seguridad (consulte a continuación).

#### <a name="related-items"></a>Elementos relacionados

El espacio aislado de la aplicación permite a una aplicación tener acceso a los elementos relacionados que tienen el mismo nombre de archivo, pero diferentes extensiones. La característica tiene dos partes: a) una lista de extensiones relacionadas en el archivo de `Info.plst` de la aplicación, b) código para indicar al espacio aislado lo que la aplicación realizará con estos archivos.

Hay dos escenarios en los que tiene sentido:

1. La aplicación debe poder guardar una versión diferente del archivo (con una nueva extensión). Por ejemplo, exportar un archivo de `.txt` a un archivo de `.pdf`. Para controlar esta situación, debe utilizar un `NSFileCoordinator` para tener acceso al archivo. Llame primero al método `WillMove(fromURL, toURL)`, mueva el archivo a la nueva extensión y, a continuación, llame a `ItemMoved(fromURL, toURL)`.
2. La aplicación debe abrir un archivo principal con una extensión y varios archivos auxiliares con extensiones diferentes. Por ejemplo, una película y un archivo de subtítulos. Use un `NSFilePresenter` para obtener acceso al archivo secundario. Proporcione el archivo principal a la propiedad `PrimaryPresentedItemURL` y el archivo secundario a la propiedad `PresentedItemURL`. Cuando se abra el archivo principal, llame al método `AddFilePresenter` de la clase `NSFileCoordinator` para registrar el archivo secundario.

En ambos escenarios, el archivo **info. plist** de la aplicación debe declarar los tipos de documento que puede abrir la aplicación. Para cualquier tipo de archivo, agregue el `NSIsRelatedItemType` (con un valor de `YES`) a su entrada en la matriz de `CFBundleDocumentTypes`.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir y guardar el comportamiento de un cuadro de diálogo con aplicaciones en espacio aislado

Los límites siguientes se colocan en el `NSOpenPanel` y `NSSavePanel` al llamarlos desde una aplicación de Xamarin. Mac en espacio aislado:

- No se puede invocar mediante programación el botón **Aceptar** .
- No se puede modificar mediante programación la selección de un usuario en un `NSOpenSavePanelDelegate`.

Además, se realizan las siguientes modificaciones de herencia:

- @No__t_2 **de  -  de aplicación no en espacio aislado** `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Marcadores de ámbito de seguridad y acceso a recursos persistentes

Como se indicó anteriormente, una aplicación de Xamarin. Mac en espacio aislado puede acceder a un archivo o a un recurso fuera de su contenedor mediante la interacción directa con el usuario (según lo proporcionado por PowerBox). Sin embargo, el acceso a estos recursos no se conserva automáticamente entre los inicios de la aplicación o los reinicios del sistema.

Mediante el uso de _marcadores de ámbito de seguridad_, una aplicación de Xamarin. Mac en espacio aislado puede conservar la intención del usuario y mantener el acceso a los recursos determinados después de reiniciar la aplicación.

#### <a name="security-scoped-bookmark-types"></a>Tipos de marcadores de ámbito de seguridad

Cuando se trabaja con marcadores de ámbito de seguridad y acceso persistente a recursos, hay dos casos de uso de SISTINE:

- **Un marcador de ámbito de aplicación proporciona acceso persistente a un archivo o carpeta especificados por el usuario.**

    Por ejemplo, si la aplicación de Xamarin. Mac en espacio aislado permite usar para abrir un documento externo para su edición (mediante un `NSOpenPanel`), la aplicación puede crear un marcador de ámbito de aplicación para que pueda tener acceso al mismo archivo en el futuro.
- **Un marcador de ámbito de documento proporciona un acceso persistente a un documento específico a un subarchivo.**

Por ejemplo, una aplicación de edición de vídeo que crea un archivo de proyecto que tiene acceso a imágenes individuales, clips de vídeo y archivos de sonido que se combinarán posteriormente en una sola película.

Cuando el usuario importa un archivo de recursos en el proyecto (a través de un `NSOpenPanel`), la aplicación crea un marcador de ámbito de documento para el elemento que se almacena en el proyecto, de modo que el archivo siempre es accesible para la aplicación.

Cualquier aplicación que pueda abrir los datos de marcador y el propio documento puede resolver un marcador de ámbito de documento. Esto permite la portabilidad, lo que permite al usuario enviar los archivos de proyecto a otro usuario y que todos los marcadores también funcionan para ellos.

> [!IMPORTANT]
> Un marcador de ámbito de documento _solo_ puede apuntar a un único archivo y no a una carpeta, y ese archivo no puede estar en una ubicación utilizada por el sistema (por ejemplo, `/private` o `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Usar marcadores con ámbito de seguridad

Para usar cualquier tipo de marcador de ámbito de seguridad, es necesario realizar los pasos siguientes:

1. **Establezca los derechos adecuados en la aplicación de Xamarin. Mac que necesita usar marcadores de ámbito de seguridad** : en el caso de los marcadores de ámbito de aplicación, establezca la clave de derechos de `com.apple.security.files.bookmarks.app-scope` en `true`. En el caso de los marcadores de ámbito de documento, establezca la clave de derechos de `com.apple.security.files.bookmarks.document-scope` en `true`.
2. **Crear un marcador de ámbito de seguridad** : lo hará para cualquier archivo o carpeta al que el usuario haya proporcionado acceso (a través de `NSOpenPanel` por ejemplo), que la aplicación necesitará acceso persistente. Use el método `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` de la clase `NSUrl` para crear el marcador.
3. **Resolver el marcador de ámbito de seguridad** : cuando la aplicación necesita acceder al recurso de nuevo (por ejemplo, después de reiniciar), deberá resolver el marcador en una dirección URL de ámbito de seguridad. Use el método `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` de la clase `NSUrl` para resolver el marcador.
4. **Notifique explícitamente al sistema que desea tener acceso al archivo desde la dirección URL de ámbito de seguridad** : este paso debe realizarse inmediatamente después de obtener la dirección URL de ámbito de seguridad anterior o, si posteriormente desea recuperar el acceso al recurso después de tener ha renunciado a su acceso a ella. Llame al método `StartAccessingSecurityScopedResource ()` de la clase `NSUrl` para iniciar el acceso a una dirección URL de ámbito de seguridad.
5. **Notifique explícitamente al sistema que ha terminado de tener acceso al archivo desde la dirección URL de ámbito de seguridad** : lo antes posible, debe informar al sistema cuando la aplicación ya no necesita tener acceso al archivo (por ejemplo, si el usuario lo cierra). Llame al método `StopAccessingSecurityScopedResource ()` de la clase `NSUrl` para dejar de obtener acceso a una dirección URL de ámbito de seguridad.

Después de ceder el acceso a un recurso, deberá volver al paso 4 de nuevo para volver a establecer el acceso. Si se reinicia la aplicación de Xamarin. Mac, debe volver al paso 3 y volver a resolver el marcador.

> [!IMPORTANT]
> Si no se libera el acceso a los recursos de la dirección URL del ámbito de seguridad, una aplicación de Xamarin. Mac perderá los recursos del kernel. Como resultado, la aplicación ya no podrá agregar ubicaciones del sistema de archivos a su contenedor hasta que se reinicie.

### <a name="the-app-sandbox-and-code-signing"></a>El espacio aislado de la aplicación y la firma de código

Después de habilitar el espacio aislado de la aplicación y de habilitar los requisitos específicos de la aplicación de Xamarin. Mac (a través de derechos), debe firmar el código del proyecto para que el espacio aislado surta efecto. Debe realizar la firma de código porque los derechos necesarios para el espacio aislado de la aplicación están vinculados a la firma de la aplicación.

macOS impone un vínculo entre el contenedor de una aplicación y su firma de código, de este modo, ninguna otra aplicación puede acceder a ese contenedor, incluso si está suplantando el identificador del lote de aplicaciones. Este mecanismo funciona de la siguiente manera:

1. Cuando el sistema crea el contenedor de la aplicación, establece una _lista de Access Control_ (ACL) en ese contenedor. La entrada de control de acceso inicial de la lista contiene el _requisito designado_ de la aplicación (Dr), que describe cómo se pueden reconocer las versiones futuras de la aplicación (cuando se ha actualizado).
2. Cada vez que se inicia una aplicación con el mismo identificador de paquete, el sistema comprueba que la firma de código de la aplicación coincide con los requisitos designados especificados en una de las entradas de la ACL del contenedor. Si el sistema no encuentra ninguna coincidencia, evita que se inicie la aplicación.

La firma de código funciona de las siguientes maneras:

1. Antes de crear el proyecto de Xamarin. Mac, obtenga un certificado de desarrollo, un certificado de distribución y un certificado de identificador de desarrollador del portal para desarrolladores de Apple.
2. Cuando Mac App Store distribuye la aplicación de Xamarin. Mac, se firma con una firma de código de Apple.

Al probar y depurar, utilizará una versión de la aplicación de Xamarin. Mac que firmó (que se usará para crear el contenedor de la aplicación). Más adelante, si desea probar o instalar la versión desde la App Store de Apple, se firmará con la firma de Apple y no se iniciará (ya que no tiene la misma firma de código que el contenedor de la aplicación original). En esta situación, obtendrá un informe de bloqueo similar al siguiente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para solucionarlo, deberá ajustar la entrada de la ACL para que apunte a la versión firmada de Apple de la aplicación.

Para obtener más información sobre la creación y descarga de los perfiles de aprovisionamiento necesarios para el espacio aislado, consulte la sección [firma y aprovisionamiento de la aplicación](#Signing_and_Provisioning_the_App) anterior.

#### <a name="adjusting-the-acl-entry"></a>Ajustar la entrada ACL

Para permitir la ejecución de la versión firmada de Apple de la aplicación de Xamarin. Mac, haga lo siguiente:

1. Abra la aplicación terminal (en `/Applications/Utilities`).
2. Abra una ventana de Finder en la versión firmada de Apple de la aplicación Xamarin. Mac.
3. Escriba `asctl container acl add -file` en la ventana de terminal.
4. Arrastre el icono de la aplicación de Xamarin. Mac desde la ventana del Finder y colóquelo en la ventana del terminal.
5. La ruta de acceso completa al archivo se agregará al comando en terminal.
6. Presione **entrar** para ejecutar el comando.

La ACL del contenedor contiene ahora los requisitos de código designados para ambas versiones de la aplicación de Xamarin. Mac y macOS ahora permitirá que se ejecute cualquiera de las versiones.

#### <a name="display-a-list-of-acl-code-requirements"></a>Mostrar una lista de requisitos de código ACL

Puede ver una lista de los requisitos de código en la ACL de un contenedor haciendo lo siguiente:

1. Abra la aplicación terminal (en `/Applications/Utilities`).
2. Escriba `asctl container acl list -bundle <container-name>`.
3. Presione **entrar** para ejecutar el comando.

El `<container-name>` suele ser el identificador de la agrupación de la aplicación de Xamarin. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Diseño de una aplicación de Xamarin. Mac para el espacio aislado de la aplicación

Hay un flujo de trabajo común que debe seguirse al diseñar una aplicación de Xamarin. Mac para el espacio aislado de la aplicación. Dicho esto, las características específicas de la implementación de espacio aislado en una aplicación van a ser exclusivas de la funcionalidad de la aplicación especificada.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis pasos para adoptar el espacio aislado de la aplicación

El diseño de una aplicación de Xamarin. Mac para el espacio aislado de la aplicación normalmente consta de los siguientes pasos:

1. Determine si la aplicación es adecuada para el espacio aislado.
2. Diseñar una estrategia de desarrollo y distribución.
3. Resuelva las incompatibilidades de la API.
4. Aplique los derechos de espacio aislado de la aplicación necesarios al proyecto de Xamarin. Mac.
5. Agregue separación de privilegios mediante XPC.
6. Implementar una estrategia de migración.

> [!IMPORTANT]
> No solo debe tener espacio aislado para el ejecutable principal en el grupo de aplicaciones, sino también para todas las aplicaciones auxiliares o herramientas incluidas en ese paquete. Esto es necesario para cualquier aplicación distribuida desde Mac App Store y, si es posible, debe realizarse para cualquier otra forma de distribución de aplicaciones.

Para obtener una lista de todos los archivos binarios ejecutables en un paquete de la aplicación de Xamarin. Mac, escriba el siguiente comando en terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Donde `[Your-App-Bundle]` es el nombre y la ruta de acceso al paquete de la aplicación.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar si una aplicación de Xamarin. Mac es adecuada para el espacio aislado

La mayoría de las aplicaciones de Xamarin. Mac son totalmente compatibles con el espacio aislado de la aplicación y, por lo tanto, son adecuadas para el espacio aislado. Si la aplicación requiere un comportamiento que no permite el espacio aislado de la aplicación, debe considerar un enfoque alternativo.

Si la aplicación requiere uno de los siguientes comportamientos, no es compatible con el espacio aislado de la aplicación:

- **Servicios de autorización** : con el espacio aislado de la aplicación, no puede trabajar con las funciones descritas en [referencia de C de servicios de autorización](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **API de accesibilidad** : no puede hospedar aplicaciones de asistencia como lectores de pantalla o aplicaciones que controlen otras aplicaciones.
- **Enviar eventos de Apple a aplicaciones arbitrarias** : Si la aplicación requiere el envío de eventos de Apple a una aplicación desconocida y arbitraria, no puede estar en un espacio aislado. En el caso de una lista conocida de aplicaciones llamadas, la aplicación todavía puede estar en un espacio aislado y los derechos deberán incluir la lista de aplicaciones llamada.
- **Enviar diccionarios de información de usuario en notificaciones distribuidas a otras tareas** : con el espacio aislado de la aplicación, no se puede incluir un diccionario de `userInfo` al publicar en un objeto `NSDistributedNotificationCenter` para la mensajería otras tareas.
- **Cargar extensiones de kernel** : la carga de extensiones de kernel está prohibida por el espacio aislado de la aplicación.
- **Simular la entrada del usuario en los cuadros de diálogo abrir y guardar** : la manipulación mediante programación de los cuadros de diálogo abrir o guardar para simular o alterar la entrada del usuario está prohibida por el espacio aislado de la aplicación.
- **Acceso o establecimiento de preferencias en otras aplicaciones** : la manipulación de la configuración de otras aplicaciones está prohibida por el espacio aislado de la aplicación.
- Configuración de **red** : la manipulación de la configuración de red está prohibida por el espacio aislado de la aplicación.
- **Finalizar otras aplicaciones** : el espacio aislado de la aplicación prohíbe el uso de `NSRunningApplication` para finalizar otras aplicaciones.

### <a name="resolving-api-incompatibilities"></a>Resolver incompatibilidades de API

Al diseñar una aplicación de Xamarin. Mac para el espacio aislado de la aplicación, pueden producirse incompatibilidades con el uso de algunas API de macOS.

Estos son algunos problemas comunes y cosas que puede hacer para solucionarlos:

- **Apertura, guardado y seguimiento de documentos** : Si está administrando documentos con cualquier tecnología que no sea `NSDocument`, debe cambiar a él debido a la compatibilidad integrada con el espacio aislado de la aplicación. `NSDocument` funciona automáticamente con PowerBox y proporciona compatibilidad para mantener documentos en el espacio aislado si el usuario los mueve en el buscador.
- **Conservar el acceso a los recursos del sistema de archivos** : Si la aplicación de Xamarin. Mac depende del acceso persistente a recursos fuera de su contenedor, use marcadores de ámbito de seguridad para mantener el acceso.
- **Crear un elemento de inicio de sesión para una aplicación** : con el espacio aislado de la aplicación, no se puede crear un elemento de inicio de sesión mediante `LSSharedFileList` ni se puede manipular el estado de los servicios de inicio mediante `LSRegisterURL`. Utilice la función `SMLoginItemSetEnabled` tal y como se describe en manzanas [agregando elementos de inicio de sesión mediante la documentación del marco de administración de servicios](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) .
- **Acceso** a los datos de usuario: Si usa funciones POSIX como `getpwuid` para obtener el directorio particular del usuario de los servicios de directorio, considere el uso de los símbolos de cacao o Core Foundation como `NSHomeDirectory`.
- **Acceder a las preferencias de otras aplicaciones** : como el espacio aislado de la aplicación dirige las API de búsqueda de rutas de acceso al contenedor de la aplicación, la modificación de las preferencias tiene lugar dentro de ese contenedor y el acceso a otras preferencias de aplicaciones no está permitido.
- **Uso de vídeo insertado en HTML5 en las vistas web** : Si la aplicación de Xamarin. Mac usa WebKit para reproducir vídeos HTML5 incrustados, también debe vincular la aplicación con el marco AV Foundation. De lo contrario, el espacio aislado de la aplicación impedirá que cocorreccióna juegue a estos vídeos.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicando los derechos de espacio aislado de la aplicación necesarios

Tendrá que editar los derechos de cualquier aplicación de Xamarin. Mac que quiera ejecutar en el espacio aislado de la aplicación y activar la casilla **habilitar espacio aislado de aplicaciones** .

En función de la funcionalidad de la aplicación, es posible que tenga que habilitar otros derechos para acceder a las características o los recursos del sistema operativo. El espacio aislado de aplicaciones funciona mejor cuando se minimizan los derechos que se solicitan al mínimo necesario para ejecutar la aplicación, por lo que solo se deben habilitar los derechos de forma aleatoria.

Para determinar qué derechos requiere una aplicación de Xamarin. Mac, haga lo siguiente:

1. Habilite el espacio aislado de la aplicación y ejecute la aplicación Xamarin. Mac.
2. Ejecute las características de la aplicación.
3. Abra la aplicación de consola (disponible en `/Applications/Utilities`) y busque infracciones de `sandboxd` en el registro **todos los mensajes** .
4. Para cada infracción de `sandboxd`, resuelva el problema mediante el contenedor de la aplicación en lugar de otras ubicaciones del sistema de archivos o aplique derechos de espacio aislado de la aplicación para habilitar el acceso a las características del sistema operativo restringido.
5. Vuelva a ejecutar y pruebe de nuevo todas las características de la aplicación de Xamarin. Mac.
6. Repetir hasta que se hayan resuelto todas las infracciones de `sandboxd`.

### <a name="add-privilege-separation-using-xpc"></a>Agregar separación de privilegios mediante XPC

Al desarrollar una aplicación de Xamarin. Mac para el espacio aislado de la aplicación, examine los comportamientos de la aplicación en los términos de privilegios y acceso y, a continuación, considere la posibilidad de separar las operaciones de alto riesgo en sus propios servicios de XPC.

Para obtener más información, vea la guía de programación de la [creación de XPC Services](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) y [daemons y servicios](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)de Apple.

### <a name="implement-a-migration-strategy"></a>Implementar una estrategia de migración

Si está publicando una nueva versión en espacio aislado de una aplicación de Xamarin. Mac que no estaba previamente en un espacio aislado, deberá asegurarse de que los usuarios actuales tengan una ruta de acceso de actualización fluida.

 Para obtener más información sobre cómo implementar un manifiesto de migración de contenedor, lea la documentación [migración de una aplicación a un espacio aislado](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) de Apple.

## <a name="summary"></a>Resumen

En este artículo se describe detalladamente el espacio aislado de una aplicación de Xamarin. Mac. En primer lugar, creamos una aplicación sencilla de Xamarin. Mac para mostrar los aspectos básicos del espacio aislado de la aplicación. A continuación, mostramos cómo resolver infracciones de espacio aislado. Después, se ha analizado en profundidad el espacio aislado de la aplicación y, por último, se ha examinado el diseño de una aplicación de Xamarin. Mac para el espacio aislado de la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Publicación en App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Acerca de la aplicación Sandbox](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comunes de espacio aislado de aplicaciones](https://developer.apple.com/library/content/qa/qa1773/_index.html)
