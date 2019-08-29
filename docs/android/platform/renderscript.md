---
title: Una introducción a Renderscript
description: En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación de Xamarin. Android que tiene como destino el nivel de API 17 o superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 14fb3740f558b006d507ff6875fd2cfc81a04298
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119728"
---
# <a name="an-introduction-to-renderscript"></a>Una introducción a Renderscript

_En esta guía se presenta Renderscript y se explica cómo usar las API intrínsecas de Renderscript en una aplicación de Xamarin. Android que tiene como destino el nivel de API 17 o superior._

## <a name="overview"></a>Información general

Renderscript es un marco de programación creado por Google con el fin de mejorar el rendimiento de las aplicaciones de Android que requieren recursos de cálculo extensivos. Se trata de una API de bajo nivel y alto rendimiento basada en [C99](https://en.wikipedia.org/wiki/C99). Dado que se trata de una API de bajo nivel que se ejecutará en CPU, GPU o DSP, Renderscript es adecuado para aplicaciones Android que pueden necesitar realizar cualquiera de las siguientes acciones:

- Gráficos
- Procesamiento de imágenes
- Cifrado
- Procesamiento de señal
- Rutinas matemáticas

Renderscript usará y compilará los scripts en el código de LLVM bytes que se incluye en el APK. `clang` Cuando la aplicación se ejecuta por primera vez, el código de bytes de LLVM se compilará en el código máquina para los procesadores del dispositivo. Esta arquitectura permite a una aplicación de Android aprovechar las ventajas del código máquina sin que los desarrolladores tengan que escribirla para cada procesador en el propio dispositivo.

Hay dos componentes en una rutina Renderscript:

1. **El tiempo de ejecución de Renderscript** &ndash; Se trata de las API nativas que son responsables de ejecutar Renderscript. Esto incluye cualquier Renderscripts escrito para la aplicación.

2. **Contenedores administrados desde el marco de trabajo de Android** &ndash; Clases administradas que permiten a una aplicación Android controlar e interactuar con el tiempo de ejecución y los scripts de Renderscript. Además de las clases proporcionadas para controlar el tiempo de ejecución de Renderscript, la cadena de herramientas de Android examinará el código fuente de Renderscript y generará clases contenedoras administradas para que las use la aplicación de Android.

En el diagrama siguiente se muestra cómo se relacionan estos componentes:

![Diagrama que ilustra cómo interactúa el marco de trabajo Android con el tiempo de ejecución de Renderscript](renderscript-images/renderscript-01.png)

Hay tres conceptos importantes para el uso de Renderscripts en una aplicación de Android:

1. **Un contexto** &ndash; Una API administrada proporcionada por el Android SDK que asigna recursos a Renderscript y permite a la aplicación Android pasar y recibir datos de Renderscript.

2. **Un _kernel de proceso_**  También conocido como _kernel raíz_ o kernel, que es una rutina que realiza el trabajo. &ndash; El kernel es muy similar a una función de C. se trata de una rutina pueden paralelizar que se ejecutará en todos los datos de la memoria asignada.

3. **Memoria asignada** Los datos se pasan a y desde un kernel a través de una _[asignación.](xref:Android.Renderscripts.Allocation)_ &ndash; Un kernel puede tener una asignación de entrada o de salida.

El espacio de nombres [Android. Renderscripts](xref:Android.Renderscripts) contiene las clases para interactuar con el tiempo de ejecución de Renderscript. En concreto, la [`Renderscript`](xref:Android.Renderscripts.RenderScript) clase administrará el ciclo de vida y los recursos del motor Renderscript. La aplicación Android debe inicializar una o más[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
los. Una asignación es una API administrada que es responsable de la asignación y el acceso a la memoria que se comparte entre la aplicación de Android y el tiempo de ejecución de Renderscript. Normalmente, se crea una asignación para la entrada y, opcionalmente, se crea otra asignación que contiene la salida del kernel. El motor en tiempo de ejecución de Renderscript y las clases contenedoras administradas asociadas administrarán el acceso a la memoria mantenida por las asignaciones, no es necesario que un desarrollador de aplicaciones Android realice ningún trabajo adicional.

Una asignación contendrá uno o varios [elementos Android. Renderscripts. Elements](xref:Android.Renderscripts.Element).
Los elementos son un tipo especializado que describen los datos de cada asignación.
Los tipos de elemento de la asignación de salida deben coincidir con los tipos del elemento de entrada. Al ejecutar, una Renderscript iterará en cada elemento de la asignación de entrada en paralelo y escribirá los resultados en la asignación de salida. Hay dos tipos de elementos:

- **tipo simple** Conceptualmente, `float` es igual que un tipo de datos de C o `char`. &ndash;

- **tipo complejo** Este tipo es similar a un C `struct`. &ndash;

El motor de Renderscript realizará una comprobación en tiempo de ejecución para asegurarse de que los elementos de cada asignación son compatibles con lo que necesita el kernel. Si el tipo de datos de los elementos de la asignación no coincide con el tipo de datos que el kernel espera, se producirá una excepción.

Todos los kernels de Renderscript se ajustarán mediante un tipo que sea un descendiente del[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
clase. La `Script` clase se usa para establecer los parámetros de un Renderscript, establecer el `Allocations`apropiado y ejecutar Renderscript. Hay dos `Script` subclases en el Android SDK:


- **`Android.Renderscripts.ScriptIntrinsic`** Algunas de las tareas más comunes de Renderscript se incluyen en el Android SDK y son accesibles mediante una subclase de la clase [ScriptIntrinsic.](xref:Android.Renderscripts.ScriptIntrinsic) &ndash; No es necesario que un desarrollador realice pasos adicionales para usar estos scripts en su aplicación, ya que ya se proporcionan.

- **`ScriptC_XXXXX`** También conocido como scripts de usuario, son scripts escritos por desarrolladores y empaquetados en el APK. &ndash; En tiempo de compilación, la cadena de herramientas de Android generará clases contenedoras administradas que permitirán usar los scripts en la aplicación de Android.
  El nombre de estas clases generadas es el nombre del archivo Renderscript, con `ScriptC_`el prefijo. La escritura e incorporación de scripts de usuario no es compatible oficialmente con Xamarin. Android y más allá del ámbito de esta guía.

De estos dos tipos, solo `StringIntrinsic` es compatible con Xamarin. Android. En esta guía se explica cómo usar scripts intrínsecos en una aplicación de Xamarin. Android.

## <a name="requirements"></a>Requisitos

Esta guía está destinada a las aplicaciones de Xamarin. Android que tienen como destino el nivel de API 17 o superior. El uso de _scripts de usuario_ no se trata en esta guía.

La [biblioteca de compatibilidad de Xamarin. Android V8](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) transporta las API de intrínsecas Renderscript para las aplicaciones destinadas a versiones anteriores de la Android SDK. Agregar este paquete a un proyecto de Xamarin. Android debe permitir que las aplicaciones destinadas a versiones anteriores de la Android SDK aprovechen los scripts intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Usar Renderscripts intrínseco en Xamarin. Android

Los scripts intrínsecos son una excelente manera de realizar tareas informáticas intensivas con una cantidad mínima de código adicional. Se han optimizado para ofrecer un rendimiento óptimo en una gran sección de dispositivos cruzados.
No es raro que un script intrínseco se ejecute 10 veces más rápido que el código administrado y 2-3 veces después de una implementación personalizada de C. Muchos de los escenarios de procesamiento típicos están incluidos en los scripts intrínsecos. Esta lista de los scripts intrínsecos describe los scripts actuales de Xamarin. Android:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; Convierte RGB en RGBA mediante una tabla de búsqueda 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) Provideshigh performance Renderscript API to [Blas.](http://www.netlib.org/blas/) &ndash; Los subprogramas de BLASs de álgebra lineal básicos son rutinas que proporcionan los bloques de creación estándar para realizar operaciones básicas de vector y matriz. 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; Combina dos asignaciones juntas.

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; Aplica un desenfoque gaussiano a una asignación.

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; Aplica una matriz de colores a una asignación (es decir, cambiar los colores, ajustar el matiz).

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; Aplica una matriz de colores 3x3 a una asignación.

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; Aplica una matriz de colores 5x5 a una asignación.

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; Un filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; Aplica una tabla de búsqueda por canal a un búfer.

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; Script para realizar el ajuste de tamaño de una asignación de 2D.

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; Convierte un búfer YUV en RGB.

Consulte la documentación de la API para obtener más información sobre cada uno de los scripts intrínsecos.

A continuación se describen los pasos básicos para usar Renderscript en una aplicación de Android.

**Crear un contexto de Renderscript** &ndash; El[`Renderscript`](xref:Android.Renderscripts.RenderScript)
la clase es un contenedor administrado en torno al contexto Renderscript y controlará la inicialización, la administración de recursos y la limpieza. El objeto Renderscript se crea mediante el `RenderScript.Create` Factory Method, que toma un contexto de Android (como una actividad) como parámetro. En la siguiente línea de código se muestra cómo inicializar el contexto Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Crear asignaciones** Dependiendo del script intrínseco, puede ser necesario crear uno o dos `Allocation`. &ndash; El[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
la clase tiene varios métodos de generador para facilitar la creación de instancias de una asignación para un intrínseco. Como ejemplo, el siguiente fragmento de código muestra cómo crear una asignación para los mapas de bits.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

A menudo, será necesario crear un `Allocation` para contener los datos de salida de un script. En el siguiente fragmento de código se muestra `Allocation.CreateTyped` cómo usar el ayudante para crear instancias `Allocation` de un segundo que tiene el mismo tipo que el original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Crear una instancia del contenedor de script** Cada una de las clases contenedoras de scripts intrínsecos debe tener métodos `Create`auxiliares (normalmente denominados) para crear instancias de un objeto contenedor para ese script. &ndash; El siguiente fragmento de código es un ejemplo de cómo crear una instancia `ScriptIntrinsicBlur` de un objeto Blur. El `Element.U8_4` método auxiliar creará un elemento que describe un tipo de datos de 4 campos de valores enteros sin signo de 8 bits, que es adecuado para contener los datos de un `Bitmap` objeto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Asignar asignaciones, establecer parámetros, & ejecutar script** La clase proporciona un`ForEach` método para ejecutar realmente Renderscript. `Script` &ndash; Este método recorre en iteración `Element` cada `Allocation` en la que contiene los datos de entrada. En algunos casos, puede ser necesario proporcionar un `Allocation` que contiene la salida.
`ForEach`sobrescribirá el contenido de la asignación de salida. Para continuar con los fragmentos de código de los pasos anteriores, este ejemplo muestra cómo asignar una asignación de entrada, establecer un parámetro y, por último, ejecutar el script (copiando los resultados en la asignación de salida):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Es posible que desee consultar la receta [desenfocar una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) , que es un ejemplo completo de cómo usar un script intrínseco en Xamarin. Android.

## <a name="summary"></a>Resumen

En esta guía se ha introducido Renderscript y cómo usarlo en una aplicación de Xamarin. Android. Describe brevemente qué es Renderscript y cómo funciona en una aplicación Android. Se describen algunos de los componentes clave de Renderscript y la diferencia entre los scripts de _usuario_ y los scripts de _intrínsecas_. Por último, en esta guía se describen los pasos para usar un script intrínseco en una aplicación de Xamarin. Android.



## <a name="related-links"></a>Vínculos relacionados

- [Espacio de nombres Android. Renderscripts](xref:Android.Renderscripts)
- [Desenfoque de una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introducción con Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
