---
title: Trabajar con valores predeterminados de usuario en Xamarin. iOS
description: En este artículo se explica cómo trabajar con NSUserDefaults para guardar la configuración predeterminada en una aplicación o extensión de Xamarin iOS. Describe NSUserDefaults en un nivel alto y describe cómo leer y escribir valores.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: 6ff697964cbc057b4a3f905394d147d7c132d79b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281955"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Trabajar con valores predeterminados de usuario en Xamarin. iOS

_En este artículo se explica cómo trabajar con NSUserDefault para guardar la configuración predeterminada en una aplicación o extensión de Xamarin. iOS._


La `NSUserDefaults` clase proporciona una manera para que las aplicaciones y extensiones de iOS interactúen con el sistema de valores predeterminados de todo el sistema mediante programación. Al usar el sistema de valores predeterminados, el usuario puede configurar el comportamiento o el estilo de una aplicación para que se adapte a sus preferencias (según el diseño de la aplicación). Por ejemplo, para presentar los datos en métricas frente a medidas de los Imperials o seleccionar un tema de la interfaz de usuario determinado.

Cuando se usa con grupos de `NSUserDefaults` aplicaciones, también proporciona una manera de comunicarse entre aplicaciones (o extensiones) dentro de un grupo determinado.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Acerca de los valores predeterminados del usuario

Como se indicó anteriormente, los valores predeterminados de usuario (`NSUserDefaults`) se pueden agregar a una aplicación (o extensión) y usarse para proporcionar las opciones configurables que el usuario final puede modificar para ajustar la apariencia o el funcionamiento de la aplicación en tiempo de ejecución.

Cuando la aplicación se ejecuta por primera `NSUserDefaults` vez, lee las claves y los valores de la base de datos de los valores predeterminados del usuario de la aplicación y los almacena en memoria caché para evitar abrir y leer la base de datos cada vez que se requiere un valor. 

> [!IMPORTANT]
> Apple ya no recomienda que el desarrollador llame `Synchronize` al método para sincronizar la memoria caché en memoria con la base de datos directamente. En su lugar, se le llamará automáticamente a intervalos periódicos para mantener la memoria caché en memoria sincronizada con la base de datos predeterminada de un usuario.

La `NSUserDefaults` clase contiene varios métodos útiles para leer y escribir valores de preferencia para tipos de datos comunes como: String, integer, Float, Boolean y URL. Otros tipos de datos se pueden archivar mediante `NSData`y, después, leer o escribir en la base de datos de valores predeterminados del usuario. Para obtener más información, consulte la [Guía de programación de preferencias y configuración](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)de Apple.

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Obtener acceso a la instancia compartida de NSUserDefaults 

La instancia de valores predeterminados de usuario compartidos proporciona acceso a los valores predeterminados del usuario para el usuario actual del dispositivo. Si el objeto de valores predeterminados compartidos no existe, se crea la primera vez que se tiene acceso a él y se inicializa con la siguiente información:

- Que `NSArgumentDomain` consta de los valores predeterminados analizados desde la aplicación actual.
- El dominio del identificador de lote de la aplicación.
- Que `NSGlobalDomain` consta de los valores predeterminados compartidos por todas las aplicaciones.
- Un dominio independiente para cada uno de los idiomas preferidos del usuario.
- `NSRegistrationDomain` Con un conjunto de valores predeterminados temporales que puede modificar la aplicación para asegurarse de que las búsquedas siempre se realizan correctamente.

Para tener acceso a la instancia de valores predeterminados de usuario compartidos, utilice el siguiente código:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acceso a una instancia de NSUserDefaults de grupo de aplicaciones

Como se indicó anteriormente, mediante el uso de `NSUserDefaults` grupos de aplicaciones, se puede usar para la comunicación entre aplicaciones (o extensiones) dentro de un grupo determinado. En primer lugar, debe asegurarse de que el grupo de aplicaciones y los identificadores de aplicaciones necesarios se hayan configurado correctamente en la sección **certificados, identificadores & perfiles** del [centro de desarrollo de iOS](https://developer.apple.com/devcenter/ios/) y se hayan instalado en el entorno de desarrollo.

A continuación, la aplicación o los proyectos de extensión deben tener uno de los identificadores de aplicación válidos creados anteriormente `Entitlements.plist` y el archivo debe incluirse en el grupo de aplicaciones con los grupos de aplicaciones habilitados y especificados.

Con esto en su lugar, se puede tener acceso a los valores predeterminados de usuario del grupo de aplicaciones compartido mediante el código siguiente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Donde `group.com.xamarin.todaysharing` es el grupo de aplicaciones creado en **certificados, identificadores & perfiles** a los que desea obtener acceso. Para obtener más información, consulte el [las capacidades de grupos de aplicación](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Leer valores predeterminados

Una vez que haya tenido acceso a la base de datos predeterminada del usuario deseado, puede leer los valores de los valores predeterminados mediante pares clave-valor y varios métodos útiles en función del tipo de datos que se están leyendo:

- `ArrayForKey`: Devuelve una matriz de `NSObjects` para el valor de clave especificado.
- `BoolForKey`: Devuelve un valor booleano para la clave especificada.
- `DataForKey`: Devuelve un `NSData` objeto para la clave especificada.
- `DictionaryForKey`: Devuelve un `NSDictionary` para la clave especificada.
- `DoubleForKey`: Devuelve un valor Double para la clave especificada.
- `FloatForKey`: Devuelve un valor Float para la clave especificada.
- `IntForKey`: Devuelve un valor entero para la clave especificada.
- `StringArrayForKey`: Devuelve una matriz de `String` objetos del valor de clave especificado.
- `StringForKey`: Devuelve un valor de cadena para la clave especificada.
- `URLForKey`: Devuelve un `NSUrl` valor para la clave especificada.

Por ejemplo, el código siguiente leerá un valor booleano de los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Escribir valores predeterminados

Al igual que la lectura de los valores anteriores, una vez que haya tenido acceso a la base de datos predeterminada del usuario, puede escribir valores en los valores predeterminados mediante pares de clave-valor y varios métodos útiles basados en el tipo de datos que se escriben:

- `SetBool`: Escribe el valor booleano dado en la clave especificada.
- `SetDouble`: Escribe el valor Double especificado en la clave especificada.
- `SetFloat`: Escribe el valor Float dado en la clave especificada.
- `SetString`: Escribe el valor de cadena especificado en la clave especificada.
- `SetURL`: Escribe el valor de dirección`NSUrl`URL () dado en la clave especificada.

Por ejemplo, el código siguiente escribiría un valor booleano en los valores predeterminados del usuario:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Cuando la aplicación se ejecuta por primera `NSUserDefaults` vez, lee las claves y los valores de la base de datos de los valores predeterminados del usuario de la aplicación y los almacena en memoria caché para evitar abrir y leer la base de datos cada vez que se requiere un valor.



<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha `NSUserDefaults` tratado la clase y cómo se puede usar para proporcionar un conjunto de opciones que el usuario final puede usar para configurar la aplicación de Xamarin. iOS. Además, se ha tratado el uso de grupos de aplicaciones para comunicarse entre una extensión y su aplicación primaria o entre aplicaciones en un grupo.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Guía de programación de preferencias y configuraciones](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
