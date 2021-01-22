---
title: 'Guiones gráficos en Xamarin. Mac: Inicio rápido'
description: En este documento se proporciona una introducción rápida a la creación de interfaces de usuario de macOS con guiones gráficos en Xamarin. Mac. Describe cómo crear un segue y crear una ventana de preferencias.
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 39e8e63c7612df95123e1e32edbfb3a8c28ffe37
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697714"
---
# <a name="storyboards-in-xamarinmac--quick-start"></a>Guiones gráficos en Xamarin. Mac: Inicio rápido

Como introducción rápida al uso de guiones gráficos para definir la interfaz de usuario de una aplicación de Xamarin. Mac, vamos a iniciar un nuevo proyecto de Xamarin. Mac. Seleccione   >    >  **aplicación de coco** de aplicación Mac y haga clic en el botón **siguiente** :

[![Adición de una nueva aplicación de coco](quickstart-images/qs01.png)](quickstart-images/qs01.png#lightbox)

Use el **nombre** de la aplicación `MacStoryboard` y haga clic en el botón **siguiente** :

[![Establecer el nombre de la aplicación](quickstart-images/qs02.png)](quickstart-images/qs02.png#lightbox)

Use el **nombre de proyecto** y el nombre de la **solución** predeterminados y haga clic en el botón **crear** :

[![Los nombres de proyecto y solución](quickstart-images/qs03.png)](quickstart-images/qs03.png#lightbox)

En el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo para su edición en el Interface Builder de Xcode:

[![Editar el guion gráfico en Xcode Interface Builder.](quickstart-images/qs04.png)](quickstart-images/qs04.png#lightbox)

Como puede ver más arriba, el guión gráfico predeterminado define la barra de menús de la aplicación y su ventana principal con el controlador de vista de ti y la vista. En nuestra aplicación de ejemplo, vamos a crear una interfaz de usuario que tiene una _vista de contenido_ principal en un lado y una _vista de inspector_ en el segundo.

Para ello, primero es necesario quitar el controlador de vista y la vista predeterminados que se incluyen con el guión gráfico; para ello, selecciónelo en Interface Builder y presione la tecla **Supr** :

[![Quitar el controlador de vista predeterminado](quickstart-images/qs05.png)](quickstart-images/qs05.png#lightbox)

A continuación, escriba `split` en el área de **filtro** , seleccione el controlador de vista de División vertical y arrástrelo hasta el _superficie de diseño_:

[![Buscar el controlador de vista en dos paneles](quickstart-images/qs06.png)](quickstart-images/qs06.png#lightbox)

Tenga en cuenta que el controlador incluye automáticamente dos controladores de vista secundarios (y sus vistas relacionadas), cableados a los lados izquierdo y derecho de la vista en dos paneles. Para asociar la vista en dos paneles a su ventana primaria, presione la tecla **control** , haga clic en el controlador de la ventana (el círculo azul en el marco del controlador de la ventana) y arrastre una línea hasta el controlador de vista en dos paneles. Seleccione el contenido de la **ventana** en el menú emergente:

[![Establecer la vista de contenido de Windows](quickstart-images/qs07.png)](quickstart-images/qs07.png#lightbox)

Esto enlazará los dos elementos de la interfaz mediante un segue:

[![Segue entre la ventana y el contenido.](quickstart-images/qs08.png)](quickstart-images/qs08.png#lightbox)

Queremos colocar una vista de texto en el lado izquierdo de la vista en dos paneles y hacer que rellene automáticamente el área disponible cuando se cambie el tamaño de la ventana o de la vista en dos paneles. Arrastre una vista de texto hasta el controlador de vista superior adjunto a la vista en dos paneles y haga clic en la restricción de diseño automático de **anclaje** (el segundo icono de la derecha en la parte inferior del superficie de diseño).

[![Configurar las restricciones](quickstart-images/qs09.png)](quickstart-images/qs09.png#lightbox)

Desde aquí, haremos clic en los cuatro iconos i en el cuadro de límite situado en la parte superior de las restricciones elemento flotante y **hago** clic en el botón **Agregar 4 restricciones** en la parte inferior para agregar las restricciones necesarias.

Si vuelve a Visual Studio para Mac y ejecuta el proyecto, observe que la vista de texto cambia automáticamente de tamaño para rellenar el lado izquierdo de la vista en dos paneles cuando se cambia el tamaño de la ventana o la División:

[![Un ejemplo de la aplicación en ejecución, que muestra texto en el panel izquierdo de la ventana.](quickstart-images/qs10.png)](quickstart-images/qs10.png#lightbox)

Como vamos a usar el lado derecho de la vista en dos paneles como un área de inspector, queremos que tenga un tamaño menor y permita que se contraiga. Vuelva a Xcode y edite la vista para el lado derecho seleccionándola en el Superficie de diseño y haciendo clic en el **Inspector size**. Desde aquí, escriba un **ancho** de `250` :

[![Establecer el ancho](quickstart-images/qs11.png)](quickstart-images/qs11.png#lightbox)

A continuación, seleccione el elemento dividido que representa el lado derecho, establezca una **prioridad de retención** superior y haga clic en la casilla el **usuario puede contraer** :

[![Editar la prioridad de retención](quickstart-images/qs12.png)](quickstart-images/qs12.png#lightbox)

Si vuelve a Visual Studio para Mac y ejecuta el proyecto ahora, observe que el lado derecho mantiene el tamaño más pequeño y se cambia el tamaño de la ventana:

[![Un ejemplo de la aplicación en ejecución, mostrando texto en el panel izquierdo más grande de la ventana.](quickstart-images/qs13.png)](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue"></a>

## <a name="defining-a-presentation-segue"></a>Definir una presentación segue

Vamos a diseñar el lado derecho de la vista en dos paneles para que actúe como inspector de las propiedades del texto seleccionado. Vamos a arrastrar algunos controles a la vista inferior para diseñar la interfaz de usuario del inspector. En el último control, queremos mostrar un elemento flotante que permita al usuario seleccionar entre cuatro estilos de carácter preestablecidos.

Agregaremos un botón al inspector y un controlador de vista al Superficie de diseño. Cambiaremos el tamaño del controlador de vista para que sea el tamaño que deseamos que tenga nuestro elemento flotante y le agregue cuatro botones. A continuación, **haremos** clic en el botón en la vista del inspector y lo arrastramos hasta el controlador de vista que representará nuestra elemento flotante:

[![Arrastrando para crear un nuevo segue en el controlador de vista.](quickstart-images/qs14.png)](quickstart-images/qs14.png#lightbox)

En el menú emergente, seleccionaremos **elemento flotante**: 

[![Seleccionar el tipo de segue de elemento flotante desde el controlador de vista.](quickstart-images/qs15.png)](quickstart-images/qs15.png#lightbox)

Por último, seleccionaremos el segue en el Superficie de diseño y estableceremos el **borde preferido** en **izquierda**. A continuación, se va a arrastrar una línea desde la **vista de delimitador** hasta el botón al que se desea adjuntar los elemento flotante:

[![Arrastrar para crear un nuevo segue adjuntando la vista del delimitador al botón.](quickstart-images/qs16.png)](quickstart-images/qs16.png#lightbox)

Si regresamos a Visual Studio para Mac, ejecutamos la aplicación y hacemos clic en el botón **None (ninguno** ) del inspector, se mostrará el elemento flotante:

[![Un ejemplo de segue en ejecución, que muestra elemento flotante.](quickstart-images/qs17.png)](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences"></a>

## <a name="creating-app-preferences"></a>Crear preferencias de aplicación

La mayoría de las aplicaciones estándar de macOS proporcionan un _cuadro de diálogo de preferencias_ que permite al usuario definir varias opciones que controlan diversos aspectos de la aplicación, como el aspecto o las cuentas de usuario.

Para definir una ventana de cuadro de diálogo de preferencias estándar, arrastre primero un controlador de vista de pestaña hasta el Superficie de diseño:

[![Editar el guion gráfico en Xcode arrastrando primero el controlador de vista de pestaña hasta el Superficie de diseño.](quickstart-images/qs18.png)](quickstart-images/qs18.png#lightbox)

De nuevo, esto incorporará automáticamente dos controladores de vista secundarios. Por ejemplo, vamos a agregar una etiqueta a cada vista que se centrará en ella:

[![Establecer las restricciones](quickstart-images/qs19.png)](quickstart-images/qs19.png#lightbox)

A continuación, queremos mostrar la ventana Preferencias cuando el usuario seleccione el elemento de menú **preferencias...** . En la barra de menús, seleccione el elemento de menú Preferencias, haga clic en la tecla **control** y arrastre una línea a nuestro controlador de vista de pestañas:

[![Arrastrar para crear un segue](quickstart-images/qs20.png)](quickstart-images/qs20.png#lightbox)

En el elemento emergente, seleccionaremos **modal** para mostrar esta ventana como un cuadro de diálogo modal:

[![Seleccionar el tipo de segue modal en el menú de segue de acción.](quickstart-images/qs21.png)](quickstart-images/qs21.png#lightbox)

Si se guardan los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el elemento de menú **preferencias...** , se mostrará el cuadro de diálogo nuevas preferencias:

[![Un ejemplo de segue en ejecución, que muestra el cuadro de diálogo nuevas preferencias.](quickstart-images/qs22.png)](quickstart-images/qs22.png#lightbox)

Es posible que observe que esto no parece una ventana de diálogo de preferencias de aplicación estándar de macOS. Para solucionarlo, incluya dos archivos de imagen en la carpeta de la aplicación de Xamarin. Mac `Resources` en el **Explorador de soluciones** y vuelva al Interface Builder de Xcode.

Seleccione el controlador de vista de pestañas y cambie su **estilo** a **barra de herramientas**: 

[![Establecer el estilo de la barra de pestañas](quickstart-images/qs23.png)](quickstart-images/qs23.png#lightbox)

Seleccione cada pestaña y asígnele una **etiqueta** y seleccione una de las imágenes para representarla:

[![Configuración de cada pestaña en Xcode](quickstart-images/qs24.png)](quickstart-images/qs24.png#lightbox)

Si se guardan los cambios, vuelva a Visual Studio para Mac, ejecute la aplicación y seleccione el elemento de menú **preferencias...** , el cuadro de diálogo se mostrará ahora como una aplicación estándar de MacOS:

[![Un ejemplo de la ventana de preferencias en ejecución](quickstart-images/qs25.png)](quickstart-images/qs25.png#lightbox)

Para obtener más información, vea la documentación sobre [Cómo trabajar con imágenes](~/mac/app-fundamentals/image.md), [menús](~/mac/user-interface/menu.md), [ventanas](~/mac/user-interface/window.md) y [cuadros de diálogo](~/mac/user-interface/dialog.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
