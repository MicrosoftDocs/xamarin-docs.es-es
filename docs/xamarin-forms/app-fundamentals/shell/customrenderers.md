---
title: Representadores personalizados de Xamarin.Forms Shell
description: Las aplicaciones de Xamarin.Forms Shell son muy personalizables mediante las propiedades y los métodos que exponen las distintas clases de Shell. Sin embargo, también es posible crear a un representador personalizado de Shell cuando se requieren personalizaciones más sofisticadas específicas de la plataforma.
ms.prod: xamarin
ms.assetid: 3B1A6AE8-1D1E-4C34-B9AB-48F4444FEF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cd70e7d50990cfc5c7668f4b7a51e889f14a9fb3
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757563"
---
# <a name="xamarinforms-shell-custom-renderers"></a>Representadores personalizados de Xamarin.Forms Shell

Una de las ventajas de las aplicaciones de Xamarin.Forms Shell es que su apariencia y comportamiento es muy personalizable mediante las propiedades y los métodos que exponen las distintas clases de Shell. Aun así, también es posible crear un representador personalizado de Shell cuando se requieren personalizaciones más extensas específicas de la plataforma. Al igual que con otros representadores personalizados, se puede agregar un representador personalizado de Shell a solo un proyecto de plataforma para personalizar la apariencia y el comportamiento, mientras se permite el comportamiento predeterminado en la otra plataforma; o se puede agregar un representador personalizado de Shell diferente a cada proyecto de plataforma para personalizar la apariencia y el comportamiento en iOS y Android.

Las aplicaciones de Shell se representan mediante la clase `ShellRenderer` en iOS y Android. En iOS, la clase `ShellRenderer` se puede encontrar en el espacio de nombres `Xamarin.Forms.Platform.iOS`. En Android, la clase `ShellRenderer` se puede encontrar en el espacio de nombres `Xamarin.Forms.Platform.Android`.

El proceso para crear un representador personalizado de Shell es el siguiente:

1. Cree la subclase de la clase [`Shell`](xref:Xamarin.Forms.Shell). Esta acción ya se realizará en la aplicación de Shell.
1. Consuma la clase [`Shell`](xref:Xamarin.Forms.Shell) con subclases. Esta acción ya se realizará en la aplicación de Shell.
1. Cree una clase de representador personalizado que se derive de la clase `ShellRenderer`, en las plataformas necesarias.

## <a name="create-a-custom-renderer-class"></a>Creación de una clase de representador personalizado

El proceso para crear una clase de representador personalizado de Shell es el siguiente:

1. Se crea una subclase de la clase `ShellRenderer`.
1. Invalide los métodos necesarios para llevar a cabo la personalización necesaria.
1. Agregue un elemento `ExportRendererAttribute` a la subclase `ShellRenderer` para especificar que se usará para representar la aplicación de Shell. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Es opcional para proporcionar un representador personalizado de Shell en cada proyecto de plataforma. Si no está registrado un representador personalizado, se usará entonces la clase `ShellRenderer` predeterminada.

La clase `ShellRenderer` expone los siguientes métodos reemplazables:

| iOS | Android | UWP |
| --- | --- | --- |
| `SetElementSize`<br />`CreateFlyoutRenderer`<br />`CreateNavBarAppearanceTracker`<br />`CreatePageRendererTracker`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellItemTransition`<br />`CreateShellSearchResultsRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTabBarAppearanceTracker`<br />`Dispose`<br />`OnCurrentItemChanged`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateBackgroundColor` | `CreateFragmentForPage`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellFlyoutRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTrackerForToolbar`<br />`CreateToolbarAppearanceTracker`<br />`CreateTabLayoutAppearanceTracker`<br />`CreateBottomNavViewAppearanceTracker`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`SwitchFragment`<br />`Dispose` | `CreateShellFlyoutTemplateSelector`<br />`CreateShellHeaderRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateFlyoutBackdropColor`<br />`UpdateFlyoutBackgroundColor` |

Las clases [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) y [`TabBar`](xref:Xamarin.Forms.TabBar) son alias de la clase [`ShellItem`](xref:Xamarin.Forms.ShellItem) y la clase [`Tab`](xref:Xamarin.Forms.Tab) es un alias de la clase [`ShellSection`](xref:Xamarin.Forms.ShellSection). Por lo tanto, el método `CreateShellItemRenderer` se debe invalidar al crear un representador personalizado para objetos `FlyoutItem`, y el método `CreateShellSectionRenderer` se debe invalidar al crear un representador personalizado para objetos `Tab`.

> [!IMPORTANT]
> Hay clases de representador adicionales de Shell, como `ShellSectionRenderer` y `ShellItemRenderer`, en iOS, Android y UWP. Sin embargo, estas clases de representador adicionales se crean mediante invalidaciones en la clase `ShellRenderer`. Por lo tanto, para personalizar el comportamiento de estas clases de representador adicionales, puede crear una subclase de ellas y una instancia de la subclase en la invalidación adecuada de la clase `ShellRenderer` en subclase.

### <a name="ios-example"></a>Ejemplo de iOS

En el ejemplo de código siguiente se muestra una clase `ShellRenderer` en subclase para iOS, que establece una imagen de fondo en la barra de navegación de la aplicación de Shell:

```csharp
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.iOS.MyShellRenderer))]
namespace Xaminals.iOS
{
    public class MyShellRenderer : ShellRenderer
    {
        protected override IShellSectionRenderer CreateShellSectionRenderer(ShellSection shellSection)
        {
            var renderer = base.CreateShellSectionRenderer(shellSection);
            if (renderer != null)
            {
                (renderer as ShellSectionRenderer).NavigationBar.SetBackgroundImage(UIImage.FromFile("monkey.png"), UIBarMetrics.Default);
            }
            return renderer;
        }
    }
}
```

La clase `MyShellRenderer` invalida el método `CreateShellSectionRenderer` y recupera el representador creado por la clase base. Luego, modifica al representador mediante el establecimiento de una imagen de fondo en la barra de navegación, antes de volver al representador.

### <a name="android-example"></a>Ejemplo de Android

En el ejemplo de código siguiente se muestra una clase `ShellRenderer` en subclase para Android, que establece una imagen de fondo en la barra de navegación de la aplicación de Shell:

```csharp
using Android.Content;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.Droid.MyShellRenderer))]
namespace Xaminals.Droid
{
    public class MyShellRenderer : ShellRenderer
    {
        public MyShellRenderer(Context context) : base(context)
        {
        }

        protected override IShellToolbarAppearanceTracker CreateToolbarAppearanceTracker()
        {
            return new MyShellToolbarAppearanceTracker(this);
        }
    }
}
```

La clase `MyShellRenderer` invalida el método `CreateToolbarAppearanceTracker` y devuelve una instancia de la clase `MyShellToolbarAppearanceTracker`. La clase `MyShellToolbarAppearanceTracker`, que se deriva de la clase `ShellToolbarAppearanceTracker`, se muestra en el ejemplo siguiente:

```csharp
using AndroidX.AppCompat.Widget;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

namespace Xaminals.Droid
{
    public class MyShellToolbarAppearanceTracker : ShellToolbarAppearanceTracker
    {
        public MyShellToolbarAppearanceTracker(IShellContext context) : base(context)
        {
        }

        public override void SetAppearance(Toolbar toolbar, IShellToolbarTracker toolbarTracker, ShellAppearance appearance)
        {
            base.SetAppearance(toolbar, toolbarTracker, appearance);
            toolbar.SetBackgroundResource(Resource.Drawable.monkey);
        }
    }
}
```

La clase `MyShellToolbarAppearanceTracker` invalida el método `SetAppearance` y modifica la barra de herramientas mediante el establecimiento de una imagen de fondo en ella.

> [!IMPORTANT]
> Solo es necesario agregar el objeto `ExportRendererAttribute` a un representador personalizado que se deriva de la clase `ShellRenderer`. Se crean clases de representador de Shell en subclase adicionales mediante la clase `ShellRenderer` en subclase.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados de Xamarin.Forms](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
