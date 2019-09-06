---
title: ¿Es posible crear un archivo. xcarchive desde Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 6d35827b00a4ccc9bbe3e71444536425e4e1c3b1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288122"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>¿Es posible crear un archivo. xcarchive desde Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A `.xcarchive` partir de Xamarin 4. x, ahora es posible crear desde Windows estableciendo la `ArchiveOnBuild` propiedad en `true`. Por ejemplo, con `MSBuild` en la línea de comandos:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

El `.xcarchive` se colocará en `$HOME/Library/Developer/Xcode/Archives` el directorio del host de compilación de Mac que Xcode y Xamarin Studio buscan para mostrar los archivos creados previamente.

Consulte esta [publicación de foros de Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para ver algunas notas adicionales `ArchiveOnBuild` acerca de la propiedad. Consulte la documentación sobre las [compilaciones de línea de comandos de Xamarin. iOS en Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obtener más información sobre las `ServerAddress` propiedades y. `ServerUser`

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para Xamarin 3 y versiones anteriores

La extensión de Visual Studio Xamarin 3. x no proporciona un mecanismo para generar `.xcarchive` archivos. Dicho esto, la lógica que se usa `.xcarchive` para crear archivos en Xamarin Studio en Mac [se describe aquí](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), por lo que probablemente podría `.xcarchive` crear su propia mano si lo desea.

Pero merece la pena mencionar que no necesita un `.xcarchive` para enviarlo a la tienda de aplicaciones. Puede enviar un archivo IPA siempre que esté firmado con un perfil de distribución de App Store (no un perfil de distribución ad hoc).

De hecho, incluso puede comprimir el `.app` paquete (firmado con un perfil de distribución de App Store) y enviar ese `.zip` archivo a la tienda de aplicaciones.

En cualquier caso, puede usar la aplicación Application Loader para enviar la aplicación (en lugar de Xcode).

