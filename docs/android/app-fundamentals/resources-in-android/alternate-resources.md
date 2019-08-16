---
title: Recursos alternativos
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 7c48244e316854b04234ec8dee4eebeb55a4f119
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526378"
---
# <a name="alternate-resources"></a>Recursos alternativos

Los recursos alternativos son aquellos que tienen como destino un dispositivo específico o una configuración en tiempo de ejecución, como el idioma actual, el tamaño de pantalla concreto o la densidad de píxeles. Si Android puede coincidir con un recurso más específico para un determinado dispositivo o configuración que el recurso predeterminado, se utilizará ese recurso en su lugar. Si no encuentra un recurso alternativo que coincida con la configuración actual, se cargarán los recursos predeterminados. La forma en que Android decide qué recursos usará una aplicación se tratará con más detalle a continuación, en la sección Ubicación de recursos.

Los recursos alternativos se organizan como un subdirectorio dentro de la carpeta recursos según el tipo de recurso, al igual que los recursos predeterminados. El nombre del subdirectorio de recurso alternativo tiene el formato siguiente: _ResourceType_-_Qualifier_

El calificador es un nombre que identifica una configuración específica del dispositivo.
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

- **MCC y MNC** El código de [país móvil](https://en.wikipedia.org/wiki/List_of_mobile_country_codes) (MCC) y, opcionalmente, el [código de red móvil](https://en.wikipedia.org/wiki/Mobile_Network_Code) (MNC). &ndash; La tarjeta SIM proporcionará la MCC, mientras que la red a la que está conectado el dispositivo proporcionará el MNC. Aunque es posible destinar a las configuraciones regionales con el código de país móvil, el enfoque recomendado es usar el calificador de lenguaje que se especifica a continuación. Por ejemplo, para dirigirse a los recursos a Alemania, el `mcc262`calificador sería. Para dirigirse a recursos de T-Mobile en EE. UU., el `mcc310-mnc026`calificador es.
  Para obtener una lista completa de códigos de país móvil y códigos de <http://mcc-mnc.com/>red móvil, consulte.

- **Idioma** de El código de [idioma ISO 639-1](https://en.wikipedia.org/wiki/ISO_639-1) de dos letras y, opcionalmente, seguido del código de la [región ISO-3166-Alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)de dos letras. &ndash; 
  Si se proporcionan ambos calificadores, se separan mediante un `-r`. Por ejemplo, para tener como destino las configuraciones regionales en francés, se `fr` usa el calificador de. Para tener como destino las `fr-rCA` configuraciones regionales en francés (Canadá), se utilizaría. Para obtener una lista completa de códigos de idioma y códigos de región, consulte [códigos para la representación de los nombres de los idiomas](http://www.loc.gov/standards/iso639-2/php/English_list.php) y [los nombres de los países y los elementos de código](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm).

- **Menor ancho** &ndash; Especifica el ancho de pantalla más pequeño en el que se va a ejecutar la aplicación. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Por ejemplo, el calificador `sw320dp` se usa para destinar dispositivos cuyo alto y ancho sea al menos 320dp.

- **Ancho disponible** El ancho mínimo de la pantalla con el formato w*n*DP, donde n es el ancho en píxeles independientes de densidad. &ndash;
  Este valor puede cambiar a medida que el usuario gira el dispositivo. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Ejemplo: el calificador w720dp se usa para dispositivos de destino que tienen un ancho de menos de 720dp.

- **Alto disponible** El alto mínimo de la pantalla con el formato h*n*DP, donde N es el alto de DP. &ndash; Este valor puede cambiar a medida que el usuario gira el dispositivo. Se describe con más detalle en [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Disponible en el nivel de API 13 (Android 3,2) y versiones posteriores. Por ejemplo, el calificador h720dp se usa para dispositivos de destino que tienen un alto de menos de 720dp

- **Tamaño de pantalla** &ndash; Este calificador es una generalización del tamaño de la pantalla para el que son estos recursos. Se trata con más detalle en la [creación de recursos para diferentes pantallas](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md). 
  Los valores posibles son `small`, `normal`, `large` y `xlarge`. Agregado en el nivel de API 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **Aspecto** de la pantalla &ndash; Esto se basa en la relación de aspecto, no en la orientación de la pantalla. Una pantalla larga es más ancha. Agregado en el nivel de API 4 (Android 1,6). Los valores posibles son Long e notlong.

- **Orientación** de la pantalla &ndash; Orientación de pantalla vertical u horizontal. Esto puede cambiar durante la vigencia de una aplicación.
  Los valores posibles son `port` y `land`.

- **Modo de acoplamiento** &ndash; En el caso de los dispositivos de una base de coche o un muelle de mesa. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `car` y `desk`.

- **Modo nocturno** &ndash; Si la aplicación se está ejecutando por la noche o en el día. Esto puede cambiar durante la vigencia de una aplicación y está diseñado para ofrecer a los desarrolladores una oportunidad de usar versiones más oscuras de una interfaz por la noche. Agregado en el nivel de API 8 (Android 2.2. x). Los valores posibles son `night` y `notnight`.

- **Densidad de píxeles de pantalla (PPP)** &ndash; Número de píxeles de un área determinada en la pantalla física. Normalmente expresado como puntos por pulgada (PPP). Los valores posibles son:

    - `ldpi`&ndash; Pantallas de baja densidad.

    - `mdpi`&ndash; Pantallas de densidad media

    - `hdpi`&ndash; Pantallas de alta densidad

    - `xhdpi`&ndash; Pantallas de alta densidad adicional

    - `nodpi`&ndash; Recursos que no se van a escalar

    - `tvdpi`&ndash; Introducido en el nivel de API 13 (Android 3,2) para pantallas entre pantallas y hdpi.

- **Tipo de pantalla táctil** &ndash; Especifica el tipo de pantalla táctil que puede tener un dispositivo. Los valores posibles `notouch` son (sin pantalla táctil) `stylus` , (una pantalla táctil resistente adecuada para un lápiz) y `finger` (una pantalla táctil).

- **Disponibilidad del teclado** &ndash; Especifica qué tipo de teclado está disponible. Esto puede cambiar durante la vigencia de una aplicación &ndash; , por ejemplo, cuando un usuario abre un teclado de hardware. Los valores posibles son:

    - `keysexposed`&ndash; El dispositivo tiene un teclado disponible. Si no hay ningún teclado de software habilitado, solo se utilizará cuando se abra el teclado de hardware.

    - `keyshidden`&ndash; El dispositivo tiene un teclado de hardware, pero está oculto y no hay ningún teclado de software habilitado.

    - `keyssoft`&ndash; el dispositivo tiene un teclado de software habilitado.

- **Método de entrada de texto principal** &ndash; Utilice para especificar qué tipos de claves de hardware están disponibles para la entrada. Los valores posibles son:

    - `nokeys`&ndash; No hay claves de hardware para la entrada.

    - `qwerty`&ndash; Hay un teclado QWERTY disponible.

    - `12key`&ndash; Hay un teclado de hardware de 12 teclas


- **Disponibilidad** de la clave de navegación &ndash; Para cuando esté disponible la navegación de 5 o d (pad direccional). Esto puede cambiar durante la vigencia de la aplicación. Los valores posibles son:

    - `navexposed`&ndash; las teclas de navegación están disponibles para el usuario

    - `navhidden`&ndash; las teclas de navegación no están disponibles.

- **Método de navegación no táctil principal** &ndash; El tipo de navegación disponible en el dispositivo. Los valores posibles son:

    - `nonav`&ndash; la única característica de navegación disponible es la pantalla táctil

    - `dpad`&ndash; un panel de control de acceso (PAD) está disponible para la navegación

    - `trackball`&ndash; el dispositivo tiene un trackball para la navegación

    - `wheel`&ndash; escenario poco frecuente en el que hay una o varias ruedas direccionales disponibles

- **Versión de la plataforma (nivel de API)** El nivel de API que admite el dispositivo en el formato v*n*, donde N es el nivel de API que se va a usar como destino. &ndash; Por ejemplo, V11 se dirigirá a un dispositivo de nivel de API 11 (Android 3,0).


Para obtener información más completa sobre los calificadores de recursos, vea [proporcionar recursos](https://developer.android.com/guide/topics/resources/providing-resources.html) en el sitio web de desarrolladores de Android.


## <a name="how-android-determines-what-resources-to-use"></a>Cómo determina Android qué recursos usar

Es muy posible y es probable que una aplicación Android contenga muchos recursos. Es importante comprender cómo Android seleccionará los recursos de una aplicación cuando se ejecuta en un dispositivo.

Android determina la base de recursos mediante la iteración de la siguiente prueba de reglas:

- **Eliminación** de calificadores contradictorios &ndash; por ejemplo, si la orientación del dispositivo es vertical, se rechazarán todos los directorios de recursos del entorno.

- **Omitir calificadores no admitidos** &ndash; No todos los calificadores están disponibles para todos los niveles de API. Si un directorio de recursos contiene un calificador que no es compatible con el dispositivo, se omitirá ese directorio de recursos.

- **Identificar el calificador de mayor prioridad siguiente** &ndash; al hacer referencia a la tabla anterior, se selecciona el calificador de prioridad superior siguiente (de arriba abajo).

- **Mantener los directorios de recursos para** el calificador &ndash; si hay algún directorio de recursos que coincida con el calificador de la tabla anterior, seleccione el calificador de prioridad más alta (de arriba abajo).

Estas reglas también se muestran en el diagrama de flujo siguiente:

[![Diagrama de flujo de recursos](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

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
- **Orientación** &ndash; puerto de
- **Densidad de pantalla** &ndash; hdpi
- **Tipo de pantalla táctil** &ndash; NoTouch
- **Método de entrada principal** &ndash; 12key

Para empezar, los recursos en francés se eliminan a medida que entran en conflicto `en-GB`con la configuración regional de, y nos dejan con:

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

Se selecciona el siguiente calificador, que es el idioma. Hay recursos que coinciden con el código de idioma. Se rechazan todos los directorios de recursos que no coinciden con el código de idioma de `en` , de modo que la lista de recursos es ahora:

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

El siguiente calificador que está presente es para la orientación de la pantalla, por lo que se eliminan todos los `port` directorios de recursos que no coinciden con la orientación de la pantalla de:

```
drawable-en-port
drawable-en-port-ldpi
```

El siguiente es el calificador para la `ldpi`densidad de pantalla,, lo que da como resultado la exclusión de un directorio de recursos más:

```
drawable-en-port-ldpi
```

Como resultado de este proceso, Android usará los recursos que se dibujan en el directorio `drawable-en-port-ldpi` de recursos para el dispositivo.

> [!NOTE]
> Los calificadores de tamaño de pantalla proporcionan una excepción a este proceso de selección. Es posible que Android seleccione recursos diseñados para una pantalla más pequeña que el que proporciona el dispositivo actual. Por ejemplo, un dispositivo de pantalla grande puede usar los recursos proporcionados para una pantalla de tamaño normal. Sin embargo, lo contrario no es cierto: el mismo dispositivo de pantalla grande no usará los recursos proporcionados para una pantalla de xlarge. Si Android no encuentra un conjunto de recursos que coincida con un tamaño de pantalla determinado, se bloqueará la aplicación.
