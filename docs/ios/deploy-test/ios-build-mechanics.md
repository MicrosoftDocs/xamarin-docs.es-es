---
title: Mecanismos de compilación de iOS
description: En esta guía se examina cómo medir el tiempo de las aplicaciones y cómo usar métodos que se pueden emplear para compilaciones rápidas en todas las configuraciones de compilación.
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 52a6c3c996e2d5df204b6d0df40368bc835e990f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936778"
---
# <a name="ios-build-mechanics"></a>Mecanismos de compilación de iOS

_En esta guía se examina cómo medir el tiempo de las aplicaciones y cómo usar métodos que se pueden emplear para compilaciones rápidas en todas las configuraciones de compilación._

El desarrollo de grandes aplicaciones conlleva mucho más que simplemente escribir código que funcione. Una aplicación bien escrita debe contener optimizaciones que realicen compilaciones más rápido con aplicaciones que son más pequeñas y que se ejecutan más rápido. Estas optimizaciones no solo mejorarán la experiencia del usuario, sino también la de usted o la de cualquier desarrollador que trabaje en el proyecto. Es fundamental asegurarse de que al trabajar con la aplicación todo se organiza con frecuencia. 

Recuerde que las opciones predeterminadas son seguras y rápidas, pero que no son óptimas para cualquier situación. Además, muchas opciones pueden ralentizar o acelerar el ciclo de desarrollo en función de cada proyecto. Por ejemplo, la eliminación nativa lleva tiempo, pero si se gana muy poco, entonces el tiempo empleado para la eliminación no se recuperará con una implementación más rápida. Por otra parte, la eliminación nativa puede reducir la aplicación significativamente, en cuyo caso se implementará más rápido. Esto varía entre los proyectos, y la única forma de saberlo es probar.

Las velocidades de compilación de Xamarin también pueden verse afectadas por distintas funcionalidades y capacidades de un equipo que pueden mermar el rendimiento: funcionalidades del procesador, velocidades del bus, cantidad de memoria física, velocidad del disco y velocidad de la red. Estas limitaciones de rendimiento van más allá del ámbito de este documento y son responsabilidad del desarrollador.

## <a name="timing-apps"></a>Aplicaciones de control de tiempo

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para habilitar el resultado de diagnóstico de MSBuild en Visual Studio para Mac:

1. Haga clic en **Visual Studio para Mac > Preferencias...**
2. En la vista de árbol de la izquierda, seleccione **Proyectos > Compilar**
3. En el panel derecho, establezca el menú desplegable del contenido de registro en **Diagnóstico**:  [![Establecimiento del nivel de detalle del registro](ios-build-mechanics-images/image2.png)](ios-build-mechanics-images/image2.png#lightbox)
4. Haga clic en **Aceptar**.
5. Reinicie Visual Studio para Mac.
6. Limpie y recompile el paquete.
7. Vea los resultados de diagnóstico en el panel de errores (Ver > Paneles > Errores); para ello, haga clic en el botón Salida de la compilación.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para habilitar el resultado de diagnóstico de MSBuild en Visual Studio:

1. Haga clic en **Herramientas > Opciones...**
2. En la vista de árbol de la izquierda, seleccione **Proyectos y soluciones > Compilación y ejecución**.
3. En el panel de la derecha, establezca *Detalles de la salida de la compilación de MSBuild* en **Diagnóstico**:  [![Establecimiento de los detalles de la salida de la compilación de MSBuild](ios-build-mechanics-images/image2-vs.png)](ios-build-mechanics-images/image2-vs.png#lightbox)
4. Haga clic en **Aceptar**.
5. Limpie y recompile el paquete.
6. El resultado de diagnóstico se muestra en el panel de resultados.

-----

## <a name="timing-mtouch"></a>Control de tiempo de mtouch

Para mostrar información específica del proceso de compilación de mtouch, pase `--time --time` a los argumentos mtouch en **Opciones del proyecto**. Los resultados se encuentran en la salida de compilación, mediante la búsqueda de la tarea `MTouch`:

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>Conectarse desde Visual Studio con el host de compilación

Las herramientas de Xamarin técnicamente funcionan en cualquier equipo Mac que pueda ejecutar OS X 10.10 Yosemite o posterior. Sin embargo, el rendimiento del equipo Mac puede afectar a las experiencias de desarrollador y a los tiempos de compilación.

En el estado desconectado, Visual Studio en Windows solo realiza la fase de compilación de C# y no intenta realizar la vinculación o compilación AOT, empaquetar la aplicación en un lote  _.app_ o firmar el lote de aplicaciones. (La fase de compilación de C# no suele crear cuellos de botella de rendimiento). Intente identificar en qué parte de la canalización la compilación se está ralentizando; para ello, compile directamente en el host de compilación de Mac en Visual Studio para Mac.

Además, uno de los puntos más comunes en los que se produce lentitud es la conexión de red entre el equipo Windows y el host de compilación de Mac. Esto puede deberse a un obstáculo físico en la red, cuando se usa una conexión inalámbrica o si hay que recorrer un equipo saturado (como un servicio en la nube en Mac).

## <a name="simulator-tricks"></a>Trucos del simulador

Al desarrollar aplicaciones para dispositivos móviles, es esencial implementar rápidamente el código. Por distintos motivos, incluidos los requisitos de velocidad y la falta de aprovisionamiento de dispositivos, los desarrolladores suelen implementar en un emulador o simulador instalado previamente. Para los fabricantes de herramientas de desarrollo, la decisión de proporcionar un simulador o emulador se basa en el equilibrio entre la velocidad y la compatibilidad. 

Apple proporciona un simulador para el desarrollo en iOS, que impulsa la velocidad en relación con la compatibilidad con la creación de un entorno menos restrictivo para la ejecución de código. Este entorno menos restrictivo permite a Xamarin usar el compilador Just-In-Time (JIT) para el simulador (en contraposición a [AOT](~/ios/internals/architecture.md) en un dispositivo), lo que significa que la compilación se realiza con código nativo en tiempo de ejecución. Como Mac es mucho más rápido que un dispositivo, permite mejorar el rendimiento.

El simulador usa un iniciador de aplicaciones compartido, lo que permite la reutilización del iniciador, en lugar de tener que compilarlo en cada ocasión, según se requiera en el dispositivo.

Teniendo en cuenta la información anterior, en la lista siguiente se proporciona alguna información sobre los pasos que hay que seguir para compilar e implementar la aplicación en el simulador a fin mejorar el rendimiento.

### <a name="tips"></a>Sugerencias

- Para compilaciones: 
  - Anule la selección de la opción **Optimizar imágenes PNG** en Opciones del proyecto. Esta optimización no es necesaria para las compilaciones en el simulador.
  - Establezca el enlazador en **No vincular**. Deshabilitar el enlazador es más rápido, porque ejecutarlo supone una gran cantidad de tiempo.
  - Deshabilitar el iniciador de aplicaciones compartido con la marca `--nofastsim` conlleva que las compilaciones del simulador sean mucho más lentas. Quite esta marca cuando ya no sea necesaria.
  - El uso de las bibliotecas nativas es más lento, porque el ejecutable principal de simlauncher compartido no puede reutilizarse en tales casos, y es necesario compilar un ejecutable específico por aplicación en cada compilación.
- Para implementación
  - Si es posible, mantenga siempre en ejecución el simulador. El simulador puede tardar hasta doce segundos en arrancar el simulador en frío.
- Consejos adicionales
  - Es mejor usar la compilación que la recompilación, ya que esta última realiza una limpieza antes de la compilación. La limpieza puede tardar mucho tiempo, ya que elimina referencias que podrían usarse.
  - Benefíciese del hecho de que el simulador no fuerza el espacio aislado. Tener recursos grandes, como vídeos u otros recursos, incluidos en el proyecto puede generar operaciones de copia de archivos costosas cada vez que la aplicación se inicia en el simulador. Evite estas operaciones costosas colocando estos archivos en el directorio principal y haga referencia a ellos en la aplicación mediante la ruta de acceso del archivo completa.  
  - En caso de duda, use la marca `--time --time` para medir el cambio.

En la captura de pantalla siguiente se ilustra cómo establecer estas opciones para el simulador en las opciones de iOS:

[![Configuración de las opciones](ios-build-mechanics-images/image3.png)](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>Trucos de dispositivos

La implementación en el dispositivo es similar a la implementación en el simulador, ya que el simulador es un subconjunto pequeño de la compilación utilizada para el dispositivo iOS. La compilación en el dispositivo requiere muchos más pasos, pero tiene la ventaja de ofrecer oportunidades adicionales para optimizar la aplicación.

### <a name="build-configurations"></a>Configuraciones de compilación

Se proporciona una serie de configuraciones de compilación para implementar aplicaciones iOS. Es importante tener un buen conocimiento de cada configuración, para saber cuándo y por qué debe optimizarse.

- Depuración
  - Esta es la configuración principal que debe usarse mientras una aplicación está en fase de desarrollo y, por tanto, debe realizarse lo más rápido posible.
- Release
  - Las compilaciones de versiones son las que se proporcionan a los usuarios, y es fundamental centrar la atención en el rendimiento. Cuando se usa la configuración de versiones, puede usar el compilador de optimización LLVM y optimizar los archivos PNG.

También es importante entender la relación entre la compilación y la implementación. El tiempo de implementación es una función del tamaño de la aplicación. Una aplicación más grande tarda más tiempo en implementarse. Al reducir el tamaño de la aplicación, puede reducir el tiempo de implementación.

Minimizar el tamaño de la aplicación también puede reducir el tiempo de compilación. Esto se debe a que quitar código de la aplicación lleva menos tiempo que compilar de forma nativa el código que no se usará. Los archivos objeto más pequeños tardan menos en vincularse, por lo que se crea un ejecutable más pequeño con menos símbolos que generar. Ahorrar espacio, por tanto, tiene una doble ventaja, que es por lo que **Vincular SDK** es la opción predeterminada para todas las compilaciones de dispositivos. 

> [!NOTE]
> La opción **Vincular SDK** puede aparecer como Vincular solo SDK de marco o como Vincular solo ensamblados de SDK, en función del IDE utilizado.

### <a name="tips"></a>Sugerencias

- Compilar: 
  - La compilación de una única arquitectura (por ejemplo, ARM64) es más rápida que un archivo binario FAT (por ejemplo, ARMv7 + ARM64).
  - Evite optimizar los archivos PNG al depurar.
  - Considere la posibilidad de vincular todos los ensamblados. Optimice cada ensamblado. 
  - Deshabilite la creación de símbolos de depuración con  `--dsym=false`. Sin embargo, debe tener en cuenta que, al deshabilitar esto, los informes de bloqueo solo pueden resolverse en el equipo que compiló la aplicación, y solo si la aplicación no se quitó.

Algunos aspectos que deben evitarse son:

- Archivos binarios FAT (depurar) 
- Deshabilitar el enlazador `--nolink` 
- Deshabilitar eliminación 
  - Símbolos `--nosymbolstrip` 
  - IL (versión) `--nostrip`.  

Consejos adicionales 

- Como en el simulador, es preferible la compilación a la recompilación. 
  - Los ensamblados de AOT (archivos objeto) se almacenan en la memoria caché. 
- Las compilaciones de depuración tardan más por los símbolos, la ejecución de dsymutil y, debido a que al final son más grandes, se necesita tiempo adicional para cargarlas en los dispositivos. 
- De forma predeterminada, las compilaciones de versiones realizarán una eliminación de IL de los ensamblados. Esto lleva poco tiempo y es posible que se recupere al implementar una aplicación más pequeña en el dispositivo.
- Evite implementar archivos estáticos grandes en cada compilación (depurar) 
  - Utilice UIFileSharingEnabled (info.plist). 
    - Los recursos se pueden cargar una vez. 
- En caso de duda, use la marca `--time --time` para medir el cambio.

En la captura de pantalla siguiente se ilustra cómo establecer estas opciones para el simulador en las opciones de iOS:

[![Configuración de las opciones](ios-build-mechanics-images/image4.png)](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>Uso del enlazador

Al compilar la aplicación, mtouch usa un enlazador para el código administrado, que elimina el código que la aplicación no usa. En teoría, esto ofrece compilaciones más pequeñas, pero más rápidas. Para más información sobre el enlazador, vea la guía [Vincular en iOS](~/ios/deploy-test/linker.md).

Considere esta opciones al usar el enlazador:

- Al seleccionar **No vincular** para un dispositivo, la compilación tarda más tiempo y también genera una aplicación más grande. 
  - Apple rechazará las aplicaciones si su tamaño supera el límite establecido. En función de `MinimumOSVersion`, el tamaño puede ser de solo 60 MB. 
  - Se incluye el ejecutable nativo. 
  - Usar No vincular es más rápido para las compilaciones del simulador, porque se usa la compilación JIT (en oposición a AOT en un dispositivo).
- Vincular SDK es la opción predeterminada.
- Puede que no sea seguro vincular todo, sobre todo si usa código que no es propio, como paquetes NuGet o componentes. Si decide no vincular los ensamblados, todo el código de estos servicios se incluye en la aplicación, con la posibilidad de crear aplicaciones más grandes. 
  - No obstante, si elige **Vincular todo**, la aplicación puede bloquearse, sobre todo si se usan componentes externos. Esto se debe a que algunos componentes usan la reflexión en determinados tipos.
  - El análisis estático y la reflexión no funcionan de forma conjunta. 

Se puede indicar a las herramientas que mantengan los elementos dentro de la aplicación con el atributo [`[Preserve]`](~/ios/deploy-test/linker.md). 

Si no tiene acceso al código fuente o si lo genera una herramienta y no quiere cambiarlo, se puede vincular con la creación de un archivo XML en el que se describen todos los tipos y los miembros que deben conservarse. Puede agregar la marca `--xml={file.name}.xml` a las opciones del proyecto, que procesa código exactamente como si usara atributos.

### <a name="partially-linking-applications"></a>Vinculación parcial de aplicaciones 

También puede vincular aplicaciones de forma parcial, para ayudar a optimizar el tiempo de compilación de la aplicación:

- Use `Link All` y omita algunos ensamblados. 
  - Se pierde alguna optimización del tamaño de la aplicación.
  - No se requiere acceso al código fuente.
  - Por ejemplo, `--linkall --linkskip=fieldserviceiOS`.

- Use la opción `Link SDK` y el atributo `[LinkerSafe]` en los ensamblados necesarios. 
  - Se requiere acceso al código fuente.
  - Indica al sistema que es seguro vincular el ensamblado y se procesa como si se tratara de un SDK de Xamarin.

### <a name="objective-c-bindings"></a>Enlaces de Objective-C 

- Usar el atributo `[Assembly: LinkerSafe]` en los enlaces puede ahorrar tiempo y tamaño.

- SmartLink 
  - Se realiza en el lado nativo. 
  - Se usa el atributo `[LinkWith (SmartLink=true)]`.
  - Esto ayuda al enlazador nativo a eliminar el código nativo de la biblioteca con la que está realizando la vinculación. 
  - Tenga en cuenta que la búsqueda dinámica de símbolos no funcionará con esto. 

## <a name="summary"></a>Resumen

En esta guía se analiza cómo controlar el tiempo de una aplicación iOS y las opciones que hay que considerar en función de las opciones y la configuración de compilación del proyecto. 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 

The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 

### Clean build time 
178 seconds 

### Build again (without cleaning) after making _no changes_ 
12.5 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 

### Build again (without cleaning) after changing 1 line in each of the following files 

- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 

3 trials: 45 seconds, 45 seconds, 45 seconds 

### Build again (without cleaning) after changing 1 line in each of the following files 

- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 

45 seconds 

## Layer 2: "app thinning" aka "device specific builds" 

The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 

As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 

Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 

(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 

### Clean build time without "device specific builds" 
177 seconds 

### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 

* * * 

## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 

(These builds were again run on the command line using `xbuild`.) 

### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 

[1] Mac system used for testing: MacBookAir5,2 

- 2.0 GHz Core i7 (I7-3667U) 

2 Cores with hyper-threading 

L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 

- Standard MacBook soldered-in solid-state storage 

- 8 GB RAM 
---->

## <a name="related-links"></a>Vínculos relacionados

- [Entrada de blog](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [Vincular en iOS](~/ios/deploy-test/linker.md)
- [Configuración personalizada del enlazador](~/cross-platform/deploy-test/linker.md)
