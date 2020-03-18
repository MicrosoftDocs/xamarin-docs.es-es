---
title: AndroidX
description: Introducción al desarrollo de aplicaciones con AndroidX mediante Xamarin.Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "77618915"
---
# <a name="androidx-with-xamarin"></a>AndroidX con Xamarin

_Introducción al desarrollo de aplicaciones con AndroidX mediante Xamarin.Android._

AndroidX supone una mejora importante en la biblioteca de compatibilidad de Android original, que ya no se mantiene. Los paquetes de **AndroidX** reemplazan por completo a la biblioteca de compatibilidad de Android proporcionando una paridad de características y nuevas bibliotecas que se pueden usar en las aplicaciones de Android.

Android incluye las siguientes características:

- Todos los paquetes dentro de AndroidX tienen ahora un espacio de nombres coherente que empieza por `androidx`. Esto significa que todos los paquetes de la biblioteca de compatibilidad de Android se asignan a un paquete de `androidx.*` correspondiente.
- Los paquetes de `androidx` se mantienen y actualizan por separado. Esto significa que puede actualizar las bibliotecas de AndroidX de forma independiente entre sí.
- A partir de la v28 de la biblioteca de compatibilidad de Android, no habrá más versiones. A partir de entonces, todo el desarrollo se incluirá en `androidx`.

![Logotipo de AndroidX](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Requisitos

Para usar las características de AndroidX en aplicaciones basadas en Xamarin, se requiere lo siguiente:

- **Visual Studio**: en Windows, actualice a Visual Studio 2019 versión 16.4 o posterior. En macOS, actualice a Visual Studio 2019 para Mac versión 8.4 o posterior.
- **Xamarin.Android**: Xamarin.Android 10.0 o posterior debe instalarse con Visual Studio (Xamarin.Android se instala automáticamente como parte de la carga de trabajo de **desarrollo móvil con .NET** en Windows y se instala como parte del **instalador de Visual Studio para Mac**).
- **Kit para desarrolladores de Java**: el desarrollo de Xamarin.Android 10.0 requiere JDK 8. La distribución de Microsoft de OpenJDK se instala automáticamente como parte de Visual Studio.
- **Android SDK**: la API 28 o superior de Android SDK debe estar instalada con el Administrador de Android SDK.

## <a name="get-started"></a>Primeros pasos

Puede empezar a trabajar con AndroidX incluyendo cualquier [paquete de NuGet de AndroidX](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) dentro del proyecto de Android. Obtenga más información sobre cómo instalar y usar un paquete en [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) o [Visual Studio para Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Cambios de comportamiento

Dado que AndroidX es un nuevo diseño de la biblioteca de compatibilidad de Android, incluye pasos de migración que afectarán a las aplicaciones Android compiladas con la biblioteca de compatibilidad de Android.

### <a name="package-name-change"></a>Cambio de nombre de paquete
Los nombres de paquete se han cambiado entre los paquetes antiguo y nuevo. A continuación puede ver un ejemplo de estos cambios:

| Antiguo                    | Nuevo                    |
| ---------------------- | ---------------------- |
| android.support.**     | androidx.@             |
| android.design.**      | com.google.android.material.@ |
| android.support.test.** | androidx.test.@       |
| android.arch.**        | androidx.@             |
| android.arch.persistence.room.** | androidx.room.@ |
| android.arch.persistence.** | androidx.sqlite.@ |

Para obtener más información sobre la nomenclatura de paquetes, [consulte la siguiente documentación](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Herramientas de migración

Hay tres pasos de migración que se deben tener en cuenta para la aplicación.

1. Si la aplicación incluye **espacios de nombres de la biblioteca de compatibilidad de Android y desea migrarlos a los espacios de nombres de AndroidX**, puede usar nuestras herramientas del IDE de **migración a AndroidX** para tratar con la mayoría de los escenarios de espacio de nombres. 

Habilite **AndroidX Migrator** a través de **Herramientas > Opciones > Xamarin > Configuración de Android** en Visual Studio 2019 (puede omitir este paso en Visual Studio para Mac).

![Habilitación de AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Haga clic con el botón derecho en el proyecto y seleccione **Migrar a AndroidX**.

![Migrar a AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Tendrá que realizar algunos cambios manuales en el espacio de nombres para los escenarios que la herramienta no abarca. Aunque asignaremos el paquete correcto automáticamente, se recomienda que eche un vistazo a las [asignaciones de artefactos](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) y las [asignaciones de clases](https://developer.android.com/jetpack/androidx/migrate/class-mappings) oficiales para ayudar a la migración del proyecto.

2. Si la aplicación incluye **las dependencias que no se han migrado al espacio de nombres de AndroidX**, tendrá que usar la [biblioteca de compatibilidad de Android con el paquete de migración de AndroidX](https://www.nuget.org/packages/Xamarin.AndroidX.Migration).
3. Si la aplicación **no incluye ninguna dependencia que requiera la migración del espacio de nombres de AndroidX**, puede usar las [bibliotecas de AndroidX en NuGet actualmente](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>Solución de problemas

- Algunos paquetes de arquitectura dentro de AndroidX entrarán en conflicto con las versiones de la biblioteca de compatibilidad. Para solucionarlo, debe usar la versión AndroidX de estos paquetes y quitar la versión de la biblioteca de compatibilidad. Por ejemplo, si hace referencia a `Xamarin.Android.Arch.Work.Runtime` en el proyecto, entrará en conflicto con los tipos del paquete de `AndroidX.Work` recién agregado.

## <a name="summary"></a>Resumen

En este artículo se presentó AndroidX y se explicó cómo instalar y configurar las herramientas y los paquetes más recientes para el desarrollo de Xamarin.Android en AndroidX. Proporcionó una visión general de lo que es AndroidX. Incluye vínculos a la documentación de la API y temas para desarrolladores de Android que le ayudarán a empezar a crear aplicaciones con AndroidX. También se resaltan los cambios de comportamiento más importantes de AndroidX y los temas para la solución de problemas que pueden afectar a las aplicaciones existentes.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a AndroidX | El programa de Xamarin](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Repositorio de GitHub de AndroidX con Xamarin](https://github.com/xamarin/AndroidX)
- [Repositorio de GitHub de migración de AndroidX con Xamarin](https://github.com/xamarin/XamarinAndroidXMigration)
