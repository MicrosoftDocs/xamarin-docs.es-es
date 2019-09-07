---
title: Plataforma de destino para Xamarin. Mac
description: En este artículo se tratan las plataformas de destino (bibliotecas de clases base) disponibles para Xamarin. Mac y las implicaciones de usarlas en el proyecto de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: a612c2c23ceff13ea1d602465573514547628e55
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769803"
---
# <a name="target-framework-for-xamarinmac"></a>Plataforma de destino para Xamarin. Mac

_En este artículo se tratan las plataformas de destino (bibliotecas de clases base) disponibles para Xamarin. Mac y las implicaciones de usarlas en el proyecto de Xamarin. Mac._

![Opciones de la plataforma de destino para Xamarin. Mac](target-framework-images/select-target.png "Opciones de la plataforma de destino para Xamarin. Mac")

## <a name="background"></a>Fondo

Cada programa o biblioteca .NET depende de la funcionalidad proporcionada por la biblioteca de clases base (BCL). Esta BCL incluye ensamblados como mscorlib, System, System .net. http y System. XML que proporcionan la funcionalidad común integrada en todos los lenguajes de .NET.

A lo largo de los años, ha desarrollado varias versiones diferentes de esta BCL, optimizadas para diferentes casos de uso. La BCL de "escritorio" incluye un conjunto más completo de bibliotecas que podrían ser demasiado pesadas para otros casos de uso, mientras que Mobile se centra en garantizar que las API son seguras para la vinculación, lo que elimina el código no utilizado para reducir la superficie de la aplicación.

Una de las repercusiones más importantes de estas distintas plataformas de destino es que todos los ensamblados de un programa determinado *deben* tener como destino ensamblados BCL compatibles. Si no es así, podría tener dos ensamblados vinculados con versiones diferentes de **System. dll** que no aceptan la firma de un tipo determinado. Una biblioteca compartida puede tener como destino [.net Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que es el subconjunto común de las plataformas de destino o una versión de .NET Framework de destino específica.

Hay tres opciones de plataforma de destino disponibles para Xamarin. Mac, cada una con diferentes ventajas e inconvenientes:

- **Moderno** (denominada Mobile en la documentación anterior): un subconjunto muy similar a lo que impulsa Xamarin. iOS, muy optimizado para el rendimiento y el tamaño. Esta plataforma de destino es segura para el vinculador, por lo que estos proyectos pueden tener su superficie final drásticamente reducida quitando el código no utilizado.

- **Completo** (llamado XM 4,5 en la documentación anterior): un subconjunto muy similar a la BCL de "escritorio", con algunas pequeñas desinstalaciones. Como la plataforma de destino es casi idéntica a net45 (y versiones posteriores), puede consumir fácilmente muchos paquetes Nuget que no proporcionan netstandard2 o compilaciones de Xamarin. Mac específicas. Sin embargo, debido al uso del sistema. Configuration, no es compatible con la vinculación.

- **No compatible** (llamado sistema en la documentación anterior): en lugar de vincular a una BCL proporcionada por Xamarin. Mac, use el sistema actual de mono instalado. Esto proporciona el conjunto más completo de ensamblados, incluidos algunos conocidos como problemáticos (System. Drawing, por ejemplo). Esta opción solo existe en un "último recurso" y se recomienda encarecidamente agotar otras opciones antes de usarla. Como implica el nombre, el uso no es compatible con los canales de soporte técnico oficiales.

## <a name="setting-the-target-framework"></a>Establecer la versión de .NET Framework de destino

Para cambiar al tipo de plataforma de destino de un proyecto de Xamarin. Mac, haga lo siguiente:

1. Abra el proyecto de Xamarin.Mac en Visual Studio para Mac.
2. En el **Explorador de soluciones**, haga doble clic en el archivo de proyecto para abrir el cuadro de diálogo **Opciones de proyecto**.
3. En la pestaña **General** , seleccione el tipo de **plataforma de destino** que se adapta a las necesidades de su aplicación:

    [![Usar la ventana Opciones de proyecto para elegir una versión de .NET Framework de destino](target-framework-images/select-target-full.png "Usar la ventana Opciones de proyecto para elegir una versión de .NET Framework de destino")](target-framework-images/select-target-full-large.png#lightbox)

4. Haga clic en el botón **Aceptar** para guardar los cambios.

Debe **limpiar** y volver a **generar** el proyecto de Xamarin. Mac después de cambiar el tipo de plataforma de destino.

## <a name="summary"></a>Resumen

En este artículo se han explicado brevemente los distintos tipos de marcos de trabajo de destino (bibliotecas de clases base) disponibles para una aplicación de Xamarin. Mac y cuándo debe usarse cada tipo de marco.

## <a name="related-links"></a>Vínculos relacionados

- [uso compartido de código de iOS y Mac](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Ensamblados](~/cross-platform/internals/available-assemblies.md)
- [Actualización de aplicaciones Mac existentes](~/cross-platform/macios/unified/updating-mac-apps.md)
