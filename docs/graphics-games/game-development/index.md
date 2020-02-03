---
title: Introducción al desarrollo de juegos con Xamarin
description: En este documento se proporciona información general de alto nivel sobre el desarrollo de juegos con Xamarin, que describen cómo se realizan los juegos y un muestreo de las tecnologías disponibles para su uso con Xamarin. iOS y Xamarin. Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 5992e8df3080bb35fd123483e5ffb5e64f268b1a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724764"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introducción al desarrollo de juegos con Xamarin

El desarrollo de juegos puede ser muy emocionante, especialmente dado lo fácil que puede ser publicar su trabajo en plataformas móviles. En este artículo se describen los conceptos y las tecnologías relacionados con el desarrollo de juegos que le ayudarán a crear juegos, tanto si el objetivo es crear un juego AAA de alta calidad como si solo se va a programar para divertirse.

En este artículo se tratan los temas siguientes:

- **Juegos frente a conceptos de programación que no son de juegos** : exploraremos algunos conceptos que son exclusivos del desarrollo de juegos o que se comparten con otros tipos de desarrollo, pero merecemos hincapié aquí debido a su importancia.
- **Equipo de desarrollo de juegos** : en esta sección se examinan los distintos roles de un equipo de desarrolladores de juegos.
- **Crear una idea de juego** : esta sección puede ayudarle a crear una nueva idea de juego, el primer paso para crear un nuevo juego.
- **Tecnología de desarrollo de juegos** : aquí se enumeran algunas de las tecnologías multiplataforma disponibles que pueden mejorar su productividad como desarrollador de juegos.

## <a name="game-vs-non-game-programming-concepts"></a>Conceptos de programación sin juegos

Los programadores que se trasladan al desarrollo de juegos suelen encontrarse con nuevos conceptos y patrones de desarrollo. En esta sección se presenta una vista de alto nivel de algunos de estos conceptos.

### <a name="the-game-loop"></a>El bucle del juego

Un juego típico requiere un movimiento constante o un cambio para que se produzca en la pantalla en respuesta a la interacción del usuario y a la lógica automática del juego. Esto se logra a través de lo que se conoce normalmente como un *bucle de juego*. Un bucle de juego es algún tipo de instrucción de bucle (como un bucle while) que se ejecuta con una frecuencia muy alta, como 30 o 60 *fotogramas por segundo*.

El siguiente es un diagrama de un bucle de juego sencillo:

![](images/image1.png "This is a diagram of a simple game loop")

Las tecnologías que se describen a continuación abstraerán el bucle while real, pero a pesar de esta abstracción, el concepto de actualizaciones de cada fotograma estará presente.

El rendimiento del código puede tener prioridad incluso en los juegos más sencillos. Por ejemplo: una función que tarda 10 milisegundos en ejecutarse puede tener un impacto significativo en el rendimiento de un juego, especialmente si se llama más de una vez por fotograma. Si el juego se ejecuta a 30 fotogramas por segundo, significa que cada fotograma debe ejecutarse en menos de 33 milisegundos. Por el contrario, una función de este tipo no se puede apreciar incluso si solo se ejecuta en respuesta a un clic de botón en una aplicación que no es de juego.

Los tipos comunes de lógica que se pueden realizar en cada fotograma incluyen:

- **Lectura** de la entrada: el juego puede tener que comprobar si el usuario ha interactuado con el juego comprobando el hardware de entrada, como la pantalla táctil, el teclado, el mouse o el controlador de juegos.
- **Movimiento** : los objetos que se mueven de un lugar a otro normalmente mueven una cantidad muy pequeña cada fotograma para dar la ilusión de movimiento fluido.
- **Colisión** : muchos juegos requieren una prueba frecuente de si varios objetos se superponen o intersectan. En una sección posterior de este artículo se tratará la colisión con más detalle. El movimiento y la colisión pueden controlarse mediante un sistema de simulación física dedicado.
- **Comprobando condiciones específicas del juego** : el estado de un juego puede estar controlado por ciertas condiciones, por ejemplo, si el jugador ha obtenido suficientes puntos o si se ha agotado el tiempo asignado.
- **Comportamiento de AI** : lógica de cada fotograma que se puede utilizar para controlar el comportamiento de los objetos que no son controlados por el jugador, como la patrulla de un enemigo o el movimiento de controladores de oponentes en torno a un Racetrack.
- **Representación** : la mayoría de los juegos actualizarán lo que se muestra en la pantalla cada fotograma. Esto se puede hacer en respuesta a los cambios que afectan a la reproducción de juego (por ejemplo, un carácter que se desplaza a través de un nivel) o simplemente para proporcionar un polaco visual (por ejemplo, un descenso de nieve o iconos animados).

Tenga en cuenta que muchas de las actividades enumeradas anteriormente pueden cambiar el estado de toda la aplicación, mientras que muchas aplicaciones que no son de juegos tienden a cambiar el estado en respuesta a eventos que se generan.

### <a name="content-loading-and-unloading"></a>Carga y descarga de contenido

Es posible que sea necesario cargar y descargar (o eliminar) manualmente el contenido en función de la tecnología que se use en el desarrollo. Es posible que sea necesario cargar y descargar manualmente los recursos por una serie de motivos:

- Los recursos pueden tardar mucho tiempo en cargarse en relación con la longitud de un solo fotograma. Algunos recursos pueden tardar incluso segundos en cargarse, lo que interrumpiría gravemente la experiencia si el juego está cargado. Si el tiempo de carga es especialmente largo (por ejemplo, más de un segundo o dos), puede que desee mostrar una pantalla de carga animada o una barra de progreso.
- Los recursos pueden consumir mucha RAM, lo que requiere la administración activa de lo que se carga para ajustarse a lo que se proporciona en las plataformas de destino del juego.
- Es posible que los juegos deban Mostrar más recursos de los que caben en la RAM. Los juegos "abiertos en todo el mundo" incluyen a menudo entornos de gran tamaño que los jugadores pueden navegar a través sin problemas, es decir, sin pantallas de carga. En este caso, puede que tenga que crear un sistema personalizado para el streaming de contenido en y administrar el uso de memoria.

Los formatos de archivo personalizados pueden necesitar procesamiento en tiempo de carga, lo que requiere código de carga personalizado.

### <a name="math"></a>Matemáticas

Muchos juegos requieren matemáticas más avanzadas que las aplicaciones que no son de juegos. Por supuesto, el nivel de matemáticas depende de la complejidad del juego. En general, los juegos en 3D requieren más cálculos que 2D. Afortunadamente, siempre puede empezar a trabajar con juegos sencillos y aprender a medida que avanza. El desarrollo de juegos puede ser una excelente manera de aprender las matemáticas.

Si está familiarizado con el plano cartesiano, que usa las coordenadas X e Y para colocar objetos, sabrá lo suficiente como para empezar a trabajar con el desarrollo de juegos. A continuación se muestra un plano cartesiano con un puntero Y apuntando hacia arriba:

![](images/image2.png "This shows a Cartesian plane with positive Y pointing upward")

> [!IMPORTANT]
> Algunos motores o API usan un sistema de coordenadas en el que al aumentar el valor Y de un objeto se desactivará, mientras que otros sistemas utilizarán un sistema de coordenadas donde el Y positivo esté activo. Tenga esto en cuenta si va a mover entre sistemas.
Las funciones trigonométricas (como el seno y el coseno) se usan normalmente en juegos en 2D que implementan cualquier forma de rotación.

Si planea realizar un juego en 3D, probablemente deberá estar familiarizado con los conceptos de álgebra lineal (para la rotación y el movimiento en el espacio 3D), así como algunos cálculo (para implementar la aceleración).

### <a name="content-pipelines"></a>Canalizaciones de contenido

El término *canalización de contenido* hace referencia al proceso que tarda un archivo en obtenerse de su formato cuando se crea (por ejemplo, un archivo de imagen. png) en su formato final cuando se usa en un juego. El formato final depende del tipo de contenido que se use, así como de la tecnología que se utiliza para presentar el contenido.

Algunas canalizaciones de contenido pueden ser muy rápidas y no requieren ningún esfuerzo manual. Por ejemplo, la mayoría de los motores de juegos y las API pueden cargar el formato de archivo. png en su formato sin procesar. Por otro lado, es posible que los formatos más complicados (como los modelos 3D) deban procesarse en un formato diferente antes de cargarse, y este proceso puede tardar algún tiempo en función del tamaño y la complejidad del recurso.

## <a name="game-development-teams"></a>Equipos de desarrollo de juegos

El desarrollo de juegos presenta nuevos roles y títulos para las personas implicadas en el proceso. La mayoría de los desarrolladores de juegos no pueden satisfacer el amplio conjunto de conocimientos necesarios para lanzar un juego completo, por lo que existe una serie de disciplinas. Tenga en cuenta que esta no es una lista completa de las áreas de desarrollo, solo algunas de las más comunes.

- **Programador** : la mayoría de las personas que lean este artículo entrarán en esta categoría. El rol de un programador en el desarrollo de juegos es similar al rol de un programador en una aplicación que no es de juego. Las responsabilidades incluyen la escritura de lógica para controlar el flujo de un juego, el desarrollo de sistemas para tareas comunes en el contexto de un proyecto determinado, la adición y visualización de contenido, y, por supuesto, la corrección de errores.
- **artista 2D** : los artistas 2D son responsables de la creación de *recursos 2D*. Estos incluyen archivos de imagen para la GUI, los objetos, los entornos y los caracteres del juego. Si el juego que está desarrollando es 3D, es posible que los artistas de 2D no sean responsables de los entornos y los caracteres. Puede encontrar un arte gratuito del juego en [http://opengameart.org/](http://opengameart.org/) .
- **artistas en 3D** : los artistas 3D son responsables de la creación de *recursos 3D*. Estos incluyen modelos 3D para entornos, caracteres y propiedades (mobiliario, plantas y otros objetos inanimados). Algunos equipos diferencian entre los artistas 3D y los animadores 3D en función del tamaño del equipo. Puede encontrar una imagen en 3D gratis para su juego en [http://opengameart.org/](http://opengameart.org/) .
- **Diseñador de juegos** : los diseñadores de juegos son responsables de definir cómo se reproduce el juego. Esto puede incluir decisiones de alto nivel, como la configuración del juego, el objetivo general del juego y cómo progresa un jugador a través del juego. Los diseñadores de juegos también pueden participar en decisiones muy detalladas, como la asignación de entradas a acciones, la definición de coeficientes para el movimiento o los niveles y el diseño de nivel. Tenga en cuenta que el término *Diseñador* puede hacer referencia a un diseñador de juegos o a un diseñador visual dependiendo del contexto.
- **Diseñador de sonido** : los diseñadores de sonido son responsables de los recursos de audio de un juego. Algunos equipos pueden diferenciar entre los individuos responsables de crear efectos y comparadores de sonido, mientras que los equipos más pequeños pueden tener una sola persona responsable de todo el audio.

## <a name="creating-a-game-idea"></a>Crear una idea de juego

Es posible que el diseño de un juego parezca sencillo: después de que todo el único requisito sea "hacer algo divertido". Desafortunadamente, muchos desarrolladores se encuentran en una pérdida cuando llega el momento de crear una idea a partir de la cual iniciar el desarrollo.

La disciplina del diseño del juego no se explica con facilidad y requiere la práctica para mejorar de la misma forma que lo hace el arte o la programación, pero esta sección puede ayudarle a iniciar la ruta de acceso.

Los nuevos desarrolladores deben empezar a ser pequeños. Puede ser difícil resistir la tentación de recrear un juego de vídeo moderno y grande, pero los juegos más pequeños pueden ser un entorno de aprendizaje mejor y el progreso más rápido permite una experiencia más gratificante.

Muchos juegos, tanto para el aprendizaje como para los juegos comerciales, comienzan como una mejora o una modificación en un juego existente. Una manera de generar ideas es mirar otros juegos para inspiración. Por ejemplo, puede tener en cuenta un juego que le gusta personalmente e intentar identificar qué características de la reproducción de juego hacen que sea divertido. Puede ser exploración, dominar la mecánica del juego o progresar a través de una historia. No olvide tener en cuenta los juegos "retro" al buscar nuevas ideas.

Otra técnica para generar nuevas ideas es considerar un género específico, como juegos puzzles, juegos de estrategia o plataformas. Un género familiar para el desarrollador podría proporcionar un buen punto de partida.

La reelaboración de los juegos existentes también es una experiencia educativa, aunque esto puede limitar la viabilidad comercial del producto terminado. El proceso de creación de un juego, incluso uno que es un clon preciso, proporciona una experiencia educativa valiosa.

## <a name="game-development-technology"></a>Tecnología de desarrollo de juegos

Los desarrolladores que usan Xamarin. Android y Xamarin. iOS tienen una amplia gama de tecnologías disponibles para ayudar en el desarrollo de juegos. En esta sección se describen algunas de las soluciones multiplataforma más populares.

### <a name="monogame"></a>MonoGame

Monogame es una versión multiplataforma de código abierto de la API XNA de Microsoft. Monogame puede usarse para crear juegos para iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One y switch.

Monogame no es técnicamente un motor de juegos, sino una API de desarrollo de juegos. Esto significa que trabajar con monogame requiere la administración directa de objetos de juego, el dibujo manual de objetos y la implementación de objetos comunes, como cámaras y *gráficos de escenas* (la jerarquía de elementos primarios y secundarios entre objetos de juego).

Monogame no ofrece un entorno de desarrollo visual estándar, por lo que trabajar con monogame requiere conocimientos de programación.

A continuación se incluyen ejemplos importantes de juegos con monogame:

FEZ:

![](images/image7.png "FEZ")

Bastión

![](images/image8.jpg "Bastion")

Para empezar a trabajar con monogame, diríjase a nuestras [guías de monogame](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp es un motor 3D y 2D de alto nivel multiplataforma que se puede usar para crear escenas animadas 3D y 2D para las aplicaciones mediante geometrías, materiales, luces y cámaras.

![](images/urhosharp.gif "UrhoSharp is a cross-platform high-level 3D and 2D engine that can be used to create animated 3D and 2D scenes")

Consulte las [guías de UrhoSharp](~/graphics-games/urhosharp/index.md) para empezar a trabajar.

### <a name="additional-technology"></a>Tecnología adicional

Las tecnologías resaltadas anteriormente son solo un ejemplo de las tecnologías disponibles. Otras tecnologías importantes incluyen:

- **Sprite kit** : Xamarin proporciona compatibilidad con el juego de juegos de Sprite de Apple, que le proporciona acceso a toda la funcionalidad de la API nativa. Dado que el kit de Sprite es una tecnología creada por Apple, proporciona una integración profunda con el resto del ecosistema de iOS. Por supuesto, el kit de Sprite no es multiplataforma, por lo que no se puede usar en Android. Para obtener más información sobre el uso del kit de Sprite, consulte esta entrada: [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de escenas** : Xamarin también proporciona compatibilidad con el marco de escenas del kit de Apple, que simplifica la implementación de gráficos 3D en aplicaciones iOS. El kit de escenas también es una tecnología que Apple proporciona, por lo que tiene tanto la integración como las consideraciones específicas de la plataforma mencionadas anteriormente para el kit de Sprite. Para obtener más información sobre el kit de escenas, consulte esta entrada: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK:** OpenTK (que se encuentra en Open Tool Kit) proporciona acceso OpenGL de bajo nivel al hardware de iOS, Apple y Mac. Para obtener más información sobre OpenTK, vea la Página principal en: [https://opentk.net/](https://opentk.net/)

## <a name="related-links"></a>Vínculos relacionados

- [Guías monogame](~/graphics-games/monogame/index.md)
- [Guías de UrhoSharp](~/graphics-games/urhosharp/index.md)
