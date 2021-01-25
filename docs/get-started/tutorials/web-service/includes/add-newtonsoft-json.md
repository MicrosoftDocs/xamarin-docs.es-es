---
ms.openlocfilehash: 4517415e2431193e56728e3bb1a24e3c7d119ca4
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634970"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y, después, seleccione **Administrar paquetes NuGet…**:

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet siendo seleccionado](../images/vs/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En el **Administrador de paquetes NuGet**, seleccione la pestaña **Examinar**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vs/add-package.png "Paquete Newtonsoft.Json NuGet Package")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **WebServiceTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **WebServiceTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En **Panel de solución**, seleccione el proyecto **WebServiceTutorial**, haga clic con el botón derecho y seleccione **Administrar paquetes NuGet…** :

    ![Captura de pantalla del elemento de menú Agregar paquetes NuGet siendo seleccionado](../images/vsmac/add-nuget-packages.png "Elemento de menú Agregar paquetes NuGet")

1. En la ventana **Administrar paquetes NuGet**, busque el paquete NuGet **Newtonsoft.Json**, selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![Captura de pantalla del paquete NuGet Newtonsoft.Json en el Administrador de paquetes NuGet](../images/vsmac/add-package.png "Paquete Newtonsoft.Json NuGet Package")

    Este paquete se usará para incorporar la deserialización de JSON en la aplicación.

1. Compile la solución para asegurarse de que no haya errores.
