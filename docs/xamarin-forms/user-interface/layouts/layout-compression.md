---
title: Compresión de diseño
description: La compresión de diseño quita los diseños especificados del árbol visual para intentar mejorar el rendimiento de la representación de páginas. En este artículo se explica cómo habilitar la compresión del diseño y las ventajas que puede llevar a cabo.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5e1cb9709c224ed5059de24bb45eed882ef216b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563541"
---
# <a name="layout-compression"></a>Compresión de diseño

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_La compresión de diseño quita los diseños especificados del árbol visual para intentar mejorar el rendimiento de la representación de páginas. En este artículo se explica cómo habilitar la compresión del diseño y las ventajas que puede llevar a cabo._

## <a name="overview"></a>Información general

Xamarin.Forms realiza el diseño mediante dos series de llamadas a métodos recursivos:

- El diseño comienza en la parte superior del árbol visual con una página y continúa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales de una página. Los elementos que son elementos primarios de otros elementos son responsables de ajustar el tamaño y la posición de sus elementos secundarios en relación con ellos mismos.
- La invalidación es el proceso por el que un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño o la posición correctos. Cada elemento del árbol visual que tiene elementos secundarios recibe una alerta cuando uno de sus elementos secundarios cambia de tamaño. Por lo tanto, un cambio en el tamaño de un elemento en el árbol visual puede producir cambios que se hagan en el árbol.

Para obtener más información sobre cómo Xamarin.Forms realiza el diseño, vea [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

El resultado del proceso de diseño es una jerarquía de controles nativos. Sin embargo, esta jerarquía incluye representadores de contenedores adicionales y contenedores para los representadores de plataforma, lo que aumenta aún más el anidamiento de la jerarquía de vistas. Cuanto más profundo sea el nivel de anidamiento, mayor será la cantidad de trabajo que debe Xamarin.Forms realizarse para mostrar una página. En el caso de los diseños complejos, la jerarquía de vistas puede ser tanto profunda como amplia, con varios niveles de anidamiento.

Por ejemplo, considere el siguiente botón de la aplicación de ejemplo para iniciar sesión en Facebook:

![Botón de Facebook](layout-compression-images/facebook-button.png)

Este botón se especifica como un control personalizado con la siguiente jerarquía de vistas XAML:

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

La jerarquía de vista anidada resultante se puede examinar con [Xamarin inspector](~/tools/inspector/index.md). En Android, la jerarquía de vistas anidadas contiene 17 vistas:

![Botón ver jerarquía para Facebook](layout-compression-images/no-compression.png)

La compresión de diseño, que está disponible para Xamarin.Forms las aplicaciones en las plataformas iOS y Android, pretende aplanar el anidamiento de la vista quitando los diseños especificados del árbol visual, lo que puede mejorar el rendimiento de la representación de páginas. La ventaja de rendimiento que se entrega varía en función de la complejidad de una página, la versión del sistema operativo que se está usando y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

> [!NOTE]
> Aunque este artículo se centra en los resultados de aplicar la compresión de diseño en Android, es igualmente aplicable a iOS.

## <a name="layout-compression"></a>Compresión de diseño

En XAML, la compresión de diseño se puede habilitar estableciendo la `CompressedLayout.IsHeadless` propiedad adjunta en `true` en una clase de diseño:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, se puede habilitar en C# especificando la instancia de diseño como el primer argumento del `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Dado que la compresión de diseño quita un diseño del árbol visual, no es adecuado para los diseños que tienen una apariencia visual o que obtienen una entrada táctil. Por lo tanto, los diseños que establecen [`VisualElement`](xref:Xamarin.Forms.VisualElement) propiedades (como,,, [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) o que aceptan gestos) no son candidatos para la compresión del diseño. Sin embargo, la habilitación de la compresión de diseño en un diseño que establece las propiedades de apariencia visual o que acepta gestos no producirá un error de compilación o en tiempo de ejecución. En su lugar, se aplicará la compresión de diseño y las propiedades de apariencia visual y el reconocimiento de gestos producirán un error en modo silencioso.

En el caso del botón Facebook, la compresión de diseño se puede habilitar en las tres clases de diseño:

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

En Android, esto da como resultado una jerarquía de vista anidada de 14 vistas:

![Ver jerarquía para el botón de Facebook con compresión de diseño](layout-compression-images/layout-compression.png)

En comparación con la jerarquía de vista anidada original de 17 vistas, esto representa una reducción en el número de vistas de 17%. Aunque esta reducción puede parecer insignificante, la reducción de la vista en toda la página puede ser más significativa.

### <a name="fast-renderers"></a>Representadores rápidos

Los representadores rápidos reducen los costos de inflación y representación de Xamarin.Forms los controles en Android mediante el acoplamiento de la jerarquía de vista nativa resultante. Esto mejora aún más el rendimiento al crear menos objetos, lo que a su vez da como resultado un árbol visual menos complejo y un menor uso de memoria. Para obtener más información acerca de los representadores rápidos, vea [representadores rápidos](~/xamarin-forms/internals/fast-renderers.md).

En el caso del botón de Facebook de la aplicación de ejemplo, la combinación de la compresión de diseño y los representadores rápidos produce una jerarquía de vistas anidadas de 8 vistas:

![Ver jerarquía para el botón de Facebook con compresión de diseño y representadores rápidos](layout-compression-images/layout-compression-with-fast-renderers.png)

En comparación con la jerarquía de vista anidada original de 17 vistas, esto representa una reducción del 52%.

La aplicación de ejemplo contiene una página extraída de una aplicación real. Sin la compresión de diseño y los representadores rápidos, la página genera una jerarquía de vistas anidada de 130 vistas en Android. Habilitar los representadores rápidos y la compresión de diseño en las clases de diseño adecuadas reduce la jerarquía de vista anidada a 70 vistas, una reducción del 46%.

## <a name="summary"></a>Resumen

La compresión de diseño quita los diseños especificados del árbol visual para intentar mejorar el rendimiento de la representación de páginas. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Representadores rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)