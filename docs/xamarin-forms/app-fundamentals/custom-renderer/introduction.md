---
title: Introducción a los representadores personalizados
description: En este artículo, se proporciona una introducción a los representadores personalizados y se describe el proceso para crear un representador personalizado.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: ad2868a82f662f45066a6111a1dd3bd2aacad671
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771880"
---
# <a name="introduction-to-custom-renderers"></a>Introducción a los representadores personalizados

_Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Se pueden usar para realizar pequeños cambios de estilo o para una personalización sofisticada del diseño y el comportamiento específicos de una plataforma. En este artículo, se proporciona una introducción a los representadores personalizados y se describe el proceso para crear un representador personalizado._

En [Páginas, diseños y controles](~/xamarin-forms/user-interface/controls/index.md) de Xamarin.Forms, se presenta una API común para describir interfaces de usuario móviles multiplataforma. Cada página, diseño y control se representa de forma distinta en cada plataforma mediante una clase `Renderer` que, a su vez, crea un control nativo (correspondiente a la representación de Xamarin.Forms), lo coloca en la pantalla y agrega el comportamiento especificado en el código compartido.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Los representadores personalizados para un tipo específico pueden agregarse a un proyecto de aplicación para personalizar el control en un lugar, mientras se permite el comportamiento predeterminado en otras plataformas; también pueden agregarse diferentes representadores personalizados a cada proyecto de aplicación para crear una apariencia distinta en iOS, Android y la Plataforma universal de Windows (UWP). Pero la implementación de una clase de representador personalizado para llevar a cabo una personalización de controles simples suele ser una respuesta compleja. Los efectos simplifican este proceso y suelen usarse para pequeños cambios de estilo. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinar por qué es necesario usar representadores personalizados

Cambiar la apariencia de un control de Xamarin.Forms, sin usar un representador personalizado, es un proceso de dos pasos en el que se crea un control personalizado mediante subclases y, después, se usa el control personalizado en lugar del control original. En el siguiente ejemplo de código, se muestra cómo crear una subclase del control `Entry`:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

El control `MyEntry` es un control de `Entry` en el que `BackgroundColor` se establece en gris y al que puede hacerse referencia en XAML mediante la declaración de un espacio de nombres para su ubicación y el uso del prefijo de espacio de nombres en el elemento de control. En el siguiente ejemplo de código, se muestra cómo usar el control personalizado `MyEntry` en una `ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

El prefijo de espacio de nombres `local` puede ser cualquier texto. Pero los valores `namespace` y `assembly` tienen que coincidir con los detalles del control personalizado. Después de declarar el espacio de nombres, el prefijo se usa para hacer referencia al control personalizado.

> [!NOTE]
> Definir el elemento `xmlns` es mucho más fácil en proyectos de biblioteca de .NET Standard que en proyectos compartidos. Una biblioteca de .NET Standard se compila en un ensamblado, por lo que resulta más sencillo determinar cuál tendría que ser el valor de `assembly=CustomRenderer`. Al usar proyectos compartidos, todos los activos compartidos (incluido el código XAML) se compilan en cada uno de los proyectos a los que se hace referencia, lo que significa que, si los proyectos de iOS, Android y UWP tienen sus propios *nombres de ensamblado*, resulta imposible escribir la declaración `xmlns`, ya que el valor tiene que ser distinto para cada aplicación. Para usar los controles personalizados en XAML para proyectos compartidos, es necesario configurar cada proyecto de aplicación con el mismo nombre de ensamblado.

Después, el control personalizado `MyEntry` se representa en cada plataforma, con un fondo gris, como se muestra en las capturas de pantalla siguientes:

![](introduction-images/screenshots.png "Control personalizado MyEntry en cada plataforma")

El cambio del color de fondo del control en cada plataforma se ha obtenido simplemente mediante la creación de subclases del control. Pero los resultados que pueden obtenerse con esta técnica son limitados, ya que no es posible aprovechar las mejoras y personalizaciones específicas de cada plataforma. Cuando sea necesario usarlos, tendrán que implementarse representadores personalizados.

## <a name="creating-a-custom-renderer-class"></a>Crear una clase de representador personalizada

Siga este procedimiento para crear una clase de representador personalizada:

1. Cree una subclase de la clase de representador que represente el control nativo.
1. Reemplace el método que representa el control nativo y escriba la lógica para personalizar el control. Con frecuencia, el método `OnElementChanged` se usa para representar el control nativo, al que se llama cuando se crea el control Xamarin.Forms correspondiente.
1. Agregue un atributo `ExportRenderer` a la clase de representador personalizada para especificar que se usará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no se registra un representador personalizado, se usará el representador predeterminado de la clase base del control. Pero los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un elemento [View](xref:Xamarin.Forms.View) o [ViewCell](xref:Xamarin.Forms.ViewCell).

En los temas de esta serie, se proporcionarán demostraciones y explicaciones de este proceso para distintos elementos de Xamarin.Forms.

## <a name="troubleshooting"></a>Solución de problemas

Si se incluye un control personalizado en un proyecto de biblioteca de .NET Standard agregado a la solución (es decir, no la biblioteca de .NET Standard creada por la plantilla de proyecto de aplicación de Xamarin.Forms de Visual Studio o Visual Studio para Mac), puede producirse una excepción en iOS al intentar acceder al control personalizado. Si se produce este problema, puede solucionarse mediante la creación de una referencia al control personalizado desde la clase `AppDelegate`:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Esto obliga al compilador a reconocer el tipo `ClassInPCL` y solucionarlo. Como alternativa, el atributo `Preserve` puede agregarse a la clase `AppDelegate` para obtener el mismo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

De esta forma, se crea una referencia al tipo `ClassInPCL`, lo que indica que se necesita en tiempo de ejecución. Para obtener más información, vea [Conservar código](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumen

En este artículo, se proporciona una introducción a los representadores personalizados y se describe el proceso para crear un representador personalizado. Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Se pueden usar para realizar pequeños cambios de estilo o para una personalización sofisticada del diseño y el comportamiento específicos de una plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
