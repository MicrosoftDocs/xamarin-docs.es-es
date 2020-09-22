---
title: Introducción a iOS 14
description: En este documento se proporciona una descripción de alto nivel de algunas API de iOS 14 para las que Xamarin proporciona enlaces de C#.
ms.prod: xamarin
ms.assetid: 4953216e-472b-4484-9c1e-7263ac537f21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: e9793617c76813fb68a57213edd8b48529f19ac7
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843630"
---
# <a name="introduction-to-ios-14"></a>Introducción a iOS 14

Siga estas [instrucciones](~/ios/platform/ios14/get-started.md) para comenzar.

## <a name="new-control-uicolorwell"></a>Nuevo control: UIColorWell

[`UIColorWell`](https://developer.apple.com/documentation/uikit/uicolorwell) es un nuevo control UIKit para seleccionar los colores de una selección de muestras, usar un cuentagotas o escribir valores manualmente. El control muestra un botón de color circular que inicia un formulario modal cuando se puntea.

![UIColorWell](ios14-images/colorwell.png)

```xaml
<ios:UIColorWell
    SelectedColor="{x:Static ios:UIColor.Red}"
    ValueChanged="OnColorChanged" />
```

```csharp
private void OnColorChanged(object sender, EventArgs e)
{
    var colorWell = (UIColorWell)sender; 
    Debug.WriteLine(colorWell.SelectedColor);
}
```

## <a name="modified-controls"></a>Controles modificados

Varios controles han recibido actualizaciones, en concreto:

- Ahora, [UIBarButtonItem](https://developer.apple.com/documentation/uikit/uibarbuttonitem) puede Agregar una UIMenu que se mostrará como elemento flotante.
- [UIDatePicker](https://developer.apple.com/documentation/uikit/uidatepicker) admite ahora varios estilos: automático (predeterminado), compacto, alineado y rueda.
- [UISplitViewController](https://developer.apple.com/documentation/uikit/uisplitviewcontroller) admite ahora tres columnas: principal, secundaria y complementaria.
 
![API de versión preliminar](~/media/shared/preview.png)

## <a name="embedded-widgetkit-support"></a>Compatibilidad con WidgetKit incrustado

Esta versión del SDK agrega compatibilidad para la inserción de extensiones WidgetKit escritas en SWIFT en la aplicación principal de Xamarin. iOS. Esto le permite compilar aplicaciones con compatibilidad con widgets hoy en día.

Con este método, creará una aplicación "híbrida", compilando la extensión del widget con SwiftUI e Embedding en una aplicación de Xamarin. iOS.

El uso de la compatibilidad con WidgetKit requerirá algunos cambios manuales en el archivo del proyecto.

Agregue una sección como esta al proyecto:

```xml
<AdditionalAppExtensions Include="$(MSBuildProjectDirectory)/../../native">
     <Name>NativeTodayExtension</Name>
     <BuildOutput Condition="'$(Platform)' == 'iPhone'">build/Debug-iphoneos</BuildOutput>
     <BuildOutput Condition="'$(Platform)' == 'iPhoneSimulator'">build/Debug-iphonesimulator</BuildOutput>
</AdditionalAppExtensions>
```

Cambie la ruta de acceso incluida en el primer vínculo para que apunte al directorio de compilación de la extensión de la interfaz de usuario de SWIFT.

Puede ser útil habilitar una ubicación de salida relativa del proyecto en el proyecto de Xcode (archivo → configuración del proyecto) para tener una ruta de acceso más sencilla para buscar:

![Configuración de Xcode](ios14-images/xcode-settings.png)

Esta [aplicación de ejemplo](https://github.com/chamons/xamarin-ios-swift-extension/blob/master/App/TestApplication/TestApplication.csproj#L143) usa la serialización de JSON para transferir datos desde una aplicación de Xamarin. iOS a un widget de ejemplo para su visualización.

Los interesados en WidgetKit se invitan a proporcionar sus [comentarios aquí](https://github.com/xamarin/xamarin-macios/issues/8933).

## <a name="related-links"></a>Vínculos relacionados

- [Notas de la versión de Xamarin. iOS 14](/xamarin/ios/release-notes/14/14.0)
- [Documentación de UIColorWell](https://developer.apple.com/documentation/uikit/uicolorwell)
