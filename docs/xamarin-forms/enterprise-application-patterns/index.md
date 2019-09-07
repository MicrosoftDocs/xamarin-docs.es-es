---
title: Patrones de aplicaciones empresariales con Xamarin. Forms eBook
description: En este libro electrónico se proporcionan instrucciones arquitectónicas para desarrollar aplicaciones empresariales de Xamarin. Forms adaptables y fáciles de mantener.
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a26fdd931f539da990e21166eec361fd1702de9c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760213"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Patrones de aplicaciones empresariales con Xamarin. Forms eBook

_Instrucciones arquitectónicas para desarrollar aplicaciones empresariales de Xamarin. Forms adaptables, mantenibles y comprobables_

![](images/cover-sml.png "Patrones de aplicaciones empresariales con Xamarin. Forms eBook")

En este libro electrónico se proporcionan instrucciones sobre cómo implementar el patrón Model-View-ViewModel (MVVM), la inserción de dependencias, la navegación, la validación y la administración de configuración, a la vez que se mantiene el acoplamiento flexible. Además, también hay instrucciones sobre cómo realizar la autenticación y la autorización con IdentityServer, el acceso a los datos de microservicios en contenedor y las pruebas unitarias.

## <a name="prefaceprefacemd"></a>[Prefacio](preface.md)

En este capítulo se explica el propósito y el ámbito de la guía, y a quién está destinada.

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Los desarrolladores de aplicaciones empresariales se enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se pueda modificar o extender con el tiempo. El diseño para esta adaptación puede ser difícil, pero normalmente implica la creación de particiones de una aplicación en componentes discretos de acoplamiento flexible que se pueden integrar fácilmente en una aplicación.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

El patrón Modelo-Vista-Modelo de vista (MVVM) ayuda a separar la lógica de negocios y presentación de una aplicación de su interfaz de usuario. Mantener una separación clara entre la lógica de aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que probar una aplicación, mantenerla y desarrollarla sea más fácil. También puede mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario colaborar con mayor facilidad al desarrollar sus respectivos elementos de una aplicación.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserción de dependencias](dependency-injection.md)

La inserción de dependencias permite desacoplar tipos concretos del código que depende de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y asignaciones entre las interfaces y los tipos abstractos, y los tipos concretos que implementan o amplían estos tipos.

Los contenedores de inserción de dependencias reducen el acoplamiento entre objetos proporcionando una utilidad para crear instancias de la clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que el objeto requiere en él. Si estas dependencias no se han creado todavía, el contenedor crea y resuelve primero sus dependencias.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicación entre componentes débilmente acoplados](communicating-between-loosely-coupled-components.md)

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. Este mecanismo permite a los publicadores y suscriptores comunicarse sin tener una referencia mutua, lo que ayuda a reducir las dependencias entre los componentes, a la vez que permite que los componentes se desarrollen y prueben de forma independiente.

## <a name="navigationnavigationmd"></a>[Navegación](navigation.md)

Xamarin. Forms incluye compatibilidad con la navegación de páginas, que suele ser el resultado de la interacción del usuario con la interfaz de usuario o de la propia aplicación, como resultado de los cambios de estado de la lógica interna controlada. Sin embargo, la navegación puede ser compleja de implementar en aplicaciones que usan el patrón MVVM.

En este capítulo se `NavigationService` presenta una clase, que se usa para realizar la navegación del modelo de vista primero desde los modelos de vista. Colocar la lógica de navegación en las clases del modelo de vista significa que la lógica se puede ejecutar a través de pruebas automatizadas. Además, el modelo de vista puede implementar la lógica para controlar la navegación para asegurarse de que se aplican ciertas reglas de negocios.

## <a name="validationvalidationmd"></a>[Validación](validation.md)

Cualquier aplicación que acepte entradas de los usuarios debe asegurarse de que la entrada sea válida. Sin validación, un usuario puede proporcionar datos que hagan que la aplicación produzca un error. La validación aplica reglas de negocio e impide que un atacante inserte datos malintencionados.

En el contexto del patrón Model-View-ViewModel (MVVM), a menudo se necesitará un modelo de vista o modelo para realizar la validación de datos y señalar los errores de validación en la vista para que el usuario pueda corregirlos.

## <a name="configuration-managementconfiguration-managementmd"></a>[Administración de configuraciones](configuration-management.md)

La configuración permite la separación de datos que configuran el comportamiento de una aplicación desde el código, lo que permite cambiar el comportamiento sin volver a compilar la aplicación. La configuración de la aplicación se compone de datos que una aplicación crea y administra, y la configuración de usuario es la configuración personalizable de una aplicación que afecta al comportamiento de la aplicación y no es necesario volver a ajustarla a menudo.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservicios en contenedores](containerized-microservices.md)

Los microservicios ofrecen un enfoque para el desarrollo y la implementación de aplicaciones que se adapta a los requisitos de agilidad, escala y confiabilidad de las aplicaciones en la nube modernas. Una de las principales ventajas de los microservicios es que se pueden escalar horizontalmente de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más capacidad de procesamiento o ancho de banda de red para admitir la demanda, sin necesidad de escalar de forma innecesaria áreas de la aplicación que no está experimentando una mayor demanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticación y autorización](authentication-and-authorization.md)

Hay muchos enfoques para integrar la autenticación y la autorización en una aplicación de Xamarin. Forms que se comunica con una aplicación web MVC de ASP.NET. Aquí, la autenticación y la autorización se realizan con un microservicio de identidad en contenedor que usa IdentityServer 4. IdentityServer es un marco de OpenID Connect y OAuth 2,0 de código abierto para ASP.NET Core que se integra con ASP.NET Core identidad para realizar la autenticación de token de portador.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Acceso a datos remotos](accessing-remote-data.md)

Muchas soluciones modernas basadas en Web hacen uso de servicios Web, hospedados por servidores Web, para proporcionar funcionalidad a las aplicaciones cliente remotas. Las operaciones que expone un servicio web constituyen una API Web y las aplicaciones cliente deben ser capaces de usar la API Web sin saber cómo se implementan los datos o las operaciones que expone la API.

## <a name="unit-testingunit-testingmd"></a>[Pruebas unitarias](unit-testing.md)

Probar modelos y ver modelos de aplicaciones MVVM es idéntico a probar cualquier otra clase y se pueden usar las mismas herramientas y técnicas. Sin embargo, hay algunos patrones que son típicos para modelar y ver las clases de modelo, que pueden beneficiarse de las técnicas de prueba unitaria específicas.

## <a name="feedback"></a>Comentarios

Este proyecto tiene un sitio de la comunidad, en el que puede publicar preguntas y proporcionar comentarios. El sitio de la comunidad se encuentra en [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). También se puede enviar un correo electrónico a [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)los comentarios sobre el libro electrónico.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
