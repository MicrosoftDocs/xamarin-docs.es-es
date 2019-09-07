---
title: Administración de fragmentos
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: f5baf8b46571c9b528fcc666a3f1f4530f18ee07
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761505"
---
# <a name="managing-fragments"></a>Administración de fragmentos

Para ayudar con la administración de fragmentos, Android `FragmentManager` proporciona la clase. Cada actividad tiene una instancia de `Android.App.FragmentManager` que buscará o cambiará dinámicamente sus fragmentos. Cada conjunto de estos cambios se conoce como una *transacción*y se realiza mediante una de las API contenidas en la clase `Android.App.FragmentTransation`, que `FragmentManager`está administrada por. Una actividad puede iniciar una transacción como esta:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Estos cambios en los fragmentos se realizan en la `FragmentTransaction` instancia de mediante métodos `Add()`como `Remove(),` `Commit()`y `Replace().` , a continuación, los cambios se aplican mediante. Los cambios en una transacción no se realizan inmediatamente.
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

Si una transacción se confirma después `Activity.OnSaveInstanceState()` de que se llame a, se producirá una excepción. Esto sucede porque cuando la actividad guarda su estado, Android también guarda el estado de los fragmentos hospedados. Si se confirman las transacciones de fragmentos después de este punto, el estado de estas transacciones se perderá cuando se restaure la actividad.

Es posible guardar las transacciones de fragmentos en la [pila de retroceso](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) de la actividad realizando una llamada a `FragmentTransaction.AddToBackStack()`. Esto permite al usuario navegar hacia atrás a través de los cambios de fragmento cuando se presiona el botón **atrás** . Sin una llamada a este método, los fragmentos que se quitan se destruirán y dejarán de estar disponibles si el usuario retrocede a través de la actividad.

En el ejemplo siguiente se muestra cómo utilizar `AddToBackStack` el método de para reemplazar un fragmento, a la vez que se conserva el estado del primer fragmento en la pila de retroceso: `FragmentTransaction`

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

*FragmentManager* conoce todos los fragmentos que están asociados a una actividad y proporciona dos métodos para ayudarle a encontrar estos fragmentos:

- **FindFragmentById** &ndash; Este método encontrará un fragmento mediante el identificador que se especificó en el archivo de diseño o el identificador del contenedor cuando el fragmento se agregó como parte de una transacción.

- **FindFragmentByTag** &ndash; Este método se usa para buscar un fragmento que tenga una etiqueta que se proporcionó en el archivo de diseño o que se agregó en una transacción.

Los fragmentos y las actividades hacen `FragmentManager`referencia a, por lo que se usan las mismas técnicas para comunicarse entre ellos. Una aplicación puede encontrar un fragmento de referencia mediante uno de estos dos métodos, convertir esa referencia al tipo adecuado y, a continuación, llamar directamente a los métodos del fragmento. El siguiente fragmento de código proporciona un ejemplo:

También es posible que la actividad use `FragmentManager` para encontrar fragmentos:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>Comunicación con la actividad

Es posible que un fragmento use la `Fragment.Activity` propiedad para hacer referencia a su host. Al convertir la actividad en un tipo más específico, es posible que una actividad llame a métodos y propiedades en su host, tal y como se muestra en el ejemplo siguiente:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
