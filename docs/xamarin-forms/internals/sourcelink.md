---
title: "Source Link with Xamarin.Forms " Description: "en este artículo se explica cómo usar el vínculo de origen para depurar en Xamarin.Forms ".
zone_pivot_groups: "plataforma" MS. Prod: Xamarin ms. AssetID: 1E13FCD9-5607-46E8-80E4-87A58B389BEB ms. Technology: Xamarin-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 09/26/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="source-link-with-xamarinforms"></a>Vínculo de origen conXamarin.Forms

Xamarin.FormsLos paquetes NuGet incluyen asignaciones de vínculo de origen. El vínculo de origen asigna las bibliotecas compiladas, contenidas en un paquete NuGet, a un repositorio de código fuente. Visual Studio descargará los archivos de código fuente durante la depuración y permitirá a los desarrolladores recorrer el código y habilitar la depuración de paquetes sin compilar desde el origen.

Para obtener más información sobre el uso del vínculo de origen, consulte la [documentación de vínculo de origen](/dotnet/standard/library-guidance/sourcelink).

::: zone pivot="windows"

> [!WARNING]
> Visual Studio 2019 admite el vínculo de origen para el **depurador de .net** , pero actualmente no admite el vínculo de origen para el **depurador de mono**. Por lo tanto, puede usar el vínculo de origen para depurar aplicaciones UWP, pero no aplicaciones Android o iOS. Al depurar aplicaciones para UWP, debe asegurarse de que los archivos PDB de las bibliotecas que desea depurar se copian en la carpeta **appx** en el directorio **bin** en el que se compila la aplicación.

## <a name="enable-source-link"></a>Habilitación del vínculo de origen

El uso de un vínculo de origen requiere la habilitación de la depuración para el código externo; de lo contrario, el depurador devolverá las llamadas pasadas al código no contenido en la solución actual. En Visual Studio 2019, se puede encontrar en el menú **Opciones** de la sección **depuración** :

[![Habilitar vínculo de origen en Visual Studio 2019](sourcelink-images/sourcelink-enable-pc-cropped.png)](sourcelink-images/sourcelink-enable-pc.png#lightbox)

Asegúrese de que **Habilitar solo mi código** está deshabilitado y que **Habilitar la compatibilidad con vínculos de origen** está habilitada.

::: zone-end
::: zone pivot="macos"

## <a name="enable-source-link"></a>Habilitación del vínculo de origen

El uso de un vínculo de origen requiere la habilitación de la depuración para el código externo; de lo contrario, el depurador devolverá las llamadas pasadas al código no contenido en la solución actual. Esta opción puede encontrarse en la ventana **preferencias** de la sección **depurador** :

[![Habilitar vínculo de origen en Visual Studio para Mac](sourcelink-images/sourcelink-enable-mac-cropped.png)](sourcelink-images/sourcelink-enable-mac.png#lightbox)

Asegúrese de que la opción **ir a código externo** está habilitada.

::: zone-end

## <a name="debug-xamarinforms-using-source-link"></a>Depurar Xamarin.Forms mediante vínculo de origen

Si está habilitada la depuración de paquetes externos, Visual Studio usará las asignaciones de vínculo de origen contenidas en el paquete de NuGet para descargar y recorrer el código fuente externo. Esto se puede probar estableciendo un punto de interrupción en una llamada a un método proporcionado por Xamarin.Forms :

[![Punto de interrupción establecido en el Xamarin.Forms método](sourcelink-images/breakpoint-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

En función de la configuración especificada en las opciones del **depurador** , Visual Studio le advertirá de que está descargando los archivos de origen:

[![ADVERTENCIA de código externo de Visual Studio](sourcelink-images/external-code-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

Una vez que permita que Visual Studio Descargue los archivos, el depurador entrará en el código externo.

::: zone pivot="windows"

## <a name="source-link-caching"></a>Almacenamiento en caché de vínculos de origen

El vínculo de origen usa el almacenamiento en caché para el rendimiento. El directorio de almacenamiento en caché para el vínculo de origen se define en el menú **Opciones** , en **depuración** , en la sección **símbolos** :

[![Almacenamiento en caché de vínculos de origen de Visual Studio](sourcelink-images/sourcelink-caching-pc-cropped.png)](sourcelink-images/sourcelink-caching-pc.png#lightbox)

Este menú permite especificar el directorio de almacenamiento en caché para todos los símbolos de depuración, así como borrar la memoria caché si se producen problemas con los símbolos almacenados en caché.

::: zone-end
::: zone pivot="macos"

## <a name="source-link-caching"></a>Almacenamiento en caché de vínculos de origen

El vínculo de origen usa el almacenamiento en caché para el rendimiento. El directorio de almacenamiento en caché para el vínculo de origen en MacOS es `/Users/<username>/Library/Caches/VisualStudio/8.0/Symbols` . Esta carpeta contiene subcarpetas que almacenan el repositorio que se usa para descargar los archivos de origen. Si el repositorio de respaldo de un paquete NuGet ha cambiado, puede que tenga que eliminar manualmente estas carpetas para actualizar la memoria caché.

::: zone-end

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de vínculo de origen](/dotnet/standard/library-guidance/sourcelink)
- [Vínculo de origen en GitHub](https://github.com/dotnet/sourcelink)
