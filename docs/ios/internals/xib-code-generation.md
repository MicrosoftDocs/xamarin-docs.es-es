---
title: Generación de código. Xib en Xamarin. iOS
description: En este documento se describe cómo Xamarin. iOS genera código para asignar archivos ". XIB" a C#, haciendo que los controles visuales sean accesibles mediante programación.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 7e6f0cdab812c65dde1305dbb3d17a7ccdc5c2e1
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603105"
---
# <a name="xib-code-generation-in-xamarinios"></a>Generación de código. Xib en Xamarin. iOS

La herramienta Apple Interface Builder ("IB") se puede usar para diseñar interfaces de usuario visualmente. Las definiciones de interfaz creadas por IB se guardan en archivos **. Xib** . A los widgets y otros objetos de archivos **. Xib** se les puede asignar una "identidad de clase", que puede ser un tipo personalizado definido por el usuario. El uso de tipos personalizados le permite personalizar el comportamiento de los widgets y escribir widgets personalizados.

Estas clases de usuario son normalmente subclases de clases de controlador de interfaz de usuario. Tienen *salidas* (propiedades) y *acciones* (eventos) que se pueden conectar a objetos de interfaz.
En tiempo de ejecución, se carga la IB. En ese momento, se crean los objetos y las tomas y las acciones se conectan de forma dinámica a los distintos objetos de la interfaz de usuario. Al definir estas clases administradas, debe definir todas las acciones y salidas para que coincidan con las que IB espera. Visual Studio para Mac usa un modelo de tipo CodeBehind para simplificar el código. Xcode tiene un modelo de Objective-C similar. Pero el modelo y las convenciones de generación de código de Xamarin. iOS se han reparado para que los desarrolladores de .NET les resulten más familiares.

## <a name="xib-files-and-custom-classes"></a>Archivos. Xib y clases personalizadas

Es posible definir tipos personalizados en archivos **. Xib** , además de usar los tipos existentes de la entrada táctil de cacao. También es posible usar tipos que se definen en otros archivos **. Xib** o que se definen únicamente en código de C#. Actualmente, Interface Builder no es consciente de los detalles de los tipos definidos fuera del archivo **. Xib** actual, por lo que no los enumerará ni mostrará sus salidas y acciones personalizadas. La eliminación de esta limitación se planea en algún momento en el futuro.

Las clases personalizadas se pueden definir en un archivo **. Xib** mediante el comando "agregar subclase" de la pestaña "clases" de Interface Builder. Hacemos referencia a esas clases como clases "CodeBehind". Si el archivo **. Xib** tiene un archivo homólogo ". Xib.Designer.cs" en el proyecto, Visual Studio para Mac lo rellenará automáticamente con definiciones de clases parciales para todas las clases personalizadas de **. Xib**. Hacemos referencia a estas clases parciales como "clases de diseñador".

## <a name="generating-code"></a>Generar código

La generación de código está habilitada por la presencia de un archivo **{0} . Xib.Designer.CS** , para cualquier archivo **{0} . Xib** con una acción de compilación de la *Página*.
Visual Studio para Mac genera clases parciales en el archivo de diseñador para todas las clases de usuario que puede encontrar en el archivo **. Xib** . Visual Studio para Mac genera propiedades para las salidas y métodos parciales para las acciones. 

El archivo del diseñador se actualiza automáticamente cuando cambia el archivo **. Xib** y Visual Studio para Mac vuelve a obtener el foco. No se recomienda realizar cambios en el archivo de diseñador, ya que los cambios se sobrescribirán la próxima vez que Visual Studio para Mac actualice el archivo.

## <a name="registration-and-namespaces"></a>Registro y espacios de nombres

Visual Studio para Mac genera las clases de diseñador utilizando el espacio de nombres predeterminado del proyecto para la ubicación del archivo de diseñador.
Este comportamiento es coherente con la generación de espacio de nombres del proyecto .NET normal.
El espacio de nombres de los archivos del diseñador usa la configuración del proyecto "espacio de nombres predeterminado" y sus "directivas de nomenclatura de .NET". Si el espacio de nombres predeterminado del proyecto cambia, las clases regeneradas usarán el nuevo espacio de nombres.
Después de la regeneración, es posible que las clases parciales ya no coincidan.

Para hacer que la clase sea reconocible por el tiempo de ejecución de Objective-C, Visual Studio para Mac aplica un `[Register (name)]` atributo a la clase. Aunque Xamarin. iOS registra automáticamente `NSObject` las clases derivadas de, usa los nombres completos de .net. El atributo aplicado por Visual Studio para Mac invalida el comportamiento de Xamarin. iOS para asegurarse de que cada clase se registra con el nombre usado en el archivo **. Xib** . Agregue el atributo manualmente para todas las clases personalizadas definidas mediante IB, sin usar Visual Studio para Mac para generar archivos de diseñador. Esto hace que las clases administradas coincidan con los nombres de clase de Objective-C esperados.

Las clases no se pueden definir en más de un **. Xib** o entrarán en conflicto.

## <a name="non-designer-class-parts"></a>Partes de clase que no son de diseñador

Las clases parciales de diseñador no están diseñadas para usarse tal cual. Las salidas son privadas y no se especifica ninguna clase base. Se espera que cada clase tenga una parte de clase "sin diseñador" correspondiente en otro archivo. El archivo "sin diseñador" establece la clase base, manipula las salidas y define constructores que son necesarios para crear instancias de la clase a partir de código nativo. Las plantillas default **. Xib** tienen las partes de clase "sin diseñador", pero para las demás clases personalizadas que defina en un **. Xib**, debe agregar manualmente la parte que no sea del diseñador.

Esta separación mediante clases parciales es necesaria para la flexibilidad. Por ejemplo, varias clases de código subyacente podrían subclaser una clase abstracta administrada común, que subclase la clase a la que se va a crear una subclase de IB.

Es convencional colocar las clases CodeBehind en un archivo **{0} . Xib.CS** al lado del archivo **{0} . Xib.Designer.CS** Designer.

<a name="generated"></a>

## <a name="generated-actions-and-outlets"></a>Acciones y salidas generadas

En las clases de diseñador parciales, Visual Studio para Mac genera propiedades correspondientes a las salidas conectadas definidas en IB y métodos parciales correspondientes a cualquier acción conectada.

### <a name="outlet-properties"></a>Propiedades de la toma

Las clases de diseñador contienen propiedades correspondientes a todas las salidas definidas en la clase personalizada. Estas propiedades habilitan el enlace diferido. Son un detalle de implementación del puente Xamarin. iOS a Objective C. Piense en ellos como equivalentes a los campos privados, pensado para usarse únicamente desde la clase CodeBehind. Haga público el campo agregando el descriptor de acceso público al campo en la parte de clase que no es de diseñador.

Si las propiedades de la toma se definen para tener un tipo de `id` (equivalente a `NSObject` ), el generador de código del diseñador determina actualmente el tipo más fuerte posible basado en los objetos conectados a esa salida, por comodidad.
Sin embargo, es posible que este comportamiento no se admita en versiones futuras. Se recomienda que escriba explícitamente las salidas al definir la clase personalizada.

### <a name="action-properties"></a>Propiedades de acción

Las clases de diseñador contienen métodos parciales correspondientes a todas las acciones definidas en la clase personalizada. Estos métodos no tienen una implementación de. El propósito de los métodos parciales es doble:

1. Si escribe  `partial` en el cuerpo de clase de la parte de clase que no es de diseñador, Visual Studio para Mac ofrecerá para Autocompletar las firmas de todos los métodos parciales no implementados.
2. Las firmas de método parcial tienen un atributo aplicado que los expone en el mundo de Objective-C, por lo que se pueden administrar como la acción correspondiente.

Puede omitir el método parcial e implementar la acción aplicando el atributo a un método diferente. O dejar que pasen a una clase base.

Si se definen las acciones para que tengan un tipo de remitente `id` (equivalente a `NSObject` ), el generador de código del diseñador determina actualmente el tipo más fuerte posible basado en los objetos conectados a esa acción. Sin embargo, es posible que este comportamiento no se admita en versiones futuras. Se recomienda que escriba explícitamente las acciones al definir la clase personalizada.

Estos métodos parciales se crean solo para C#, porque CodeDOM no admite métodos parciales. No se generan para otros lenguajes.

## <a name="cross-xib-class-usage"></a>Uso de clases Cross-XIB

A veces, los usuarios quieren hacer referencia a la misma clase desde varios archivos **. Xib** , por ejemplo con controladores de pestañas. Puede hacer referencia explícitamente a la definición de clase desde otro archivo **. Xib** o definir el mismo nombre de clase de nuevo en el segundo **. Xib**.

Este último caso puede ser problemático debido a Visual Studio para Mac procesar archivos **. Xib** de forma individual. Visual Studio para Mac no puede detectar ni combinar definiciones duplicadas. Puede acabar con conflictos al aplicar el atributo Register varias veces cuando se define la misma clase parcial en varios archivos de diseñador. Las versiones recientes de Visual Studio para Mac intentar resolver los conflictos, pero puede que no siempre funcione según lo esperado. En el futuro, es probable que este comportamiento deje de ser compatible y, en su lugar, Visual Studio para Mac hará que todos los tipos definidos en todos los archivos **. Xib** y el código administrado del proyecto estén directamente visibles en todos los archivos **. Xib** .

## <a name="type-resolution"></a>Resolución de tipos

Los tipos que se usan en IB son nombres de tipo de Objective-C asignados a tipos CLR mediante el uso de atributos de registro. Al generar código, Visual Studio para Mac resolverá los tipos de CLR, calificando por completo los nombres de tipo para los tipos de Objective-C. Estos tipos de Objective-C se incluyen en el núcleo de Xamarin. iOS.

El generador de código no puede resolver actualmente los tipos CLR a partir de nombres de tipo de Objective-C en bibliotecas o código de usuario. En tales casos, genera el nombre de tipo literalmente. Debe tener el mismo nombre que el tipo de Objective-C para resolver correctamente el tipo CLR.
El tipo CLR debe estar en el mismo espacio de nombres que el código que lo está usando. Las versiones futuras del generador de código considerarán todos los tipos de Objective-C del proyecto.
