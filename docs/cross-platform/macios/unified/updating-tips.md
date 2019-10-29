---
title: Sugerencias para actualizar el código a Unified API
description: En este documento se describen los errores comunes y varias sugerencias útiles al actualizar una aplicación para que use el Unified API de Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ee207ffc83f887e9c86c650b6f93fc2ff9f5b69c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014991"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>Sugerencias para actualizar el código a Unified API

Al actualizar las soluciones de Xamarin anteriores al Unified API, pueden producirse los siguientes errores.

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException no encontró el error de guion gráfico

Hay un [error](https://bugzilla.xamarin.com/show_bug.cgi?id=25569) en la versión actual de Visual Studio para Mac que puede producirse después de usar la herramienta de migración automatizada para convertir el proyecto en las API unificadas. Después de la actualización, si recibe un mensaje de error con el formato:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

Puede hacer lo siguiente para solucionar este problema, busque el siguiente archivo de destino de compilación:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

En este archivo, debe buscar la siguiente declaración de destino:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Y agregue el atributo `DependsOnTargets="_CollectBundleResources"`. Así:

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

Guarde el archivo, reinicie Visual Studio para Mac y realice una limpieza & la recompilación del proyecto. En breve, Xamarin debe publicar una corrección para este problema.

## <a name="useful-tips"></a>Sugerencias útiles

Después de usar la herramienta de migración, es posible que se sigan produciendo algunos errores del compilador que requieren intervención manual.
Algunos aspectos que es posible que deban solucionarse manualmente son los siguientes:

- Comparar `enum`s puede requerir una conversión `(int)`.

- `NSDictionary.IntValue` devuelve ahora una `nint`, hay una `Int32Value` que se puede usar en su lugar.

- los tipos `nfloat` y `nint` no se pueden marcar como `const`; `static readonly nint` es una alternativa razonable.

- Los elementos que solía estar directamente en el espacio de nombres `MonoTouch.` ahora están en el espacio de nombres `ObjCRuntime.` (por ejemplo: `MonoTouch.Constants.Version` es ahora `ObjCRuntime.Constants.Version`).

- El código que serializa objetos se puede interrumpir al intentar serializar `nint` y tipos de `nfloat`. Asegúrese de comprobar que el código de serialización funciona según lo esperado después de la migración.

- A veces, la herramienta automatizada pierde código dentro `#if #else` directivas de compilador condicional. En este caso, deberá realizar las correcciones manualmente (consulte los errores comunes a continuación).

- Es posible que la herramienta de migración no solucione automáticamente los métodos exportados manualmente con `[Export]`, por ejemplo, en este código Snippert debe actualizar manualmente el tipo de valor devuelto para `nfloat`:

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- El Unified API no proporciona una conversión implícita entre NSDate y .NET DateTime porque no es una conversión sin pérdida. Para evitar errores relacionados con `DateTimeKind.Unspecified` convertir el `DateTime` .NET en local o UTC antes de realizar la conversión a `NSDate`.

- Los métodos de categoría Objective-C ahora se generan como métodos de extensión en el Unified API. Por ejemplo, el código que usaba anteriormente `UIView.DrawString` ahora haría referencia a `NSString.DrawString` de la Unified API.

- El código que usa las clases AVFoundation con `VideoSettings` debe cambiar para usar la propiedad `WeakVideoSettings`. Esto requiere un `Dictionary`, que está disponible como una propiedad en las clases de configuración, por ejemplo:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- El constructor de `.ctor(IntPtr)` NSObject ha cambiado de Public a Protected ([para evitar un uso inadecuado](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction` se ha [reemplazado](~/cross-platform/macios/unified/overview.md#NSAction) por el `Action`estándar de .net. Algunos delegados simples (de un solo parámetro) también se han reemplazado por `Action<T>`.

Por último, consulte las [diferencias de v Unified API clásico](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) para buscar cambios en las API del código. La búsqueda de [esta página](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) le ayudará a encontrar las API clásicas y sus actualizaciones.

> [!NOTE]
> El espacio de nombres `MonoTouch.Dialog` permanece igual después de la migración. Si el código usa **MonoTouch. Dialog** debe seguir usando ese espacio de nombres. *no* cambie `MonoTouch.Dialog` a `Dialog`.

## <a name="common-compiler-errors"></a>Errores comunes del compilador

A continuación se muestran otros ejemplos de errores comunes, junto con la solución:

**Error CS0012: el tipo ' MonoTouch. UIKit. UIView ' está definido en un ensamblado al que no se hace referencia.**

Corrección: normalmente significa que el proyecto hace referencia a un componente o paquete de NuGet que no se ha compilado con el Unified API. Debe eliminar y volver a agregar todos los componentes y paquetes de NuGet. Si esto no soluciona el error, es posible que la biblioteca externa no admita todavía el Unified API.

**Error MT0034: no se pueden incluir ' MonoTouch. dll ' y ' Xamarin. iOS. dll ' en el mismo proyecto de Xamarin. iOS: ' Xamarin. iOS. dll ' se hace referencia de forma explícita, mientras que ' Xamarin. Mobile, version = 0.6.3.0, Culture = neutral, PublicKeyToken = null '.**

Corregir: Elimine el componente que está causando este error y vuelva a agregarlo al proyecto.

**Error CS0234: el tipo o el nombre de espacio de nombres ' Foundation ' no existe en el espacio de nombres ' MonoTouch '. ¿Falta una referencia de ensamblado?**

Corrección: la herramienta de migración automatizada en Visual Studio para Mac *debe* actualizar todas las referencias de `MonoTouch.Foundation` a `Foundation`, pero en algunos casos deberá actualizarse manualmente. Pueden aparecer errores similares para los demás espacios de nombres contenidos anteriormente en `MonoTouch`, como `UIKit`.

**Error CS0266: no se puede convertir implícitamente el tipo ' Double ' en ' System. float '**

Corregir: cambie el tipo y conviértalo a `nfloat`. Este error también puede producirse en otros tipos con equivalentes de 64 bits (como `nint`).

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**Error CS0266: no se puede convertir implícitamente el tipo ' CoreGraphics. CGRect ' a ' System. Drawing. RectangleF '. Existe una conversión explícita (¿falta una conversión?)**

Corrección: cambie las instancias a `RectangleF` a `CGRect`, `SizeF` a `CGSize`y `PointF` a `CGPoint`. El espacio de nombres `using System.Drawing;` debe reemplazarse por `using CoreGraphics;` (si aún no está presente).

**error CS1502: la mejor coincidencia de método sobrecargado para ' CoreGraphics. CGContext. SetLineDash (System. nfloat, System. nfloat []) ' tiene algunos argumentos no válidos**

Fix: cambie el tipo de matriz a `nfloat[]` y convierta `Math.PI`explícitamente.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Error CS0115: ' WordsTableSource. RowsInSection (UIKit. UITableView, int) ' está marcado como invalidación, pero no se encontró ningún método adecuado para invalidar**

Corregir: cambie el valor devuelto y los tipos de parámetro a `nint`. Esto se produce normalmente en invalidaciones de método, como las de `UITableViewSource`, como `RowsInSection`, `NumberOfSections`, `GetHeightForRow`, `TitleForHeader`, `GetViewForHeader`, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Error CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: el tipo de valor devuelto debe ser ' System. NINT ' para que coincida con el miembro invalidado `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Fix: cuando el tipo de valor devuelto cambia a `nint`, convierta el valor devuelto en `nint`.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**Error CS1061: el tipo ' CoreGraphics. CGPath ' no contiene una definición para ' AddElipseInRect '**

Corrección: corrija la ortografía de `AddEllipseInRect`. Otros cambios de nombre incluyen:

- Cambie ' color. Black ' a `NSColor.Black`.
- Cambie MapKit ' AddAnnotation ' a `AddAnnotations`.
- Cambie AVFoundation ' DataUsingEncoding ' a `Encode`.
- Cambie AVFoundation ' AVMetadataObject. TypeQRCode ' a `AVMetadataObjectType.QRCode`.
- Cambie AVFoundation ' videosettings ' por `WeakVideoSettings`.
- Cambie PopViewControllerAnimated a `PopViewController`.
- Cambie CoreGraphics ' CGBitmapContext. SetRGBFillColor ' a `SetFillColor`.

**Error CS0546: no se puede invalidar porque ' MapKit. MKAnnotation. Coordinate ' no tiene un descriptor de acceso set reemplazable (CS0546)**

Al crear una anotación personalizada mediante la creación de subclases MKAnnotation, el campo de coordenadas no tiene ningún establecedor, solo un captador.

[Corrección](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

- Agregar un campo para realizar un seguimiento de la coordenada
- devuelve este campo en el captador de la propiedad Coordinate.
- Invalide el método SetCoordinate y establezca el campo.
- Llamar a SetCoordinate en el constructor con el parámetro de coordenadas pasado

Debería tener un aspecto similar al siguiente:

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones](~/cross-platform/macios/unified/updating-apps.md)
- [Actualización de aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Actualización de aplicaciones Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Actualizar enlaces](~/cross-platform/macios/unified/update-binding.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
