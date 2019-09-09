---
title: Compresión de diseño
description: Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de representación de página. En este artículo se explica cómo habilitar la compresión de diseño y los beneficios que puede ofrecer.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 453da8c1b943591c331950ecb433bf0055faf85d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770366"
---
# <a name="layout-compression"></a>Compresión de diseño

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de representación de página. En este artículo se explica cómo habilitar la compresión de diseño y los beneficios que puede ofrecer._

## <a name="overview"></a>Información general

Xamarin.Forms se realiza mediante dos series de llamadas al método recursiva del diseño:

- Diseño comienza en la parte superior del árbol visual con una página y pasa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales en una página. Los elementos que son elementos primarios a otros elementos son responsables de ajuste de tamaño y la posición de sus elementos secundarios en relación con ellos mismos.
- Invalidación es el proceso por el cual un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño correcto o la posición. Todos los elementos en el árbol visual que tiene elementos secundarios es una alerta cuando cambia uno de sus elementos secundarios tamaños. Por lo tanto, un cambio en el tamaño de un elemento en el árbol visual puede provocar cambios que ripple el árbol.

Para obtener más información acerca de cómo realiza el diseño Xamarin.Forms, consulte [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

El resultado del proceso de diseño es una jerarquía de controles nativos. Sin embargo, esta jerarquía incluye los representadores de contenedor adicional y contenedores de los representadores de plataforma, lo que infla aún más la jerarquía de vistas de anidamiento. La profundidad del nivel de anidamiento, mayor será la cantidad de trabajo que Xamarin.Forms tiene que realizar para mostrar una página. Para diseños complejos, la jerarquía de vistas puede ser profunda y amplia, con varios niveles de anidamiento.

Por ejemplo, considere el siguiente botón desde la aplicación de ejemplo para iniciar sesión en Facebook:

![](layout-compression-images/facebook-button.png "Botón de Facebook")

Este botón se especifica como un control personalizado con la jerarquía de vistas XAML siguiente:

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

Se puede examinar la jerarquía resultante de la vista anidada con [Xamarin Inspector](~/tools/inspector/index.md). En Android, la jerarquía de la vista anidada contiene 17 vistas:

![](layout-compression-images/no-compression.png "Jerarquía de vistas para botón de Facebook")

Compresión de diseño, que está disponible para las aplicaciones de Xamarin.Forms en las plataformas iOS y Android, tiene como objetivo simplificar el anidamiento quitando diseños especificados del árbol visual, lo que puede mejorar el rendimiento de la representación de la página de vistas. La ventaja de rendimiento que se entrega varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

> [!NOTE]
> Aunque en este artículo se centra en los resultados de aplicar la compresión de diseño en Android, es igualmente aplicable a iOS.

## <a name="layout-compression"></a>Compresión de diseño

En XAML, se puede habilitar la compresión de diseño estableciendo el `CompressedLayout.IsHeadless` propiedad adjunta `true` en una clase de diseño:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, se puede habilitar en C# mediante la especificación de la instancia de diseño como el primer argumento para el `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Dado que la compresión de diseño quita un diseño el árbol visual, no es adecuado para los diseños que tienen una apariencia visual, o que obtener la entrada táctil. Por lo tanto, los diseños que establezca [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) propiedades (como [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible), [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation), [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) o que aceptar movimientos, no son candidatos para el diseño compresión. Sin embargo, habilitar la compresión de diseño en un diseño que establece las propiedades de apariencia visual, o que acepta los gestos, no se producirá un error de compilación o en tiempo de ejecución. En su lugar, se aplicará la compresión de diseño y las propiedades de apariencia visual y reconocimiento de gestos, fallarán en modo silencioso.

Para el botón de Facebook, se puede habilitar la compresión de diseño en las clases de tres diseño:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

En Android, el resultado en una jerarquía de vistas anidadas de 14 vistas:

![](layout-compression-images/layout-compression.png "Jerarquía de vistas de botón de Facebook con compresión de diseño")

En comparación con la jerarquía de vistas anidadas original de 17 vistas, esto representa una reducción en el número de vistas de un 17%. Aunque esta reducción puede parecer insignificante, la reducción de la vista a través de una página entera puede ser más importante.

### <a name="fast-renderers"></a>Representadores rápidos

Los representadores rápidos reducen la inflación y los costos de representación de controles de Xamarin.Forms en Android mediante la reducción de la jerarquía de vistas nativas resultante. Esto mejora aún más el rendimiento porque crea menos objetos, lo que a su vez resulta en un árbol visual menos complejo y menos uso de memoria. Para obtener más información sobre los representadores rápidos, consulte [representadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

Para el botón de Facebook en la aplicación de ejemplo, combinación de compresión de diseño y representadores rápidos genera una jerarquía de vistas anidadas de 8 vistas:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Jerarquía de vistas de botón de Facebook con compresión de diseño y representadores rápidos")

En comparación con la jerarquía de vistas anidadas original de 17 vistas, esto representa una reducción de 52%.

La aplicación de ejemplo contiene una página que se extraen de una aplicación real. Sin compresión de diseño y representadores rápidos, la página genera una jerarquía de vistas anidadas de 130 vistas en Android. Habilitación de representadores rápidos y compresión de diseño en las clases de diseño apropiado, reduce la jerarquía de vistas anidadas a 70 vistas, una reducción del 46%.

## <a name="summary"></a>Resumen

Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de representación de página. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Representadores rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)
