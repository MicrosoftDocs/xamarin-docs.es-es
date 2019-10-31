---
title: Firmar el paquete de aplicación de Android
description: Cómo firmar el paquete de aplicaciones Android (APK) para la publicación
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/02/2018
ms.openlocfilehash: b646893d8bbe63aa537c973c90552e0367735e47
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021219"
---
# <a name="signing-the-android-application-package"></a>Firmar el paquete de aplicación de Android

En [Preparar una aplicación para su lanzamiento](~/android/deploy-test/release-prep/index.md), ha usado **Archive Manager** para compilar la aplicación y colocarla en un archivo para su firma y publicación. En esta sección, obtendrá información sobre cómo crear una identidad de firma de Android, crear un nuevo certificado de firma para aplicaciones de Android y publicar la aplicación archivada *ad hoc* en disco. Se puede realizar una instalación de prueba del APK resultante en dispositivos Android sin pasar por una tienda de aplicaciones.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En [Archive for Publishing](~/android/deploy-test/release-prep/index.md#archive) (Archivo para publicar), el cuadro de diálogo **Canal de distribución** ofrece dos opciones para la distribución. Seleccione **Ad-Hoc** (Ad hoc):

[![Cuadro de diálogo Canal de distribución](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En [Archive for Publishing](~/android/deploy-test/release-prep/index.md#archive) (Archivo para publicar), el cuadro de diálogo **Sign and Distribute…** (Firmar y distribuir…) ofrece dos opciones para la distribución. Seleccione **Ad-Hoc** (Ad hoc) y haga clic en **Siguiente**:

[![Cuadro de diálogo Firmar y distribuir](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs" />
<a name="newcert" />
<a name="newcertxs" />

## <a name="create-a-new-certificate"></a>Crear un certificado

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una vez que haya seleccionado **Ad-Hoc** (Ad hoc), Visual Studio abre la página **Identidad de firma** del cuadro de diálogo, como se muestra en la captura de pantalla siguiente. Para publicar el .APK, debe estar firmado con una clave de firma (también denominada certificado).

Para usar un certificado existente, haga clic en el botón **Importar** y, después, seleccione [Firmar el APK](#sign-the-apk). En caso contrario, haga clic en el botón **+** para crear un certificado:

[![Identidad de firma ad hoc](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

Se muestra el cuadro de diálogo **Create Android Key Store** (Crear almacén de claves de Android). Úselo para crear un certificado de firma que se pueda usar para firmar aplicaciones de Android. Escriba la información necesaria (resaltada en rojo), como se muestra en este cuadro de diálogo:

[![Cuadro de diálogo Create Android Key Store (Crear almacén de claves de Android)](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

En el ejemplo siguiente se muestra el tipo de información que se debe proporcionar. Haga clic en **Crear** para crear el certificado:

[![Creación de un certificado nuevo](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

El almacén de claves resultante se encuentra en la ubicación siguiente:

**C:\\Usuarios\\*NOMBRE DE USUARIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*ALIAS*\\*ALIAS*.keystore**

Por ejemplo, mediante el uso de **chimp** como alias, los pasos anteriores podrían crear una clave de firma en la ubicación siguiente:

**C:\\Usuarios\\*NOMBRE DE USUARIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

> [!NOTE]
> Debe crear una copia de seguridad del archivo de almacén de claves y la contraseña resultantes en un lugar seguro, ya que no se incluye en la solución. Si pierde el archivo (por ejemplo, porque ha cambiado de equipo o porque ha reinstalado Windows), no podrá firmar la aplicación con el mismo certificado que las versiones anteriores.

Para obtener más información sobre el almacén de claves, consulte [Finding your Keystore's MD5 or SHA1 Signature](~/android/deploy-test/signing/keystore-signature.md) (Buscar la firma MD5 o SHA1 del almacén de claves).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Después de hacer clic en **Ad-Hoc** (Ad hoc), Visual Studio para Mac abre el cuadro de diálogo **Identidad de firma de Android**, como se muestra en la siguiente captura de pantalla. Para publicar el .APK, debe estar firmado con una clave de firma (también denominada certificado). Si ya existe un certificado, haga clic en el botón **Importar una clave existente** para importarlo y, después, seleccione [Firmar el APK](#sign-the-apk). En caso contrario, haga clic en el botón **Crear una clave nueva** para crear un certificado:

[![Cuadro de diálogo Identidad de firma para Android](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

El cuadro de diálogo **Crear nuevo certificado** se usa para crear un certificado de firma que se puede usar para firmar aplicaciones de Android. Haga clic en **Aceptar** después de especificar la información necesaria:

[![Cuadro de diálogo Crear nuevo certificado](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

El almacén de claves resultante se encuentra en la ubicación siguiente:

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

Por ejemplo, los pasos anteriores podrían crear una clave de firma en la ubicación siguiente:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

> [!NOTE]
> Debe crear una copia de seguridad del archivo de almacén de claves y la contraseña resultantes en un lugar seguro, ya que no se incluye en la solución. Si pierde el archivo (por ejemplo, porque ha cambiado de equipo o porque ha reinstalado macOS), no podrá firmar la aplicación con el mismo certificado que las versiones anteriores.

Para obtener más información sobre el almacén de claves, consulte [Finding your Keystore's MD5 or SHA1 Signature](~/android/deploy-test/signing/keystore-signature.md) (Buscar la firma MD5 o SHA1 del almacén de claves).

-----

## <a name="sign-the-apk"></a>Firmar el APK

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Al hacer clic en **Crear**, se guardará un nuevo almacén de claves (con un nuevo certificado). Aparecerá en **Identidad de firma**, como se muestra en la captura de pantalla siguiente. Para publicar una aplicación en Google Play, haga clic en **Cancelar** y vaya a [Publicación en Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Para publicar *ad hoc*, seleccione la identidad de firma que se va a usar para firmar y haga clic en **Guardar como** para publicar la aplicación para su distribución independiente. Por ejemplo, en esta captura de pantalla se ha seleccionado la identidad de firma **chimp** (creada anteriormente):

[![Ejemplo de identidad de firma](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

Después, el **administrador de archivos** muestra el progreso de la publicación. Cuando se completa el proceso de publicación, se abre el cuadro de diálogo **Guardar como** para solicitar la ubicación donde se almacenará el archivo .APK generado:

[![Cuadro de diálogo Guardar como](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

Vaya a la ubicación deseada y haga clic en **Guardar**. Si la contraseña de la clave es desconocida, aparecerá el cuadro de diálogo **Contraseña de firma**, en el que se le solicitará la contraseña del certificado seleccionado:

[![Cuadro de diálogo Contraseña de firma](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

Al finalizar el proceso de firma, haga clic en **Abrir distribución**:

[![Botón Abrir distribución](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

Esto hace que el Explorador de Windows abra la carpeta que contiene el archivo APK generado. Llegados a este punto, Visual Studio ha compilado la aplicación de Xamarin.Android en un APK listo para su distribución.
En la captura de pantalla siguiente se muestra un ejemplo de la aplicación lista para su publicación, **MyApp.MyApp.apk**:

[![APK en el Explorador de Windows](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Tal como se muestra aquí, se ha agregado un nuevo certificado en el almacén de claves. Para publicar una aplicación en Google Play, haga clic en **Cancelar** y vaya a [Publicación en Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
En caso contrario, haga clic en **Siguiente** para publicar la aplicación *ad hoc* (para la distribución independiente) como se muestra en este ejemplo:

[![Cuadro de diálogo Firmar y distribuir](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

En el cuadro de diálogo **Publish as Ad Hoc** (Publicar como ad hoc) se incluye un resumen de la aplicación firmada antes de su publicación. Si esta información es correcta, haga clic en **Publicar**.

[![Cuadro de diálogo Publicar como ad hoc](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

El cuadro de diálogo **Output APK file** (Archivo APK de salida) guardará el APK en la ruta especificada. Haga clic en **Guardar**.

![Cuadro de diálogo Archivo APK de salida](images/xs/06-output-apk-file.png)

Después, escriba la contraseña del certificado (la contraseña que se usó en el cuadro de diálogo **Crear nuevo certificado**) y haga clic en **Aceptar**:

![Escribir la contraseña del certificado](images/xs/07-signing-certificate.png)

El APK se firma con el certificado y se guarda en la ubicación especificada. Haga clic en **Mostrar en el Finder**:

[![Cuadro de diálogo de publicación correcta](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

Se abrirá el Finder en la ubicación del archivo APK firmado:

[![APK en Finder](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

El APK ya se puede copiar en el Finder y enviar a su destino final. Es conveniente instalar el APK en un dispositivo Android y probarlo antes de la distribución. Vea [Publicación de forma independiente](~/android/deploy-test/publishing/publishing-independently.md) para más información sobre cómo publicar un APK *ad hoc*.

-----

## <a name="next-steps"></a>Pasos siguientes

Una vez que el paquete de aplicación se haya firmado para su lanzamiento, es necesario publicarlo. En las secciones siguientes se describen varias formas de publicar una aplicación.
