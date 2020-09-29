---
title: Localización de la interfaz de usuario de la aplicación
description: En este documento se describen los conceptos multiplataforma de internacionalización y localización, y se examina cómo afectan al diseño de la aplicación.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 38bee7e2c4c0b3cdf5107842e47feeeb1445d626
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457736"
---
# <a name="localization"></a>Localización

En esta guía se presentan los conceptos relacionados con la *internacionalización* y la *localización* , así como vínculos a instrucciones sobre cómo generar aplicaciones móviles de Xamarin con esos conceptos.

Si quiere ir directamente a los detalles técnicos de la localización de aplicaciones de Xamarin, comience con uno de estos artículos de procedimientos específicos de la plataforma:

- Localización multiplataforma de [**Xamarin. Forms**](~/xamarin-forms/app-fundamentals/localization/index.md) mediante archivos resx.
- Localización de la plataforma nativa de [**Xamarin. iOS**](~/ios/app-fundamentals/localization/index.md) .
- Localización de la plataforma nativa de [**Xamarin. Android**](~/android/app-fundamentals/localization.md) .

## <a name="i18n-and-l10n"></a>i18n y l10n

La *internacionalización* es el proceso de que el código pueda mostrar distintos idiomas y adaptar su presentación para diferentes configuraciones regionales (como el formato de número y fecha). Esto también se conoce como *globalización*.

La *localización* es el paso siguiente: crear recursos (como cadenas e imágenes) para cada idioma y agruparlos con la aplicación internacionalizate.

Internacionalización a menudo se acorta a i18n – abreviatura para 18 letras entre "i" y "n". La localización se acorta de forma similar a l10n, para 10 letras entre "L" y "n".

## <a name="overview"></a>Información general

En este documento se presentan los conceptos asociados a la internacionalización y localización, y cómo se aplican al desarrollo de aplicaciones móviles en general.
Al diseñar y compilar una aplicación, lo que antes podía haber codificado, pero que debe parametrizarse para la localización, incluye:

- Diseños de pantalla y texto,
- Iconos, gráficos y colores,
- Archivos de vídeo y sonido,
- Texto dinámico y formato de texto (como números, moneda y fechas)
- Cambios de diseño de idiomas que se leen de derecha a izquierda (RTL) y
- Ordenación de datos.

Con independencia de las plataformas móviles a las que se destina la aplicación, estas sugerencias le ayudarán a crear una aplicación localizada de alta calidad.

## <a name="design-considerations"></a>Consideraciones de diseño

La arquitectura de una aplicación para que sea posible localizar su contenido se denomina internacionalización. La internacionalización correcta es algo más que simplemente permitir que se carguen cadenas de idioma diferentes en tiempo de ejecución: una aplicación bien diseñada debe permitir que todos los recursos se cambien en función del idioma y la configuración regional (incluidas imágenes, sonidos y vídeos) y puede adaptar el formato y el diseño para hacer frente a cadenas de tamaño diferentes.

En esta sección se describen algunas consideraciones de diseño que se deben tener en cuenta al crear una aplicación internacionalizada.

### <a name="layouts-and-string-length"></a>Diseños y longitud de cadena

Las cadenas de chino y japonés pueden ser muy cortas: a veces uno o dos caracteres pueden tener un significado suficiente para una etiqueta de campo de entrada.

Las cadenas alemanas (por ejemplo,) pueden ser muy largas; a veces, una palabra relativamente corta en inglés se vuelve muy larga en otros idiomas: se está recortando o se retransmite de forma inesperada el diseño.

Compare las longitudes de cadena para algunos elementos de la pantalla principal de iOS en inglés, alemán y japonés:

[![Longitud de cadena de alemán frente a Japonés](localization-images/language-compare-sml.png)](localization-images/language-compare.png#lightbox)

Tenga en cuenta que la **configuración** en inglés (8 caracteres) requiere 13 caracteres para la traducción alemana, pero solo 2 caracteres en japonés.

Los diseños en los que la etiqueta de visualización y el campo de entrada están en paralelo son difíciles de trabajar con cuando la longitud de la etiqueta puede variar en gran medida. A menudo, un diseño en el que la etiqueta se muestra sobre un campo es más fácil de localizar porque el ancho completo de la pantalla está disponible tanto para la etiqueta como para la entrada.

Como norma general, si va a compilar diseños fijos (especialmente elementos en paralelo), permita al menos un 50% más de ancho que las cadenas en inglés que se necesitan para las etiquetas y el texto. Esto no solucionará todos los problemas, pero proporcionará un búfer que funcionará en muchos casos.

### <a name="input-validation"></a>Validación de entradas

Tenga cuidado con las suposiciones al escribir reglas de validación. Podría parecer que es necesario especificar una entrada de campo de texto para "requerir" al menos tres caracteres en inglés, ya que una sola letra apenas tiene significado. En chino y japonés, sin embargo, un único carácter podría ser una entrada válida y un mensaje de validación "al menos 3 caracteres obligatorios" no tiene sentido para esos idiomas.

Otras tareas aparentemente simples, como la validación de una dirección de correo electrónico o una dirección URL del sitio web, se vuelven más complicadas, ya que los caracteres no se limitan al subconjunto ASCII.

Escriba sus reglas de validación teniendo en cuenta la internacionalización: elija las reglas menos restrictivas o escriba la lógica para que funcione de forma diferente para cada idioma.

### <a name="images-and-color"></a>Imágenes y color

No todas las imágenes deben cambiar en función de la elección del idioma del usuario. Muchos iconos o fotos serán adecuados para todos los usuarios, independientemente del lenguaje que hablen.
Sin embargo, algunos recursos tienen sentido localizar, como:

- Imágenes que describen personas o ubicaciones específicas: su aplicación puede sentirse más relevante para los usuarios si muestra personas o ubicaciones locales.
- Iconos: algunos iconografía pueden ser específicos de la referencia cultural y puede hacer que la aplicación sea más fácil de usar localizando la imagen para reflejar la comprensión local.
- Colores: algunas referencias culturales entienden los colores de manera diferente: rojo puede significar una advertencia en una región, pero buena suerte en otra. Consulte con los oradores nativos al diseñar la aplicación para determinar si debe crear un mecanismo para localizar los colores.

### <a name="videos-and-sound"></a>Vídeos y sonido

Los vídeos y el sonido presentan problemas especiales a la hora de localizar una aplicación, porque aunque es relativamente fácil obtener cadenas traducidas, la grabación de varias pistas de VoiceOver o clips de vídeo puede ser costosa y difícil.

Varias copias de archivos de vídeo y sonido también pueden aumentar considerablemente el tamaño de la aplicación (especialmente si se localiza en un gran número de idiomas o si hay muchos archivos multimedia). Considere la posibilidad de descargar solo los recursos de idioma necesarios una vez que el usuario haya instalado la aplicación, pero esto también podría dar lugar a una mala experiencia de usuario en redes lentas.

A menudo hay varias maneras de solucionar problemas de localización: lo más importante es considerarlas por adelantado y asegurarse de que la aplicación está diseñada para encargarse de ellas.

### <a name="dates-times-numbers-and-currency"></a>Fechas, horas, números y moneda

Si utiliza funciones de formato .NET, no olvide especificar la referencia cultural para que los separadores decimales se analicen correctamente (y evitar que se inicien excepciones de conversión). Por ejemplo, 1,99 y 1, 99 son representaciones decimales válidas en función de la configuración regional.

Cuando los datos proceden de un origen conocido (es decir, desde su propio código o un servicio Web que usted controle), puede codificar un identificador de referencia cultural que coincida con el formato, como InvariantCulture, que funcionará para el formato de idioma inglés estándar.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Si el usuario de la aplicación está introduciendo los datos, analice el uso de una instancia CultureInfo que refleje su configuración regional:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Para obtener más información, consulte los artículos [análisis de cadenas numéricas](/dotnet/standard/base-types/parsing-numeric) y análisis de cadenas de [fecha y hora](/dotnet/standard/base-types/parsing-datetime) de MSDN.

<a name="rtl"></a>

### <a name="right-to-left-rtl-languages"></a>Idiomas de derecha a izquierda (RTL)

Algunos idiomas, como el árabe, el hebreo y el Urdu (por ejemplo), se leen de derecha a izquierda.
Las aplicaciones que admiten estos lenguajes deben usar diseños de pantalla que se adapten a lectores de derecha a izquierda, por ejemplo:

- El texto debe estar alineado a la derecha.
- Las etiquetas deben aparecer a la derecha de los campos de entrada.
- Normalmente, se invierte la posición de los botones predeterminados.
- La navegación jerárquica deslizante y la animación (y otras metáforas y animaciones de navegación) que usan la dirección para el contexto también deben invertirse.

Tanto iOS como Android admiten diseños de derecha a izquierda y representación de fuentes, con características integradas que ayudan a realizar los ajustes anteriores. Xamarin. Forms no admite actualmente la representación RTL.

### <a name="sorting"></a>Ordenar

Los distintos idiomas definen el criterio de ordenación de los alfabetos de forma diferente, incluso cuando utilizan el mismo juego de caracteres.

Vea los [detalles de la comparación de cadenas](/dotnet/standard/base-types/best-practices-strings#the_details_of_string_comparison) en [procedimientos recomendados para el uso de cadenas en el .NET Framework](/dotnet/standard/base-types/best-practices-strings) para ver un ejemplo en el que el lenguaje (CultureInfo) afecta al criterio de ordenación.

No es probable que las capacidades de base de datos integrada en las plataformas móviles admitan el orden específico del idioma, por lo que puede ser necesario implementar código adicional en la lógica de negocios.

### <a name="text-search"></a>Búsqueda de texto

Asegúrese de escribir y probar el algoritmo de búsqueda con varios idiomas en mente. Entre los aspectos que se deben tener en cuenta se incluyen:

- Autocompletar: Si ha creado una función de autocompletar, asegúrese de que incluye sugerencias relevantes para el idioma del usuario.
- Consulta de búsqueda de coincidencias en los datos: ¿se ejecutarán las consultas escritas en un idioma concreto solo en el contenido escrito en ese idioma o en todo el contenido de la aplicación?
- Lematización: Si la búsqueda se crea para buscar palabras similares, raíces de Word y otras optimizaciones de búsqueda, ¿se han creado las optimizaciones para todos los idiomas admitidos?
- Ordenación: Asegúrese de que los resultados se ordenen correctamente (consulte más arriba).

### <a name="data-from-external-sources"></a>Datos de orígenes externos

Muchas aplicaciones descargan datos de orígenes externos, desde fuentes de Twitter y RSS hasta precios meteorológicos, noticias o acciones. Cuando se muestra a un usuario, es necesario tener en cuenta la posibilidad de que se muestre una pantalla de información irrelevante o ilegible.

Hay algunas estrategias que puede usar para probar y asegurarse de que la aplicación muestra los datos relevantes para el usuario:

- Distintos orígenes: la aplicación puede descargar los datos de un origen diferente según el idioma o la configuración regional del usuario. Las noticias de la configuración regional, el tiempo y los precios de las acciones pueden tener más sentido que algo descargado de una fuente de Norteamérica.
- Presentación localizada: Si va a mostrar un Twitter o una fuente de fotos, debe mostrar los metadatos (por ejemplo, el tiempo invertido) en su propio idioma, incluso si el contenido permanece en el idioma original.
- Traducción: puede crear una opción de traducción en la aplicación para realizar una traducción automática de los datos entrantes. Esto podría ser automático o a discreción del usuario: solo debe asegurarse de notificar al usuario si se está llevando a cabo, ya que las traducciones del equipo nunca son perfectas.

Esto también podría afectar a los vínculos externos a las pistas de audio o vídeos: al diseñar la aplicación, asegúrese de planear con anterioridad el origen del contenido traducido o de asegurarse de que la interfaz de usuario informará a los usuarios adecuadamente cuando el contenido no se presente en su idioma.

### <a name="dont-over-translate"></a>No sobretraducción

Es posible que algunas cadenas de la aplicación no necesiten traducir o que, al menos, el traductor tenga especial atención. Algunos ejemplos pueden incluir:

- URL: si enumera una dirección URL, puede que sea necesario ajustarla o no mediante el lenguaje. Por ejemplo, facebook.com no requiere traducción que detecte automáticamente el idioma en el sitio principal. Otros sitios tienen contenido específico de la configuración regional y es posible que desee ofrecer una dirección URL diferente, como yahoo.com frente a yahoo.fr o yahoo.it.
- Números de teléfono, especialmente aquellos con códigos de país o números distintos para los autores de llamadas que hablan un idioma determinado.
- Detalles de contacto: las direcciones y otra información pueden variar según el idioma o la configuración regional.
- Marcas comerciales & nombres de producto: algunas cadenas no necesitan traducirse porque siempre están escritas en el mismo idioma.

Por último, asegúrese de incluir instrucciones detalladas para el traductor si algunas cadenas requieren un tratamiento especial.

### <a name="formatted-text"></a>Texto con formato

No suele ser un problema con Mobile Apps porque las cadenas no suelen tener un formato enriquecido. Sin embargo, si se requiere texto enriquecido (por ejemplo, formato de negrita o cursiva) en la aplicación, asegúrese de que el traductor sabe cómo introducir el formato, que los archivos de cadenas lo almacenan correctamente y que tienen el formato correcto antes de que se muestren al usuario (es decir, no deje que los códigos de formato se presenten al usuario).

## <a name="translation-tips"></a>Sugerencias de traducción

La traducción de las cadenas usadas por una aplicación se considera parte del proceso de localización. Normalmente, esta tarea se sobrescribirá en un servicio de traducción y la lleva a cabo un personal multilingüe que puede no conocer su aplicación o su empresa.

Las siguientes sugerencias le ayudarán a generar cadenas que son más fáciles de traducir y, por tanto, a mejorar la calidad de las aplicaciones localizadas.

### <a name="localize-complete-strings-not-words"></a>Localizar cadenas completas, no palabras

A veces, los desarrolladores adoptan el enfoque de intentar especificar palabras individuales o frases ' fragmentos de código ' para que puedan volver a usarlas en toda la aplicación. Por ejemplo, para el texto "tiene 5 mensajes". pueden especificar las siguientes cadenas para la traducción

**Incorrecto**:

```csharp
"You have"
"no"
"message"
"messages"
```

y, a continuación, intente crear la frase correcta sobre la marcha en el código mediante la concatenación de cadenas:

**Incorrecto**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Esto no se recomienda** porque no funcionará necesariamente para todos los lenguajes y será difícil para el traductor comprender el contexto de cada segmento corto. También conduce a reutilizar las cadenas traducidas, lo que puede causar problemas posteriormente si se usan en contextos diferentes (y, a continuación, se actualizan).

### <a name="allow-for-parameter-re-ordering"></a>Permitir reordenación de parámetros

Algunos lenguajes de programación requieren una sintaxis adicional para especificar el orden de los parámetros en una cadena; sin embargo, .NET ya admite el concepto de marcadores de posición numerados, por lo que

**Bueno**:

```csharp
"a {0} b {1} cde {3}"
```

podría traducirse lo siguiente (donde se cambia la posición y el orden de los marcadores de posición)

```csharp
"{2} {3} f g h {0}"
```

y los tokens se ordenarán como el traductor previsto. Asegúrese de incluir una explicación de lo que contiene cada marcador de posición al enviar la cadena a un traductor.

### <a name="use-multiple-strings-for-cardinality"></a>Usar varias cadenas para la cardinalidad

Evite cadenas como `"You have {0} message/s."` usar cadenas específicas para cada Estado para proporcionar una mejor experiencia de usuario:

**Bueno**:

```csharp
"You have no messages."
"You have 1 message."
"You have 2 messages."
"You have {0} messages."
```

Tendrá que escribir código en la aplicación para evaluar el número que se muestra y elegir la cadena adecuada. Algunas plataformas (incluidas iOS y Android) tienen características integradas para elegir automáticamente la mejor cadena plural según las preferencias de idioma o configuración regional actual.

### <a name="allowing-for-gender"></a>Permitir sexo

En ocasiones, los lenguajes basados en latín usan diferentes palabras según el género del sujeto. Si su aplicación conoce el sexo, debe permitir que las cadenas traducidas reflejen esto.

También hay un caso más obvio, incluso en inglés, en el que las cadenas hacen referencia a una persona concreta o a un usuario de la aplicación. Por ejemplo, algunos sitios muestran mensajes como `"Bob commented on his post"` , por lo que necesita cadenas para el sexo macho, hembra, no binaria o desconocido:

**Bueno**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>No volver a usar cadenas

O más concretamente, no reutilice cadenas solo porque son similares cuando la propia cadena tiene un propósito o un significado diferente.

Por ejemplo: Imagine que tiene un conmutador de activado/desactivado en la aplicación y el control de modificador necesita el texto de "activado" y "desactivado" para localizarlo. También se muestra el valor de ese valor en otra parte de la aplicación en una etiqueta de texto. Debe usar cadenas diferentes para la presentación del conmutador frente al estado del conmutador (incluso si son la misma cadena en el idioma predeterminado), por ejemplo:

- "Activado": se muestra en el propio conmutador
- "Desactivado": se muestra en el propio conmutador
- "Activado": se muestra en una etiqueta
- "Desactivado": se muestra en una etiqueta

Esto proporciona la máxima flexibilidad para el traductor:

- Por motivos de diseño, quizás el propio conmutador use "ON" y "OFF" en minúsculas, pero la etiqueta de pantalla usa mayúsculas "ON" y "OFF".
- Algunos lenguajes pueden necesitar que el valor del modificador se abrevie para que quepa en el control de la interfaz de usuario, mientras que la palabra completa (traducida) puede aparecer en la etiqueta.
- Como alternativa, en algunos lenguajes la representación del conmutador podría usar "I" y "O" para la familiaridad cultural, pero es posible que quiera que la etiqueta Lea "ON" u "OFF".

### <a name="translation-services"></a>Servicios de traducción

#### <a name="machine-translation"></a>Traducción automática

Para compilar características de traducción en la aplicación, tenga en cuenta la [Translator Text API de Azure](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

Con fines de prueba, puede usar una de las muchas herramientas de traducción en línea para incluir algún texto localizado en la aplicación durante el desarrollo:

- [Traductor de Bing](https://www.bing.com/translator/)
- [Traducción de Google](https://translate.google.com/)

Hay muchas otras disponibles. La calidad de la traducción de la máquina generalmente no se considera lo suficientemente adecuada como para publicar una aplicación sin que los traductores profesionales o los hablantes nativos la revisen y prueben primero.

#### <a name="professional-translation"></a>Traducción profesional

También hay servicios de traducción profesional que tomarán las cadenas y las distribuirá a sus propios traductores, lo que le proporcionará traducciones finalizadas.

Uno de los servicios más conocidos es [Lionbridge](https://www.lionbridge.com/). La mayoría de los servicios profesionales admiten todos los tipos de archivo comunes, como cadenas, XML, RESX y POT/PO.

## <a name="summary"></a>Resumen

En este artículo se presentaron algunos de los conceptos con los que debe familiarizarse antes de internacionalizar la aplicación y, a continuación, localizar los recursos, y también se explica cómo cambiar las preferencias de idioma para cada plataforma.

Estos conceptos se pueden aplicar a las diversas técnicas de internacionalización multiplataforma y específicas de la plataforma que son posibles con Xamarin.

Siga leyendo los detalles técnicos de la plataforma en la que está interesado:

- Localización multiplataforma de [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md) mediante archivos resx.
- Localización de la plataforma nativa de [Xamarin. iOS](~/ios/app-fundamentals/localization/index.md) .
- Localización de la plataforma nativa de [Xamarin. Android](~/android/app-fundamentals/localization.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la localización de Apple](https://developer.apple.com/internationalization/)
- [Lista de comprobación de la localización de Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Prácticas recomendadas para desarrollar aplicaciones de uso internacional (MSDN)](/previous-versions/visualstudio/visual-studio-2008/w7x1y988(v=vs.90))