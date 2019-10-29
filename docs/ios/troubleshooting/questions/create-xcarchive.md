---
title: ¿Es posible crear un archivo. xcarchive desde Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 27697223735c4074dd508d3f603ef6aa4e47b1be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031171"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>¿Es posible crear un archivo. xcarchive desde Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A partir de Xamarin 4. x, ahora es posible crear un `.xcarchive` desde Windows estableciendo la propiedad `ArchiveOnBuild` en `true`. Por ejemplo, el uso de `MSBuild` en la línea de comandos:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

El `.xcarchive` se colocará en el directorio de `$HOME/Library/Developer/Xcode/Archives` del host de compilación de Mac que Xcode y Xamarin Studio buscarán para mostrar los archivos creados previamente.

Consulte esta [publicación de foros de Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para ver algunas notas adicionales acerca de la propiedad `ArchiveOnBuild`. Consulte la documentación sobre las [compilaciones de línea de comandos de Xamarin. iOS en Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obtener más información acerca de las propiedades `ServerAddress` y `ServerUser`.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para Xamarin 3 y versiones anteriores

La extensión Xamarin 3. x de Visual Studio no proporciona un mecanismo para producir archivos `.xcarchive`. Dicho esto, la lógica que se usa para crear archivos de `.xcarchive` en Xamarin Studio en Mac [se describe aquí](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), por lo que probablemente podría crear sus propios `.xcarchive` a mano si lo desea.

Pero merece la pena mencionar que no necesita un `.xcarchive` para enviarlo a la tienda de aplicaciones. Puede enviar un archivo IPA siempre que esté firmado con un perfil de distribución de App Store (no un perfil de distribución ad hoc).

De hecho, incluso puede comprimir el paquete de `.app` (firmado con un perfil de distribución de la tienda de aplicaciones) y enviar ese archivo de `.zip` a la tienda de aplicaciones.

En cualquier caso, puede usar la aplicación Application Loader para enviar la aplicación (en lugar de Xcode).
