---
title: Usar bibliotecas de .NET Standard para compartir código
description: En este documento se describe cómo usar las bibliotecas de .NET Standard para compartir código. Describe la creación de una biblioteca de .NET Standard, la edición de la configuración y su uso en una aplicación.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: 6c2f0ef1d34b60e712dd713d4fff3fe5f9856926
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458047"
---
# <a name="net-standard-library-code-sharing"></a>Uso compartido de código de biblioteca .NET Standard

.NET Standard bibliotecas tienen una API uniforme para todas las plataformas de .NET, como Xamarin y .NET Core. Cree una biblioteca de .NET Standard única y úsela desde cualquier tiempo de ejecución que admita la plataforma de .NET Standard. Consulte [este gráfico](/dotnet/standard/net-standard#net-implementation-support) para obtener detalles de las plataformas admitidas.

Aunque .NET Standard versiones 1,0 a 1,6 proporcionan subconjuntos incrementales de mayor tamaño de la .NET Framework, .NET Standard 2,0 proporciona el mejor nivel de compatibilidad para las aplicaciones de Xamarin y para migrar las bibliotecas de clases portables existentes.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

En esta sección se explica cómo crear y usar una biblioteca de .NET Standard con Visual Studio para Mac.

### <a name="creating-a-net-standard-library"></a>Creación de una biblioteca de .NET Standard

Puede Agregar una biblioteca de .NET Standard a la solución con estos pasos:

1. En el cuadro de diálogo **Agregar nuevo proyecto** , seleccione la categoría **.net Core** y, a continuación, seleccione **biblioteca de .net Standard**:

    ![Crear una biblioteca de .NET Standard](net-standard-images/vsm01-m157.png "Crear una nueva biblioteca de .NET Standard")

2. En la siguiente pantalla, elija la versión de .NET Framework de destino: se recomienda **.NET Standard 2,0** :

    [![Elija .NET Standard 2,0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. En la última pantalla, escriba el nombre del proyecto y haga clic en **crear**.

4. El proyecto de biblioteca de .NET Standard aparecerá como se muestra en el Explorador de soluciones. El nodo dependencias indicará que la biblioteca usa [NETStandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![El nodo dependencias de la solución indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Editar la configuración de la biblioteca de .NET Standard

Para ver y cambiar la configuración de la biblioteca de .NET Standard, haga clic con el botón derecho en el proyecto y seleccione `Options` tal como se muestra en esta captura de pantalla:

![Editar .NET Standard plataforma de destino en las opciones del proyecto](net-standard-images/vsm03-m157.png "Edite la versión de .NET Standard plataforma de destino en las opciones del proyecto")

Dentro de, puede cambiar la versión de `netstandard` cambiando el `Target Framework` valor de la lista desplegable.

**Además:** Puede editar `.csproj` directamente para cambiar este valor.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

En esta sección se explica cómo crear y usar una biblioteca de .NET Standard con Visual Studio.

### <a name="creating-a-net-standard-library"></a>Creación de una biblioteca de .NET Standard

Agregar una biblioteca de .NET Standard a la solución es bastante sencillo.

1. En el cuadro de diálogo **nuevo proyecto** , seleccione la categoría **.net Standard** y, a continuación, seleccione **biblioteca de clases (.net Standard)**.

    ![Crear una nueva biblioteca de clases de .NET Standard](net-standard-images/vs01-w157.png "Crear nueva biblioteca de clases de .NET Standard")

2. El proyecto de biblioteca de .NET Standard aparecerá como se muestra en el Explorador de soluciones. El nodo dependencias indicará que la biblioteca usa [NETStandard. Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![NETStandard. library en la carpeta de proyecto](net-standard-images/vs02-w157.png ".NET Standard proyecto en la solución")

### <a name="editing-net-standard-library-settings"></a>Editar la configuración de la biblioteca de .NET Standard

Para ver y cambiar la configuración de la biblioteca de .NET Standard, haga clic con el botón derecho en el proyecto y seleccione **propiedades** como se muestra en esta captura de pantalla:

![Edición de las plataformas de destino de .NET Standard en las propiedades del proyecto](net-standard-images/vs03-w157.png "Hacer referencia a una biblioteca de .NET Standard de la misma manera que otros proyectos")

**Además:** Puede editar `.csproj` directamente para editar el `TargetFramework` elemento y cambiar la versión de destino (por ejemplo, `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Usar un proyecto de biblioteca de .NET Standard

Una vez creada una biblioteca de .NET Standard, puede Agregar una referencia a ella desde cualquier proyecto de biblioteca o aplicación compatible de la misma manera que normalmente agrega referencias. En Visual Studio, haga clic con el botón derecho en el nodo referencias y elija **Agregar referencia...** a continuación, cambie a la pestaña **proyectos > solución** , como se muestra a continuación:

![Referencia a una biblioteca de .NET Standard](net-standard-images/vs04.png "En Visual Studio, haga clic con el botón derecho en el nodo referencias y elija Agregar referencia... a continuación, cambie a la pestaña proyectos de la solución, como se muestra")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET Standard y Xamarin. Forms para el desarrollador de .NET (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Vínculos relacionados

* [.Net Standard](/dotnet/standard/net-standard) : información detallada y comparación con PCL.