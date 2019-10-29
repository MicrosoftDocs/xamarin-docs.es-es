---
title: Proporcionar compatibilidad con versiones anteriores del paquete de soporte de Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027296"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Proporcionar compatibilidad con versiones anteriores del paquete de soporte de Android

La utilidad de los fragmentos sería limitada sin compatibilidad con versiones anteriores a Android 3,0 (nivel de API 11). Para proporcionar esta funcionalidad, Google presentó la [biblioteca de soporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente llamada *biblioteca de compatibilidad de Android* cuando se lanzó), que transporta algunas de las API de versiones más recientes de Android a versiones anteriores de Android. Es el paquete de soporte de Android que permite que los dispositivos que ejecutan Android 1,6 (nivel de API 4) en Android 2.3.3. (Nivel de API 10).

> [!NOTE]
> Solo los `ListFragment` y los `DialogFragment` están disponibles a través del paquete de soporte de Android. No se admite ninguna de las demás subclases de fragmento, como el `PreferenceFragment,` en el paquete de soporte de Android. No funcionarán en aplicaciones anteriores a Android 3,0. 

## <a name="adding-the-support-package"></a>Agregar el paquete de soporte

El paquete de soporte de Android no se agrega automáticamente a una aplicación de Xamarin. Android. Xamarin proporciona el [paquete NuGet de la biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de soporte técnico a una aplicación de Xamarin. Android. Para incluir los paquetes de soporte en la aplicación de Xamarin. Android, incluya el componente [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) en el proyecto de Xamarin. Android, como se muestra en la siguiente captura de pantalla: 

[![captura de pantalla del paquete de la biblioteca de compatibilidad de Android V4 que se va a agregar al proyecto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Una vez realizados estos pasos, se pueden usar fragmentos en versiones anteriores de Android. Las API de fragmentos funcionarán de la misma ahora en estas versiones anteriores, con las siguientes excepciones: 

- **Cambie la versión mínima de android** &ndash; la aplicación ya no necesita tener como destino Android 3,0 o superior, como se muestra a continuación: 

    [![captura de pantalla del destino de Android mínimo que se establece en el manifiesto de Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Extienda FragmentActivity** &ndash; las actividades que hospedan fragmentos deben heredar ahora de `Android.Support.V4.App.FragmentActivity` y no de `Android.App.Activity`. 

- **Actualice los espacios de nombres** &ndash; clases que heredan de `Android.App.Fragment` deben heredar de `Android.Support.V4.App.Fragment`. Quite la instrucción using "`using Android.App;`" en la parte superior del archivo de código fuente y reemplácela por "`using Android.Support.V4.App`". 

- **Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` expone una propiedad `SupportingFragmentManager` que se debe utilizar para obtener una referencia al `FragmentManager`. Por ejemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Una vez realizados estos cambios, será posible ejecutar una aplicación basada en fragmentos en Android 1,6 o 2. x, así como en Honeycomb y Sandwich de hielo. 

## <a name="related-links"></a>Vínculos relacionados

- [NuGet de la biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
