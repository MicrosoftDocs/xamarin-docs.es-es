---
title: ¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e3b535b8624f03e9c832f1719237409cb9ff26c2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939911"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>¿Puedo agregar o quitar archivos de un archivo IPA después de compilarlos en Visual Studio?

Sí, es posible, pero normalmente será necesario volver a firmar la `.app` agrupación después de realizar el cambio.

Tenga en cuenta que `.ipa` no es necesario modificar el archivo en el uso normal. Este artículo se proporciona únicamente con fines informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Ejemplo: `.ipa` quitar un archivo de un archivo

En este ejemplo se supone que el nombre del proyecto de Xamarin. iOS es `iPhoneApp1` y que `generated session id` es`cc530d20d6b19da63f6f1c6f67a0a254`

1. Compile el `.ipa` archivo como normal desde Visual Studio.

2. Cambie al host de compilación de Mac.

3. Busque la compilación en la `~/Library/Caches/Xamarin/mtbs/builds` carpeta. Puede pegar esta ruta de acceso en **finder > ir > ir a la carpeta** para examinar la carpeta en Finder. Busque la carpeta que coincida con el nombre del proyecto. Dentro de esa carpeta, busque la carpeta que coincida con la `generated session id` de la compilación. Lo más probable es que la subcarpeta tenga la hora de modificación más reciente.

4. Abra una nueva `Terminal.app` ventana.

5. Escriba `cd` en la ventana de terminal. app y, a continuación, arrastre & Coloque la `generated session id` carpeta en la `Terminal.app` ventana:

    ![Buscar la carpeta de ID. de sesión generada en el buscador](modify-ipa-images/session-id-folder.png)

6. Escriba la clave de retorno para cambiar el directorio a la `generated session id` carpeta.

7. Descomprima el `.ipa` archivo en una `old/` carpeta temporal con el siguiente comando. Ajuste los `Ad-Hoc` `iPhoneApp1` nombres y según sea necesario para su proyecto en particular.

    > Ditto-x KB bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. IPA Old/

8. Mantenga la `Terminal.app` ventana abierta.

9. Elimine los archivos deseados de la `.ipa` . Puede moverlos a la papelera mediante el buscador o eliminarlos en la línea de comandos mediante `Terminal.app` . Para ver el contenido del `Payload/iPhone` archivo en el buscador, haga clic en el archivo y seleccione **Mostrar contenido del paquete**.

10. Con el mismo enfoque general que en el paso 3, busque el archivo de registro en `~/Library/Logs/Xamarin/MonoTouchVS/` que tiene el nombre del proyecto y el `generated session id` en el nombre: ![ Busque el registro de compilación del proyecto en Finder](modify-ipa-images/build-log.png)

11. Abra el registro de compilación del paso 10, por ejemplo, haciendo doble clic en él.

12. Busque la línea que incluye `tool /usr/bin/codesign execution started with arguments: -v --force --sign` .

13. Escriba `/usr/bin/codesign` en la ventana de terminal. app del paso 8.

14. Copie todos los argumentos que comienzan por `-v` desde la línea del paso 12 y péguelos en la ventana terminal. app.

15. Cambie el último argumento a la `.app` agrupación que se encuentra dentro de la `old/Payload/` carpeta y, a continuación, ejecute el comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Cambie al `old/` directorio en terminal:

    ```bash
    cd old
    ```

17. Comprimir el contenido del directorio en un `.ipa` archivo nuevo con el `zip` comando. Puede cambiar el `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para generar el `.ipa` archivo donde quiera:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Mensajes comunes de error

Si ve `Invalid Signature. A sealed resource is missing or invalid.` , eso suele significar que se ha cambiado algo dentro de la `.app` agrupación y que el `.app` paquete no se ha vuelto a firmar correctamente después. Tenga en cuenta también que si desea crear un `.ipa` con un perfil de distribución, _debe_ compilar el original `.ipa` con un perfil de distribución. De lo contrario, `Entitlements.xcent` será incorrecto.

Para obtener un ejemplo concreto de cómo puede producirse este error, si ejecuta el siguiente `codesign --verify` comando en la ventana de terminal después del paso 9, verá el error junto con la causa exacta del error:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Y el proceso de comprobación de App Store notificará un mensaje de error similar:

> ERROR ITMS-90035: "signatura no válida. Falta un recurso sellado o no es válido. El archivo binario de la ruta de acceso [iPhoneApp1. app/iPhoneApp1] contiene una firma no válida. Asegúrese de que ha firmado la aplicación con un certificado de distribución, no un certificado ad hoc o un certificado de desarrollo. Compruebe que la configuración de la firma de código en Xcode sea correcta en el nivel de destino (lo que invalidará los valores en el nivel de proyecto). Además, asegúrese de que el paquete que está cargando se compiló con un destino de versión en Xcode, no un destino de simulador. Si está seguro de que la configuración de la firma de código es correcta, elija "limpiar todo" en Xcode, elimine el directorio "compilar" en el buscador y recompile el destino de la versión. Para obtener más información, consulte [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html) "
