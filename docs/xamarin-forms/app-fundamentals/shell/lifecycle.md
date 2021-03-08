---
title: Ciclo de vida de Xamarin.Forms Shell
description: Las aplicaciones de Shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento Appearing si una página está a punto de aparecer en la pantalla y un evento Disappearing si una página está a punto de desaparecer de la pantalla.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1897f04c4dc1c148fa1963fb2620aad33b38e524
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757525"
---
# <a name="xamarinforms-shell-lifecycle"></a>Ciclo de vida de Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Las aplicaciones de Shell respetan el ciclo de vida de Xamarin.Forms; se genera un evento [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing) si una página está a punto de aparecer en la pantalla y un evento [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing) si una página está a punto de desaparecer de la pantalla. Estos eventos se propagan a las páginas y se pueden controlar si se invalidan los métodos [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) o [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) en la página.

> [!NOTE]
> En una aplicación del shell, los eventos `Appearing` y `Disappearing` se generan desde código multiplataforma, antes de que el código de plataforma haga que una página sea visible o quite una página de la pantalla.

Para obtener más información sobre el ciclo de vida de las aplicaciones de Xamarin.Forms, consulte [Ciclo de vida de la aplicación de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navegación jerárquica

En una aplicación de Shell, al insertar una página en la pila de navegación, se obtendrá el objeto [`ShellContent`](xref:Xamarin.Forms.ShellContent) visible actualmente y el contenido de la página, lo que provocará el evento [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing). De forma similar, al sacar la última página de la pila de navegación, se obtendrá el objeto `ShellContent` recién visible y el contenido de la página, lo que provocará el evento [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing).

Para obtener más información sobre la navegación jerárquica, consulte [Navegación jerárquica de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navegación modal

En una aplicación de Shell, al insertar una página modal en la pila de navegación modal, todos los objetos de Shell estarán visibles y se provocará el evento [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing). De forma similar, al sacar la última página modal de la pila de navegación modal, todos los objetos de Shell estarán visibles y se provocará el evento [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing).

Para obtener más información sobre la navegación modal, consulte [Páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Ciclo de vida de las aplicaciones de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
