---
title: Voz de Android
description: En este artículo se tratan los aspectos básicos del uso del espacio de nombres de Android. Speech muy eficaz. Desde su inicio, Android ha podido reconocer la voz y enviarla como texto. Es un proceso relativamente sencillo. En el caso de texto a voz, sin embargo, el proceso es más complicado, ya que no solo es necesario tener en cuenta el motor de voz, sino también los idiomas disponibles e instalados desde el sistema de texto a voz (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: 2a6de91772b1b6135a5b3567f691b8cd3522a924
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643252"
---
# <a name="android-speech"></a>Voz de Android

_En este artículo se tratan los aspectos básicos del uso del espacio de nombres de Android. Speech muy eficaz. Desde su inicio, Android ha podido reconocer la voz y enviarla como texto. Es un proceso relativamente sencillo. En el caso de texto a voz, sin embargo, el proceso es más complicado, ya que no solo es necesario tener en cuenta el motor de voz, sino también los idiomas disponibles e instalados desde el sistema de texto a voz (TTS)._

## <a name="speech-overview"></a>Información general sobre voz

El hecho de tener un sistema, que "comprenda" la voz humana y la enunciates, lo que se va a escribir (voz a texto y Text to Speech) es un área en constante crecimiento en el desarrollo móvil a medida que aumenta la demanda de comunicación natural con nuestros dispositivos. Hay muchos casos en los que tener una característica que convierte texto en voz, o viceversa, es una herramienta muy útil para incorporar a la aplicación Android.

Por ejemplo, con la abrazadera en el uso del teléfono móvil durante la conducción, los usuarios quieren una forma gratuita de trabajar con sus dispositivos. La gran cantidad de factores de forma de Android diferentes, como el desgaste de Android, y la inclusión en constante ampliación de aquellos que pueden usar dispositivos Android (como tabletas y paneles de notas), ha creado un enfoque más grande en aplicaciones TTS excelentes.

Google proporciona al desarrollador un amplio conjunto de API en el espacio de nombres de Android. Speech para abarcar la mayoría de las instancias de hacer que el dispositivo "tenga en cuenta la voz" (por ejemplo, el software diseñado para la persiana).  El espacio de nombres incluye la utilidad para permitir que el texto se traduzca `Android.Speech.Tts`en voz a través, control sobre el motor usado para realizar la traducción, así como `RecognizerIntent`un número de s que permiten convertir la voz en texto.

Aunque las instalaciones están allí para que se entienda la voz, existen limitaciones basadas en el hardware usado. Es poco probable que el dispositivo interprete correctamente todo lo que se le diga en cada idioma disponible.

## <a name="requirements"></a>Requisitos

No hay ningún requisito especial para esta guía, aparte del dispositivo que tiene un micrófono y un altavoz.

El núcleo de un dispositivo Android que interpreta la voz es el uso de `Intent` un con un `OnActivityResult`correspondiente.
Sin embargo, es importante reconocer que la voz no se entiende, pero se interpreta como texto. La diferencia es importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La diferencia entre entender e interpretar

Una definición simple de entender es que puede determinar por tono y contexto el significado real de lo que se está diciendo. Para interpretar solo significa que se tomen las palabras y se generen en otro formato.

Considere el siguiente ejemplo sencillo que se usa en la conversación diaria:

<kbd>¿Hola cómo estás?</kbd>

Sin inflexión (énfasis en determinadas palabras o partes de palabras), se trata de una pregunta sencilla. Sin embargo, si se aplica un ritmo lento a la línea, la persona que realiza escuchas detectará que el autor de la pregunta no está demasiado satisfecho y quizás sea necesario que se ponga en contacto con él. Si el énfasis se coloca en "es", la persona que solicita está normalmente más interesada en la respuesta.

Sin un procesamiento de audio bastante eficaz para hacer uso de la inflexión y un grado de inteligencia artificial (AI) para comprender el contexto, el software no puede incluso empezar a comprender lo que se dijo; lo más sencillo es convertir la voz en texto.

## <a name="setting-up"></a>Configuración

Antes de usar el sistema de voz, siempre es aconsejable comprobar para asegurarse de que el dispositivo tiene un micrófono. En un panel de notas de Kindle o Google, no hay ningún micrófono instalado.

En el ejemplo de código siguiente se muestra cómo consultar si un micrófono está disponible y, si no, para crear una alerta. Si no hay micrófono disponible en este momento, se cerrará la actividad o se deshabilitará la capacidad de grabar la voz.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Crear el intento

La intención del sistema de voz utiliza un tipo determinado de intento denominado `RecognizerIntent`. Esta intención controla un gran número de parámetros, incluido el tiempo que se debe esperar con el silencio hasta que la grabación se considere, cualquier lenguaje adicional que se reconozca y se genere, y `Intent`cualquier texto que se incluya en el cuadro de diálogo modal de la instrucción. En este fragmento de `VOICE` código, `readonly int` se usa para el `OnActivityResult`reconocimiento en.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Conversión de la voz

El texto que se interpreta de la voz se entregará `Intent`en, que se devuelve cuando se ha completado la actividad y se tiene acceso `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`a ella a través de. Esto devolverá `IList<string>`una, de la que se puede usar y mostrar el índice, en función del número de idiomas solicitados en el intento del autor de la llamada `RecognizerIntent.ExtraMaxResults`(y especificados en). A pesar de cualquier lista, merece la pena comprobar para asegurarse de que hay datos que se van a mostrar.

Al escuchar el valor devuelto de `StartActivityForResult`, se debe proporcionar el `OnActivityResult` método.

En el ejemplo siguiente, `textBox` es un `TextBox` que se usa para la salida de lo que se ha dictado. Se podría usar igualmente para pasar el texto a algún tipo de intérprete y, desde allí, la aplicación puede comparar el texto y la bifurcación con otra parte de la aplicación.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>Text to Speech

El texto a voz no es bastante inverso a la voz del texto y se basa en dos componentes clave: un motor de texto a voz instalado en el dispositivo y un idioma que se está instalando.

En gran medida, los dispositivos Android tienen instalado el servicio de TTS predeterminado de Google y al menos un idioma. Esto se establece cuando el dispositivo se configura por primera vez y se basará en el lugar en el que se encuentra el dispositivo (por ejemplo, un teléfono configurado en Alemania instalará el idioma alemán, mientras que uno en America tendrá inglés americano).

### <a name="step-1---instantiating-texttospeech"></a>Paso 1: crear una instancia de TextToSpeech

`TextToSpeech`puede tardar hasta 3 parámetros, los dos primeros son obligatorios, y el tercero es`AppContext`opcional `IOnInitListener`( `engine`,,). El agente de escucha se usa para enlazar con el servicio y probar si hay errores con el motor como cualquier número de texto Android disponible para los motores de voz. Como mínimo, el dispositivo tendrá el propio motor de Google.

### <a name="step-2---finding-the-languages-available"></a>Paso 2: búsqueda de los idiomas disponibles

La `Java.Util.Locale` clase contiene un método útil denominado `GetAvailableLocales()`. Esta lista de idiomas admitidos por el motor de voz se puede probar con los idiomas instalados.

Es una cuestión trivial generar la lista de idiomas "entendidos". Siempre habrá un idioma predeterminado (el idioma que el usuario estableció al configurar el dispositivo por primera vez), por lo que en este ejemplo `List<string>` el tiene el valor "default" como primer parámetro, el resto de la lista se rellenará en función del resultado `textToSpeech.IsLanguageAvailable(locale)`de.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

Este código llama a [TextToSpeech. IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) para comprobar si el paquete de idioma de una configuración regional determinada ya está presente en el dispositivo.
Este método devuelve un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), que indica si el idioma de la configuración regional que se pasa está disponible. Si `LanguageAvailableResult` indica que el idioma es `NotSupported`, no hay ningún paquete de voz disponible (incluso para su descarga) para ese idioma. Si `LanguageAvailableResult` está establecido en `MissingData`, es posible descargar un nuevo paquete de idioma, tal y como se explica más adelante en el paso 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Paso 3: establecimiento de la velocidad y el paso

Android permite al usuario modificar el sonido de la voz mediante la `SpeechRate` modificación de y `Pitch` (la velocidad de velocidad y el tono de la voz). Esto va de 0 a 1, con la voz "normal" en 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Paso 4: probar y cargar nuevos idiomas

La descarga de un nuevo lenguaje se realiza mediante `Intent`un. El resultado de este intento hace que se invoque el método [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) . A diferencia del ejemplo de conversión de voz a texto (que usaba [RecognizerIntent](xref:Android.Speech.RecognizerIntent) como `PutExtra` parámetro de `Intent`), las pruebas y las cargas `Intent`se `Action`basan en:

- [TextToSpeech. Engine. ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; inicia una actividad del motor de `TextToSpeech` plataforma para comprobar la correcta instalación y disponibilidad de los recursos de idioma en el dispositivo.

- [TextToSpeech. Engine. ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; inicia una actividad que pide al usuario que descargue los idiomas necesarios.

En el ejemplo de código siguiente se muestra cómo usar estas acciones para probar los recursos de idioma y descargar un nuevo idioma:

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData`comprueba la disponibilidad de los recursos de idioma. `OnActivityResult`se invoca cuando se completa esta prueba. Si es necesario descargar recursos de idioma, `OnActivityResult` se desencadena la `TextToSpeech.Engine.ActionInstallTtsData` acción de iniciar una actividad que permite al usuario descargar los idiomas necesarios. Tenga en cuenta `OnActivityResult` que esta implementación no comprueba `Result` el código porque, en este ejemplo simplificado, ya se ha realizado la determinación de que es necesario descargar el paquete de idioma.

La `TextToSpeech.Engine.ActionInstallTtsData` acción hace que la actividad de **datos de voz de Google TTS** se presente al usuario para elegir los idiomas que se van a descargar:

![Actividad de datos de voz de Google TTS](speech-images/01-google-tts-voice-data.png)

Por ejemplo, el usuario podría elegir francés y hacer clic en el icono de descarga para descargar datos de voz en francés:

![Ejemplo de descarga del idioma francés](speech-images/02-selecting-french.png)

La instalación de estos datos se produce automáticamente una vez completada la descarga.


### <a name="step-5---the-ioninitlistener"></a>Paso 5: IOnInitListener

Para que una actividad pueda convertir el texto en voz, se debe implementar el método `OnInit` de interfaz (es decir, el segundo parámetro especificado para la creación de instancias de la `TextToSpeech` clase). Esto inicializa el agente de escucha y prueba el resultado.

El agente de `OperationResult.Success` escucha debe probar y `OperationResult.Failure` como mínimo.
En el ejemplo siguiente se muestra solo eso:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Resumen

En esta guía hemos analizado los aspectos básicos de la conversión de texto a voz y voz a texto y métodos posibles de cómo incluirlos en sus propias aplicaciones. Aunque no cubren cada caso determinado, ahora debe tener un conocimiento básico de cómo se interpreta la voz, cómo instalar nuevos idiomas y cómo aumentar el inclusivity de las aplicaciones.



## <a name="related-links"></a>Vínculos relacionados

- [DependencyService de Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Text to Speech (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Voz a texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Espacio de nombres Android. Speech](xref:Android.Speech)
- [Espacio de nombres Android. Speech. TTS](xref:Android.Speech.Tts)
