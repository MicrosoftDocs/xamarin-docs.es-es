---
title: Iconos de aplicación alternativos en Xamarin. iOS
description: En este documento se describe cómo usar iconos de aplicación alternativos en Xamarin. iOS. Describe cómo agregar estos iconos a un proyecto de Xamarin. iOS, cómo modificar el archivo info. plist y cómo administrar el icono de la aplicación mediante programación.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 43ef420f569f7dbca6140798f798f3a128d9c706
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653437"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Iconos de aplicación alternativos en Xamarin. iOS

_En este artículo se explica el uso de iconos de aplicación alternativos en Xamarin. iOS._

Apple ha agregado varias mejoras a iOS 10,3 que permiten a una aplicación administrar su icono:

- `ApplicationIconBadgeNumber`: Obtiene o establece el distintivo del icono de la aplicación en el Springboard.
- `SupportsAlternateIcons`: Si `true` la aplicación tiene un conjunto alternativo de iconos.
- `AlternateIconName`: Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si se usa el icono principal.
- `SetAlternameIconName`: Use este método para cambiar el icono de la aplicación al icono alternativo determinado.

![](alternate-app-icons-images/icons04.png "Una alerta de ejemplo cuando una aplicación cambia su icono")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Agregar iconos alternativos a un proyecto de Xamarin. iOS

Para permitir que una aplicación cambie a un icono alternativo, es necesario incluir una colección de imágenes de icono en el proyecto de aplicación de Xamarin. iOS. Estas imágenes no se pueden agregar al proyecto con el método `Assets.xcassets` típico, sino que se deben agregar directamente a la carpeta de **recursos** .

Haga lo siguiente:

1. Seleccione las imágenes de icono necesarias en una carpeta, seleccione todas y arrástrelas hasta la carpeta **recursos** en el **Explorador de soluciones**:

    ![](alternate-app-icons-images/icons00.png "Selección de las imágenes de iconos de una carpeta")

2. Cuando se le solicite, seleccione **copiar**, **use la misma acción para todos los archivos seleccionados** y haga clic en el botón **Aceptar** :

    ![](alternate-app-icons-images/icons02.png "Cuadro de diálogo Agregar archivo a carpeta")

3. La carpeta de **recursos** debería tener un aspecto similar al siguiente:

    ![](alternate-app-icons-images/icons01.png "La carpeta de recursos debe tener este aspecto.")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificar el archivo info. plist

Con las imágenes necesarias agregadas a la carpeta Resources, la clave [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) deberá agregarse al archivo **info. plist** del proyecto. Esta clave definirá el nombre del nuevo icono y las imágenes que lo componen.

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo **Info.plist** para abrirlo para su edición.
2. Cambie a la vista de **código fuente** .
3. Agregue una clave de **iconos** de agrupación y deje el **tipo** establecido en **Diccionario**.
4. Agregue una `CFBundleAlternateIcons` clave y establezca el **tipo** en **Dictionary**.
5. Agregue una `AppIcon2` clave y establezca el **tipo** en **Dictionary**. Este será el nombre del nuevo conjunto de iconos de aplicación alternativo.
6. Agregar una `CFBundleIconFiles` clave y establecer el **tipo** en **matriz**
7. Agregue una nueva cadena a la `CFBundleIconFiles` matriz para cada archivo de icono que salga de la extensión `@2x`y `@3x`los sufijos,, etc `100_icon`. (por ejemplo,). Repita este paso para todos los archivos que componen el conjunto de iconos alternativo.
8. Agregue una `UIPrerenderedIcon` `AppIcon2` clave al diccionario, establezca el **tipo** en **booleano** y el valor en **no**.
9. Guarde los cambios en el archivo.

El archivo **info. plist** resultante debería tener un aspecto similar al siguiente cuando se complete:

![](alternate-app-icons-images/icons03.png "El archivo info. plist completado")

O similar a esto si se abre en un editor de texto:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Administrar el icono de la aplicación 

Con las imágenes de icono incluidas en el proyecto de Xamarin. iOS y el archivo **info. plist** configurado correctamente, el desarrollador puede usar una de las muchas características nuevas que se han agregado a iOS 10,3 para controlar el icono de la aplicación.

La `SupportsAlternateIcons` propiedad de la `UIApplication` clase permite al desarrollador ver si una aplicación admite iconos alternativos. Por ejemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

La `ApplicationIconBadgeNumber` propiedad de la `UIApplication` clase permite al desarrollador obtener o establecer el número de identificación actual del icono de la aplicación en el Springboard. El valor predeterminado es cero (0). Por ejemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

La `AlternateIconName` propiedad de la `UIApplication` clase permite al desarrollador obtener el nombre del icono de la aplicación alternativa seleccionada actualmente o devuelve `null` si la aplicación usa el icono principal. Por ejemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

La `SetAlternameIconName` propiedad de la `UIApplication` clase permite al desarrollador cambiar el icono de la aplicación. Pase el nombre del icono a SELECT o `null` a para volver al icono principal. Por ejemplo:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Cuando se ejecute la aplicación y el usuario seleccione un icono alternativo, se mostrará una alerta similar a la siguiente:

![](alternate-app-icons-images/icons04.png "Una alerta de ejemplo cuando una aplicación cambia su icono")

Si el usuario vuelve al icono principal, se mostrará una alerta similar a la siguiente:

![](alternate-app-icons-images/icons05.png "Una alerta de ejemplo cuando una aplicación cambia al icono principal")

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado la adición de iconos de aplicación alternativos a un proyecto de Xamarin. iOS y su uso dentro de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
