---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 644262310614874794810fd083ba1823abfd0da2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025417"
---
# <a name="alternate-resources"></a>Recursos alternativos

Los recursos alternativos son aquellos que tienen como destino un dispositivo específico o una configuración en tiempo de ejecución, como el idioma actual, el tamaño de pantalla concreto o la densidad de píxeles. Si Android puede coincidir con un recurso más específico para un determinado dispositivo o configuración que el recurso predeterminado, se utilizará ese recurso en su lugar. Si no encuentra un recurso alternativo que coincida con la configuración actual, se cargarán los recursos predeterminados. La forma en que Android decide qué recursos usará una aplicación se tratará con más detalle a continuación, en la sección Ubicación de recursos.

Los recursos alternativos se organizan como un subdirectorio dentro de la carpeta recursos según el tipo de recurso, al igual que los recursos predeterminados. El nombre del subdirectorio de recursos alternativo tiene el formato: _ResourceType_-_calificador_

El *calificador* es un nombre que identifica una configuración específica del dispositivo.
Puede haber más de un calificador en un nombre, cada uno de ellos separado por un guión. Por ejemplo, la captura de pantalla siguiente muestra un proyecto simple que tiene recursos alternativos para varias configuraciones, como la configuración regional, la densidad de la pantalla, el tamaño de la pantalla y la orientación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos](alternate-resources-images/alternate-resources-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos](alternate-resources-images/alternate-resources-xs.png)

-----

Al agregar calificadores a un tipo de recurso, se aplican las siguientes reglas:

1. Puede haber más de un calificador, donde cada calificador está separado por un guión.

2. Es posible que los calificadores solo se especifiquen una vez.

3. Los calificadores deben estar en el orden en que aparecen en la tabla siguiente.

Los calificadores posibles se enumeran a continuación como referencia:

- **MCC y MNC** &ndash; el [código de país móvil](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) y, opcionalmente, el [código de red móvil](https://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). La tarjeta SIM proporcionará la MCC, mientras que la red a la que está conectado el dispositivo proporcionará el MNC. Aunque es posible destinar a las configuraciones regionales con el código de país móvil, el enfoque recomendado es usar el calificador de lenguaje que se especifica a continuación. Por ejemplo, para dirigirse a los recursos a Alemania, el calificador se `mcc262`. Para dirigirse a los recursos de T-Mobile en EE. UU., el calificador se `mcc310-mnc026`.
  Para obtener una lista completa de códigos de país móvil y códigos de red móvil, consulte <http://mcc-mnc.com/>.

- **Language** &ndash; el código de idioma de dos letras [ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) y, opcionalmente, seguido del [código de región ISO-3166-Alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)de dos letras. 
  Si se proporcionan ambos calificadores, se separan mediante un `-r`. Por ejemplo, para tener como destino las configuraciones regionales en francés, se usa el calificador de `fr`. Para tener como destino las configuraciones regionales en francés (Canadá), se utilizará el `fr-rCA`. Para obtener una lista completa de códigos de idioma y códigos de región, consulte [códigos para la representación de los nombres de los idiomas](https://www.loc.gov/standards/iso639-2/php/English_list.php) y [los nombres de los países y los elementos de código](https://www.iso.org/iso-3166-country-codes.html).

- **Ancho más pequeño** &ndash; especifica el ancho de pantalla más pequeño en el que se va a ejecutar la aplicación. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Por ejemplo, el calificador `sw320dp` se usa para destinar dispositivos cuyo alto y ancho sea al menos 320dp.

- **Ancho disponible** &ndash; el ancho mínimo de la pantalla con el formato w*n*DP, donde *N* es el ancho en píxeles independientes de densidad.
  Este valor puede cambiar a medida que el usuario gira el dispositivo. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Ejemplo: el calificador w720dp se usa para dispositivos de destino que tienen un ancho de menos de 720dp.

- **Alto disponible** &ndash; el alto mínimo de la pantalla con el formato h*N*DP, donde *N* es el alto de DP. Este valor puede cambiar a medida que el usuario gira el dispositivo. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Por ejemplo, el calificador h720dp se usa para dispositivos de destino que tienen un alto de menos de 720dp

- **Tamaño de pantalla** &ndash; este calificador es una generalización del tamaño de la pantalla para la que son estos recursos. Se trata con más detalle en la [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Los valores posibles son `small`, `normal`, `large` y `xlarge`. Agregado en el nivel de API 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **Aspecto** de la pantalla &ndash; se basa en la relación de aspecto, no en la orientación de la pantalla. Una pantalla larga es más ancha. Agregado en el nivel de API 4 (Android 1,6). Los valores posibles son Long e notlong.

- **Orientación** de la pantalla &ndash; orientación vertical u horizontal de la pantalla. Esto puede cambiar durante la vigencia de una aplicación.
  Los valores posibles son `port` y `land`.

- **Modo de acoplamiento** &ndash; para dispositivos en un Dock o en un muelle de escritorio. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `car` y `desk`.

- **Modo nocturno** &ndash; si la aplicación se está ejecutando por la noche o en el día. Esto puede cambiar durante la vigencia de una aplicación y está diseñado para ofrecer a los desarrolladores una oportunidad de usar versiones más oscuras de una interfaz por la noche. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `night` y `notnight`.

- **Densidad de píxeles de pantalla (PPP)** &ndash; el número de píxeles de un área determinada en la pantalla física. Normalmente expresado como puntos por pulgada (PPP). Los valores posibles son:

  - `ldpi` &ndash; pantallas de baja densidad.

  - `mdpi` &ndash; pantallas de densidad media

  - `hdpi` &ndash; pantallas de alta densidad

  - `xhdpi` &ndash; pantallas de alta densidad extra

  - `nodpi` &ndash; recursos que no se van a escalar

  - `tvdpi` &ndash; introducido en el nivel de API 13 (Android 3,2) para pantallas entre pantallas y hdpi.

- **Tipo de pantalla táctil** &ndash; especifica el tipo de pantalla táctil que puede tener un dispositivo. Los valores posibles son `notouch` (sin pantalla táctil), `stylus` (una pantalla táctil resistente adecuada para un lápiz) y `finger` (una pantalla táctil).

- &ndash; de **disponibilidad de teclado** especifica qué tipo de teclado está disponible. Esto puede cambiar durante la vigencia de una aplicación &ndash; por ejemplo, cuando un usuario abre un teclado de hardware. Los valores posibles son:

  - `keysexposed` &ndash; el dispositivo tiene un teclado disponible. Si no hay ningún teclado de software habilitado, solo se utilizará cuando se abra el teclado de hardware.

  - `keyshidden` &ndash; el dispositivo tiene un teclado de hardware, pero está oculto y no hay ningún teclado de software habilitado.

  - `keyssoft` &ndash; el dispositivo tiene un teclado de software habilitado.

- El **método de entrada de texto principal** &ndash; usar para especificar qué tipos de claves de hardware están disponibles para la entrada. Los valores posibles son:

  - `nokeys` &ndash; no hay ninguna clave de hardware para la entrada.

  - `qwerty` &ndash; hay un teclado QWERTY disponible.

  - `12key` &ndash; hay un teclado de hardware de 12 teclas

- La disponibilidad de la **clave de navegación** &ndash; para cuando la navegación de 5 o d (pad direccional) esté disponible. Esto puede cambiar durante la vigencia de la aplicación. Los valores posibles son:

  - `navexposed` &ndash; las teclas de navegación están disponibles para el usuario

  - `navhidden` &ndash; las teclas de navegación no están disponibles.

- **Método de navegación no táctil principal** &ndash; el tipo de navegación disponible en el dispositivo. Los valores posibles son:

  - `nonav` &ndash; la única utilidad de navegación disponible es la pantalla táctil

  - `dpad` &ndash; está disponible un panel de dirección d (almohadilla) para la navegación

  - `trackball` &ndash; el dispositivo tiene un trackball para la navegación

  - `wheel` &ndash; escenario poco frecuente en el que hay una o varias ruedas direccionales disponibles

- **Versión de la plataforma (nivel de API)** &ndash; el nivel de API compatible con el dispositivo en el formato v*N*, donde *N* es el nivel de API que se va a usar como destino. Por ejemplo, V11 se dirigirá a un dispositivo de nivel de API 11 (Android 3,0).

Para obtener información más completa sobre los calificadores de recursos, vea [proporcionar recursos](https://developer.android.com/guide/topics/resources/providing-resources.html) en el sitio web de desarrolladores de Android.

## <a name="how-android-determines-what-resources-to-use"></a>Cómo determina Android qué recursos usar

Es muy posible y es probable que una aplicación Android contenga muchos recursos. Es importante comprender cómo Android seleccionará los recursos de una aplicación cuando se ejecuta en un dispositivo.

Android determina la base de recursos mediante la iteración de la siguiente prueba de reglas:

- **Elimine los calificadores contradictorios** &ndash; por ejemplo, si la orientación del dispositivo es vertical, se rechazarán todos los directorios de recursos del entorno.

- **Omitir calificadores no admitidos** &ndash; no todos los calificadores están disponibles para todos los niveles de API. Si un directorio de recursos contiene un calificador que no es compatible con el dispositivo, se omitirá ese directorio de recursos.

- **Identifique el siguiente calificador de prioridad más alta** &ndash; que haga referencia a la tabla anterior Seleccione el siguiente calificador de prioridad más alta (de arriba abajo).

- **Mantenga los directorios de recursos para el calificador** &ndash; si hay algún directorio de recursos que coincida con el calificador en la tabla anterior, seleccione el calificador de prioridad siguiente (de arriba abajo).

Estas reglas también se muestran en el diagrama de flujo siguiente:

[diagrama de flujo de![recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

Cuando el sistema busca recursos específicos de la densidad y no los encuentra, intentará localizar otros recursos específicos de la densidad y escalarlos. Es posible que Android no use necesariamente los recursos predeterminados.
Por ejemplo, al buscar un recurso de baja densidad y no está disponible, Android puede seleccionar la versión de alta densidad del recurso a través de los recursos predeterminados o de densidad media. Esto se debe a que el recurso de alta densidad puede reducirse en un factor de 0,5, lo que producirá menos problemas de visibilidad que el escalado vertical de un recurso de densidad media, lo que requeriría un factor de 0,75.

Como ejemplo, considere una aplicación que tiene los siguientes directorios de recursos Dibujables:

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

Y ahora la aplicación se ejecuta en un dispositivo con la configuración siguiente:

- **Configuración regional** &ndash; en-GB
- Puerto de **orientación** &ndash;
- **Densidad de pantalla** &ndash; hdpi
- **Tipo de pantalla táctil** &ndash; NoTouch
- **Método de entrada principal** &ndash; 12key

Para empezar, los recursos en francés se eliminan cuando entran en conflicto con la configuración regional de `en-GB`, y nos dejan con:

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

A continuación, se selecciona el primer calificador de la tabla de calificadores anterior: MCC y MNC. No hay directorios de recursos que contengan este calificador, por lo que se omite el código de MCC/MNC.

Se selecciona el siguiente calificador, que es el idioma. Hay recursos que coinciden con el código de idioma. Se rechazan todos los directorios de recursos que no coinciden con el código de idioma de `en`, de modo que la lista de recursos es ahora:

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

El siguiente calificador que está presente es para la orientación de la pantalla, por lo que se eliminan todos los directorios de recursos que no coinciden con la orientación de pantalla de `port`:

```
drawable-en-port
drawable-en-port-ldpi
```

El siguiente es el calificador para la densidad de pantalla, `ldpi`, lo que da como resultado la exclusión de un directorio de recursos más:

```
drawable-en-port-ldpi
```

Como resultado de este proceso, Android usará los recursos que se dibujan en el directorio de recursos `drawable-en-port-ldpi` para el dispositivo.

> [!NOTE]
> Los calificadores de tamaño de pantalla proporcionan una excepción a este proceso de selección. Es posible que Android seleccione recursos diseñados para una pantalla más pequeña que el que proporciona el dispositivo actual. Por ejemplo, un dispositivo de pantalla grande puede usar los recursos proporcionados para una pantalla de tamaño normal. Sin embargo, lo contrario no es cierto: el mismo dispositivo de pantalla grande no usará los recursos proporcionados para una pantalla de xlarge. Si Android no encuentra un conjunto de recursos que coincida con un tamaño de pantalla determinado, se bloqueará la aplicación.
