---
title: NFC principal en Xamarin. iOS
description: En este documento se describe cómo leer etiquetas de comunicación de campo en Xamarin. iOS mediante las API introducidas en iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 2e19fd37270d3c96cb175d30dc786a95a01c3fcf
ms.sourcegitcommit: 2ed3d1c933fce4ce332128f125acb2f23f9e0f1a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753016"
---
# <a name="core-nfc-in-xamarinios"></a>NFC principal en Xamarin. iOS

_Lectura de etiquetas de transmisión de campos en proximidad (NFC) mediante iOS 11_

CoreNFC es un nuevo marco de trabajo de iOS 11 que proporciona acceso a la radio de _transmisión de campos en proximidad_ (NFC) para leer etiquetas de dentro de las aplicaciones. CoreNFC funciona en iPhone 7, iPhone 7 Plus, iPhone 8, iPhone 8 Plus, iPhone X, iPhone XS y iPhone 11 Models (mientras que los modelos iPhone 6 y iPhone 6 más tienen la funcionalidad de pago de NFC, no admiten CoreNFC).

El lector de etiquetas NFC en dispositivos iOS es compatible con todos los tipos de etiqueta NFC de 1 a 5 que contienen información del _formato de intercambio de datos NFC_ (NDEF).

Hay algunas restricciones que se deben tener en cuenta:

- CoreNFC solo admite la lectura de etiquetas (sin escritura ni formato).
- Los recorridos de etiquetas deben ser iniciados por el usuario y agotarse el tiempo de espera después de 60 segundos.
- Las aplicaciones deben estar visibles en primer plano para su análisis.
- CoreNFC solo se puede probar en dispositivos reales (no en el simulador).

En esta página se describe la configuración necesaria para usar CoreNFC y se muestra cómo usar la API con el [código de ejemplo "NFCTagReader"](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader).

## <a name="configuration"></a>Configuración de

Para habilitar CoreNFC, debe configurar tres elementos en el proyecto:

- Una clave de privacidad **info. plist** .
- Una entrada de **derechos. plist** .
- Un perfil de aprovisionamiento con capacidad de **lectura de etiquetas NFC** .

### <a name="infoplist"></a>Info.plist

Agregue la clave de privacidad y el texto de **NFCReaderUsageDescription** , que se muestra al usuario mientras se está produciendo el análisis. Use un mensaje adecuado para su aplicación (por ejemplo, explique la finalidad del análisis):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

La aplicación debe solicitar la funcionalidad de **lectura de etiquetas Near Field Communications** con el siguiente par clave-valor en los **derechos. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Perfil de aprovisionamiento

Cree un nuevo **identificador de aplicación** y asegúrese de que el servicio de **lectura de etiquetas NFC** esté marcado:

[![página nuevo ID. de aplicación del portal para desarrolladores con la lectura de etiquetas NFC seleccionada](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Después, debe crear un nuevo perfil de aprovisionamiento para este identificador de aplicación y, después, descargarlo e instalarlo en el equipo Mac de desarrollo.

## <a name="reading-a-tag"></a>Leer una etiqueta

Una vez configurado el proyecto, agregue `using CoreNFC;` a la parte superior del archivo y siga estos tres pasos para implementar la funcionalidad de lectura de etiquetas NFC:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. implementar `INFCNdefReaderSessionDelegate`

La interfaz tiene dos métodos que se van a implementar:

- `DidDetect`: se le llama cuando se lee correctamente una etiqueta.
- `DidInvalidate`: se le llama cuando se produce un error o se alcanza el tiempo de espera de 60 segundos.

#### <a name="diddetect"></a>DidDetect

En el código de ejemplo, cada mensaje examinado se agrega a una vista de tabla:

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Se puede llamar varias veces a este método (y se puede pasar una matriz de mensajes) si la sesión permite varias lecturas de etiquetas. Esto se establece mediante el tercer parámetro del método `Start` (que se explica en el [paso 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

La invalidación puede producirse por varias razones:

- Error al examinar.
- La aplicación dejó de estar en primer plano.
- El usuario decidió cancelar el examen.
- La aplicación canceló el examen.

En el código siguiente se muestra cómo controlar un error:

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Una vez que se ha invalidado una sesión, se debe crear un nuevo objeto de sesión para examinar de nuevo.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. iniciar un `NFCNdefReaderSession`

El análisis debe comenzar con una solicitud de usuario, como presionar un botón.
El código siguiente crea e inicia una sesión de examen:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Los parámetros del constructor `NFCNdefReaderSession` son los siguientes:

- `delegate`: una implementación de `INFCNdefReaderSessionDelegate`. En el código de ejemplo, el delegado se implementa en el controlador de vista de tabla, por lo que `this` se utiliza como parámetro de delegado.
- `queue`: la cola en la que se administran las devoluciones de llamada. Puede ser `null`, en cuyo caso asegúrese de usar el `DispatchQueue.MainQueue` al actualizar los controles de la interfaz de usuario (como se muestra en el ejemplo).
- `invalidateAfterFirstRead`: cuando `true`, el examen se detiene después del primer análisis correcto; Cuando el análisis de `false` continuará y se devuelvan varios resultados hasta que se cancele el examen o se alcance el tiempo de espera de 60 segundos.

### <a name="3-cancel-the-scanning-session"></a>3. cancelar la sesión de examen

El usuario puede cancelar la sesión de detección a través de un botón proporcionado por el sistema en la interfaz de usuario:

![Botón Cancelar durante el examen](corenfc-images/scan-cancel-sml.png)

La aplicación puede cancelar mediante programación el análisis llamando al método `InvalidateSession`:

```csharp
Session.InvalidateSession();
```

En ambos casos, se llamará al método `DidInvalidate` del delegado.

## <a name="summary"></a>Resumen

CoreNFC permite que la aplicación Lea datos de etiquetas NFC. Admite la lectura de una variedad de formatos de etiqueta (tipos NDEF 1 a 5), pero no admite la escritura o el formato.

## <a name="related-links"></a>Vínculos relacionados

- [NFCTagReader (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)
- [Introducción al NFC básico (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/718/)
