---
title: Generación de código. Xib en Xamarin. iOS
description: En este documento se describe cómo Xamarin. iOS genera código para asignar archivos. C#Xib a, haciendo que los controles visuales sean accesibles mediante programación.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 280802adbb5326854b4d47045bbb1569dd123f30
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527330"
---
# <a name="xib-code-generation-in-xamarinios"></a>Generación de código. Xib en Xamarin. iOS

> [!IMPORTANT]
>  En este documento se explica la integración de Visual Studio para Mac solo con Interface Builder de Xcode, ya que las acciones y salidas no se usan en el Xamarin Designer para iOS. Para obtener más información sobre el diseñador de iOS, revise el documento del [Diseñador de iOS](~/ios/user-interface/designer/index.md) .

La herramienta Apple Interface Builder ("IB") se puede usar para diseñar interfaces de usuario visualmente. Las definiciones de interfaz creadas por IB se guardan en archivos **. Xib** . A los widgets y otros objetos de archivos **. Xib** se les puede asignar una "identidad de clase", que puede ser un tipo personalizado definido por el usuario. Esto le permite personalizar el comportamiento de los widgets y escribir widgets personalizados.

Estas clases de usuario son normalmente subclases de clases de controlador de interfaz de usuario. Tienen *salidas* (análogas a las propiedades) y *acciones* (análogas a los eventos) que se pueden conectar a objetos de interfaz. En tiempo de ejecución, cuando se carga el archivo IB, se crean los objetos y las salidas y acciones se conectan a los distintos objetos de la interfaz de usuario de forma dinámica. Al definir estas clases administradas, debe definir todas las acciones y salidas para que coincidan con las que IB espera. Visual Studio para Mac usa un modelo de tipo CodeBehind para simplificar esto. Esto es similar a lo que hace Xcode para Objective-C, pero el modelo de generación de código y las convenciones se han ajustado para que los desarrolladores de .NET les resulten más familiares.

Actualmente no se admite el uso de archivos **. Xib** en Xamarin. iOS para Visual Studio.

## <a name="xib-files-and-custom-classes"></a>Archivos. Xib y clases personalizadas

Además de usar los tipos existentes de la entrada táctil de cacao, es posible definir tipos personalizados en archivos **. Xib** . También es posible usar tipos que se definen en otros archivos **. Xib** o que se definen exclusivamente en C# el código. Actualmente, Interface Builder no es consciente de los detalles de los tipos definidos fuera del archivo **. Xib** actual, por lo que no se mostrarán ni se mostrarán sus salidas y acciones personalizadas. La eliminación de esta limitación se planea en algún momento en el futuro.

Las clases personalizadas se pueden definir en un archivo **. Xib** mediante el comando "agregar subclase" de la pestaña "clases" de Interface Builder. Nos referimos a ellas como clases "CodeBehind". Si el archivo **. Xib** tiene un archivo homólogo ". Xib.Designer.cs" en el proyecto, Visual Studio para Mac lo rellenará automáticamente con definiciones de clases parciales para todas las clases personalizadas de **. Xib**. Hacemos referencia a estas clases parciales como "clases de diseñador".

## <a name="generating-code"></a>Generar código

Para cualquier  **{0}archivo. Xib** con una acción de compilación de la *Página*, si también existe un  **{0}archivo. Xib.Designer.CS** en el proyecto, Visual Studio para Mac generará clases parciales en el archivo de diseñador para todas las clases de usuario que puede encontrar en el archivo **. Xib** , con las propiedades de las salidas y métodos parciales para todas las acciones. La generación de código se habilita simplemente por la presencia de este archivo.

El archivo del diseñador se actualiza automáticamente cuando cambia el archivo **. Xib** y Visual Studio para Mac vuelve a obtener el foco. El archivo del diseñador no debe modificarse manualmente, ya que los cambios se sobrescribirán la próxima vez que Visual Studio para Mac actualice el archivo.

## <a name="registration-and-namespaces"></a>Registro y espacios de nombres

Visual Studio para Mac genera las clases de diseñador mediante el espacio de nombres predeterminado del proyecto para la ubicación del archivo de diseñador, para que sea coherente con la namespacing normal del proyecto .NET. El espacio de nombres de los archivos del diseñador está controlado por el "espacio de nombres predeterminado" del proyecto y sus opciones de "directivas de nomenclatura de .NET". Tenga cuidado de que si cambia el espacio de nombres predeterminado del proyecto, MD volverá a generar las clases en el nuevo espacio de nombres, por lo que es posible que las clases parciales ya no coincidan.

Para hacer que la clase sea reconocible por el tiempo de ejecución de Objective-C, `[Register (name)]` Visual Studio para Mac aplica un atributo a la clase. Aunque Xamarin. iOS registra `NSObject`automáticamente las clases derivadas de, usa los nombres completos de .net. El atributo aplicado por Visual Studio para Mac invalida esto para asegurarse de que cada clase se registra con el nombre usado en el archivo **. Xib** . Si usa clases personalizadas en IB sin usar Visual Studio para Mac para generar archivos de diseñador, puede que tenga que aplicar esto manualmente para que las clases administradas coincidan con los nombres de clase de Objective-C esperados.

Las clases no se pueden definir en más de un **. Xib**o entrarán en conflicto.

## <a name="non-designer-class-parts"></a>Partes de clase que no son de diseñador

Las clases parciales de diseñador no están diseñadas para usarse tal cual. Las salidas son privadas y no se especifica ninguna clase base. Se espera que cada clase tenga una parte de clase "sin diseñador" correspondiente en otro archivo, que establece la clase base, utiliza o expone las salidas y define constructores que son necesarios para crear una instancia de la clase a partir de código nativo al cargar el archivo **. Xib** . Las plantillas default **. Xib** hacen esto, pero para las clases personalizadas adicionales que defina en un **. Xib**, debe agregar manualmente la parte que no sea del diseñador.

La razón de esto es la necesidad de flexibilidad. Por ejemplo, varias clases de código subyacente podrían subclaser una clase abstracta administrada común, que subclase la clase a la que se va a crear una subclase de IB.

Es convencional colocarlos en un  **{0}archivo. Xib.CS** junto al archivo de  **{0}diseñador. Xib.Designer.CS** .

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Acciones y salidas generadas

En las clases de diseñador parciales, Visual Studio para Mac genera propiedades correspondientes a las salidas conectadas definidas en IB y métodos parciales correspondientes a cualquier acción conectada.

### <a name="outlet-properties"></a>Propiedades de la toma

Las clases de diseñador contienen propiedades correspondientes a todas las salidas definidas en la clase personalizada. El hecho de que estas son propiedades es un detalle de implementación del puente Xamarin. iOS a Objective C para habilitar el enlace diferido. Debe considerarlos equivalentes a los campos privados, pensados para usarse únicamente desde la clase CodeBehind. Si desea que sean públicos, agregue propiedades de descriptor de acceso a la parte de clase que no sea de diseñador, como haría con cualquier otro campo privado.

Si las propiedades de la toma se definen para tener `id` un tipo de `NSObject`(equivalente a), el generador de código del diseñador determina actualmente el tipo más fuerte posible basado en los objetos conectados a esa salida, por comodidad.
Sin embargo, es posible que no se admita en versiones futuras, por lo que se recomienda que escriba explícitamente las salidas al definir la clase personalizada.

### <a name="action-properties"></a>Propiedades de acción

Las clases de diseñador contienen métodos parciales correspondientes a todas las acciones definidas en la clase personalizada. Son métodos sin una implementación de. El propósito de los métodos parciales es doble:

1. Si escribe `partial` en el cuerpo de clase de la parte de clase que no es de diseñador, Visual Studio para Mac ofrecerá para Autocompletar las firmas de todos los métodos parciales no implementados.
2. Las firmas de método parcial tienen un atributo aplicado que los expone en el mundo de Objective-C, por lo que se pueden administrar como la acción correspondiente.


Si lo desea, puede omitir el método parcial e implementar la acción aplicando el atributo a un método diferente o dejar que pase a una clase base.

Si se definen las acciones para que tengan un tipo de `id` remitente (equivalente `NSObject`a), el generador de código del diseñador determina actualmente el tipo más fuerte posible basado en los objetos conectados a esa acción. Sin embargo, es posible que no se admita en versiones futuras, por lo que se recomienda que escriba explícitamente las acciones al definir la clase personalizada.

Tenga en cuenta que estos métodos parciales se crean C#solo para, porque CodeDom no admite métodos parciales, por lo que no se generan para otros lenguajes.

## <a name="cross-xib-class-usage"></a>Uso de clases Cross-XIB

A veces, los usuarios quieren hacer referencia a la misma clase desde varios archivos **. Xib** , por ejemplo con controladores de pestañas. Esto se puede hacer haciendo referencia explícitamente a la definición de clase desde otro archivo **. Xib** o definiendo de nuevo el mismo nombre de clase en el segundo **. Xib**.

Este último caso puede ser problemático debido a Visual Studio para Mac procesar archivos **. Xib** de forma individual. No puede detectar ni fusionar automáticamente las definiciones duplicadas, por lo que puede acabar con conflictos al aplicar el atributo Register varias veces cuando se define la misma clase parcial en varios archivos de diseñador. Las versiones recientes de Visual Studio para Mac intentar resolver esto, pero puede que no siempre funcione según lo esperado. En el futuro, es probable que esto deje de ser compatible y, en su lugar, Visual Studio para Mac hará que todos los tipos definidos en todos los archivos **. Xib** y el código administrado del proyecto estén directamente visibles desde todos los archivos **. Xib** .

## <a name="type-resolution"></a>Resolución de tipos

Los tipos que se usan en IB son nombres de tipo Objective-C. Estos se asignan a los tipos de CLR mediante el uso de atributos de registro. Al generar el código de salida y de acción, Visual Studio para Mac resolverá los tipos de CLR correspondientes para todos los tipos de Objective-C contenidos en el núcleo de Xamarin. iOS y calificará totalmente sus nombres de tipo.

Sin embargo, el generador de código no puede resolver actualmente los tipos CLR a partir de nombres de tipo de Objective-C en bibliotecas o código de usuario, por lo que en estos casos se genera el nombre de tipo literalmente. Esto significa que el tipo de CLR correspondiente debe tener el mismo nombre que el tipo de Objective-C y estar en el mismo espacio de nombres que el código que lo está usando. Se prevé que se solucione en algún momento en el futuro si se tienen en cuenta todos los tipos de Objective-C del proyecto durante la generación de código.
