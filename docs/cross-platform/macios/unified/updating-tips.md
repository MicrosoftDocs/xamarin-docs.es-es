---
title: Sugerencias para actualizar el código a Unified API
description: En este documento se describen los errores comunes y varias sugerencias útiles al actualizar una aplicación para que use el Unified API de Xamarin.
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 844730d2ace717b951df2d80b2add6d1094fe997
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226100"
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

Y agréguele el `DependsOnTargets="_CollectBundleResources"` atributo. Así:

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

- La comparación de `(int)` `enum`s puede requerir una conversión.

- `NSDictionary.IntValue`ahora devuelve un `nint`, `Int32Value` que puede usarse en su lugar.

- `nfloat`los `nint` tipos y no se `const`pueden marcar; `static readonly nint` es una alternativa razonable.

- Los elementos que solía estar directamente en el `MonoTouch.` espacio de nombres ahora están en `ObjCRuntime.` el `MonoTouch.Constants.Version` espacio de nombres (por ejemplo `ObjCRuntime.Constants.Version`, ahora).

- El código que serializa objetos se puede interrumpir al intentar `nint` serializar `nfloat` los tipos y. Asegúrese de comprobar que el código de serialización funciona según lo esperado después de la migración.

- A veces, la herramienta automatizada pierde `#if #else` código dentro de las directivas de compilador condicional. En este caso, deberá realizar las correcciones manualmente (consulte los errores comunes a continuación).

- Es posible que la `[Export]` herramienta de migración no corrija automáticamente los métodos exportados manualmente mediante, por ejemplo, en este código Snippert debe actualizar manualmente `nfloat`el tipo de valor devuelto a:

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- El Unified API no proporciona una conversión implícita entre NSDate y .NET DateTime porque no es una conversión sin pérdida. Para evitar errores relacionados con `DateTimeKind.Unspecified` la conversión de `DateTime` .net a local o UTC antes de `NSDate`realizar la conversión a.

- Los métodos de categoría Objective-C ahora se generan como métodos de extensión en el Unified API. Por ejemplo, el código que se `UIView.DrawString` usaba anteriormente ahora `NSString.DrawString` hace referencia a en el Unified API.

- El código que usa las `VideoSettings` clases AVFoundation con debe cambiar `WeakVideoSettings` para utilizar la propiedad. Esto requiere `Dictionary`, que está disponible como una propiedad en las clases de configuración, por ejemplo:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- El constructor `.ctor(IntPtr)` NSObject ha cambiado de Public a Protected ([para evitar un uso inadecuado](~/cross-platform/macios/unified/overview.md#NSObject_ctor)).

- `NSAction`se ha [reemplazado](~/cross-platform/macios/unified/overview.md#NSAction) por el estándar `Action`.net. Algunos delegados simples (de un solo parámetro) también `Action<T>`se han reemplazado por.

Por último, consulte las [diferencias de v Unified API clásico](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) para buscar cambios en las API del código. La búsqueda de [esta página](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) le ayudará a encontrar las API clásicas y sus actualizaciones.

> [!NOTE]
> El `MonoTouch.Dialog` espacio de nombres permanece igual después de la migración. Si el código usa **MonoTouch. Dialog** debe seguir usando ese espacio de nombres. `Dialog` *no* cambie `MonoTouch.Dialog` a.

## <a name="common-compiler-errors"></a>Errores comunes del compilador

A continuación se muestran otros ejemplos de errores comunes, junto con la solución:

**CS0012 de error: El tipo ' MonoTouch. UIKit. UIView ' se define en un ensamblado al que no se hace referencia.**

Corregir Normalmente, esto significa que el proyecto hace referencia a un componente o paquete de NuGet que no se ha compilado con el Unified API. Debe eliminar y volver a agregar todos los componentes y paquetes de NuGet. Si esto no soluciona el error, es posible que la biblioteca externa no admita todavía el Unified API.

**Error MT0034: No puede incluir ' MonoTouch. dll ' y ' Xamarin. iOS. dll ' en el mismo proyecto de Xamarin. iOS: ' Xamarin. iOS. dll ' se hace referencia explícitamente, mientras que ' Xamarin. Mobile, version = 0.6.3.0, Culture = neutral, PublicKeyToken = null ' hace referencia a ' MonoTouch. dll '.**

Corregir Elimine el componente que está causando este error y vuelva a agregarlo al proyecto.

**CS0234 de error: El tipo o el nombre de espacio de nombres ' Foundation ' no existe en el espacio de nombres ' MonoTouch '. ¿Falta una referencia de ensamblado?**

Corregir La herramienta de migración automatizada en Visual Studio para Mac debe `MonoTouch.Foundation` actualizar todas `Foundation`las referencias a; sin embargo, en algunos casos, será necesario actualizarlos manualmente. Pueden aparecer errores similares para los otros espacios de nombres contenidos anteriormente `MonoTouch`en, `UIKit`como.

**CS0266 de error: No se puede convertir implícitamente el tipo ' Double ' en ' System. float '**

Fix: cambie el tipo y conviértalo `nfloat`a. Este error también puede producirse en otros tipos con equivalentes de 64 bits (como `nint`,).

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**CS0266 de error: No se puede convertir implícitamente el tipo ' CoreGraphics. CGRect ' en ' System. Drawing. RectangleF '. Existe una conversión explícita (¿falta una conversión?)**

Corregir Cambie las instancias `RectangleF` de `CGRect`a `SizeF` , `CGSize`a y`PointF` a .`CGPoint` El espacio `using System.Drawing;` de nombres debe reemplazarse por `using CoreGraphics;` (si aún no está presente).

**CS1502 de error: La mejor coincidencia de método sobrecargado para ' CoreGraphics. CGContext. SetLineDash (System. nfloat, System. nfloat []) ' tiene algunos argumentos no válidos**

Corregir Cambiar el tipo de `nfloat[]` matriz a y `Math.PI`convertir explícitamente.

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**Error CS0115: ' WordsTableSource. RowsInSection (UIKit. UITableView, int) ' está marcado como invalidación, pero no se encontró ningún método adecuado para invalidar**

Corregir Cambie el valor devuelto y los tipos `nint`de parámetro a. Esto se produce normalmente en invalidaciones de método, como `UITableViewSource`las de `RowsInSection`, `NumberOfSections`incluidas `GetHeightForRow`, `TitleForHeader` `GetViewForHeader`,,,, etc.

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**Error CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: el tipo de valor devuelto debe ser ' System. NINT ' para que coincida con el miembro invalidado`UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

Corregir Cuando se cambia el tipo de valor `nint`devuelto a, convierta `nint`el valor devuelto a.

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**CS1061 de error: El tipo ' CoreGraphics. CGPath ' no contiene una definición para ' AddElipseInRect '**

Corregir Corrija la ortografía `AddEllipseInRect`de. Otros cambios de nombre incluyen:

- Cambie ' color. Black ' a `NSColor.Black`.
- Cambie MapKit ' AddAnnotation ' a `AddAnnotations`.
- Cambie AVFoundation ' DataUsingEncoding ' a `Encode`.
- Cambie AVFoundation ' AVMetadataObject. TypeQRCode ' por `AVMetadataObjectType.QRCode`.
- Cambie AVFoundation ' videosettings ' por `WeakVideoSettings`.
- Cambie PopViewControllerAnimated a `PopViewController`.
- Cambie CoreGraphics ' CGBitmapContext. SetRGBFillColor ' por `SetFillColor`.

**Error CS0546: no se puede invalidar porque ' MapKit. MKAnnotation. Coordinate ' no tiene un descriptor de acceso set reemplazable (CS0546)**

Al crear una anotación personalizada mediante la creación de subclases MKAnnotation, el campo de coordenadas no tiene ningún establecedor, solo un captador.

[Fix](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

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
