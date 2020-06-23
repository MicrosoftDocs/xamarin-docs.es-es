---
title: 'Xamarin.Essentials: Almacenamiento seguro'
description: En este documento se describe la clase SecureStorage de Xamarin.Essentials, que ayuda a almacenar pares clave-valor sencillos de manera segura. Describe cómo usar la clase, especificaciones de implementación de la plataforma y limitaciones.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 061bc1cfe785ad080092ba21340f7d38bc499ed9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801951"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Almacenamiento seguro

La clase **SecureStorage** ayuda a almacenar pares de clave-valor sencillos de manera segura.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la funcionalidad **SecureStorage**, se requiere la siguiente configuración específica para la plataforma:

# <a name="android"></a>[Android](#tab/android)

> [!TIP]
> [Copia de seguridad para aplicaciones](https://developer.android.com/guide/topics/data/autobackup) es una característica de Android 6.0 (nivel de API 23) y versiones posteriores que crea copias de seguridad de los datos de aplicación del usuario (preferencias compartidas, archivos en el almacenamiento interno de la aplicación y otros archivos específicos). Los datos se restauran cuando se reinstala o instala una aplicación en un dispositivo nuevo. Esto puede afectar a `SecureStorage`, que utiliza las preferencias compartidas de las que se creó una copia de seguridad y que no se pueden descifrar cuando se realiza la restauración. Xamarin.Essentials controla automáticamente este caso quitando la clave para que se pueda restablecer, pero puede dar un paso adicional deshabilitando Copia de seguridad automática.

### <a name="enable-or-disable-backup"></a>Habilitación o deshabilitación de copia de seguridad
Puede elegir deshabilitar Copia de seguridad automática para toda la aplicación al establecer el valor `android:allowBackup` en false en el archivo `AndroidManifest.xml`. Este enfoque solo se recomienda si planea restaurar los datos de otra manera.

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>Copia de seguridad selectiva
Es posible configurar Copia de seguridad automática para deshabilitar la copia de seguridad de contenido específico. Puede crear un conjunto de reglas personalizadas para excluir los elementos `SecureStore` de la copia de seguridad.

1. Establezca el atributo `android:fullBackupContent` en **AndroidManifest.xml**:

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. Cree un archivo XML denominado **auto_backup_rules.xml** en el directorio **Resources/xml** con la acción de compilación **AndroidResource**. Luego, establezca este contenido que incluye todas las preferencias compartidas, excepto `SecureStorage`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="ios"></a>[iOS](#tab/ios)

Cuando desarrolle en el **emulador de iOS**, habilite el derecho **Keychain** y agregue un grupo de acceso de cadena de claves para el identificador de agrupación de la aplicación.

Abra **Entitlements.plist** en el proyecto de iOS, busque el derecho **Keychain** y habilítelo. Esto agregará automáticamente el identificador de la aplicación como grupo.

En las propiedades del proyecto, en **iOS Bundle Signing** (Firma de agrupación de iOS), establezca **Custom Entitlements** (Derecho personalizado) en **Entitlements.plist**.

> [!TIP]
> Al implementar en un dispositivo iOS, no es necesario este derecho y se debe quitar.

# <a name="uwp"></a>[UWP](#tab/uwp)

No se requiere configuración adicional.

-----

## <a name="using-secure-storage"></a>Uso de Secure Storage

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Para guardar un valor para una _clave_ determinada en el almacenamiento seguro:

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

Para recuperar un valor desde el almacenamiento seguro:

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> Si no hay ningún valor asociado con la clave solicitada, `GetAsync` devolverá `null`.

Para quitar una clave específica, llame a:

```csharp
SecureStorage.Remove("oauth_token");
```

Para quitar todas las claves, llame a:

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>Detalles de implementación de la plataforma

# <a name="android"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html) se usa para almacenar la clave de cifrado con la que se cifra el valor antes de guardarlo en [Preferencias compartidas](https://developer.android.com/training/data-storage/shared-preferences.html) con un nombre de archivo **[ID-PAQUETE-APLICACIÓN].xamarinessentials**.  La clave (no una clave criptográfica, la _clave_ para el _valor_) usada en el archivo de preferencias compartido es un _hash MD5_ de la clave pasada a las API `SecureStorage`.

**Nivel de API 23 y superior**

En los niveles de API más nuevos, una clave **AES** se obtiene de Android KeyStore y se usa con una cifra **AES/GCM/NoPadding** para cifrar el valor antes de que se almacene en el archivo de preferencias compartidas.

**Nivel de API 22 e inferior**

En los niveles de API anteriores, Android KeyStore solo admite el almacenamiento de claves **RSA**, que se usa con una cifra **RSA/ECB/PKCS1Padding** para cifrar una clave **AES** (generada de manera aleatoria en tiempo de ejecución) y se almacena en el archivo de preferencias compartidas en la clave _SecureStorageKey_, si todavía no se ha generado una.

**SecureStorage** usa la API [Preferences](preferences.md) y sigue la misma persistencia de datos que se describe en la documentación sobre [Preferencias](preferences.md#persistence). Si se actualiza un dispositivo desde Nivel de API 22 o inferior a Nivel de API 23 y superior, se seguirá usando este tipo de cifrado a menos que la aplicación se desinstale o se llame a **RemoveAll**.

# <a name="ios"></a>[iOS](#tab/ios)

[KeyChain](xref:Security.SecKeyChain) se usa para almacenar valores de manera segura en dispositivos iOS.  El elemento `SecRecord` que se usa para almacenar el valor tiene un valor `Service` establecido en **[ID-PAQUETE-APLICACIÓN].xamarinessentials**.

En algunos casos, los datos de KeyChain se sincronizan con iCloud y es posible que la desinstalación de la aplicación no quite los valores seguros de iCloud ni otros dispositivos del usuario.

# <a name="uwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) se usa para cifrar valores de manera segura en dispositivos de UWP.

Los valores cifrados se almacenan en `ApplicationData.Current.LocalSettings`, dentro de un contenedor con el nombre **[ID-APLICACIÓN].xamarinessentials**.

**SecureStorage** usa la API [Preferences](preferences.md) y sigue la misma persistencia de datos que se describe en la documentación sobre [Preferencias](preferences.md#persistence). También utiliza `LocalSettings` que tiene la restricción de que el nombre de cada valor puede tener una longitud máxima de 255 caracteres. Cada valor puede tener un tamaño máximo de 8 KB y cada valor compuesto puede tener un tamaño máximo de 64 KB.

-----

## <a name="limitations"></a>Limitaciones

Esta API está pensada para almacenar pequeñas cantidades de texto.  El rendimiento puede ser lento si intenta usarla para almacenar grandes cantidades de texto.

## <a name="api"></a>API

- [Código fuente de SecureStorage](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/SecureStorage)
- [Documentación de API de SecureStorage](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
