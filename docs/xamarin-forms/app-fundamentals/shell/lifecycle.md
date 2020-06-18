---
title: 'title: "Xamarin.Forms: Ciclo de vida del shell" description: "Las aplicaciones del shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento Appearing si una página está a punto de aparecer en la pantalla y un evento Disappearing si una página está a punto de desaparecer de la pantalla."'
description: 'ms.prod: xamarin ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 07/25/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a7a46187d861098b61f638a3fb460d890b081dd
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84138728"
---
# <a name="xamarinforms-shell-lifecycle"></a>Ciclo de vida de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Las aplicaciones del shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento `Appearing` si una página está a punto de aparecer en la pantalla y se genera un evento `Disappearing` si una página está a punto de desaparecer de la pantalla. Estos eventos se propagan a las páginas y se pueden controlar si se invalidan los métodos [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) o [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) en la página.

> [!NOTE]
> En una aplicación del shell, los eventos `Appearing` y `Disappearing` se generan desde código multiplataforma, antes de que el código de plataforma haga que una página sea visible o quite una página de la pantalla.

Para obtener más información sobre el ciclo de vida de la aplicación de Xamarin.Forms, consulte [Ciclo de vida de la aplicación de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navegación jerárquica

En una aplicación del shell, al insertar una página en la pila de navegación, se obtendrá el objeto `ShellContent` visible actualmente y el contenido de la página, lo que provocará el evento `Disappearing`. De forma similar, al sacar la última página de la pila de navegación, se obtendrá el objeto `ShellContent` recién visible y el contenido de la página, lo que provocará el evento `Appearing`.

Para obtener más información sobre la navegación jerárquica, consulte [Navegación jerárquica de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navegación modal

En una aplicación del shell, al insertar una página modal en la pila de navegación modal, todos los objetos del shell estarán visibles y se provocará el evento `Disappearing`. De forma similar, al sacar la última página modal de la pila de navegación modal, todos los objetos del shell estarán visibles y se provocará el evento `Appearing`.

Para obtener más información sobre la navegación modal, consulte [Páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Ciclo de vida de la aplicación de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
