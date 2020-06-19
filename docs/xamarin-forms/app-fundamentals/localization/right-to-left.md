---
title: Localización de derecha a izquierda
description: La localización de derecha a izquierda agrega compatibilidad para la dirección de flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 212674472706a36c66436a3955ab7b988f8e246b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137558"
---
# <a name="right-to-left-localization"></a>Localización de derecha a izquierda

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localización de derecha a izquierda agrega compatibilidad para la dirección de flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms._

> [!NOTE]
> La localización de derecha a izquierda requiere el uso de iOS 9 o versiones posteriores; en Android, la API 17 o versiones posteriores.

La dirección de flujo es la dirección en la que el ojo humano lee los elementos de la interfaz de usuario en la página. Algunos lenguajes, como el árabe y hebreo, requieren que los elementos de interfaz de usuario se distribuyan en una dirección de flujo de derecha a izquierda. Esto también puede lograrse estableciendo la propiedad [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection). Esta propiedad obtiene o establece la dirección en que fluyen los elementos de interfaz de usuario dentro de cualquier elemento primario que controle su diseño, y debe establecerse en uno de los valores de enumeración de [`FlowDirection`](xref:Xamarin.Forms.FlowDirection):

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Al establecer la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) en un elemento, generalmente se establece la alineación a la derecha, el orden de lectura de derecha a izquierda y el diseño del control para que fluya de derecha a izquierda:

[![TodoItemPage en árabe con una dirección de flujo de derecha a izquierda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en árabe con una dirección de flujo de derecha a izquierda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en árabe con una dirección de flujo de derecha a izquierda")

> [!TIP]
> Solo debe establecerse la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en el diseño inicial. El cambio de este valor en tiempo de ejecución hace que el proceso de diseño sea difícil y que ello afecte al rendimiento.

El valor de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predeterminado para un elemento sin un elemento primario es [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight), mientras que el valor predeterminado de `FlowDirection` para un elemento con un elemento primario es [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, un elemento hereda el valor de la propiedad `FlowDirection` de su elemento primario en el árbol visual, y cualquier elemento puede invalidar el valor que obtiene de su elemento primario.

> [!TIP]
> Al localizar una aplicación para idiomas con flujo de derecha a izquierda, establezca la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en una página o un diseño raíz. Esto hace que todos los elementos contenidos dentro de la página, o el diseño raíz, respondan adecuadamente a la dirección del flujo.

## <a name="respecting-device-flow-direction"></a>Respetar la dirección del flujo del dispositivo

Respetar la dirección del flujo del dispositivo según el idioma y la región seleccionados es una opción explícita del desarrollador y no se realiza automáticamente. Se consigue mediante el establecimiento de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) de una página, o diseño raíz, en el valor `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection):

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Posteriormente, todos los elementos secundarios de la página, o el diseño raíz, de forma predeterminada heredarán el valor [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection).

## <a name="platform-setup"></a>Configuración de la plataforma

Se requiere una configuración de la plataforma específica para habilitar las configuraciones regionales de derecha a izquierda.

### <a name="ios"></a>iOS

La configuración regional de derecha a izquierda necesaria debe agregarse como un idioma compatible con los elementos de matriz para la clave `CFBundleLocalizations`**Info.plist**. En el ejemplo siguiente se muestra el idioma árabe agregado a la matriz para la clave `CFBundleLocalizations`:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Idiomas compatibles de Info.plist](rtl-images/ios-locales.png "Idiomas compatibles de Info.plist")

Para obtener más información, consulte [Localization Basics in iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios) (Introducción a la localización en iOS).

A continuación, se puede probar la localización de derecha a izquierda cambiando el idioma y la región del dispositivo o el simulador a una configuración regional de derecha a izquierda que se haya especificado en **Info.plist**.

> [!WARNING]
> Tenga en cuenta que al cambiar el idioma y la región para una configuración regional de derecha a izquierda en iOS, todas las vistas [`DatePicker`](xref:Xamarin.Forms.DatePicker) emitirán una excepción si no se incluyen los recursos necesarios para la configuración regional. Por ejemplo, al probar una aplicación en árabe que tenga un `DatePicker`, asegúrese de que **mideast (Oriente Medio)** esté seleccionado en la sección **Internationalization** (Internacionalización) del panel **iOS Build** (Compilación de iOS).

### <a name="android"></a>Android

El archivo **AndroidManifest.xml** de la aplicación debe actualizarse para que el nodo `<uses-sdk>` establezca el atributo `android:minSdkVersion` en 17 y el nodo `<application>` establezca el atributo `android:supportsRtl` en `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

A continuación, se puede probar la localización de derecha a izquierda cambiando el dispositivo o el emulador para que use el idioma de derecha a izquierda o habilitando la opción **Forzar dirección diseño RTL** en **Ajustes > Opciones del desarrollador**.

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Se deben especificar los recursos de idioma necesarios en el nodo `<Resources>` del archivo **Package.appxmanifest**. En el ejemplo siguiente se muestra el idioma árabe agregado al nodo `<Resources>`:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Además, UWP requiere que la referencia cultural predeterminada de la aplicación se defina explícitamente en la biblioteca .NET Standard. Esto puede realizarse estableciendo el atributo `NeutralResourcesLanguage` en `AssemblyInfo.cs`, o en otra clase, en la referencia cultural predeterminada:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

A continuación, se puede probar la localización de derecha a izquierda cambiando el idioma y la región del dispositivo a la configuración regional de derecha a izquierda adecuada.

## <a name="limitations"></a>Limitaciones

La localización de derecha a izquierda de Xamarin.Forms actualmente tiene una serie de limitaciones:

- El control de la ubicación del botón [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), la ubicación de elementos de barra de herramientas y la animación de transición lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La dirección de deslizamiento de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) no realiza la acción.
- El contenido visual de [`Image`](xref:Xamarin.Forms.Image) no se invierte.
- El control de la orientación de [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) y [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- El contenido de [`WebView`](xref:Xamarin.Forms.WebView) no respeta la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Debe agregarse una propiedad `TextDirection`, para controlar la alineación del texto.

### <a name="ios"></a>iOS

- El control de la orientación de [`Stepper`](xref:Xamarin.Forms.Stepper) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- El control de la alineación del texto de [`EntryCell`](xref:Xamarin.Forms.EntryCell) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La alineación y los gestos de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) no se invierten.

### <a name="android"></a>Android

- El control de la orientación de [`SearchBar`](xref:Xamarin.Forms.SearchBar) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- El control de la colocación de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

### <a name="uwp"></a>UWP

- El control de la alineación del texto de [`Editor`](xref:Xamarin.Forms.Editor) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Los elementos secundarios [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) no heredan la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- El control de la alineación del texto de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) lo lleva a cabo la configuración regional del dispositivo, en lugar de la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

## <a name="force-right-to-left-layout"></a>Forzar el diseño de derecha a izquierda

Las aplicaciones de Xamarin.iOS y Xamarin.Android se pueden forzar para que siempre usen un diseño de derecha a izquierda, independientemente de la configuración del dispositivo, modificando los proyectos de plataforma respectivos.

### <a name="ios"></a>iOS

Las aplicaciones de Xamarin.iOS se pueden forzar para que siempre usen un diseño de derecha a izquierda modificando la clase **AppDelegate** de la siguiente manera:

1. Declare la función `IntPtr_objc_msgSend` como la primera línea de la clase `AppDelegate`:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Llame a la función `IntPtr_objc_msgSend` desde el método `FinishedLaunching`, antes de volver del método `FinshedLaunching`:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Este enfoque es útil para las aplicaciones que siempre requieren un diseño de derecha a izquierda y elimina el requisito para establecer la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

Para obtener más información sobre el método `IntrPtr_objc_msgSend`, consulte los [selectores de Objective-C en Xamarin.iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Las aplicaciones de Xamarin.Android se pueden forzar para que siempre usen un diseño de derecha a izquierda modificando la clase **MainActivity** para que incluya la siguiente línea:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Este enfoque es útil para las aplicaciones que siempre requieren un diseño de derecha a izquierda y elimina el requisito para establecer la propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Vídeo de Xamarin.University sobre la compatibilidad de idiomas de derecha a izquierda

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Vídeo de compatibilidad de derecha a izquierda de Xamarin.Forms 3.0**

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
