---
title: ViewPager con fragmentos
description: ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar una interfaz de usuario que se pueda deslizar con ViewPager, usando fragmentos como páginas de datos.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d0fdfa44ce6caa0c5f0e0aa2eed6406e606eacc4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029029"
---
# <a name="viewpager-with-fragments"></a>ViewPager con fragmentos

_ViewPager es un administrador de diseño que le permite implementar la navegación de gestural. La navegación Gestural permite al usuario desplazarse a la izquierda y a la derecha para recorrer las páginas de datos. En esta guía se explica cómo implementar una interfaz de usuario que se pueda deslizar con ViewPager, usando fragmentos como páginas de datos._

## <a name="overview"></a>Información general

`ViewPager` se usa a menudo junto con fragmentos para que sea más fácil administrar el ciclo de vida de cada página en el `ViewPager`. En este tutorial, `ViewPager` se usa para crear una aplicación llamada **FlashCardPager** que presenta una serie de problemas matemáticos en tarjetas flash. Cada tarjeta flash se implementa como un fragmento. El usuario se desliza rápidamente a la izquierda y a la derecha a través de las tarjetas flash y se pulsa un problema matemático para revelar su respuesta. Esta aplicación crea una instancia de `Fragment` para cada tarjeta flash e implementa un adaptador derivado de `FragmentPagerAdapter`. En [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la mayor parte del trabajo se realizó en `MainActivity` métodos del ciclo de vida. En **FlashCardPager**, la mayor parte del trabajo lo realizará un `Fragment` en uno de sus métodos de ciclo de vida. 

En esta guía no se tratan los conceptos básicos de los fragmentos &ndash; si aún no está familiarizado con los fragmentos de Xamarin. Android, consulte [fragmentos](~/android/platform/fragments/index.md) para ayudarle a empezar a trabajar con fragmentos. 

## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Cree un nuevo proyecto de Android llamado **FlashCardPager**. A continuación, inicie el administrador de paquetes NuGet (para obtener más información sobre la instalación de paquetes NuGet, consulte [Tutorial: incluir un NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Busque e instale el paquete **Xamarin. Android. support. V4** tal y como se explica en [Viewpager y vistas](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 

## <a name="add-an-example-data-source"></a>Agregar un origen de datos de ejemplo

En **FlashCardPager**, el origen de datos es una baraja de tarjetas flash representadas por la clase `FlashCardDeck`; Este origen de datos proporciona el `ViewPager` con el contenido del elemento. `FlashCardDeck` contiene una colección preparada de problemas y respuestas matemáticas. El constructor `FlashCardDeck` no requiere ningún argumento: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La colección de tarjetas flash en `FlashCardDeck` está organizada de manera que un indexador puede acceder a cada tarjeta flash. Por ejemplo, la siguiente línea de código recupera el cuarto problema de la tarjeta flash en la baraja: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Esta línea de código recupera la respuesta correspondiente al problema anterior:

```csharp
string answer = flashCardDeck[3].Answer;
```

Dado que los detalles de implementación de `FlashCardDeck` no son relevantes para comprender `ViewPager`, el código de `FlashCardDeck` no se muestra aquí.
El código fuente para `FlashCardDeck` está disponible en [FlashCardDeck.CS](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Descargue este archivo de código fuente (o copie y pegue el código en un nuevo archivo **FlashCardDeck.CS** ) y agréguelo al proyecto.

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

Este XML define un `ViewPager` que ocupa toda la pantalla. Tenga en cuenta que debe usar el nombre completo **Android. support. V4. View. ViewPager** porque `ViewPager` está empaquetado en una biblioteca de soporte. `ViewPager` solo está disponible desde la [biblioteca de compatibilidad de Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); no está disponible en el Android SDK.

## <a name="set-up-viewpager"></a>Configuración de ViewPager

Edite **MainActivity.CS** y agregue las siguientes instrucciones de `using`:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Cambie la declaración de clase `MainActivity` para que se derive de `FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` se deriva de`FragmentActivity` (en lugar de `Activity`) porque `FragmentActivity` sabe cómo administrar la compatibilidad de los fragmentos. Reemplace el método `OnCreate` con el código siguiente: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Este código hace lo siguiente:

1. Establece la vista a partir del recurso de diseño **Main. axml** .

2. Recupera una referencia al `ViewPager` del diseño.

3. Crea una instancia de un nuevo `FlashCardDeck` como origen de datos.

Al compilar y ejecutar este código, debería ver una pantalla similar a la siguiente captura de pantalla: 

[![captura de pantalla de la aplicación FlashCardPager con ViewPager vacío](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

En este punto, la `ViewPager` está vacía porque no hay los fragmentos que se usan para rellenar el `ViewPager`y carece de un adaptador para crear estos fragmentos a partir de los datos de **FlashCardDeck**. 

En las secciones siguientes, se crea un `FlashCardFragment` para implementar la funcionalidad de cada tarjeta flash y se crea un `FragmentPagerAdapter` para conectar el `ViewPager` a los fragmentos creados a partir de los datos del `FlashCardDeck`. 

## <a name="create-the-fragment"></a>Crear el fragmento

Cada tarjeta flash se administrará mediante un fragmento de la interfaz de usuario denominado `FlashCardFragment`. la vista de `FlashCardFragment`mostrará la información contenida en una sola tarjeta flash. Cada instancia de `FlashCardFragment` se hospedará en el `ViewPager`. 
la vista de `FlashCardFragment`consistirá en una `TextView` que muestra el texto del problema de la tarjeta flash. Esta vista implementará un controlador de eventos que utiliza un `Toast` para mostrar la respuesta cuando el usuario puntea la pregunta de la tarjeta flash. 

### <a name="create-the-flashcardfragment-layout"></a>Crear el diseño de FlashCardFragment

Antes de que se pueda implementar `FlashCardFragment`, debe definirse su diseño. Este diseño es un diseño de contenedor de fragmentos para un único fragmento. Agregue un nuevo diseño de Android a **recursos/diseño** denominado **flashcard_layout. axml**. Abra **Resources/layout/flashcard_layout. axml** y reemplace su contenido por el código siguiente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Este diseño define un único fragmento de la tarjeta flash; cada fragmento se compone de un `TextView` que muestra un problema matemático mediante una fuente grande (100SP). Este texto se centra vertical y horizontalmente en la tarjeta flash. 

### <a name="create-the-initial-flashcardfragment-class"></a>Crear la clase FlashCardFragment inicial

Agregue un nuevo archivo denominado **FlashCardFragment.CS** y reemplace su contenido por el código siguiente:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

En este código se muestra la definición de `Fragment` esenciales que se usará para mostrar una tarjeta flash. Tenga en cuenta que `FlashCardFragment` se deriva de la versión de la biblioteca de compatibilidad de `Fragment` definida en `Android.Support.V4.App.Fragment`. El constructor está vacío, por lo que el Factory Method `newInstance` se usa para crear un nuevo `FlashCardFragment` en lugar de un constructor. 

El método de ciclo de vida `OnCreateView` crea y configura el `TextView`. Aumenta el diseño del `TextView` del fragmento y devuelve el `TextView` inflado al autor de la llamada. `LayoutInflater` y `ViewGroup` se pasan a `OnCreateView` para que pueda aumentar el diseño. El paquete de `savedInstanceState` contiene datos que `OnCreateView` utiliza para volver a crear el `TextView` a partir de un estado guardado. 

La vista del fragmento se infla explícitamente mediante la llamada a `inflater.Inflate`. El argumento `container` es el elemento primario de la vista y la marca `false` indica al informador que no agregue la vista inflada al elemento primario de la vista (se agregará cuando `ViewPager` llame al método `GetItem` del adaptador más adelante en este tutorial). 

### <a name="add-state-code-to-flashcardfragment"></a>Agregar código de estado a FlashCardFragment

Al igual que una actividad, un fragmento tiene un `Bundle` que usa para guardar y recuperar su estado. En **FlashCardPager**, este `Bundle` se usa para guardar el texto de la pregunta y la respuesta de la tarjeta flash asociada. En **FlashCardFragment.CS**, agregue las siguientes claves de `Bundle` a la parte superior de la definición de clase de `FlashCardFragment`: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modifique el Factory Method de `newInstance` para que cree un objeto `Bundle` y utilice las teclas anteriores para almacenar la pregunta y el texto de respuesta que se han pasado en el fragmento una vez creada su instancia: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Modifique el método de ciclo de vida del fragmento `OnCreateView` para recuperar esta información de la agrupación pasada y cargar el texto de la pregunta en el `TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

La variable `answer` no se usa aquí, pero se utilizará más adelante cuando se agregue código de controlador de eventos a este archivo. 

## <a name="create-the-adapter"></a>Crear el adaptador

`ViewPager` usa un objeto de controlador de adaptador que se encuentra entre el `ViewPager` y el origen de datos (vea la ilustración en el artículo del [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) de ViewPager). Para tener acceso a estos datos, `ViewPager` requiere que proporcione un adaptador personalizado derivado de `PagerAdapter`. Dado que en este ejemplo se usan fragmentos, se usa un `FragmentPagerAdapter` de &ndash; de `FragmentPagerAdapter` se deriva de `PagerAdapter`. 
`FragmentPagerAdapter` representa cada página como una `Fragment` que se mantiene de forma persistente en el administrador de fragmentos durante el tiempo que el usuario puede volver a la página. A medida que el usuario desliza el dedo por las páginas del `ViewPager`, el `FragmentPagerAdapter` extrae información del origen de datos y la usa para crear `Fragment`s para que la `ViewPager` la muestre. 

Al implementar un `FragmentPagerAdapter`, debe invalidar lo siguiente:

- **Count** &ndash; propiedad de solo lectura que devuelve el número de vistas (páginas) disponibles.

- **GetItem** &ndash; devuelve el fragmento que se va a mostrar para la página especificada.

Agregue un nuevo archivo denominado **FlashCardDeckAdapter.CS** y reemplace su contenido por el código siguiente:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Este código codifica como código auxiliar la implementación de `FragmentPagerAdapter` fundamental. En las secciones siguientes, cada uno de estos métodos se reemplaza por código de trabajo. El propósito del constructor es pasar el administrador de fragmentos al constructor de clase base del `FlashCardDeckAdapter`. 

### <a name="implement-the-adapter-constructor"></a>Implementar el constructor de adaptador

Cuando la aplicación crea instancias del `FlashCardDeckAdapter`, proporciona una referencia al administrador de fragmentos y una `FlashCardDeck`con instancias. Agregue la siguiente variable miembro a la parte superior de la clase `FlashCardDeckAdapter` en **FlashCardDeckAdapter.CS**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Agregue la siguiente línea de código al constructor `FlashCardDeckAdapter`: 

```csharp
this.flashCardDeck = flashCards;
```

Esta línea de código almacena la instancia de `FlashCardDeck` que usará el `FlashCardDeckAdapter`. 

### <a name="implement-count"></a>Recuento de implementaciones

La implementación de `Count` es relativamente sencilla: devuelve el número de tarjetas flash de la baraja de tarjetas flash. Reemplaza `Count` por el código siguiente: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

La propiedad `NumCards` de `FlashCardDeck` devuelve el número de tarjetas flash (número de fragmentos) del conjunto de datos. 

### <a name="implement-getitem"></a>Implementar GetItem

El método `GetItem` devuelve el fragmento asociado a la posición especificada. Cuando se llama a `GetItem` para una posición en la baraja de la tarjeta flash, devuelve un `FlashCardFragment` configurado para mostrar el problema de la tarjeta flash en esa posición. Reemplace el método `GetItem` con el código siguiente: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Este código hace lo siguiente:

1. Busca la cadena de problema matemático en la baraja de `FlashCardDeck` para la posición especificada. 

2. Busca la cadena de respuesta en la baraja de `FlashCardDeck` para la posición especificada. 

3. Llama al `newInstance`de Factory Method `FlashCardFragment`, pasando el problema de la tarjeta flash y las cadenas de respuesta. 

4. Crea y devuelve una nueva tarjeta flash `Fragment` que contiene el texto de la pregunta y la respuesta para esa posición. 

Cuando el `ViewPager` representa el `Fragment` en `position`, muestra el `TextBox` que contiene la cadena de problema matemático que reside en `position` en la baraja de tarjetas flash. 

## <a name="add-the-adapter-to-the-viewpager"></a>Agregar el adaptador a ViewPager

Ahora que está implementado el `FlashCardDeckAdapter`, es el momento de agregarlo al `ViewPager`. En **MainActivity.CS**, agregue la siguiente línea de código al final del método `OnCreate`:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Este código crea una instancia del `FlashCardDeckAdapter`, pasando el `SupportFragmentManager` en el primer argumento. (La propiedad `SupportFragmentManager` de FragmentActivity se utiliza para obtener una referencia a la &ndash; de `FragmentManager` para obtener más información sobre el `FragmentManager`, vea [administrar fragmentos](~/android/platform/fragments/managing-fragments.md)). 

La implementación principal ahora está completa &ndash; compilar y ejecutar la aplicación.
Debería ver que la primera imagen de la baraja de la tarjeta flash aparece en la pantalla, como se muestra a la izquierda en la siguiente captura de pantalla. Deslice el dedo hacia la izquierda para ver más tarjetas flash y luego deslice el dedo hacia la derecha para retroceder a través de la baraja de tarjetas flash:

[![capturas de pantallas de ejemplo de la aplicación FlashCardPager sin indicadores de buscapersonas](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Agregar un indicador de buscapersonas

Esta implementación de `ViewPager` mínima muestra cada tarjeta flash en la baraja, pero no proporciona ninguna indicación de dónde se encuentra el usuario dentro de la baraja. El siguiente paso consiste en agregar un `PagerTabStrip`. El `PagerTabStrip` informa al usuario sobre el número de problema que se muestra y proporciona el contexto de navegación al mostrar una sugerencia de las tarjetas flash anteriores y siguientes. 

Abra **Resources/layout/main. axml** y agregue un `PagerTabStrip` al diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
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

Al compilar y ejecutar la aplicación, debería ver el `PagerTabStrip` vacío mostrado en la parte superior de cada tarjeta flash: 

[![primer plano de PagerTabStrip sin texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>Mostrar un título

Para agregar un título a cada pestaña de página, implemente el método `GetPageTitleFormatted` en el adaptador. `ViewPager` llama a `GetPageTitleFormatted` (si se implementa) para obtener la cadena de título que describe la página en la posición especificada. Agregue el método siguiente a la clase `FlashCardDeckAdapter` en **FlashCardDeckAdapter.CS**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Este código convierte la posición en la baraja de la tarjeta flash en un número de problema. La cadena resultante se convierte en un `String` de Java que se devuelve al `ViewPager`. Al ejecutar la aplicación con este nuevo método, cada página muestra el número del problema en el `PagerTabStrip`: 

[![capturas de pantalla de FlashCardPager con el número de problema mostrado sobre cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Puede deslizarse hacia atrás y hacia delante para ver el número del problema en la baraja de la tarjeta flash que se muestra en la parte superior de cada tarjeta flash. 

## <a name="handle-user-input"></a>Controlar los datos proporcionados por el usuario

**FlashCardPager** presenta una serie de tarjetas flash basadas en fragmentos en un `ViewPager`, pero aún no tiene una manera de revelar la respuesta para cada problema. En esta sección, se agrega un controlador de eventos al `FlashCardFragment` para mostrar la respuesta cuando el usuario puntea en el texto del problema de la tarjeta flash. 

Abra **FlashCardFragment.CS** y agregue el código siguiente al final del método `OnCreateView` justo antes de que la vista se devuelva al autor de la llamada: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Este controlador de eventos `Click` muestra la respuesta en una notificación del sistema que aparece cuando el usuario puntea el `TextBox`. La variable `answer` se inicializó anteriormente cuando se leyó información de estado de la agrupación que se pasó a `OnCreateView`. Compile y ejecute la aplicación y, a continuación, puntee en el texto del problema en cada tarjeta flash para ver la respuesta: 

[![capturas de pantallas de la aplicación FlashCardPager cuando se puntea el problema matemático](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

La **FlashCardPager** presentada en este tutorial usa un `MainActivity` derivado de `FragmentActivity`, pero también puede derivar `MainActivity` de `AppCompatActivity` (que también proporciona compatibilidad para administrar fragmentos). Para ver un ejemplo de `AppCompatActivity`, consulte [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) en la galería de ejemplos.

## <a name="summary"></a>Resumen

En este tutorial se proporciona un ejemplo paso a paso de cómo crear una aplicación básica basada en `ViewPager`con `Fragment`s. Se presentó un ejemplo de origen de datos que contiene preguntas y respuestas sobre tarjetas flash, un diseño `ViewPager` para mostrar las tarjetas flash y una subclase `FragmentPagerAdapter` que conecta el `ViewPager` al origen de datos. Para ayudar al usuario a desplazarse por las tarjetas flash, se incluyeron instrucciones que explican cómo agregar un `PagerTabStrip` para mostrar el número del problema en la parte superior de cada página. Por último, se ha agregado código de control de eventos para mostrar la respuesta cuando el usuario pulsa un problema con la tarjeta flash. 

## <a name="related-links"></a>Vínculos relacionados

- [FlashCardPager (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
