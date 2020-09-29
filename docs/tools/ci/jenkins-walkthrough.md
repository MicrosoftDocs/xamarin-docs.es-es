---
title: Uso de Jenkins con Xamarin
description: En este documento se describe cómo usar Jenkins para la integración continua con las aplicaciones de Xamarin. Describe cómo instalar, configurar y usar Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0ce1d4d0b74330b623b6d933e385222a71a38ec4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458165"
---
# <a name="using-jenkins-with-xamarin"></a>Uso de Jenkins con Xamarin

_En esta guía se muestra cómo configurar Jenkins como un servidor de integración continua y automatizar la compilación de aplicaciones móviles creadas con Xamarin. Describe cómo instalar Jenkins en OS X, configurarlo y configurar trabajos para compilar aplicaciones de Xamarin. iOS y Xamarin. Android cuando los cambios se confirman en el sistema de administración de código fuente._

La [Introducción a la integración continua con Xamarin](~/tools/ci/intro-to-ci.md) presenta la integración continua como una práctica de desarrollo de software útil que proporciona una advertencia temprana del código dañado o incompatible. CI permite a los desarrolladores solucionar problemas y problemas a medida que se producen y mantiene el software en un estado adecuado para la implementación. En este tutorial se explica cómo usar el contenido de ambos documentos juntos.

En esta guía se muestra cómo instalar Jenkins en un equipo dedicado que ejecuta OS X y configurarlo para que se ejecute automáticamente cuando se inicie el equipo. Una vez instalado Jenkins, se instalarán Complementos adicionales para admitir MS Build. Jenkins admite git de la caja. Si TFS se usa para el control de código fuente, también debe instalarse un complemento adicional y las utilidades de línea de comandos.

Una vez que Jenkins esté configurado y se hayan instalado los complementos necesarios, crearemos uno o varios trabajos para compilar los proyectos de Xamarin. Android y Xamarin. iOS. Un trabajo es una colección de pasos y metadatos necesarios para realizar algún trabajo. Normalmente, un trabajo consta de lo siguiente:

- **Administración de código fuente (SCM)** : se trata de una entrada de metadatos en los archivos de configuración de Jenkins que contiene información sobre cómo conectarse al control de código fuente y qué archivos se van a recuperar.
- **Desencadenadores** : los desencadenadores se usan para iniciar un trabajo en función de ciertas acciones, como cuando un programador confirma cambios en el repositorio de código fuente.
- **Instrucciones de compilación** : se trata de un complemento o un script que compilará el código fuente y generará un archivo binario que se puede instalar en dispositivos móviles.
- **Acciones de compilación opcionales** : esto podría incluir la ejecución de pruebas unitarias, la realización de análisis estáticos en el código, la firma de código o el inicio de otro trabajo para realizar otro trabajo relacionado con la compilación.
- **Notificaciones** : un trabajo puede enviar algún tipo de notificación sobre el estado de una compilación.
- **Seguridad** : aunque es opcional, se recomienda encarecidamente que las características de seguridad de Jenkins también estén habilitadas.

En esta guía se describe cómo configurar un servidor de Jenkins que abarque cada uno de estos puntos. Al final, deberíamos tener una buena comprensión de cómo configurar y configurar Jenkins para crear IPA y APK para nuestros proyectos móviles de Xamarin.

## <a name="requirements"></a>Requisitos

El servidor de compilación ideal es un equipo independiente que se dedica a la única finalidad de compilar y probar la aplicación. Un equipo dedicado garantiza que los artefactos que podrían ser necesarios para otros roles (como el de un servidor Web) no contaminan la compilación. Por ejemplo, si el servidor de compilación también actúa como servidor Web, el servidor web puede requerir una versión en conflicto de alguna biblioteca común. Debido a este conflicto, es posible que el servidor Web no funcione correctamente o que Jenkins pueda crear compilaciones que no funcionen cuando se implementen en los usuarios.

El servidor de compilación de las aplicaciones móviles de Xamarin se configura de forma muy parecida a la estación de trabajo de un desarrollador. Tiene una cuenta de usuario en la que se instalará Jenkins, Visual Studio para Mac y Xamarin. iOS y Xamarin. Android. También se deben instalar todos los certificados de firma de código, los perfiles de aprovisionamiento y los almacenes de claves. *Normalmente, la cuenta de usuario del servidor de compilación es independiente de las cuentas de desarrollador. Asegúrese de instalar y configurar todo el software, las claves y los certificados mientras se inicia sesión con la cuenta de usuario del servidor de compilación.*

En el siguiente diagrama se ilustran todos estos elementos en un servidor de compilación de Jenkins típico:

[![En este diagrama se ilustran todos estos elementos en un servidor de compilación de Jenkins típico](jenkins-walkthrough-images/image1.png)](jenkins-walkthrough-images/image1.png#lightbox)

las aplicaciones de iOS solo se pueden compilar y firmar en un equipo que ejecute macOS. Una Mini Mac es una opción de bajo costo razonable, pero cualquier equipo capaz de ejecutar OS X 10,10 (Yosemite) o superior es suficiente.

Si TFS se usa para el control de código fuente, querrá instalar [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere proporciona acceso multiplataforma a TFS en el terminal de macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalación de Jenkins

La primera tarea para usar Jenkins es instalarla. Hay tres formas de ejecutar Jenkins en OS X:

- Como demonio, que se ejecuta en segundo plano.
- Dentro de un contenedor de Servlets como Tomcat, Jetty o JBoss.
- Como un proceso normal que se ejecuta en una cuenta de usuario.

La mayoría de las aplicaciones de integración continua más tradicionales se ejecutan en segundo plano, ya sea como un demonio (en OS X o \* Nix) o como un servicio (en Windows). Esto es adecuado para escenarios en los que no se requiere ninguna interacción con la interfaz gráfica de usuario y donde se puede realizar fácilmente la instalación del entorno de compilación. Mobile Apps también requiere certificados de firma y keystores que podrían ser problemáticos para acceder cuando Jenkins se ejecuta como un demonio. Debido a estos problemas, este documento se centra en el tercer escenario: ejecutar Jenkins en una cuenta de usuario en el servidor de compilación.

Jenkins. app es una forma cómoda de instalar Jenkins. Se trata de un contenedor AppleScript que simplifica el inicio y la detención de un servidor Jenkins. En lugar de ejecutarse en un shell de Bash, Jenkins se ejecuta como una aplicación con el icono en el Dock, tal como se muestra en la siguiente captura de pantalla:

[![En lugar de ejecutarse en un shell de Bash, Jenkins se ejecuta como una aplicación con el icono en el Dock, tal como se muestra en esta captura de pantalla.](jenkins-walkthrough-images/image2.png)](jenkins-walkthrough-images/image2.png#lightbox)

Iniciar o detener Jenkins es tan sencillo como iniciar o detener Jenkins. app.

Para instalar Jenkins. app, descargue la versión más reciente de la página de descarga del proyecto, que se muestra en la siguiente captura de pantalla:

[![, Descargue la versión más reciente de la página de descarga de proyectos, que se ilustra en esta captura de pantalla](jenkins-walkthrough-images/image3.png)](jenkins-walkthrough-images/image3.png#lightbox)

Extraiga el archivo zip en la `/Applications` carpeta del servidor de compilación e inícielo como cualquier otra aplicación de OS X.
La primera vez que inicie Jenkins. app, aparecerá un cuadro de diálogo en el que se le informará de que se descargará Jenkins:

[![Aplicación, presentará un cuadro de diálogo que le informará de que se descargará Jenkins](jenkins-walkthrough-images/image4.png)](jenkins-walkthrough-images/image4.png#lightbox)

Una vez que Jenkins. app ha finalizado su descarga, mostrará otro cuadro de diálogo que le pregunta si desea personalizar el inicio de Jenkins, tal como se muestra en la siguiente captura de pantalla:

[![La aplicación ha finalizado su descarga, aparecerá otro cuadro de diálogo en el que se le pregunta si desea personalizar el inicio de Jenkins, tal como se muestra en esta captura de pantalla.](jenkins-walkthrough-images/image5.png)](jenkins-walkthrough-images/image5.png#lightbox)

La personalización de Jenkins es opcional y no tiene que realizarse cada vez que se inicia la aplicación: la configuración predeterminada de Jenkins funcionará en la mayoría de los casos.

Si es necesario personalizar Jenkins, haga clic en el botón **cambiar valores predeterminados** . Esto le presentará dos cuadros de diálogo consecutivos: uno que solicita parámetros de la línea de comandos de Java y otro que solicita parámetros de la línea de comandos de Jenkins. En las dos capturas de pantallas siguientes se muestran estos dos cuadros de diálogo:

[![En esta captura de pantalla se muestran los cuadros de diálogo](jenkins-walkthrough-images/image6.png)](jenkins-walkthrough-images/image6.png#lightbox)

[![En esta captura de pantalla se muestran los cuadros de diálogo](jenkins-walkthrough-images/image7.png)](jenkins-walkthrough-images/image7.png#lightbox)

Una vez que Jenkins se está ejecutando, es posible que desee establecerlo como un elemento de inicio de sesión para que se inicie cada vez que el usuario inicie sesión en el equipo. Para ello, haga clic con el botón derecho en el icono de Jenkins en el Dock y elija **Opciones... > abrir en Inicio de sesión**, como se muestra en la siguiente captura de pantalla:

[![Para ello, haga clic con el botón derecho en el icono de Jenkins en el Dock y elija OptionsOpen en Inicio de sesión, tal como se muestra en esta captura de pantalla.](jenkins-walkthrough-images/image8.png)](jenkins-walkthrough-images/image8.png#lightbox)

Esto hará que Jenkins. app se inicie automáticamente cada vez que el usuario inicie sesión, pero no cuando se inicie el equipo. Se puede especificar una cuenta de usuario que OS X usará para iniciar sesión automáticamente en el momento del arranque. Abra las **preferencias del sistema**y seleccione el icono **usuarios & grupos** como se muestra en esta captura de pantalla:

[![Abra las preferencias del sistema y seleccione el icono grupos de usuarios, tal como se muestra en esta captura de pantalla.](jenkins-walkthrough-images/image9.png)](jenkins-walkthrough-images/image9.png#lightbox)

Haga clic en el botón **Opciones de inicio de sesión** y, a continuación, elija la cuenta que os X usará para el inicio de sesión en el momento del arranque.

En este punto, se ha instalado Jenkins. Sin embargo, si queremos crear aplicaciones móviles de Xamarin, tendremos que instalar algunos complementos.

### <a name="installing-plugins"></a>Instalar complementos

Cuando el instalador Jenkins. app se haya completado, iniciará Jenkins e iniciará el explorador Web con la dirección URL http://localhost:8080 , como se muestra en la siguiente captura de pantalla:

[![8080, como se muestra en esta captura de pantalla](jenkins-walkthrough-images/image10.png)](jenkins-walkthrough-images/image10.png#lightbox)

En esta página, seleccione **jenkins > Manage jenkins > Manage plugins (Administrar complementos** ) en el menú de la esquina superior izquierda, tal como se muestra en la siguiente captura de pantalla:

[![En esta página, seleccione Jenkins Administrar complementos de Jenkins en el menú de la esquina superior izquierda.](jenkins-walkthrough-images/image11.png)](jenkins-walkthrough-images/image11.png#lightbox)

Se mostrará la página del **Administrador de complementos de Jenkins** . Si hace clic en la pestaña disponible, verá una lista de más de 600 complementos que se pueden descargar e instalar. Esto se muestra en la siguiente captura de pantalla:

[![Si hace clic en la pestaña disponible, verá una lista de más de 600 complementos que se pueden descargar e instalar.](jenkins-walkthrough-images/image12.png)](jenkins-walkthrough-images/image12.png#lightbox)

Desplazarse por todos los complementos de 600 para encontrar algunos puede ser tedioso y propenso a errores. Jenkins proporciona un campo de búsqueda de filtro en la esquina superior derecha de la interfaz. El uso de este campo de filtro para buscar simplificará la ubicación e instalación de uno o todos los complementos siguientes:

- **Complemento de MSBuild para Jenkins** : este complemento permite compilar soluciones de Visual Studio y Visual Studio para Mac (. sln) y proyectos (. csproj).
- **Complemento de inyector de entorno** : se trata de un complemento opcional pero útil que permite establecer variables de entorno en el nivel de trabajo y de compilación. También ofrece protección adicional para variables como las contraseñas que se usan para firmar el código de la aplicación. A veces se abrevia como el  *complemento EnvInject* .
- **Complemento de Team Foundation Server** : se trata de un complemento opcional que solo es necesario si usa Team Foundation Server o Team Foundation Services para el control de código fuente.

Jenkins admite git sin complementos adicionales.

Después de instalar todos los complementos, querrá reiniciar Jenkins y configurar las opciones globales de cada complemento. Para encontrar la configuración global de un complemento, seleccione **jenkins > administrar jenkins > configurar el sistema** en la esquina superior izquierda, tal como se muestra en la siguiente captura de pantalla:

[![Para encontrar la configuración global de un complemento, seleccione Jenkins/Manage Jenkins/configure System en la esquina superior izquierda.](jenkins-walkthrough-images/image13.png)](jenkins-walkthrough-images/image13.png#lightbox)

Al seleccionar esta opción de menú, se le dirigirá a la página **configurar el sistema [Jenkins]** . Esta página contiene secciones para configurar Jenkins y establecer algunos de los valores globales del complemento.  En la captura de pantalla siguiente se muestra un ejemplo de esta página:

[![En esta captura de pantalla se muestra un ejemplo de esta página.](jenkins-walkthrough-images/image14.png)](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurar el complemento de MSBuild

El complemento MSBuild debe estar configurado para usar **/Library/Frameworks/mono.Framework/Commands/xbuild** con el fin de compilar Visual Studio para Mac solución y archivos de proyecto. Desplácese hacia abajo en la página **configurar sistema [Jenkins]** hasta que aparezca el botón **Agregar MSBuild** , tal como se muestra en la siguiente captura de pantalla:

 [![Desplácese hacia abajo en la página configurar Jenkins del sistema hasta que aparezca el botón Agregar MSBuild.](jenkins-walkthrough-images/image15.png)](jenkins-walkthrough-images/image15.png#lightbox)

Haga clic en este botón y rellene el **nombre** y la **ruta de acceso** a los campos de **msbuild** en el formulario que aparece. El nombre de la instalación de **msbuild** debe ser algo significativo, mientras que la **ruta de acceso a MSBuild** debe ser la ruta de acceso a `xbuild` , que suele ser **/Library/Frameworks/mono.Framework/Commands/xbuild**. Después de guardar los cambios haciendo clic en el botón Guardar o aplicar en la parte inferior de la página, Jenkins podrá usar `xbuild` para compilar las soluciones.

#### <a name="configuring-the-tfs-plugin"></a>Configuración del complemento de TFS

Esta sección es obligatoria si tiene previsto usar TFS para el control de código fuente.

Para que una estación de trabajo de macOS interactúe con un servidor de TFS, [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) se debe instalar en la estación de trabajo. Team Explorer Everywhere es un conjunto de herramientas de Microsoft que incluye un cliente de línea de comandos multiplataforma para tener acceso a TFS. Team Explorer Everywhere puede descargarse de Microsoft e instalarse en tres pasos:

1. Descomprima el archivo de almacenamiento en un directorio que sea accesible para la cuenta de usuario. Por ejemplo, puede descomprimir el archivo en **~/Tee**.
2. Configure el shell o la ruta de acceso del sistema para incluir la carpeta que contiene los archivos que se descomprimen en el paso uno anterior. Por ejemplo,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Para confirmar que Team Explorer Everywhere está instalado, abra una sesión de terminal y ejecute el `tf` comando. Si TF está configurado correctamente, verá el siguiente resultado en la sesión de terminal:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Una vez instalado el cliente de línea de comandos para TFS, Jenkins debe configurarse con la ruta de acceso completa al cliente de la `tf` línea de comandos. Desplácese hacia abajo en la página **configurar sistema [Jenkins]** hasta que encuentre la sección Team Foundation Server, como se muestra en la siguiente captura de pantalla:

[![Desplácese hacia abajo en la página configurar Jenkins del sistema hasta que encuentre la sección Team Foundation Server](jenkins-walkthrough-images/image17.png)](jenkins-walkthrough-images/image17.png#lightbox)

Escriba la ruta de acceso completa al `tf` comando y haga clic en el botón **Guardar** .

### <a name="configure-jenkins-security"></a>Configuración de la seguridad de Jenkins

Cuando se instala por primera vez, Jenkins tiene la seguridad deshabilitada, por lo que es posible que cualquier usuario configure y ejecute cualquier tipo de trabajo de forma anónima. En esta sección se explica cómo configurar la seguridad mediante la base de datos de usuario de Jenkins para configurar la autenticación y la autorización.

Para encontrar la configuración de seguridad, seleccione **jenkins > administrar jenkins > configurar la seguridad global**, como se muestra en esta captura de pantalla:

[![Para encontrar la configuración de seguridad, seleccione Jenkins/Manage Jenkins/configure Global Security.](jenkins-walkthrough-images/image18.png)](jenkins-walkthrough-images/image18.png#lightbox)

En la página **Configurar seguridad global** , active la casilla **Habilitar seguridad** y aparecerá el formulario **Access Control** , similar a la siguiente captura de pantalla:

[![En la página Configurar seguridad global, active la casilla habilitar seguridad y aparecerá el formulario Access Control, similar a esta captura de pantalla.](jenkins-walkthrough-images/image19.png)](jenkins-walkthrough-images/image19.png#lightbox)

Alterne el botón de radio para la **base de datos de usuario de Jenkins** en la **sección dominio de seguridad**y asegúrese de que la **opción permitir a los usuarios registrarse** también esté activada, como se muestra en la siguiente captura de pantalla:

[![Alterne el botón de radio para la base de datos de usuario de Jenkins en la sección dominio Kerberos y asegúrese de que la opción permitir a los usuarios registrarse también esté activada](jenkins-walkthrough-images/image20.png)](jenkins-walkthrough-images/image20.png#lightbox)

Por último, reinicie Jenkins y cree una cuenta nueva. La primera cuenta que se crea es la cuenta raíz y esta cuenta se promoverá automáticamente a un administrador. Vuelva a la página **Configurar seguridad global** y desactive el botón de radio **seguridad basada en matrices** . Se debe conceder acceso total a la cuenta raíz y la cuenta anónima debe tener acceso de solo lectura, como se muestra en la siguiente captura de pantalla:

[![Se debe conceder acceso total a la cuenta raíz y se debe conceder acceso de solo lectura a la cuenta anónima.](jenkins-walkthrough-images/image21.png)](jenkins-walkthrough-images/image21.png#lightbox)

Una vez que se guarda esta configuración y se reinicia Jenkins, se activará la seguridad.

#### <a name="disabling-security"></a>Deshabilitar la seguridad

En el caso de una contraseña olvidada o un bloqueo en todo Jenkins, es posible deshabilitar la seguridad siguiendo estos pasos:

1. Detenga Jenkins. Si usa Jenkins. app, puede hacerlo haciendo clic con el botón derecho en el icono de Jenkins. app en el Dock y seleccionando salir en el menú que aparece:

    ![Icono de la aplicación en el Dock y seleccionando salir en el menú que aparece](jenkins-walkthrough-images/image19.png)

2. Abra el archivo **~/.jenkins/config.xml** en un editor de texto.
3. Cambie el valor del `<usesecurity></usesecurity>` elemento de `true` a `false` .
4. Elimine los `<authorizationstrategy></authorizationstrategy>` elementos y del `<securityrealm></securityrealm>` archivo.
5. Reinicie Jenkins.

## <a name="setting-up-a-job"></a>Configuración de un trabajo

En el nivel superior, Jenkins organiza todas las diversas tareas necesarias para crear software en un *trabajo*. Un trabajo también tiene metadatos asociados, lo que proporciona información sobre la compilación, como la obtención del código fuente, la frecuencia con la que debe ejecutarse la compilación, las variables especiales necesarias para la generación y cómo notificar a los desarrolladores si se produce un error en la compilación.

Los trabajos se crean seleccionando **Jenkins > nuevo trabajo** en el menú de la esquina superior derecha, tal como se muestra en la siguiente captura de pantalla:

![Los trabajos se crean seleccionando el nuevo trabajo de Jenkins en el menú de la esquina superior derecha.](jenkins-walkthrough-images/image22.png)

Se mostrará la página **nuevo trabajo [Jenkins]** . Escriba un nombre para el trabajo y seleccione el botón de radio **compilar un proyecto de software de estilo libre** . En la captura de pantalla siguiente se muestra un ejemplo de esto:

![Escriba un nombre para el trabajo y seleccione el botón de radio compilar un proyecto de software de estilo libre](jenkins-walkthrough-images/image23.png)

Al hacer clic en el botón **Aceptar** , se muestra la página de configuración del trabajo. Debe ser similar a la siguiente captura de pantalla:

![Debería ser similar a esta captura de pantalla](jenkins-walkthrough-images/image24.png)

Jenkins organiza los trabajos en un directorio en el disco duro que se encuentra en la siguiente ruta de acceso: **~/.Jenkins/Jobs/[Job Name]**

Esta carpeta contiene todos los archivos y artefactos específicos del trabajo, como registros, archivos de configuración y el código fuente que debe compilarse.

Una vez creado el trabajo inicial, debe configurarse con uno o varios de los siguientes elementos:

- Se debe especificar el sistema de administración de código fuente.
- Se deben agregar al proyecto una o varias *acciones de compilación* . Estos son los pasos o las tareas necesarias para compilar la aplicación.
- Al trabajo se le debe asignar un *desencadenador de compilación* : un conjunto de instrucciones que informan a Jenkins con qué frecuencia recuperar el código y compilar el proyecto final.

### <a name="configuring-source-code-control"></a>Configurar el control de código fuente

La primera tarea de Jenkins es recuperar el código fuente del sistema de administración de código fuente. Jenkins admite muchos de los sistemas de administración de código fuente más conocidos disponibles hoy en día. En esta sección se tratan dos sistemas populares, GIT y Team Foundation Server. Cada uno de estos sistemas de administración de código fuente se describe con más detalle en las secciones siguientes.

#### <a name="using-git-for-source-code-control"></a>Usar git para el control de código fuente

Si usa TFS para el control de código fuente, [omita](#using-tfs-for-source-code-management) esta sección y continúe con la siguiente sección con TFS.

Jenkins admite git de la caja: no se necesitan Complementos adicionales. Para usar GIT, haga clic en el botón de radio **git** y escriba la dirección URL del repositorio de Git, tal como se muestra en la siguiente captura de pantalla:

![Para usar GIT, haga clic en el botón de radio git y escriba la dirección URL del repositorio de Git.](jenkins-walkthrough-images/image25.png)

Una vez guardados los cambios, se completa la configuración de Git.

#### <a name="using-tfs-for-source-code-management"></a>Usar TFS para la administración de código fuente

Esta sección solo se aplica a los usuarios de TFS.

Haga clic en el botón de opción **Team Foundation Server** y debe aparecer la sección configuración de TFS, similar a lo que se muestra en la siguiente captura de pantalla:

![Haga clic en el botón de opción Team Foundation Server y debe aparecer la sección configuración de TFS.](jenkins-walkthrough-images/image26.png)

Proporcione la información necesaria para TFS. En la captura de pantalla siguiente se muestra un ejemplo del formulario completado:

![Esta captura de pantalla muestra un ejemplo del formulario completado](jenkins-walkthrough-images/image27.png)

#### <a name="testing-the-source-code-control-configuration"></a>Probar la configuración de control de código fuente

Una vez configurado el control de código fuente adecuado, haga clic en **Guardar** para guardar los cambios. Esto le devolverá a la Página principal del trabajo, que se parecerá a la siguiente captura de pantalla:

![Esto le devolverá a la Página principal del trabajo, que se parecerá a esta captura de pantalla](jenkins-walkthrough-images/image28.png)

La manera más sencilla de validar que el control de código fuente está configurado correctamente es desencadenar una compilación manualmente, aunque no se especifique ninguna acción de compilación. Para iniciar una compilación manualmente, la Página principal del trabajo tiene un vínculo **generar ahora** en el menú de la izquierda, tal como se muestra en la siguiente captura de pantalla:

![Para iniciar una compilación manualmente, la Página principal del trabajo tiene un vínculo generar ahora en el menú del lado izquierdo.](jenkins-walkthrough-images/image29.png)

Cuando se ha iniciado una compilación, el cuadro de diálogo historial de compilaciones muestra un círculo azul parpadeante, una barra de progreso, el número de compilación y la hora en que se inició la compilación, similar a la siguiente captura de pantalla:

![Cuando se ha iniciado una compilación, el cuadro de diálogo historial de compilaciones muestra un círculo azul parpadeante, una barra de progreso, el número de compilación y la hora en que se inició la compilación.](jenkins-walkthrough-images/image30.png)

Si el trabajo se realiza correctamente, se mostrará un círculo azul. Si se produce un error en el trabajo, se mostrará un círculo rojo.

Para ayudar a solucionar problemas que pueden surgir como parte de la compilación, Jenkins capturará todos los resultados de la consola del trabajo. Para ver la salida de la consola, haga clic en el trabajo en el **historial de compilación**y, a continuación, en el vínculo salida de la **consola** en el menú de la izquierda. En la captura de pantalla siguiente se muestra el vínculo de salida de la **consola** , así como algunos de los resultados de un trabajo correcto:

![En esta captura de pantalla se muestra el vínculo de salida de la consola, así como algunos de los resultados de un trabajo correcto.](jenkins-walkthrough-images/image31.png)

#### <a name="location-of-build-artifacts"></a>Ubicación de los artefactos de compilación

Jenkins recuperará el código fuente completo en una carpeta especial denominada *área de trabajo*. Este directorio puede encontrarse dentro de la carpeta en la siguiente ubicación:

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

La ruta de acceso al área de trabajo se almacenará en una variable de entorno denominada `$WORKSPACE` .

Es posible examinar la carpeta del área de trabajo en Jenkins; para ello, vaya a la página de aterrizaje de un trabajo y, a continuación, haga clic en el vínculo del **área de trabajo** en el menú de la izquierda. En la captura de pantalla siguiente se muestra un ejemplo del área de trabajo para un trabajo denominado **HelloWorld**:

![Esta captura de pantalla muestra un ejemplo del área de trabajo para un trabajo denominado HelloWorld](jenkins-walkthrough-images/image32.png)

### <a name="build-triggers"></a>Desencadenadores de compilación

Hay varias estrategias diferentes para iniciar las compilaciones en Jenkins: se denominan *desencadenadores de compilación*. Un desencadenador de compilación ayuda a Jenkins a decidir cuándo iniciar un trabajo y compilar el proyecto. Dos de los desencadenadores de compilación más comunes son:

- **Compilar periódicamente** : este desencadenador hace que Jenkins inicie un trabajo a intervalos especificados, como cada dos horas o a medianoche de los días laborables. La compilación se iniciará independientemente de si se han producido cambios en el repositorio de código fuente.
- **Sondeo SCM** : este desencadenador sondeará el control de código fuente de forma periódica. Si se ha confirmado algún cambio en el repositorio de código fuente, Jenkins iniciará una nueva compilación.

Sondeo SCM es un desencadenador conocido porque proporciona comentarios rápidos cuando un desarrollador confirma cambios que hacen que la compilación se interrumpa. Esto resulta útil para avisar a los equipos de que algún código confirmado recientemente está causando problemas y permite que los desarrolladores solucionen el problema mientras los cambios se siguen actualizando en mente.

Las compilaciones periódicas se suelen usar para crear una versión de la aplicación que se puede distribuir a los evaluadores. Por ejemplo, una compilación periódica podría estar programada el viernes por la noche para que los miembros del equipo de QA puedan probar el trabajo de la semana anterior.

### <a name="compiling-a-xamarinios-applications"></a>Compilación de aplicaciones de Xamarin. iOS
Los proyectos de Xamarin. iOS se pueden compilar en la línea de comandos mediante `xbuild` o `msbuild` . El comando de Shell se ejecutará en el contexto de la cuenta de usuario que ejecuta Jenkins. Es importante que la cuenta de usuario tenga acceso al perfil de aprovisionamiento para que la aplicación se pueda empaquetar correctamente para su distribución. Es posible agregar este comando de Shell a la página de configuración del trabajo.

Desplácese hacia abajo hasta la sección **compilación** . Haga clic en el botón **Agregar paso de compilación** y seleccione **Ejecutar Shell**, tal como se muestra en la siguiente captura de pantalla:

![Haga clic en el botón Agregar paso de compilación y seleccione Ejecutar Shell.](jenkins-walkthrough-images/image33.png)

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Compilar un proyecto de Xamarin. Android

La compilación de un proyecto de Xamarin. Android es muy similar en concepto a la compilación de un proyecto de Xamarin. iOS. Para crear un APK a partir de un proyecto de Xamarin. Android, Jenkins se debe configurar para realizar los dos pasos siguientes:

- Compilar el proyecto mediante el complemento de MSBuild
- Sign y zip Alinee el APK con un almacén de claves de versión válido.

Estos dos pasos se tratarán con más detalle en las dos secciones siguientes.

### <a name="creating-the-apk"></a>Crear APK

Haga clic en el botón **Agregar paso de compilación** y seleccione **compilar un proyecto o solución de Visual Studio con MSBuild**, tal como se muestra en la siguiente captura de pantalla:

![Al crear el APK, haga clic en el botón Agregar paso de compilación y seleccione compilar un proyecto o solución de Visual Studio con MSBuild.](jenkins-walkthrough-images/image36.png)

Una vez agregado el paso de compilación al proyecto, rellene los campos de formulario que aparecen. La captura de pantalla siguiente es un ejemplo del formulario completado:

![Una vez agregado el paso de compilación al proyecto, rellene los campos de formulario que aparecen.](jenkins-walkthrough-images/image37.png)

Este paso de compilación se ejecutará `xbuild` en la carpeta **$Workspace** . El archivo de compilación de MSBuild se establece en el archivo **Xamarin. Android. csproj** . Los **argumentos** de la línea de comandos especifican una versión de lanzamiento del **PackageForAndroid**de destino. El producto de este paso será un APK que se encuentra en la siguiente ubicación:

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

En la captura de pantalla siguiente se muestra un ejemplo de este APK:

![En esta captura de pantalla se muestra un ejemplo de este APK](jenkins-walkthrough-images/image38.png)

Esta APK no está lista para la implementación, ya que no se ha firmado con un almacén de claves privado y debe estar alineada en zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firmar y zipalign APK para la versión

La firma y la zipalign de APK son técnicamente dos tareas independientes que realizan dos herramientas de línea de comandos independientes de la Android SDK. Sin embargo, es conveniente realizarlos en una acción de compilación. Para obtener más información sobre cómo firmar y zipalign un APK, consulte la documentación de Xamarin sobre la preparación de una aplicación Android para su lanzamiento.

Ambos comandos requieren parámetros de línea de comandos que pueden variar de un proyecto a un proyecto. Además, algunos de estos parámetros de la línea de comandos son contraseñas que no deben aparecer en la salida de la consola cuando se ejecuta la compilación. Almacenaremos algunos de estos parámetros de línea de comandos en variables de entorno. Las variables de entorno necesarias para la firma y/o la alineación de zip se describen en la tabla siguiente:

|Variable de entorno|Descripción|
|--- |--- |
|KEYSTORE_FILE|Esta es la ruta de acceso al almacén de claves para firmar el APK|
|KEYSTORE_ALIAS|La clave en el almacén de claves que se utilizará para firmar el APK.|
|INPUT_APK|APK que crea `xbuild` .|
|SIGNED_APK|La APK firmada generada por `jarsigner` .|
|FINAL_APK|Este es el APK con alineación zip que produce `zipalign` .|
|STORE_PASS|Esta es la contraseña que se usa para tener acceso al contenido del almacén de claves para el archivo.|

Tal y como se describe en la sección de requisitos, se pueden establecer estas variables de entorno durante la compilación mediante el complemento EnvInject. El trabajo debe tener un nuevo paso de compilación agregado en función de las variables de entorno de inserción, como se muestra en la siguiente captura de pantalla:

![El trabajo debe tener un nuevo paso de compilación agregado en función de las variables de entorno de inserción](jenkins-walkthrough-images/image39.png)

En el campo de formulario de **contenido de propiedades** que aparecerá, se agregan las variables de entorno, una por línea, en el formato siguiente:

```
ENVIRONMENT_VARIABLE_NAME = value
```

En la captura de pantalla siguiente se muestran las variables de entorno necesarias para firmar APK:

![En esta captura de pantalla se muestran las variables de entorno necesarias para firmar el APK](jenkins-walkthrough-images/image40.png)

Tenga en cuenta que algunas de las variables de entorno para los archivos APK se basan en la `WORKSPACE` variable de entorno.

La variable de entorno final es la contraseña para tener acceso al contenido del almacén de claves: `STORE_PASS` . Las contraseñas son valores confidenciales que se deben ocultar o omitir en los archivos de registro. El complemento EnvInject puede configurarse para proteger estos valores, de modo que no se muestren en los registros.

Inmediatamente antes de que la sección de **compilación** de la configuración del trabajo sea una sección de **entorno de compilación** . Cuando se activa la casilla **Insertar contraseñas** , algunos campos de formulario aparecen. Estos campos de formulario se utilizan para capturar el nombre y el valor de la variable de entorno. La captura de pantalla siguiente es un ejemplo de cómo agregar la `STORE_PASS` variable de entorno:

![Esta captura de pantalla es un ejemplo de cómo agregar la variable de entorno STOREPASS](jenkins-walkthrough-images/image41.png)

Una vez que se han inicializado las variables de entorno, el paso siguiente consiste en agregar un paso de compilación para firmar y comprimir el código APK. Inmediatamente después del paso de compilación para insertar las variables de entorno, se creará otra compilación del comando **Execute Shell** que ejecutará `jarsigner` y `zipalign` . Cada comando ocupará una línea, tal como se muestra en el siguiente fragmento de código:

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

En la captura de pantalla siguiente se muestra un ejemplo de cómo escribir los `jarsigner` `zipalign` comandos y en el paso:

![En esta captura de pantalla se muestra un ejemplo de cómo especificar los comandos jarsigner y zipalign en el paso.](jenkins-walkthrough-images/image42.png)

Una vez que todas las acciones de compilación estén en su lugar, es recomendable desencadenar una compilación manual para comprobar que todo funciona. Si se produce un error en la compilación, se debe revisar la salida de la **consola** para obtener información sobre la causa de que se produzca un error en la compilación.

### <a name="submitting-tests-to-test-cloud"></a>Enviar pruebas a Test Cloud

Las pruebas automatizadas se pueden enviar a Test Cloud mediante comandos de Shell. Para obtener más información sobre cómo configurar una serie de pruebas en Xamarin Test Cloud, consulte preparación de las [aplicaciones de Xamarin. Android](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) y preparación de las [aplicaciones de Xamarin. iOS](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

## <a name="summary"></a>Resumen

En esta guía hemos introducido Jenkins como un servidor de compilación en macOS y lo hemos configurado para compilar y preparar las aplicaciones móviles de Xamarin para su lanzamiento. Se ha instalado Jenkins en un equipo macOS junto con varios complementos para admitir el proceso de compilación. Hemos creado y configurado un trabajo que extraerá el código de TFS o git y, a continuación, compilará el código en una aplicación de versión preparada. También hemos explorado dos maneras diferentes de programar Cuándo se deben ejecutar los trabajos.

## <a name="related-links"></a>Vínculos relacionados

- [Integración continua](~/tools/ci/index.md)
- [App Center Test](/appcenter/test-cloud/)