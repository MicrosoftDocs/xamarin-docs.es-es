---
title: Proporcionar compatibilidad con versiones anteriores del paquete de soporte de Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: f1f886f0e4c14f2da29342a2a651f91ed510fd25
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524263"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Proporcionar compatibilidad con versiones anteriores del paquete de soporte de Android

La utilidad de los fragmentos sería limitada sin compatibilidad con versiones anteriores a Android 3,0 (nivel de API 11). Para proporcionar esta funcionalidad, Google presentó la [biblioteca de soporte](https://developer.android.com/sdk/compatibility-library.html) (originalmente llamada *biblioteca de compatibilidad de Android* cuando se lanzó), que transporta algunas de las API de versiones más recientes de Android a versiones anteriores de Android. Es el paquete de soporte de Android que permite que los dispositivos que ejecutan Android 1,6 (nivel de API 4) en Android 2.3.3. (Nivel de API 10).

> [!NOTE]
> Solo los `ListFragment` `DialogFragment` y están disponibles a través del paquete de soporte de Android. Ninguna de las otras subclases de fragmento, como `PreferenceFragment,` , se admiten en el paquete de soporte de Android. No funcionarán en aplicaciones anteriores a Android 3,0. 


## <a name="adding-the-support-package"></a>Agregar el paquete de soporte

El paquete de soporte de Android no se agrega automáticamente a una aplicación de Xamarin. Android. Xamarin proporciona el [paquete NuGet de la biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) para simplificar la adición de las bibliotecas de soporte técnico a una aplicación de Xamarin. Android. Para incluir los paquetes de soporte en la aplicación de Xamarin. Android, incluya el componente [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) en el proyecto de Xamarin. Android, como se muestra en la siguiente captura de pantalla: 

[![Captura de pantalla del paquete de la biblioteca de compatibilidad de Android V4 que se va a agregar al proyecto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Una vez realizados estos pasos, se pueden usar fragmentos en versiones anteriores de Android. Las API de fragmentos funcionarán de la misma ahora en estas versiones anteriores, con las siguientes excepciones: 

- **Cambiar la versión mínima de Android** &ndash; La aplicación ya no necesita tener como destino Android 3,0 o superior, como se muestra a continuación: 

    [![Captura de pantalla del destino de Android mínimo que se establece en el manifiesto de Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Extender FragmentActivity** Las actividades que hospedan fragmentos deben heredar ahora `Android.Support.V4.App.FragmentActivity` de y no de `Android.App.Activity`. &ndash; 

- **Actualizar espacios de nombres** Las clases que heredan de `Android.App.Fragment` deben heredar ahora de `Android.Support.V4.App.Fragment`. &ndash; Quite la instrucción using " `using Android.App;` " en la parte superior del archivo de código fuente y reemplácela por " `using Android.Support.V4.App` ". 

- **Usar SupportFragmentManager** expone una propiedad que se debe utilizar para obtener una referencia a. `FragmentManager` &ndash; `Android.Support.V4.App.FragmentActivity` `SupportingFragmentManager` Por ejemplo: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Una vez realizados estos cambios, será posible ejecutar una aplicación basada en fragmentos en Android 1,6 o 2. x, así como en Honeycomb y Sandwich de hielo. 


## <a name="related-links"></a>Vínculos relacionados

- [NuGet de la biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
