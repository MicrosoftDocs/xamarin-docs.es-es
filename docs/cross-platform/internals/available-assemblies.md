---
title: Ensamblados disponibles
description: En este documento se enumeran los ensamblados disponibles para su uso en Xamarin. iOS, Xamarin. Android y Xamarin. Mac. También se incluyen vínculos a documentación sobre bibliotecas de .NET Standard y bibliotecas de clases portables.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 31066d09b1e753dd054a6a908b626ca3edee008e
ms.sourcegitcommit: 04929b5ff4384ca807727bec7c0467111a7eb283
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867613"
---
# <a name="available-assemblies"></a>Ensamblados disponibles

Xamarin. iOS, Xamarin. Android y Xamarin. Mac se distribuyen con más de una docena de ensamblados. Del mismo modo que Silverlight es un subconjunto extendido de los ensamblados .NET de escritorio, las plataformas Xamarin también son un subconjunto extendido de varios ensamblados .NET de escritorio y Silverlight.

Las plataformas Xamarin no son compatibles con ABI con ensamblados existentes compilados para un perfil diferente. Debe volver a compilar el código fuente para generar los ensamblados que tengan como destino el perfil correcto (de la misma forma que necesita volver a compilar el código fuente para Silverlight y .NET 3,5 por separado).

Las aplicaciones de Xamarin. Mac se pueden compilar en tres modos: uno que usa seleccionada Mobile Profile de Xamarin, el marco de trabajo de Xamarin. Mac .NET 4,5, que permite tener como destino ensamblados de escritorio completos existentes y una no compatible que usa la API de .NET que se encuentra en un sistema mono montaje. Para obtener más información, consulte la documentación de las [plataformas de destino](~/mac/platform/target-framework.md) .

## <a name="net-standard-libraries"></a>Bibliotecas de .NET Standard

Además de los enlaces de iOS, Android y Mac, los proyectos de Xamarin pueden usar [bibliotecas de .net Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de clases portables

Los proyectos de Xamarin también pueden consumir [bibliotecas de clases portables .net](~/cross-platform/app-fundamentals/pcl.md), aunque esta tecnología está en desuso en favor de .net Standard.

## <a name="supported-assemblies"></a>Ensamblados admitidos

Estos son los ensamblados disponibles en el **Administrador de referencias > ensamblados > Framework** (Visual Studio 2017) y **Editar referencias > paquetes** (Visual Studio para Mac) y su compatibilidad con las plataformas de Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Ensamblado|Compatibilidad de API|Xamarin iOS|Xamarin Android|Mac de Xamarin|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |l18N. dll|Incluye CJK, Oriente bajo, otros, poco frecuentes, oeste|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Microsoft.CSharp.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Mono.CSharp.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Mono.Data.Sqlite.dll|Proveedor ADO.NET para SQLite; vea limitaciones.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Mono.Data.Tds.dll|Compatibilidad con el protocolo TDS; se usa para la compatibilidad con [System. Data. SqlClient](xref:System.Data.SqlClient) dentro de [System. Data](xref:System.Data).|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Mono.Dynamic.&#8203;Interpreter.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| | |
> |Mono.Security.dll|API criptográficas.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |monotouch.dll|Este ensamblado contiene C# el enlace a la API de CocoaTouch. Esto solo está disponible en proyectos de iOS clásicos.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| | |
> |MonoTouch.&#8203;Dialog-1.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| | |
> |MonoTouch.&#8203;NUnitLite.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |OpenTK-1.0.dll|Las API orientadas a objetos abiertos y OpenGL, ampliadas para proporcionar compatibilidad con dispositivos iPhone.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los siguientes espacios de nombres:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Data.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx) , con [alguna funcionalidad eliminada](~/ios/data-cloud/system.data.md).|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Data.&#8203;Services.&#8203;Client.dll|Cliente oData completo.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.IO. &#8203;Compresión de| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Net.&#8203;Http.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;Numerics.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;ServiceModel.dll|Pila de WCF tal como está presente en [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), además de los tipos de los siguientes espacios de nombres: <br />System<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;Transactions.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); parte de la compatibilidad con [System. Data](~/ios/data-cloud/system.data.md) .|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Web.&#8203;Services.dll|Servicios web básicos del perfil de .NET 3,5, con las características de servidor quitadas.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;Windows.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.Xml.Serialization.dll| |![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")|![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")|![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Xamarin.iOS.dll|Este ensamblado contiene C# el enlace a la API de CocoaTouch. Solo se utiliza en proyectos de iOS unificados.|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| | |
> |Java.Interop.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |Mono.Android.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |Mono.Android.&#8203;Export.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |Mono.Posix.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |System.&#8203;EnterpriseServices.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |![Xamarin. Android compatible](~/media/shared/yes.png "Xamarin. Android compatible")| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Para escritores de compiladores.| | |![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |Xamarin.Mac.dll| | | |![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|
> |System.&#8203;Drawing.dll|System. Drawing no se admite en el Unified API para los marcos de trabajo de Xamarin. Mac, .NET 4,5 o Mobile. Se puede Agregar compatibilidad con System. Drawing a iOS y macOS mediante la biblioteca [sysdrawing-CoreGraphics](https://github.com/mono/sysdrawing-coregraphics)|![Se admite Xamarin. iOS](~/media/shared/yes.png "Se admite Xamarin. iOS")| |![Xamarin. Mac compatible](~/media/shared/yes.png "Xamarin. Mac compatible")|