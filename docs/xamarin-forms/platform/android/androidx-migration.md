---
title: Migración de AndroidX en Xamarin. Forms
description: En este artículo se explica por qué existe AndroidX y cómo migrar a AndroidX en la aplicación de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 98884003-E65A-4EB4-842D-66CFE27344A4
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/22/2020
ms.openlocfilehash: 13fb802dec326cdb82bac8825ca84343ef85b13e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77646657"
---
# <a name="androidx-migration-in-xamarinforms"></a>Migración de AndroidX en Xamarin. Forms

AndroidX reemplaza a la biblioteca de compatibilidad de Android. En este artículo se explica por qué existe AndroidX, cómo afecta a Xamarin. Forms y cómo migrar la aplicación para usar las bibliotecas de AndroidX.

## <a name="history-of-androidx"></a>Historial de AndroidX

La biblioteca de compatibilidad de Android se creó para proporcionar las nuevas características de las versiones anteriores de Android. Se trata de una capa de compatibilidad que permite a los desarrolladores usar funciones que pueden no existir en todas las versiones del sistema operativo Android y que tienen reservas correctas para versiones anteriores. La biblioteca de compatibilidad también incluye clases auxiliares y auxiliares, herramientas de depuración y utilidad, y clases sofisticadas que dependen de otras clases de la biblioteca de compatibilidad para que funcionen.

Aunque la biblioteca de soporte técnico era originalmente un solo binario, ha crecido y ha evolucionado a un conjunto de bibliotecas, que son casi esenciales para el desarrollo de aplicaciones modernas. Estas son algunas de las características más utilizadas de la biblioteca de soporte:

- La clase de soporte `Fragment`.
- El `RecyclerView`, que se usa para administrar listas largas.
- Multidex admite aplicaciones con más de 65.536 métodos.
- La clase `ActivityCompat`.

AndroidX es un sustituto de la biblioteca de soporte técnico, que ya no se mantiene, todo el desarrollo de nuevas bibliotecas se producirá en la biblioteca de AndroidX. AndroidX es una biblioteca rediseñada que usa el control de versiones semántico, nombres de paquetes más claros y una mejor compatibilidad para patrones de arquitectura de aplicaciones comunes. AndroidX versión 1.0.0 es el binario equivalente a la versión 28.0.0 de la biblioteca de compatibilidad. Para obtener una lista completa de las asignaciones de clases de la biblioteca de compatibilidad a AndroidX, consulte compatibilidad de las [asignaciones de clases](https://developer.android.com/jetpack/androidx/migrate/class-mappings) de la biblioteca en Developer.Android.com.

Google creó un proceso de migración denominado Jetifier con AndroidX. El Jetifier inspecciona el código de bytes jar durante el proceso de compilación y reasigna las referencias de la biblioteca de compatibilidad, tanto en el código de la aplicación como en las dependencias, a su AndroidX equivalente.

En una aplicación de Xamarin. Forms, al igual que en una aplicación Java de Android, las dependencias jar se deben migrar a AndroidX. Sin embargo, también se deben migrar los enlaces de Xamarin para que apunten a los archivos JAR subyacentes y correctos. Xamarin. Forms agregó compatibilidad con la migración automática de AndroidX en la versión 4,5.

Para obtener más información sobre AndroidX, consulte [información general sobre AndroidX](https://developer.android.com/jetpack/androidx) en Developer.Android.com.

## <a name="automatic-migration-in-xamarinforms"></a>Migración automática en Xamarin. Forms

Para migrar automáticamente a AndroidX, un proyecto de Xamarin. Forms debe:

- Versión 29 de la API de destino, o superior.
- Use la versión 4,5 o superior de Xamarin. Forms.

Una vez que haya confirmado estos valores en el proyecto, compile la aplicación de Android en Visual Studio 2019. Durante el proceso de compilación, se inspecciona el lenguaje intermedio (IL) y las dependencias de la biblioteca de soporte y los enlaces se intercambian con las dependencias de AndroidX. Si la aplicación tiene todas las dependencias de AndroidX necesarias para compilar, observará que no hay ninguna diferencia en el proceso de compilación.

> [!NOTE]
> Debe mantener las referencias a la biblioteca de soporte en el proyecto. Se usan para compilar la aplicación antes de que el proceso de migración Inspeccione el IL resultante y transforme las dependencias.

Si se detectan dependencias de AndroidX que no forman parte del proyecto, se genera un error de compilación que indica qué paquetes de AndroidX faltan. A continuación se muestra un ejemplo de error de compilación:

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

Los paquetes de NuGet que faltan se pueden instalar a través del administrador de paquetes NuGet en Visual Studio, o bien se pueden instalar editando el archivo Android. csproj para incluir los `PackageReference` elementos XML enumerados en el error.

Una vez que se resuelven los paquetes que faltan, al volver a compilar el proyecto se cargan los paquetes que faltan y el proyecto se compila mediante dependencias de AndroidX en lugar de admitir dependencias de biblioteca.

> [!NOTE]
> Si el proyecto y las dependencias del proyecto no hacen referencia a las bibliotecas de compatibilidad con Android, el proceso de migración no realiza ninguna acción y no se ejecuta.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a la biblioteca de compatibilidad de Android](https://developer.android.com/topic/libraries/support-library/index) en Developer.Android.com
- [Información general de AndroidX](https://developer.android.com/jetpack/androidx) en Developer.Android.com
