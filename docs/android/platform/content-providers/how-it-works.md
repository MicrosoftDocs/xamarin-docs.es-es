---
title: Cómo funcionan los proveedores de contenido
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020517"
---
# <a name="how-content-providers-work"></a>Cómo funcionan los proveedores de contenido

Hay dos clases implicadas en una interacción de `ContentProvider`:

- **ContentProvider**: implementa una API que expone un conjunto de datos de forma estándar. Los métodos principales son la consulta, la inserción, la actualización y la eliminación.

- **ContentResolver**: un proxy estático que se comunica con una instancia de `ContentProvider` para acceder a sus datos, bien desde dentro de la misma aplicación o desde otra aplicación.

Normalmente, un proveedor de contenido está respaldado por una base de datos de SQLite, pero la API implica que no es necesario que el código de consumo conozca nada sobre el SQL subyacente. Las consultas se realizan a través de un URI mediante el uso de constantes para hacer referencia a nombres de columna (y reducir las dependencias de la estructura de datos subyacente) y se devuelve un elemento `ICursor` en el que el código de consumo realiza la iteración.

## <a name="consuming-a-contentprovider"></a>Consumo de una instancia de ContentProvider

Las instancias de `ContentProviders` exponen su funcionalidad a través de un URI que está registrado en el archivo **AndroidManifest.xml** de la aplicación que publica los datos. Existe una convención en la que el URI y las columnas de datos que se exponen deben estar disponibles como constantes para facilitar el enlace a los datos. Las instancias de `ContentProviders` integradas de Android proporcionan clases de conveniencia con constantes que hacen referencia a la estructura de datos en el espacio de nombres [`Android.Providers`](xref:Android.Provider).

### <a name="built-in-providers"></a>Proveedores integrados

Android ofrece acceso a una amplia gama de datos de usuario y del sistema mediante `ContentProviders`:

- *Browser*: los marcadores y el historial del explorador (requiere el permiso `READ_HISTORY_BOOKMARKS` o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog*: las llamadas recientes realizadas o recibidas con el dispositivo.

- *Contacts*: la información detallada de la lista de contactos del usuario, incluidos los contactos, los teléfonos, las fotos y los grupos.

- *MediaStore*: contenido del dispositivo del usuario: audio (álbumes, artistas, géneros, listas de reproducción), imágenes (incluidas las miniaturas) y vídeo.

- *Settings*: la configuración y las preferencias del dispositivo en todo el sistema.

- *UserDictionary*: el contenido del diccionario definido por el usuario que se usa para la entrada de texto predictivo.

- *Voicemail*: el historial de mensajes de correo de voz.

## <a name="classes-overview"></a>Información general sobre clases

A continuación, se muestran las clases principales que se usan al trabajar con una instancia de `ContentProvider`:

[![Diagrama de clases de la aplicación de proveedor de contenido e interacciones de la aplicación de consumo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

En este diagrama, la instancia de `ContentProvider` implementa consultas y registra los URI que otras aplicaciones usan para localizar datos. La instancia de `ContentResolver` actúa como "proxy" en la instancia de `ContentProvider` (métodos de consulta, inserción, actualización y eliminación). El elemento `SQLiteOpenHelper` contiene los datos que la instancia de `ContentProvider` usa, pero no se expone directamente a las aplicaciones de consumo.
El elemento `CursorAdapter` pasa el cursor devuelto por `ContentResolver` que se va a mostrar en un control `ListView`. `UriMatcher` es una clase auxiliar que analiza los URI al procesar consultas.

A continuación, se describe el propósito de cada clase:

- **ContentProvider**: implementa los métodos de esta clase abstracta para exponer los datos. La API se pone a disposición de otras clases y aplicaciones a través del atributo URI que se agrega a la definición de clase.

- **SQLiteOpenHelper**: ayuda a implementar el almacén de datos de SQLite que expone la instancia de `ContentProvider`.

- **UriMatcher**: usa `UriMatcher` en la implementación de `ContentProvider` para ayudar a administrar los URI que se usan para consultar el contenido.

- **ContentResolver**: el código de consumo utiliza un elemento `ContentResolver` para acceder a una instancia de `ContentProvider`. Las dos clases combinadas se encargan de los problemas de comunicación entre procesos, lo que permite que los datos se compartan fácilmente entre las aplicaciones. El código de consumo nunca crea una clase `ContentProvider` explícitamente; en su lugar, se accede a los datos mediante la creación de un cursor basado en un URI expuesto por la aplicación `ContentProvider`.

- **CursorAdapter**: usa `CursorAdapter` o `SimpleCursorAdapter` para mostrar los datos a los que se accede a través de una instancia de `ContentProvider`.

La API de `ContentProvider` permite a los consumidores realizar una serie de operaciones en los datos, como:

- Consultar los datos para devolver listas o registros individuales.
- Modificar registros individuales.
- Agregar registros nuevos.
- Eliminar registros.

Este documento contiene un ejemplo que usa una instancia de `ContentProvider` proporcionada por el sistema, así como un ejemplo simple de solo lectura que implementa una instancia de `ContentProvider`personalizada.
