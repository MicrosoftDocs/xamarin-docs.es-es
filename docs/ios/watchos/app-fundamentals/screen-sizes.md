---
title: Trabajar con tamaños de pantalla de watchos en Xamarin
description: En este documento se describe cómo trabajar con varios tamaños de pantalla de watchos. Describe el diseñador de la interfaz watchos, el simulador de watchos y los recursos de imagen.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: e9c87b76dc6845962450b8cb6fab921ea1748832
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768327"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Trabajar con tamaños de pantalla de watchos en Xamarin

Apple Watch está disponible en dos tamaños de pantalla:

- **38mm**
  - 136 x 170 píxeles lógicos (272 x 340 píxeles físicos)

- **42mm**
  - 156 x 195 píxeles lógicos (312 x 390 píxeles físicos).

Debe tener en cuenta el tamaño de la pantalla al diseñar y probar sus aplicaciones.

## <a name="watchos-interface-designer"></a>Diseñador de la interfaz watchos

De forma predeterminada, el diseñador de Visual Studio para Mac mostrará controladores de interfaz de inspección en **cualquier Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "El diseñador muestra los controladores de interfaz de inspección en cualquier Apple Watch")

Use el menú tamaño para editar y obtener una vista previa del guion gráfico en cualquiera de los tamaños de pantalla disponibles: **38mm** o **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selección del tamaño de 38mm o 42mm")

En ocasiones, el tamaño de pantalla mayor representará el contenido que se truncaría o ocultaría en la pantalla más pequeña.
Asegúrese de probar ambos tamaños.

### <a name="interface-design"></a>Diseño de interfaces

La aplicación debe mostrar el mismo contenido en la pantalla, independientemente del tamaño, y debe expandir o contraer los elementos según corresponda. En el diseñador de Visual Studio para Mac, en el inspector de atributos, debe usar en relación con el contenedor o **el tamaño para ajustar el contenido** en **función** de los tamaños fijos.

![](screen-sizes-images/sizeattributepanel-sml.png "Usar en relación con el contenedor o el tamaño para ajustar el contenido en preferencia a los tamaños fijos")

Dado que la pantalla de inspección está rodeada por un bisel negro, no se recomienda proporcionar relleno alrededor de la interfaz. Permita que los elementos se coloquen en el borde de la pantalla y deje que el bisel forme un borde natural alrededor de la aplicación.

## <a name="watchos-simulator"></a>Simulador de watchos

Al realizar pruebas en el simulador, puede cambiar fácilmente entre los dos tamaños de pantalla mediante el menú **Hardware > dispositivo** .

![](screen-sizes-images/simulator.png "El simulador puede cambiar entre los dos tamaños de pantalla mediante el menú dispositivo de hardware")

## <a name="image-resources"></a>Recursos de imagen

Debe usar varios recursos de imagen si un solo recurso no tiene un aspecto adecuado en tamaños diferentes. Los catálogos de recursos de imagen permiten especificar mapas de bits independientes para cada tamaño:

![](screen-sizes-images/images-xcassets.png "Editor de catálogo de recursos de imagen")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

También puede usar el código para determinar el tamaño de la pantalla y cargar imágenes diferentes por completo:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Obtenga más información sobre el uso del [control de imagen](~/ios/watchos/user-interface/image.md).

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
