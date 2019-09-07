---
title: Cambios adicionales en los marcos de watchos 3
description: En este documento se describen los distintos cambios del marco de trabajo introducidos con watchos 3 y cómo trabajar con ellos en Xamarin. Se describen los datos principales, Motion Core, Foundation, HealthKit, HomeKit, PassKit y UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: cd4bc8dbc02a44807ec197d39349971d8f9cd6f9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768583"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Cambios adicionales en los marcos de watchos 3

_En este artículo se describen cambios más pequeños o mejoras en los marcos de trabajo existentes para watchos 3._

Además de los principales cambios en iOS, Apple ha realizado modificaciones y mejoras en varios marcos de trabajo existentes en watchos 3.

## <a name="core-data"></a>Datos principales

Se han realizado las siguientes mejoras en el marco de datos principal para Watch OS 3:

- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) raíz admiten errores y recuperaciones simultáneas sin serialización.
- La clase [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) mantiene un grupo de almacenes de datos de SQLite.
- Los objetos [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con almacenes de datos de SQLite en el modo de diario de Wal admiten la nueva característica de generación de consultas, donde los contextos de objeto administrados (MOC) se pueden anclar a versiones de base de datos específicas para futuras capturas y transacciones con errores.
- Usar el nivel `NSPersistenceContainer` alto para `NSPersistentStoreCoordinator`hacer referencia a, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principales.
- Se han agregado varios métodos de conveniencia para `NSManagedObject` facilitar la realización de capturas y la creación de subclases.

Para obtener más información, consulte la referencia de la [plataforma principal de datos](https://developer.apple.com/reference/coredata)de Apple.

## <a name="core-motion"></a>Movimiento de núcleo

Se han realizado las siguientes mejoras en Core Motion Framework para Watch OS 3:

- El nuevo evento de movimiento de dispositivo usa el acelerómetro y giroscopio para proporcionar actualizaciones de movimiento y orientación. La aplicación se puede registrar para esta actualización (a velocidades de hasta 100 Hz).
- El nuevo evento pedometer habilita las notificaciones rápidas y en tiempo real cuando el usuario se detiene y se reanuda la ejecución. Use [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) para registrarse para eventos pedometer de primer o segundo plano.

## <a name="foundation"></a>Cimiento

Se han realizado las siguientes mejoras en Foundation Framework para Watch OS 3:

- Use la nueva clase [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) para realizar cálculos de intervalos de fecha y hora, como duraciones, para comparar intervalos y pruebas de intersecciones de intervalo.
- Se han agregado varias propiedades nuevas a la clase [NSLocal](https://developer.apple.com/reference/foundation/nslocale) para adquirir información local y los formatos de presentación disponibles.
- Use la nueva clase [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) para realizar la conversión entre las diferentes unidades de medida (unidad de medida) o realice cálculos en los valores de UOMs diferentes.
- Use la nueva clase [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) para dar formato a las medidas localizadas que se van a mostrar al usuario final.
- Use las nuevas clases [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) para representar UOMs específicas.

## <a name="healthkit"></a>HealthKit

Se han realizado las siguientes mejoras en el marco de trabajo de HealthKit para Watch OS 3:

- Use la nueva clase [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- Se han agregado los nuevos [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y el método `WheelchairUse` de la clase [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) para trabajar con los datos de mantenimiento relacionados con la silla.
- Se han agregado nuevas claves de metadatos para los tipos meteorológicos `HKWeatherConditionCloudy`( `HKWeatherConditionClear` como y) y los tipos `HKWorkoutActivityTypeFlexibility` de `HKWorkoutActivityTypeWheelchairRunPace`entrenamiento (como y).

## <a name="homekit"></a>HomeKit

Se han realizado las siguientes mejoras en el marco de trabajo de HomeKit para Watch OS 3:

- Se ha agregado la capacidad de ver e interactuar con las cámaras IP conectadas a HomeKit.
- Se han agregado varios servicios y características nuevos.
- Agrega más contexto y configuración de los accesorios de servicios principales y servicios de vínculo.

## <a name="passkit"></a>PassKit

Se han realizado las siguientes mejoras en el marco de trabajo de PassKit para Watch OS 3:

- Amplía el marco de trabajo para admitir pagos en la aplicación y seguros en el Apple Watch de bienes y servicios físicos.
- Las siguientes clases están ahora disponibles: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) y [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)

## <a name="uikit"></a>UIKit

Se han realizado las siguientes mejoras en el marco de trabajo de UIKit para Watch OS 3:

- Para admitir el tipo dinámico en etiquetas, los campos de texto y los cuadros `PreferredFontForTextStyle` de texto utilizan `UIFont` el nuevo método de la clase.
- Se `ColorWithDisplayP3` ha agregado el método para admitir el color ancho.

## <a name="related-links"></a>Vínculos relacionados

- [ejemplos de watchos](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS%20watchos)
- [Novedades de watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
