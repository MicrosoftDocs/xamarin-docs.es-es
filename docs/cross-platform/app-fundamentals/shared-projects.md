---
title: Usar proyectos compartidos para compartir código
description: Los proyectos compartidos permiten escribir código común al que hacen referencia varios proyectos de aplicación diferentes. El código se compila como parte de cada proyecto de referencia y puede incluir directivas de compilador para ayudar a incorporar una funcionalidad específica de la plataforma en el código base compartido.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: eee76c056d05edccd1e039bc5e4cb8107d1aceb5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016697"
---
# <a name="shared-projects-code-sharing"></a>Uso compartido de código de proyectos compartidos

_Los proyectos compartidos permiten escribir código común al que hacen referencia varios proyectos de aplicación diferentes. El código se compila como parte de cada proyecto de referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en la base de código compartido._

Los proyectos compartidos (también denominados a veces proyectos de recursos compartidos) permiten escribir código que se comparte entre varios proyectos de destino, incluidas las aplicaciones de Xamarin.

Admiten directivas de compilador para que pueda incluir condicionalmente código específico de la plataforma que se va a compilar en un subconjunto de los proyectos que hacen referencia al proyecto compartido. También hay compatibilidad con el IDE para ayudar a administrar las directivas de compilador y visualizar el aspecto que tendrá el código en cada aplicación.

Si ha usado la vinculación de archivos en el pasado para compartir código entre proyectos, los proyectos compartidos funcionan de forma similar, pero con una compatibilidad con el IDE mucho mejor.

## <a name="what-is-a-shared-project"></a>¿Qué es un proyecto compartido?

A diferencia de la mayoría de los demás tipos de proyecto, un proyecto compartido no tiene ningún resultado (en formato DLL), sino que el código se compila en cada proyecto que hace referencia a él. Esto se muestra en el diagrama siguiente: conceptualmente, todo el contenido del proyecto compartido se "copia en" cada proyecto que hace referencia y se compila como si formara parte de ellos.

![](shared-projects-images/sharedassetproject.png "Shared Project architecture")

El código de un proyecto compartido puede contener directivas de compilador que habilitarán o deshabilitarán secciones de código dependiendo del proyecto de aplicación que use el código, que se sugiere en los cuadros plataforma de color del diagrama.

Un proyecto compartido no se compila por sí solo, existe únicamente como una agrupación de archivos de código fuente que se pueden incluir en otros proyectos. Cuando se hace referencia a él en otro proyecto, el código se compila eficazmente como *parte* de ese proyecto. Los proyectos compartidos no pueden hacer referencia a ningún otro tipo de proyecto (incluidos otros proyectos compartidos).

Tenga en cuenta que los proyectos de aplicación de Android no pueden hacer referencia a otros proyectos de aplicación Android; por ejemplo, un proyecto de prueba unitaria de Android no puede hacer referencia a un proyecto de aplicación de Android. Para obtener más información acerca de esta limitación, consulte este debate en el [Foro](https://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Tutorial de Visual Studio para Mac

En esta sección se explica cómo crear y usar un proyecto compartido mediante Visual Studio para Mac. Consulte la sección [ejemplo de proyectos compartidos](#Shared_Project_Example) para ver un ejemplo completo.

## <a name="creating-a-shared-project"></a>Crear un proyecto compartido

Para crear un nuevo proyecto compartido, vaya a **archivo > nueva solución...** (o haga clic con el botón derecho en una solución existente y elija **Agregar > agregar nuevo proyecto...** ):

[![Nuevo proyecto compartido](shared-projects-images/xs-newsolution-sml.png "Nueva solución")](shared-projects-images/xs-newsolution.png#lightbox)

En la siguiente pantalla, elija el nombre del proyecto y haga clic en **crear**.

A continuación se muestra un nuevo proyecto compartido; Observe que no hay ninguna referencia ni nodo de componente. no se admiten para los proyectos compartidos.

![Proyecto compartido vacío](shared-projects-images/xs-empty.png "Proyecto compartido vacío")

Para que un proyecto compartido sea útil, es necesario que haga referencia al menos a un proyecto que permita la compilación (por ejemplo, una aplicación o biblioteca de iOS o Android, o un proyecto de PCL). Un proyecto compartido no se compila cuando no tiene nada que haga referencia a él, por lo que no se resaltarán los errores de sintaxis (o cualquier otro) hasta que otra cosa haya hecho referencia a él.

La adición de una referencia a un proyecto compartido se realiza de la misma manera que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin. iOS que hace referencia a un proyecto compartido.

![](shared-projects-images/xs-reference.png "Project reference to Shared Project")

Una vez que otra biblioteca o aplicación hace referencia al proyecto compartido, puede compilar la solución y ver los errores en el código. Cuando _dos o más_ proyectos hacen referencia al proyecto compartido, aparece un menú en la parte superior izquierda del editor de código fuente que muestra elegir qué proyectos hacen referencia a este archivo.

## <a name="shared-project-options"></a>Opciones de proyecto compartido

Al hacer clic con el botón derecho en un proyecto compartido y elegir **Opciones** , hay menos valores de configuración que otros tipos de proyecto. Dado que los proyectos compartidos no se compilan (por sí solos), no se pueden establecer las opciones de salida o de compilador, las configuraciones de proyecto, la firma de ensamblados o los comandos personalizados. El código de un proyecto compartido hereda de forma eficaz estos valores de cualquier cosa que haga referencia a ellos.

A continuación se muestra la pantalla **Opciones** : el **nombre** del proyecto y el **espacio de nombres predeterminado** son los dos únicos valores que normalmente cambiará.

![](shared-projects-images/xs-sharedprojectoptions.png "Shared Project Options")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio

En esta sección se explica cómo crear y usar un proyecto compartido con Visual Studio. Consulte la sección [ejemplo de proyectos compartidos](#Shared_Project_Example) para ver una implementación completa.

### <a name="creating-a-shared-project"></a>Crear un proyecto compartido

Para crear un nuevo proyecto compartido, vaya a **archivo**  > **nuevo**  > **proyecto**.

En Visual Studio 2019, escriba **compartido** en el cuadro de búsqueda de la página **crear un nuevo proyecto** . Seleccione la plantilla **proyecto compartido** y, a continuación, seleccione **siguiente**. Escriba un nombre para el proyecto y, a continuación, seleccione **crear**.

En Visual Studio 2017, seleccione la plantilla **proyecto compartido** y, a continuación, elija un nombre para el proyecto.

![Plantilla de proyecto compartido en Visual Studio 2017](shared-projects-images/vs-newsolution.png)

También puede Agregar un nuevo proyecto compartido a una solución existente; para ello, haga clic con el botón derecho en el archivo de solución y elija **agregar > nuevo proyecto**. Un nuevo proyecto compartido tiene el aspecto que se muestra a continuación (después de que se haya agregado un archivo de clase). Observe que no hay referencias ni nodos de componentes. no se admiten para los proyectos compartidos.

![](shared-projects-images/vs-empty.png "Empty Shared Project")

Para que un proyecto compartido sea útil, es necesario que haga referencia al menos a un proyecto que permita la compilación (por ejemplo, una aplicación o biblioteca de iOS o Android, o un proyecto de PCL). Un proyecto compartido no se compila cuando no tiene nada que haga referencia a él, por lo que no se resaltarán los errores de sintaxis (o cualquier otro) hasta que otra cosa haya hecho referencia a él.

La adición de una referencia a un proyecto compartido se realiza de la misma manera que hace referencia a un proyecto de biblioteca normal. Esta captura de pantalla muestra un proyecto de Xamarin. iOS que hace referencia a un proyecto compartido.

![](shared-projects-images/vs-reference.png "Project reference to Shared Project")

Una vez que otra biblioteca o aplicación hace referencia al proyecto compartido, puede compilar la solución y ver los errores en el código. Cuando _dos o más_ proyectos hacen referencia al proyecto compartido, se muestra un menú en la parte superior izquierda del editor de código fuente para ver qué proyectos hacen referencia al archivo de código actual.

### <a name="shared-project-properties"></a>Propiedades del proyecto compartido

Al seleccionar un proyecto compartido, hay menos opciones de configuración en el panel propiedades que en otros tipos de proyecto. Dado que los proyectos compartidos no se compilan (por sí solos), no se pueden establecer las opciones de salida o de compilador, las configuraciones de proyecto, la firma de ensamblados o los comandos personalizados. El código de un proyecto compartido hereda de forma eficaz estos valores de cualquier cosa que haga referencia a ellos.

El panel **propiedades** se muestra a continuación: el **espacio de nombres raíz** es el único valor que puede cambiar.

![](shared-projects-images/vs-sharedprojectproperties.png "Shared Project Properties")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Ejemplo de proyecto compartido

En el ejemplo de [tarea](https://github.com/xamarin/mobile-samples/tree/master/Tasky) se usa un proyecto compartido para contener el código común que usan las aplicaciones iOS, Android y Windows Phone. Los archivos de código fuente `SQLite.cs` y `TaskRepository.cs` utilizan directivas de compilador (por ejemplo, `#if __ANDROID__`) para generar una salida diferente para cada una de las aplicaciones que hacen referencia a ellas.

La estructura de la solución completa se muestra a continuación (en Visual Studio para Mac y Visual Studio, respectivamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac solution")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio solution")

-----

Se puede navegar por el proyecto Windows Phone desde Visual Studio para Mac, aunque ese tipo de proyecto no se admite para la compilación en Visual Studio para Mac.

A continuación se muestran las aplicaciones en ejecución:

![](shared-projects-images/example.png "iOS, Android, Windows Phone examples")

## <a name="summary"></a>Resumen

En este documento se describe cómo funcionan los proyectos compartidos, cómo se pueden crear y usar en Visual Studio para Mac y Visual Studio, y se ha introducido una sencilla aplicación de ejemplo que muestra un proyecto compartido en acción.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de tarea](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Bibliotecas de clases portables (ejemplo)](~/cross-platform/app-fundamentals/pcl.md)
- [Opciones de código compartido (ejemplo)](~/cross-platform/app-fundamentals/code-sharing.md)
