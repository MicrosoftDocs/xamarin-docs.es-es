---
title: Compatibilidad con iOS 9
description: Aunque no tenga previsto agregar características de iOS 9 a la aplicación de inmediato, debe volver a compilar las aplicaciones con la versión más reciente de Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: cbea7686c2ec96492f9531e1ff30d1686db1a4c0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031774"
---
# <a name="ios-9-compatibility"></a>Compatibilidad con iOS 9

_Aunque no tenga previsto agregar características de iOS 9 a la aplicación de inmediato, debe volver a compilar las aplicaciones con la versión más reciente de Xamarin._

> [!IMPORTANT]
> La información de esta página está dirigida a los clientes con aplicaciones que ya están en la tienda de aplicaciones que tienen como destino iOS 8 o anterior, que aún no han enviado actualizaciones para la compatibilidad con iOS 9. Si ya usa las versiones más recientes (Xcode 7 y Xamarin. iOS 9) para el desarrollo de la aplicación, visite la [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Cuando apareció la primera versión beta de iOS 9, hemos identificado dos problemas con versiones anteriores de Xamarin que se manifestaban como aplicaciones más antiguas que no se pueden iniciar en iOS 9.

Estos dos problemas (como se [detallan en nuestros foros](https://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) fueron:

- Las aplicaciones compiladas para iOS 8 o versiones anteriores no se pueden iniciar en dispositivos de 32 bits (incluidas las aplicaciones compiladas con la [Unified API](~/cross-platform/macios/unified/index.md)).
- No se especificó un error de P/Invoke con la ruta de acceso completa.

La actualización de la instalación de Xamarin a la versión de canal estable más reciente y, a continuación, recompilar y volver a implementar las aplicaciones, corrige estos dos problemas.

_Incluso si no está pensando en actualizar la aplicación con las características de iOS 9 de inmediato, se recomienda volver a compilar con la versión más reciente de Xamarin y volver a enviarla a la tienda de aplicaciones_.

Esto garantizará que la aplicación se ejecutará en iOS 9 después de la actualización de los clientes.
Puede seguir admitiendo iOS 8: recompilar con la versión más reciente no afecta a la versión de destino de la aplicación.

Si tiene más problemas al probar las aplicaciones existentes en iOS 9, lea la sección [mejorar la compatibilidad](#compat) más adelante.

### <a name="updating-with-visual-studio"></a>Actualización con Visual Studio

Se recomienda comprobar explícitamente que Visual Studio se ha actualizado a la versión estable más reciente.

## <a name="what-about-components-nugets-and-other-libraries"></a>¿Qué ocurre con los componentes, paquetes Nuget y otras bibliotecas?

**No** es necesario esperar a las nuevas versiones de los componentes o paquetes Nuget que usa para solucionar los dos problemas mencionados anteriormente.
Estos problemas se corrigen simplemente volviendo a compilar la aplicación con la versión estable más reciente de Xamarin. iOS.

Del mismo modo, **no** es necesario que los proveedores de componentes y los autores de Nuget envíen nuevas compilaciones solo para corregir los dos problemas mencionados anteriormente. Sin embargo, si un componente o Nuget usa `UICollectionView` o carga vistas de archivos **Xib** , *puede* ser necesaria una actualización para solucionar los problemas de compatibilidad de iOS 9 que se mencionan a continuación.

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Mejorar la compatibilidad en el código

Hay algunos casos de patrones de código que se *usan* para trabajar en versiones anteriores de la interrupción de iOS en iOS 9. Estos son algunos posibles problemas (y sus soluciones) que pueden surgir al realizar pruebas en iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView es null en los constructores

**Motivo:** En iOS 9, el constructor de `initWithFrame:` es necesario ahora, debido a los cambios de comportamiento en iOS 9 como los Estados de la [documentación de UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si registró una clase para el identificador especificado y se debe crear una nueva celda, la celda se inicializará ahora llamando a su método `initWithFrame:`.

**Corrección:** Agregue el `initWithFrame:` constructor de la siguiente manera:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplos relacionados: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView no se puede inicializar con el codificador al cargar una vista desde Xib/NIB

**Motivo:** El constructor `initWithCoder:` es el que se llama al cargar una vista desde un archivo Interface Builder Xib. Si este constructor no se exporta, el código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (por ejemplo, en iOS 8), se invocó el constructor `IntPtr` para inicializar la vista.

**Corrección:** Cree y exporte el `initWithCoder:` constructor de la siguiente manera:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Mensaje de dyld: no hay ninguna imagen de caché con el nombre...

Podría experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Se trata de un error en el enlazador nativo de Apple, que tiene lugar cuando hace que un marco privado sea público (JavaScriptCore se hizo público en iOS 7, antes de que fuera un marco de trabajo privado) y el destino de implementación de la aplicación sea para una versión de iOS cuando el marco de trabajo era privado. En este caso, el enlazador de Apple se vinculará con la versión privada de Framework en lugar de con la versión pública.

**Corrección:** Esto se solucionará para iOS 9, pero existe una solución sencilla que puede aplicarse a la vez: solo tiene que elegir como destino una versión posterior de iOS en el proyecto (puede probar iOS 7 en este caso). Otros marcos de trabajo pueden mostrar problemas similares, por ejemplo, el marco de la versión de .NET Framework se hizo público en iOS 8 (y, por tanto, el destino de iOS 7 producirá este error; debe tener como destino iOS 8 para usar WebKit en la aplicación).

## <a name="related-links"></a>Vínculos relacionados

- [información de versión de compatibilidad de iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Actualización de las aplicaciones de Xamarin. iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
