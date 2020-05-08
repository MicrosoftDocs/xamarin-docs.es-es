---
title: Rendimiento multiplataforma
description: En este documento se describen diversas técnicas que pueden usarse para mejorar el rendimiento de una aplicación móvil. Además, analiza el generador de perfiles, el recurso IDisposable, las referencias débiles, el recolector de elementos no utilizados SGen, las técnicas de reducción de tamaño y mucho más.
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: f99b64b67f4f1cabf3a5884fabb1301e5ee39eee
ms.sourcegitcommit: 06043f6a5628a7326ac6690eb62ead8e4780f4a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738794"
---
# <a name="cross-platform-performance"></a>Rendimiento multiplataforma

El mal rendimiento de una aplicación se manifiesta de muchas formas. Puede hacer que parezca que una aplicación deja de responder, puede ocasionar un desplazamiento lento y puede reducir la duración de la batería. La optimización del rendimiento conlleva mucho más que la mera implementación de código eficaz. También debe tenerse en cuenta la experiencia de rendimiento de la aplicación del usuario. Por ejemplo, asegurarse de que las operaciones se ejecuten sin evitar que el usuario realice otras actividades puede ayudar a mejorar su experiencia.

<a name="profiler" />

## <a name="use-the-profiler"></a>Usar el generador de perfiles

Al desarrollar una aplicación, es importante intentar optimizar el código únicamente cuando ya se ha generado un perfil. La generación de perfiles es una técnica para determinar dónde tendrán más efecto las optimizaciones de código a la hora de reducir problemas de rendimiento. El generador de perfiles realiza el seguimiento del consumo de memoria de la aplicación y registra el tiempo de ejecución de los métodos de la aplicación. Estos datos ayudan a navegar por las rutas de ejecución de la aplicación y el consumo de recursos de ejecución del código, por lo que permiten detectar las mejores oportunidades de optimización.

Xamarin Profiler mide, evalúa y ayuda a detectar problemas relacionados con el rendimiento en una aplicación. Se puede usar para generar perfiles de aplicaciones de Xamarin.iOS y Xamarin.Android desde Visual Studio para Mac o Visual Studio. Para más información sobre Xamarin Profiler, vea [Introducción a Xamarin Profiler](~/tools/profiler/index.md).

Al generar perfiles de una aplicación, estos son los procedimientos recomendados:

- Evite la generación de perfiles de una aplicación en un simulador, ya que este puede distorsionar el rendimiento de la aplicación.
- Lo ideal sería generar perfiles en varios dispositivos, ya que la toma de medidas de rendimiento en un dispositivo no siempre muestra las características de rendimiento de otros dispositivos. Como mínimo, la generación de perfiles debe realizarse en un dispositivo con la menor especificación prevista.
- Cierre las demás aplicaciones para garantizar la medición del impacto total de la aplicación cuyo perfil se está generando, y no del de las otras.

<a name="idisposable" />

## <a name="release-idisposable-resources"></a>Liberar los recursos de IDisposable

La interfaz `IDisposable` proporciona un mecanismo para liberar recursos. Proporciona un método `Dispose` que debe implementarse para liberar recursos de forma explícita. `IDisposable` no es un destructor y solo se debe implementar en los siguientes casos:

- Si la clase tiene recursos no administrados. Los recursos típicos no administrados que exigen liberación incluyen los archivos, las secuencias y las conexiones de red.
- Si la clase tiene recursos `IDisposable` administrados.

Los consumidores del tipo pueden llamar a la implementación `IDisposable.Dispose` para liberar recursos cuando ya no se necesite la instancia. Existen dos enfoques para conseguirlo:

- Ajustar el objeto `IDisposable` en una instrucción `using`.
- Ajustar la llamada a `IDisposable.Dispose` en un bloque `try`/`finally`.

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>Ajuste del objeto IDisposable en una instrucción using

En el ejemplo de código siguiente se muestra cómo ajustar un objeto `IDisposable` en una instrucción `using`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

La clase `StreamReader` implementa `IDisposable` y la instrucción `using` proporciona una sintaxis adecuada que llama al método `StreamReader.Dispose` en el objeto `StreamReader` antes de quede fuera del ámbito. Dentro del bloque `using`, el objeto `StreamReader` es de solo lectura y no se puede reasignar. La instrucción `using` también garantiza que se llame al método `Dispose` aun cuando se produzca una excepción, ya que el compilador implementa el lenguaje intermedio (IL) para un bloque `try`/`finally`.

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>Ajuste de la llamada a IDisposable.Dispose en un bloque Try/Finally

En el ejemplo de código siguiente se muestra cómo ajustar la llamada `IDisposable.Dispose` en un bloque `try`/`finally`:

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

La clase `StreamReader` implementa `IDisposable` y el bloque `finally` llama al método `StreamReader.Dispose` para liberar el recurso.

Para más información, vea [Interfaz IDisposable](xref:System.IDisposable).

<a name="events" />

## <a name="unsubscribe-from-events"></a>Cancelar la suscripción a eventos

Para evitar fugas de memoria, se debería cancelar la suscripción a eventos antes de que se elimine el objeto de suscriptor. Hasta que se cancela la suscripción al evento, el delegado del evento en el objeto de publicación tiene una referencia al delegado que encapsula el controlador de eventos del suscriptor. Mientras el objeto de publicación contenga esta referencia, la recolección de elementos no utilizados no recuperará la memoria del objeto de suscriptor.

En el ejemplo de código siguiente se muestra cómo cancelar la suscripción a un evento:

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

La clase `Subscriber` cancela la suscripción al evento en su método `Dispose`.

También se pueden producir ciclos de referencia al usar controladores de eventos y sintaxis lambda, ya que las expresiones lambda pueden hacer referencia a objetos activos y conservarlos. Por lo tanto, se puede almacenar una referencia al método anónimo en un campo y usarla para cancelar la suscripción al evento, como se muestra en el ejemplo de código siguiente:

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

El campo `handler` mantiene la referencia al método anónimo y se usa para la suscripción a eventos y su cancelación.

<a name="weakreferences" />

## <a name="use-weak-references-to-prevent-immortal-objects"></a>Usar referencias débiles para evitar objetos inmortales

> [!NOTE]
> Los desarrolladores de iOS deben revisar la documentación sobre cómo [evitar referencias circulares en iOS](~/ios/deploy-test/performance.md#avoid-strong-circular-references) para asegurarse de que sus aplicaciones usan memoria de forma eficaz.

<a name="lazy" />

## <a name="delay-the-cost-of-creating-objects"></a>Retrasar el consumo de recursos de la creación de objetos

Se puede usar la inicialización diferida para aplazar la creación de un objeto hasta que se use por primera vez. Esta técnica se usa principalmente para mejorar el rendimiento, evitar el cálculo y reducir los requisitos de memoria.

Considere el uso de la inicialización diferida para aquellos objetos cuya creación consume muchos recursos en estos dos escenarios:

- La aplicación podría no usar el objeto.
- Antes de crear el objeto, deben completarse otras operaciones que consumen muchos recursos.

Se usa la clase `Lazy<T>` para definir un tipo con inicialización diferida, como se muestra en el ejemplo de código siguiente:

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

La inicialización diferida se produce la primera vez que se accede a la propiedad `Lazy<T>.Value`. El tipo encapsulado se crea y se devuelve en el primer acceso y se almacena para cualquier acceso futuro.

Para más información sobre la inicialización diferida, vea [Lazy Initialization (Inicialización diferida)](https://msdn.microsoft.com/library/dd997286(v=vs.110).aspx).

<a name="async" />

## <a name="implement-asynchronous-operations"></a>Implementar operaciones asincrónicas

.NET proporciona versiones asincrónicas de muchas de sus API. A diferencia de las API sincrónicas, las API asincrónicas garantizan que el subproceso de ejecución activo nunca bloquee al subproceso de llamada durante una cantidad considerable de tiempo. Por lo tanto, al llamar a una API desde el subproceso de IU, use la API asincrónica si está disponible. Esto mantendrá desbloqueado al subproceso de IU, lo que le ayudará a mejorar la experiencia del usuario con la aplicación.

Además, las operaciones de larga ejecución deberían ejecutarse en un subproceso en segundo plano para evitar el bloqueo del subproceso de IU. .NET proporciona las palabras clave `async` y `await` que permiten la escritura de código asincrónico que ejecuta operaciones de larga ejecución en un subproceso en segundo plano, y accede a los resultados al finalizar. Pero aunque las operaciones de larga ejecución pueden ejecutarse asincrónicamente con la palabra clave `await`, esto no garantiza que la operación se ejecute en un subproceso en segundo plano. Esto puede realizarse pasando la operación de larga duración a `Task.Run`, como se muestra en el ejemplo de código siguiente:

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

El método `RecognizeFace` se ejecuta en un subproceso en segundo plano, con el método `RecognizeFaceButtonClick` en espera hasta que el método `RecognizeFace` se completa antes de continuar.

Las operaciones de larga ejecución también deberían permitir la cancelación. Por ejemplo, la continuación de una operación de larga duración podría ser innecesaria si el usuario se desplazara dentro de la aplicación. El modelo para implementar la cancelación es el siguiente:

- Cree una instancia `CancellationTokenSource`. Esta instancia administrará y enviará notificaciones de cancelación.
- Pase el valor de propiedad `CancellationTokenSource.Token` a cada tarea que deba cancelarse.
- Proporcione un mecanismo para que cada tarea responda a la cancelación.
- Llame al método `CancellationTokenSource.Cancel` para proporcionar una notificación de cancelación.

> [!IMPORTANT]
> La clase `CancellationTokenSource` implementa la interfaz `IDisposable`, y se debería invocar al método `CancellationTokenSource.Dispose` una vez que se termine con la instancia `CancellationTokenSource`.

Para más información, vea [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

<a name="sgen" />

## <a name="use-the-sgen-garbage-collector"></a>Usar el recolector de elementos no utilizados SGen

Los lenguajes administrados como C# usan la recolección de elementos no utilizados para reclamar la memoria asignada a los objetos que ya no están en uso. Los dos recolectores de elementos no utilizados que emplea la plataforma Xamarin son:

- [**SGen**](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/): se trata de un recolector de elementos no utilizados generacional y es el predeterminado en la plataforma Xamarin.
- [**Boehm**](http://www.hboehm.info/gc/): se trata de un recolector de elementos no utilizados no generacional conservador. Es el predeterminado para las aplicaciones de Xamarin.iOS que usan la API clásica.

SGen usa uno de tres montones para asignar espacio para los objetos:

- **Guardería**: es donde se asignan los nuevos objetos pequeños. Cuando se queda sin espacio, se produce una recolección de elementos no utilizados menor. Todos los objetos activos se mueven al montón principal.
- **Montón principal**: es donde se guardan los objetos de larga ejecución. Si no hay suficiente memoria en el montón principal, se produce una importante recolección de elementos no utilizados. Si se produce un error en una recolección de elementos no utilizados para liberar memoria, SGen le pide más memoria al sistema.
- **Espacio de objetos grandes**: es donde se guardan los objetos que necesitan más de 8000 bytes. Los objetos grandes no se envían a la guardería, sino que se asignan a este montón.

Una de las ventajas de SGen es que el tiempo necesario para realizar una recolección menor es proporcional al número de nuevos objetos activos creados desde la última recolección menor. Esto reduce el impacto de la recolección de elementos no utilizados sobre el rendimiento de una aplicación, ya que estas recolecciones menores tardan menos que una recolección importante. La recolección de elementos importante se seguirá produciendo, pero con menos frecuencia.

El recolector de elementos no utilizados SGen es el valor predeterminado en Xamarin.iOS 9.2.1 y en las versiones posteriores, por lo que se usará automáticamente. Tenga en cuenta que la posibilidad de cambiar el recolector de elementos no utilizados se ha quitado de las versiones más recientes de Visual Studio. Para obtener más información, vea [New Reference Counting System](~/ios/internals/newrefcount.md) (Nuevo sistema de recuento de referencias).

### <a name="reducing-pressure-on-the-garbage-collector"></a>Reducción de la presión sobre el recolector de elementos no utilizados

Cuando SGen inicia una recolección de elementos no utilizados, detiene los subprocesos de la aplicación mientras reclama memoria. Mientras se está reclamando memoria, la aplicación puede experimentar una breve pausa o parpadeo de la interfaz de usuario. Cómo de perceptible sea esta pausa depende de dos factores:

1. **Frecuencia**: frecuencia con la que se produce la recolección de elementos no utilizados. La frecuencia de las recolecciones de elementos no utilizados aumenta a medida que se asigna más memoria entre recolecciones.
1. **Duración**: tiempo que dura cada recolección de elementos no utilizados. Es aproximadamente proporcional al número de objetos activos que se recopila.

En general, esto significa que, si se asignan muchos objetos, pero no se mantienen activos, habrá muchas recolecciones breves de elementos no utilizados. Por el contrario, si se asignan nuevos objetos lentamente y se mantienen activos, habrá menos recolecciones pero serán más largas.

Para reducir la presión sobre el recolector de elementos no utilizados, siga estas directrices:

- Evite la recolección de elementos no utilizados en bucles estrechos mediante los grupos de objetos. Esto es de especial importancia para los juegos, que necesitan crear la mayoría de sus objetos por adelantado.
- Libere de forma explícita recursos como secuencias, conexiones de red, bloques grandes de memoria y archivos una vez que ya no sean necesarios. Para más información, vea [Liberar los recursos de IDisposable](#idisposable).
- Elimine del Registro los controladores de eventos una vez que ya no sean necesarios para que los objetos se puedan recopilar. Para más información, vea [Cancelar la suscripción a eventos](#events).

<a name="linker" />

## <a name="reduce-the-size-of-the-application"></a>Reducir el tamaño de la aplicación

Es importante entender el proceso de compilación en cada plataforma para comprender de dónde procede el tamaño del archivo ejecutable de una aplicación:

- Las aplicaciones de iOS se compilan Ahead Of Time (AOT) en lenguaje ensamblador ARM. Se incluye .NET Framework, y las clases no usadas se quitan solo si está habilitada la opción del enlazador adecuada.
- Las aplicaciones de Android se compilan en lenguaje intermedio (IL) y se empaquetan con MonoVM y compilación Just-In-Time (JIT). Las clases de Framework no usadas se quitan solo si está habilitada la opción del enlazador adecuada.
- Las aplicaciones de Windows Phone se compilan en IL y se ejecutan con el runtime integrado.

Además, si una aplicación usa mucho los genéricos, el tamaño final del archivo ejecutable aumentará más, puesto que incluirá versiones compiladas de forma nativa de las posibilidades del genérico.

Para reducir el tamaño de las aplicaciones, la plataforma Xamarin incluye un enlazador como parte de las herramientas de compilación. De forma predeterminada, el enlazador está deshabilitado y debe habilitarse en las opciones del proyecto de la aplicación. Durante la compilación, lleva a cabo un análisis estático de la aplicación para determinar qué tipos y miembros usa esta realmente. Luego quita los tipos y los métodos no usados de la aplicación.

En la siguiente captura de pantalla se muestran las opciones del enlazador en Visual Studio para Mac para un proyecto de Xamarin.iOS:

![](memory-perf-best-practices-images/linker-options-ios.png)

En la siguiente captura de pantalla se muestran las opciones del enlazador en Visual Studio para Mac para un proyecto de Xamarin.Android:

![](memory-perf-best-practices-images/linker-options-droid.png)

El enlazador proporciona tres opciones distintas para controlar su comportamiento:

- **Don’t Link (No vincular)** : no se quita ningún tipo ni método no usado por el enlazador. Por motivos de rendimiento, esta es la opción predeterminada para las compilaciones de depuración.
- **Link Framework SDKs/SDK Assemblies Only (Vincular solo ensamblados de SDK o SDK de Framework)** : esta opción solo reduce el tamaño de los ensamblados enviados por Xamarin. El código de usuario no se verá afectado.
- **Link All Assemblies (Vincular todos los ensamblados)** : se trata de una optimización más agresiva que se dirige a los ensamblados de SDK y al código de usuario. En el caso de los enlaces, quita los campos de respaldo no usados y aligera cada instancia (u objetos enlazados), consumiendo menos memoria.

*Link All Assemblies (Vincular todos los ensamblados)* debe usarse con precaución, ya que puede interrumpir la aplicación de formas inesperadas. El análisis estático que realiza el enlazador puede identificar incorrectamente todo el código necesario, lo que daría lugar a que se quitara demasiado código de la aplicación compilada. Esta situación solo se manifiesta en runtime cuando se bloquea la aplicación. Por ello es importante probar exhaustivamente una aplicación después de cambiar el comportamiento del enlazador.

Si las pruebas revelan que el enlazador ha quitado incorrectamente una clase o método, es posible marcar tipos o métodos a los que no se hace referencia de forma estática pero que son necesarios para la aplicación mediante uno de los siguientes atributos:

- `Xamarin.iOS.Foundation.PreserveAttribute`: este atributo es para proyectos de Xamarin.iOS.
- `Android.Runtime.PreserveAttribute`: este atributo es para proyectos de Xamarin.Android.

Por ejemplo, puede ser necesario conservar los constructores predeterminados de los tipos cuyas instancias se han creado de forma dinámica. Además, el uso de la serialización XML puede exigir que se conserven las propiedades de los tipos.

Para más información, vea [Linker for iOS (Enlazador para iOS)](~/ios/deploy-test/linker.md) y [Linker for Android (Enlazador para Android)](~/android/deploy-test/linker.md).

### <a name="additional-size-reduction-techniques"></a>Otras técnicas de reducción de tamaño

Hay una gran variedad de arquitecturas de CPU eficaces para los dispositivos móviles. Así, Xamarin.iOS y Xamarin.Android generan *archivos binarios fat* que contienen una versión compilada de la aplicación para cada arquitectura de CPU. Esto garantiza que una aplicación móvil se pueda ejecutar en un dispositivo independientemente de la arquitectura de CPU.

Los siguientes pasos pueden usarse para reducir aún más el tamaño del archivo ejecutable de la aplicación:

- Asegúrese de que se genere una compilación de versión.
- Reduzca el número de arquitecturas para el que se compila la aplicación a fin de evitar que se genere un archivo binario FAT.
- Asegúrese de que se use el compilador LLVM para generar un archivo ejecutable más optimizado.
- Reduzca el tamaño del código administrado de la aplicación. Esto se puede lograr si se habilita el enlazador en cada ensamblado (*Link All (Vincular todos)* para proyectos de iOS y *Link all assemblies (Vincular todos los ensamblados)* para proyectos de Android).

Las aplicaciones de Android también se pueden dividir en un APK independiente para cada ABI ("arquitectura").
Obtenga más información en la entrada de blog: [How To Keep Your Android App Size Down](https://montemagno.com/how-to-keep-your-android-app-size-down/) (Cómo reducir el tamaño de una aplicación de Android).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Optimizar los recursos de imagen

Las imágenes son uno de los recursos con más consumo usados por las aplicaciones y se suelen capturar en altas resoluciones. Aunque esto crea imágenes vibrantes llenas de detalles, las aplicaciones que muestran dichas imágenes normalmente necesitan usar más CPU para descodificar la imagen y más memoria para almacenar la imagen descodificada. Es un desperdicio descodificar una imagen de alta resolución en memoria cuando se reducirá a un tamaño menor para su presentación. Reduzca el uso de CPU y la superficie de memoria mediante la creación de varias versiones de resolución de imágenes almacenadas que se acerquen a los tamaños de presentación previstos. Por ejemplo, una imagen que aparece en una vista de lista probablemente deba tener menos resolución que una imagen que aparece en pantalla completa. Además, las versiones reducidas de imágenes de alta resolución pueden cargarse para mostrarse de forma eficaz con una repercusión mínima en la memoria. Para más información, vea [Load Large Bitmaps Efficiently (Cargar eficazmente mapas de bits grandes)](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

Independientemente de la resolución de la imagen, mostrar recursos de imagen puede aumentar considerablemente la superficie de memoria de la aplicación. Por tanto, solo se deberían crear cuando fuera necesario y deberían liberarse en cuanto la aplicación no los necesitara.

<a name="activationperiod" />

## <a name="reduce-the-application-activation-period"></a>Reducir el período de activación de la aplicación

Todas las aplicaciones tienen un *período de activación*, que es el tiempo entre su inicio y el momento en que están listas para su uso. Este período de activación proporciona a los usuarios la primera impresión de la aplicación, por lo que es importante reducirlo, así como la percepción que los usuarios tienen de él, para que tengan una primera impresión favorable de la aplicación.

Antes de que una aplicación muestre su interfaz de usuario inicial, debe proporcionar una pantalla de presentación para indicar al usuario que se está iniciando. Si la aplicación no puede mostrar rápidamente su interfaz de usuario inicial, se debe usar la pantalla de presentación para informar al usuario del progreso durante el período de activación, a fin de ofrecer la seguridad de que la aplicación no se ha bloqueado. Podría hacerse mediante una barra de progreso o un control similar.

Durante el período de activación, las aplicaciones ejecutan lógica de activación, que suele incluir la carga y el procesamiento de los recursos. El período de activación se puede reducir asegurándose de que los recursos necesarios estén empaquetados en la aplicación en lugar de recuperarse de forma remota. Por ejemplo, en algunas circunstancias puede ser adecuado durante el período de activación cargar datos de marcador de posición almacenados localmente. Luego, una vez que aparece la interfaz de usuario inicial y el usuario puede interactuar con la aplicación, se pueden reemplazar los datos de marcador de posición progresivamente desde un origen remoto. Además, la lógica de activación de la aplicación solo debe realizar el trabajo necesario para que el usuario pueda empezar a usar la aplicación. Esto puede ayudar si retrasa la carga de ensamblados adicionales, ya que los ensamblados se cargan la primera vez que se usan.

<a name="webservicecommunication" />

## <a name="reduce-web-service-communication"></a>Reducir la comunicación con servicios web

La conexión a un servicio web desde una aplicación puede tener impacto en su rendimiento. Por ejemplo, un aumento del uso del ancho de banda de red provocará un aumento del uso de la batería del dispositivo. Además, los usuarios pueden estar usando la aplicación en un entorno de ancho de banda limitado. Por lo tanto, es razonable limitar el uso de ancho de banda entre una aplicación y un servicio web.

Un enfoque para reducir el uso de ancho de banda de una aplicación es comprimir los datos antes de transferirlos a través de una red. Pero el uso adicional de CPU del proceso de compresión también puede dar lugar a un mayor consumo de batería. Por lo tanto, este equilibrio se debería evaluar cuidadosamente antes de decidir si se van a mover datos comprimidos a través de una red.

Otro problema que hay que tener en cuenta es el formato de los datos que se mueven entre una aplicación y un servicio web. Los dos formatos principales son lenguaje de marcado extensible (XML) y notación de objetos JavaScript (JSON). XML es un formato de intercambio de datos basado en texto que genera cargas de datos relativamente grandes, porque contiene un gran número de caracteres de formato. JSON es un formato de intercambio de datos basado en texto que genera cargas de datos compactas, lo que reduce los requisitos de ancho de banda al enviar y recibir datos. Por lo tanto, JSON suele ser el formato preferido para las aplicaciones móviles.

Se recomienda usar objetos de transferencia de datos (DTO) al transferir datos entre una aplicación y un servicio web. Un DTO contiene un conjunto de datos para transferir a través de la red. Al usar DTO, se pueden transmitir más datos en una única llamada remota, lo que puede ayudar a reducir el número de llamadas remotas realizadas por la aplicación. Por lo general, una llamada remota con una carga de datos mayor tarda lo mismo que una llamada que solo lleva una carga de datos pequeña.

Los datos recuperados del servicio web deben almacenarse en caché localmente para poder usar los datos en caché en lugar de recuperarlos repetidamente del servicio web. Al adoptar este enfoque también es necesario implementar una estrategia adecuada de almacenamiento en caché para actualizar los datos en la memoria caché local si cambian en el servicio web.

## <a name="summary"></a>Resumen

En este artículo se han descrito y explicado técnicas para aumentar el rendimiento de las aplicaciones compiladas con la plataforma Xamarin. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento de Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Rendimiento de Xamarin.Android](~/android/deploy-test/performance.md)
- [Introducción a Xamarin Profiler](~/tools/profiler/index.md)
- [Rendimiento de Xamarin.Forms](~/xamarin-forms/deploy-test/performance.md)
- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [IDisposable](xref:System.IDisposable)
