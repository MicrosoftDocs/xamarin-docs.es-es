---
title: ¿Por qué no funciona el diseñador XAML de Visual Studio para Xamarin.Forms los archivos XAML?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dfc17ecabbf0dc079c3a8096d33bc1a6cfb3eed7
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602728"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>¿Por qué no funciona el diseñador XAML de Visual Studio para Xamarin.Forms los archivos XAML?

Xamarin.Forms no admite actualmente los diseñadores visuales para los archivos XAML. Por este motivo, al intentar abrir un archivo XAML de formularios en el diseñador de la *interfaz* de usuario XAML de Visual Studio o en el diseñador de la *interfaz de usuario XAML con codificación*, se produce el siguiente mensaje de error:

> "El archivo no se puede abrir con el editor seleccionado. Elija otro editor ".

Esta limitación se describe en la guía de [ Xamarin.Forms conceptos básicos de XAML](~/xamarin-forms/xaml/xaml-basics/index.md) :

> "No hay ningún diseñador visual para generar XAML en Xamarin.Forms aplicaciones, por lo que todos los XAML deben estar escritos a mano".

Sin embargo, el Xamarin.Forms editor XAML se puede mostrar si se selecciona la opción de menú **Ver > otro Xamarin.Forms editor de Windows >** .
