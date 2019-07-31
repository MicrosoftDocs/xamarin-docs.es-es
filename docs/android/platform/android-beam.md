---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 0c4f7303d3620dcc2c829d732fe7a5f97f0e3883
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643758"
---
# <a name="android-beam"></a>Android Beam

El rayo de Android es una tecnología de transmisión de datos en proximidad (NFC) introducida en Android 4,0 que permite a las aplicaciones compartir información a través de NFC en estrecha proximidad.

[![Diagrama que ilustra dos dispositivos en información de uso compartido de proximidad aproximada](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

El rayo de Android funciona insertando mensajes sobre NFC cuando dos dispositivos están dentro del alcance. Los dispositivos de 4cm entre sí pueden compartir datos con el rayo de Android. Una actividad en un dispositivo crea un mensaje y especifica una actividad (o actividades) que puede controlar la inserción. Cuando la actividad especificada está en primer plano y los dispositivos están dentro del alcance, el rayo de Android enviará el mensaje al segundo dispositivo. En el dispositivo receptor, se invoca una intención que contiene los datos del mensaje.

Android admite dos maneras de configurar mensajes con el rayo de Android:

-   `SetNdefPushMessage`-Antes de que se inicie el rayo de Android, una aplicación puede llamar a SetNdefPushMessage para especificar un NdefMessage para insertarlo sobre NFC y la actividad que lo está insertando. Este mecanismo se utiliza mejor cuando un mensaje no cambia mientras una aplicación está en uso.

-   `SetNdefPushMessageCallback`-Cuando se inicia el rayo de Android, una aplicación puede controlar una devolución de llamada para crear un NdefMessage. Este mecanismo permite que se retrase la creación de mensajes hasta que los dispositivos se encuentren dentro del alcance. Admite escenarios en los que el mensaje puede variar en función de lo que sucede en la aplicación.


En cualquier caso, para enviar datos con el rayo de Android, una aplicación `NdefMessage`envía un y empaqueta los datos `NdefRecords`en varios. Echemos un vistazo a los puntos clave que deben abordarse antes de que podamos desencadenar el haz de Android. En primer lugar, trabajaremos con el estilo de devolución de `NdefMessage`llamada de creación de.


## <a name="creating-a-message"></a>Crear un mensaje

Podemos registrar devoluciones de llamada `NfcAdapter` con un en el `OnCreate` método de la actividad. Por ejemplo, suponiendo que `NfcAdapter` un `mNfcAdapter` nombre se declara como una variable de clase en la actividad, podemos escribir el código siguiente para crear la devolución de llamada que construirá el mensaje:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

La actividad, que implementa `NfcAdapter.ICreateNdefMessageCallback`, se pasa `SetNdefPushMessageCallback` al método anterior. Cuando se inicia el rayo de Android, el sistema `CreateNdefMessage`llamará a, desde el que la `NdefMessage` actividad puede construir un, como se muestra a continuación:

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

[![Capturas de pantallas de ejemplo de la demostración de rayos de Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Vínculos relacionados

- [Demo de rayos de Android (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
- [Presentación del bocadillo de helado](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
