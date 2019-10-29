---
title: Selector de implementación de HttpClient y SSL/TLS para iOS/macOS
description: La pila HttpClient y el selector de implementación de SSL/TLS determinan la implementación de HttpClient y SSL/TLS que usará la aplicación de Xamarin iOS, tvOS o macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 3b87eb6ae9e97be4ee34b092efce72c0d97c88b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015850"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Selector de implementación de HttpClient y SSL/TLS para iOS/macOS

El **selector de implementación de HttpClient** para Xamarin. iOS, Xamarin. TvOS y Xamarin. Mac controla qué `HttpClient` implementación se va a usar. Puede cambiar a una implementación que usa transportes nativos de iOS, tvOS o macOS (`NSUrlSession` o `CFNetwork`, según el sistema operativo). La parte superior es la compatibilidad con TLS 1,2, binarios más pequeños y descargas más rápidas. el inconveniente es que requiere que se ejecute el bucle de eventos para que se ejecuten las operaciones asincrónicas.

Los proyectos deben hacer referencia al ensamblado **System .net. http** .

> [!WARNING]
> **Abril de 2018** : debido a los mayores requisitos de seguridad, incluido el cumplimiento de PCI, los principales proveedores en la nube y los servidores Web se espera que dejen de admitir versiones de TLS anteriores a 1,2. Los proyectos de Xamarin creados en versiones anteriores de Visual Studio usan de forma predeterminada versiones anteriores de TLS.
>
> Para asegurarse de que las aplicaciones continúan funcionando con estos servidores y servicios, **debe actualizar los proyectos de Xamarin con la configuración `NSUrlSession` que se muestra a continuación y volver a compilar y volver a implementar las aplicaciones** para los usuarios.

## <a name="selecting-an-httpclient-stack"></a>Selección de una pila HttpClient

Para ajustar el `HttpClient` que utiliza la aplicación:

1. Haga doble clic en el **nombre del proyecto** en el **Explorador de soluciones** para abrir las opciones del proyecto.
2. Cambie a la configuración de **compilación** del proyecto (por ejemplo, la **compilación de iOS** para una aplicación de Xamarin. iOS).
3. En la lista desplegable **implementación de HttpClient** , seleccione el `HttpClient` tipo como uno de los siguientes: **NSUrlSession** (recomendado), **CFNetwork**o **Managed**.

[![elegir la implementación de HttpClient desde Managed, CFNetwork o NSUrlSession](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Para la compatibilidad con TLS 1,2, se recomienda la opción `NSUrlSession`.

### <a name="nsurlsession"></a>NSUrlSession

El controlador basado en `NSURLSession`se basa en el marco de `NSURLSession` nativo disponible en iOS 7 y versiones más recientes. 
**Esta es la configuración recomendada.**

#### <a name="pros"></a>Profesionales ti

- Usa las API nativas para mejorar el rendimiento y el tamaño de los archivos ejecutables más pequeños.
- Compatibilidad con los estándares más recientes, como TLS 1,2.

#### <a name="cons"></a>Iconos

- Requiere iOS 7 o posterior.
- Algunas características o opciones de `HttpClient` no están disponibles.

### <a name="cfnetwork"></a>CFNetwork

El controlador basado en `CFNetwork`se basa en el marco de `CFNetwork` nativo disponible en iOS 6 y versiones más recientes.

#### <a name="pros"></a>Profesionales ti

- Usa las API nativas para mejorar el rendimiento y el tamaño de los archivos ejecutables más pequeños.
- Compatibilidad con estándares más recientes, como TLS 1,2.

#### <a name="cons"></a>Iconos

- Requiere iOS 6 o posterior.
- No está disponible en watchos.
- Algunas opciones y características de HttpClient no están disponibles.

### <a name="managed"></a>Administrado

El controlador administrado es el controlador HttpClient totalmente administrado que se incluye con la versión anterior de Xamarin.

#### <a name="pros"></a>Profesionales ti

- Tiene el conjunto de características más compatible con Microsoft .NET y versiones anteriores de Xamarin.

#### <a name="cons"></a>Iconos

- No está totalmente integrado con los sistemas operativos de Apple y está limitado a TLS 1,0. Es posible que no pueda conectarse a servidores Web seguros o a servicios en la nube en el futuro.
- Normalmente es mucho más lento, como el cifrado que las API nativas.
- Requiere más código administrado, con lo que se crea una aplicación más grande distribuible.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Establecer mediante programación el HttpMessageHandler

Además de la configuración de todo el proyecto mostrada anteriormente, también puede crear una instancia de un `HttpClient` e insertar el `HttpMessageHandler` deseado a través del constructor, tal y como se muestra en estos fragmentos de código:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Esto permite usar una `HttpMessageHandler` distinta de la que se declara en el cuadro de diálogo **Opciones del proyecto** .

## <a name="ssltls-implementation"></a>Implementación de SSL/TLS

SSL (capa de sockets seguros) y su sucesor, TLS (seguridad de la capa de transporte), proporcionan compatibilidad con HTTP y otras conexiones de red a través de `System.Net.Security.SslStream`. La implementación de `System.Net.Security.SslStream` de Xamarin. iOS, Xamarin. tvOS o Xamarin. Mac llamará a la implementación nativa de SSL/TLS de Apple en lugar de usar la implementación administrada que proporciona mono. La implementación nativa de Apple admite TLS 1,2.

> [!WARNING]
> La próxima versión de Xamarin.Mac 4.8 solo será compatible con macOS 10.9 o versiones posteriores.
> Las versiones anteriores de Xamarin.Mac son compatibles con macOS 10.7 o versiones posteriores, pero estas versiones anteriores de macOS carecen de infraestructura TLS suficiente para admitir TLS 1.2. Para macOS 10.7 o Mac OS 10.8 de destino, use Xamarin.Mac 4.6 o una versión anterior.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

La _seguridad de transporte de aplicaciones_ (ATS) de Apple aplica conexiones seguras entre recursos de Internet (como el servidor back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de Internet se ajusten a los procedimientos recomendados de conexión segura, lo que evita la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o de una biblioteca que consume.

Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9, tvOS 9 y OS X 10,11 (el Capitan) y versiones más recientes, todas las conexiones que usen `NSUrlConnection`, `CFUrl` o `NSUrlSession` estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

En función de la pila de HttpClient y de las selecciones de implementación de SSL/TLS, es posible que deba realizar modificaciones en la aplicación para que funcione correctamente con ATS.

Para obtener más información sobre ATS, consulte nuestra [Guía de seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problemas conocidos

En esta sección se abordan los problemas conocidos de la compatibilidad con TLS en Xamarin. iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>No se pudo cargar el proyecto: "no se encontró el valor solicitado AppleTLS"

Xamarin. iOS 9,8 presentó algunas nuevas configuraciones contenidas en el archivo **. csproj** para una aplicación de Xamarin. iOS. Estos cambios pueden provocar problemas cuando el proyecto se abre con versiones anteriores de Xamarin. iOS. La captura de pantalla siguiente es un ejemplo del mensaje de error que se puede mostrar en este escenario:

![Captura de pantalla del error al intentar cargar el proyecto. no se encontró el valor solicitado heredado](http-stack-images/tlserror-xs.png)

Este error se debe a la introducción del valor de `MtouchTlsProvider` en el archivo de proyecto de Xamarin. iOS 9,8. Si no es posible actualizar a Xamarin. iOS 9,8 (o posterior), la solución consiste en editar manualmente la aplicación del archivo **. csproj** , quitar el elemento `MtouchTlsprovider` y, a continuación, guardar el archivo de proyecto modificado.

El siguiente fragmento de código es un ejemplo del aspecto que puede tener el valor `MtouchTlsProvider` dentro de un archivo **. csproj** :

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
