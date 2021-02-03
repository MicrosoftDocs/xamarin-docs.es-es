---
title: Compilación XAML en Xamarin.Forms
description: En este artículo se explica cómo se puede compilar XAML directamente en lenguaje intermedio (IL) con el Xamarin.Forms compilador XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/03/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d53f80372062f4830d92213110f01d005f92018
ms.sourcegitcommit: 4f274920d1fe906cda0bf83b8e928b3b50147d40
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509889"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilación XAML en Xamarin.Forms

_XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC)._

La compilación de XAML ofrece una serie de ventajas:

- Realiza la comprobación en tiempo de compilación de XAML, notificando al usuario de los errores.
- Reduce parte del tiempo de carga y creación de instancias para los elementos XAML.
- Facilita reducir el tamaño de archivo del ensamblado final al dejar de incluir archivos .xaml.

La compilación XAML está deshabilitada de forma predeterminada en el marco de trabajo. Sin embargo, está habilitada en las plantillas para los proyectos nuevos. Se puede habilitar o deshabilitar explícitamente ( `XamlCompilationOptions.Skip` ) en el nivel de ensamblado y de clase agregando el [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo.

En el ejemplo de código siguiente se muestra cómo habilitar la compilación XAML en el nivel de ensamblado:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

Aunque el atributo se puede colocar en cualquier parte, un buen lugar para ponerlo está en **AssemblyInfo.CS**.

En este ejemplo, se realizará la comprobación en tiempo de compilación de todo el código XAML incluido en el ensamblado, con errores de XAML que se informan en tiempo de compilación en lugar de en tiempo de ejecución. Por lo tanto, el `assembly` prefijo del [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo especifica que el atributo se aplica a todo el ensamblado.

> [!NOTE]
> El [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo y la [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) enumeración residen en el `Xamarin.Forms.Xaml` espacio de nombres, que se debe importar para usarlos.

En el ejemplo de código siguiente se muestra cómo habilitar la compilación XAML en el nivel de clase:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

En este ejemplo, se realizará la comprobación en tiempo de compilación del XAML de la `HomePage` clase y se registrarán los errores como parte del proceso de compilación.

> [!NOTE]
> Los enlaces compilados se pueden habilitar para mejorar el rendimiento del enlace de datos en Xamarin.Forms las aplicaciones. Para obtener más información, vea [Enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Vínculos relacionados

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
