---
title: Información general sobre la creación de aplicaciones multiplataforma
description: En este documento se proporciona información general de alto nivel sobre la creación de aplicaciones multiplataforma. Describe el valor de, modelos C#de diseño como MVC/MVVM y ius nativas.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7d839e0141f14f4ba86897b128bf2a8c0a79548d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016909"
---
# <a name="building-cross-platform-applications-overview"></a>Información general sobre la creación de aplicaciones multiplataforma

En esta guía se presenta la plataforma Xamarin y cómo diseñar una aplicación multiplataforma para maximizar el uso del código y ofrecer una experiencia nativa de alta calidad en todas las plataformas móviles principales: iOS, Android y Windows Phone.

El enfoque que se usa en este documento suele ser aplicable a las aplicaciones de productividad y a las aplicaciones de juegos. sin embargo, el enfoque se centra en la productividad y la utilidad (aplicaciones que no son de juego). Consulte la [Introducción al documento de monogame](~/graphics-games/monogame/introduction/index.md) o consulte [Visual Studio Tools para Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) para obtener instrucciones sobre el desarrollo de juegos multiplataforma.

La frase "escribir una vez, ejecutar en todas partes" se usa a menudo para Extol las virtudes de un único código base que se ejecuta sin modificar en varias plataformas. Aunque tiene la ventaja de reutilización del código, este enfoque conduce a menudo a aplicaciones que tienen un conjunto de características con el menor nivel de nominador y una interfaz de usuario de aspecto genérico que no encaja bien en ninguna de las plataformas de destino.

Xamarin no es solo una plataforma de "escritura en todas partes", ya que una de sus ventajas es la capacidad de implementar interfaces de usuario nativas específicamente para cada plataforma. Sin embargo, con un diseño exhaustivo, todavía es posible compartir la mayor parte del código de la interfaz que no es de usuario y obtener lo mejor de ambos mundos: escribir el almacenamiento de datos y el código de lógica de negocios una vez y presentar las ius nativas en cada plataforma. En este documento se describe un enfoque de arquitectura general para lograr este objetivo.

A continuación se muestra un resumen de los puntos clave para crear aplicaciones de Xamarin multiplataforma:

- **Use C#**  : escriba las aplicaciones en C#. El código existente escrito C# en se puede migrar a iOS y Android con Xamarin con facilidad y se usa obviamente en aplicaciones de Windows.
- **Usar patrones de diseño de MVC o MVVM** : desarrolle la interfaz de usuario de la aplicación mediante el patrón de modelo/vista/controlador. Diseñe su aplicación mediante un enfoque de modelo/vista/controlador o un enfoque de modelo/vista/ViewModel en el que hay una separación clara entre el "modelo" y el resto. Determine qué partes de la aplicación van a usar elementos de interfaz de usuario nativos de cada plataforma (iOS, Android, Windows y Mac) y úselas como guía para dividir la aplicación en dos componentes: "Core" y "User-Interface".
- **Crear ius nativa** : cada aplicación específica del sistema operativo proporciona un nivel de interfaz de usuario diferente ( C# implementado en con la ayuda de las herramientas de diseño de interfaz de usuario nativas):

1. En iOS, use las API de UIKit para crear aplicaciones de aspecto nativo y, opcionalmente, utilizar el diseñador de iOS de Xamarin para crear visualmente la interfaz de usuario.
1. En Android, use Android. views para crear aplicaciones de aspecto nativo, aprovechando el diseñador de la interfaz de usuario de Xamarin.
1. En Windows, usará XAML para el nivel de presentación, creado en el diseñador de la interfaz de usuario de Visual Studio o Blend.
1. En Mac, usará guiones gráficos para el nivel de presentación creado en Xcode.

Los proyectos de Xamarin. Forms se admiten en todas las plataformas y permiten crear interfaces de usuario que se pueden compartir entre plataformas mediante el XAML de Xamarin. Forms. 

La cantidad de reutilización del código dependerá en gran medida de la cantidad de código que se mantenga en el núcleo compartido y de la cantidad de código que sea específico de la interfaz de usuario. El código principal es todo lo que no interactúa directamente con el usuario, sino que proporciona servicios para las partes de la aplicación que recopilarán y mostrarán esta información.

Para aumentar la cantidad de reutilización del código, puede adoptar componentes multiplataforma que proporcionan servicios comunes en todos estos sistemas, como:

1. [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) para almacenamiento local de SQL,
1. [Complementos de Xamarin](https://xamarin.com/plugins) para acceder a funcionalidades específicas del dispositivo, como la cámara, los contactos y la ubicación geográfica,
1. [Paquetes NuGet](https://nuget.org) que son compatibles con los proyectos de Xamarin, como [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/),
1. Usar las características de .NET Framework para redes, servicios Web, e/s y mucho más.

Algunos de estos componentes se implementan en el caso práctico de *tarea* .

 <a name="Separate_Reusable_Code_into_a_Core_Library" />

## <a name="separate-reusable-code-into-a-core-library"></a>Separar el código reutilizable en una biblioteca principal

Al seguir el principio de separación de la responsabilidad mediante la disposición en capas de la arquitectura de la aplicación y, a continuación, la funcionalidad básica que es independiente de la plataforma en una biblioteca principal reutilizable, puede maximizar el uso compartido de código entre plataformas, como en la ilustración siguiente. muestra

 ![](overview-images/layers2.png "By following the principle of separation of responsibility by layering your application architecture and then moving core functionality that is platform agnostic into a reusable core library, you can maximize code sharing across platforms")

 <a name="Case_Studies" />

## <a name="case-studies"></a>Casos prácticos

Hay un caso práctico que acompaña a este documento – *tasky Pro*. En cada caso práctico se describe la implementación de los conceptos que se describen en este documento en un ejemplo del mundo real. El código es de código abierto y está disponible en [GitHub](https://github.com/xamarin/mobile-samples/).
