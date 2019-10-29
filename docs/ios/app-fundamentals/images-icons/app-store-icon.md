---
title: Iconos de App Store en Xamarin. iOS
description: En este documento se describe cómo usar los catálogos de recursos para administrar un icono de App Store para una aplicación de Xamarin. iOS. Anteriormente, los iconos de App Store se administraban con iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/26/2017
ms.openlocfilehash: 62253adafcd028f459e8ca0cc11a8eb3e54d52c3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004186"
---
# <a name="app-store-icons-in-xamarinios"></a>Iconos de App Store en Xamarin. iOS

Antes de que Xcode 9 todos los iconos de App Store se agregaron a través de iTunes Connect. Sin embargo, esto ya no es así. Los iconos de App Store ahora deben incluirse como parte del paquete de proyectos y agregarse en un catálogo de recursos. Apple rechazará las aplicaciones que no contengan un icono de App Store.

El icono de App Store es el aspecto de la aplicación para los usuarios, por lo que debe ser memorable y mostrarse bien en un tamaño pequeño. Los iconos fáciles de recordar se reconocen inmediatamente, y son simples y limpios.

Apple sugiere las siguientes directrices al diseñar el icono de la aplicación:

- Haga que el icono sea adecuado para la aplicación.
- Cree un icono que sea coherente con el diseño de la aplicación.
- Evite usar palabras en su icono.
- Piense de manera global: se usa un único icono de aplicación en todos los territorios de la tienda.

Se precisa una imagen de 1024 x 1024 píxeles para el icono de la aplicación que se mostrará en la App Store.  Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

Para más información, consulte las directrices de la [interfaz humana de iOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)de Apple.

## <a name="adding-an-app-store-icon"></a>Agregar un icono de App Store

Los iconos de Application Store ahora deben entregarse en un catálogo de recursos. 

Para agregar un icono de App Store, haga lo siguiente:

1. Busque el conjunto de imágenes **Appicor** en el archivo **assets. xcassets** del proyecto. 
    - Todos los proyectos nuevos deben aparecer con un archivo **assets. xcassets** que contiene un conjunto de imágenes AppIcon.
    - Para agregar un nuevo catálogo de recursos, haga clic con el botón derecho en el proyecto y seleccione **agregar > nuevo archivo > catálogo de recursos**.
    - Para agregar un nuevo conjunto de imágenes icono de aplicación, haga clic con el botón derecho en el área del conjunto de iconos y seleccione iconos de la **aplicación & imágenes de inicio > icono nueva aplicación**:

    ![Agregar nueva opción de conjunto de imágenes](app-store-icon-images/image1.png)

2. Desplácese hasta el icono de **App Store** en la lista:

    ![Icono de App Store](app-store-icon-images/image2.png)

3. Haga clic en el icono y busque la imagen de 1024 x 1024 píxeles. Guarde el catálogo de recursos.

## <a name="related-links"></a>Vínculos relacionados

- [Administrar iconos con catálogos de recursos](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
