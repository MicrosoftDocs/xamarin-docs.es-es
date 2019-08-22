---
ms.openlocfilehash: 22a8542c0e829db8b889abc2c7fe5f5ab9d19ed4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527646"
---

Al compilar aplicaciones para iOS en Visual Studio y el agente de compilación para Mac, el lote .app no se vuelve a copiar en la máquina de Windows. Las herramientas de Xamarin para Visual Studio 7.4 agregan una nueva propiedad `CopyAppBundle` que permite a las compilaciones de integración continua copiar lotes .app en Windows.

Para utilizar esta funcionalidad, agregue la propiedad `CopyAppBundle` al elemento .csproj, en el grupo de propiedades en el que quiera aplicarla. En el ejemplo siguiente se muestra cómo copiar el lote .app en el equipo de Windows para una compilación **Depuración** que tiene como objetivo **iPhoneSimulator**:

```xml
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
    <CopyAppBundle>true</CopyAppBundle>
</PropertyGroup>
```
