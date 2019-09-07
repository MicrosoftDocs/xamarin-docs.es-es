---
title: Uso de enlaces de Xamarin. Mac para aplicaciones de consola
description: Cree una aplicación de consola sin periféricos con Xamarin. Mac para acceder a las API de macOS nativas.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769892"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Enlaces de Xamarin. Mac en aplicaciones de consola

Hay algunos escenarios en los que desea usar algunas de las API nativas de C# Apple en para compilar &ndash; una aplicación sin periféricos que no tenga &ndash; una C#interfaz de usuario mediante.

Las plantillas de proyecto para las aplicaciones Mac incluyen una `NSApplication.Init()` llamada a seguida de una `NSApplication.Main(args)`llamada a, normalmente tiene el siguiente aspecto:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

La llamada a `Init` prepara el entorno de tiempo de ejecución de Xamarin. Mac `Main(args)` , la llamada a inicia el bucle principal de la aplicación de coco, que prepara la aplicación para recibir eventos del teclado y del mouse y mostrar la ventana principal de la aplicación.   La llamada a `Main` también intentará buscar recursos de cacao, preparar una ventana de nivel superior y esperar que el programa forme parte de un paquete de aplicaciones (programas distribuidos en un directorio `.app` con la extensión y un diseño muy específico).

Las aplicaciones sin periféricos no necesitan una interfaz de usuario y no necesitan ejecutarse como parte de un lote de aplicaciones.

## <a name="creating-the-console-app"></a>Creación de la aplicación de consola

Por tanto, es mejor empezar con un tipo de proyecto de consola .NET normal.

Tiene que hacer algunas cosas:

- Cree un proyecto vacío.
- Haga referencia a la biblioteca Xamarin. Mac. dll.
- Traiga la dependencia no administrada al proyecto.

Estos pasos se explican con más detalle a continuación:

### <a name="create-an-empty-console-project"></a>Crear un proyecto de consola vacío

Cree un nuevo proyecto de consola de .NET, asegúrese de que es .NET y no .NET Core, ya que Xamarin. Mac. dll no se ejecuta en el entorno de tiempo de ejecución de .NET Core, solo se ejecuta con el entorno de ejecución de mono.

### <a name="reference-the-xamarinmac-library"></a>Referencia a la biblioteca de Xamarin. Mac

Para compilar el código, querrá hacer referencia al `Xamarin.Mac.dll` ensamblado desde este directorio:`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Para ello, vaya a las referencias del proyecto, seleccione la pestaña **ensamblado .net** y haga clic en el botón **examinar** para buscar el archivo en el sistema de archivos.  Navegue hasta la ruta de acceso anterior y, a continuación, seleccione el **archivo Xamarin. Mac. dll** de ese directorio.

Esto le proporcionará acceso a las API de coco en tiempo de compilación.   Llegados a este punto, puede `using AppKit` agregar a la parte superior del archivo y llamar al `NSApplication.Init()` método.   Solo hay un paso más para poder ejecutar la aplicación.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Traiga la biblioteca de compatibilidad no administrada al proyecto

Antes de que se ejecute la aplicación, debe incluir la `Xamarin.Mac` biblioteca de soporte técnico en el proyecto.   Para ello, agregue un nuevo archivo al proyecto (en opciones del proyecto, seleccione **Agregar**y, a continuación, **Agregar archivo existente**) y navegue hasta este directorio:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Aquí, seleccione el archivo **libxammac. dylib**.   Se le ofrecerá la posibilidad de copiar, vincular o mover.   Personalmente, como la vinculación, pero la copia también funciona.    A continuación, debe seleccionar el archivo y, en el panel de propiedades (seleccionar **ver > paneles > propiedades** si el panel de propiedades no está visible), ir a la sección **generar** y establecer la opción **Copiar en el directorio de salida** en **copiar si es posterior**.

Ahora puede ejecutar la aplicación de Xamarin. Mac.

El resultado en el directorio bin tendrá el siguiente aspecto:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Para ejecutar esta aplicación, necesitará todos esos archivos en el mismo directorio.

## <a name="building-a-standalone-application-for-distribution"></a>Creación de una aplicación independiente para su distribución

Es posible que desee distribuir un único ejecutable a los usuarios.  Para ello, puede usar la `mkbundle` herramienta para convertir los distintos archivos en un ejecutable independiente.

En primer lugar, asegúrese de que la aplicación se compila y se ejecuta.   Una vez que esté satisfecho con los resultados, puede ejecutar el siguiente comando desde la línea de comandos:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

En la invocación de línea de comandos anterior, `-o` se usa la opción para especificar la salida generada, en este caso `/tmp/consoleapp`, pasamos.   Ahora se trata de una aplicación independiente que puede distribuir y no tiene dependencias externas en mono o Xamarin. Mac, es un ejecutable totalmente contenido.

La línea de comandos especificó manualmente el archivo **Machine. config** que se va a usar y un archivo de configuración de asignación de la biblioteca de todo el sistema.   No son necesarios para todas las aplicaciones, pero es conveniente empaquetarlas, ya que se usan cuando se usan más capacidades de .NET.

## <a name="project-less-builds"></a>Compilaciones sin proyecto

No necesita un proyecto completo para crear una aplicación de Xamarin. Mac independiente; también puede usar archivos make de UNIX simples para realizar el trabajo.   En el ejemplo siguiente se muestra cómo configurar un archivo make para una aplicación de línea de comandos simple:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)

bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

Lo anterior `Makefile` proporciona tres destinos:

- `make`compilará el programa
- `make run`compilará y ejecutará el programa en el directorio actual.
- `make bundle`creará un archivo ejecutable independiente
