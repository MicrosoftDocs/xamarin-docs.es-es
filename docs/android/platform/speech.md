---
title: Voz de Android
description: En este artículo se tratan los aspectos básicos del uso del espacio de nombres de Android.Speech muy eficaz. Desde su inicio, Android ha podido reconocer la voz y representarla como texto. Se trata de un proceso relativamente sencillo. En el caso de texto a voz, sin embargo, el proceso es más complicado, ya que no solo es necesario tener en cuenta el motor de voz, sino también los idiomas disponibles e instalados desde el sistema de texto a voz (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: ce94179fe0ae7f3df1f2fa5ead20ec09fd6f8f5e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457918"
---
# <a name="android-speech"></a>Voz de Android

_En este artículo se tratan los aspectos básicos del uso del espacio de nombres de Android.Speech muy eficaz. Desde su inicio, Android ha podido reconocer la voz y representarla como texto. Se trata de un proceso relativamente sencillo. En el caso de texto a voz, sin embargo, el proceso es más complicado, ya que no solo es necesario tener en cuenta el motor de voz, sino también los idiomas disponibles e instalados desde el sistema de texto a voz (TTS)._

## <a name="speech-overview"></a>Información general sobre voz

El hecho de tener un sistema que "comprenda" la voz humana y pronuncie lo que se escribe (voz a texto y texto a voz, respectivamente) es un área en constante crecimiento en el desarrollo móvil a medida que aumenta la demanda de comunicación natural con nuestros dispositivos. Hay muchos casos en los que tener una característica que convierte texto a voz, o viceversa, es una herramienta que resulta muy útil incorporar a la aplicación Android.

Por ejemplo, con la prohibición del uso del teléfono móvil durante la conducción, los usuarios quieren un sistema de manos libres para interactuar con sus dispositivos. La gran cantidad de factores de forma de Android diferentes, como Android Wear, y la inclusión en constante ampliación de aquellos que pueden usar dispositivos Android (como tabletas y bloc de notas), ha dado lugar a que se haga mayor hincapié en aplicaciones TTS excelentes.

Google proporciona al desarrollador un amplio conjunto de API en el espacio de nombres de Android.Speech para abarcar la mayoría de las instancias destinadas a hacer que un dispositivo "reconozca la voz" (por ejemplo, el software diseñado para los invidentes).  El espacio de nombres incluye la característica para permitir convertir texto en voz mediante `Android.Speech.Tts`, controlar el motor usado para realizar la traducción, así como una serie de `RecognizerIntent` que permiten convertir la voz en texto.

Aunque existen las características para el reconocimiento de la voz, hay limitaciones basadas en el hardware utilizado. Es poco probable que el dispositivo interprete correctamente todo lo que se le diga en cada idioma disponible.

## <a name="requirements"></a>Requisitos

No hay ningún requisito especial para esta guía, aparte del dispositivo que tiene un micrófono y un altavoz.

El núcleo de un dispositivo Android que interpreta la voz es el uso de `Intent` con un elemento `OnActivityResult` correspondiente.
Sin embargo, es importante reconocer que la voz no se entiende, pero que se interpreta como texto. La diferencia es importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>La diferencia entre entender e interpretar

Una definición sencilla de entender es que se puede determinar por tono y contexto el significado real de lo que se está diciendo. Interpretar solo significa que se capten las palabras y se representen en otro formato.

Considere el siguiente ejemplo sencillo que se usa en cada conversación:

<kbd>Hola, ¿cómo está?</kbd>

Sin inflexión (énfasis en palabras específicas o partes de palabras), se trata de una pregunta sencilla. Sin embargo, si se aplica un ritmo lento a la línea, la persona que escucha detectará que el autor de la pregunta no está muy contento y quizás necesita ánimo o que está enfermo. Si el énfasis recae en "está", la persona que pregunta suele estar más interesada en la respuesta.

Sin un procesamiento de audio bastante eficaz para hacer uso de la inflexión y un grado de inteligencia artificial (AI) para comprender el contexto, el software no puede siquiera empezar a comprender lo que se dijo; lo más sencillo que un teléfono puede hacer es convertir la voz a texto.

## <a name="setting-up"></a>Instalación

Antes de usar el sistema de voz, siempre es aconsejable comprobar para asegurarse de que el dispositivo tiene un micrófono. No tendría mucho sentido tratar de ejecutar la aplicación en un bloc de notas de Kindle o Google sin un micrófono instalado.

En el ejemplo de código siguiente se muestra cómo consultar si un micrófono está disponible y, si no, cómo crear una alerta. Si no hay micrófono disponible en este momento, se cerrará la actividad o se deshabilitará la capacidad de grabar la voz.

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

### <a name="creating-the-intent"></a>Creación de la intención

La intención del sistema de voz utiliza un tipo determinado de intención denominado `RecognizerIntent`. Esta intención controla un gran número de parámetros, incluido el tiempo que se debe esperar en silencio hasta que se considera que la grabación ha finalizado, cualquier lenguaje adicional que se reconozca y se genere y cualquier texto que se incluya en el cuadro de diálogo modal de `Intent` como medio de instrucción. En este fragmento, `VOICE` es un elemento `readonly int` utilizado para el reconocimiento en `OnActivityResult`.

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

El texto interpretado a partir de la voz se entregará dentro de `Intent`, que se devuelve cuando se ha completado la actividad y donde se accede a través de `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Esto devolverá `IList<string>`, de donde se puede usar y mostrar el índice, en función del número de idiomas solicitados en la intención del autor de la llamada, y según se especifica en `RecognizerIntent.ExtraMaxResults`. A pesar de cualquier lista, merece la pena comprobar para asegurarse de que hay datos que se van a mostrar.

Al escuchar el valor devuelto de `StartActivityForResult`, se debe proporcionar el método `OnActivityResult`.

En el ejemplo siguiente, `textBox` es un elemento `TextBox` que se usa para la salida de lo que se ha dictado. Se podría usar igualmente para pasar el texto a algún tipo de intérprete y, desde allí, la aplicación puede comparar el texto y la bifurcación a otra parte de la aplicación.

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

Texto a voz no es suficiente para convertir la voz en texto y se basa en dos componentes clave: un motor de texto a voz instalado en el dispositivo y un idioma que se está instalando.

En gran medida, los dispositivos Android tienen instalado el servicio TTS predeterminado de Google y al menos un idioma. Esto se establece cuando el dispositivo se configura por primera vez y se basará en el lugar en el que se encuentra el dispositivo (por ejemplo, un teléfono configurado en Alemania instalará el idioma alemán, mientras que uno en Estados Unidos instalará el inglés estadounidense).

### <a name="step-1---instantiating-texttospeech"></a>Paso 1: Crear una instancia de TextToSpeech

`TextToSpeech` puede adoptar hasta 3 parámetros; los dos primeros son obligatorios y el tercero es opcional (`AppContext`, `IOnInitListener` y `engine`). El agente de escucha se usa para enlazar con el servicio y probar si hay errores con el motor, con cualquier número de motores disponibles de texto a voz de Android. Como mínimo, el dispositivo tendrá el propio motor de Google.

### <a name="step-2---finding-the-languages-available"></a>Paso 2: Búsqueda de los idiomas disponibles

La clase `Java.Util.Locale` contiene un método útil denominado `GetAvailableLocales()`. Esta lista de idiomas admitidos por el motor de voz se puede probar con los idiomas instalados.

Es una cuestión trivial generar la lista de idiomas "comprensibles". Siempre habrá un idioma predeterminado (el idioma que el usuario estableció al configurar el dispositivo por primera vez), por lo que en este ejemplo el atributo `List<string>` tiene "default" como primer parámetro, el resto de la lista se rellenará en función del resultado de `textToSpeech.IsLanguageAvailable(locale)`.

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

Este código llama a [TextToSpeech.IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) para comprobar si el paquete de idioma de una configuración regional determinada ya está presente en el dispositivo.
Este método devuelve un [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), que indica si el idioma de la configuración regional que se pasa está disponible. Si `LanguageAvailableResult` indica que el idioma es `NotSupported`, no hay ningún paquete de voz disponible (incluso para su descarga) para ese idioma. Si `LanguageAvailableResult` se establece en `MissingData`, es posible descargar un nuevo paquete de idioma, tal y como se explica más adelante en el paso 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Paso 3: Establecimiento de la velocidad y el tono

Android permite al usuario modificar el sonido de la voz mediante la modificación de `SpeechRate` y `Pitch` (la tasa de velocidad y el tono de la voz). Esto va de 0 a 1, con la voz "normal" en 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Paso 4: Probar y cargar nuevos idiomas

La descarga de un nuevo idioma se realiza mediante un elemento `Intent`. El resultado de esta intención hace que se invoque el método [OnActivityResult](xref:Android.App.Activity.OnActivityResult*). A diferencia del ejemplo de conversión de voz a texto (que usaba [RecognizerIntent](xref:Android.Speech.RecognizerIntent) como un parámetro `PutExtra` para `Intent`), las pruebas y la carga de `Intent` se basan en `Action`:

- [TextToSpeech.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData): inicia una actividad desde el motor `TextToSpeech` de la plataforma para comprobar la correcta instalación y disponibilidad de los recursos de idioma en el dispositivo.

- [TextToSpeech.Engine.ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData): inicia una actividad que pide al usuario que descargue los idiomas necesarios.

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

`TextToSpeech.Engine.ActionCheckTtsData` comprueba la disponibilidad de los recursos de idioma. `OnActivityResult` se invoca cuando se completa esta prueba. Si es necesario descargar recursos de idioma, `OnActivityResult` desactiva la acción `TextToSpeech.Engine.ActionInstallTtsData` para iniciar una actividad que permite al usuario descargar los idiomas necesarios. Tenga en cuenta que esta implementación de `OnActivityResult` no comprueba el código de `Result` porque, en este ejemplo simplificado, ya se ha realizado la determinación de que es necesario descargar el paquete de idioma.

La acción `TextToSpeech.Engine.ActionInstallTtsData` da lugar a que la actividad de **datos de voz de TTS de Google** se presente al usuario para elegir los idiomas que se van a descargar:

![Actividad de datos de voz de TTS de Google](speech-images/01-google-tts-voice-data.png)

Por ejemplo, el usuario podría elegir francés y hacer clic en el icono de descarga para descargar datos de voz en francés:

![Ejemplo de descarga del idioma francés](speech-images/02-selecting-french.png)

La instalación de estos datos se produce automáticamente una vez completada la descarga.

### <a name="step-5---the-ioninitlistener"></a>Paso 5: IOnInitListener

Para que una actividad pueda convertir el texto en voz, el método de interfaz `OnInit` tiene que implementarse (es decir, el segundo parámetro especificado para la creación de instancias de la clase `TextToSpeech`). Esto inicializa el cliente de escucha y prueba el resultado.

El cliente de escucha debe probar tanto `OperationResult.Success` como `OperationResult.Failure` como mínimo.
En el ejemplo siguiente se muestra que:

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

En esta guía hemos analizado los aspectos básicos de la conversión de texto a voz y voz a texto y métodos posibles de cómo incluirlos en sus propias aplicaciones. Aunque no abarcan cada caso determinado, ahora debe tener un conocimiento básico de cómo se interpreta la voz, cómo instalar nuevos idiomas y cómo aumentar la inclusión de las aplicaciones.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Forms DependencyService](/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Texto a voz (ejemplo)](/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Voz a texto (ejemplo)](/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Espacio de nombres Android.Speech](xref:Android.Speech)
- [Espacio de nombres Android.Speech.Tts](xref:Android.Speech.Tts)