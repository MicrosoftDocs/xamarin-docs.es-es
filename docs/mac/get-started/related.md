---
title: Documentación relacionada con Xamarin.Mac
description: 'En este documento se proporcionan vínculos a la documentación relevante para los desarrolladores de Xamarin.Mac: documentación de Xamarin.iOS, Mac Dev Center de Apple y diversas guías en las que se describe cómo compilar interfaces de usuario con Xamarin.Mac.'
ms.prod: xamarin
ms.assetid: 0a282c58-1c37-4f73-8440-85de2daf454a
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 3b0e9f87505e7537f9c8aeb6f006072feffdbd57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278912"
---
# <a name="xamarinmac-related-documentation"></a>Documentación relacionada con Xamarin.Mac

Además de la sección de Mac de [Microsoft Docs](~/mac/get-started/index.md), hay tres orígenes de documentación excelentes que también pueden ser de ayudar con preguntas sobre Xamarin.Mac:

- [**Documentación de Xamarin.iOS**](~/ios/get-started/index.md): para muchas API (principalmente fuera AppKit/UIKit) hay solo pequeñas diferencias entre las versiones de iOS y Mac OS. En algunos casos cuando una determinada API de iOS tiene el nombre `UIFoo`, una API similar denominada `NSFoo` se puede encontrar en Mac OS. Por lo general estos ejemplos ya estarán en C#.

- **[Mac Dev Center](https://developer.apple.com/devcenter/mac/) de Apple**: en muchos casos un ejemplo de qué API llamar en Objective-C se puede convertir a C# de una forma sencilla. Vea [Understanding Mac APIs](~/mac/app-fundamentals/mac-apis.md) (Descripción de las API de Mac) para más información sobre cómo hacerlo.

- [**Stack Overflow**](https://stackoverflow.com/): un excelente recurso para preguntas sencillas como ["¿Cómo puedo expandir automáticamente todos los nodos de una NSOutlineView?"](https://stackoverflow.com/questions/519751/nsoutlineview-auto-expand-all-nodes). Estos ejemplos estarán a menudo en Objective C y deben convertirse a C#, pero hay un subconjunto de respuestas en C#.

## <a name="user-interface"></a>Interfaz de usuario

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, el desarrollador tiene acceso a los mismos controles de interfaz de usuario que un desarrollador que trabaje en *Objective-C* y *Xcode*. Ya que Xamarin.Mac se integra directamente con Xcode, el desarrollador puede usar _Interface Builder_ de Xcode para crear y mantener interfaces de usuario de una aplicación (o, si quiere, crearlas directamente en código de C#).

Las guías siguientes ofrecen información detallada sobre cómo trabajar con elementos de macOS en una aplicación de Xamarin.Mac:

- [Windows](~/mac/user-interface/window.md)
- [Cuadros de diálogo](~/mac/user-interface/dialog.md)
- [Alertas](~/mac/user-interface/alert.md)
- [Menús](~/mac/user-interface/menu.md)
- [Barras de herramientas](~/mac/user-interface/toolbar.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
