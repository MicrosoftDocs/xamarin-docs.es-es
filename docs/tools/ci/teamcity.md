---
title: Usar el equipo City con Xamarin
description: En esta guía se explican los pasos implicados en el uso de TeamCity para compilar aplicaciones móviles y después enviarlas a App Center prueba.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: a7118cc3ce9c0a5c4254bb5921800fe38acd85a0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458230"
---
# <a name="using-team-city-with-xamarin"></a>Usar el equipo City con Xamarin

_En esta guía se explican los pasos implicados en el uso de TeamCity para compilar aplicaciones móviles y después enviarlas a App Center prueba._

Como se describe en la guía de [Introducción a la integración continua](~/tools/ci/intro-to-ci.md) , la integración continua (CI) es una práctica útil al desarrollar aplicaciones móviles de calidad. Existen muchas opciones viables para el software de servidor de integración continua. Esta guía se centrará en [TeamCity](https://www.jetbrains.com/teamcity/) de JetBrains.

Hay varias permutaciones diferentes de una instalación de TeamCity. En la lista siguiente se describen algunas de estas permutaciones:

- **Servicio de Windows** : en este escenario, TeamCity se inicia cuando Windows arranca como un servicio de Windows. Se debe emparejar con un host de compilación de Mac para compilar las aplicaciones de iOS.

- **Iniciar demonio en OS X** : conceptualmente, esto es similar a la ejecución como un servicio de Windows que se describe en el paso anterior. De forma predeterminada, las compilaciones se ejecutarán en la cuenta raíz.

- **Cuenta de usuario en OS X** : es posible ejecutar TeamCity en una cuenta de usuario que se inicia cada vez que el usuario inicia sesión.

De los escenarios anteriores, la ejecución de TeamCity en una cuenta de usuario en OS X es la más sencilla y fácil de configurar.

Hay varios pasos implicados en la configuración de TeamCity:

- **Instalación de TeamCity** : la instalación de TeamCity no se trata en esta guía. En esta guía se da por supuesto que TeamCity se ha instalado y se está ejecutando en una cuenta de usuario. Las instrucciones sobre cómo [instalar TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) se pueden encontrar en la [documentación de TeamCity 8](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) de JetBrains.

- **Preparar el servidor de compilación** : este paso implica la instalación del software, las herramientas y los certificados necesarios para compilar aplicaciones móviles y prepararlas para su distribución.

- **Crear un script de compilación** : este paso no es estrictamente necesario, pero un script de compilación es una ayuda útil para crear aplicaciones desatendidas. El uso de un script de compilación le ayudará a solucionar problemas de compilación que pueden surgir y proporciona una manera coherente y repetible de crear los archivos binarios para la distribución, incluso si no se practica la integración continua.

- **Crear un proyecto de TeamCity** : una vez completados los tres pasos anteriores, debemos crear un proyecto de TeamCity que contendrá todos los metadatos necesarios para recuperar el código fuente, compilar los proyectos y enviar las pruebas a App Center prueba.

## <a name="requirements"></a>Requisitos

Se requiere experiencia con [App Center prueba](/appcenter/test-cloud/) .

Es necesario estar familiarizado con TeamCity 8,1. La instalación de TeamCity está fuera del ámbito de este documento. Se supone que TeamCity está instalado en OS X Mavericks y se ejecuta con una cuenta de usuario normal y no con la cuenta raíz.

El servidor de compilación debe ser un equipo independiente que ejecute OS X y que esté dedicado a la integración continua. Idealmente, el servidor de compilación no será responsable de ningún otro rol, como un servidor de base de datos, un servidor web o una estación de trabajo de desarrollador.

> [!IMPORTANT]
> Esta guía no cubre una instalación "sin periféricos" de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparar el servidor de compilación

Un paso fundamental en la configuración de un servidor de compilación es instalar todas las herramientas, el software y los certificados necesarios para compilar las aplicaciones móviles. Es importante que el servidor de compilación pueda compilar la solución móvil y ejecutar cualquier prueba. Para minimizar los problemas de configuración, el software y las herramientas deben instalarse en la misma cuenta de usuario que hospeda TeamCity. En la lista siguiente se detalla lo que se requiere:

1. **Visual Studio para Mac** : Esto incluye Xamarin. iOS y Xamarin. Android.
2. **Iniciar sesión en el almacén de componentes de Xamarin** : este paso es opcional y solo es necesario si la aplicación usa componentes del almacén de componentes de Xamarin. Iniciar sesión de forma proactiva en el almacén de componentes en este momento impedirá que se produzcan problemas cuando una compilación de TeamCity intente compilar la aplicación.
3. **Xcode** : Xcode es necesario para compilar y firmar aplicaciones de iOS.
4. **Herramientas de línea de comandos de Xcode** : esto se describe en el paso 1 de la sección de instalación de la guía de [actualización de Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki) .
5. **Identidad de firma & perfiles de aprovisionamiento** : importe los certificados y el perfil de aprovisionamiento a través de Xcode. Consulte la guía de Apple sobre la [exportación de identidades de firma y perfiles de aprovisionamiento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obtener más detalles.
6. **Keystores de Android** : Copie el keystores de Android necesario en un directorio al que el usuario TeamCity tenga acceso, es decir, `~/Documents/keystores/MyAndroidApp1` .
7. **Calabash** : este paso es opcional si la aplicación tiene pruebas escritas con calabash. Consulte la guía de [instalación de calabash en OS X Mavericks](https://github.com/calabash/calabash-ios/wiki) y la guía de [actualización de Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki) para obtener más información.

En el siguiente diagrama se ilustran todos estos componentes:

![En este diagrama se ilustran todos estos componentes](teamcity-images/image1.png)

Una vez instalado todo el software, inicie sesión en la cuenta de usuario y confirme que todo el software está correctamente instalado y en funcionamiento. Esto debe implicar la compilación de la solución y el envío de la aplicación a App Center prueba. Esto se puede simplificar mediante la ejecución del script de compilación, como se describe en la sección siguiente.

## <a name="create-a-build-script"></a>Crear un script de compilación

Aunque es posible que TeamCity controle todos los aspectos de la compilación y el envío de aplicaciones móviles a App Center prueba por sí sola; se recomienda crear un script de compilación. Un script de compilación proporciona las siguientes ventajas:

1. **Documentación** : un script de compilación sirve como una forma de documentación sobre cómo se compila el software. Esto quita parte de la "magia" que está asociada con la implementación de la aplicación y permite a los desarrolladores concentrarse en la funcionalidad.
1. **Repetibilidad** : un script de compilación garantiza que cada vez que se compila e implementa la aplicación, se produce de la misma manera, independientemente de quién o qué hace el trabajo. Esta coherencia repetible elimina los problemas o errores que puedan aparecer debido a una compilación o un error humano incorrectos.
1. Control de **versiones** : un script de compilación se puede incluir en el sistema de control de código fuente. Esto significa que se puede realizar un seguimiento de los cambios en el script de compilación, supervisarlos y corregirlos si se encuentran errores o imprecisiones.
1. **Preparar el entorno** : un script de compilación puede incluir lógica para instalar las dependencias de terceros necesarias. Así se asegurará de que las aplicaciones se compilan con los componentes adecuados.

El script de compilación puede ser tan simple como un archivo de PowerShell (en Windows) o un script de Bash (en OS X). Al crear el script de compilación, hay varias opciones para los lenguajes de scripting:

- [**Inclinación**](https://github.com/jimweirich/rake) : se trata de un lenguaje específico de dominio (DSL) para compilar proyectos, basado en Ruby. El uso de inclinación tiene la ventaja de la popularidad y el completo ecosistema de las bibliotecas.

- [**psake**](https://github.com/psake/psake) : se trata de una biblioteca de Windows PowerShell para compilar software

- [**Falsa**](https://fsharp.github.io/FAKE/) : se trata de un DSL basado en F # que permite usar las bibliotecas de .net existentes si es necesario.

El lenguaje de scripting que se use dependerá de sus preferencias y requisitos.

> [!NOTE]
> Es posible usar un sistema de compilación basado en XML como MSBuild o NAnt, pero estos no tienen la expresividad y el mantenimiento de un DSL dedicado a la creación de software.

### <a name="parameterizing-the-build-script"></a>Parametrización del script de compilación

El proceso de creación y prueba de software requiere información que debe mantenerse en secreto. La creación de una APK puede requerir una contraseña para el almacén de claves o el alias de clave en el almacén de claves. Del mismo modo, App Center prueba requiere una [clave de API](/appcenter/api-docs/) que sea única para un desarrollador. Estos tipos de valores no deben estar codificados de forma rígida en el script de compilación. En su lugar, deben pasarse como variables al script de compilación.

Menos sensible son valores como el identificador de dispositivo iOS o el identificador de dispositivo Android que identifican qué dispositivos App Center deben usar para las series de pruebas. Estos no son valores que deben protegerse, pero pueden cambiar de compilación a compilación.

Almacenar estos tipos de variables fuera del script de compilación también facilita el uso compartido del script de compilación dentro de una organización, por ejemplo, con los desarrolladores. Los desarrolladores pueden usar exactamente el mismo script que el servidor de compilación, pero pueden usar sus propias [claves](/appcenter/api-docs/)de keystores y API.

Hay dos opciones posibles para almacenar estos valores confidenciales:

- **Un archivo de configuración** : para proteger la [clave de API](/appcenter/api-docs/) , este valor no debe protegerse en el control de versiones. El archivo se puede crear para cada equipo. La forma en que se leen los valores de este archivo depende del lenguaje de scripting utilizado.

- **Variables de entorno** : se pueden establecer fácilmente en cada equipo y son independientes del lenguaje de scripting subyacente.

Cada una de estas opciones tiene ventajas y desventajas. TeamCity funciona perfectamente con variables de entorno, por lo que en esta guía se recomienda esta técnica al crear scripts de compilación.

### <a name="build-steps"></a>Pasos de compilación

El script de compilación debe realizar los siguientes pasos:

- **Compilar la aplicación** : Esto incluye firmar la aplicación con el perfil de aprovisionamiento correcto.

- **Envíe la aplicación a Xamarin test Cloud** : Esto incluye la firma y el código postal que alinea el APK con el almacén de claves adecuado.

Estos dos pasos se explicarán con más detalle a continuación.

#### <a name="compiling-a-xamarinios-application"></a>Compilar una aplicación de Xamarin. iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilar una aplicación de Xamarin. Android

Para compilar una aplicación de Android, use **xbuild** (o **msbuild** en Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
La compilación de la aplicación de Android **xbuild** usa el proyecto, mientras que la aplicación de iOS **xbuild** usa la solución.

#### <a name="submitting-xamarinuitests-to-app-center"></a>Enviando Xamarin. UITests a App Center

UITests se envían mediante la [CLI de App Center](https://github.com/microsoft/appcenter-cli), como se muestra en el siguiente fragmento de código:

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

Cuando se ejecuta la prueba, los resultados de la prueba se devolverán en forma de un archivo XML de estilo NUnit denominado **report.xml**. TeamCity mostrará la información en el registro de compilación.

Para obtener más información sobre cómo enviar UITests a App Center, consulte [preparar aplicaciones de Xamarin. Android](/appcenter/test-cloud/uitest/preparing-for-upload-android) o [preparar aplicaciones de Xamarin. iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios).

#### <a name="submitting-calabash-tests-to-app-center"></a>Enviar pruebas de calabash a App Center

Las pruebas de calabash se envían mediante la [CLI de App Center](https://github.com/microsoft/appcenter-cli), como se muestra en el siguiente fragmento de código:

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

Para enviar una aplicación de Android a App Center prueba, es necesario recompilar primero el servidor de prueba de APK con calabash-Android:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

Para obtener más información sobre el envío de pruebas de Calabash, consulte la guía de Xamarin sobre el [envío de pruebas de calabash a test Cloud](https://github.com/calabash/calabash-ios/wiki).

## <a name="creating-a-teamcity-project"></a>Creación de un proyecto de TeamCity

Una vez que se instala TeamCity y Visual Studio para Mac puede compilar el proyecto, es el momento de crear un proyecto en TeamCity para compilar el proyecto y enviarlo a App Center.

1. Se inicia iniciando sesión en TeamCity a través del explorador Web. Navegue hasta el proyecto raíz:

    ![Navegue hasta el proyecto raíz](teamcity-images/image2.png "Navegue hasta el proyecto raíz.") . Debajo del proyecto raíz, cree un nuevo subproyecto:

    ![Vaya al proyecto raíz debajo del proyecto raíz, crear un nuevo subproyecto](teamcity-images/image3.png "Vaya al proyecto raíz debajo del proyecto raíz, crear un nuevo subproyecto")
2. Una vez creado el subproyecto, agregue una nueva configuración de compilación:

    ![Una vez creado el subproyecto, agregue una nueva configuración de compilación.](teamcity-images/image5.png "Una vez creado el subproyecto, agregue una nueva configuración de compilación.")
3. Adjunte un proyecto de VCS a la configuración de compilación. Esto se hace a través de la pantalla de configuración de control de versiones:

    ![Esto se hace a través de la pantalla de configuración de control de versiones](teamcity-images/image6.png "Esto se hace a través de la pantalla de configuración de control de versiones")

    Si no se ha creado ningún proyecto de VCS, puede crear uno en la nueva página raíz del VCS que se muestra a continuación:

    ![Si no se ha creado ningún proyecto de VCS, puede crear uno en la página raíz del nuevo VCS.](teamcity-images/image7.png "Si no se ha creado ningún proyecto de VCS, tiene la opción de crear uno en la página raíz del nuevo VCS.")

    Una vez que se ha conectado la raíz del VCS, TeamCity desprotegerá el proyecto e intentará detectar automáticamente los pasos de la compilación. Si está familiarizado con TeamCity, puede seleccionar uno de los pasos de compilación detectados. Por ahora, es seguro omitir los pasos de compilación detectados.

4. A continuación, configure un desencadenador de compilación. De esta forma se pone en cola una compilación cuando se cumplen ciertas condiciones, por ejemplo, cuando un usuario confirma el código en el repositorio. En la captura de pantalla siguiente se muestra cómo agregar un desencadenador de compilación:

    ![En esta captura de pantalla se muestra cómo agregar un desencadenador de compilación](teamcity-images/image8.png "En esta captura de pantalla se muestra cómo agregar un desencadenador de compilación") Un ejemplo de configuración de un desencadenador de compilación puede verse en la siguiente captura de pantalla:

    ![En esta captura de pantalla puede verse un ejemplo de configuración de un desencadenador de compilación](teamcity-images/image9.png "En esta captura de pantalla puede verse un ejemplo de configuración de un desencadenador de compilación")

5. En la sección anterior, parametrización del script de compilación, se sugieren almacenar algunos valores como variables de entorno. Estas variables se pueden agregar a la configuración de compilación a través de la pantalla de parámetros. Agregue las variables para la [clave de API](/appcenter/api-docs/)de App Center, el identificador de dispositivo iOS y el identificador de dispositivo Android, tal como se muestra en la siguiente captura de pantalla:

    ![Agregue las variables de la clave de API de prueba de App Center, el identificador de dispositivo iOS y el identificador de dispositivo Android.](teamcity-images/image11.png "Agregue las variables para la clave de API de Test Cloud, el identificador de dispositivo iOS y el identificador de dispositivo Android.")

6. El paso final consiste en agregar un paso de compilación que invoque el script de compilación para compilar la aplicación y poner la aplicación en cola para App Center prueba. La captura de pantalla siguiente es un ejemplo de un paso de compilación que usa Rakefile para compilar una aplicación:

    ![Esta captura de pantalla es un ejemplo de un paso de compilación que usa un Rakefile para compilar una aplicación.](teamcity-images/image12.png "Esta captura de pantalla es un ejemplo de un paso de compilación que usa un Rakefile para compilar una aplicación.")

7. En este momento, se completa la configuración de compilación. Es una buena idea desencadenar una compilación para confirmar que el proyecto está correctamente configurado. Una buena forma de hacerlo es confirmar un pequeño cambio insignificante en el repositorio. TeamCity debe detectar la confirmación e iniciar una compilación.

8. Una vez completada la compilación, revise el registro de compilación y compruebe si hay algún problema o advertencia con la compilación que requiera atención.

## <a name="summary"></a>Resumen

En esta guía se explica cómo usar TeamCity para compilar aplicaciones móviles de Xamarin y después enviarlas a App Center test. Hemos explicado cómo crear un script de compilación para automatizar el proceso de compilación. El script de compilación se encarga de compilar la aplicación, enviar a App Center prueba y esperar los resultados.

A continuación, hemos explicado cómo crear un proyecto en TeamCity que pone en cola una compilación cada vez que un desarrollador confirma el código y llama al script de compilación.

## <a name="related-links"></a>Vínculos relacionados

- [Preparación de las aplicaciones de Xamarin. Android](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [Preparación de las aplicaciones de Xamarin. iOS](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [Instalación y configuración de TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)