---
title: Pantallas de inicio para aplicaciones de Xamarin. iOS
description: En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos iOS, en cualquier resolución y orientación, con un solo guión gráfico unificado.
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2018
ms.openlocfilehash: 76e9d91b735f2ae5041330d8e290347ae9314487
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654802"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Pantallas de inicio para aplicaciones de Xamarin. iOS

_En este artículo se explica cómo crear una pantalla de inicio de la aplicación para todos los dispositivos iOS, en cualquier resolución y orientación, con un solo guión gráfico unificado._

Antes de iOS 8, la creación de una pantalla de inicio para una aplicación de iOS requirió que el desarrollador proporcionara un recurso de imagen para cada uno de los distintos factores de forma y resolución de los dispositivos en los que se podía ejecutar la aplicación. Sin embargo, desde el lanzamiento de iOS 8, se ha podido usar un solo guion gráfico unificado para crear una pantalla de inicio que sea correcta en todos los casos.

En este breve tutorial se describe cómo crear una pantalla de inicio con un guion gráfico proporcionado de forma predeterminada en un proyecto nuevo o con un guion gráfico agregado manualmente a un proyecto existente. A continuación, se muestra cómo usar el diseñador de iOS para agregar una vista de imagen y una etiqueta al guion gráfico, establecer restricciones en esas vistas y comprobar que el guion gráfico es correcto para varios dispositivos y orientaciones.

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>Administrar pantallas de inicio con guiones gráficos

En iOS 8 (y versiones posteriores), el desarrollador puede crear un guión gráfico unificado especial para proporcionar la pantalla de inicio en lugar de usar una o varias imágenes de inicio estáticas. Al crear un guion gráfico de inicio en el diseñador de iOS, use las clases de tamaño y el diseño automático para definir diferentes diseños para diferentes entornos de visualización. Mediante el uso de clases de tamaño y diseño automático, el desarrollador puede crear una única pantalla de inicio que tenga buenos problemas en todos los dispositivos y entornos de pantalla.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En Visual Studio para Mac, cree un nuevo proyecto; para ello, seleccione **archivo > nueva solución** y, a continuación, elija **aplicación de vista única**: 

    ![La ventana nuevo proyecto, con la aplicación de vista única seleccionada](launch-screens-images/launch01.png)

    - De forma predeterminada, un proyecto nuevo incluye un archivo **LaunchScreen. Storyboard** que define la interfaz de la pantalla de inicio. 
    - Para agregar en su lugar un guion gráfico de pantalla de inicio a un proyecto existente, haga clic con el botón derecho en el nombre del proyecto en el **Panel de solución** , elija **Agregar > nuevo archivo...** y, a continuación, seleccione **pantalla de inicio**:

    ![La ventana nuevo archivo, con la pantalla de inicio de iOS seleccionada](launch-screens-images/launch01b.png)

    - Asigne al archivo el nombre **LaunchScreen** u otro nombre de su elección.

2. Configure el proyecto para que use el guión gráfico adecuado para su pantalla de Inicio:

    - Haga doble clic en el archivo **info. plist** en el **Panel de solución** para abrirlo para su edición.
    - En la sección **imágenes de inicio** , asegúrese de que la pantalla de **Inicio** está establecida en el nombre del guion gráfico adecuado:

    ![Selector de la pantalla de inicio en info. plist](launch-screens-images/launch02.png)

    - De forma predeterminada, un nuevo proyecto está configurado para usar **LaunchScreen. Storyboard** como su pantalla de inicio.

3. Agregue una imagen al catálogo de recursos **assets. xcassets** para que esté disponible para su uso en la pantalla de inicio. Para obtener más información, consulte la sección [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md) de la guía de [visualización](~/ios/app-fundamentals/images-icons/displaying-an-image.md) de imágenes.

4. Abra **LaunchScreen. Storyboard** para editarlo haciendo doble clic en él en el **Panel de solución**.

5. Elija un dispositivo y una orientación para obtener una vista previa del guión gráfico de la pantalla de inicio en el diseñador de iOS. Abra el panel de selección de dispositivos en la barra de herramientas inferior y seleccione **iPhone 4S** y **vertical**.

    ![Barra de herramientas de selección de dispositivos](launch-screens-images/launch05.png)

    - Tenga en cuenta que la selección de un dispositivo y la orientación solo cambia el modo en que iOS Designer muestra una vista previa del diseño. Independientemente de la selección realizada aquí, las restricciones recién agregadas se aplican a todos los dispositivos y orientaciones, a menos que se use el botón **Editar rasgos** para especificar lo contrario. 

6. Establecer el color de **fondo** de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el centro del controlador de vista y ajuste el color de fondo mediante el **Panel de propiedades**:

    ![Una vista única con un color de fondo púrpura](launch-screens-images/launch06.png)

7. Agregue una **vista de imagen** a la pantalla de inicio y establezca su **imagen**de origen:

    - Arrastre una **vista de imagen** desde el **panel del cuadro de herramientas** hasta el centro de la vista.
    - Con la **vista de imagen** seleccionada, en la sección **Widget** del **Panel de propiedades** establezca la propiedad **Image** en el conjunto de imágenes ya agregado al catálogo de recursos **assets. xcassets** . Cambie la posición y el tamaño de la **vista de imagen** según sea necesario:
    
    ![Una vista de imagen con su conjunto de propiedades de imagen](launch-screens-images/launch07.png)

8. Agregue una **etiqueta** debajo de la **vista de imagen** y use el **Panel de propiedades** para establecer sus atributos: 

    ![Una etiqueta con su conjunto de colores y texto](launch-screens-images/launch08.png)

9. Cambie al modo de edición de restricciones con el botón derecho de la **barra de herramientas restricciones**:
    
    ![Botón modo de edición de restricciones](launch-screens-images/launch09.png)

10. Agregar restricciones a la vista de **imagen**, establecer su alto y ancho y centrarla horizontal y verticalmente:

    ![Una vista de imagen con restricciones de diseño](launch-screens-images/launch10.png)

    - Para obtener más información sobre cómo agregar restricciones, consulte [diseño automático con el Xamarin Designer para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

11. Agregar restricciones a la **etiqueta**, centrarla horizontalmente, darle un alto y un ancho, y colocarla verticalmente a una distancia fija de la vista de **imagen**:

    ![Etiqueta con restricciones de diseño](launch-screens-images/launch11.png)

12. Pruebe otros dispositivos y orientaciones para comprobar que el diseño se parece a todos los escenarios. En los casos en los que es necesario realizar ajustes para un dispositivo o una orientación concretos, use el botón **Editar rasgos** para agregar restricciones a clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch12.png)

13. Guarde los cambios en el guion gráfico. Ejecute la aplicación en un simulador o un dispositivo y la pantalla de inicio estará visible mientras se inicia la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cree un nuevo proyecto. En Visual Studio, seleccione **archivo > nuevo > proyecto > Visual C# > iPhone & iPad > aplicación iOS (Xamarin)** :

    ![La ventana nuevo proyecto, con la aplicación iOS (Xamarin) seleccionada](launch-screens-images/launch01.w157.png)

    Seleccione la plantilla **aplicación de vista única** y haga clic en **Aceptar**:

    ![Plantilla de aplicación de vista única](launch-screens-images/launch01-2.w157.png)

2. Si hay **recursos > LaunchScreen. Xib** en el **Explorador de soluciones**, elimínelo; para ello, haga clic con el botón derecho en el archivo y seleccione **eliminar**. Este archivo se reemplazará por un guion gráfico en el paso siguiente.

3. Cree un guion gráfico para usarlo como pantalla de inicio. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y elija **Agregar > nuevo elemento...** seguido de un **guion gráfico vacío**. Asigne a este guion gráfico el nombre **LaunchScreen. Storyboard** y haga clic en **Agregar**:

    ![La ventana Agregar nuevo elemento, con un guion gráfico vacío seleccionado](launch-screens-images/launch03.w157.png)

4. Configure el proyecto para usar **LaunchScreen. Storyboard** como su guión gráfico de la pantalla de Inicio:

    - Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición. 
    - En la pestaña **activos visuales** , establezca la **pantalla de inicio** en **LaunchScreen**.

    ![Selector de la pantalla de inicio en info. plist](launch-screens-images/launch04-vs.png)

5. Agregue una imagen a un catálogo de recursos en el proyecto para que esté disponible para su uso en la pantalla de Inicio:

    - En el **Explorador de soluciones**, haga clic con el botón derecho en catálogos de **recursos** y seleccione **Agregar catálogo de recursos**. Asigne a este nuevo activo el nombre **del catálogo de**recursos:

    ![La ventana Agregar nuevo elemento, con el catálogo de recursos seleccionado](launch-screens-images/launch05.w157.png)

    - Agregue un nuevo conjunto de imágenes al catálogo de recursos de **activos** , como se describe en la sección [Agregar imágenes a un conjunto de imágenes del catálogo](~/ios/app-fundamentals/images-icons/displaying-an-image.md) de recursos de la guía de [visualización](~/ios/app-fundamentals/images-icons/displaying-an-image.md) de imágenes.

6. Abra **LaunchScreen. Storyboard** para editarlo haciendo doble clic en él en el **Explorador de soluciones**.

    - Para editar un archivo de guion gráfico, Visual Studio necesita una conexión activa a un host de compilación de Mac. Consulte la guía [conexión con el equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obtener más información.

7. Elija un dispositivo y una orientación para obtener una vista previa del guión gráfico de la pantalla de inicio en el diseñador de iOS. Abra el panel de selección de dispositivos en la barra de herramientas inferior y seleccione **iPhone 4S** y **vertical**: 
 
    ![Barra de herramientas de selección de dispositivos](launch-screens-images/launch07-vs.png)

    - Tenga en cuenta que la selección de un dispositivo y la orientación solo cambia el modo en que iOS Designer muestra una vista previa del diseño. Independientemente de la selección realizada aquí, las restricciones recién agregadas se aplican a todos los dispositivos y orientaciones, a menos que se use el botón **Editar rasgos** para especificar lo contrario. 

8. Agregue un **controlador de vista** al guion gráfico arrastrando uno desde el **cuadro de herramientas** hasta la superficie de diseño: 

    ![Un controlador de vista vacío agregado a la superficie de diseño](launch-screens-images/launch08-vs.png)

9. Establecer el color de **fondo** de la vista principal del controlador de vista. Seleccione la vista haciendo clic en el centro del controlador de vista y ajuste el color de fondo mediante la **ventana Propiedades**:
    
    ![Una vista única con un color de fondo púrpura](launch-screens-images/launch09-vs.png)

10. Agregue una **vista de imagen** a la pantalla de inicio y establezca su **imagen**de origen:

    - Arrastre una **vista de imagen** desde el **cuadro de herramientas** hasta el centro de la vista.
    - Con la **vista imagen** todavía seleccionada, en la sección **Widget** de la **ventana Propiedades** , establezca la propiedad **Image** en el conjunto de imágenes ya agregado al catálogo de activos de **activos** . Cambie la posición y el tamaño de la **vista de imagen** según sea necesario:
    
    ![Una vista de imagen con su conjunto de propiedades de imagen](launch-screens-images/launch10-vs.png)

11. Agregue una **etiqueta** debajo de la **vista de imagen**:

    - Arrastre una **etiqueta** desde el **cuadro de herramientas** hasta la superficie de diseño, colóquela debajo de la **vista imagen**.
    - Establezca los atributos de la **etiqueta** mediante la **ventana Propiedades**:

    ![Una etiqueta con su conjunto de colores y texto](launch-screens-images/launch11-vs.png) 

12. Cambie al modo de edición de restricciones con el botón derecho de la **barra de herramientas restricciones**:
    
    ![Botón modo de edición de restricciones](launch-screens-images/launch12-vs.png) 

13. Agregar restricciones a la vista de **imagen**, establecer su alto y ancho y centrarla horizontal y verticalmente:

    ![Una vista de imagen con restricciones de diseño](launch-screens-images/launch13-vs.png) 

    - Para obtener información sobre cómo agregar restricciones, consulte [diseño automático con el Xamarin Designer para iOS](~/ios/user-interface/designer/designer-auto-layout.md).

14. Agregar restricciones a la **etiqueta**, centrarla horizontalmente, darle un alto y un ancho, y colocarla verticalmente a una distancia fija de la vista de **imagen**:
    
    ![Etiqueta con restricciones de diseño](launch-screens-images/launch14-vs.png) 

15. Pruebe otros dispositivos y orientaciones para comprobar que el diseño se parece a todos los escenarios. En los casos en los que es necesario realizar ajustes para un dispositivo o una orientación concretos, use el botón **Editar rasgos** para agregar restricciones a clases de tamaño específico:

    ![La pantalla de inicio se representa como un iPhone X con orientación horizontal](launch-screens-images/launch15-vs.png) 

16. Guarde los cambios en el guion gráfico. Ejecute la aplicación en un simulador o un dispositivo y la pantalla de inicio estará visible mientras se inicia la aplicación.

-----

> [!NOTE]
> Un guion gráfico utilizado como pantalla de inicio _debe_ incluir solo elementos de interfaz de usuario integrados y **no puede** realizar cálculos ni derivar de una clase personalizada.

Para obtener más información sobre cómo crear una pantalla de inicio con un guion gráfico unificado, consulte la sección [pantallas de inicio dinámico](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens) de la guía de [guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="migrating-to-launch-screen-storyboards"></a>Migración de guiones gráficos de la pantalla de inicio

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Al actualizar una aplicación existente para usar guiones gráficos para sus pantallas de inicio, haga clic con el botón derecho en el **nombre del proyecto** en el **Explorador de soluciones** y seleccione **Agregar** > **nuevo archivo..** .. Seleccione**pantalla de inicio** de **iOS** > y haga clic en el botón **nuevo** :

![](launch-screens-images/storyboard02.png "Selección de una pantalla de inicio de iOS")

A continuación, haga doble clic `Info.plist` en el archivo en el **Explorador de soluciones** para abrirlo para su edición. En **pantalla de inicio**, seleccione el nuevo archivo de guion gráfico creado anteriormente.

![](launch-screens-images/storyboard09.png "Seleccione el nuevo archivo de guion gráfico creado anteriormente")


Para usar el nuevo guión gráfico como una pantalla de inicio, haga lo siguiente:

1. Haga doble clic en `Info.plist` el archivo en el **Explorador de soluciones** para abrirlo para su edición.
2. Desplácese hasta la sección **imágenes de inicio universal** del editor, abra el menú desplegable de la **pantalla de inicio** y seleccione el nombre del guión gráfico creado anteriormente: 

    ![](launch-screens-images/storyboard08.png "Establecimiento de la pantalla de inicio en el guión gráfico")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Agregar** > **nuevo archivo..** .: 

    ![](launch-screens-images/image012.png "Agregar nuevo archivo")
2. Escriba un nombre para la pantalla de inicio y haga clic en el botón **Agregar** : 

    ![](launch-screens-images/image013.png "Escriba un nombre para la pantalla de inicio")
3. En el **Explorador de soluciones**, haga doble clic en el archivo de guion gráfico recién creado para abrirlo y editarlo.
4. Asegúrese de que la **clase size** esté establecida en **any: any** y la **vista como** sea **genérica**: 

    ![](launch-screens-images/image016.png "Asegúrese de que la clase de tamaño está establecida en any: any y la vista como es genérica")
5. Ensamble la pantalla de inicio de clases de tamaño, elementos de interfaz de `UIImageView`usuario simples (como) e imágenes que haya incluido en el paquete de la aplicación: 

    ![](launch-screens-images/image017.png "Ensamblado de la pantalla de inicio en el diseñador de iOS")
6. Guarde los cambios en el guion gráfico.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Pantallas de inicio dinámico (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [Conceptos básicos de iOS Designer](~/ios/user-interface/designer/index.md)
- [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [Diseño automático con el Xamarin Designer para iOS](~/ios/user-interface/designer/designer-auto-layout.md)
- [Directrices de la interfaz humana: Pantalla de inicio](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
