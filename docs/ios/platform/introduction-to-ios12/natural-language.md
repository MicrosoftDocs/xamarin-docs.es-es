---
title: Usar el marco de lenguaje natural con Xamarin. iOS
description: En este documento se describe el marco de lenguaje natural. En iOS 12, el marco de lenguaje natural es la API de iOS preferida que se usará para el reconocimiento de idiomas, para partes de la identificación de voz y para el reconocimiento de entidades con nombre.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/20/2018
ms.openlocfilehash: 3d559f197ffc1696c4fd9f7beff7b107103142d3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434185"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Usar el marco de lenguaje natural con Xamarin. iOS

En iOS 12, el marco de lenguaje natural permite el procesamiento de lenguaje natural en el dispositivo. Admite el reconocimiento de idioma, la tokenización y el etiquetado. La tokenización divide el texto en sus palabras, oraciones o párrafos de componente; el etiquetado identifica partes de la voz, las personas, los lugares y las organizaciones.

El marco de lenguaje natural también puede utilizar modelos de aprendizaje automático básicos para clasificar y etiquetar texto en contextos especializados.

La clase [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) todavía está disponible. Sin embargo, el marco de lenguaje natural es el mecanismo preferido para el procesamiento de lenguaje natural.

## <a name="sample-app-xamarinnl"></a>Aplicación de ejemplo: XamarinNL

Para obtener información sobre cómo usar el marco de lenguaje natural con Xamarin. iOS, eche un vistazo a la [aplicación de ejemplo XamarinNL](/samples/xamarin/ios-samples/ios12-xamarinnl).
En esta aplicación de ejemplo se muestra cómo usar el marco de lenguaje natural para:

- [Reconocer idiomas](#recognizing-languages).
- [Acortar el texto en palabras y oraciones](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Etiquete las entidades con nombre y las partes de la voz](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Reconocimiento de idiomas

En la pestaña **reconocedor** de la aplicación de ejemplo se muestra cómo usar un [`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
para determinar el idioma de un bloque de texto.

> [!NOTE]
> El reconocimiento de idioma es un tipo específico de clasificación de texto. El marco de lenguaje natural también admite la clasificación de texto personalizado a través de modelos de aprendizaje automático proporcionados por el desarrollador. Para obtener más información, eche un vistazo a la introducción a la sesión de [marco de lenguaje natural](https://developer.apple.com/videos/play/wwdc2018/713/) desde WWDC 2018.

### <a name="dominant-language"></a>Idioma dominante

Puntee en el botón **idioma** para identificar el idioma dominante en la entrada del usuario.

El `HandleDetermineLanguageButtonTap` método del `LanguageRecognizerViewController` utiliza el [`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
método de `NLLanguageRecognizer` para capturar el [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
para el idioma principal que se encuentra en el texto:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Probabilidades de lenguaje

Puntee en el botón **probabilidades de lenguaje** para capturar una lista de supuestos de lenguaje para la entrada del usuario.

El `HandleLanguageProbabilitiesButtonTap` método de la `LanguageRecognizerViewController` clase crea una instancia de `NLLanguageRecognizer` y le solicita que [`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
texto del usuario. A continuación, llama a la del reconocedor de lenguaje [`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
método, que captura un diccionario de lenguajes y probabilidades asociadas. `LanguageRecognizerTableViewController`A continuación, la clase representa estos lenguajes y probabilidades.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

`NLLanguage`Los valores posibles son:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Una lista completa de los idiomas admitidos está disponible como parte del [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentación de la API enum.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Tokening de texto en palabras, oraciones y párrafos

En la pestaña **Tokenizer** de la aplicación de ejemplo se muestra cómo separar un bloque de texto en sus palabras o oraciones de componente con un [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer) .

Puntee en el botón **palabras** o **oraciones** para obtener una lista de tokens. Cada token está asociado a una palabra o frase en el texto original.

`ShowTokens` divide la entrada del usuario en tokens llamando al método [`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
método de un `NLTokenizer` . Este método devuelve una matriz de [`NSValue`](xref:Foundation.NSValue)
objetos, cada uno de los cuales ajusta un `NSRange` valor que corresponde a un token en el texto original.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController` representa un token único en cada celda de la tabla. Extrae un `NSRange` de un token `NSValue` , busca la cadena correspondiente en el texto original y establece una etiqueta en la celda de la vista de tabla:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Etiquetado de entidades con nombre y partes de la voz

La pestaña **etiquetador** de la aplicación de ejemplo XamarinNL muestra cómo usar el método [`NLTagger`](xref:NaturalLanguage.NLTagger)
clase para asociar categorías con tokens de una cadena de entrada.
El marco de lenguaje natural incluye compatibilidad integrada para reconocer personas, lugares, organizaciones y partes de la voz.

> [!NOTE]
> El marco de lenguaje natural también admite esquemas de etiquetado personalizados a través de los modelos de ML principales proporcionados por el desarrollador. Para obtener más información, eche un vistazo a la introducción a la sesión de [marco de lenguaje natural](https://developer.apple.com/videos/play/wwdc2018/713/) desde WWDC 2018.

Puntee en el botón **entidades con nombre** o **partes de voz** para capturar:

- Matriz de `NSValue` objetos, cada uno de los cuales ajusta un objeto `NSRange` para un token en el texto original.
- Una matriz de [`NLTag`](xref:NaturalLanguage.NLTag) valores: categorías para los `NSValue` tokens en el mismo índice de matriz.

En `LanguageTaggerViewController` , `HandlePartsOfSpeechButtonTap` y `HandleNamedEntitiesButtonTap` cada llamada `ShowTags` , pasando un elemento, [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) ya sea `NLTagScheme.LexicalClass` (para elementos de voz) o `NLTagScheme.NameType` (para entidades con nombre).

`ShowTags` crea una `NLTagger` instancia de, con una matriz de `NLTagScheme` tipos para la que se consultará (en este caso, solo el `NLTagScheme` valor pasado). A continuación, usa el [`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
en el `NLTagger` para determinar las etiquetas relevantes para el texto de los datos proporcionados por el usuario.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

Después, las etiquetas se muestran en una tabla por `LanguageTaggerTableViewController` .

`NLTag`Los valores posibles son:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Una lista completa de etiquetas admitidas está disponible como parte del [`NLTag`](xref:NaturalLanguage.NLTag)
documentación de la API enum.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: XamarinNL](/samples/xamarin/ios-samples/ios12-xamarinnl)
- [Introducción al marco de lenguaje natural](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Lenguaje natural (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)