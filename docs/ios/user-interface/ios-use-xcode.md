---
title: Usar Xcode para diseñar ius
description: La manera recomendada de compilar interfaces de usuario de iOS ahora es directamente mediante Xcode en un equipo Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b6fc5c89e9221bc84b364290a275fbd6d35c12c4
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343337"
---
# <a name="using-xcode-to-design-user-interfaces"></a>Usar Xcode para diseñar interfaces de usuario

_La manera recomendada de editar archivos. Storyboard y. NIB es editarlas en Xcode Interface Builder en un equipo Mac._

## <a name="how-to-open-a-storyboard"></a>Cómo abrir un guion gráfico 

Abra un archivo de interfaz de usuario de iOS en Visual Studio para Mac; para ello, haga clic con el botón derecho en un archivo de guion gráfico y seleccione **Xcode Interface Builder** :

[![Seleccionar Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

Después debería ver el inicio de la ventana de Xcode. Cualquier modificación guardada aquí se reflejará en el proyecto de Visual Studio.

[![Ventana de Xcode](images/xcode.png)](images/xcode.png#lightbox)

Puede encontrar más información sobre el Interface Builder de Xcode [aquí](https://developer.apple.com/xcode/interface-builder/).

## <a name="known-problems"></a>Problemas conocidos

Los problemas conocidos se describen en esta sección.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>"Visual Studio no se pudo comunicar con Xcode"

En macOS Catalina o superior, puede encontrar el siguiente error:

[![no se comunica el error ERR](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

En primer lugar, asegúrese de que en las preferencias del sistema de Mac en **seguridad & privacidad > automatización** que aparece Visual Studio y que **Xcode** está activado.

[![seguridad de macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Si se comprueba **Xcode** y aparece el mensaje de error, es posible que tenga que restablecer los permisos de privacidad Visual Studio para Mac.

Esto puede realizarse mediante el inicio de una ventana de terminal y la emisión del comando:

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Para asegurarse de que el cambio anterior surte efecto, restablezca el PRAM de su Mac. [Aquí](https://support.apple.com/HT204063)encontrará instrucciones sobre cómo hacerlo.
