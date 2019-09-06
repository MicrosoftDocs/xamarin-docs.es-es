---
title: Información general sobre la arquitectura
description: En este documento se describe la arquitectura de Xamarin Workbooks, examinando cómo funcionan conjuntamente el agente interactivo y el cliente interactivo.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7129d0bedddb272ef87e3d209cb05c2ca0c0acf4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285280"
---
# <a name="architecture-overview"></a>Información general sobre la arquitectura

Xamarin Workbooks incluye dos componentes principales que deben funcionar conjuntamente entre sí: _Agente_ y _cliente_.

## <a name="interactive-agent"></a>Agente interactivo

El componente del agente es un ensamblado específico de la plataforma pequeño que se ejecuta en el contexto de una aplicación .NET.

Xamarin Workbooks proporciona aplicaciones "vacías" predefinidas para varias plataformas, como iOS, Android, Mac y WPF. Estas aplicaciones hospedan explícitamente el agente.

Durante la inspección en vivo (Xamarin Inspector), el agente se inserta a través del depurador del IDE en una aplicación existente como parte del flujo de trabajo de depuración de & de desarrollo normal.

## <a name="interactive-client"></a>Cliente interactivo

El cliente es un shell nativo (cacao en Mac, WPF en Windows) que hospeda una superficie de explorador Web para presentar la interfaz Workbook/REPL. Desde la perspectiva del SDK, todas las integraciones de cliente se implementan en JavaScript y CSS.

El cliente es responsable (a través de Roslyn) de compilar el código fuente en ensamblados pequeños y enviarlos al agente conectado para su ejecución. Los resultados de la ejecución se devuelven al cliente para su representación. Cada celda de un libro produce un ensamblado que hace referencia al ensamblado de la celda anterior.

Dado que un agente puede ejecutarse en cualquier tipo de plataforma .NET y tiene acceso a cualquier elemento de la aplicación en ejecución, se debe tener cuidado para serializar los resultados de una manera independiente de la plataforma.
