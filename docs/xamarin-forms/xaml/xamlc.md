---
title: "compilar XAML en Xamarin.Forms " Description: "en este artículo se explica cómo se puede compilar XAML directamente en lenguaje intermedio (IL) con el Xamarin.Forms compilador XAML (XAMLC)".
MS. Prod: Xamarin ms. AssetID: 9A2D10A6-5DFC-485F-A75A-2F7B98314025 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/22/2018 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-compilation-in-xamarinforms"></a>Compilación XAML enXamarin.Forms

_XAML se puede compilar de forma opcional directamente en lenguaje intermedio (IL) con el compilador XAML (XAMLC)._

La compilación de XAML ofrece una serie de ventajas:

- Realiza la comprobación en tiempo de compilación de XAML, notificando al usuario de los errores.
- Reduce parte del tiempo de carga y creación de instancias para los elementos XAML.
- Facilita reducir el tamaño de archivo del ensamblado final al dejar de incluir archivos .xaml.

La compilación XAML está deshabilitada de forma predeterminada para garantizar la compatibilidad con versiones anteriores. Se puede habilitar en el nivel de ensamblado y de clase agregando el [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo.

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
