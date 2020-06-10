---
title: "patrones de aplicaciones empresariales que usan Xamarin.Forms libro electrónico" Descripción: "este libro electrónico proporciona instrucciones arquitectónicas para el desarrollo de aplicaciones empresariales adaptables, fáciles de mantener y comprobables Xamarin.Forms ".
MS. Prod: Xamarin ms. AssetID: 28cfed6c-6175-4223-a8cc-798d40bf0832 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/07/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Patrones de aplicación empresarial con el libro electrónico de Xamarin.Forms

_Instrucciones arquitectónicas para desarrollar aplicaciones empresariales adaptables, mantenibles y comprobables Xamarin.Forms_

![](images/cover-sml.png "Enterprise Application Patterns using Xamarin.Forms eBook")

En este libro electrónico se proporcionan instrucciones sobre cómo implementar el patrón Model-View-ViewModel (MVVM), la inserción de dependencias, la navegación, la validación y la administración de configuración, a la vez que se mantiene el acoplamiento flexible. Además, también hay instrucciones sobre cómo realizar la autenticación y la autorización con IdentityServer, el acceso a los datos de microservicios en contenedor y las pruebas unitarias.

## <a name="preface"></a>[Prefacio](preface.md)

En este capítulo se explica el propósito y el ámbito de la guía y a quién se destina.

## <a name="introduction"></a>[Introducción](introduction.md)

Los desarrolladores de aplicaciones empresariales se enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se pueda modificar o ampliar con el tiempo. El diseño para esta adaptación puede ser difícil, pero normalmente implica la creación de particiones de una aplicación en componentes discretos de acoplamiento flexible que se pueden integrar fácilmente en una aplicación.

## <a name="mvvm"></a>[MVVM](mvvm.md)

El patrón Model-View-ViewModel (MVVM) ayuda a separar sin problemas la lógica empresarial y de presentación de una aplicación desde su interfaz de usuario (UI). Mantener una separación limpia entre la lógica de la aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que una aplicación sea más fácil de probar, mantener y desarrollar. También puede mejorar considerablemente las oportunidades de reutilización del código y permite a los desarrolladores y diseñadores de la interfaz de usuario colaborar con mayor facilidad al desarrollar sus partes respectivas de una aplicación.

## <a name="dependency-injection"></a>[Inserción de dependencias](dependency-injection.md)

La inserción de dependencias permite desacoplar tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

Los contenedores de inserción de dependencias reducen el acoplamiento entre objetos proporcionando una utilidad para crear instancias de la clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que el objeto requiere en él. Si estas dependencias no se han creado todavía, el contenedor crea y resuelve primero sus dependencias.

## <a name="communicating-between-loosely-coupled-components"></a>[Comunicación entre componentes débilmente acoplados](communicating-between-loosely-coupled-components.md)

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. Este mecanismo permite a los publicadores y suscriptores comunicarse sin tener una referencia mutua, lo que ayuda a reducir las dependencias entre los componentes, a la vez que permite que los componentes se desarrollen y prueben de forma independiente.

## <a name="navigation"></a>[Navegación](navigation.md)

Xamarin.Formsincluye compatibilidad para la navegación de páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario, o de la propia aplicación, como resultado de los cambios internos en el estado controlado por lógica. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón MVVM.

En este capítulo se presenta una `NavigationService` clase, que se usa para realizar la navegación del modelo de vista primero desde los modelos de vista. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas automatizadas. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios.

## <a name="validation"></a>[Validación](validation.md)

Cualquier aplicación que acepte la entrada de los usuarios debe asegurarse de que la entrada es válida. Sin validación, un usuario puede proporcionar datos que provocan un error en la aplicación. La validación aplica las reglas de negocios y evita que un atacante Inserte datos malintencionados.

En el contexto del patrón Model-View-ViewModel (MVVM), a menudo se necesitará un modelo de vista o modelo para realizar la validación de datos y señalar los errores de validación en la vista para que el usuario pueda corregirlos.

## <a name="configuration-management"></a>[Administración de configuración](configuration-management.md)

La configuración permite la separación de los datos que configura el comportamiento de una aplicación desde el código, lo que permite cambiar el comportamiento sin volver a compilar la aplicación. La configuración de la aplicación son los datos que una aplicación crea y administra, y la configuración de usuario es la configuración personalizable de una aplicación que afecta al comportamiento de la aplicación y no requiere un reajuste frecuente.

## <a name="containerized-microservices"></a>[Microservicios en contenedores](containerized-microservices.md)

Los microservicios ofrecen un enfoque para el desarrollo y la implementación de aplicaciones que se adapta a los requisitos de agilidad, escala y confiabilidad de las aplicaciones en la nube modernas. Una de las principales ventajas de los microservicios es que se pueden escalar horizontalmente de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más capacidad de procesamiento o ancho de banda de red para admitir la demanda, sin necesidad de escalar de forma innecesaria las áreas de la aplicación que no experimentan una mayor demanda.

## <a name="authentication-and-authorization"></a>[Autenticación y autorización](authentication-and-authorization.md)

Hay muchos enfoques para integrar la autenticación y la autorización en una Xamarin.Forms aplicación que se comunica con una aplicación web MVC de ASP.net. Aquí, la autenticación y la autorización se realizan con un microservicio de identidad en contenedor que usa IdentityServer 4. IdentityServer es un marco de OpenID Connect y OAuth 2,0 de código abierto para ASP.NET Core que se integra con ASP.NET Core identidad para realizar la autenticación de token de portador.

## <a name="accessing-remote-data"></a>[Acceso a datos remotos](accessing-remote-data.md)

Muchas soluciones modernas basadas en Web hacen uso de servicios Web, hospedados por servidores Web, para proporcionar funcionalidad a las aplicaciones cliente remotas. Las operaciones que expone un servicio web constituyen una API Web y las aplicaciones cliente deben ser capaces de usar la API Web sin saber cómo se implementan los datos o las operaciones que expone la API.

## <a name="unit-testing"></a>[Pruebas unitarias](unit-testing.md)

Probar modelos y ver modelos de aplicaciones MVVM es idéntico a probar cualquier otra clase y se pueden usar las mismas herramientas y técnicas. Sin embargo, hay algunos patrones que son típicos para modelar y ver las clases de modelo, que pueden beneficiarse de las técnicas de prueba unitaria específicas.

## <a name="feedback"></a>Comentarios

Este proyecto tiene un sitio de la comunidad, en el que puede publicar preguntas y proporcionar comentarios. El sitio de la comunidad se encuentra en [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). También se puede enviar un correo electrónico a los comentarios sobre el libro electrónico [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com) .

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
