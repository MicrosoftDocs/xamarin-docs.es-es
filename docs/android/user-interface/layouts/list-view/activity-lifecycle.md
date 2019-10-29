---
title: ListView. Android ListView y el ciclo de vida de la actividad
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: a4ebda89ad3bdcb663dc9d7cd513e45760163c34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028946"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>ListView. Android ListView y el ciclo de vida de la actividad

Las actividades pasan por determinados Estados a medida que se ejecuta la aplicación, como el inicio, la ejecución, la pausa y la detención. Para obtener más información e instrucciones específicas sobre el control de las transiciones de estado, vea el [tutorial del ciclo de vida](~/android/app-fundamentals/activity-lifecycle/index.md)de la actividad.
Es importante comprender el ciclo de vida de la actividad y colocar el código `ListView` en las ubicaciones correctas.

Todos los ejemplos de este documento realizan tareas de configuración en el método de `OnCreate` de la actividad y, si es necesario, realizan el "Desmontaje" en `OnDestroy`. Los ejemplos suelen usar pequeños conjuntos de datos que no cambian, por lo que no es necesario volver a cargar los datos con más frecuencia.

Sin embargo, si los datos cambian con frecuencia o usan una gran cantidad de memoria, podría ser adecuado utilizar distintos métodos de ciclo de vida para rellenar y actualizar el `ListView`. Por ejemplo, si los datos subyacentes cambian constantemente (o pueden verse afectados por actualizaciones en otras actividades), al crear el adaptador en `OnStart` o `OnResume` se asegurará de que se muestren los datos más recientes cada vez que se muestre la actividad.

Si el adaptador usa recursos como memoria, o un cursor administrado, recuerde liberar esos recursos en el método complementario en el que se crearon instancias (por ejemplo, los objetos creados en `OnStart` se pueden desechar en `OnStop`).

## <a name="configuration-changes"></a>Cambios de configuración

Es importante recordar que los cambios de configuración &ndash; especialmente la rotación de la pantalla y la visibilidad del teclado &ndash; pueden hacer que la actividad actual se destruya y se vuelva a crear (a menos que se especifique lo contrario mediante el atributo `ConfigurationChanges`). Esto significa que, en condiciones normales, la rotación de un dispositivo provocará que se vuelvan a crear una `ListView` y `Adapter` y (a menos que haya escrito código en `OnPause` y `OnResume`) se perderán los Estados de selección de fila y posición de desplazamiento.

El atributo siguiente impedirá que una actividad se destruya y se vuelva a crear como resultado de los cambios de configuración:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

A continuación, la actividad debe reemplazar `OnConfigurationChanged` para responder a esos cambios adecuadamente. Para obtener más información sobre cómo controlar los cambios de configuración, consulte la documentación de.
