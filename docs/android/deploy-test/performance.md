---
title: Rendimiento de Xamarin.Android
description: Existen muchas técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.Android. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación. En este artículo se describen y se explican estas técnicas.
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e49c6d92d4c6bcabf45047321e183fe36d19096b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454720"
---
# <a name="xamarinandroid-performance"></a>Rendimiento de Xamarin.Android

_Existen muchas técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.Android. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación. En este artículo se describen y se explican estas técnicas._

## <a name="performance-overview"></a>Información general de rendimiento

El mal rendimiento de una aplicación se manifiesta de muchas formas. Puede hacer que parezca que una aplicación deja de responder, puede ocasionar un desplazamiento lento y puede reducir la duración de la batería. La optimización del rendimiento conlleva mucho más que la mera implementación de código eficaz. También debe tenerse en cuenta la experiencia de rendimiento de la aplicación del usuario. Por ejemplo, asegurarse de que las operaciones se ejecuten sin evitar que el usuario realice otras actividades puede ayudar a mejorar su experiencia.

Existen varias técnicas para aumentar el rendimiento, y la percepción de rendimiento, de las aplicaciones compiladas con Xamarin.Android. Son los siguientes:

- [Optimizar las jerarquías de diseño](#optimizelayout)
- [Optimizar las vistas de lista](#optimizelistviews)
- [Quitar controladores de eventos de actividades](#removeeventhandlers)
- [Limitar la duración de los servicios](#limitservices)
- [Liberar recursos cuando se notifique](#releaseresources)
- [Liberar recursos cuando se oculte la interfaz de usuario](#releaseresourcesuihidden)
- [Optimizar los recursos de imagen](#optimizeimages)
- [Eliminar recursos de imágenes sin usar](#disposeimages)
- [Evitar la aritmética de punto flotante](#avoidfloats)
- [Descartar cuadros de diálogo](#dismissdialogs)

> [!NOTE]
> Antes de leer este artículo, debería leer [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), donde se explican técnicas no específicas de una plataforma para mejorar el uso de memoria y el rendimiento de las aplicaciones compiladas con la plataforma Xamarin.

<a name="optimizelayout"></a>

## <a name="optimize-layout-hierarchies"></a>Optimizar las jerarquías de diseño

Cada diseño agregado a una aplicación exige inicialización, diseño y dibujo. El cálculo de diseño puede ser consumir muchos recursos si se anidan instancias [`LinearLayout`](xref:Android.Widget.LinearLayout) que usan el parámetro `weight`, ya que cada elemento secundario se medirá dos veces. El empleo de instancias anidadas de `LinearLayout` puede dar lugar a una jerarquía de vista profunda, lo que puede causar un mal rendimiento de los diseños que se inflan varias veces, como en una [`ListView`](xref:Android.Widget.ListView). Por lo tanto, es importante optimizar estos diseños, ya que las ventajas de rendimiento se multiplicarán.

Por ejemplo, imagine un [`LinearLayout`](xref:Android.Widget.LinearLayout) para una fila de vista de lista que tiene un icono, un título y una descripción. El `LinearLayout` contendrá una [`ImageView`](xref:Android.Widget.ImageView) y un `LinearLayout` vertical que contiene dos instancias [`TextView`](xref:Android.Widget.TextView):

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

Este diseño tiene tres niveles de profundidad y desperdicia recursos si se infla para cada fila [`ListView`](xref:Android.Widget.ListView). Pero se puede mejorar si se reduce el diseño, como se muestra en el ejemplo de código siguiente:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

La jerarquía de tres niveles anterior se ha reducido a una jerarquía de dos niveles y una sola instancia [`RelativeLayout`](xref:Android.Widget.RelativeLayout) ha reemplazado a dos instancias [`LinearLayout`](xref:Android.Widget.LinearLayout). Se obtendrá un aumento considerable del rendimiento cuando se infle el diseño de cada fila [`ListView`](xref:Android.Widget.ListView).

<a name="optimizelistviews"></a>

## <a name="optimize-list-views"></a>Optimizar las vistas de lista

Los usuarios esperan un desplazamiento sin problemas y unos tiempos de carga rápidos para las instancias de [`ListView`](xref:Android.Widget.ListView). Pero el rendimiento del desplazamiento puede verse afectado si cada fila de vista de lista contiene jerarquías de vista profundamente anidadas o si las filas de vista de lista contienen diseños complejos. Pero hay técnicas que se pueden usar para evitar un mal rendimiento de `ListView`:

- Vuelva a usar las vistas de fila. Para más información, vea [Volver a usar vistas de fila](#reuserowviews).
- Aligere los diseños, siempre que sea posible.
- Almacene en caché el contenido de fila recuperado de un servicio web.
- Evite el escalado de imagen.

Colectivamente, estas técnicas facilitan que las instancias de [`ListView`](xref:Android.Widget.ListView) se desplacen sin problemas.

<a name="reuserowviews"></a>

### <a name="reuse-row-views"></a>Volver a usar vistas de fila

Al mostrar cientos de filas en una [`ListView`](xref:Android.Widget.ListView), sería un desperdicio de memoria crear cientos de objetos [`View`](xref:Android.Views.View) cuando solo un pequeño número de ellos se muestra en pantalla a la vez. Así, se pueden cargar en la memoria solo los objetos `View` visibles en las filas de la pantalla y cargar el **contenido** en estos objetos reutilizados. Esto evita la creación de instancias de cientos de objetos adicionales, con lo que se ahorra tiempo y memoria.

Por lo tanto, cuando una fila desaparece de la pantalla, su vista puede colocarse en una cola para su reutilización, como se muestra en el ejemplo de código siguiente:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Cuando el usuario se desplaza, la [`ListView`](xref:Android.Widget.ListView) llama a la invalidación `GetView` para solicitar nuevas vistas para mostrar: si está disponible, pasa una vista sin usar en el parámetro `convertView`. Si este valor es `null`, el código crea una nueva instancia [`View`](xref:Android.Views.View), de lo contrario, se pueden volver a establecer y usar las propiedades `convertView`.

Para más información, vea [Row View Re-Use (Reutilización de vista de lista)](~/android/user-interface/layouts/list-view/populating.md#row-view-re-use) en [Populating a ListView with Data (Relleno de una ListView con datos)](~/android/user-interface/layouts/list-view/populating.md).

<a name="removeeventhandlers"></a>

## <a name="remove-event-handlers-in-activities"></a>Quitar controladores de eventos de actividades

Cuando se destruye una actividad en el runtime de Android, podría seguir activa en el runtime de Mono. Por lo tanto, quite los controladores de eventos de los objetos externos de `Activity.OnPause` para evitar que el runtime mantenga una referencia a una actividad que se ha destruido.

En una actividad, declare los controladores de eventos en el nivel de clase:

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

Luego implemente los controladores en la actividad, como en `OnResume`:

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

Cuando la actividad sale del estado de ejecución, se llama a `OnPause`. En la implementación de `OnPause`, quite los controladores de esta forma:

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices"></a>

## <a name="limit-the-lifespan-of-services"></a>Limitar la duración de los servicios

Cuando se inicia un servicio, Android mantiene el proceso de ese servicio en ejecución. Esto hace que el proceso consuma muchos recursos, porque su memoria no se puede paginar ni usar en otro lugar. Dejar un servicio en ejecución cuando no es necesario aumenta el riesgo de mal rendimiento de una aplicación debido a restricciones de memoria. También puede hacer que el cambio entre aplicaciones sea menos eficaz, ya que reduce el número de procesos que Android puede almacenar en caché.

Se puede limitar la duración de un servicio mediante un `IntentService`, que se cierra a sí mismo una vez controlado el propósito que lo inició.

<a name="releaseresources"></a>

## <a name="release-resources-when-notified"></a>Liberar recursos cuando se notifique

Durante el ciclo de vida de la aplicación, la devolución de llamada [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) envía una notificación si el dispositivo tiene poca memoria. Esta devolución de llamada debería implementarse para escuchar las notificaciones de nivel de memoria siguientes:

- [`TrimMemoryRunningModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate): la aplicación *podría* querer liberar algunos recursos innecesarios.
- [`TrimMemoryRunningLow`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningLow): la aplicación *debería* liberar algunos recursos innecesarios.
- [`TrimMemoryRunningCritical`](xref:Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical): la aplicación *debería* liberar tantos procesos no críticos como pudiera.

Además, cuando el proceso de la aplicación está almacenado en caché, la devolución de llamada [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) podría recibir las notificaciones de nivel de memoria siguientes:

- [`TrimMemoryBackground`](xref:Android.Content.ComponentCallbacks2.TrimMemoryBackground): liberar recursos que se puedan compilar rápida y eficazmente si el usuario vuelve a la aplicación.
- [`TrimMemoryModerate`](xref:Android.Content.ComponentCallbacks2.TrimMemoryModerate): liberar recursos que puedan ayudar al sistema a mantener otros procesos almacenados en caché para mejorar el rendimiento general.
- [`TrimMemoryComplete`](xref:Android.Content.ComponentCallbacks2.TrimMemoryComplete): si no se recupera más memoria pronto, el proceso de la aplicación se cerrará.

Se debería responder a las notificaciones mediante la liberación de recursos según el nivel recibido.

<a name="releaseresourcesuihidden"></a>

## <a name="release-resources-when-the-user-interface-is-hidden"></a>Liberar recursos cuando se oculte la interfaz de usuario

Libere recursos usados por la interfaz de usuario de la aplicación cuando el usuario vaya a otra aplicación, ya que eso puede aumentar considerablemente la capacidad de Android para procesos almacenados en caché, lo que a su vez puede afectar a la calidad de la experiencia de usuario.

Para recibir una notificación cuando el usuario salga de la interfaz de usuario, implemente la devolución de llamada [`OnTrimMemory`](xref:Android.App.Activity.OnTrimMemory*) en las clases `Activity` y escuche al nivel [`TrimMemoryUiHidden`](xref:Android.Content.ComponentCallbacks2.TrimMemoryUiHidden), que indica que la interfaz de usuario está oculta. Esta notificación se recibirá solo cuando *todos* los componentes de la interfaz de usuario de la aplicación estén ocultos. La liberación de recursos de la interfaz de usuario cuando se recibe esta notificación garantiza que si el usuario vuelve desde otra actividad de la aplicación, los recursos de la interfaz de usuario seguirán estando disponibles para reanudar rápidamente la actividad.

<a name="optimizeimages"></a>

## <a name="optimize-image-resources"></a>Optimizar los recursos de imagen

Las imágenes son uno de los recursos con más consumo usados por las aplicaciones y se suelen capturar en altas resoluciones. Por lo tanto, al mostrar una imagen, hágalo en la resolución necesaria para la pantalla del dispositivo. Si la imagen es de una resolución mayor que la pantalla, se debe reducir.

Para más información, vea [Optimizar los recursos de imagen](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) en la guía [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md).

<a name="disposeimages"></a>

## <a name="dispose-of-unused-image-resources"></a>Eliminar recursos de imágenes sin usar

Para ahorrar consumo de memoria, se recomienda eliminar los recursos de imágenes grandes que ya no sean necesarios. Es importante asegurarse de que las imágenes se eliminan correctamente. En lugar de usar una invocación explícita `.Dispose()`, puede aprovechar las ventajas de las instrucciones [using](/dotnet/csharp/language-reference/keywords/using-statement) para garantizar el uso correcto de objetos `IDisposable`. 

Por ejemplo, la clase [Bitmap](xref:Android.Graphics.Bitmap) implementa `IDisposable`. Si encapsula la creación de instancias de un objeto `BitMap` en un bloque `using`, se asegurará de que se elimine correctamente al salir del bloque:

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

Para más información sobre la liberación de recursos desechables, vea [Release IDisposable Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable) (Liberar los recursos de IDisposable).  

<a name="avoidfloats"></a>

## <a name="avoid-floating-point-arithmetic"></a>Evitar la aritmética de punto flotante

En los dispositivos Android, la aritmética de punto flotante es aproximadamente dos veces más lenta que la aritmética de enteros. Por lo tanto, si es posible, reemplace la aritmética de punto flotante por la aritmética de enteros. No obstante, no hay ninguna diferencia en cuanto a tiempo de ejecución entre la aritmética `float` y `double` en el hardware reciente.

> [!NOTE]
> Incluso para la aritmética de enteros, algunas CPU carecen de capacidades de división de hardware. Por lo tanto, la división de enteros y las operaciones de módulo se suelen realizar en el software.

<a name="dismissdialogs"></a>

## <a name="dismiss-dialogs"></a>Cuadros de diálogo Dismiss (Descartar)

Cuando use la clase [`ProgressDialog`](xref:Android.App.ProgressDialog) (o cualquier cuadro de diálogo o alerta), en lugar de llamar al método [`Hide`](xref:Android.App.Dialog.Hide*) cuando se complete la finalidad del cuadro de diálogo, llame al método [`Dismiss`](xref:Android.App.Dialog.Dismiss*). De lo contrario, el cuadro de diálogo permanecerá activo y perderá la actividad al mantener una referencia a ella.

## <a name="summary"></a>Resumen

En este artículo se describen y se explican técnicas para aumentar el rendimiento de las aplicaciones compiladas con Xamarin.Android. En conjunto, estas técnicas pueden reducir considerablemente la cantidad de trabajo que está realizando una CPU y la cantidad de memoria consumida por una aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)