---
title: ViewPager con vistas
description: ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar una interfaz de usuario que se pueda deslizar con ViewPager y PagerTabStrip mediante las vistas como páginas de datos (en una guía posterior se explica cómo usar los fragmentos de las páginas).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e4f4243c06d98eac6f3501c41b48508f260d4633
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456995"
---
# <a name="viewpager-with-views"></a>ViewPager con vistas

_ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar una interfaz de usuario que se pueda deslizar con ViewPager y PagerTabStrip mediante las vistas como páginas de datos (en una guía posterior se explica cómo usar los fragmentos de las páginas)._

## <a name="overview"></a>Información general

Esta guía es un tutorial que proporciona una demostración paso a paso sobre cómo usar `ViewPager` para implementar una galería de imágenes de árboles deciduous y perennes. En esta aplicación, el usuario se desliza a la izquierda y a la derecha a través de un "Catálogo de árboles" para ver las imágenes de árbol. En la parte superior de cada página del catálogo, el nombre del árbol se muestra en un `PagerTabStrip` y se muestra una imagen del árbol en `ImageView` . Un adaptador se utiliza para interactuar `ViewPager` con el modelo de datos subyacente. Esta aplicación implementa un adaptador derivado de `PagerAdapter` . 

Aunque `ViewPager` las aplicaciones basadas en suelen implementarse con `Fragment` s, hay algunos casos de uso relativamente sencillos en los que `Fragment` no es necesaria la complejidad adicional de s. Por ejemplo, la aplicación de la galería de imágenes básica que se ilustra en este tutorial no requiere el uso de `Fragment` s. Dado que el contenido es estático y el usuario solo se desliza hacia atrás y hacia delante entre distintas imágenes, la implementación se puede simplificar mediante el uso de vistas y diseños estándar de Android. 

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android denominado **TreePager** (vea [Hola, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información sobre la creación de nuevos proyectos de Android). A continuación, inicie el administrador de paquetes NuGet. (Para obtener más información sobre la instalación de paquetes NuGet, vea [Tutorial: incluir un NuGet en el proyecto](/visualstudio/mac/nuget-walkthrough)). Buscar e instalar la **biblioteca de compatibilidad de Android V4**: 

[![Captura de pantalla de la compatibilidad de NuGet V4 seleccionada en el administrador de paquetes NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

También se instalarán los paquetes adicionales reaquired de la **biblioteca de compatibilidad de Android V4**.

## <a name="add-an-example-data-source"></a>Agregar un origen de datos de ejemplo

En este ejemplo, el origen de datos del catálogo de árbol (representado por la `TreeCatalog` clase) proporciona el `ViewPager` objeto con el contenido del elemento. 
`TreeCatalog` contiene una colección lista para la creación de imágenes de árbol y títulos de árbol que el adaptador usará para crear `View` s. El `TreeCatalog` constructor no requiere ningún argumento:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

La colección de imágenes en `TreeCatalog` se organiza de tal forma que un indexador puede tener acceso a cada imagen. Por ejemplo, la siguiente línea de código recupera el identificador de recurso de imagen para la tercera imagen de la colección: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Dado que los detalles de implementación de `TreeCatalog` no son relevantes para comprender `ViewPager` , el `TreeCatalog` código no se muestra aquí. El código fuente de `TreeCatalog` está disponible en [TreeCatalog.CS](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Descargue este archivo de código fuente (o copie y pegue el código en un nuevo archivo **TreeCatalog.CS** ) y agréguelo al proyecto. Además, descargue y descomprima los [archivos de imagen](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) en la carpeta **Resources/drawable** e inclúyalo en el proyecto. 

## <a name="create-a-viewpager-layout"></a>Crear un diseño de ViewPager

Abra **Resources/layout/main. axml** y reemplace su contenido por el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>
```

Este XML define un `ViewPager` que ocupa toda la pantalla. Tenga en cuenta que debe usar el nombre completo **Android. support. V4. View. ViewPager** porque `ViewPager` está empaquetado en una biblioteca de soporte técnico. `ViewPager` solo está disponible desde la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); no está disponible en el Android SDK. 

## <a name="set-up-viewpager"></a>Configuración de ViewPager

Edite **MainActivity.CS** y agregue la siguiente `using` instrucción:

```csharp
using Android.Support.V4.View;
```

Reemplace el método `OnCreate` por el código siguiente:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

Este código hace lo siguiente:

1. Establece la vista a partir del recurso de diseño **Main. axml** .

2. Recupera una referencia a `ViewPager` del diseño.

3. Crea una instancia de un nuevo `TreeCatalog` como el origen de datos.

Al compilar y ejecutar este código, debería ver una pantalla similar a la siguiente captura de pantalla: 

[![Captura de pantalla de la aplicación que muestra un ViewPager vacío](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

En este punto, `ViewPager` está vacío porque no tiene un adaptador para tener acceso al contenido de **TreeCatalog**. En la sección siguiente, se crea un **PagerAdapter** para conectar el `ViewPager` a **TreeCatalog**. 

## <a name="create-the-adapter"></a>Crear el adaptador

`ViewPager` utiliza un objeto de controlador de adaptador que se encuentra entre `ViewPager` y el origen de datos (vea la ilustración del [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter)). Para tener acceso a estos datos, `ViewPager` es necesario proporcionar un adaptador personalizado derivado de `PagerAdapter` . Este adaptador rellena cada `ViewPager` página con el contenido del origen de datos. Dado que este origen de datos es específico de la aplicación, el adaptador personalizado es el código que entiende cómo obtener acceso a los datos. A medida que el usuario desliza el dedo por las páginas del `ViewPager` , el adaptador extrae información del origen de datos y la carga en las páginas para `ViewPager` que la muestre. 

Al implementar un `PagerAdapter` , debe invalidar lo siguiente:

- **InstantiateItem** &ndash; Crea la página ( `View` ) para una posición determinada y la agrega a la `ViewPager` colección de vistas de. 

- **DestroyItem** &ndash; Quita una página de una posición determinada.

- **Recuento** &ndash; Propiedad de solo lectura que devuelve el número de vistas (páginas) disponibles. 

- **IsViewFromObject** &ndash; Determina si una página está asociada a un objeto de clave concreto. (El método crea este objeto `InstantiateItem` ). En este ejemplo, el objeto de clave es el `TreeCatalog` objeto de datos.

Agregue un nuevo archivo denominado **TreePagerAdapter.CS** y reemplace su contenido por el código siguiente: 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Este código codifica como código auxiliar la `PagerAdapter` implementación esencial. En las secciones siguientes, cada uno de estos métodos se reemplaza por código de trabajo. 

### <a name="implement-the-constructor"></a>Implementar el constructor

Cuando la aplicación crea una instancia de `TreePagerAdapter` , proporciona un contexto ( `MainActivity` ) y una instancia de `TreeCatalog` . Agregue las siguientes variables de miembro y constructor a la parte superior de la `TreePagerAdapter` clase en **TreePagerAdapter.CS**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

El propósito de este constructor es almacenar el contexto y la `TreeCatalog` instancia que `TreePagerAdapter` utilizará. 

### <a name="implement-count"></a>Recuento de implementaciones

La `Count` implementación es relativamente simple: devuelve el número de árboles del catálogo de árbol. Reemplaza `Count` por el código siguiente:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

La `NumTrees` propiedad de `TreeCatalog` devuelve el número de árboles (número de páginas) del conjunto de datos.

### <a name="implement-instantiateitem"></a>Implementación de InstantiateItem

El `InstantiateItem` método crea la página para una posición determinada. También debe agregar la vista recién creada a la `ViewPager` colección de vistas de. Para que esto sea posible, se `ViewPager` pasa como el parámetro del contenedor. 

Reemplace el método `InstantiateItem` por el código siguiente:

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

Este código hace lo siguiente:

1. Crea una instancia de un nuevo `ImageView` para mostrar la imagen de árbol en la posición especificada. La aplicación `MainActivity` es el contexto que se pasará al `ImageView` constructor.

2. Establece el `ImageView` recurso en el `TreeCatalog` identificador de recurso de imagen en la posición especificada.

3. Convierte el contenedor pasado `View` en una `ViewPager` referencia.
    Tenga en cuenta que debe usar `JavaCast<ViewPager>()` para realizar correctamente esta conversión (esto es necesario para que Android realice una conversión de tipos comprobada en tiempo de ejecución).

4. Agrega la instancia de `ImageView` a `ViewPager` y devuelve `ImageView` al llamador.

Cuando `ViewPager` muestra la imagen en `position` , se muestra `ImageView` . Inicialmente, `InstantiateItem` se llama dos veces para rellenar las dos primeras páginas con vistas. A medida que el usuario se desplaza, se vuelve a llamar para mantener las vistas justo detrás y por adelante del elemento mostrado actualmente. 

### <a name="implement-destroyitem"></a>Implementación de DestroyItem

El `DestroyItem` método quita una página de la posición especificada. En las aplicaciones en las que puede cambiar la vista en cualquier posición determinada, `ViewPager` debe tener alguna manera de quitar una vista obsoleta en esa posición antes de reemplazarla por una nueva vista. En el `TreeCatalog` ejemplo, la vista en cada posición no cambia, por lo que una vista `DestroyItem` que quite simplemente se volverá a agregar cuando `InstantiateItem` se llame a para esa posición. (Para obtener una mayor eficacia, puede implementar un grupo para reciclar `View` que se volverá a mostrar en la misma posición). 

Reemplace el método `DestroyItem` por el código siguiente: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Este código hace lo siguiente:

1. Convierte el contenedor pasado `View` en una `ViewPager` referencia.

2. Convierte el objeto Java pasado ( `view` ) en un C# `View` ( `view as View` );

3. Quita la vista de `ViewPager` . 

### <a name="implement-isviewfromobject"></a>Implementación de IsViewFromObject

A medida que el usuario se desliza hacia la izquierda y la derecha a través de las páginas de contenido, `ViewPager` llama `IsViewFromObject` a para comprobar que el elemento secundario `View` en la posición especificada está asociado al objeto del adaptador para la misma posición (por lo tanto, el objeto del adaptador se denomina *clave de objeto*). En el caso de las aplicaciones relativamente sencillas, la asociación es una de las identidades que &ndash; la clave de objeto del adaptador en esa instancia es la vista que se devolvió previamente a `ViewPager` Via `InstantiateItem` . Sin embargo, para otras aplicaciones, la clave de objeto puede ser alguna otra instancia de clase específica del adaptador que esté asociada a la vista secundaria, pero no a la misma, que se `ViewPager` muestra en esa posición. Solo el adaptador sabe si la vista y la clave de objeto que se han pasado están asociadas. 

`IsViewFromObject` debe implementarse para `PagerAdapter` que funcione correctamente. Si `IsViewFromObject` devuelve `false` para una posición determinada, `ViewPager` no mostrará la vista en esa posición. En la `TreePager` aplicación, la clave de objeto devuelta por `InstantiateItem` *es* la página `View` de un árbol, por lo que el código solo tiene que comprobar la identidad (es decir, la clave de objeto y la vista son una y la misma). Reemplaza `IsViewFromObject` por el código siguiente: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>Agregar el adaptador a ViewPager

Ahora que `TreePagerAdapter` está implementado, es el momento de agregarlo al `ViewPager` . En **MainActivity.CS**, agregue la siguiente línea de código al final del `OnCreate` método:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Este código crea una instancia de `TreePagerAdapter` , pasando `MainActivity` como el contexto ( `this` ). La instancia de `TreeCatalog` se pasa al segundo argumento del constructor. La `ViewPager` `Adapter` propiedad de se establece en el objeto al que se ha creado una instancia `TreePagerAdapter` ; de esta forma, se conecta `TreePagerAdapter` a `ViewPager` . 

La implementación básica ahora se ha completado &ndash; compilar y ejecutar la aplicación. Debería ver que la primera imagen del catálogo de árbol aparece en la pantalla, como se muestra a la izquierda en la siguiente captura de pantalla. Deslice el dedo hacia la izquierda para ver más vistas de árbol y deslice el dedo hacia la derecha para retroceder por el catálogo de árbol: 

[![Capturas de pantallas de la aplicación TreePager pasar rápidamente por las imágenes de árbol](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Agregar un indicador de buscapersonas

Esta `ViewPager` implementación mínima muestra las imágenes del catálogo de árbol, pero no proporciona ninguna indicación de dónde se encuentra el usuario en el catálogo. El siguiente paso consiste en agregar un `PagerTabStrip` . `PagerTabStrip`Informa al usuario de la página que se muestra y proporciona el contexto de navegación al mostrar una sugerencia de las páginas anteriores y siguientes. `PagerTabStrip` está pensado para usarse como un indicador de la página actual de un `ViewPager` ; se desplaza y se actualiza a medida que el usuario desliza el dedo por cada página. 

Abra **Resources/layout/main. axml** y agregue un `PagerTabStrip` al diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` y `PagerTabStrip` están diseñados para funcionar conjuntamente. Al declarar un `PagerTabStrip` dentro de un `ViewPager` diseño, el buscará `ViewPager` automáticamente el `PagerTabStrip` y lo conectará al adaptador. Al compilar y ejecutar la aplicación, debería ver el vacío `PagerTabStrip` mostrado en la parte superior de cada pantalla: 

[![Primer plano captura de pantalla de un PagerTabStrip vacío](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>Mostrar un título

Para agregar un título a cada pestaña de página, implemente el `GetPageTitleFormatted` método en la `PagerAdapter` clase derivada de. `ViewPager` llama `GetPageTitleFormatted` a (si está implementado) para obtener la cadena de título que describe la página en la posición especificada. Agregue el método siguiente a la `TreePagerAdapter` clase en **TreePagerAdapter.CS**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Este código recupera la cadena de título de árbol de la página (posición) especificada en el catálogo de árbol, la convierte en un Java `String` y la devuelve a `ViewPager` . Al ejecutar la aplicación con este nuevo método, cada página muestra el título del árbol en `PagerTabStrip` . Debería ver el nombre del árbol en la parte superior de la pantalla sin subrayado: 

[![Capturas de pantallas de páginas con pestañas PagerTabStrips llenas de texto](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Puede deslizar el dedo hacia atrás y hacia delante para ver cada imagen de árbol con título en el catálogo. 

### <a name="pagertitlestrip-variation"></a>Variación de PagerTitleStrip

`PagerTitleStrip` es muy similar a, `PagerTabStrip` salvo que `PagerTabStrip` agrega un subrayado para la pestaña seleccionada actualmente. Puede reemplazar `PagerTabStrip` por `PagerTitleStrip` en el diseño anterior y volver a ejecutar la aplicación para ver su aspecto con `PagerTitleStrip` : 

[![PagerTitleStrip con subrayados quitados del texto](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Tenga en cuenta que el subrayado se quita al convertir a `PagerTitleStrip` . 

## <a name="summary"></a>Resumen

En este tutorial se proporciona un ejemplo paso a paso de cómo compilar una `ViewPager` aplicación basada en Basic sin usar `Fragment` s. Se presentó un ejemplo de origen de datos que contiene cadenas de imágenes y leyendas, un `ViewPager` diseño para mostrar las imágenes y una `PagerAdapter` subclase que conecta `ViewPager` con el origen de datos. Para ayudar al usuario a navegar por el conjunto de datos, se incluyeron instrucciones que explican cómo agregar un `PagerTabStrip` o `PagerTitleStrip` para mostrar el título de la imagen en la parte superior de cada página. 

## <a name="related-links"></a>Vínculos relacionados

- [TreePager (ejemplo)](/samples/xamarin/monodroid-samples/userinterface-treepager)