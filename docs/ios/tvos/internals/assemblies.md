---
title: Ensamblados compatibles con Xamarin para tvOS
description: Para ayudar a aclarar las características disponibles para las aplicaciones de tvOS, en este documento se proporciona una lista de ensamblados compatibles con Xamarin para el desarrollo de tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 138b65d51c7069f04433f5b4aee6f8d2b743ccdb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435023"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Ensamblados compatibles con Xamarin para tvOS

## <a name="supported-assemblies"></a>Ensamblados admitidos

Esta es una lista de los ensamblados admitidos por Xamarin para las aplicaciones de Xamarin. tvOS. A continuación se muestra la lista detallada.  Algunas omisiones destacadas incluyen `System.EnterpriseServices` , la pila ASP.net y Windows. Forms.

|Ensamblado|Adición|Compatibilidad de API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1,0|Para escritores de compiladores.|
|Mono.Data.Sqlite.dll|1.2|Proveedor ADO.NET para SQLite; vea [limitaciones](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Compatibilidad con el protocolo TDS; se usa para la compatibilidad con [System. Data. SqlClient](xref:System.Data.SqlClient) dentro de [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1,0|API criptográficas.|
|monotouch.dll|1,0|Este ensamblado contiene el [enlace de C# a la API de CocoaTouch](/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1,0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|OpenTK.dll|1,0|Las API orientadas a objetos abiertos y OpenGL, [ampliadas para proporcionar compatibilidad con dispositivos iPhone](xref:OpenGLES).|
|System.dll|1,0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), además de los tipos de los siguientes espacios de nombres: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System. ComponentModel. Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System .net. cache</li> <li>System.Net.Mail</li> <li>System .net. MIME</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System. Timers</li></ul>|
|System.Core.dll|1,0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Data.dll|1.2|[.Net 3,5](/previous-versions/ms229335(v=vs.100)), [con alguna funcionalidad eliminada](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Cliente oData completo.|
|System.Drawing|1,0|System. Drawing API: solo Classic API.<br />_System. Drawing no se admite en el Unified API para los marcos de trabajo de Xamarin. Mac .NET 4,5 o Mobile._|
|System.Json.dll|1.1|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Runtime.Serialization.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.ServiceModel.dll|1.1|Pila de [WCF](../../../cross-platform/data-cloud/web-services/index.md) tal como está presente en [Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.ServiceModel.Web.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), además de los tipos de los siguientes espacios de nombres: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3,5](/previous-versions/ms229335(v=vs.100)); parte de la compatibilidad con [System. Data](../../data-cloud/system.data.md) .|
|System.Web.Services|1.1|[Servicios web básicos](../../../cross-platform/data-cloud/web-services/index.md) del perfil de .net 3,5, con las características de servidor quitadas.|
|System.Xml.dll|1,0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|
|System.Xml.Linq.dll|1,0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|

<a name="Summary"></a>

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

Además de los enlaces Mac, Xamarin. tvOS puede usar las [bibliotecas de clases portables de .net](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Vínculos relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)