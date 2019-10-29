---
title: Cómo funcionan los proveedores de contenido
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020517"
---
# <a name="how-content-providers-work"></a>Cómo funcionan los proveedores de contenido

Hay dos clases implicadas en una interacción de `ContentProvider`:

- **ContentProvider** &ndash; implementa una API que expone un conjunto de datos de forma estándar. Los métodos principales son Query, INSERT, Update y DELETE.

- **Contentresolver devuelvan** &ndash; un proxy estático que se comunica con un `ContentProvider` para tener acceso a sus datos, ya sea dentro de la misma aplicación o de otra aplicación.

Normalmente, un proveedor de contenido está respaldado por una base de datos de SQLite, pero la API significa que no es necesario que el código de consumo sepa nada sobre el SQL subyacente. Las consultas se realizan a través de un URI mediante el uso de constantes para hacer referencia a nombres de columna (para reducir las dependencias de la estructura de datos subyacente) y se devuelve una `ICursor` para que el código de consumo realice la iteración.

## <a name="consuming-a-contentprovider"></a>Consumir un ContentProvider

`ContentProviders` exponen su funcionalidad a través de un URI que está registrado en el **archivo AndroidManifest. XML** de la aplicación que publica los datos. Existe una Convención en la que el URI y las columnas de datos que se exponen deben estar disponibles como constantes para facilitar el enlace a los datos. Los `ContentProviders` integrados de Android proporcionan clases útiles con constantes que hacen referencia a la estructura de datos en el espacio de nombres [`Android.Providers`](xref:Android.Provider) .

### <a name="built-in-providers"></a>Proveedores integrados

Android ofrece acceso a una amplia gama de datos de usuario y del sistema mediante `ContentProviders`:

- *Explorador* &ndash; marcadores y el historial del explorador (requiere `READ_HISTORY_BOOKMARKS` de permisos y/o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; llamadas recientes realizadas o recibidas con el dispositivo.

- *Contactos* &ndash; información detallada de la lista de contactos del usuario, que incluye personas, teléfonos, fotos & grupos.

- *MediaStore* &ndash; contenido del dispositivo del usuario: audio (álbumes, intérpretes, géneros, listas de reproducción), imágenes (incluidas las miniaturas) & vídeo.

- *Configuración* &ndash; opciones y preferencias del dispositivo en todo el sistema.

- *UserDictionary* &ndash; contenido del diccionario definido por el usuario que se usa para la entrada de texto predictivo.

- *Correo de voz* &ndash; historial de mensajes de correo de voz.

## <a name="classes-overview"></a>Información general sobre clases

A continuación se muestran las clases principales que se usan al trabajar con un `ContentProvider`:

[![diagrama de clases de la aplicación del proveedor de contenido y el consumo de interacciones de la aplicación](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

En este diagrama, el `ContentProvider` implementa las consultas y registra el URI del URI que otras aplicaciones usan para buscar datos. El `ContentResolver` actúa como ' proxy ' en el `ContentProvider` (métodos de consulta, inserción, actualización y eliminación). El `SQLiteOpenHelper` contiene los datos que usa el `ContentProvider`, pero no se expone directamente a las aplicaciones de consumo.
El `CursorAdapter` pasa el cursor devuelto por el `ContentResolver` que se va a mostrar en un `ListView`. El `UriMatcher` es una clase auxiliar que analiza los URI al procesar las consultas.

A continuación se describe el propósito de cada clase:

- **ContentProvider** &ndash; implementan los métodos de esta clase abstracta para exponer los datos. La API se pone a disposición de otras clases y aplicaciones a través del atributo URI que se agrega a la definición de clase.

- **SQLiteOpenHelper** &ndash; ayuda a implementar el almacén de almacenamiento de los almacenes de los mismos que expone el `ContentProvider`.

- **UriMatcher** &ndash; usar `UriMatcher` en su implementación de `ContentProvider` para ayudar a administrar los URI que se usan para consultar el contenido.

- **Contentresolver devuelvan** &ndash; consumo de código utiliza un `ContentResolver` para tener acceso a una instancia de `ContentProvider`. Las dos clases se encargan de los problemas de comunicación entre procesos, lo que permite que los datos se compartan fácilmente entre las aplicaciones. El consumo de código nunca crea una clase `ContentProvider` explícitamente; en su lugar, se obtiene acceso a los datos mediante la creación de un cursor basado en un URI expuesto por la aplicación `ContentProvider`.

- **CursorAdapter** &ndash; usar `CursorAdapter` o `SimpleCursorAdapter` para mostrar los datos a los que se tiene acceso a través de un `ContentProvider`.

La API de `ContentProvider` permite a los consumidores realizar una serie de operaciones en los datos, como:

- Consultar los datos para devolver listas o registros individuales.
- Modifique los registros individuales.
- Agregar nuevos registros.
- Eliminar registros.

Este documento contiene un ejemplo que usa un `ContentProvider`proporcionado por el sistema, así como un ejemplo simple de solo lectura que implementa un `ContentProvider`personalizado.
