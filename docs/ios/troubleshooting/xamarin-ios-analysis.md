---
title: Reglas de análisis de Xamarin. iOS
description: En este documento se describe un conjunto de reglas de análisis que comprueban la configuración del proyecto de Xamarin. iOS para ayudar a determinar si hay disponible una configuración optimizada más o mejor.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 4a7a1c5a9fccb14a84e78e5854bcd8507394ce4a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574501"
---
# <a name="xamarinios-analysis-rules"></a>Reglas de análisis de Xamarin. iOS

El análisis de Xamarin. iOS es un conjunto de reglas que comprueban la configuración del proyecto para ayudarle a determinar si la configuración mejor o más optimizada está disponible.

Ejecute las reglas de análisis tantas veces como sea posible para buscar posibles mejoras en el tiempo de desarrollo.

Para ejecutar las reglas, en el menú de Visual Studio para Mac, seleccione **proyecto > ejecutar análisis de código**.

> [!NOTE]
> El análisis de Xamarin. iOS solo se ejecuta en la configuración seleccionada actualmente. Se recomienda ejecutar la herramienta para las configuraciones de depuración **y** lanzamiento.

<a name="XIA0001"></a>

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** El enlazador está deshabilitado en el dispositivo para el modo de depuración.
- **Corrección:** Debe intentar ejecutar el código con el enlazador para evitar sorpresas.
Para configurarlo, vaya a Project > iOS Build > comportamiento del enlazador.

<a name="XIA0002"></a>

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** Apple rechazará las compilaciones de aplicaciones que inicializan el agente de Test Cloud cuando se envíen, ya que usan la API privada.
- **Corrección:** Agregue o corrija los #if necesarios y defina en el código.

<a name="XIA0003"></a>

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** La configuración de depuración que usa claves de firma de desarrollador no debe generar un IPA, ya que solo es necesario para la distribución, que ahora usa el Asistente para publicación.
- **Corrección:** Deshabilite la compilación de IPA en opciones de proyecto para la configuración de depuración.

<a name="XIA0004"></a>

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** La arquitectura admitida para "release | el dispositivo "no es compatible con 64 bits, falta ARM64. Este es un problema ya que Apple no acepta solo aplicaciones iOS de 32 bits en el AppStore.
- **Corrección:** Haga doble clic en el proyecto de iOS, vaya a compilar > iOS build y cambie las arquitecturas admitidas para que tenga ARM64.

<a name="XIA0005"></a>

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** Si no se usa la opción float32 (--AOT-Options =-O = float32), se genera un costo de rendimiento elevado, especialmente en el caso de los dispositivos móviles, donde las matemáticas de precisión doble son más lentas. Tenga en cuenta que .NET usa la precisión doble internamente, incluso para Float, por lo que la habilitación de esta opción afecta a la precisión y, posiblemente, a la compatibilidad.
- **Corrección:** Haga doble clic en el proyecto de iOS, vaya a compilar > compilación de iOS y desactive la casilla "realizar todas las operaciones de punto flotante de 32 bits como punto flotante de 64 bits".

<a name="XIA0006"></a>

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** Se recomienda usar el controlador HttpClient nativo en lugar del administrado para mejorar el rendimiento, el tamaño de los archivos ejecutables más pequeños y la compatibilidad con los estándares más recientes.
- **Corrección:** Haga doble clic en el proyecto de iOS, vaya a compilar > iOS build y cambie la implementación de HttpClient a NSUrlSession (iOS 7 +) o CFNetwork para que sea compatible con la versión anterior a iOS 7.

<a name="XIA0007"></a>

## <a name="xia0007-usellvmrule"></a>XIA0007: UseLLVMRule

- **Problema:** Para la versión | configuración de iPhone se recomienda habilitar el compilador LLVM, que genera código que es más rápido de ejecutarse a costa del tiempo de compilación.
- **Corrección:** Haga doble clic en el proyecto de iOS, vaya a compilar > la compilación de iOS y para la versión | iPhone, active la opción del compilador de optimización de LLVM.
