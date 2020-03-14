---
title: Diseño automático con el Xamarin Designer para iOS
description: En esta guía se presenta el diseño automático de iOS y se describe cómo usar la Xamarin Designer para iOS para crear y editar diseños mediante restricciones. También se explica cómo modificar las restricciones en el código, animar los cambios de restricción, etc.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 35a8d3aeb00ac73f944712cb31f913f98bd3b6e8
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306060"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Diseño automático con el Xamarin Designer para iOS

El diseño automático (también denominado "diseño adaptable") es un enfoque de diseño con capacidad de respuesta. A diferencia del sistema de diseño de transición, donde la ubicación de cada elemento está codificada de forma rígida en un punto de la pantalla, el diseño automático trata sobre las *relaciones* : las posiciones de los elementos en relación con otros elementos en la superficie de diseño. En el corazón del diseño automático está la idea de restricciones o reglas que definen la ubicación de un elemento o conjunto de elementos en el contexto de otros elementos de la pantalla. Dado que los elementos no están asociados a una posición concreta en la pantalla, las restricciones ayudan a crear un diseño adaptable que tenga un aspecto correcto en diferentes tamaños de pantalla y orientaciones de dispositivo.

En esta guía se presentan las restricciones y cómo trabajar con ellas en Xamarin iOS Designer. En esta guía no se trata el trabajo con restricciones mediante programación. Para obtener información sobre el uso del diseño automático mediante programación, consulte la [documentación de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

El Xamarin Designer para iOS está disponible en Visual Studio para Mac en Visual Studio 2017 y versiones posteriores en Windows.

En esta guía se da por supuesto que conoce los componentes del diseñador en la guía de [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md) .

## <a name="introduction-to-constraints"></a>Introducción a las restricciones

Una restricción es una representación matemática de la relación entre dos elementos de la pantalla. Representar una posición del elemento de la interfaz de usuario como una relación matemática resuelve varios problemas asociados con la codificación rígida de la ubicación de un elemento de la interfaz de usuario. Por ejemplo, si fuera a colocar un botón 20px desde la parte inferior de la pantalla en el modo vertical, la posición del botón quedaría fuera de la pantalla en modo horizontal. Para evitar esto, podríamos establecer una restricción que coloque el borde inferior del botón 20px desde la parte inferior de la vista. La posición del borde del botón se calcularía entonces como *Button. Bottom = View. Bottom-20px*, que colocaría el botón 20px desde la parte inferior de la vista en el modo vertical y horizontal. La capacidad de calcular la ubicación en función de una relación matemática es lo que hace que las restricciones resulten útiles en el diseño de la interfaz de usuario.

Cuando se establece una restricción, se crea un objeto `NSLayoutConstraint` que toma como argumentos los objetos que se van a restringir y las propiedades, o *atributos*, sobre los que actuará la restricción. En el diseñador de iOS, los atributos incluyen bordes como el *izquierdo*, *derecho*, *superior*e *inferior* de un elemento. También incluyen atributos de tamaño como el *alto* y el *ancho*, y la ubicación del punto central, *CenterX* y *CenterY*. Por ejemplo, cuando se agrega una restricción a la posición del límite izquierdo de dos botones, el diseñador genera el código siguiente en segundo plano:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

En la siguiente sección se explica cómo trabajar con restricciones mediante el diseñador de iOS, incluida la habilitación y deshabilitación del diseño automático y el uso de la barra de herramientas restricciones.

## <a name="enable-auto-layout"></a>Habilitar el diseño automático

La configuración predeterminada del diseñador de iOS tiene habilitado el modo de restricción. Sin embargo, si necesita habilitarla o deshabilitarla manualmente, puede hacerlo en dos pasos:

1. Haga clic en un espacio vacío en la superficie de diseño. Esto anula la selección de los elementos y muestra las propiedades del documento de guion gráfico.
1. Active o desactive la casilla **usar Autodiseño** en el panel de propiedades:

    ![](designer-auto-layout-images/image01.png "The Use Autolayout checkbox in the property panel")

De forma predeterminada, no se crea ninguna restricción ni está visible en la superficie. En su lugar, se deducen automáticamente a partir de la información del marco en tiempo de compilación. Para agregar restricciones, es necesario seleccionar un elemento en la superficie de diseño y agregarle restricciones. Podemos hacerlo mediante la barra de **herramientas de restricciones**.

## <a name="constraints-toolbar"></a>Barra de herramientas restricciones

 [![](designer-auto-layout-images/toolbarnew.png "The Context Menu Commands")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra de herramientas restricciones se ha actualizado y ahora consta de dos partes principales:

- **Botón de alternancia del modo de restricciones**: anteriormente, ha entrado en el modo de restricciones haciendo clic de nuevo en la vista seleccionada en la superficie de diseño. Ahora debería usar este botón de alternancia en la barra de restricciones:

  ![alternancia de modos de restricciones](designer-auto-layout-images/constraints.png)

- **Botón "actualizar restricciones":** Es importante tener en cuenta que los cambios dependen de si está en modo de edición de restricciones.
  - En el modo de edición de restricciones, este botón ajusta las restricciones para que coincidan con el marco del elemento.
  - En el modo de edición de fotogramas, este botón ajusta el marco del elemento para que coincida con la posición en la que se definen las restricciones.

## <a name="constraints-editing-popover"></a>Restricciones de edición de elemento flotante

La ventana emergente del editor de restricciones nos permite agregar y actualizar varias restricciones a la vez para una vista Select. Podemos crear varias restricciones de espaciado, relación de aspecto y alineación, como alinear una vista con los bordes izquierdos de dos vistas.

Para editar restricciones en la vista seleccionada, haga clic en los puntos suspensivos para mostrar la elemento flotante: ![restricciones de edición de elemento flotante](designer-auto-layout-images/constraints-popup.png)

Al abrir las restricciones elemento flotante, se muestran las restricciones predeterminadas en la vista. Podemos establecer todas las restricciones de espaciado seleccionando **todos los lados** del cuadro combinado en la esquina superior derecha y seleccionar **Borrar todo** para quitarlas.

La **W** establecerá width y **H** establecerá la restricción de alto. Al comprobar la **relación de aspecto**, el alto y el ancho de las vistas se controlarán en tamaños de pantalla diferentes, el ancho de la vista se utiliza como numerador para la ración y el alto como denominador.

![espaciado de restricciones](designer-auto-layout-images/constraints-spacing.png)

Cuatro cuadros combinados para restricciones de espaciado muestran las vistas vecinas para delimitar la restricción

## <a name="surface-based-constraint-editing"></a>Edición de restricciones basada en superficies

Para una edición de restricciones más ajustada, podemos interactuar con las restricciones directamente en la superficie de diseño. En esta sección se presentan los conceptos básicos de la edición de restricciones basada en superficies, incluidos los controles de espaciado de PIN, las áreas de colocación y el trabajo con distintos tipos de restricciones.

### <a name="creating-constraints"></a>Crear restricciones

La herramienta de diseñador de iOS ofrece dos tipos de controles para manipular los elementos en la superficie de diseño. *Arrastrar controles* y *controles de espaciado de anclaje*, tal como se muestra en la siguiente imagen:

![controles de vista](designer-auto-layout-images/controls.png)

Para ello, hay que seleccionar el botón modo de restricciones en la barra de restricciones.

Los 4 identificadores en forma de T de cada lado del elemento definen los bordes *superior*, *derecho*, *inferior*e *izquierdo* del elemento para una restricción. Los dos identificadores en forma de I de la derecha e inferior del elemento definen las restricciones de *alto* y *ancho* , respectivamente. El cuadrado intermedio controla las restricciones *CenterX* y *CenterY* .

Para crear una restricción, seleccione un identificador y arrástrelo a cualquier parte de la superficie de diseño. Al iniciar el arrastre, aparecerá una serie de líneas o cuadros verdes en la superficie que indica lo que puede restringir. Por ejemplo, en la siguiente captura de pantalla, se está restringiendo el lado superior del botón central:

 [![](designer-auto-layout-images/image07.png "Constraining the top side of the middle button")](designer-auto-layout-images/image07.png#lightbox)

Tenga en cuenta las tres líneas verdes de guiones en los otros dos botones. Las líneas verdes indican *áreas de colocación*o los atributos de otros elementos a los que se puede restringir. En la captura de pantalla anterior, los otros dos botones ofrecen 3 áreas de colocación verticales ( *inferior*, *central*y *superior*) para restringir nuestro botón. La línea verde discontinua en la parte superior de la vista significa que el controlador de vista ofrece una restricción en la parte superior de la vista, y el cuadro verde sólido significa que el controlador de vista ofrece una restricción debajo de la guía de diseño superior.

> [!IMPORTANT]
> Las guías de diseño son tipos especiales de destinos de restricción que nos permiten crear restricciones Top y bottom que tienen en cuenta la presencia de barras del sistema, como barras de estado o barras de herramientas. Uno de los principales usos es tener una aplicación compatible entre iOS 6 e iOS 7, ya que la versión más reciente tiene la vista de contenedor que se extiende por debajo de la barra de estado. Para obtener más información sobre la guía de diseño superior, consulte la [documentación de Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).

Las tres secciones siguientes presentan el trabajo con distintos tipos de restricciones.

### <a name="size-constraints"></a>Restricciones de tamaño

Con restricciones de tamaño: *alto* y *ancho* : tiene dos opciones. La primera opción es arrastrar el controlador para restringir el tamaño de un elemento vecino, como se muestra en el ejemplo anterior. La otra opción es hacer doble clic en el identificador para crear una autorestricción. Esto nos permite especificar un valor de tamaño constante, tal como se muestra en la siguiente captura de pantalla:

 [![](designer-auto-layout-images/sizec.png "Drag the handle to constrain to a neighbor element size, as illustrated here")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restricciones del centro

El identificador cuadrado creará una restricción *CenterX* o *CenterY* , dependiendo del contexto. Al arrastrar el identificador cuadrado, se iluminarán los demás elementos para ofrecer áreas de colocación verticales y horizontales, como se muestra en la siguiente captura de pantalla:

 [![](designer-auto-layout-images/centerc.png "Center Constraints")](designer-auto-layout-images/centerc.png#lightbox)

Si elige un área de colocación vertical, se creará una restricción de *Centro* . Si elige un área de colocación horizontal, la restricción se basará en *CenterX*.

### <a name="combinational-constraints"></a>Restricciones combinativas

Para crear restricciones de igualdad de tamaño y alineación entre dos elementos, puede seleccionar elementos de la barra de herramientas superior para especificar: en la alineación horizontal del orden, la alineación vertical y la igualdad de tamaño, tal como se muestra en la siguiente captura de pantalla:

 [![](designer-auto-layout-images/image06.png "Combinational Constraints")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualización y edición de restricciones

Al agregar una restricción, se mostrará en la superficie de diseño como una línea azul al seleccionar un elemento:

 [![](designer-auto-layout-images/image09.png "Visualizing Constraints")](designer-auto-layout-images/image09.png#lightbox)

Puede seleccionar una restricción haciendo clic en una línea azul y editando los valores de restricción directamente en el panel de propiedades. O bien, si hace doble clic en una línea azul, se abrirá un elemento flotante que le permite editar los valores directamente en la superficie de diseño:

 [![](designer-auto-layout-images/image08.png "Editing Constraints")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restricciones

Pueden surgir varios tipos de problemas al usar restricciones:

- **Restricciones en conflicto** : esto sucede si varias restricciones obligan a que el elemento tenga valores en conflicto para un atributo y el motor de restricciones no puede reconciliarlos.
- **Elementos con restricción** : las propiedades de un elemento (ubicación + tamaño) deben cubrirse completamente por su conjunto de restricciones y tamaños intrínsecos para que las restricciones sean válidas. Si estos valores son ambiguos, se dice que el elemento está subrestringido.
- Error de **colocación del marco** : esto se produce cuando el marco de un elemento y su conjunto de restricciones definen dos rectángulos resultantes diferentes.

En esta sección se explican los tres problemas mencionados anteriormente y se proporcionan detalles sobre cómo controlarlos.

### <a name="conflicting-constraints"></a>Restricciones en conflicto

Las restricciones en conflicto se marcan en rojo y tienen un símbolo de advertencia. Al mantener el puntero sobre los símbolos de advertencia se muestra un elemento flotante con información sobre el conflicto:

 [![](designer-auto-layout-images/image11.png "Conflicting Constraints warning")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementos con restricción

Los elementos subrestringidos aparecen en color naranja y desencadenan la apariencia de un icono de marcador naranja en la barra de objetos del controlador de vista:

 [![](designer-auto-layout-images/image02.png "Underconstrained items appear in orange")](designer-auto-layout-images/image02.png#lightbox)

Si hace clic en ese icono de marcador, puede obtener información sobre los elementos sublimitados de la escena y solucionar los problemas, ya sea por completo o quitando sus restricciones, como se muestra en la siguiente captura de pantalla:

 [![](designer-auto-layout-images/image10.png "Fixing Underconstrained Items")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Desubicación de fotogramas

La desubicación de fotogramas utiliza el mismo código de color que los elementos subrestringidos. El elemento siempre se representará en la superficie mediante su fotograma nativo, pero en el caso de una inposición de fotograma, un rectángulo rojo marcará el lugar en el que terminará el elemento cuando se ejecute la aplicación, como se muestra en la siguiente captura de pantalla:

 [![](designer-auto-layout-images/image05.png "Sample Frame Misplacement view")](designer-auto-layout-images/image05.png#lightbox)

Para resolver errores de colocación de fotogramas, seleccione el botón **Actualizar fotogramas en función** de las restricciones en la barra de herramientas de restricciones (botón secundario):

 [![](designer-auto-layout-images/image03.png "Update Frames based on Constraints toolbar button")](designer-auto-layout-images/image03.png#lightbox)

Esto ajustará automáticamente el marco del elemento para que coincida con las posiciones definidas por los controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificar restricciones en el código

En función de los requisitos de la aplicación, puede haber ocasiones en las que necesite modificar una restricción en el código. Por ejemplo, para cambiar el tamaño o la posición de la vista a la que está asociada una restricción, cambie la prioridad de una restricción o desactive una restricción.

Para tener acceso a una restricción en el código, primero debe exponerla en el diseñador de iOS haciendo lo siguiente:

1. Cree la restricción como normal (mediante cualquiera de los métodos enumerados anteriormente).
2. En el **Explorador de esquemas de documento**, busque la restricción deseada y selecciónela:

    [![](designer-auto-layout-images/modify01.png "The Document Outline Explorer")](designer-auto-layout-images/modify01.png#lightbox)
3. A continuación, asigne un **nombre** a la restricción en la pestaña **Widget** del **Explorador de propiedades**:

    [![](designer-auto-layout-images/modify02.png "The Widget Tab")](designer-auto-layout-images/modify02.png#lightbox)
4. Guarde los cambios.

Una vez realizados los cambios anteriores, puede tener acceso a la restricción en el código y modificar sus propiedades. Por ejemplo, puede utilizar lo siguiente para establecer el alto de la vista adjunta en cero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dado el siguiente valor para la restricción en el diseñador de iOS:

[![](designer-auto-layout-images/modify03.png "Editing a Constraint in the Property Explorer")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>El paso de diseño diferido

En lugar de actualizar de forma instantánea la vista adjunta en respuesta a los cambios de restricción, el motor de diseño automático programa un _paso de diseño diferido_ para un futuro próximo. Durante este paso aplazado, no solo se actualiza la restricción de la vista determinada, las restricciones de cada vista de la jerarquía se vuelven a calcular y actualizar para ajustarse al nuevo diseño.

En cualquier momento, puede programar su propio paso de diseño aplazado mediante una llamada a los métodos `SetNeedsLayout` o `SetNeedsUpdateConstraints` de la vista primaria.

El paso de diseño diferido consta de dos pasos únicos a través de la jerarquía de vistas:

- **El paso de actualización** : en este paso, el motor de diseño automático atraviesa la jerarquía de vistas e invoca el método `UpdateViewConstraints` en todos los controladores de vista y el método de `UpdateConstraints` en todas las vistas.
- **El paso de diseño** : de nuevo, el motor de diseño automático atraviesa la jerarquía de vistas, pero esta vez invoca el método `ViewWillLayoutSubviews` en todos los controladores de vista y el método `LayoutSubviews` en todas las vistas. El método `LayoutSubviews` actualiza la propiedad `Frame` de cada subvista con el rectángulo calculado por el motor de diseño automático.

### <a name="animating-constraint-changes"></a>Animar cambios de restricciones

Además de modificar las propiedades de restricción, puede usar la animación básica para animar los cambios en las restricciones de una vista. Por ejemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La clave aquí está llamando al método `LayoutIfNeeded` de la vista primaria dentro del bloque de animación. Esto indica a la vista que dibuje cada "fotograma" de la ubicación o el cambio de tamaño animados. Sin esta línea, la vista simplemente se ajustaría a la versión final sin animar.

## <a name="summary"></a>Resumen

En esta guía se ha introducido el diseño de iOS auto (o "adaptativo") y el concepto de restricciones como representaciones matemáticas de las relaciones entre los elementos de la superficie de diseño. Se describe cómo habilitar el diseño automático en el diseñador de iOS, trabajar con la **barra de herramientas de restricciones**y editar restricciones individualmente en la superficie de diseño. A continuación, se explica cómo solucionar tres problemas de restricciones comunes. Por último, se mostró cómo modificar restricciones en el código.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles que se diseñan en iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Información general de Android Designer](~/android/user-interface/android-designer/index.md)
- [Restricciones de programación](~/ios/user-interface/programmatic-layout-constraints.md)
