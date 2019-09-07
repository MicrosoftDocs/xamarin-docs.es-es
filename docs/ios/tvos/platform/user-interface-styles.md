---
title: Estilos de la interfaz de usuario de tvOS en Xamarin
description: En este artículo se tratan los temas claros y oscuros de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 89756d5b897b39dd0cf45074474189a4a0a8ada8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769985"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Estilos de la interfaz de usuario de tvOS en Xamarin

_En este artículo se tratan los temas claros y oscuros de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin. tvOS._

tvOS 10 ahora es compatible con un tema de la interfaz de usuario oscuro y ligero al que todos los controles de UIKit de la compilación se adaptarán automáticamente, en función de las preferencias del usuario. Además, el desarrollador puede ajustar manualmente los elementos de la interfaz de usuario en función del tema seleccionado por el usuario y puede invalidar un tema determinado.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>Acerca de los nuevos estilos de la interfaz de usuario

Como se indicó anteriormente, tvOS 10 ahora es compatible con un tema de la interfaz de usuario oscuro y claro que todos los controles de UIKit de compilación se adaptarán automáticamente a, en función de las preferencias del usuario.

El usuario puede cambiar este tema; para**ello, vaya** a **configuración** > **General** > y cambie entre **Light** y **Dark**:

[![](user-interface-styles-images/theme01.png "La aplicación de configuración")](user-interface-styles-images/theme01.png#lightbox)

Cuando se selecciona el tema **oscuro** , todos los elementos de la interfaz de usuario cambiarán al texto claro en un fondo oscuro:

[![](user-interface-styles-images/theme02.png "El tema oscuro")](user-interface-styles-images/theme02.png#lightbox)

El usuario tiene la opción de cambiar el tema en cualquier momento y puede hacerlo en función de la actividad actual, donde se encuentra el Apple TV o la hora del día.

El tema de la interfaz de usuario de la luz es el tema predeterminado y cualquier aplicación de tvOS existente seguirá usando el tema claro, independientemente de las preferencias del usuario, a menos que se modifiquen para tvOS 10 con el fin de aprovechar el tema oscuro. Una aplicación de tvOS 10 también tiene la capacidad de invalidar el tema actual y usar siempre el tema claro o oscuro para algunas o todas las interfaces de usuario.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Adopción de los temas claro y oscuro

Para admitir esta característica, Apple ha agregado una nueva API a la `UITraitCollection` clase y una aplicación tvOS debe participar para admitir el aspecto oscuro (a través de un valor de configuración `Info.plist` en su archivo).

Para participar en la compatibilidad con temas claros y oscuros, haga lo siguiente:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. Seleccione la vista **código fuente** (en la parte inferior del editor).
3. Agregue una nueva clave y llámela `UIUserInterfaceStyle`:

    [![](user-interface-styles-images/theme03.png "La clave UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Deje el tipo establecido en `String` y escriba un valor de `Automatic`:

    [![](user-interface-styles-images/theme04.png "Especificar automático")](user-interface-styles-images/theme04.png#lightbox)
5. Guarde los cambios en el archivo.

Hay tres valores posibles para la `UIUserInterfaceStyle` clave:

- **Light** : obliga a la interfaz de usuario de la aplicación tvOS a usar siempre el tema claro.
- **Dark** : obliga a la interfaz de usuario de la aplicación tvOS a usar siempre el tema oscuro.
- **Automático** : cambia entre el tema claro y oscuro en función de las preferencias del usuario en la configuración. Esta es la configuración preferida.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Compatibilidad con temas de UIKit

Si una aplicación de tvOS usa controles estándar basados en `UIView` integrados, responderán automáticamente al tema de la interfaz de usuario sin intervención del desarrollador.

Además, `UILabel` y `UITextView` cambiará automáticamente su color según el tema seleccionar la interfaz de usuario:

- El texto será negro en el tema claro.
- El texto estará en blanco en el tema oscuro.

Si el desarrollador cambia el color del texto manualmente (ya sea en el guión gráfico o en el código), será responsable de administrar los cambios de color basados en el tema de la interfaz de usuario.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Nuevos efectos de desenfoque

Para admitir los temas claro y oscuro en una aplicación de tvOS 10, Apple ha agregado dos nuevos efectos de desenfoque. Estos nuevos efectos ajustarán automáticamente el desenfoque en función del tema de la interfaz de usuario que el usuario haya seleccionado como sigue:

- `UIBlurEffectStyleRegular`: Usa un desenfoque claro en el tema claro y un desenfoque oscuro en el tema oscuro.
- `UIBlurEffectStyleProminent`: Usa un desenfoque extra claro en el tema claro y un desenfoque extra oscuro en el tema oscuro.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Trabajar con colecciones de rasgos

La nueva `UserInterfaceStyle` propiedad de la `UITraitCollection` clase se puede usar para obtener el tema de la interfaz de usuario actualmente seleccionado `UIUserInterfaceStyle` y será una enumeración de uno de los valores siguientes:

- **Claro** : se selecciona el tema de la interfaz de usuario de luz.
- **Dark** : se selecciona el tema de la interfaz de usuario oscuro.
- No **especificado** : la vista no se ha mostrado todavía en pantalla, por lo que se desconoce el tema de la interfaz de usuario actual.

Además, las colecciones de rasgos tienen las siguientes características en tvOS 10:

- El proxy de apariencia puede personalizarse en función `UserInterfaceStyle` de la de `UITraitCollection` un determinado para cambiar cosas como imágenes o colores de los elementos basados en el tema.
- Una aplicación tvOS puede controlar los cambios de la colección de rasgos `TraitCollectionDidChange` invalidando `UIView` el `UIViewController` método de una clase o.

> [!IMPORTANT]
> La versión preliminar de Xamarin. tvOS para tvOS 10 `UIUserInterfaceStyle` `UITraitCollection` todavía no es totalmente compatible con. En una versión futura se agregará soporte técnico completo.

<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Personalizar la apariencia basada en el tema

En el caso de los elementos de la interfaz de usuario que admiten el proxy de apariencia, su apariencia se puede ajustar en función del tema de la interfaz de usuario de su colección de rasgos. Por lo tanto, para un elemento de la interfaz de usuario determinado, el desarrollador puede especificar un color para el tema claro y otro color para el tema oscuro.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Desafortunadamente, la versión preliminar de Xamarin. tvOS para tvOS 10 no `UIUserInterfaceStyle` es `UITraitCollection`totalmente compatible con, por lo que este tipo de personalización todavía no está disponible. En una versión futura se agregará soporte técnico completo.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Responder directamente a los cambios de tema

En el desarrollador requiere un control más profundo sobre la apariencia de un elemento de la interfaz de usuario basado en el tema de la `TraitCollectionDidChange` interfaz de usuario `UIView` seleccionado `UIViewController` , pueden invalidar el método de una clase o.

Por ejemplo:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>Invalidar una colección de rasgos

En función del diseño de una aplicación de tvOS, puede haber ocasiones en las que el desarrollador necesite invalidar la colección de rasgos de un elemento de interfaz de usuario determinado y hacer que siempre use un tema específico de la interfaz de usuario.

Esto se puede hacer mediante el `SetOverrideTraitCollection` método en la `UIViewController` clase. Por ejemplo:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Para obtener más información, consulte las secciones [rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) e [invalidación de rasgos](~/ios/user-interface/storyboards/unified-storyboards.md) de nuestra introducción a la documentación sobre [guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Colecciones de rasgos y guiones gráficos

En tvOS 10, el guión gráfico de una aplicación se puede establecer para responder a las colecciones de rasgos y muchos elementos de la interfaz de usuario se pueden convertir en un tema claro y oscuro. La versión preliminar actual de Xamarin. tvOS para tvOS 10 no es compatible con esta característica en el diseñador de la interfaz, por lo que el guion gráfico deberá editarse en la Interface Builder de Xcode como solución alternativa.

Para habilitar la compatibilidad con la colección de rasgos, haga lo siguiente:

1. Haga clic con el botón derecho en el archivo de guion gráfico en el **Explorador de soluciones** y seleccione **abrir con** > **Xcode Interface Builder**:

    [![](user-interface-styles-images/theme05.png "Abrir con Xcode Interface Builder")](user-interface-styles-images/theme05.png#lightbox)
2. Para habilitar la compatibilidad con la colección de rasgos, cambie al **Inspector de archivos** y active la propiedad **usar variaciones de rasgos** en la sección **Interface Builder documento** :

    [![](user-interface-styles-images/theme06.png "Habilitar la compatibilidad con la colección de rasgos")](user-interface-styles-images/theme06.png#lightbox)
3. Confirme el cambio para usar variaciones de rasgos:

    [![](user-interface-styles-images/theme07.png "La alerta usar variaciones de rasgos")](user-interface-styles-images/theme07.png#lightbox)
4. Guarde los cambios en el archivo de guion gráfico.

Apple ha agregado las siguientes capacidades al editar guiones gráficos de tvOS en Interface Builder:

- El desarrollador puede especificar diferentes variaciones de elementos de la interfaz de usuario basados en el tema de la interfaz de usuario en el **Inspector de atributos**:

  - Ahora varias propiedades tienen una **+** junto a ellas, en las que se puede hacer clic para agregar una versión específica del tema de la interfaz de usuario:

    [![](user-interface-styles-images/theme08.png "Agregar una versión específica del tema de la interfaz de usuario")](user-interface-styles-images/theme08.png#lightbox)

  - El desarrollador puede especificar una nueva propiedad o hacer clic en el botón **x** para quitarla:

    [![](user-interface-styles-images/theme09.png "Especifique una nueva propiedad o haga clic en el botón x para quitarla.")](user-interface-styles-images/theme09.png#lightbox)
- El desarrollador puede obtener una vista previa del diseño de la interfaz de usuario en el tema claro o oscuro desde Interface Builder:

  - La parte inferior del Superficie de diseño permite al desarrollador cambiar el tema de la interfaz de usuario actual:

    [![](user-interface-styles-images/theme10.png "La parte inferior del Superficie de diseño")](user-interface-styles-images/theme10.png#lightbox)

  - El nuevo tema se mostrará en Interface Builder y se mostrarán los ajustes específicos de la colección de rasgos:

    [![](user-interface-styles-images/theme11.png "El tema que se muestra en Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

Además, el simulador tvOS ahora tiene un método abreviado de teclado que permite al desarrollador cambiar rápidamente entre los temas claro y oscuro al depurar una aplicación de tvOS. Use la secuencia de teclado del **comando SHIFT-D** para alternar entre Light y Dark.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han tratado los temas claros y oscuros de la interfaz de usuario que Apple ha agregado a tvOS 10 y cómo implementarlos en una aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
