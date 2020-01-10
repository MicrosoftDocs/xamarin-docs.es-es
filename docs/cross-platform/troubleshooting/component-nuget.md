---
title: Actualización de referencias de componentes a NuGet
description: En este documento se describe cómo reemplazar las referencias de componente con paquetes NuGet para probar las aplicaciones en el futuro, ya que el almacén de componentes de Xamarin ya no se incluye.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 4a5aa13a197e885b074b07eae3594abd4992ee71
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728257"
---
# <a name="updating-component-references-to-nuget"></a>Actualización de referencias de componentes a NuGet

> [!IMPORTANT]
> El almacén de componentes se ha suspendido desde el 15 de mayo de 2018 (este cierre se [anunció](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) originalmente en el 2017 de noviembre).
>
> Los componentes de Xamarin ya no se admiten en Visual Studio y deben reemplazarse por paquetes NuGet. Siga las instrucciones siguientes para quitar manualmente las referencias a componentes de los proyectos.

Consulte estas instrucciones para agregar paquetes NuGet en [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) o [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Hay disponible una lista de [bibliotecas y complementos](https://github.com/xamarin/XamarinComponents/blob/master/README.md) de Xamarin populares para ayudar a encontrar alternativas a los componentes que no están disponibles como paquetes NuGet.

## <a name="manually-removing-component-references"></a>Quitar manualmente las referencias a componentes

La versión 15,6 de Visual Studio y 7,4 de Visual Studio para Mac ya no admite componentes en el proyecto. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Si carga un proyecto en Visual Studio, se muestra el cuadro de diálogo siguiente, en el que se explica que debe quitar manualmente los componentes del proyecto:

![Cuadro de diálogo de alerta que explica que un componente se ha encontrado en el proyecto y debe quitarse](component-nuget-images/component-alert-vs.png)

Para quitar un componente del proyecto:

1. Abra el archivo **.csproj**. Para ello, haga clic con el botón derecho en el nombre del proyecto y seleccione **descargar el proyecto**. 

2. Vuelva a hacer clic con el botón derecho en el proyecto descargado y seleccione **Editar {your-Project-Name}. csproj**.

3. Busque cualquier referencia en el archivo para `XamarinComponentReference`. Debe ser similar al ejemplo siguiente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, es seguro quitar todo el `ItemGroup`.

5. Una vez guardado el archivo, haga clic con el botón derecho en el nombre del proyecto y seleccione **volver a cargar el proyecto**.

6. Repita los pasos anteriores para cada proyecto de la solución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Si carga un proyecto en Visual Studio para Mac, se muestra el cuadro de diálogo siguiente, en el que se explica que debe quitar manualmente los componentes del proyecto:

![Cuadro de diálogo de alerta que explica que un componente se ha encontrado en el proyecto y debe quitarse](component-nuget-images/component-alert.png)

Para quitar un componente del proyecto:

1. Abra el archivo. csproj. Para ello, haga clic con el botón derecho en el nombre del proyecto y seleccione **herramientas > Editar archivo**.

2. Busque cualquier referencia en el archivo para `XamarinComponentReference`. Debe ser similar al ejemplo siguiente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, es seguro quitar todo el `ItemGroup`

4. Repita los pasos anteriores para cada proyecto de la solución.

-----

> [!WARNING]
> Las siguientes instrucciones solo funcionan con versiones anteriores de Visual Studio.
> El nodo **componentes** ya no está disponible en las versiones actuales de Visual Studio 2017 o Visual Studio para Mac.

En las secciones siguientes se explica cómo actualizar las soluciones de Xamarin existentes para cambiar las referencias de componentes a paquetes de NuGet.

- [Componentes que contienen paquetes NuGet](#contain)
- [Componentes con reemplazos de NuGet](#replace)

La mayoría de los componentes se encuentran en una de las categorías anteriores.
Si usa un componente que no parece tener un paquete de NuGet equivalente, lea la sección [componentes sin una ruta de acceso de migración de Nuget](#require-update) a continuación.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componentes que contienen paquetes NuGet

Muchos componentes ya contienen paquetes NuGet y la ruta de migración es simplemente eliminar la referencia de componente.

Puede determinar si el componente ya incluye un paquete NuGet haciendo doble clic en el componente en la solución:

![Nodo de componentes expandido](component-nuget-images/solution-sml.png)

En la pestaña **paquetes** se enumeran todos los paquetes NuGet incluidos en el componente:

![La pestaña Paquetes contiene NuGet](component-nuget-images/packages-tab-sml.png)

Tenga en cuenta que la pestaña **ensamblados** estará vacía:

![La pestaña ensamblados está vacía](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Actualización de la solución

Para actualizar la solución, elimine la entrada del **componente** de la solución:

![Eliminar componente](component-nuget-images/delete-component-sml.png)

El paquete NuGet permanecerá en la lista del nodo **paquetes** y la aplicación se compilará y ejecutará como de costumbre. En el futuro, las actualizaciones de este paquete se realizarán a través de la característica de actualización de **NuGet** :

![Actualización del paquete NuGet](component-nuget-images/nuget-update-sml.png)

<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componentes con reemplazos de NuGet

Si la pestaña **ensamblados** de la página de información de componentes contiene entradas como se muestra a continuación, deberá buscar manualmente el paquete de NuGet equivalente.

![Contiene ensamblados](component-nuget-images/assemblies-tab-sml.png)

Tenga en cuenta que la pestaña **paquetes** probablemente estará vacía:

![](component-nuget-images/packages-tab-empty-sml.png)

_Puede contener dependencias de NuGet, pero se pueden omitir._

Para confirmar que existe un paquete de NuGet de reemplazo, busque [NuGet.org](https://www.nuget.org/packages), use el nombre del componente o, como alternativa, el autor.

Por ejemplo, puede encontrar el popular paquete **SQLite-net-PCL** buscando:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) : el nombre del producto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) : el perfil del autor.

### <a name="updating-the-solution"></a>Actualización de la solución

Una vez que haya confirmado que el componente está disponible en NuGet, siga estos pasos:

#### <a name="delete-the-component"></a>Eliminar el componente

Haga clic con el botón derecho en el componente de la solución y elija **quitar**:

![Quitar componente](component-nuget-images/remove-component-sml.png)

Esto eliminará el componente y las referencias. Esto interrumpirá la compilación hasta que agregue el paquete de NuGet equivalente para reemplazarlo.

#### <a name="add-the-nuget-package"></a>Incorporación del paquete NuGet

1. Haga clic con el botón derecho en el nodo **paquetes** y elija **agregar paquetes.** ...
2. Busque el reemplazo de NuGet por nombre o autor:

    ![](component-nuget-images/nuget-search-sml.png)

3. Presione **Agregar paquete**.

El paquete NuGet se agregará al proyecto, junto con las dependencias.
Esto debería corregir la compilación. Si la compilación sigue produciendo errores, investigue cada error para ver si hubo diferencias de API entre el componente y el paquete NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componentes sin una ruta de migración de NuGet

No se preocupe si no encuentra inmediatamente una sustitución de los componentes que se usan en la aplicación. Los componentes existentes seguirán funcionando en Visual Studio 15,5 y el nodo **componentes** aparecerá en la solución como de costumbre.

Sin embargo, las futuras versiones de Visual Studio _no_ restaurarán ni actualizarán los componentes.
Esto significa que si abre la solución en un equipo nuevo, el componente no se descargará ni instalará. y el autor no podrá proporcionarle actualizaciones. Debe planear:

- Extraiga los ensamblados del componente y haga referencia a ellos directamente en el proyecto.
- Póngase en contacto con el autor del componente y pregúntese sobre los planes de migración a NuGet.
- Investigue paquetes de NuGet alternativos o busque el código fuente si el componente es de código abierto.

Muchos proveedores de componentes siguen trabajando en la migración a NuGet y otros (incluidos los productos disponibles comercialmente) pueden investigar opciones de entrega alternativas.

## <a name="related-links"></a>Vínculos relacionados

- [Lista de complementos y bibliotecas de Xamarin populares](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar y usar un paquete de NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Inclusión de un paquete NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
