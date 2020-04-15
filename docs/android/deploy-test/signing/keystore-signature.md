---
title: Búsqueda de la firma de su almacén de claves
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 49d67db46416584248a9b15db606538f5ebfba79
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021016"
---
# <a name="finding-your-keystores-signature"></a>Búsqueda de la firma de su almacén de claves

La firma MD5 o SHA1 de una aplicación Xamarin.Android depende del archivo **.keystore** que se usó para firmar el APK. Normalmente, una compilación de depuración usará un archivo **.keystore** diferente de una compilación de versión.

## <a name="for-debug--non-custom-signed-builds"></a>Compilaciones para depurar o firmadas no personalizadas

Xamarin.Android firma todas las compilaciones de depuración con el mismo archivo **debug.keystore**. Este archivo se genera cuando se instala por primera vez Xamarin.Android. Los siguientes pasos detallan el proceso para buscar la firma MD5 o SHA1 de archivo predeterminado de Xamarin.Android **debug.keystore**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Busque el archivo de Xamarin **debug.keystore** que se utiliza para firmar la aplicación. De forma predeterminada, el almacén de claves que se utiliza para firmar las versiones de depuración de una aplicación de Xamarin.Android puede encontrarse en la siguiente ubicación:

**C:\\Usuarios\\*NOMBRE DE USUARIO*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool.exe` desde el JDK. La herramienta normalmente se encuentra en la siguiente ubicación:

**C:\\Archivos de programa (x86)\\Java\\jdk*VERSIÓN*\\bin\\keytool.exe**

Agregue el directorio que contiene **keytool.exe** a la variable de entorno `PATH`.
Abra una **ventana del símbolo del sistema** y ejecute `keytool.exe` mediante el siguiente comando:

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

Cuando se ejecuta, **keytool.exe** debe dar como resultado el siguiente texto. Las etiquetas **MD5:** y **SHA1:** identifican las firmas correspondientes:

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Busque el archivo de Xamarin **debug.keystore** que se utiliza para firmar la aplicación. De forma predeterminada, el almacén de claves que se utiliza para firmar las versiones de depuración de una aplicación de Xamarin.Android puede encontrarse en la siguiente ubicación:

**~/.local/share/Xamarin/Mono for Android/debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando **keytool** desde el JDK. La herramienta normalmente se encuentra en la siguiente ubicación:

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

Agregue el directorio que contiene **keytool** a la variable de entorno **PATH**.
Abra un **Terminal** y ejecute **keytool** mediante el siguiente comando:

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

Cuando se ejecute, **keytool** debe dar como resultado el siguiente texto. Las etiquetas **MD5:** y **SHA1:** identifican las firmas correspondientes:

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>Compilaciones de versión o firmadas personalizadas

Los procesos para crear compilaciones de versión que se firman con un archivo **.keystore** personalizado son los mismos que los explicados anteriormente, con la versión del archivo **.keystore** reemplazando el archivo **debug.keystore** que utiliza Xamarin.Android. Sustituya sus propios valores para la contraseña de KeyStore y el nombre de alias utilizado cuando se creó el archivo del almacén de claves de la versión.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Cuando se utilice el asistente de Visual Studio **Distribuir** para firmar una aplicación de Xamarin.Android, encontrará el almacén de claves resultante en la siguiente ubicación:

**C:\\Usuarios\\*NOMBRE DE USUARIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*alias*\\*alias*.keystore**

Por ejemplo, si ha seguido los pasos de [Crear un nuevo certificado](~/android/deploy-test/signing/index.md#newcertvs) para crear una nueva clave de firma, el almacén de claves de ejemplo resultante reside en la siguiente ubicación:

**C:\\Usuarios\\*NOMBRE DE USUARIO*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

Para más información sobre cómo firmar una aplicación de Xamarin.Android, vea [Firmar el paquete de aplicación de Android](~/android/deploy-test/signing/index.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Cuando utilice el asistente de Visual Studio para Mac **Firmar y distribuir...** para firmar la aplicación, encontrará el almacén de claves resultante en la siguiente ubicación:

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

Por ejemplo, si ha seguido los pasos de [Crear un nuevo certificado](~/android/deploy-test/signing/index.md#newcertxs) para crear una nueva clave de firma, el almacén de claves de ejemplo resultante reside en la siguiente ubicación:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

Para más información sobre cómo firmar una aplicación de Xamarin.Android, vea [Firmar el paquete de aplicación de Android](~/android/deploy-test/signing/index.md).

-----
