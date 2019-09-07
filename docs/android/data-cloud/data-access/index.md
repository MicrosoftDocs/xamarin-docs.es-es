---
title: Acceso a datos de Xamarin. Android
description: La mayoría de las aplicaciones tienen algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos sea trivialmente pequeña, normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso a la base de datos.  Android tiene el motor de base de datos SQLite ' integrado ' y el acceso para almacenar y recuperar datos se simplifica con la plataforma de Xamarin. En este documento se muestra cómo obtener acceso a una base de datos de SQLite de una manera multiplataforma.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2343603199661ea39b1f0af172ce0ccf48a2cd66
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754582"
---
# <a name="xamarinandroid-data-access"></a>Acceso a datos de Xamarin. Android

_La mayoría de las aplicaciones tienen algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos sea trivialmente pequeña, normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso a la base de datos.  Android tiene el motor de base de datos SQLite ' integrado ' y el acceso para almacenar y recuperar datos se simplifica con la plataforma de Xamarin. En este documento se muestra cómo obtener acceso a una base de datos de SQLite de una manera multiplataforma._

## <a name="data-access-overview"></a>Introducción al acceso a datos

La mayoría de las aplicaciones tienen algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos sea trivialmente pequeña, normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso a la base de datos. Android tiene el motor de base de datos de SQLite "integrado" y el acceso a los datos se simplifica mediante la plataforma de Xamarin, que viene con el proveedor de datos de SQLite.

Xamarin. Android admite las API de acceso a bases de datos como:

- Marco de ADO.NET.
- SQLite: biblioteca de terceros de la red.

La mayoría del código de esta sección es totalmente multiplataforma y se ejecutará en iOS o Android sin modificaciones. Se han explicado dos aplicaciones de ejemplo:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; Las operaciones de datos simples escriben los resultados en un control de presentación de texto;

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; Integra las operaciones de datos en una pequeña aplicación de trabajo que enumera y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de iOS y Android.

En el caso de las aplicaciones de Xamarin. Forms, lea [trabajar con bases de datos](~/xamarin-forms/data-cloud/data/databases.md) , en la que se explica cómo trabajar con SQLite en una biblioteca PCL con Xamarin. Forms.

En los temas de esta sección se describe el acceso a datos en Xamarin. Android con SQLite como el motor de base de datos. Se puede tener acceso a la base de datos "directamente" mediante la sintaxis ADO.NET o se puede incluir el SQLite.NET ORM y realizar C#operaciones de datos en.

Se revisan dos ejemplos: uno que contiene código de acceso a datos muy simple que genera un resultado en un campo de texto y una aplicación sencilla que incluye la funcionalidad de creación, lectura, actualización y eliminación. También se analizan los subprocesos y cómo inicializar la aplicación con una base de datos SQLite rellenada previamente.

Para obtener más ejemplos de acceso a datos multiplataforma, consulte nuestro caso práctico de [pro de tarea](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
