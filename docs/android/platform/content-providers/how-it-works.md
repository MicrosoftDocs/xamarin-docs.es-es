---
title: Cómo funcionan los proveedores de contenido
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 8fb0b5136cc75463fc35ced2336e0ea321e8aecd
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524415"
---
# <a name="how-content-providers-work"></a>Cómo funcionan los proveedores de contenido

Hay dos clases implicadas en una `ContentProvider` interacción:

- **ContentProvider** &ndash; Implementa una API que expone un conjunto de datos de forma estándar. Los métodos principales son Query, INSERT, Update y DELETE.

- **ContentResolver devuelvan** Proxy estático que se comunica con un `ContentProvider` para tener acceso a sus datos, desde dentro de la misma aplicación o desde otra aplicación. &ndash;

Normalmente, un proveedor de contenido está respaldado por una base de datos de SQLite, pero la API significa que no es necesario que el código de consumo sepa nada sobre el SQL subyacente. Las consultas se realizan a través de un URI mediante el uso de constantes para hacer referencia a nombres de columna (para reducir las dependencias `ICursor` de la estructura de datos subyacente) y se devuelve una para que el código de consumo realice la iteración.


## <a name="consuming-a-contentprovider"></a>Consumir un ContentProvider

`ContentProviders`exponga su funcionalidad a través de un URI que esté registrado en el **archivo AndroidManifest. XML** de la aplicación que publica los datos. Existe una Convención en la que el URI y las columnas de datos que se exponen deben estar disponibles como constantes para facilitar el enlace a los datos. La funcionalidad integrada `ContentProviders` de Android proporciona clases útiles con constantes que hacen referencia a la estructura de datos en [`Android.Providers`](xref:Android.Provider) el espacio de nombres.



### <a name="built-in-providers"></a>Proveedores integrados

Android ofrece acceso a una amplia gama de datos de usuario y del `ContentProviders`sistema mediante:

- *Explorador* de marcadores e historial del explorador (requiere permisos `READ_HISTORY_BOOKMARKS` y/o `WRITE_HISTORY_BOOKMARKS`). &ndash;

- *CallLog* &ndash; llamadas recientes realizadas o recibidas con el dispositivo.

- *Contactos* de &ndash; información detallada de la lista de contactos del usuario, que incluye personas, teléfonos, fotos & grupos.

- *Mediastore* &ndash; contenido del dispositivo del usuario: audio (álbumes, intérpretes, géneros, listas de reproducción), imágenes (incluidas las miniaturas) & vídeo.

- *Configuración* de &ndash; opciones y preferencias de configuración de dispositivos en todo el sistema.

- *UserDictionary* &ndash; contenido del diccionario definido por el usuario que se usa para la entrada de texto predictivo.

- *Correo de voz* &ndash; historial de mensajes de correo de voz.



## <a name="classes-overview"></a>Información general sobre clases

A continuación se muestran las clases principales que `ContentProvider` se usan al trabajar con un:

[![Diagrama de clases de la aplicación del proveedor de contenido y uso de interacciones de la aplicación](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

En este diagrama, `ContentProvider` implementa las consultas y registra el URI del URI que otras aplicaciones usan para buscar datos. Actúa como ' proxy ' `ContentProvider` para (métodos de consulta, inserción, actualización y eliminación). `ContentResolver` Contiene `SQLiteOpenHelper` los datos que usa, `ContentProvider`pero no se expone directamente a las aplicaciones de consumo.
Pasa el cursor devuelto `ContentResolver` por que se va a mostrar `ListView`en. `CursorAdapter` `UriMatcher` Es una clase auxiliar que analiza los URI al procesar las consultas.

A continuación se describe el propósito de cada clase:

- **ContentProvider** &ndash; Implemente los métodos de esta clase abstracta para exponer los datos. La API se pone a disposición de otras clases y aplicaciones a través del atributo URI que se agrega a la definición de clase.

- **SQLiteOpenHelper** Ayuda a implementar el almacén de almacenes de los almacenes de `ContentProvider`los mismos que expone. &ndash;

- **UriMatcher** &ndash; Use en`UriMatcher` su`ContentProvider` implementación de para ayudar a administrar los URI que se usan para consultar el contenido.

- **ContentResolver devuelvan** El uso de código utiliza `ContentResolver` un para tener `ContentProvider` acceso a una instancia de. &ndash; Las dos clases se encargan de los problemas de comunicación entre procesos, lo que permite que los datos se compartan fácilmente entre las aplicaciones. El consumo de código nunca crea `ContentProvider` una clase explícita; en su lugar, se obtiene acceso a los datos mediante la creación de un cursor basado en un `ContentProvider` URI expuesto por la aplicación.

- **CursorAdapter** &ndash; `ContentProvider`Use `CursorAdapter` o paramostrarlosdatosalosquesetiene`SimpleCursorAdapter` acceso a través de.

La `ContentProvider` API permite a los consumidores realizar una serie de operaciones en los datos, como:

- Consultar los datos para devolver listas o registros individuales.
- Modifique los registros individuales.
- Agregar nuevos registros.
- Eliminar registros.

Este documento contiene un ejemplo que usa un proporcionado `ContentProvider`por el sistema, así como un ejemplo simple de solo lectura que implementa un personalizado. `ContentProvider`

