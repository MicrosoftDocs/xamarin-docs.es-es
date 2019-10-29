---
title: Acceso a datos de Xamarin. iOS
description: En este documento se proporcionan vínculos a guías que describen cómo trabajar con bases de datos locales en una aplicación de Xamarin. iOS. El contenido vinculado describe SQLite.NET, ADO.NET, etc.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 163bd88349ee55af5f518a20364bbce7fe6052b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008438"
---
# <a name="xamarinios-data-access"></a>Acceso a datos de Xamarin. iOS

Xamarin. iOS admite API de acceso a bases de datos como:

- Marco de ADO.NET.
- SQLite: biblioteca de terceros de la red.

En esta guía se proporciona información general de alto nivel de las bases de datos en general antes de describir cómo configurar ADO.NET y SQLite.NET para tener acceso a las bases de datos de SQLite en las aplicaciones de Xamarin. iOS. 

La mayoría del código de este documento es totalmente multiplataforma y se ejecutará en iOS o Android sin modificaciones. Se han explicado dos aplicaciones de ejemplo:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : las operaciones de datos simples escriben los resultados en un control de presentación de texto;
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) : integra las operaciones de datos en una pequeña aplicación de trabajo que enumera y edita una estructura de datos simple.

Ambas soluciones de ejemplo contienen proyectos de aplicación de ejemplo de iOS y Android.

En el caso de las aplicaciones de Xamarin. Forms, lea [trabajar con bases de datos](~/xamarin-forms/data-cloud/data/databases.md) , en la que se explica cómo trabajar con SQLite en una biblioteca PCL con Xamarin. Forms.

## <a name="sections"></a>Secciones

- [Introducción](introduction.md)
- [Configuración](configuration.md)
- [Uso de SQLite.NET ORM](using-sqlite-orm.md)
- [Uso de ADO.NET](using-adonet.md)
- [Uso de datos en una aplicación](using-data-in-an-app.md)

## <a name="summary"></a>Resumen

En este capítulo se describe el acceso a datos en Xamarin. iOS con SQLite como el motor de base de datos. Se puede tener acceso a la base de datos "directamente" mediante la sintaxis de ADO.NET o se puede incluir el ORM de C#SQLite.net y realizar operaciones de datos en.

Hemos revisado dos ejemplos: uno que contiene código de acceso a datos muy simple que se genera en un campo de texto y una aplicación sencilla que incluye la funcionalidad de creación, lectura, actualización y eliminación. También hemos analizado los subprocesos y cómo inicializar la aplicación con una base de datos SQLite rellenada previamente.

Para obtener más ejemplos de acceso a datos multiplataforma, consulte nuestro caso práctico de [pro de tarea](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Acceso a datos básico (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [Acceso a la configuración avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recetas de datos de iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
