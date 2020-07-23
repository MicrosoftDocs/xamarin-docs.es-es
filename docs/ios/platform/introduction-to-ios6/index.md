---
title: Introducción a iOS 6
description: Este documento contiene vínculos a guías que describen las características introducidas en iOS 6. Se describen las vistas de colección, PassKit, el marco de redes sociales y los cambios en StoreKit.
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: be78e76e2a52fb6e924fd67e3f0de9e0890ee25b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933437"
---
# <a name="introduction-to-ios-6"></a>Introducción a iOS 6

_iOS 6 incluye una variedad de nuevas tecnologías para desarrollar aplicaciones, que Xamarin. iOS 6 aporta a los desarrolladores de C#._

[![El logotipo de iOS 6](images/ios6-large.jpg)](images/ios6-large.jpg#lightbox)

Con iOS 6 y Xamarin. iOS 6, los desarrolladores ahora tienen una gran capacidad a su disposición para crear aplicaciones de iOS, incluidas las que tienen como destino iPhone 5.
En este documento se enumeran algunas de las nuevas características más interesantes que están disponibles y vínculos a artículos para cada tema. Además, toca un par de cambios que serán importantes a medida que los desarrolladores se muevan a iOS 6 y la nueva resolución de iPhone 5.

## <a name="introduction-to-collection-views"></a>[Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md)

Las vistas de colección permiten mostrar el contenido mediante diseños arbitrarios. Permiten crear diseños similares a cuadrículas de forma sencilla, al mismo tiempo que también se admiten diseños personalizados. Para obtener más información, vea la guía de [Introducción a las vistas de colección](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md) .

## <a name="introduction-to-passkit"></a>[Introduction to PassKit](~/ios/platform/passkit.md) (Introducción a PassKit)

El marco de PassKit permite a las aplicaciones interactuar con los pasos digitales que se administran en la aplicación passbook. Para obtener más información, consulte la [Guía de introducción al kit de Pass](~/ios/platform/passkit.md).

## <a name="introduction-to-eventkit"></a>[Introducción a EventKit](~/ios/platform/eventkit.md)

El marco de EventKit proporciona una manera de tener acceso a los calendarios, eventos de calendario y datos de recordatorios que almacena la base de datos de calendario. El acceso a los calendarios y los eventos de calendario está disponible desde iOS 4, pero ahora iOS 6 expone el acceso a los datos de recordatorios. Para obtener más información, consulte la guía [I](~/ios/platform/eventkit.md) [ntroduction to EventKit](~/ios/platform/eventkit.md) .

## <a name="introduction-to-the-social-framework"></a>[Introducción al marco de redes sociales](~/ios/platform/social-framework.md)

El marco de redes sociales proporciona una API unificada para interactuar con redes sociales, como Twitter y Facebook, así como SinaWeibo para los usuarios de China. Para obtener más información, consulte la guía de [Introducción a la versión de .NET Framework](~/ios/platform/social-framework.md) .

## <a name="changes-to-storekit"></a>[Cambios en StoreKit](changes-to-storekit.md)

Apple ha incorporado dos nuevas características en el kit de tiendas: compra y descarga de contenido de iTunes o App Store desde dentro de la aplicación y hospedaje de archivos de contenido para compras desde la aplicación. Para obtener más información, consulte la guía [cambios en el kit de tiendas](changes-to-storekit.md) .

## <a name="other-changes"></a>Otros cambios

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload y ViewDidUnload desusados

Ya `ViewWillUnload` `ViewDidUnload` no se llama a los métodos y de `UIViewController` en iOS 6. En versiones anteriores de iOS, las aplicaciones pueden haber usado estos métodos para guardar el estado antes de que se descargue una vista y el código de limpieza, respectivamente.

Por ejemplo, Visual Studio para Mac crearía un método denominado `ReleaseDesignerOutlets` , que se muestra a continuación, que a continuación se llamaría desde `ViewDidUnload` :

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

Sin embargo, en iOS 6, ya no es necesario llamar a `ReleaseDesignerOutlets` .   

Para el código de limpieza, las aplicaciones de iOS 6 deben usar `DidReceiveMemoryWarning` . Sin embargo, el código que llama `Dispose` debe usarse con moderación y solo para objetos con un uso intensivo de la memoria, como se muestra a continuación:

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

De nuevo, no `Dispose` es necesario llamar a como antes. En general, la mayoría de las aplicaciones deben ser quitar controladores de eventos.

En el caso de guardar el estado, las aplicaciones pueden realizar esto en `ViewWillDisappear` y en `ViewDidDisappear` lugar de `ViewWillUnload` .

### <a name="iphone-5-resolution"></a>Resolución de iPhone 5

los dispositivos iPhone 5 tienen una resolución de 640x1136. Las aplicaciones que tienen como destino versiones anteriores de iOS aparecerán en el modo de panorámica cuando se ejecuten en un iPhone 5, como se muestra a continuación:

 [![Las aplicaciones que tienen como destino versiones anteriores de iOS aparecerán en la pantalla panorámica cuando se ejecuten en un iPhone 5.](images/01-letterboxed.png)](images/01-letterboxed.png#lightbox)

Para que la aplicación aparezca en pantalla completa en iPhone 5, simplemente agregue una imagen denominada con `Default-568h@2x.png` una resolución de 640x1136. En la captura de pantalla siguiente se muestra la aplicación que se ejecuta después de que se haya incluido esta imagen:

 [![Esta captura de pantalla muestra la aplicación que se ejecuta después de que se haya incluido esta imagen](images/02-fullscreen.png)](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>Subclase de UINavigationBar

En iOS 6 `UINavigationBar` se pueden crear subclases. Esto permite un control adicional de la apariencia del `UINavigationBar` . Por ejemplo, las aplicaciones pueden subclases para agregar subvistas, animar esas vistas y modificar los límites de `UINavigationBar` .

El código siguiente muestra un ejemplo de una subclase `UINavigationBar` que agrega `UIImageView` :

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

Para agregar una subclase `UINavigationBar` a `UINavigationController` , use el `UINavigationController` constructor que toma el tipo de `UINavigationBar` y `UIToolbar` , como se muestra a continuación:

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

Al usar esta `UINavigationBar` subclase, se muestra la vista de imagen como se muestra en la siguiente captura de pantalla:

 [![Al usar esta subclase UINavigationBar, se muestra la vista de imagen como se muestra en esta captura de pantalla](images/03-navbar.png)](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>Orientación de la interfaz

Antes de que las aplicaciones de iOS 6 pudieran invalidarse `ShouldAutorotateToInterfaceOrientation` , devuelve true para cualquier orientación que el controlador determinado admita. Por ejemplo, el código siguiente se utilizaría para admitir solo vertical:

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

En iOS 6 `ShouldAutorotateToInterfaceOrientation` está en desuso.
En su lugar, las aplicaciones pueden invalidar `GetSupportedInterfaceOrientations` en el controlador de vista raíz, como se muestra a continuación:

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

En iPad, el valor predeterminado es las cuatro orientaciones si `GetSupportedInterfaceOrientation` no se implementa. En iPhone y iPod Touch, el valor predeterminado es todas las orientaciones, excepto `PortraitUpsideDown` .
