---
title: Error de paquetes que faltan después de actualizar paquetes NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: a1ed4a2be63973e9e26dcb06163a3f9fd7eae649
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728101"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Error de paquetes que faltan después de actualizar paquetes NuGet

Este problema se ha comunicado principalmente en las soluciones de aplicación de ejemplo de Xamarin. Forms, pero la posibilidad de este problema puede producirse en cualquier proyecto que use paquetes de NuGet.

Si después de actualizar los paquetes de NuGet en el proyecto o la solución, verá un error que hace referencia a los números de versión del paquete anterior, como:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

En este ejemplo, *Xamarin. Forms. 1.3.1.6296* es el número de versión anterior que se quitó con la actualización del paquete NuGet.

Esto puede ocurrir si los elementos XML del archivo. csproj que hacen referencia al número de versión anterior del paquete se han agregado o editado manualmente, NuGet no los quitará ni los actualizará si se hubieran agregado o editado manualmente, por lo que el proyecto ahora busca los paquetes que se han eliminado.

Para corregir este problema, edite manualmente los archivos. csproj y elimine todos los elementos que hacen referencia al número de versión anterior.

Elementos de ejemplo que se van a quitar (si tienen el número de versión anterior del paquete):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
