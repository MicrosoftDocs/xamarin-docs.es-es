---
title: Restricciones de diseño mediante programación en Xamarin. iOS
description: En esta guía se presenta el uso de restricciones de diseño automático de iOS en el código de C# en lugar de crearlas en el diseñador de iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3dd413d6d747c9bbec43e5a88f7e24b8e7868327
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602910"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Restricciones de diseño mediante programación en Xamarin. iOS

_En esta guía se presenta el uso de restricciones de diseño automático de iOS en el código de C# en lugar de crearlas en el diseñador de iOS._

El diseño automático (también denominado "diseño adaptable") es un enfoque de diseño con capacidad de respuesta. A diferencia del sistema de diseño de transición, donde la ubicación de cada elemento está codificada de forma rígida en un punto de la pantalla, el diseño automático trata sobre las *relaciones* : las posiciones de los elementos en relación con otros elementos en la superficie de diseño. En el corazón del diseño automático está la idea de restricciones o reglas que definen la ubicación de un elemento o conjunto de elementos en el contexto de otros elementos de la pantalla. Dado que los elementos no están asociados a una posición concreta en la pantalla, las restricciones ayudan a crear un diseño adaptable que tenga un aspecto correcto en diferentes tamaños de pantalla y orientaciones de dispositivo.

Normalmente, al trabajar con el diseño automático en iOS, usará los Interface Builder de Xcode para colocar de forma gráfica las restricciones de diseño en los elementos de la interfaz de usuario. Sin embargo, puede haber ocasiones en las que necesite crear y aplicar restricciones en el código de C#. Por ejemplo, cuando se utilizan elementos de interfaz de usuario creados dinámicamente y se agregan a `UIView` .

En esta guía se muestra cómo crear y trabajar con restricciones mediante código C# en lugar de crearlas de forma gráfica en la Interface Builder de Xcode.

<a name="Creating-Constraints-Programmatically"></a>

## <a name="creating-constraints-programmatically"></a>Crear restricciones mediante programación

Como se indicó anteriormente, normalmente trabajará con restricciones de diseño automático en el diseñador de iOS. En aquellas ocasiones en las que tenga que crear las restricciones mediante programación, tiene tres opciones para elegir:

- [Delimitadores de diseño](#Layout-Anchors) : esta API proporciona acceso a las propiedades de delimitador (como `TopAnchor` , `BottomAnchor` o `HeightAnchor` ) de los elementos de la interfaz de usuario que están restringidos.
- [Restricciones de diseño](#Layout-Constraints) : puede crear restricciones directamente mediante la `NSLayoutConstraint` clase.
- [Lenguaje de formato visual](#Visual-Format-Language) : proporciona un dibujo ASCII como método para definir restricciones.

En las secciones siguientes se detallan todas las opciones.

<a name="Layout-Anchors"></a>

### <a name="layout-anchors"></a>Delimitadores de diseño

Mediante el uso de la `NSLayoutAnchor` clase, tiene una interfaz fluida para crear restricciones basadas en las propiedades de delimitador de los elementos de la interfaz de usuario que se están restringiendo. Por ejemplo, las guías de diseño superior e inferior de un controlador de vista exponen las propiedades `TopAnchor` , `BottomAnchor` y `HeightAnchor` delimitador mientras una vista expone propiedades de borde, centro, tamaño y línea de base.

> [!IMPORTANT]
> Además del conjunto estándar de propiedades de delimitador, las vistas de iOS también incluyen las `LayoutMarginsGuides` `ReadableContentGuide` propiedades y. Estas propiedades exponen `UILayoutGuide` objetos para trabajar con los márgenes y guías de contenido legibles de la vista, respectivamente.

Los delimitadores de diseño proporcionan varios métodos para crear restricciones en un formato compacto fácil de leer:

- **ConstraintEqualTo** : define una relación en la que `first attribute = second attribute + [constant]` con un valor de desplazamiento proporcionado opcionalmente `constant` .
- **ConstraintGreaterThanOrEqualTo** : define una relación en la que `first attribute >= second attribute + [constant]` con un valor de desplazamiento proporcionado opcionalmente `constant` .
- **ConstraintLessThanOrEqualTo** : define una relación en la que `first attribute <= second attribute + [constant]` con un valor de desplazamiento proporcionado opcionalmente `constant` .

Por ejemplo:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Una restricción de diseño típica se puede expresar simplemente como una expresión lineal. Tome el ejemplo siguiente:

[![Una restricción de diseño expresada como una expresión lineal](programmatic-layout-constraints-images/graph01.png)](programmatic-layout-constraints-images/graph01.png#lightbox)

Que se convertiría en la siguiente línea de código de C# usando delimitadores de diseño:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Donde las partes del código de C# se corresponden con las partes especificadas de la ecuación de la manera siguiente:

|Ecuación|Código|
|---|---|
|Elemento 1|PurpleView|
|Atributo 1|LeadingAnchor|
|Relación|ConstraintEqualTo|
|Multiplicador|El valor predeterminado es 1,0, por lo que no se especifica|
|Item 2|OrangeView|
|Atributo 2|TrailingAnchor|
|Constante|10.0|

Además de proporcionar solo los parámetros necesarios para resolver una ecuación de restricción de diseño determinada, cada uno de los métodos de delimitador de diseño aplica la seguridad de tipos de los parámetros que se le pasan. Por tanto, los delimitadores de restricción horizontales como `LeadingAnchor` o `TrailingAnchor` solo se pueden utilizar con otros tipos de delimitadores horizontales y los multiplicadores solo se proporcionan a las restricciones de tamaño.

<a name="Layout-Constraints"></a>

### <a name="layout-constraints"></a>Restricciones de diseño

Puede agregar restricciones de diseño automático manualmente mediante la construcción directa `NSLayoutConstraint` de un en código de C#. A diferencia del uso de delimitadores de diseño, debe especificar un valor para cada parámetro, incluso si no tiene ningún efecto en la restricción que se está definiendo. Como resultado, terminará produciendo una cantidad considerable de código difícil de leer y reutilizable. Por ejemplo:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Donde la `NSLayoutAttribute` enumeración define el valor de los márgenes de la vista y corresponde a las `LayoutMarginsGuide` propiedades como, y, `Left` `Right` `Top` `Bottom` y la `NSLayoutRelation` enumeración define la relación que se creará entre los atributos especificados como `Equal` , `LessThanOrEqual` o `GreaterThanOrEqual` .

A diferencia de la API de delimitador de diseño, los `NSLayoutConstraint` métodos de creación no resaltan los aspectos importantes de una restricción determinada y no se realizan comprobaciones en tiempo de compilación en la restricción. Como resultado, es fácil construir una restricción no válida que producirá una excepción en tiempo de ejecución.

<a name="Visual-Format-Language"></a>

### <a name="visual-format-language"></a>Lenguaje de formato visual

El lenguaje de formato visual permite definir restricciones mediante ilustraciones ASCII como cadenas que proporcionan una representación visual de la restricción que se va a crear. Esto tiene las siguientes ventajas y desventajas:

- El lenguaje de formato visual solo exige la creación de restricciones válidas.
- El diseño automático genera restricciones en la consola mediante el lenguaje de formato visual para que los mensajes de depuración se parezcan al código que se usa para crear la restricción.
- El lenguaje de formato visual le permite crear varias restricciones al mismo tiempo con una expresión muy compacta.
- Dado que no hay ninguna validación en el lado de compilación de las cadenas del lenguaje de formato visual, solo se pueden detectar problemas en tiempo de ejecución.
- Dado que el lenguaje de formato visual destaca la visualización sobre la integridad, no se pueden crear tipos de restricción con él (por ejemplo, las proporciones).

Siga los pasos que se indican a continuación al usar el lenguaje de formato visual para crear una restricción:

1. Cree un `NSDictionary` objeto que contenga los objetos de vista y guías de diseño y una clave de cadena que se utilizará al definir los formatos.
2. Opcionalmente, cree un `NSDictionary` que defina un conjunto de claves y valores ( `NSNumber` ) usados como el valor constante para la restricción.
3. Cree la cadena de formato para diseñar una sola columna o fila de elementos.
4. Llame al `FromVisualFormat` método de la `NSLayoutConstraint` clase para generar las restricciones.
5. Llame al `ActivateConstraints` método de la `NSLayoutConstraint` clase para activar y aplicar las restricciones.

Por ejemplo, para crear una restricción inicial y una final en el lenguaje de formato visual, podría usar lo siguiente:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Dado que el lenguaje de formato visual siempre crea restricciones de cero puntos adjuntados a los márgenes de la vista primaria al usar el espaciado predeterminado, este código genera resultados idénticos a los ejemplos presentados anteriormente.

En el caso de diseños de interfaz de usuario más complejos, como varias vistas secundarias en una sola línea, el lenguaje de formato visual especifica el espaciado horizontal y la alineación vertical. Como en el ejemplo anterior, donde especifica, `AlignAllTop` `NSLayoutFormatOptions` alinea todas las vistas de una fila o columna con su parte superior.

Vea el apéndice sobre el [lenguaje visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) de Apple para ver algunos ejemplos de cómo especificar restricciones comunes y la gramática de cadena de formato visual.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En esta guía se ha presentado la creación y el trabajo con restricciones de diseño automático en C# en lugar de crearlas gráficamente en el diseñador de iOS. En primer lugar, se examinó el uso de delimitadores de diseño ( `NSLayoutAnchor` ) para controlar el diseño automático. A continuación, se mostró cómo trabajar con restricciones de diseño ( `NSLayoutConstraint` ). Por último, se presentó mediante el lenguaje de formato visual para el diseño automático.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles que se diseñan en iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Diseño automático con el Xamarin Designer para iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple: crear restricciones mediante programación](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple: Apéndice de idioma de formato visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
