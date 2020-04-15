---
title: Introducción a Renderscript
description: En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación Xamarin.Android que tiene como destino el nivel de API 17 o posterior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019793"
---
# <a name="an-introduction-to-renderscript"></a>Introducción a Renderscript

_En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación Xamarin.Android que tiene como destino el nivel de API 17 o posterior._

## <a name="overview"></a>Información general

Renderscript es un marco de programación creado por Google con el fin de mejorar el rendimiento de las aplicaciones Android que requieren recursos de cálculo extensivos. Se trata de una API de bajo nivel y alto rendimiento basada en [C99](https://en.wikipedia.org/wiki/C99). Dado que se trata de una API de bajo nivel que se ejecutará en CPU, GPU o DSP, Renderscript es adecuado para aplicaciones Android que pueden necesitar realizar cualquiera de las siguientes acciones:

- Gráficos
- Procesamiento de imágenes
- Cifrado
- Procesamiento de señales
- Rutinas matemáticas

Renderscript usará `clang` y compilará los scripts en el código de bytes de LLVM que se agrupa en APK. Cuando la aplicación se ejecuta por primera vez, el código de bytes de LLVM se compilará en el código máquina para los procesadores del dispositivo. Esta arquitectura permite a una aplicación Android aprovechar las ventajas del código máquina sin que los desarrolladores tengan que escribirlo para cada procesador en el propio dispositivo.

Hay dos componentes en una rutina de Renderscript:

1. **Renderscript en tiempo de ejecución**: se trata de las API nativas responsables de ejecutar Renderscript. Esto incluye cualquier script Renderscript escrito para la aplicación.

2. **Contenedores administrados desde el marco de trabajo de Android**: clases administradas que permiten a una aplicación Android controlar e interactuar con los scripts y el tiempo de ejecución de Renderscript. Además de las clases proporcionadas en el marco para controlar el tiempo de ejecución de Renderscript, la cadena de herramientas de Android examinará el código fuente de Renderscript y generará clases contenedoras administradas para que la aplicación Android las use.

El siguiente diagrama ilustra cómo se relacionan estos componentes:

![El diagrama ilustra cómo interactúa el marco de trabajo de Android con el tiempo de ejecución de Renderscript](renderscript-images/renderscript-01.png)

Hay tres conceptos importantes para el uso de Renderscripts en una aplicación Android:

1. **Un contexto**: una API administrada proporcionada por Android SDK que asigna recursos a Renderscript y permite a la aplicación Android pasar y recibir datos desde Renderscript.

2. **Un _kernel de proceso_** : también conocido como _kernel raíz_ o _kernel_, que es una rutina que realiza el trabajo. El kernel es muy similar a una función de C; se trata de una rutina paralelizable que se ejecutará en todos los datos de la memoria asignada.

3. **Memoria asignada**: los datos se pasan a y desde un kernel a través de una _[asignación](xref:Android.Renderscripts.Allocation)_ . Un kernel puede tener una asignación de entrada o de salida.

El espacio de nombres [Android.Renderscripts](xref:Android.Renderscripts) contiene las clases para interactuar con el tiempo de ejecución de Renderscript. En concreto, la clase [`Renderscript`](xref:Android.Renderscripts.RenderScript) administrará el ciclo de vida y los recursos del motor de Renderscript. La aplicación Android debe inicializar uno o más objetos [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
. Una asignación es una API administrada que es responsable de la asignación y el acceso a la memoria que se comparte entre la aplicación Android y el tiempo de ejecución de Renderscript. Normalmente, se crea una asignación para la entrada y, opcionalmente, se crea otra asignación que contiene la salida del kernel. El motor en tiempo de ejecución de Renderscript y las clases contenedoras administradas asociadas administrarán el acceso a la memoria mantenida por las asignaciones; no es necesario que un desarrollador de aplicaciones Android realice ningún trabajo adicional.

Una asignación contendrá uno o varios elementos [Android.Renderscripts.Elements](xref:Android.Renderscripts.Element).
Los elementos son un tipo especializado que describen los datos de cada asignación.
Los tipos del elemento de la asignación de salida deben coincidir con los tipos del elemento de entrada. Al realizar la ejecución, un elemento Renderscript iterará en cada elemento de la asignación de entrada en paralelo y escribirá los resultados en la asignación de salida. Hay dos tipos de elementos:

- **Tipo simple**: conceptualmente, es igual que un tipo de datos de C, `float` o `char`.

- **Tipo completo**: este tipo es similar a un elemento `struct` de C.

El motor de Renderscript realizará una comprobación en tiempo de ejecución para asegurarse de que los elementos de cada asignación son compatibles con lo que necesita el kernel. Si el tipo de datos de los elementos de la asignación no coincide con el tipo de datos que el kernel espera, se producirá una excepción.

Todos los kernels de Renderscript se encapsularán mediante un tipo que sea un descendiente de [`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script).
clase. La clase `Script` se usa para establecer los parámetros de un elemento Renderscript, establecer el elemento `Allocations`adecuado y ejecutar Renderscript. Hay dos subclases `Script` en Android SDK:

- **`Android.Renderscripts.ScriptIntrinsic`** : algunas de las tareas más comunes de Renderscript se encapsulan en Android SDK, y una subclase de la clase [ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic) puede acceder a ellas. No es necesario que un desarrollador realice pasos adicionales para usar estos scripts en su aplicación, puesto que ya se proporcionan.

- **`ScriptC_XXXXX`** : también se conoce como _scripts de usuario_; son scripts escritos por desarrolladores y empaquetados en APK. En tiempo de compilación, la cadena de herramientas de Android generará clases contenedoras administradas que permitirán usar los scripts en la aplicación Android.
  El nombre de estas clases generadas es el nombre del archivo de Renderscript, con el prefijo `ScriptC_`. La escritura e incorporación de scripts de usuario no es compatible oficialmente con Xamarin.Android y va más allá del ámbito de esta guía.

De estos dos tipos, Xamarin.Android solo admite `StringIntrinsic`. En esta guía se explica cómo usar scripts intrínsecos en una aplicación Xamarin.Android.

## <a name="requirements"></a>Requisitos

Esta guía está destinada a las aplicaciones Xamarin.Android que tienen como destino el nivel de API 17 o posterior. El uso de _scripts de usuario_ no se trata en esta guía.

La [biblioteca de compatibilidad de Xamarin.Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) transporta las API de Renderscript intrínsecas para las aplicaciones destinadas a versiones anteriores de Android SDK. Agregar este paquete a un proyecto de Xamarin.Android debe permitir que las aplicaciones destinadas a versiones anteriores de Android SDK aprovechen los scripts intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usar Renderscripts intrínsecos en Xamarin.Android

Los scripts intrínsecos son una excelente manera de realizar tareas informáticas intensivas con una cantidad mínima de código adicional. Se han optimizado para ofrecer un rendimiento óptimo en una gran sección de dispositivos cruzados.
No es raro que un script intrínseco se ejecute 10 veces más rápido que el código administrado y 2 o 3 veces más tarde que una implementación personalizada de C. Muchos de los escenarios de procesamiento típicos están incluidos en los scripts intrínsecos. Esta lista de los scripts intrínsecos describe los scripts actuales de Xamarin.Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT): convierte RGB en RGBA mediante una tabla de búsqueda 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html): ofrece API de Renderscript de alto rendimiento a [BLAS](http://www.netlib.org/blas/). Los subprogramas básicos de álgebra lineal (BLAS) son rutinas que proporcionan los bloques de creación estándar para realizar operaciones básicas de vector y matriz. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend): combina dos asignaciones.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur): aplica un desenfoque gaussiano a una asignación.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix): aplica una matriz de colores a una asignación (es decir, cambiar los colores, ajustar el matiz, etc.).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3): aplica una matriz de colores 3x3 a una asignación.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5): aplica una matriz de colores 5x5 a una asignación.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram): un filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT): aplica una tabla de búsqueda por canal a un búfer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize): script para realizar el ajuste de tamaño de una asignación 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB): convierte un búfer YUV en RGB.

Consulte la documentación de la API para obtener más información sobre cada uno de los scripts intrínsecos.

A continuación, se describen los pasos básicos para usar Renderscript en una aplicación Android.

**Creación de un contexto de Renderscript**: la clase [`Renderscript`](xref:Android.Renderscripts.RenderScript)
es un contenedor administrado en torno al contexto de Renderscript y controlará la inicialización, la administración de recursos y la limpieza. El objeto de Renderscript se crea mediante el método Factory Method `RenderScript.Create`, que adopta un contexto de Android (como una actividad) como parámetro. En la siguiente línea de código se muestra cómo inicializar el contexto de Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Creación de asignaciones**: según el script intrínseco, puede que sea necesario crear uno o dos elementos `Allocation`. La clase [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
tiene varios métodos Factory Method para facilitar la creación de instancias de una asignación para un elemento intrínseco. Como ejemplo, el fragmento de código siguiente muestra cómo crear una asignación para los mapas de bits.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

A menudo, será necesario crear un elemento `Allocation` para contener los datos de salida de un script. En el siguiente fragmento de código se muestra cómo usar la aplicación auxiliar de `Allocation.CreateTyped` para crear instancias de un segundo elemento `Allocation` que sea del mismo tipo que el original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Creación de una instancia del contenedor de scripts**: cada una de las clases contenedoras de scripts intrínsecos debe tener métodos auxiliares (normalmente denominados `Create`) para crear instancias de un objeto contenedor para ese script. El siguiente fragmento de código es un ejemplo de cómo crear una instancia de un objeto de desenfoque `ScriptIntrinsicBlur`. El método auxiliar `Element.U8_4` creará un elemento que describe un tipo de datos que es 4 campos de valores enteros de 8 bits sin signo, adecuado para contener los datos de un objeto `Bitmap`:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Realizar asignaciones, establecer parámetros y ejecutar scripts**: la clase `Script` ofrece un método `ForEach` para ejecutar realmente Renderscript. Este método iterará en cada `Element` de `Allocation` que contiene los datos de entrada. En algunos casos, puede ser necesario proporcionar un elemento `Allocation` que contiene la salida.
`ForEach` sobrescribirá el contenido de la asignación de salida. Para continuar con los fragmentos de código de los pasos anteriores, este ejemplo muestra cómo realizar una asignación de entrada, establecer un parámetro y, por último, ejecutar el script (copiando los resultados en la asignación de salida):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Puede consultar el artículo [Desenfoque de una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript), ya que se trata de un ejemplo completo de cómo usar un script intrínseco de Xamarin.Android.

## <a name="summary"></a>Resumen

En esta guía se ha presentado Renderscript y cómo usarlo en una aplicación Xamarin.Android. Describe brevemente qué es Renderscript y cómo funciona en una aplicación Android. Describe algunos de los componentes clave de Renderscript y la diferencia entre _scripts de usuario_ y _scripts intrínsecos_. Por último, en esta guía se describen los pasos para usar un script intrínseco en una aplicación Xamarin.Android.

## <a name="related-links"></a>Vínculos relacionados

- [Espacio de nombres Android.Renderscripts](xref:Android.Renderscripts)
- [Desenfoque de una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introducción a Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
