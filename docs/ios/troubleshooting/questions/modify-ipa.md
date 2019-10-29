---
title: ¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: f63cc544b251ca284a8d087e09f9cbc4dfb3f769
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030957"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?

Sí, es posible, pero normalmente será necesario volver a firmar el paquete de `.app` después de realizar el cambio.

Tenga en cuenta que no es necesario modificar el archivo `.ipa` en el uso normal. Este artículo se proporciona únicamente con fines informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Ejemplo: quitar un archivo de un archivo `.ipa`

En este ejemplo se da por supuesto que el nombre del proyecto de Xamarin. iOS es `iPhoneApp1` y el `generated session id` es `cc530d20d6b19da63f6f1c6f67a0a254`

1. Cree el archivo de `.ipa` como normal desde Visual Studio.

2. Cambie al host de compilación de Mac.

3. Busque la compilación en la carpeta `~/Library/Caches/Xamarin/mtbs/builds`. Puede pegar esta ruta de acceso en **finder > ir > ir a la carpeta** para examinar la carpeta en Finder. Busque la carpeta que coincida con el nombre del proyecto. Dentro de esa carpeta, busque la carpeta que coincida con la `generated session id` de la compilación. Lo más probable es que la subcarpeta tenga la hora de modificación más reciente.

4. Abra una nueva ventana de `Terminal.app`.

5. Escriba `cd` en la ventana terminal. app y, a continuación, arrastre & Coloque la carpeta de `generated session id` en la ventana de `Terminal.app`:

    ![](modify-ipa-images/session-id-folder.png "Locating the generated session id folder in Finder")

6. Escriba la clave de retorno para cambiar el directorio a la carpeta `generated session id`.

7. Descomprima el archivo de `.ipa` en una carpeta de `old/` temporal con el siguiente comando. Ajuste los nombres de `Ad-Hoc` y `iPhoneApp1` según sea necesario para su proyecto en particular.

    > Ditto-x KB bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. IPA Old/

8. Mantenga la ventana de `Terminal.app` abierta.

9. Elimine los archivos deseados del `.ipa`. Puede moverlos a la papelera mediante el buscador, o bien eliminarlos en la línea de comandos mediante `Terminal.app`. Para ver el contenido del archivo de `Payload/iPhone` en Finder, haga clic en el archivo y seleccione **Mostrar contenido del paquete**.

10. Con el mismo enfoque general que en el paso 3, busque el archivo de registro en `~/Library/Logs/Xamarin/MonoTouchVS/` que tenga el nombre del proyecto y el `generated session id` en el nombre:![](modify-ipa-images/build-log.png "Busque el registro de compilación del proyecto en Finder")

11. Abra el registro de compilación del paso 10, por ejemplo, haciendo doble clic en él.

12. Busque la línea que incluye `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Escriba `/usr/bin/codesign` en la ventana terminal. app del paso 8.

14. Copie todos los argumentos a partir de `-v` de la línea del paso 12 y péguelos en la ventana terminal. app.

15. Cambie el último argumento a la `.app` agrupación ubicada en la carpeta `old/Payload/` y, a continuación, ejecute el comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Cambie al directorio de `old/` en terminal:

    ```bash
    cd old
    ```

17. Comprimir el contenido del directorio en un nuevo archivo de `.ipa` mediante el comando `zip`. Puede cambiar el argumento de `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` para que se genere el archivo `.ipa` siempre que lo desee:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Mensajes de error comunes

Si ve `Invalid Signature. A sealed resource is missing or invalid.`, eso significa que se ha cambiado algo en el paquete de `.app` y que el paquete de `.app` no se ha vuelto a firmar correctamente después. Tenga en cuenta también que si desea crear un `.ipa` con un perfil de distribución, _debe_ generar el `.ipa` original con un perfil de distribución. De lo contrario, el `Entitlements.xcent` será incorrecto.

Para obtener un ejemplo concreto de cómo puede producirse este error, si ejecuta el siguiente comando de `codesign --verify` en la ventana de terminal después del paso 9, verá el error junto con la causa exacta del error:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Y el proceso de comprobación de App Store notificará un mensaje de error similar:

> ERROR ITMS-90035: "signatura no válida. Falta un recurso sellado o no es válido. El archivo binario de la ruta de acceso [iPhoneApp1. app/iPhoneApp1] contiene una firma no válida. Asegúrese de que ha firmado la aplicación con un certificado de distribución, no un certificado ad hoc o un certificado de desarrollo. Compruebe que la configuración de la firma de código en Xcode sea correcta en el nivel de destino (lo que invalidará los valores en el nivel de proyecto). Además, asegúrese de que el paquete que está cargando se compiló con un destino de versión en Xcode, no un destino de simulador. Si está seguro de que la configuración de la firma de código es correcta, elija "limpiar todo" en Xcode, elimine el directorio "compilar" en el buscador y recompile el destino de la versión. Para obtener más información, consulte [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)".
