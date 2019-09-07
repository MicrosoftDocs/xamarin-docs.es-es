---
title: Barra de herramientas
description: 'La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acciones predeterminada: se puede colocar en cualquier parte de la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a0ca1aa42d9173abbc86a38ae26b14bfb4865a58
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764617"
---
# <a name="toolbar"></a>Barra de herramientas

_La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acciones predeterminada: se puede colocar en cualquier parte de la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas._

## <a name="overview"></a>Información general

Un elemento de diseño clave de cualquier actividad de Android es una *barra de acciones*. La barra de acciones es el componente de la interfaz de usuario que se usa para la navegación, la búsqueda, los menús y la personalización de marca en una aplicación Android. En las versiones de Android anteriores a Android 5,0 Lollipop, la barra de acciones (también conocida como la barra de la *aplicación*) era el componente recomendado para proporcionar esta funcionalidad. 

El `Toolbar` widget (introducido en Android 5,0 Lollipop) puede considerarse como una generalización de la interfaz &ndash; de la barra de acciones, por lo que se pretende reemplazar la barra de acciones. Se `Toolbar` puede usar en cualquier parte del diseño de una aplicación y es mucho más personalizable que una barra de acción. En la captura de pantalla siguiente se `Toolbar` muestra el ejemplo personalizado creado en esta guía: 

[![Captura de pantalla de ejemplo de una barra de herramientas con elementos de menú Editar, guardar y desbordamiento](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Hay algunas diferencias importantes entre `Toolbar` y la barra de acciones: 

- Se `Toolbar` puede colocar en cualquier parte de la interfaz de usuario.

- Se pueden mostrar varias barras de herramientas en la misma pantalla.

- Si se usan fragmentos, cada fragmento puede tener el suyo `Toolbar`propio. 

- Se `Toolbar` puede configurar para que abarque solo un ancho parcial de la pantalla. 

- Dado que no está enlazado a la combinación de colores del décor de ventana de la actividad, puede tener una combinación de colores visualmente distinta. `Toolbar` 

- A diferencia de la barra de acciones `Toolbar` , no incluye un icono a la izquierda. Los menús de la derecha usan menos espacio. 

- El `Toolbar` alto es ajustable. 

- Se pueden incluir otras vistas dentro de `Toolbar`. 

Un `Toolbar` elemento puede contener uno o varios de los siguientes elementos: 

- Botón de navegación

- Una imagen de logotipo con marca

- Título y subtítulo

- Vistas personalizadas

- Menú Acción

- Menú de desbordamiento

Las [directrices de diseño de material](https://material.google.com/) de Google recomiendan aprovechar estos elementos para dar una apariencia distintiva a las aplicaciones (en lugar de confiar exclusivamente en un icono de aplicación y un título). 

En esta guía se tratan los escenarios de `Toolbar` uso más frecuente:

- Reemplazar la barra de acciones predeterminada de una actividad `Toolbar`por un. 

- Agregar un segundo `Toolbar` a una actividad.

- Con la biblioteca de **compatibilidad de Android V7** , biblioteca de AppCompat (denominada *AppCompat* en el resto de esta guía) `Toolbar` para implementar en versiones anteriores de Android. 

## <a name="requirements"></a>Requisitos

`Toolbar`está disponible en Android 5,0 Lollipop (API 21) y versiones posteriores. Cuando el destino sea versiones de Android anteriores a Android 5,0, use la [biblioteca de compatibilidad de Android V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que `Toolbar` proporciona compatibilidad compatible con versiones anteriores en un paquete de NuGet. 
La compatibilidad de la [barra de herramientas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica cómo usar esta biblioteca. 

## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
