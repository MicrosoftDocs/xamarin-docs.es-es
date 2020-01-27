---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723817"
---
# <a name="android-beam"></a>Android Beam

El rayo de Android es una tecnología de transmisión de datos en proximidad (NFC) introducida en Android 4,0 que permite a las aplicaciones compartir información a través de NFC en estrecha proximidad.

[Diagrama de ![que ilustra dos dispositivos en información de uso compartido de proximidad aproximada](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

El rayo de Android funciona insertando mensajes sobre NFC cuando dos dispositivos están dentro del alcance. Los dispositivos de 4cm entre sí pueden compartir datos con el rayo de Android. Una actividad en un dispositivo crea un mensaje y especifica una actividad (o actividades) que puede controlar la inserción. Cuando la actividad especificada está en primer plano y los dispositivos están dentro del alcance, el rayo de Android enviará el mensaje al segundo dispositivo. En el dispositivo receptor, se invoca una intención que contiene los datos del mensaje.

Android admite dos maneras de configurar mensajes con el rayo de Android:

- `SetNdefPushMessage`: antes de que se inicie el haz de Android, una aplicación puede llamar a SetNdefPushMessage para especificar un NdefMessage de inserción sobre NFC y la actividad que lo inserta. Este mecanismo se utiliza mejor cuando un mensaje no cambia mientras una aplicación está en uso.

- `SetNdefPushMessageCallback`: cuando se inicia el rayo de Android, una aplicación puede controlar una devolución de llamada para crear un NdefMessage. Este mecanismo permite que se retrase la creación de mensajes hasta que los dispositivos se encuentren dentro del alcance. Admite escenarios en los que el mensaje puede variar en función de lo que sucede en la aplicación.

En cualquier caso, para enviar datos con el rayo de Android, una aplicación envía un `NdefMessage`, empaquetando los datos en varios `NdefRecords`. Echemos un vistazo a los puntos clave que deben abordarse antes de que podamos desencadenar el haz de Android. En primer lugar, trabajaremos con el estilo de devolución de llamada de creación de un `NdefMessage`.

## <a name="creating-a-message"></a>Crear un mensaje

Podemos registrar devoluciones de llamada con un `NfcAdapter` en el método de `OnCreate` de la actividad. Por ejemplo, suponiendo que una `NfcAdapter` denominada `mNfcAdapter` se declara como una variable de clase en la actividad, podemos escribir el código siguiente para crear la devolución de llamada que construirá el mensaje:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

La actividad, que implementa `NfcAdapter.ICreateNdefMessageCallback`, se pasa al método `SetNdefPushMessageCallback` anterior. Cuando se inicia el rayo de Android, el sistema llamará `CreateNdefMessage`, desde el que la actividad puede construir un `NdefMessage` como se muestra a continuación:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```

## <a name="receiving-a-message"></a>Recibir un mensaje

En el lado receptor, el sistema invoca una intención con la `ActionNdefDiscovered` acción, desde la que se puede extraer el NdefMessage como se indica a continuación:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obtener un ejemplo de código completo que usa el rayo de Android, que se muestra en la siguiente captura de pantalla, vea la [demostración de rayos de Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) en la galería de ejemplo.

[![capturas de pantallas de ejemplo de la demostración de rayos de Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Demo de rayos de Android (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
