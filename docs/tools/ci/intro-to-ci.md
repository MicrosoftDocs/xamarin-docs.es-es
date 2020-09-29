---
title: Introducción a la integración continua con Xamarin
description: En este documento se describe la integración continua con Xamarin. Describe el control de versiones y varios entornos de integración continua.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: davidortinau
ms.author: daortin
ms.date: 07/19/2017
ms.openlocfilehash: 98606173913b57b7cf55f24c2581acbf82a587e3
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457866"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introducción a la integración continua con Xamarin

_La integración continua es una práctica de ingeniería de software en la que una compilación automatizada compila y, opcionalmente, prueba una aplicación cuando los desarrolladores agregan o modifican código en el repositorio de control de versiones del proyecto. En este artículo se describen los conceptos generales de la integración continua y algunas de las opciones disponibles para la integración continua con los proyectos de Xamarin._

Es habitual que los desarrolladores trabajen en paralelo en proyectos de software. En algún momento, es necesario integrar todas estas secuencias paralelas de trabajo en un código base que conforma el producto final. En los primeros días de desarrollo de software, esta integración se realizó al final de un proyecto, que era un proceso difícil y arriesgado.

La integración continua (CI) evita este tipo de complejidads combinando los cambios de cada Desarrollador en la base de código común de forma continua, normalmente siempre que los desarrolladores protegen los cambios en el repositorio de código compartido del proyecto. Cada inserción en el repositorio desencadena una compilación automatizada y ejecuta pruebas automatizadas para comprobar que el código recién introducido no ha interrumpido el código existente.  De esta manera, los elementos de configuración muestran errores y problemas inmediatamente y garantizan que todos los miembros del equipo están actualizados con el trabajo de los demás. Esto da como resultado un código base coherente y estable.

Los sistemas de integración continua tienen dos partes principales:

- **Control de versiones** : el control de versiones (VC), también denominado control de código fuente o administración de código fuente, consolida todo el código de un proyecto en un único repositorio compartido y mantiene un historial completo de todos los cambios en cada archivo. Este repositorio, que a menudo se conoce *como la principal o la* bifurcación *principal* , contiene el código fuente que se usará en última instancia para compilar la versión de producción o de lanzamiento de la aplicación. Hay muchos productos comerciales y de código abierto para esta tarea, que normalmente permiten a los equipos o usuarios bifurcar una copia del código en ramas secundarias, donde pueden realizar cambios extensivos o realizar experimentos sin riesgo en la rama principal. Una vez que se validan los cambios en una rama secundaria, se pueden volver a combinar todas en la rama principal.
- **Servidor de integración continua** : el servidor de integración continua es responsable de recopilar todos los artefactos de un proyecto (código fuente, imágenes, vídeos, bases de datos, pruebas automatizadas, etc.), compilar la aplicación y ejecutar las pruebas automatizadas. De nuevo, hay muchas herramientas de servidor de CI de código abierto y comerciales.

Los desarrolladores suelen tener una copia de trabajo de una o varias bifurcaciones en sus estaciones de trabajo, donde el trabajo se realiza inicialmente. Una vez que se completa un conjunto adecuado de trabajo, los cambios se "protegen" o "confirmados" en la bifurcación correspondiente, que los propaga a las copias de trabajo de otros desarrolladores. Así es como un equipo se asegura de que todo funciona en el mismo código.

De nuevo, con la integración continua, el hecho de confirmar los cambios hace que el servidor de CI compile el proyecto y ejecute pruebas automatizadas para comprobar la corrección del código fuente. Si hay errores de compilación o errores de prueba, un servidor de CI informa al desarrollador responsable (por correo electrónico, mensajería instantánea, Twitter, Growl, etc.) para que pueda corregir el problema. (Los servidores de CI pueden incluso rechazar la confirmación si hay errores, lo que se denomina "inserción en el repositorio validada").

En el siguiente diagrama se ilustra este proceso:

[![Este diagrama ilustra este proceso](intro-to-ci-images/intro01-small.png)](intro-to-ci-images/intro01.png#lightbox)

Las aplicaciones móviles presentan desafíos únicos para la integración continua. Las aplicaciones pueden requerir sensores como el GPS o la cámara que solo están disponibles en dispositivos físicos. Además, los simuladores o emuladores son solo una aproximación del hardware y pueden ocultar o ocultar problemas. Al final, es necesario probar una aplicación móvil en hardware real para estar seguro de que está realmente preparada para el cliente.

La [prueba de App Center](/appcenter/test-cloud) aborda este problema concreto probando aplicaciones directamente en cientos de dispositivos físicos. Los desarrolladores escriben pruebas de aceptación automatizadas, lo que permite realizar pruebas de IU eficaces. Una vez que estas pruebas se cargan en App Center, el servidor de CI puede ejecutarlas automáticamente como parte de un proceso de CI, tal como se muestra en el diagrama siguiente:

[![Una vez que estas pruebas se cargan en App Center, el servidor de CI puede ejecutarlas automáticamente como parte de un proceso de CI, tal como se muestra en este diagrama.](intro-to-ci-images/intro02-small.png)](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Componentes de integración continua

Existe un amplio ecosistema de herramientas comerciales y de código abierto diseñadas para admitir CI. En esta sección se explican algunos de los más comunes.

### <a name="version-control"></a>Control de versiones

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps y Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) y [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) son herramientas de colaboración de Microsoft para los servicios de compilación de integración continua, seguimiento de tareas, herramientas de planeación y generación de informes ágiles y control de versiones. Con el control de versiones, Azure DevOps y TFS pueden trabajar con su propio sistema (Control de versiones de Team Foundation o TFVC) o con proyectos hospedados en GitHub.

- Azure DevOps proporciona servicios a través de la nube. Su principal ventaja es que no requiere hardware ni infraestructura dedicados y se puede acceder a ellos desde cualquier lugar a través de exploradores Web y a través de herramientas de desarrollo conocidas como Visual Studio, lo que hace que resulte atractivo para los equipos que están distribuidos geográficamente. Es gratis para los equipos de cinco desarrolladores o menos, después del cual se pueden adquirir licencias adicionales para dar cabida a un equipo en aumento.
- TFS está diseñado para servidores de Windows locales y se obtiene acceso a ellos a través de una red local o una conexión VPN a esa red. La principal ventaja es que se controla totalmente la configuración de los servidores de compilación y se puede instalar el software o los servicios adicionales que se necesiten. TFS tiene una edición Express de nivel de entrada gratuita para equipos pequeños.

Tanto TFS como Azure DevOps se integran estrechamente con Visual Studio y permiten a los desarrolladores realizar muchas tareas de control de versiones y de elementos de contenido desde la comodidad de un solo IDE. También está disponible el complemento de Team Explorer Everywhere para Eclipse (consulte a continuación). Visual Studio para Mac tiene [una vista previa de TFVC disponible](/visualstudio/mac/tf-version-control/).

Las [canalizaciones DevOps de Azure](/azure/devops/pipelines/languages/xamarin/) tienen compatibilidad directa con los proyectos de Xamarin, en los que se crea una definición de compilación para cada plataforma de destino (Android, iOS y Windows). La licencia de Xamarin adecuada es necesaria para cada definición de compilación. También es posible conectar un servidor de compilación de TFS compatible con Xamarin local a Azure DevOps con este fin. Con esta configuración, las compilaciones que se ponen en cola en Azure DevOps se delegarán en el servidor local. Para obtener más información, consulte [agentes de compilación y lanzamiento](/azure/devops/pipelines/agents/agents). Como alternativa, puede usar otra herramienta de compilación como Jenkins o City del equipo.

Un resumen completo de todas las características de la administración del ciclo de vida de las aplicaciones (ALM) de Visual Studio, Azure DevOps y Team Foundation Server, consulte [DevOps con aplicaciones Xamarin](/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) aporta la eficacia de Team Foundation Server y Azure DevOps a los equipos que desarrollan fuera de Visual Studio. Permite a los desarrolladores conectarse a proyectos de equipo locales o en la nube desde Eclipse o el cliente de línea de comandos multiplataforma para OS X y Linux. Team Explorer Everywhere proporciona acceso completo al control de versiones (incluido GIT), a los elementos de trabajo y a las capacidades de compilación para las plataformas que no son de Windows.

#### <a name="git"></a>Git

[Git](https://git-scm.com) es una conocida solución de control de versiones de código abierto que se desarrolló originalmente para administrar el código fuente del kernel de Linux. Es un sistema muy rápido y flexible que es popular con proyectos de software de todos los tamaños. Se puede escalar fácilmente desde desarrolladores únicos con acceso a Internet deficiente a equipos grandes que abarcan todo el mundo. Git también hace que la bifurcación resulte muy fácil, lo que a su vez puede favorecer transmisiones paralelas de desarrollo con un riesgo mínimo.

Git puede funcionar completamente a través de los exploradores Web o [los clientes de GUI](https://git-scm.com/downloads/guis) que se ejecutan en Linux, Mac OSX y Windows. Es gratis para los repositorios públicos; los repositorios privados requieren un [plan de pago](https://github.com/pricing).

Las versiones actuales de Visual Studio para Windows y Mac proporcionan compatibilidad nativa con git. Microsoft proporciona una [extensión descargable para git](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) para versiones anteriores de Visual Studio. Como se indicó anteriormente, Azure DevOps y TFS pueden usar git para el control de versiones en lugar de TFVC.

#### <a name="subversion"></a>Subversion

[Subversion](https://subversion.apache.org) (SVN) es un conocido sistema de control de versiones de código abierto que se ha usado desde 2000. SVN se ejecuta en todas las versiones modernas de OS X, Windows, FreeBSD, Linux y Unix. Visual Studio para Mac tiene compatibilidad nativa con SVN. Hay extensiones de terceros que aportan compatibilidad con SVN a Visual Studio.

### <a name="continuous-integration-environments"></a>Entornos de integración continua

La configuración de un entorno de integración continua significa la combinación de un sistema de control de versiones con un servicio de compilación.  En este último caso, los dos más comunes son:

- [Azure pipelines](/azure/devops/pipelines/) es el sistema de compilación de Azure DEVOPS y TFS. Está estrechamente integrado con Visual Studio, lo que facilita a los desarrolladores la activación de compilaciones, la ejecución automática de pruebas y la visualización de los resultados.
- Jenkins es un servidor de CI de código abierto con un rico ecosistema de complementos para admitir todo tipo de desarrollo de software. Se ejecuta en Windows y Mac OS X. Jenkins no se integra con ningún IDE específico. En su lugar, se configura y se administra a través de una interfaz Web. El elemento de configuración de Jenkins también es fácil de instalar y configurar, lo que hace que resulte atractivo para equipos pequeños.

Puede usar TFS/Azure DevOps por sí solo, o puede usar Jenkins en combinación con TFS/Azure DevOps o GIT, tal como se describe en las secciones siguientes.

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps y Team Foundation Server

Como se explicó, Azure DevOps y Team Foundation Server proporciona servicios de compilación y control de versiones. Los servicios de compilación siempre requieren una licencia de Xamarin Business o Enterprise para cada plataforma de destino.

Con Azure DevOps, puede crear una definición de compilación independiente para cada plataforma de destino y especificar allí la licencia adecuada. Una vez configurado, Azure DevOps ejecutará compilaciones y pruebas en la nube. Consulte [Azure pipelines](/azure/devops/pipelines/) para obtener más detalles.

Con Team Foundation Server, configure una máquina de compilación como se indica a continuación para plataformas de destino específicas:

- **Android y Windows:** Instale Visual Studio y las herramientas de Xamarin (para Android y Windows) y configúrela con sus licencias de Xamarin. También es necesario trasladar el Android SDK a una ubicación compartida en el servidor donde el agente de compilación de TFS pueda encontrarlo. Para obtener más información, consulte [configuración de TFVC](/azure/devops/repos/tfvc/overview).
- **iOS y Xamarin:** Instale Visual Studio y las herramientas de Xamarin en Windows Server con la licencia adecuada. A continuación, instale Visual Studio para Mac en una máquina Mac OS X accesible desde la red, que servirá como host de compilación y creará el paquete de aplicación final (IPA para iOS, APP para OS X).

En el siguiente diagrama se ilustra esta topografía:

[![Este diagrama ilustra esta topografía](intro-to-ci-images/intro03-small.png)](intro-to-ci-images/intro03.png#lightbox)

También es posible vincular un servidor de TFS local a un proyecto de Azure DevOps para que las compilaciones de Azure DevOps se deleguen en el servidor local. Para obtener más información, vea [agentes de compilación y lanzamiento](/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps y Jenkins

Si usa Jenkins para compilar las aplicaciones, puede almacenar el código en Azure DevOps o Team Foundation Server y seguir usando Jenkins para las compilaciones de CI. Puede desencadenar una compilación de Jenkins cuando inserte código en el repositorio de Git del proyecto de equipo o cuando compruebe el código en TFVC. Para más información, consulte [Jenkins con Azure DevOps](/azure/devops/service-hooks/services/jenkins).

[![Si usa Jenkins para compilar las aplicaciones, puede almacenar el código en Azure DevOps o Team Foundation Server y seguir usando Jenkins para las compilaciones de CI](intro-to-ci-images/intro04-small.png)](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git y Jenkins

Otro entorno común de CI puede ser totalmente basado en OS X. Este escenario implica el uso de Git para el control de código fuente y Jenkins para el servidor de compilación. Ambos se ejecutan en un único equipo Mac OS X con Visual Studio para Mac instalado. Esto es muy similar al entorno de Azure DevOps + Jenkins descrito en la sección anterior:

[![Esto es muy similar al entorno de Azure DevOps + Jenkins descrito en la sección anterior.](intro-to-ci-images/intro05-small.png)](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins [no es compatible con Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**