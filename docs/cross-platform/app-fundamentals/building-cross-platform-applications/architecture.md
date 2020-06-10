---
title: 'Parte 2: Arquitectura'
description: En este documento se describen los patrones de arquitectura útiles para la creación de aplicaciones multiplataforma. Se describen las capas de aplicación típicas (capa de datos, capa de acceso a datos, etc.) y patrones comunes de software móvil (MVVM, MVC, etc.).
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 315defe5017e3744013d1babd35f06deed255946
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566336"
---
# <a name="part-2---architecture"></a>Parte 2: Arquitectura

Un principio clave de la creación de aplicaciones multiplataforma es crear una arquitectura que se presta a una maximización del uso compartido de código entre plataformas. La continuación de los siguientes principios de programación orientada a objetos ayuda a compilar una aplicación bien diseñada:

- **Encapsulación** : Asegúrese de que las clases e incluso los niveles arquitectónicos solo exponen una API mínima que realiza sus funciones necesarias y oculta los detalles de implementación. En un nivel de clase, esto significa que los objetos se comportan como "cuadros negros" y que el código que lo consume no necesita saber cómo llevan a cabo sus tareas. En un nivel arquitectónico, significa implementar patrones como la fachada que fomentan una API simplificada que organiza interacciones más complejas en nombre del código en capas más abstractas. Esto significa que el código de la interfaz de usuario (por ejemplo) solo debe ser responsable de mostrar pantallas y aceptar la entrada del usuario; y nunca interactúan directamente con la base de datos. Del mismo modo, el código de acceso a datos solo debe leer y escribir en la base de datos, pero nunca interactuar directamente con botones o etiquetas.
- **Separación de responsabilidades** : Asegúrese de que cada componente (tanto en la arquitectura como en el nivel de clase) tenga un propósito claro y bien definido. Cada componente debe realizar solo sus tareas definidas y exponer esa funcionalidad a través de una API que sea accesible a las demás clases que necesiten usarla.
- **Polimorfismo** : la programación en una interfaz (o clase abstracta) que admite varias implementaciones significa que se puede escribir y compartir código principal entre plataformas, a la vez que se interactúa con características específicas de la plataforma.

El resultado natural es una aplicación modelada después de entidades reales o abstractas con capas lógicas independientes. La separación del código en las capas hace que las aplicaciones sean más fáciles de entender, probar y mantener. Se recomienda que el código de cada capa sea físicamente independiente (ya sea en directorios o incluso en proyectos independientes para aplicaciones muy grandes), así como de forma lógica independiente (mediante espacios de nombres).

 <a name="Typical_Application_Layers"></a>

## <a name="typical-application-layers"></a>Capas de aplicación típicas

En este documento y en los casos prácticos, hacemos referencia a los seis niveles de aplicación siguientes:

- **Capa de datos** : persistencia de datos no volátil, que probablemente sea una base de datos de SQLite, pero que podría implementarse con archivos XML o con cualquier otro mecanismo adecuado.
- **Capa de acceso a datos** : contenedor en torno a la capa de datos que proporciona acceso de creación, lectura, actualización y eliminación (CRUD) a los datos sin exponer los detalles de implementación al llamador. Por ejemplo, la capa DAL puede contener instrucciones SQL para consultar o actualizar los datos, pero no es necesario que el código de referencia lo sepa.
- **Nivel de negocio** : (a veces denominado capa de lógica de negocios o BLL) contiene definiciones de entidades de negocio (el modelo) y lógica de negocios. Patrón de fachada para empresas.
- **Nivel de acceso al servicio** : se usa para acceder a los servicios en la nube: desde servicios web complejos (REST, JSON, WCF) hasta la recuperación simple de datos e imágenes de servidores remotos. Encapsula el comportamiento de red y proporciona una API sencilla que las capas de aplicación y de interfaz de usuario usarán.
- **Nivel de aplicación** : código que suele ser específico de la plataforma (no suele compartirse entre plataformas) o el código específico de la aplicación (no suele ser reutilizable). Una buena prueba de si el código debe colocarse en el nivel de aplicación frente a la capa de la interfaz de usuario (a) para determinar si la clase tiene algún control de pantalla real o (b) si se podría compartir entre varias pantallas o dispositivos (por ejemplo, iPhone y iPad).
- **Capa de la interfaz de usuario (IU)** : la capa orientada al usuario, contiene pantallas, widgets y controladores que los administran.

Es posible que una aplicación no contenga necesariamente todas las capas; por ejemplo, la capa de acceso de servicio no existirá en una aplicación que no tenga acceso a los recursos de red. Una aplicación muy sencilla podría combinar la capa de datos y el nivel de acceso a datos porque las operaciones son extremadamente básicas.

 <a name="Common_Mobile_Software_Patterns"></a>

## <a name="common-mobile-software-patterns"></a>Patrones comunes de software móvil

Los patrones son una manera establecida de capturar soluciones recurrentes a problemas comunes. Hay algunos patrones clave que resultan útiles para entenderse en la creación de aplicaciones móviles que se pueden mantener y comprender.

- **Model, View, ViewModel (MVVM)** : el patrón Model-View-ViewModel es popular con los marcos de trabajo que admiten el enlace de datos, como Xamarin. Forms. Los SDK habilitados para XAML, como Windows Presentation Foundation (WPF) y Silverlight, lo han utilizado. donde el ViewModel actúa como un paso entre los datos (modelo) y la interfaz de usuario (vista) mediante el enlace de datos y los comandos.
- **Modelo, vista, controlador (MVC)** : un patrón común y a menudo no entendido, MVC se usa con más frecuencia al compilar interfaces de usuario y proporciona una separación entre la definición real de una pantalla (vista) de la interfaz de usuario, el motor subyacente que controla la interacción (controlador) y los datos que la rellenan (modelo). En realidad, el modelo es una pieza completamente opcional y, por lo tanto, el núcleo de comprensión de este patrón se encuentra en la vista y el controlador. MVC es un enfoque popular para las aplicaciones de iOS.
- **Fachada empresarial** : el patrón de administrador de aka proporciona un punto de entrada simplificado para el trabajo complejo. Por ejemplo, en una aplicación de seguimiento de tareas, puede tener una `TaskManager` clase con métodos como `GetAllTasks()` , `GetTask(taskID)` , `SaveTask (task)` , etc. La `TaskManager` clase proporciona una fachada al funcionamiento interno de guardar o recuperar objetos de tareas.
- **Singleton** : el patrón singleton proporciona una manera en la que solo puede existir una única instancia de un objeto determinado. Por ejemplo, al usar SQLite en aplicaciones móviles, solo desea una instancia de la base de datos. Usar el patrón singleton es una manera sencilla de asegurarse de esto.
- **Proveedor** : modelo acuñó por Microsoft (es decir, similar a la estrategia o inserción de dependencias básica) para fomentar la reutilización de código en aplicaciones de Silverlight, WPF y WinForms. El código compartido se puede escribir en una interfaz o una clase abstracta, y las implementaciones concretas específicas de la plataforma se escriben y se pasan cuando se usa el código.
- **Async** : no se debe confundir con la palabra clave Async, el patrón Async se usa cuando es necesario ejecutar el trabajo de ejecución prolongada sin mantener la interfaz de usuario o el procesamiento actual. En su forma más sencilla, el patrón Async simplemente describe que las tareas de ejecución prolongada deben iniciarse en otro subproceso (o en una abstracción de subproceso similar, como una tarea) mientras el subproceso actual continúa procesándose y escucha una respuesta del proceso en segundo plano y, a continuación, actualiza la interfaz de usuario cuando se devuelven los datos y el estado.

Cada uno de los patrones se examinará con más detalle, ya que su uso práctico se ilustra en los casos prácticos. Wikipedia tiene descripciones más detalladas de los patrones [MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel), [MVC](https://en.wikipedia.org/wiki/Model–view–controller), [Facade](https://en.wikipedia.org/wiki/Facade_pattern), [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern), [estrategia](https://en.wikipedia.org/wiki/Strategy_pattern) y [proveedor](https://en.wikipedia.org/wiki/Provider_model) (y de los [patrones de diseño](https://en.wikipedia.org/wiki/Design_Patterns) generalmente).
