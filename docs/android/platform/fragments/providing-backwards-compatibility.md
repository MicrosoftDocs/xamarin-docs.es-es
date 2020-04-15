---
title: Proporción de compatibilidad con versiones anteriores mediante el paquete de soporte técnico de Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027296"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Proporción de compatibilidad con versiones anteriores mediante el paquete de soporte técnico de Android

La utilidad de los fragmentos se vería limitada sin la compatibilidad con versiones anteriores en los dispositivos anteriores a Android 3.0 (nivel de API 11). Para proporcionar esta funcionalidad, Google presentó la [Biblioteca de compatibilidad](https://developer.android.com/sdk/compatibility-library.html) (originalmente llamada *Biblioteca de compatibilidad de Android* cuando se lanzó) que transporta algunas de las API de versiones más recientes de Android a versiones anteriores de Android. Es el paquete de soporte técnico de Android que permite que los dispositivos que ejecutan Android 1.6 (nivel de API 4) en Android 2.3.3. (Nivel de API 10).

> [!NOTE]
> Solo los fragmentos `ListFragment` y `DialogFragment` están disponibles a través del paquete de soporte técnico de Android. No se admite ninguna de las demás subclases de fragmento, como `PreferenceFragment,` en el paquete de soporte técnico de Android. No funcionarán en aplicaciones anteriores a Android 3.0. 

## <a name="adding-the-support-package"></a>Adición del paquete de soporte técnico

El paquete de soporte técnico de Android no se agrega automáticamente a una aplicación de Xamarin.Android. Xamarin proporciona el [paquete NuGet de bibliotecas de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de compatibilidad a una aplicación de Xamarin.Android. Para incluir los paquetes de soporte técnico en la aplicación de Xamarin.Android, incluya el componente [Biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) en el proyecto de Xamarin.Android, como se ilustra en la siguiente captura de pantalla: 

[![Captura de pantalla del paquete de bibliotecas de compatibilidad de Android v4 que se va a agregar al proyecto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Una vez realizados estos pasos, se pueden usar fragmentos en versiones anteriores de Android. Ahora, las API de fragmentos funcionarán igual en estas versiones anteriores, con las siguientes excepciones: 

- **Cambio de la versión mínima de Android**: la aplicación ya no necesita tener como destino Android 3.0 o superior, como se muestra a continuación: 

    [![Captura de pantalla del destino de Android mínimo que se establece en el manifiesto de Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Extensión de FragmentActivity**: las actividades que hospedan fragmentos deben heredar ahora de `Android.Support.V4.App.FragmentActivity` y no de `Android.App.Activity`. 

- **Actualización de espacios de nombres**: las clases que heredan de `Android.App.Fragment` deben heredar ahora de `Android.Support.V4.App.Fragment`. Quite la instrucción using "`using Android.App;`" de la parte superior del archivo de código fuente y reemplácela por "`using Android.Support.V4.App`". 

- **Uso de SupportFragmentManager**: `Android.Support.V4.App.FragmentActivity` expone una propiedad `SupportingFragmentManager` que se debe utilizar para obtener una referencia a `FragmentManager`. Por ejemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Una vez realizados estos cambios, será posible ejecutar una aplicación basada en fragmentos en Android 1.6 o 2.x, así como en Honeycomb e Ice Cream Sandwich. 

## <a name="related-links"></a>Vínculos relacionados

- [NuGet de bibliotecas de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
