---
title: "¿por qué es mi Xamarin.Forms . No se puede realizar el proyecto de Android de Maps con COMPILETODALVIK ERROR de nivel superior inesperado? "
MS. topic: solución de problemas de MS. Prod: Xamarin ms. AssetID: C0251EB1-F509-47AD-98D6-846AF46425E5 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/25/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>¿Por qué mi Xamarin.Forms . Se produce un ERROR en el proyecto de Android de Maps con COMPILETODALVIK de nivel superior inesperado?

Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos de Android con Xamarin.Forms . Maps.

Esto se resuelve normalmente aumentando el tamaño del montón de Java para el proyecto de Xamarin. Android. Siga estos pasos para aumentar el tamaño del montón:

## <a name="visual-studio"></a>Visual Studio

1. Haga clic con el botón derecho en el proyecto de Android & abrir las opciones del proyecto.
2. Vaya a **Opciones de Android-> avanzado**
3. En el cuadro de texto tamaño del montón de Java escriba 1G.
4. Recompile el proyecto.

![Captura de pantalla de las opciones de proyecto de Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opciones de compilación de Android en Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Haga clic con el botón derecho en el proyecto de Android & abrir las opciones del proyecto.
2. Vaya a **compilación-> Android Build-> avanzado**
3. En el cuadro de texto tamaño del montón de Java escriba 1G.
4. Recompile el proyecto.  

![Captura de pantalla de las opciones del proyecto Visual Studio para Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opciones de compilación de Android en Visual Studio para Mac")
