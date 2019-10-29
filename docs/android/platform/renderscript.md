---
title: Una introducción a Renderscript
description: En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación de Xamarin. Android que tiene como destino el nivel de API 17 o superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019793"
---
# <a name="an-introduction-to-renderscript"></a>Una introducción a Renderscript

_En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación de Xamarin. Android que tiene como destino el nivel de API 17 o superior._

## <a name="overview"></a>Información general

Renderscript es un marco de programación creado por Google con el fin de mejorar el rendimiento de las aplicaciones de Android que requieren recursos de cálculo extensivos. Se trata de una API de bajo nivel y alto rendimiento basada en [C99](https://en.wikipedia.org/wiki/C99). Dado que se trata de una API de bajo nivel que se ejecutará en CPU, GPU o DSP, Renderscript es adecuado para aplicaciones Android que pueden necesitar realizar cualquiera de las siguientes acciones:

- Gráficos
- Procesamiento de imágenes
- Cifra
- Procesamiento de señal
- Rutinas matemáticas

Renderscript usará `clang` y compilará los scripts en el código LLVM byte que se incluye en APK. Cuando la aplicación se ejecuta por primera vez, el código de bytes de LLVM se compilará en el código máquina para los procesadores del dispositivo. Esta arquitectura permite a una aplicación de Android aprovechar las ventajas del código máquina sin que los desarrolladores tengan que escribirla para cada procesador en el propio dispositivo.

Hay dos componentes en una rutina Renderscript:

1. **El tiempo de ejecución de Renderscript** &ndash; se trata de las API nativas que son responsables de ejecutar Renderscript. Esto incluye cualquier Renderscripts escrito para la aplicación.

2. Los **contenedores administrados desde el marco de trabajo de android** &ndash; clases administradas que permiten a una aplicación Android controlar e interactuar con los scripts y el tiempo de ejecución de Renderscript. Además de las clases proporcionadas para controlar el tiempo de ejecución de Renderscript, la cadena de herramientas de Android examinará el código fuente de Renderscript y generará clases contenedoras administradas para que las use la aplicación de Android.

En el diagrama siguiente se muestra cómo se relacionan estos componentes:

![Diagrama que ilustra cómo interactúa el marco de trabajo Android con el tiempo de ejecución de Renderscript](renderscript-images/renderscript-01.png)

Hay tres conceptos importantes para el uso de Renderscripts en una aplicación de Android:

1. **Un contexto** &ndash; una API administrada proporcionada por el Android SDK que asigna recursos a Renderscript y permite a la aplicación Android pasar y recibir datos de Renderscript.

2. **Un _kernel de proceso_**  &ndash; también conocido como kernel _raíz_ o _kernel_, que es una rutina que realiza el trabajo. El kernel es muy similar a una función de C. se trata de una rutina pueden paralelizar que se ejecutará en todos los datos de la memoria asignada.

3. **Memoria asignada** &ndash; datos se pasan a y desde un kernel a través de una _[asignación](xref:Android.Renderscripts.Allocation)_ . Un kernel puede tener una asignación de entrada o de salida.

El espacio de nombres [Android. Renderscripts](xref:Android.Renderscripts) contiene las clases para interactuar con el tiempo de ejecución de Renderscript. En concreto, la clase [`Renderscript`](xref:Android.Renderscripts.RenderScript) administrará el ciclo de vida y los recursos del motor Renderscript. La aplicación Android debe inicializar una o más [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
los. Una asignación es una API administrada que es responsable de la asignación y el acceso a la memoria que se comparte entre la aplicación de Android y el tiempo de ejecución de Renderscript. Normalmente, se crea una asignación para la entrada y, opcionalmente, se crea otra asignación que contiene la salida del kernel. El motor en tiempo de ejecución de Renderscript y las clases contenedoras administradas asociadas administrarán el acceso a la memoria mantenida por las asignaciones, no es necesario que un desarrollador de aplicaciones Android realice ningún trabajo adicional.

Una asignación contendrá uno o varios [elementos Android. Renderscripts. Elements](xref:Android.Renderscripts.Element).
Los elementos son un tipo especializado que describen los datos de cada asignación.
Los tipos de elemento de la asignación de salida deben coincidir con los tipos del elemento de entrada. Al ejecutar, una Renderscript iterará en cada elemento de la asignación de entrada en paralelo y escribirá los resultados en la asignación de salida. Hay dos tipos de elementos:

- **tipo simple** &ndash; conceptualmente es igual que un tipo de datos de C, `float` o `char`.

- **tipo complejo** &ndash; este tipo es similar a un `struct`de C.

El motor de Renderscript realizará una comprobación en tiempo de ejecución para asegurarse de que los elementos de cada asignación son compatibles con lo que necesita el kernel. Si el tipo de datos de los elementos de la asignación no coincide con el tipo de datos que el kernel espera, se producirá una excepción.

Todos los kernels de Renderscript se ajustarán mediante un tipo que sea un descendiente del [`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
clase. La clase `Script` se usa para establecer los parámetros de un Renderscript, establecer la `Allocations`adecuada y ejecutar Renderscript. Hay dos subclases de `Script` en el Android SDK:

- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; se incluyen algunas de las tareas más comunes de Renderscript en el Android SDK y son accesibles mediante una subclase de la clase [ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic) . No es necesario que un desarrollador realice pasos adicionales para usar estos scripts en su aplicación, ya que ya se proporcionan.

- **`ScriptC_XXXXX`** &ndash; también conocido como _scripts de usuario_, son scripts escritos por desarrolladores y empaquetados en apk. En tiempo de compilación, la cadena de herramientas de Android generará clases contenedoras administradas que permitirán usar los scripts en la aplicación de Android.
  El nombre de estas clases generadas es el nombre del archivo Renderscript, con el prefijo `ScriptC_`. La escritura e incorporación de scripts de usuario no es compatible oficialmente con Xamarin. Android y más allá del ámbito de esta guía.

De estos dos tipos, Xamarin. Android solo admite el `StringIntrinsic`. En esta guía se explica cómo usar scripts intrínsecos en una aplicación de Xamarin. Android.

## <a name="requirements"></a>Requisitos

Esta guía está destinada a las aplicaciones de Xamarin. Android que tienen como destino el nivel de API 17 o superior. El uso de _scripts de usuario_ no se trata en esta guía.

La [biblioteca de compatibilidad de Xamarin. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) transporta las API de intrínsecas Renderscript para las aplicaciones destinadas a versiones anteriores de la Android SDK. Agregar este paquete a un proyecto de Xamarin. Android debe permitir que las aplicaciones destinadas a versiones anteriores de la Android SDK aprovechen los scripts intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usar Renderscripts intrínseco en Xamarin. Android

Los scripts intrínsecos son una excelente manera de realizar tareas informáticas intensivas con una cantidad mínima de código adicional. Se han optimizado para ofrecer un rendimiento óptimo en una gran sección de dispositivos cruzados.
No es raro que un script intrínseco se ejecute 10 veces más rápido que el código administrado y 2-3 veces después de una implementación personalizada de C. Muchos de los escenarios de procesamiento típicos están incluidos en los scripts intrínsecos. Esta lista de los scripts intrínsecos describe los scripts actuales de Xamarin. Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; convierte RGB en RGBA mediante una tabla de búsqueda 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh performance Renderscript API to [Blas](http://www.netlib.org/blas/). Los subprogramas de BLASs de álgebra lineal básicos son rutinas que proporcionan los bloques de creación estándar para realizar operaciones básicas de vector y matriz. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina dos asignaciones.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; aplica un desenfoque gaussiano a una asignación.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; aplica una matriz de colores a una asignación (es decir, cambiar los colores, ajustar el matiz).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; aplica una matriz de colores 3x3 a una asignación.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; aplica una matriz de colores 5x5 a una asignación.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; un filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; aplica una tabla de búsqueda por canal a un búfer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; script para realizar el ajuste de tamaño de una asignación de 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; convierte un búfer YUV en RGB.

Consulte la documentación de la API para obtener más información sobre cada uno de los scripts intrínsecos.

A continuación se describen los pasos básicos para usar Renderscript en una aplicación de Android.

**Cree un contexto de Renderscript** &ndash; el [`Renderscript`](xref:Android.Renderscripts.RenderScript)
la clase es un contenedor administrado en torno al contexto Renderscript y controlará la inicialización, la administración de recursos y la limpieza. El objeto Renderscript se crea mediante el Factory Method `RenderScript.Create`, que toma un contexto de Android (como una actividad) como parámetro. En la siguiente línea de código se muestra cómo inicializar el contexto Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Crear asignaciones** &ndash; dependiendo del script intrínseco, puede que sea necesario crear una o dos `Allocation`s. El [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
la clase tiene varios métodos de generador para facilitar la creación de instancias de una asignación para un intrínseco. Como ejemplo, el siguiente fragmento de código muestra cómo crear una asignación para los mapas de bits.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

A menudo, será necesario crear un `Allocation` para contener los datos de salida de un script. En el siguiente fragmento de código se muestra cómo usar la aplicación auxiliar de `Allocation.CreateTyped` para crear instancias de un segundo `Allocation` que sea del mismo tipo que el original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Cree una instancia del contenedor de scripts** &ndash; cada una de las clases contenedoras de scripts intrínsecos debe tener métodos auxiliares (normalmente denominados `Create`) para crear instancias de un objeto contenedor para ese script. El siguiente fragmento de código es un ejemplo de cómo crear una instancia de un objeto `ScriptIntrinsicBlur` Blur. El método auxiliar `Element.U8_4` creará un elemento que describe un tipo de datos que es 4 campos de valores enteros de 8 bits sin signo, adecuado para contener los datos de un objeto `Bitmap`:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Asignar asignaciones, establecer parámetros, & ejecutar el Script** &ndash; la clase `Script` proporciona un método `ForEach` para ejecutar realmente Renderscript. Este método iterará en cada `Element` del `Allocation` que contiene los datos de entrada. En algunos casos, puede ser necesario proporcionar una `Allocation` que contiene la salida.
`ForEach` sobrescribirá el contenido de la asignación de salida. Para continuar con los fragmentos de código de los pasos anteriores, este ejemplo muestra cómo asignar una asignación de entrada, establecer un parámetro y, por último, ejecutar el script (copiando los resultados en la asignación de salida):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Es posible que desee consultar la receta [desenfocar una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) , que es un ejemplo completo de cómo usar un script intrínseco en Xamarin. Android.

## <a name="summary"></a>Resumen

En esta guía se ha introducido Renderscript y cómo usarlo en una aplicación de Xamarin. Android. Describe brevemente qué es Renderscript y cómo funciona en una aplicación Android. Se describen algunos de los componentes clave de Renderscript y la diferencia entre los _scripts de usuario_ y los _scripts de intrínsecas_. Por último, en esta guía se describen los pasos para usar un script intrínseco en una aplicación de Xamarin. Android.

## <a name="related-links"></a>Vínculos relacionados

- [Espacio de nombres Android. Renderscripts](xref:Android.Renderscripts)
- [Desenfoque de una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introducción con Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
