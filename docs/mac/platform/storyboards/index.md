---
title: Introducción a los guiones gráficos en Xamarin. Mac
description: En este artículo se proporciona una introducción al uso de guiones gráficos en una aplicación de Xamarin. Mac. En él, se describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos e Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 75affbdb6e919c15c644a68ae45e7ff657a53f6a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652272"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introducción a los guiones gráficos en Xamarin. Mac

_En este artículo se proporciona una introducción al uso de guiones gráficos en una aplicación de Xamarin. Mac. Trata la creación y el mantenimiento de la interfaz de usuario de la aplicación mediante guiones gráficos y Interface Builder de Xcode._

Los guiones gráficos permiten desarrollar una interfaz de usuario para la aplicación de Xamarin. Mac que no solo incluye las definiciones y los controles de la ventana, sino que también contiene los vínculos entre las distintas ventanas (a través de objetos segue) y los Estados de vista.

[![](images/intro01.png "Una interfaz de usuario de ejemplo en Xcode")](images/intro01.png#lightbox)

En este artículo se proporciona una introducción al uso de guiones gráficos para definir la interfaz de usuario de una aplicación de Xamarin. Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>¿Qué son los guiones gráficos?

Mediante el uso de guiones gráficos, toda la interfaz de usuario de una aplicación de Xamarin. Mac se puede definir en una sola ubicación con toda la navegación entre sus elementos individuales e interfaces de usuario. Los guiones gráficos para Xamarin. Mac funcionan de manera similar a los guiones gráficos de Xamarin. iOS. Sin embargo, contienen un conjunto diferente de _tipos segue_ debido a los diferentes expresiones de interfaz.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Trabajar con escenas

Como se indicó anteriormente, un guion gráfico define toda la interfaz de usuario de una aplicación determinada dividida en una información general funcional de _los controladores de vista_. En el Interface Builder de Xcode, cada uno de estos controladores reside en su propia _escena_.

[![](images/intro02.png "Un controlador de vista de ejemplo")](images/intro02.png#lightbox)

Cada escena representa un par determinado de vistas y controladores de vistas con un conjunto de líneas (denominado objetos segue) que conectan cada escena en la interfaz de usuario, con lo que se muestran sus relaciones. Algunos objetos segue definen cómo un controlador de vista contiene una o varias vistas secundarias o controladores de vista. Otros objetos segue, definen las transiciones entre el controlador de vista (como mostrar un elemento flotante o un cuadro de diálogo). 

[![](images/intro03.png "Un segue de ejemplo")](images/intro03.png#lightbox)

Lo más importante que hay que tener en cuenta es que cada segue representa el flujo de alguna forma de datos entre el elemento especificado de la interfaz de usuario de la aplicación.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Trabajar con controladores de vista

Los controladores de vista definen las relaciones entre una vista determinada de la información dentro de una aplicación Mac y el modelo de datos que proporciona esa información. Cada escena de nivel superior del guión gráfico representa un controlador de vista en el código de la aplicación de Xamarin. Mac.

[![](images/intro04.png "Un controlador de vista Slip de ejemplo")](images/intro04.png#lightbox)

De esta manera, cada controlador de vista es un emparejamiento reutilizable e independiente de la representación visual (vista) de la información y la lógica para presentar y controlar esa información.

Dentro de una escena determinada, puede realizar todas las acciones que normalmente se habrían controlado por archivos individuales `.xib` : 

- Coloque subvistas y controles (como botones y cuadros de texto).
- Defina las posiciones de los elementos y las restricciones de diseño automático.
- Acciones de conexión y salidas para exponer elementos de la interfaz de usuario al código.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Trabajar con objetos segue

Como se indicó anteriormente, objetos segue proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con el trabajo en guiones gráficos en iOS, sabe que objetos segue para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere de macOS, cuando objetos segue normalmente define "contención" (donde una escena es el elemento secundario de una escena primaria).

En macOS, la mayoría de las aplicaciones suelen agrupar sus vistas en la misma ventana con elementos de interfaz de usuario como vistas y pestañas de división. A diferencia de iOS, en el que las vistas se deben pasar de una pantalla a otra, debido a un espacio de presentación físico limitado.

En función de la contención de las tendencias de macOS, hay situaciones en las que se usan la _presentación objetos segue_ , como ventanas modales, vistas de hoja y popovers.

Al usar la presentación objetos segue, puede invalidar `PrepareForSegue` el método del controlador de vista primario para la presentación a fin de inicializar las variables y y proporcionar los datos al controlador de vistas que se está presentando.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Diseño y tiempo de ejecución

En tiempo de diseño (al diseñar la interfaz de usuario en la Interface Builder de Xcode), cada elemento de la interfaz de usuario de la aplicación se divide en sus elementos constituyentes:

- **Scenes** , que se componen de:
    - **Controlador de vista** : que define las relaciones entre las vistas y los datos que las admiten.
    - **Vistas y** subvistas: los elementos reales que componen la interfaz de usuario.
    - **Contención objetos segue** : que define las relaciones de elementos primarios y secundarios entre las escenas.
- **Objetos segue de presentación** : que definen modos de presentación individuales. 

Al definir cada elemento de esta manera, se permite la carga diferida de cada elemento solo cuando sea necesario durante el tiempo de ejecución. En macOS, todo el proceso se diseñó para permitir al desarrollador crear interfaces de usuario complejas y flexibles que requieran un mínimo de código de respaldo para que funcionen, todo ello a la vez que sea tan eficaz como sea posible con los recursos del sistema.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Inicio rápido de guion gráfico

En el [guion gráfico Inicio rápido](~/mac/platform/storyboards/quickstart.md) guía, vamos a crear una aplicación sencilla de Xamarin. Mac que presenta los conceptos clave para trabajar con guiones gráficos para crear una interfaz de usuario. La aplicación de ejemplo constará de una vista dividir que contiene un _área de contenido_ y un _área de inspector_ y presentará una ventana de cuadro de diálogo de preferencias simple. Vamos a usar objetos segue para unir todos los elementos de la interfaz de usuario.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Trabajo con guiones gráficos

En esta sección se tratan los detalles detallados sobre [Cómo trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) en una aplicación de Xamarin. Mac. Vamos a echar un vistazo en profundidad y cómo se componen de los controladores de vista y la vista. A continuación, echaremos un vistazo a cómo se unen las escenas con objetos segue. Por último, echaremos un vistazo a trabajar con tipos de segue personalizados. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Ejemplo de Storyboard complejo

Para ver un ejemplo de un ejemplo complejo de cómo trabajar con guiones gráficos en una aplicación de Xamarin. Mac, consulte la [aplicación de ejemplo SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha examinado rápidamente el trabajo con guiones gráficos en una aplicación de Xamarin. Mac. Vimos cómo crear una nueva aplicación con guiones gráficos y cómo definir una interfaz de usuario. También vimos cómo navegar entre diferentes ventanas y Estados de vista mediante objetos segue.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Hello, Mac (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/hello-mac)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
