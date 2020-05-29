---
title: Aplicar estilos a las Xamarin.Forms aplicaciones mediante estilos XAML
description: En esta guía se explica cómo personalizar la apariencia de una aplicación mediante el Xamarin.Forms uso de estilos XAML.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 72effe15d3456b5a48cbf5d09e889600134ac686
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138806"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Aplicar estilos a las Xamarin.Forms aplicaciones mediante estilos XAML

## <a name="introduction"></a>[Introducción](introduction.md)

Xamarin.Formslas aplicaciones suelen contener varios controles que tienen un aspecto idéntico. Establecer la apariencia de cada control individual puede ser repetitivo y propenso a errores. En su lugar, se pueden crear estilos que personalicen la apariencia del control agrupando y estableciendo las propiedades disponibles en el tipo de control.

## <a name="explicit-styles"></a>[Estilos explícitos](explicit.md)

Un estilo *explícito* es aquel que se aplica de forma selectiva a los controles estableciendo sus [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedades.

## <a name="implicit-styles"></a>[Estilos implícitos](implicit.md)

Un estilo *implícito* es aquél que usan todos los controles del mismo [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , sin necesidad de que cada control haga referencia al estilo.

## <a name="global-styles"></a>[Estilos globales](application.md)

Los estilos se pueden poner a disposición globalmente agregándolos a la de la aplicación [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Esto ayuda a evitar la duplicación de estilos en páginas o controles.

## <a name="style-inheritance"></a>[Herencia de estilo](inheritance.md)

Los estilos pueden heredar de otros estilos para reducir la duplicación y habilitar la reutilización.

## <a name="dynamic-styles"></a>[Estilos dinámicos](dynamic.md)

Los estilos no responden a los cambios de propiedad y permanecen sin cambios durante la ejecución de una aplicación. Sin embargo, las aplicaciones pueden responder dinámicamente a los cambios de estilo en tiempo de ejecución mediante recursos dinámicos.

## <a name="device-styles"></a>[Estilos de dispositivo](device.md)

Xamarin.Formsincluye seis estilos *dinámicos* , conocidos como estilos de *dispositivo* , en la [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) clase. Los seis estilos solo se pueden aplicar a [`Label`](xref:Xamarin.Forms.Label) instancias de.

## <a name="style-classes"></a>[Clases de estilo](style-class.md)

Xamarin.Formslas clases de estilo permiten aplicar varios estilos a un control, sin tener que recurrir a la herencia de estilo.
