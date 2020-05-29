---
title: "Introducción al desarrollo de aplicaciones empresariales" Descripción: "en este capítulo se proporciona una introducción al desarrollo de aplicaciones empresariales y se presenta la aplicación móvil eShopOnContainers".
MS. Prod: Xamarin ms. AssetID: cbce0659-FA03-447A-86ec-140438143230 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/07/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-enterprise-app-development"></a>Introducción al desarrollo de aplicaciones empresariales

Independientemente de la plataforma, los desarrolladores de aplicaciones empresariales se enfrentan a varios desafíos:

- Requisitos de la aplicación que pueden cambiar con el tiempo.
- Nuevas oportunidades de negocio y desafíos.
- Comentarios en curso durante el desarrollo que pueden afectar significativamente al ámbito y a los requisitos de la aplicación.

Teniendo esto en cuenta, es importante crear aplicaciones que se puedan modificar o ampliar fácilmente con el tiempo. El diseño para esta adaptación puede ser difícil, ya que requiere una arquitectura que permita que las partes individuales de la aplicación se desarrollen y prueben de forma independiente en aislamiento sin que afecte al resto de la aplicación.

Muchas aplicaciones empresariales son lo suficientemente complejas como para requerir más de un desarrollador. Puede ser un desafío importante decidir cómo diseñar una aplicación para que varios desarrolladores puedan trabajar de forma eficaz en distintas partes de la aplicación de forma independiente, a la vez que se aseguran de que las piezas se unen sin problemas cuando se integran en la aplicación.

El enfoque tradicional para diseñar y compilar una aplicación produce lo que se conoce como una aplicación *monolítica* , donde los componentes están estrechamente acoplados sin una separación clara entre ellos. Normalmente, este enfoque monolítico conduce a aplicaciones que son difíciles y poco eficientes de mantener, ya que puede ser difícil resolver errores sin romper otros componentes en la aplicación, y puede ser difícil agregar nuevas características o reemplazar las existentes.

Una solución eficaz para estos desafíos es particionar una aplicación en componentes discretos de acoplamiento flexible que se pueden integrar fácilmente juntos en una aplicación. Este enfoque ofrece varias ventajas:

- Permite que distintas personas o equipos puedan desarrollar, probar, ampliar y mantener la funcionalidad individual.
- Promueve la reutilización y una separación limpia de las preocupaciones entre las funcionalidades horizontales de la aplicación, como la autenticación y el acceso a datos, y las funcionalidades verticales, como la funcionalidad empresarial específica de la aplicación. Esto permite administrar las dependencias y las interacciones entre los componentes de la aplicación más fácilmente.
- Ayuda a mantener una separación de roles, ya que permite que individuos, o equipos, se centren en una tarea específica o en una parte de la funcionalidad de acuerdo con sus conocimientos. En concreto, proporciona una separación más nítida entre la interfaz de usuario y la lógica de negocios de la aplicación.

Sin embargo, hay muchos problemas que se deben resolver al particionar una aplicación en componentes discretos de acoplamiento flexible. Estas incluyen:

- Decidir cómo proporcionar una separación limpia de los aspectos entre los controles de la interfaz de usuario y su lógica. Una de las decisiones más importantes a la hora de crear una Xamarin.Forms aplicación empresarial es si se debe colocar lógica de negocios en archivos de código subyacente, o si se va a crear una separación limpia de los aspectos entre los controles de la interfaz de usuario y su lógica, para que la aplicación sea más fácil de mantener y probar. Para obtener más información, vea [modelo-vista-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
- Determinar si se va a usar un contenedor de inserción de dependencias. Los contenedores de inserción de dependencias reducen el acoplamiento de dependencias entre los objetos al proporcionar una utilidad para construir instancias de clases con sus dependencias insertadas y administrar su duración en función de la configuración del contenedor. Para obtener más información, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
- Elección entre los eventos proporcionados por la plataforma y la comunicación basada en mensajes de acoplamiento flexible entre los componentes que no son convenientes para vincularlos mediante referencias de tipo y objeto. Para obtener más información, vea la introducción a la [comunicación entre componentes de acoplamiento flexible](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
- Decidir cómo desplazarse entre las páginas, incluido cómo invocar la navegación y dónde debe residir la lógica de navegación. Para obtener más información, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md) (Navegación).
- Determinar cómo validar los datos proporcionados por el usuario para comprobar si son correctos. La decisión debe incluir cómo validar los datos proporcionados por el usuario y cómo notificar a los usuarios sobre los errores de validación. Para obtener más información, consulte [validación](~/xamarin-forms/enterprise-application-patterns/validation.md).
- Decidir cómo realizar la autenticación y cómo proteger los recursos con autorización. Para obtener más información, consulte [autenticación y autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
- Determinar cómo obtener acceso a datos remotos desde servicios Web, incluido cómo recuperar datos de forma confiable y cómo almacenar datos en caché. Para obtener más información, vea [acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
- Decidir cómo probar la aplicación. Para más información, consulte [Pruebas unitarias](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

En esta guía se proporcionan instrucciones sobre estos problemas y se centra en los patrones y la arquitectura principales para crear una aplicación empresarial multiplataforma con Xamarin.Forms . La guía pretende ayudar a generar código adaptable, fácil de mantener y comprobable, ya que aborda Xamarin.Forms escenarios comunes de desarrollo de aplicaciones empresariales y separa las preocupaciones de la presentación, la lógica de presentación y las entidades a través de la compatibilidad con el patrón Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicación de ejemplo

En esta guía se incluye una aplicación de ejemplo, eShopOnContainers, que es una tienda en línea que incluye la siguiente funcionalidad:

- Autenticación y autorización en un servicio back-end.
- Exploración de un catálogo de camisas, tazas de café y otros elementos de marketing.
- Filtrar el catálogo.
- Ordenar los elementos del catálogo.
- Ver el historial de pedidos del usuario.
- Configuración de las opciones.

### <a name="sample-application-architecture"></a>Arquitectura de aplicación de ejemplo

En la figura 1-1 se proporciona información general de alto nivel sobre la arquitectura de la aplicación de ejemplo.

![](introduction-images/architecture.png "eShopOnContainers high-level architecture")

**Figura 1-1**: arquitectura de alto nivel de eShopOnContainers

La aplicación de ejemplo se incluye en tres aplicaciones cliente:

- Una aplicación MVC desarrollada con ASP.NET Core.
- Una aplicación de una sola página (SPA) desarrollada con angular 2 y Typescript. Este enfoque para las aplicaciones web evita realizar un recorrido de ida y vuelta al servidor con cada operación.
- Una aplicación móvil desarrollada con Xamarin.Forms , que admite iOS, Android y el plataforma universal de Windows (UWP).

Para obtener información sobre las aplicaciones Web, consulte [diseño y desarrollo de aplicaciones web modernas con ASP.net Core y Microsoft Azure](https://aka.ms/WebAppEbook).

La aplicación de ejemplo incluye los siguientes servicios back-end:

- Un microservicio de identidad, que usa ASP.NET Core identidad y IdentityServer.
- Un microservicio de catálogo, que es un servicio de creación, lectura, actualización y eliminación (CRUD) controlado por datos que usa una SQL Server base de datos mediante EntityFramework Core.
- Un microservicio de pedidos, que es un servicio controlado por dominio que utiliza patrones de diseño controlados por dominio.
- Un microservicio de cesta, que es un servicio CRUD controlado por datos que usa Redis Cache.

Estos servicios de back-end se implementan como microservicios mediante ASP.NET Core MVC y se implementan como contenedores únicos en un solo host de Docker. Colectivamente, estos servicios de back-end se conocen como la aplicación de referencia eShopOnContainers. Las aplicaciones cliente se comunican con los servicios back-end a través de una interfaz Web de transferencia de estado representacional (REST). Para más información sobre los microservicios y Docker, consulte [microservicios en contenedores](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obtener información sobre la implementación de los servicios back-end, consulte [microservicios de .net: arquitectura para aplicaciones .net en contenedor](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Aplicación móvil

Esta guía se centra en la creación de aplicaciones empresariales multiplataforma con Xamarin.Forms y usa la aplicación móvil eShopOnContainers como ejemplo. En la figura 1-2 se muestran las páginas de la aplicación móvil eShopOnContainers que proporcionan la funcionalidad descrita anteriormente.

[![](introduction-images/screenshots.png "The eShopOnContainers mobile app")](introduction-images/screenshots-large.png#lightbox "The eShopOnContainers mobile app")

**Figura 1-2**: la aplicación móvil eShopOnContainers

La aplicación móvil consume los servicios back-end proporcionados por la aplicación de referencia eShopOnContainers. Sin embargo, se puede configurar para consumir datos de servicios ficticios para aquellos que deseen evitar la implementación de los servicios back-end.

La aplicación móvil eShopOnContainers ejercita la siguiente Xamarin.Forms funcionalidad:

- XAML
- Controles
- Enlaces
- Convertidores
- Estilos
- Animaciones
- Comandos:
- comportamientos
- Desencadenadores
- Efectos
- Representadores personalizados
- MessagingCenter
- Controles personalizados

Para obtener más información acerca de esta funcionalidad, consulte la [ Xamarin.Forms documentación](~/xamarin-forms/index.yml)y [creación de Xamarin.Forms Mobile Apps con ](https://aka.ms/xamformsebook).

Además, se proporcionan pruebas unitarias para algunas de las clases de la aplicación móvil eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solución de aplicación móvil

La solución de aplicación móvil eShopOnContainers organiza el código fuente y otros recursos en proyectos. Todos los proyectos utilizan carpetas para organizar el código fuente y otros recursos en categorías. En la tabla siguiente se describen los proyectos que componen la aplicación móvil eShopOnContainers:

|Proyecto|Descripción|
|--- |--- |
|eShopOnContainers. Core|Este proyecto es el proyecto de biblioteca de clases portable (PCL) que contiene el código compartido y la interfaz de usuario compartida.|
|eShopOnContainers. Droid|Este proyecto contiene código específico de Android y es el punto de entrada de la aplicación Android.|
|eShopOnContainers. iOS|Este proyecto contiene código específico de iOS y es el punto de entrada de la aplicación de iOS.|
|eShopOnContainers. UWP|Este proyecto contiene código específico de Plataforma universal de Windows (UWP) y es el punto de entrada de la aplicación de Windows.|
|eShopOnContainers. TestRunner. Droid|Este proyecto es el ejecutor de pruebas de Android para el proyecto eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. iOS|Este proyecto es el ejecutor de pruebas de iOS para el proyecto eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. Windows|Este proyecto es el ejecutor de pruebas Plataforma universal de Windows del proyecto eShopOnContainers. UnitTests.|
|eShopOnContainers. UnitTests|Este proyecto contiene pruebas unitarias para el proyecto eShopOnContainers. Core.|

Las clases de la aplicación móvil eShopOnContainers se pueden volver a usar en cualquier Xamarin.Forms aplicación con poca o ninguna modificación.

##### <a name="eshoponcontainerscore-project"></a>Proyecto eShopOnContainers. Core

El proyecto de PCL eShopOnContainers. Core contiene las siguientes carpetas:

|Carpeta|Descripción|
|--- |--- |
|Animaciones|Contiene clases que permiten usar animaciones en XAML.|
|comportamientos|Contiene los comportamientos que se exponen a las clases de vista.|
|Controles|Contiene controles personalizados usados por la aplicación.|
|Convertidores|Contiene los convertidores de valores que aplican la lógica personalizada a un enlace.|
|Efectos|Contiene la `EntryLineColorEffect` clase, que se usa para cambiar el color del borde de `Entry` controles concretos.|
|Excepciones|Contiene el personalizado `ServiceAuthenticationException` .|
|Extensiones|Contiene métodos de extensión para `VisualElement` las `IEnumerable` clases y.|
|Asistentes|Contiene las clases auxiliares para la aplicación.|
|Modelos|Contiene las clases de modelo de la aplicación.|
|Propiedades|Contiene `AssemblyInfo.cs` un archivo de metadatos de ensamblado .net.|
|Servicios|Contiene interfaces y clases que implementan los servicios que se proporcionan a la aplicación.|
|Desencadenadores|Contiene el `BeginAnimation` desencadenador, que se usa para invocar una animación en XAML.|
|Validations (Validaciones)|Contiene clases implicadas en la validación de la entrada de datos.|
|ViewModels|Contiene la lógica de la aplicación que se expone a las páginas.|
|Vistas|Contiene las páginas de la aplicación.|

##### <a name="platform-projects"></a>Proyectos de plataforma

Los proyectos de plataforma contienen implementaciones de efectos, implementaciones de representadores personalizados y otros recursos específicos de la plataforma.

## <a name="summary"></a>Resumen

Las plataformas y herramientas de desarrollo de aplicaciones móviles multiplataforma de Xamarin proporcionan una solución completa para las aplicaciones de cliente móvil B2E, B2B y B2C, lo que proporciona la capacidad de compartir código en todas las plataformas de destino (iOS, Android y Windows) y contribuye a reducir el costo total de propiedad. Las aplicaciones pueden compartir su interfaz de usuario y el código de lógica de la aplicación, a la vez que conservan la apariencia y el funcionamiento de la plataforma nativa.

Los desarrolladores de aplicaciones empresariales se enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se pueda modificar o ampliar con el tiempo. El diseño para esta adaptación puede ser difícil, pero normalmente implica la creación de particiones de una aplicación en componentes discretos de acoplamiento flexible que se pueden integrar fácilmente en una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
