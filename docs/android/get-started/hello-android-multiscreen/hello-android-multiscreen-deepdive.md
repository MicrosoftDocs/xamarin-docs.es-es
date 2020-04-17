---
title: 'Hello, Android Multiscreen: profundización'
description: En esta guía de dos partes, ampliaremos la aplicación Phoneword básica creada en la guía Hello, Android a fin de controlar una segunda pantalla. Durante el proceso, presentaremos los bloques de creación de aplicaciones de Android básicos. Se incluye un análisis más profundo de la arquitectura de Android como ayuda para entender mejor la funcionalidad y la estructura de las aplicaciones de Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E4150036-7760-4023-BD33-B7BDE7B7AF5B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4acbfe810abefd9a25721ddf59c9f4f197afdf28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020978"
---
# <a name="hello-android-multiscreen-deep-dive"></a>Hello, Android Multiscreen: profundización

_En esta guía de dos partes, ampliaremos la aplicación Phoneword básica creada en la guía Hello, Android a fin de controlar una segunda pantalla. Durante el proceso, presentaremos los bloques de compilación de aplicaciones de Android básicos. Se incluye un análisis más profundo de la arquitectura de Android como ayuda para entender mejor la funcionalidad y la estructura de las aplicaciones de Android._

En [Hello, Android Multiscreen Quickstart](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md) (Guía de inicio rápido de Hello, Android Multiscreen), compiló y ejecutó su primera aplicación de Xamarin.Android.

En esta guía explorará una arquitectura de Android más avanzada. Se explica la navegación de Android con *intenciones* y se describen las opciones de navegación del hardware de Android. Además, se analizan las nuevas adiciones a la aplicación Phoneword a medida que se desarrolla una perspectiva integral de la relación de la aplicación con el sistema operativo y otras aplicaciones.

## <a name="android-architecture-basics"></a>Conceptos básicos de la arquitectura de Android

En [Análisis detallado de Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md), aprendió que las aplicaciones de Android son programas únicos porque no tienen un único punto de entrada. En su lugar, el sistema operativo (u otra aplicación) inicia cualquiera de las actividades registradas de la aplicación que, a su vez, inicia el proceso de la aplicación. Este análisis detallado de la arquitectura de Android amplía sus conocimientos sobre cómo se construyen las aplicaciones de Android. Para ello, presenta los bloques de creación de aplicaciones de Android y sus funciones.

### <a name="android-application-building-blocks"></a>Bloques de compilación de aplicaciones de Android

Una aplicación de Android consta de una colección de clases de Android especiales denominadas *bloques de aplicación*, empaquetados con una serie de recursos de la aplicación (imágenes, temas, clases del asistente, etc.). Estos se coordinan mediante un archivo XML denominado *manifiesto de Android*.

Los bloques de aplicación son la espina dorsal de las aplicaciones de Android, ya que permiten hacer cosas que normalmente no se pueden hacer con una clase normal. Los dos más importantes son las _actividades_ y los _servicios_:

- **Actividad**: una actividad se corresponde con una pantalla con una interfaz de usuario, y como concepto se parece a una página web de una aplicación web. Por ejemplo, en una aplicación de suministro de noticias, la pantalla de inicio de sesión será la primera actividad, la lista desplazable de noticias sería otra actividad y la página de detalles de cada elemento sería la tercera actividad. Puede obtener más información sobre las actividades en la guía [Activity Lifecycle](~/android/app-fundamentals/activity-lifecycle/index.md) (Ciclo de vida de la actividad).

- **Servicio**: los servicios de Android admiten actividades. Para ello, asumen tareas de ejecución prolongada y las ejecutan en segundo plano. Los servicios no tiene una interfaz de usuario y se usan para controlar las tareas que no están vinculadas a pantallas &ndash; (por ejemplo, reproducir una canción en segundo plano o cargar fotografías en un servidor). Para obtener más información sobre los servicios, consulte las guías [Creating Services](~/android/app-fundamentals/services/index.md) (Crear servicios) y [Android Services](~/android/app-fundamentals/services/index.md) (Servicios de Android).

Una aplicación de Android podría no usar todos los tipos de bloques y, a menudo, tiene varios bloques de un mismo tipo. Por ejemplo, la aplicación Phoneword creada en [Hello, Android Quickstart](~/android/get-started/hello-android/hello-android-quickstart.md) (Guía de inicio rápido de Hello, Android) estaba formada por una sola actividad (pantalla) y algunos archivos de recursos. Una aplicación sencilla de reproductor de música podría tener varias actividades y un servicio para reproducir música cuando la aplicación está en segundo plano.

### <a name="intents"></a>Intenciones

Otro concepto fundamental en las aplicaciones de Android es la *intención*.
El diseño de Android se basa en el *principio de privilegios mínimos*, es decir, las aplicaciones tienen acceso únicamente a los bloques que necesitan para funcionar y tienen un acceso limitado a los bloques que componen el sistema operativo u otras aplicaciones. De forma similar, los bloques tienen un *acoplamiento flexible*, lo que significa que están diseñados para tener un conocimiento reducido y un acceso limitado a otros bloques (incluso a los bloques que forman parte de la misma aplicación).

Para comunicarse, los bloques de aplicación envían mensajes asincrónicos denominados *intenciones* de un lado a otro. Las intenciones contienen información sobre el bloque de recepción y, en ocasiones, algunos datos. Una intención que se envía desde un componente de una aplicación desencadena un evento que se produce en otro componente de la aplicación, lo que vincula a ambos componentes y les permite comunicarse. Mediante el envío de intenciones de un lado a otro, puede conseguir que los bloques coordinen acciones complejas, como iniciar la aplicación de cámara para que saque una fotografía y la guarde, recopilar información de ubicación o desplazarse de una pantalla a la siguiente.

### <a name="androidmanifestxml"></a>AndroidManifest.XML

Cuando se agrega un bloque a la aplicación, se registra con un archivo XML especial denominado **manifiesto de Android**. El manifiesto realiza un seguimiento de todos los bloques de una aplicación, así como de los requisitos de la versión, los permisos y las bibliotecas &ndash; vinculadas, es decir, todo lo que el sistema operativo necesita saber para que la aplicación se ejecute. El **manifiesto de Android** también funciona con las actividades y las intenciones para controlar qué acciones son apropiadas para una actividad determinada. Estas características avanzadas del manifiesto de Android se tratan en la guía [Working with the Android Manifest](~/android/platform/android-manifest.md) (Trabajar con el manifiesto de Android).

En la versión de una sola pantalla de la aplicación Phoneword, solo se usaron una actividad, una intención y `AndroidManifest.xml`, junto con recursos adicionales como iconos. En la versión de varias pantallas de Phoneword se agregó una actividad adicional, que se inició desde la primera actividad mediante una intención. En la sección siguiente se describe cómo ayudan las intenciones a crear la navegación en aplicaciones de Android.

## <a name="android-navigation"></a>Navegación de Android

Para ir de una pantalla a otra se han empleado intenciones. Es el momento de profundizar en este código para ver cómo funcionan las intenciones y entender su función en la navegación de Android.

### <a name="launching-a-second-activity-with-an-intent"></a>Iniciar una segunda actividad con una intención

En la aplicación Phoneword, se usó una intención para iniciar una segunda pantalla (actividad). En primer lugar, cree una intención. Para ello, pase el *contexto* actual (`this`, que hace referencia al **contexto** actual) y el tipo de bloque de aplicación que busca (`TranslationHistoryActivity`):

```csharp
Intent intent = new Intent(this, typeof(TranslationHistoryActivity));
```

El **contexto** es una interfaz para la información global sobre el entorno de aplicación. Permite a los objetos recién creados saber lo que pasa con la aplicación. Considere la intención como un mensaje: es como si proporcionara el nombre del destinatario del mensaje (`TranslationHistoryActivity`) y la dirección del receptor (`Context`).

Android proporciona una opción para asociar datos simples a una intención (los datos complejos se controlan de forma distinta). En el ejemplo de Phoneword, se usa `PutStringArrayExtra` para asociar una lista de números de teléfono a la intención y se llama a `StartActivity` en el destinatario de la intención. El código completo es similar al siguiente:

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", _phoneNumbers);
    StartActivity(intent);
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Otros conceptos presentados en Phoneword

La aplicación Phoneword presentó varios conceptos que no se tratan en esta guía. Entre estos conceptos se incluyen los siguientes:

**Recursos de cadena**: en la aplicación Phoneword, el texto de `TranslationHistoryButton` se estableció en `"@string/translationHistory"`. La sintaxis `@string` significa que el valor de la cadena se almacena en el _archivo de recursos de cadena_, **Strings.xml**. Se agregó en **Strings.xml** el valor siguiente para la cadena `translationHistory`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Call History</string>
</resources>
```

Para obtener más información sobre los recursos de cadena y otros recursos de Android, consulte la guía [Android Resources](~/android/app-fundamentals/resources-in-android/index.md) (Recursos de Android).

**ListView y ArrayAdapter**: _ListView_ es un componente de interfaz de usuario que proporciona una manera sencilla de presentar una lista desplazable de filas. Una instancia `ListView` necesita un _adaptador_ para llenarlo con los datos que contienen las vistas de la fila. Se usó la siguiente línea de código para rellenar la interfaz de usuario de `TranslationHistoryActivity`:

```csharp
this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
```

Los elementos ListView y los adaptadores están fuera del ámbito de este documento, pero se tratan de manera exhaustiva en la guía [ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) (Elementos ListView y adaptadores).
En [Relleno de ListView con datos](~/android/user-interface/layouts/list-view/populating.md) se trata específicamente el uso de las clases `ListActivity` y `ArrayAdapter` integradas para crear y rellenar un elemento `ListView` sin definir un diseño personalizado, como se hizo en el ejemplo de Phoneword.

## <a name="summary"></a>Resumen

Enhorabuena, ha completado su primera aplicación multipantalla de Android. En esta guía se presentaron los *bloques de creación de aplicaciones de Android* y las *intenciones*, y se usaron para compilar una aplicación multipantalla de Android. Ahora tiene la base necesaria para empezar a desarrollar sus propias aplicaciones de Xamarin.Android.

Después, aprenderá a compilar aplicaciones multiplataforma con Xamarin con la guía [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (Compilar aplicaciones multiplataforma).
