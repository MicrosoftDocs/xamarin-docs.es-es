---
title: ListView. Android ListView y el ciclo de vida de la actividad
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1c093d3d67ace3b0f9186fca8226d4ef631d9af0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762307"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>ListView. Android ListView y el ciclo de vida de la actividad

Las actividades pasan por determinados Estados a medida que se ejecuta la aplicación, como el inicio, la ejecución, la pausa y la detención. Para obtener más información e instrucciones específicas sobre el control de las transiciones de estado, vea el [tutorial del ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md)de la actividad.
Es importante comprender el ciclo de vida de la actividad y `ListView` colocar el código en las ubicaciones correctas.

Todos los ejemplos de este documento realizan tareas de configuración en el método de `OnCreate` la actividad y, si es necesario, realizan el "Desmontaje" en. `OnDestroy` Los ejemplos suelen usar pequeños conjuntos de datos que no cambian, por lo que no es necesario volver a cargar los datos con más frecuencia.

Sin embargo, si los datos cambian con frecuencia o usan una gran cantidad de memoria, podría ser adecuado utilizar distintos métodos de ciclo de vida para `ListView`rellenar y actualizar. Por ejemplo, si los datos subyacentes cambian constantemente (o pueden verse afectados por actualizaciones en otras actividades), al crear el adaptador `OnStart` en `OnResume` o se garantizará que se muestren los datos más recientes cada vez que se muestre la actividad.

Si el adaptador usa recursos como memoria, o un cursor administrado, recuerde liberar esos recursos en el método complementario en el que se crearon instancias (por ejemplo, los objetos creados `OnStart` en se pueden desechar en `OnStop`).

## <a name="configuration-changes"></a>Cambios de configuración

Es importante recordar que los cambios &ndash; de configuración, especialmente el giro de la pantalla y la visibilidad &ndash; del teclado, pueden hacer que la actividad actual se destruya y se vuelva a `ConfigurationChanges` crear (a menos que se especifique lo contrario mediante el atributo). Esto significa que, en condiciones normales, la rotación de un dispositivo `ListView` `Adapter` hará que se vuelva a crear y (a menos que haya escrito código `OnPause` en `OnResume`y), se perderán los Estados de selección de fila y posición de desplazamiento.

El atributo siguiente impedirá que una actividad se destruya y se vuelva a crear como resultado de los cambios de configuración:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

Después, la actividad debe `OnConfigurationChanged` reemplazar para responder a los cambios de forma adecuada. Para obtener más información sobre cómo controlar los cambios de configuración, consulte la documentación de.
