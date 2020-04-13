---
title: Uso de Team City con Xamarin
description: En esta guía se analizarán los pasos relacionados con el uso de TeamCity para compilar aplicaciones móviles y, a continuación, enviarlas a App Center Test.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 6ecd453180e8c392ba7d7527778617eb40950a9e
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587460"
---
# <a name="using-team-city-with-xamarin"></a>Uso de Team City con Xamarin

_En esta guía se analizarán los pasos relacionados con el uso de TeamCity para compilar aplicaciones móviles y, a continuación, enviarlas a App Center Test._

Como se describe en la guía Introducción a la [integración continua,](~/tools/ci/intro-to-ci.md) la integración continua (CI) es una práctica útil al desarrollar aplicaciones móviles de calidad. Hay muchas opciones viables para el software de servidor de integración continua; esta guía se centrará en [TeamCity](https://www.jetbrains.com/teamcity/) de JetBrains.

Hay varias permutaciones diferentes de una instalación de TeamCity. La lista siguiente describe algunas de estas permutaciones:

- **Servicio de Windows:** en este escenario, TeamCity se inicia cuando Windows se inicia como un servicio de Windows. Debe estar emparejado con un host de compilación de Mac para compilar cualquier aplicación de iOS.

- **Iniciar Daemon en OS X** – Conceptualmente, esto es similar a ejecutar como un servicio de Windows descrito en el paso anterior. De forma predeterminada, las compilaciones se ejecutarán en la cuenta raíz.

- Cuenta de **usuario en OS X:** es posible ejecutar TeamCity en una cuenta de usuario que se inicia cada vez que el usuario inicia sesión.

De los escenarios anteriores, ejecutar TeamCity en una cuenta de usuario en OS X es el más sencillo y fácil de configurar.

Hay varios pasos relacionados con la configuración de TeamCity:

- **Instalación de TeamCity:** la instalación de TeamCity no se describe en esta guía. En esta guía se supone que TeamCity está instalado y ejecutándose con una cuenta de usuario. Las instrucciones sobre la instalación de [TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) se pueden encontrar en la documentación de [TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) de JetBrains.

- **Preparación del** servidor de compilación: este paso implica la instalación del software, las herramientas y los certificados necesarios para crear aplicaciones móviles y prepararlos para su distribución.

- Creación de un script de **compilación:** este paso no es estrictamente necesario, pero un script de compilación es una ayuda útil para crear aplicaciones desatendidas. El uso de un script de compilación le ayudará a solucionar problemas de compilación que puedan surgir y proporciona una forma coherente y repetible de crear los archivos binarios para la distribución, incluso si no se practica la integración continua.

- Creación de un proyecto de **TeamCity:** una vez completados los tres pasos anteriores, debemos crear un proyecto de TeamCity que contendrá todos los metadatos necesarios para recuperar el código fuente, compilar los proyectos y enviar las pruebas a la prueba de App Center.

## <a name="requirements"></a>Requisitos

Se requiere experiencia con [App Center Test.](https://docs.microsoft.com/appcenter/test-cloud/)

Se requiere familiaridad con TeamCity 8.1. La instalación de TeamCity está fuera del alcance de este documento. Se supone que TeamCity está instalado en OS X Mavericks y se ejecuta con una cuenta de usuario normal y no con la cuenta raíz.

El servidor de compilación debe ser un equipo independiente, que ejecute OS X, dedicado a la integración continua. Idealmente, el servidor de compilación no será responsable de ningún otro rol, como un servidor de base de datos, un servidor web o una estación de trabajo para desarrolladores.

> [!IMPORTANT]
> Esta guía no cubre una instalación "sin cabeza" de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparación del servidor de compilación

Un paso crucial en la configuración de un servidor de compilación es instalar todas las herramientas, software y certificados necesarios para crear las aplicaciones móviles. Es importante que el servidor de compilación pueda compilar la solución móvil y ejecutar las pruebas. Para minimizar los problemas de configuración, el software y las herramientas deben instalarse en la misma cuenta de usuario que hospeda TeamCity. La siguiente lista detalla lo que se requiere:

1. **Visual Studio para Mac:** incluye Xamarin.iOS y Xamarin.Android.
2. **Iniciar sesión en el almacén** de componentes de Xamarin: este paso es opcional y solo es necesario si la aplicación usa componentes desde el almacén de componentes de Xamarin. El inicio de sesión de forma proactiva en el almacén de componentes en este momento evitará cualquier problema cuando una compilación de TeamCity intente compilar la aplicación.
3. **Xcode:** Xcode es necesario para compilar y firmar aplicaciones iOS.
4. **Herramientas de línea de comandos de Xcode:** se describe en el paso 1 de la sección Instalación de la guía Actualización de [Ruby con rbenv.](https://github.com/calabash/calabash-ios/wiki)
5. **Firma de perfiles** de aprovisionamiento de & de identidad: importe los certificados y el perfil de aprovisionamiento a través de XCode. Consulte la guía de Apple sobre [la exportación de identidades](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) de firma y perfiles de aprovisionamiento para obtener más detalles.
6. **Almacenes de claves Android:** copie los almacenes de claves Android necesarios `~/Documents/keystores/MyAndroidApp1`en un directorio al que el usuario de TeamCity tiene acceso, es decir, .
7. **Calabash:** este es un paso opcional si la aplicación tiene pruebas escritas con Calabash. Consulte la guía [Instalación de Calabash en OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) y la guía Actualización de [Ruby con rbenv](https://github.com/calabash/calabash-ios/wiki) para obtener más información.

El diagrama siguiente ilustra todos estos componentes:

![](teamcity-images/image1.png "This diagram illustrates all of these components")

Una vez instalado todo el software, inicie sesión en la cuenta de usuario y confirme que todo el software está instalado y funcionando correctamente. Esto debe implicar compilar la solución y enviar la aplicación a App Center Test. Esto se puede simplificar ejecutando el script de compilación, como se describe en la sección siguiente.

## <a name="create-a-build-script"></a>Crear un script de compilación

Aunque es posible que TeamCity controle todos los aspectos de la compilación y el envío de aplicaciones móviles a App Center Test por sí mismo; se recomienda crear un script de compilación. Un script de compilación proporciona las siguientes ventajas:

1. **Documentación:** un script de compilación sirve como una forma de documentación sobre cómo se crea el software. Esto elimina parte de la "magia" que está asociada con la implementación de la aplicación y permite a los desarrolladores concentrarse en la funcionalidad.
1. **Repetibilidad:** un script de compilación garantiza que cada vez que se compila e implementa la aplicación, se produce de la misma manera, independientemente de quién o qué haga el trabajo. Esta coherencia repetible elimina los problemas o errores que puedan aparecer debido a una compilación ejecutada incorrectamente o a un error humano.
1. **Control de versiones:** se puede incluir un script de compilación en el sistema de control de código fuente. Esto significa que los cambios en el script de compilación se pueden realizar un seguimiento, supervisar y corregir si se encuentran errores o inexactitudes.
1. **Preparar el entorno:** un script de compilación puede incluir lógica para instalar las dependencias de 3a parte necesarias. Esto garantizará que las aplicaciones se construyen con los componentes adecuados.

El script de compilación puede ser tan simple como un archivo de PowerShell (en Windows) o un script bash (en OS X). Al crear el script de compilación, hay varias opciones para los lenguajes de scripting:

- [**Rake**](https://github.com/jimweirich/rake) – este es un lenguaje específico de dominio (DSL) para la construcción de proyectos, basado en Ruby. Rake tiene la ventaja de la popularidad y un rico ecosistema de bibliotecas.

- [**psake**](https://github.com/psake/psake) – esta es una biblioteca de Windows PowerShell para la creación de software

- [**FAKE**](https://fsharp.github.io/FAKE/) : se trata de un DSL basado en F, lo que permite utilizar bibliotecas de .NET existentes si es necesario.

El lenguaje de scripting que se utiliza depende de sus preferencias y requisitos.

> [!NOTE]
> Es posible usar un sistema de compilación basado en XML como MSBuild o NAnt, pero estos carecen de la expresividad y la capacidad de mantenimiento de un DSL que se dedica a la creación de software.

### <a name="parameterizing-the-build-script"></a>Parametrizar el script de compilación

El proceso de creación y prueba de software requiere información que debe mantenerse en secreto. La creación de un APK puede requerir una contraseña para el almacén de claves y/o el alias de clave en el almacén de claves. Del mismo modo, App Center Test requiere una clave de [API](/appcenter/api-docs/) que sea exclusiva de un desarrollador. Estos tipos de valores no deben estar codificados de forma rígida en el script de compilación. En su lugar, se deben pasar como variables al script de compilación.

Menos sensibles son valores como el identificador de dispositivo iOS o el identificador de dispositivo Android que identifican qué dispositivos debe usar App Center para las ejecuciones de prueba. Estos no son valores que deben protegerse, pero pueden cambiar de compilación a compilación.

Almacenar estos tipos de variables fuera del script de compilación también facilita el uso compartido del script de compilación dentro de una organización, con los desarrolladores, por ejemplo. Los desarrolladores pueden usar exactamente el mismo script que el servidor de compilación, pero pueden usar sus propios almacenes de claves y claves de [API.](/appcenter/api-docs/)

Hay dos opciones posibles para almacenar estos valores confidenciales:

- Un archivo de **configuración:** para proteger la clave de [API,](/appcenter/api-docs/) este valor no se debe proteger en el control de versiones. El archivo se puede crear para cada equipo. La forma en que se leen los valores de este archivo depende del lenguaje de scripting utilizado.

- **Variables** de entorno: se pueden establecer fácilmente por equipo y son independientes del lenguaje de scripting subyacente.

Hay ventajas y desventajas para cada una de estas opciones. TeamCity funciona muy bien con variables de entorno, por lo que esta guía recomendará esta técnica al crear scripts de compilación.

### <a name="build-steps"></a>Construir pasos

El script de compilación debe realizar los pasos siguientes:

- **Compilar la aplicación:** esto incluye firmar la aplicación con el perfil de aprovisionamiento correcto.

- **Enviar la aplicación a Xamarin Test Cloud:** esto incluye la firma y la alineación zip del APK con el almacén de claves adecuado.

Estos dos pasos se explicarán con más detalle a continuación.

#### <a name="compiling-a-xamarinios-application"></a>Compilación de una aplicación Xamarin.iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilación de una aplicación Xamarin.Android

Para compilar una aplicación Android, utilice **xbuild** (o **msbuild** en Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
La compilación de la aplicación **Android xbuild** utiliza el proyecto, mientras que la aplicación iOS **xbuild** usa la solución.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Envío de Xamarin.UITests al Centro de aplicaciones

LAS UITests se envían mediante la CLI de [App Center,](https://github.com/microsoft/appcenter-cli)como se muestra en el siguiente fragmento de código:

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Cuando se ejecuta la prueba, los resultados de la prueba se devolverán en forma de un archivo XML de estilo NUnit denominado **report.xml**. TeamCity mostrará la información en el registro de compilación.

Para obtener más información acerca de cómo enviar UITests a App Center, vea [Preparar aplicaciones de Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android) o [Preparar aplicaciones de Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Envío de pruebas de Calabash al App Center

Las pruebas de Calabash se envían mediante la CLI de [App Center,](https://github.com/microsoft/appcenter-cli)como se muestra en el siguiente fragmento de código:

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Para enviar una aplicación Android a App Center Test, es necesario volver a generar primero el servidor de prueba APK mediante calabash-android:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Para obtener más información sobre cómo enviar pruebas de Calabash, consulte la guía de Xamarin sobre el envío de pruebas de [Calabash a La](https://github.com/calabash/calabash-ios/wiki)nube de pruebas .

## <a name="creating-a-teamcity-project"></a>Creación de un proyecto TeamCity

Una vez que TeamCity está instalado y Visual Studio para Mac puede compilar el proyecto, es el momento de crear un proyecto en TeamCity para compilar el proyecto y enviarlo a App Center.

1. Se inició sesión en TeamCity a través del navegador web. Vaya al proyecto raíz:

    ![Vaya al proyecto raíz](teamcity-images/image2.png "Vaya al proyecto raíz") Debajo del proyecto raíz, cree un nuevo subproyecto:

    ![Vaya al proyecto raíz debajo del proyecto raíz, cree un nuevo subproyecto](teamcity-images/image3.png "Vaya al proyecto raíz debajo del proyecto raíz, cree un nuevo subproyecto")
2. Una vez creado el subproyecto, agregue una nueva configuración de compilación:

    ![Una vez creado el subproyecto, agregue una nueva configuración de compilación](teamcity-images/image5.png "Una vez creado el subproyecto, agregue una nueva configuración de compilación")
3. Asocie un proyecto VCS a la configuración de la compilación. Esto se hace a través de la pantalla Configuración de control de versiones:

    ![Esto se hace a través de la pantalla Configuración de control de versiones](teamcity-images/image6.png "Esto se hace a través de la pantalla Configuración de control de versiones")

    Si no hay proyecto VCS creado, usted puede crear uno de la nueva página raíz VCS mostrada abajo:

    ![Si no hay proyecto VCS creado, usted puede crear uno de la nueva página raíz VCS](teamcity-images/image7.png "Si no hay proyecto VCS creado, usted tiene la opción de crear uno de la nueva página raíz VCS")

    Una vez que la raíz VCS se ha asociado, TeamCity desprotegerá el proyecto e intentará detectar automáticamente los pasos de compilación. Si está familiarizado con TeamCity, puede seleccionar uno de los pasos de compilación detectados. Es seguro ignorar los pasos de compilación detectados por ahora.

4. A continuación, configure un desencadenador de compilación. Esto pondrá en cola una compilación cuando se cumplan determinadas condiciones, como cuando un usuario confirma código en el repositorio. La siguiente captura de pantalla muestra cómo agregar un desencadenador de compilación:

    ![Esta captura de pantalla muestra cómo agregar un desencadenador](teamcity-images/image8.png "Esta captura de pantalla muestra cómo agregar un desencadenador de compilación") de compilación Un ejemplo de configuración de un desencadenador de compilación se puede ver en la siguiente captura de pantalla:

    ![Un ejemplo de configuración de un desencadenador de compilación se puede ver en esta captura de pantalla](teamcity-images/image9.png "Un ejemplo de configuración de un desencadenador de compilación se puede ver en esta captura de pantalla")

5. En la sección anterior, Parameterizing the Build Script, se sugiere almacenar algunos valores como variables de entorno. Estas variables se pueden agregar a la configuración de compilación a través de la pantalla Parámetros. Agregue las variables para app Center [API Key](/appcenter/api-docs/), el ID de dispositivo iOS y el ID de dispositivo Android como se muestra en la captura de pantalla siguiente:

    ![Agregue las variables para la clave de API de prueba de App Center, el identificador de dispositivo iOS y el identificador de dispositivo Android](teamcity-images/image11.png "Agregue las variables para la clave de API de Test Cloud, el ID de dispositivo iOS y el ID de dispositivo Android")

6. El último paso es agregar un paso de compilación que invocará el script de compilación para compilar la aplicación y poner en cola la aplicación en App Center Test. La siguiente captura de pantalla es un ejemplo de un paso de compilación que utiliza un Rakefile para compilar una aplicación:

    ![Esta captura de pantalla es un ejemplo de un paso de compilación que utiliza un Rakefile para crear una aplicación](teamcity-images/image12.png "Esta captura de pantalla es un ejemplo de un paso de compilación que utiliza un Rakefile para crear una aplicación")

7. En este momento, la configuración de compilación se ha completado. Es una buena idea desencadenar una compilación para confirmar que el proyecto está configurado correctamente. Una buena manera de hacerlo es confirmar un pequeño e insignificante cambio en el repositorio. TeamCity debe detectar la confirmación e iniciar una compilación.

8. Una vez completada la compilación, inspeccione el registro de compilación y compruebe si hay algún problema o advertencia con la compilación que requiera atención.

## <a name="summary"></a>Resumen

Esta guía explica cómo usar TeamCity para compilar aplicaciones de Xamarin Mobile y, a continuación, enviarlas a la prueba de App Center. Hemos discutido la creación de un script de compilación para automatizar el proceso de compilación. El script de compilación se encarga de compilar la aplicación, enviarla a App Center Test y esperar los resultados.

A continuación, tratamos cómo crear un proyecto en TeamCity que pondrá en cola una compilación cada vez que un desarrollador confirme el código y llame al script de compilación.

## <a name="related-links"></a>Vínculos relacionados

- [Preparación de aplicaciones Xamarin.Android](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Preparación de aplicaciones de Xamarin.iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Instalación y configuración de TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
