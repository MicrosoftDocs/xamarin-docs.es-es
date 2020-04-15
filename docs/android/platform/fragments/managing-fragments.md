---
title: Administración de fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027317"
---
# <a name="managing-fragments"></a>Administración de fragmentos

Para ayudar con la administración de fragmentos, Android proporciona la clase `FragmentManager`. Cada actividad tiene una instancia de `Android.App.FragmentManager` que buscará o cambiará dinámicamente sus fragmentos. Cada conjunto de estos cambios se conoce como *transacción* y se realiza mediante una de las API contenidas en la clase `Android.App.FragmentTransation`, que se administra mediante `FragmentManager`. Una actividad puede iniciar una transacción como esta:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Estos cambios en los fragmentos se realizan en la instancia de `FragmentTransaction` mediante métodos como `Add()`, `Remove(),` y `Replace().`. A continuación, se aplican los cambios mediante `Commit()`. Los cambios en una transacción no se realizan inmediatamente.
En su lugar, están programados para ejecutarse en el subproceso de interfaz de usuario de la actividad lo antes posible.

En el ejemplo siguiente se muestra cómo agregar un fragmento a un contenedor existente:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Si una transacción se confirma después de que se llame a `Activity.OnSaveInstanceState()`, se generará una excepción. Esto sucede porque, cuando la actividad guarda su estado, Android también guarda el estado de los fragmentos hospedados. Si se confirman las transacciones de fragmentos después de este punto, el estado de estas transacciones se perderá cuando se restaure la actividad.

Es posible guardar las transacciones de fragmentos en la [pila de retroceso](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) de la actividad mediante una llamada a `FragmentTransaction.AddToBackStack()`. Esto permite al usuario navegar hacia atrás por cambios de fragmento cuando se presiona el botón **Atrás**. Sin una llamada a este método, los fragmentos que se quitan se destruyen y dejarán de estar disponibles si el usuario retrocede por la actividad.

En el ejemplo siguiente se muestra cómo usar el método `AddToBackStack` de `FragmentTransaction` para reemplazar un fragmento, a la vez que se conserva el estado del primer fragmento en la pila de retroceso:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```

## <a name="communicating-with-fragments"></a>Comunicación con fragmentos

*FragmentManager* tiene toda la información sobre todos los fragmentos que están conectados a una actividad y proporciona dos métodos para ayudar a buscarlos:

- **FindFragmentById**: este método busca un fragmento con el identificador que se especificó en el archivo de diseño o el identificador del contenedor cuando el fragmento se agregó como parte de una transacción.

- **FindFragmentByTag**: este método se usa para buscar un fragmento que tenga una etiqueta que se proporcionó en el archivo de diseño o que se agregó en una transacción.

Tanto los fragmentos como las actividades hacen referencia a `FragmentManager`, por lo que se usan las mismas técnicas para comunicarse entre ellos. Una aplicación puede buscar un fragmento de referencia mediante uno de estos dos métodos, convertir esa referencia al tipo adecuado y, a continuación, llamar directamente a los métodos del fragmento. El siguiente fragmento de código proporciona un ejemplo:

También es posible que la actividad use `FragmentManager` para buscar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Comunicarse con la actividad

Es posible que un fragmento use la propiedad `Fragment.Activity` para hacer referencia a su host. Al convertir la actividad en un tipo más específico, es posible que una actividad llame a métodos y propiedades en su host, tal y como se muestra en el ejemplo siguiente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
