---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "76723817"
---
# <a name="android-beam"></a>Android Beam

Android Beam es una tecnología de transmisión de datos en proximidad (NFC) introducida en Android 4.0 que permite a las aplicaciones compartir información a través de NFC cuando se está en estrecha proximidad.

[![Diagrama que ilustra dos dispositivos en estrecha proximidad compartiendo información](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android Beam funciona insertando mensajes a través de NFC cuando dos dispositivos están dentro del alcance. Los dispositivos que están a 4 cm unos de otros pueden compartir datos con Android Beam. Una actividad en un dispositivo crea un mensaje y lo inserta para especificar la actividad (o las actividades) que se pueden controlar. Cuando la actividad especificada está en primer plano y los dispositivos están dentro del alcance, Android Beam insertará el mensaje en el segundo dispositivo. En el dispositivo receptor, se invoca una intención que contiene los datos del mensaje.

Android admite dos maneras de configurar mensajes con Android Beam:

- `SetNdefPushMessage`: antes de que se inicie Android Beam, una aplicación puede llamar a SetNdefPushMessage para especificar un elemento NdefMessage que se va a insertar a través de NFC y la actividad que lo inserta. Este mecanismo se usa principalmente cuando un mensaje no cambia mientras una aplicación está en uso.

- `SetNdefPushMessageCallback`: cuando se inicia Android Beam, una aplicación puede administrar una devolución de llamada para crear un elemento NdefMessage. Este mecanismo permite que se retrase la creación de mensajes hasta que los dispositivos se encuentren dentro del alcance. Admite escenarios en los que el mensaje puede variar en función de lo que sucede en la aplicación.

En cualquier caso, para enviar datos con Android Beam, una aplicación envía un elemento `NdefMessage` y empaqueta los datos en varios elementos `NdefRecords`. Vamos a examinar los puntos clave que deben abordarse antes de que podamos desencadenar Android Beam. En primer lugar, trabajaremos con el estilo de devolución de llamada de creación de un elemento `NdefMessage`.

## <a name="creating-a-message"></a>Creación de un mensaje

Se pueden registrar devoluciones de llamada con `NfcAdapter` en el método `OnCreate` de la actividad. Por ejemplo, suponiendo que un elemento `NfcAdapter` llamado `mNfcAdapter` se declare como una variable de clase en la actividad, se puede escribir el código siguiente para crear la devolución de llamada que construirá el mensaje:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

La actividad, que implementa `NfcAdapter.ICreateNdefMessageCallback`, se pasa al método `SetNdefPushMessageCallback` anterior. Cuando se inicia Android Beam, el sistema llamará a `CreateNdefMessage`, desde donde la actividad puede construir un elemento `NdefMessage`, como se muestra a continuación:

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

## <a name="receiving-a-message"></a>Recepción de un mensaje

En el lado de recepción, el sistema invoca una intención con la acción `ActionNdefDiscovered`, desde la que se puede extraer el elemento NdefMessage como se indica a continuación:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para ver un ejemplo de código completo donde se usa Android Beam, que se muestra en ejecución en la captura de pantalla siguiente, consulte la [demostración de Android Beam](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) en la galería de ejemplos.

[![Capturas de pantallas de ejemplo de la demostración de Android Beam](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de Android Beam (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
