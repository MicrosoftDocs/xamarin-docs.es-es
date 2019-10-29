---
title: ToolBar
description: 'La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acciones predeterminada: se puede colocar en cualquier parte de la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3f4a9393d8ef6ef54ba3dba29f1109080f1e321a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029102"
---
# <a name="toolbar"></a>ToolBar

_La barra de herramientas es un componente de barra de acción que proporciona más flexibilidad que la barra de acciones predeterminada: se puede colocar en cualquier parte de la aplicación, se puede cambiar su tamaño y puede usar una combinación de colores diferente del tema de la aplicación. Además, cada pantalla de la aplicación puede tener varias barras de herramientas._

## <a name="overview"></a>Información general

Un elemento de diseño clave de cualquier actividad de Android es una *barra de acciones*. La barra de acciones es el componente de la interfaz de usuario que se usa para la navegación, la búsqueda, los menús y la personalización de marca en una aplicación Android. En las versiones de Android anteriores a Android 5,0 Lollipop, la barra de acciones (también conocida como la barra de la *aplicación*) era el componente recomendado para proporcionar esta funcionalidad. 

La `Toolbar` widget (introducida en Android 5,0 Lollipop) puede considerarse como una generalización de la interfaz de la barra de acciones &ndash; está pensada para reemplazar la barra de acciones. El `Toolbar` se puede usar en cualquier parte del diseño de una aplicación y es mucho más personalizable que una barra de acción. En la captura de pantalla siguiente se muestra el ejemplo de `Toolbar` personalizado creado en esta guía: 

[![captura de pantalla de ejemplo de una barra de herramientas con elementos de menú Editar, guardar y desbordamiento](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Hay algunas diferencias importantes entre el `Toolbar` y la barra de acciones: 

- Un `Toolbar` se puede colocar en cualquier parte de la interfaz de usuario.

- Se pueden mostrar varias barras de herramientas en la misma pantalla.

- Si se usan fragmentos, cada fragmento puede tener su propio `Toolbar`. 

- Un `Toolbar` se puede configurar para abarcar solo un ancho parcial de la pantalla. 

- Dado que la `Toolbar` no está enlazada a la combinación de colores del décor de ventana de la actividad, puede tener una combinación de colores visualmente distinta. 

- A diferencia de la barra de acciones, el `Toolbar` no incluye un icono a la izquierda. Los menús de la derecha usan menos espacio. 

- El alto de `Toolbar` es ajustable. 

- En el `Toolbar`se pueden incluir otras vistas. 

Un `Toolbar` puede contener uno o varios de los siguientes elementos: 

- Botón de navegación

- Una imagen de logotipo con marca

- Título y subtítulo

- Vistas personalizadas

- Menú Acción

- Menú de desbordamiento

Las [directrices de diseño de material](https://material.google.com/) de Google recomiendan aprovechar estos elementos para dar una apariencia distintiva a las aplicaciones (en lugar de confiar exclusivamente en un icono de aplicación y un título). 

En esta guía se tratan los escenarios de `Toolbar` usados con más frecuencia:

- Reemplazar la barra de acciones predeterminada de una actividad por una `Toolbar`. 

- Agregar un segundo `Toolbar` a una actividad.

- Use la biblioteca de **compatibilidad de Android V7** (denominada *AppCompat* en el resto de esta guía) para implementar `Toolbar` en versiones anteriores de Android. 

## <a name="requirements"></a>Requisitos

`Toolbar` está disponible en Android 5,0 Lollipop (API 21) y versiones posteriores. Cuando el destino sea versiones de Android anteriores a Android 5,0, use la [biblioteca de compatibilidad de Android V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que proporciona compatibilidad con `Toolbar` compatibles con versiones anteriores en un paquete de NuGet. 
La compatibilidad de la [barra de herramientas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica cómo usar esta biblioteca. 

## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas de círculo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de herramientas AppCompat (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
