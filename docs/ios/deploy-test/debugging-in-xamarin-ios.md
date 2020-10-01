---
title: Depurar aplicaciones Xamarin.iOS
description: En este documento se describe cómo usar el depurador de Visual Studio para Mac o Visual Studio 2019 para depurar una aplicación Xamarin.iOS, incluido el establecimiento de puntos de interrupción, la depuración inalámbrica, etc.
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 8d4b60a2e8e53b5b3f97a781b7f1888239541f1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431871"
---
# <a name="debugging-xamarinios-apps"></a>Depurar aplicaciones Xamarin.iOS

_Con el depurador integrado en Visual Studio para Mac o Visual Studio, se pueden depurar aplicaciones de Xamarin.iOS._

Usar la compatibilidad con la depuración nativa de Visual Studio para Mac para depurar C# y otros lenguajes de código administrados, y usar [LLDB](https://lldb.llvm.org/tutorial.html) cuando necesite depurar código de C, C++ u Objective C que podría vincular con el proyecto de Xamarin.iOS.

> [!NOTE]
> Al compilar aplicaciones en modo de depuración, Xamarin.iOS generará aplicaciones más lentas y mucho mayores, ya que debe instrumentarse cada línea de código. Antes de lanzar, asegúrese de hacer una compilación de versión.

El depurador de Xamarin.iOS se integra en el IDE y permite a los desarrolladores depurar aplicaciones de Xamarin.iOS compiladas con cualquiera de los lenguajes administrados admitidos por Xamarin.iOS en el simulador y en el dispositivo.

El depurador de Xamarin.iOS utiliza el [Mono Soft Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/), lo que significa que el código generado y el tiempo de ejecución Mono cooperarán con el IDE a fin de proporcionar una experiencia de depuración. Esto es diferente de depuradores como LLDB o MDB, que controlan el programa sin el conocimiento o la cooperación del programa depurado.

## <a name="setting-breakpoints"></a>Establecer puntos de interrupción

Cuando esté listo para iniciar la depuración de la aplicación, el primer paso es [establecer puntos de interrupción en la aplicación](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). Para ello, haga clic en el área del margen del editor, junto al número de la línea de código en la que desea interrumpir:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Establecimiento de puntos de interrupción](debugging-in-xamarin-ios-images/debugging1.png)](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Establecimiento de puntos de interrupción](debugging-in-xamarin-ios-images/debugging1a.png)](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

Puede ver todos los puntos de interrupción que se establecieron en el código en el **Panel de puntos de interrupción**:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Panel Puntos de interrupción](debugging-in-xamarin-ios-images/image0a.png)](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 Si el Panel de puntos de interrupción no se muestra automáticamente, para hacerlo visible, seleccione _Vista > Depurar Windows > Puntos de interrupción_.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Panel Puntos de interrupción](debugging-in-xamarin-ios-images/image0.png)](debugging-in-xamarin-ios-images/image0.png#lightbox)

 Si el Panel de puntos de interrupción no se muestra automáticamente, para hacerlo visible, seleccione _Depurar > Windows > Puntos de interrupción_.

-----

Antes de comenzar la depuración de cualquier aplicación, asegúrese siempre de que la configuración está establecida en **Depurar**, ya que contiene un conjunto de herramientas útil para admitir la depuración, incluyendo puntos de interrupción, la utilización de visualizadores de datos y ver la pila de llamadas:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Depuración en el simulador](debugging-in-xamarin-ios-images/debugging7.png)](debugging-in-xamarin-ios-images/debugging7.png#lightbox)
[![Depuración en un dispositivo físico](debugging-in-xamarin-ios-images/debugging7a.png)](debugging-in-xamarin-ios-images/debugging7a.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Depuración en el simulador](debugging-in-xamarin-ios-images/debugging7c.png)](debugging-in-xamarin-ios-images/debugging7c.png#lightbox)
[![Depuración en un dispositivo físico](debugging-in-xamarin-ios-images/debugging7d.png)](debugging-in-xamarin-ios-images/debugging7d.png#lightbox)

-----

## <a name="start-debugging"></a>Iniciar depuración
Para iniciar la depuración, seleccione el dispositivo de destino o similar en el IDE:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Selección del dispositivo de destino](debugging-in-xamarin-ios-images/debugging7b.png)](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Selección del dispositivo de destino](debugging-in-xamarin-ios-images/debugging7e.png)](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----

A continuación, implemente la aplicación presionando el botón **Reproducir**.

Cuando se alcance un punto de interrupción, el código se resaltará en amarillo:

[![Código resaltado en amarillo](debugging-in-xamarin-ios-images/image2.png)](debugging-in-xamarin-ios-images/image2.png#lightbox)

Las herramientas de depuración, como la inspección de los valores de objetos, pueden utilizarse en este punto para obtener más información sobre lo que sucede en el código:

[![Representación de un valor de color](debugging-in-xamarin-ios-images/image3.png)](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>Puntos de interrupción condicionales

También puede establecer reglas que dictan las circunstancias en las que debería ocurrir un punto de interrupción, esto se conoce como agregar un *punto de interrupción condicional*.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para establecer un punto de interrupción condicional, obtenga acceso a la **ventana de propiedades del punto de interrupción**, lo cual puede hacerse de dos maneras:

- Para agregar un nuevo punto de interrupción condicional, haga clic con el botón derecho en el margen del editor, a la izquierda del número de línea del código para el que quiere establecer un punto de interrupción y seleccione Nuevo punto de interrupción:

  [![Selección de Nuevo punto de interrupción](debugging-in-xamarin-ios-images/image4.png)](debugging-in-xamarin-ios-images/image4.png#lightbox)

- Para agregar una condición a un punto de interrupción existente, haga doble clic en el punto de interrupción y seleccione **Propiedades de punto de interrupción** o, en el **Panel de puntos de interrupción**, seleccione el botón de propiedades que se muestra a continuación:

  [![Panel Puntos de interrupción](debugging-in-xamarin-ios-images/image5.png)](debugging-in-xamarin-ios-images/image5.png#lightbox)

Puede escribir la condición bajo la que desea que se produzca el punto de interrupción:

[![Especificación de la condición para que se produzca el punto de interrupción](debugging-in-xamarin-ios-images/image6.png)](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para establecer un punto de interrupción condicional en Visual Studio, primero [establezca un punto de interrupción normal](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint). Haga doble clic en el punto de interrupción para mostrar el menú contextual:

 [![Menú contextual del punto de interrupción](debugging-in-xamarin-ios-images/image4vs.png)](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

Seleccione **Condiciones...** para mostrar el menú _Configuración de punto de interrupción_:

 [![Menú Configuración de punto de interrupción](debugging-in-xamarin-ios-images/image6vs.png)](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

Aquí podrá escribir la condición bajo la que desea que se produzca el punto de interrupción

Para obtener más información sobre el uso de condiciones de punto de interrupción en las versiones anteriores de Visual Studio, consulte la [Documentación de Visual Studio](/visualstudio/debugger/using-breakpoints) sobre este tema.

-----

## <a name="navigating-through-code"></a>Navegar por el código

Cuando se ha alcanzado un punto de interrupción, las herramientas de depuración le permiten obtener el control sobre la ejecución del programa. El IDE mostrará cuatro botones, permitiéndole ejecutar y recorrer el código.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En Visual Studio para Mac, tendrán un aspecto similar al siguiente:

 [![Las herramientas de depuración permiten a los desarrolladores tener el control de la ejecución del programa.](debugging-in-xamarin-ios-images/image7.png)](debugging-in-xamarin-ios-images/image7.png#lightbox)

Estos son:

- **Reproducir/detener**: iniciará o detendrá la ejecución del código, hasta el siguiente punto de interrupción.
- **Paso a paso por procedimientos**: ejecutará la siguiente línea de código. Si la siguiente línea es una llamada de función, el salto ejecutará la función y se detendrá en la siguiente línea de código _después_ de la función.
- **Depurar paso a paso con instrucciones**: esto también ejecutará la siguiente línea de código. Si la siguiente línea es una llamada de función, Depurar paso a paso con instrucciones se detendrá en la primera línea de la función, lo que le permitirá continuar con la depuración línea por línea de la función. Si la línea siguiente no es una función, se comportará igual Paso a paso por procedimientos.
- **Paso a paso para salir**: volverá a la línea donde se llamó la función actual.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En Visual Studio tendrá un aspecto similar al siguiente:

[![Las herramientas de depuración permiten a los desarrolladores tener el control de la ejecución del programa.](debugging-in-xamarin-ios-images/image7vs.png)](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

Estos son:

- **Reproducir/detener**: iniciará o detendrá la ejecución del código, hasta el siguiente punto de interrupción.
- **Paso a paso por procedimientos (F11)** : ejecutará la siguiente línea de código. Si la siguiente línea es una llamada de función, el salto ejecutará la función y se detendrá en la siguiente línea de código _después_ de la función.
- **Depurar paso a paso con instrucciones(F10)** : esto también ejecutará la siguiente línea de código. Si la siguiente línea es una llamada de función, Depurar paso a paso con instrucciones se detendrá en la primera línea de la función, lo que le permitirá continuar con la depuración línea por línea de la función. Si la línea siguiente no es una función, se comportará igual Paso a paso por procedimientos.
- **Paso a paso para salir (MAYÚS + F11)** : volverá a la línea donde se llamó la función actual.

Para acceder a documentación más exhaustiva sobre la depuración, vea [Navegar por el código con el depurador de Visual Studio](/visualstudio/debugger/navigating-through-code-with-the-debugger).

-----

### <a name="breakpoints"></a>Puntos de interrupción

Es importante señalar que iOS proporciona a las aplicaciones solo unos cuantos segundos (10) para que se inicien y completen el método `FinishedLaunching` en el delegado de la aplicación. Si la aplicación no completa este método en 10 segundos, iOS terminará el proceso.

Esto significa que es casi imposible establecer puntos de interrupción en el código de inicio del programa. Si desea depurar el código de inicio, debe retrasar parte de su inicialización y colocarla en un método invocado por el temporizador o en algún otro tipo de método de devolución de llamada que se ejecute después de que FinishedLaunching haya terminado.

## <a name="device-diagnostics"></a>Diagnóstico del dispositivo

Si se produce un error al configurar el depurador, puede habilitar el diagnóstico detallado agregando "-v - v - v" a los argumentos mtouch adicionales en las opciones del proyecto. Esto imprimirá información detallada del error en la consola del dispositivo.

 <a name="WiFi_Debugging"></a>

## <a name="wireless-debugging"></a>Depuración inalámbrica

El valor predeterminado de Xamarin.iOS es depurar la aplicación en los dispositivos a través de la conexión USB. A veces, el dispositivo USB puede ser necesario para probar la conexión o desconexión del cable para desarrollar aplicaciones basadas en ExternalAccessory. En esos casos, puede utilizar la depuración a través de la red inalámbrica.

Para más información sobre la implementación y depuración inalámbricas, consulte la guía sobre [implementación inalámbrica](~/ios/deploy-test/wireless-deployment.md).

<a name="Technical_Details"></a>

## <a name="technical-details"></a>Detalles técnicos

Xamarin.iOS utiliza el nuevo depurador suave Mono. A diferencia del depurador Mono estándar, que es un programa que controla un proceso independiente mediante las interfaces del sistema operativo para controlar un proceso independiente, el depurador suave funciona haciendo que el tiempo de ejecución Mono exponga la funcionalidad de depuración a través de un protocolo de conexión.

Al inicio, una aplicación que va a ser depurada contacta el depurador y este empieza a funcionar. En Xamarin.iOS para Visual Studio, Xamarin Mac Agent actúa como intermediario entre la aplicación (en Visual Studio) y el depurador.

Este depurador suave requiere una combinación cooperativa de depuración cuando se ejecuta en el dispositivo. Esto significa que el binario se generará cuando la depuración sea mayor, a medida que el código se instrumenta para contener código adicional en cada punto de secuencia para dar soporte a la depuración.

<a name="Accessing_the_Console"></a>

## <a name="accessing-the-console"></a>Acceso a la consola

Los registros de bloqueo y los resultados de la clase Console se enviarán a la consola del iPhone. Puede acceder a esta consola con Xcode mediante el "Organizador", seleccionando el dispositivo desde él.

Como alternativa, si no desea iniciar Xcode, puede utilizar la Utilidad de configuración de iPhone de Apple para acceder directamente a la consola. Esto tiene la ventaja adicional de que puede acceder a los registros de la consola de una máquina de Windows si está depurando un problema en el campo.

Para los usuarios de Visual Studio, hay algunos registros disponibles en la ventana de salida, pero debe cambiar al equipo Mac para obtener registros más exhaustivos y detallados.

-----

<a name="Debugging_Mono's_Class_Libraries"></a>

## <a name="debugging-monos-class-libraries"></a>Depuración de bibliotecas de clases de Mono

Xamarin.iOS se distribuye con el código fuente de bibliotecas de clases de Mono y puede utilizarlo para ver cómo funcionan las cosas con el depurador.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Puesto que esta característica consume más memoria durante la depuración, está desactivada de forma predeterminada.

Para habilitar esta característica, asegúrese de que la opción **Solo depurar código de proyecto, no depurar paso a paso con instrucciones en el código de marco** está desactivada en el menú _Visual Studio para Mac > Preferencias > Depurador_ tal como se muestra a continuación:

[![Depuración de bibliotecas de clases de Mono](debugging-in-xamarin-ios-images/debugging6.png)](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para depurar las bibliotecas de clases en Visual Studio, debe deshabilitar **Solo mi código** en el menú _Depurar > Opciones_. En el nodo _Depuración > General_, desactive la casilla **Habilitar Solo mi código**:

[![Depuración de bibliotecas de clases de Mono](debugging-in-xamarin-ios-images/debugging6vs.png)](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

Una vez hecho esto, puede iniciar la aplicación y depurar paso a paso con instrucciones en cualquiera de las bibliotecas de clases de núcleo de Mono.

## <a name="related-links"></a>Vínculos relacionados

- [Depuración con Xamarin](/visualstudio/mac/debugging/)
- [Visualizaciones de datos](/visualstudio/mac/data-visualizations/)
- [Establezca un punto de interrupción](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)
- [Examinar el código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)
- [Información de salida a la ventana de registro](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)