---
title: Opciones del enlazador de Xamarin.Mac
description: En este documento se describe la vinculación en Xamarin.Mac. La vinculación es una herramienta eficaz de optimización que reduce el tamaño de la aplicación mediante la eliminación de código no utilizado.
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 00d5d5cf9cda38fbfc09fcfe8b4dcb56d00dee5b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291424"
---
# <a name="xamarinmac-linker-options"></a>Opciones del enlazador de Xamarin.Mac

_La vinculación es una herramienta eficaz de optimización que reduce el tamaño de la aplicación mediante la eliminación de código no utilizado._

## <a name="overview"></a>Información general

En función del [marco de destino](~/mac/platform/target-framework.md) que utilice el proyecto, es posible que las opciones del limitador estén limitadas. Esto se debe al hecho de que la vinculación requiere la creación de un gráfico de objetos de cada tipo utilizado por la aplicación, y esto no es posible en Full (o Unsupported) debido a System.Configuration.

Hay cuatro opciones disponibles:

- **None**: deshabilitar todos los vínculos. Es el valor predeterminado en el modo Debug en Modern y en todas las configuraciones en Full.
- **SDK** : vincular todos los ensamblados SDK, sin incluir los ensamblados de usuario. Es el valor predeterminado en la configuración de Release en el modo Modern. No está disponible en Full.
- **Full**: vincular todos los ensamblados. Para ello, se necesita que el código de usuario sea seguro para el enlazador; vea las [notas](~/ios/deploy-test/linker.md) para obtener más información. No está disponible en Full.
- **Plataform**: vincular simplemente Xamarin.Mac.dll. Para obtener información más detallada, vea a continuación.

## <a name="platform-linking"></a>Vinculación de plataforma

La vinculación de las aplicaciones que usan el [marco de destino](~/mac/platform/target-framework.md) Full generalmente no es segura, pero hay una serie de situaciones en las que se requiere una forma muy limitada de vinculación.

Por ejemplo, las aplicaciones que se envían al App Store de macOS no deben hacer referencia a un número de interfaces API obsoletas (por ejemplo, QTKit), de algunas de las cuales Xamarin.Mac contiene enlaces. Incluso si una aplicación no llama a estos enlaces, la invocación existirá en el SDK y se rechazará.

La vinculación de plataforma supone que la aplicación y BCL no son seguros para el enlazador y simplemente se quita el código no utilizado de Xamarin.Mac.dll. 

Cualquier aplicación que no se refleje en los tipos de Xamarin.Mac.dll verán una pequeña mejora en el inicio a partir de la eliminación de tipos innecesarios.

La vinculación de plataforma generalmente sólo es útil para aplicaciones que utilizan el marco de destino Full, ya que la aplicación Modern puede utilizar la opción SDK más eficaz.

## <a name="setting-the-linker-configuration"></a>Configuración del enlazador

Para cambiar a la configuración del enlazador para un proyecto de Xamarin.Mac, realice lo siguiente:

1. Abra el proyecto de Xamarin.Mac en Visual Studio para Mac.
2. En el **Explorador de soluciones**, haga doble clic en el archivo de proyecto para abrir el cuadro de diálogo **Opciones de proyecto**.
3. En la pestaña **Compilación para Mac**, seleccione el tipo de **Comportamiento del enlazador** que se adapte a las necesidades de su aplicación:

    ![Elegir qué comportamiento de enlazador se utiliza](linker-images/link-behavior.png "Elegir qué comportamiento de enlazador se utiliza")

4. La vinculación de plataforma para los marcos de destino Full no aparecerán en el IDE hasta una futura actualización. Hasta entonces, agregue `--linkplatform` a los **argumentos de mmp adicionales** en su lugar.
5. Haga clic en el botón **Aceptar** para guardar los cambios.


## <a name="related-links"></a>Vínculos relacionados

- [Vincular en iOS](~/ios/deploy-test/linker.md)
