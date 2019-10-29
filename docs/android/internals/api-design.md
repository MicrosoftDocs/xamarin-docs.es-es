---
title: Principios de diseño de la API de Xamarin. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c68812e22ed813e6b76470eec5c354b910d22b23
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028319"
---
# <a name="xamarinandroid-api-design-principles"></a>Principios de diseño de la API de Xamarin. Android

Además de las bibliotecas de clases base básicas que forman parte de mono, Xamarin. Android incluye enlaces para varias API de Android que permiten a los desarrolladores crear aplicaciones nativas de Android con mono.

En el núcleo de Xamarin. Android hay un motor de interoperabilidad que C# une el mundo con el mundo de Java y ofrece a los desarrolladores acceso a C# las API de Java desde u otros lenguajes de .net.

## <a name="design-principles"></a>Principios de diseño

Estos son algunos de los principios de diseño para el enlace de Xamarin. Android

- Se ajusta a las [directrices de diseño de .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

- Permite a los desarrolladores subclases de clases de Java.

- La subclase debe funcionar C# con construcciones estándar.

- Derivar de una clase existente.

- Llame al constructor base para encadenar.

- Los métodos de invalidación deben realizarse con C#el sistema de invalidación de.

- Facilite las tareas comunes de Java y las tareas de Java difíciles.

- Exponga las propiedades de C# JavaBean como propiedades.

- Exponga una API fuertemente tipada:

  - Aumentar la seguridad de tipos.

  - Minimice los errores en tiempo de ejecución.

  - Obtiene el IDE de IntelliSense en los tipos de valor devuelto.

  - Permite la documentación del menú emergente del IDE.

- Anime la exploración en el IDE de las API:

  - Use alternativas de marco para minimizar la exposición de Classlib de Java.

  - Exponga C# los delegados (expresiones lambda, métodos anónimos y System. Delegate) en lugar de las interfaces de un solo método cuando sea adecuado y aplicable.

  - Proporcionar un mecanismo para llamar a bibliotecas de Java arbitrarias ( [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)).

## <a name="assemblies"></a>Ensamblados

Xamarin. Android incluye una serie de ensamblados que constituyen el *Perfil de monomobile*. La página [ensamblados](~/cross-platform/internals/available-assemblies.md) tiene más información.

Los enlaces a la plataforma Android se encuentran en el ensamblado de `Mono.Android.dll`. Este ensamblado contiene todo el enlace para consumir las API de Android y comunicarse con la máquina virtual en tiempo de ejecución de Android.

## <a name="binding-design"></a>Diseño de enlace

### <a name="collections"></a>Colecciones

Las API de Android usan las colecciones Java. util exhaustivamente para proporcionar listas, conjuntos y asignaciones. Estos elementos se exponen mediante las interfaces [System. Collections. Generic](xref:System.Collections.Generic) en nuestro enlace. Las asignaciones fundamentales son:

- [java. util. Set\<E >](https://developer.android.com/reference/java/util/Set.html) asigna al tipo de sistema [ICollection\<t >](xref:System.Collections.Generic.ICollection`1), clase auxiliar [Android. Runtime. JavaSet\<t >](xref:Android.Runtime.JavaSet`1).

- [java. util. List\<E >](https://developer.android.com/reference/java/util/List.html) asigna al tipo de sistema [IList\<t >](xref:System.Collections.Generic.IList`1), clase auxiliar [Android. Runtime. JavaList\<t >](xref:Android.Runtime.JavaList`1).

- [java. util. Map < K, V >](https://developer.android.com/reference/java/util/Map.html) se asigna al tipo de sistema [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), clase auxiliar [Android. Runtime. JavaDictionary < K, V >](xref:Android.Runtime.JavaDictionary`2).

- [java. util. Collection\<E >](https://developer.android.com/reference/java/util/Collection.html) asigna al tipo de sistema [ICollection\<t >](xref:System.Collections.Generic.ICollection`1), clase auxiliar [Android. Runtime. JavaCollection\<t >](xref:Android.Runtime.JavaCollection`1).

Hemos proporcionado clases de aplicación auxiliar para facilitar la serialización de estos tipos de forma más rápida. Cuando sea posible, se recomienda usar estas colecciones proporcionadas en lugar de la implementación proporcionada por el marco, como [`List<T>`](xref:System.Collections.Generic.List`1) o [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2). Las implementaciones de [Android. Runtime](xref:Android.Runtime) usan internamente una colección de Java nativa y, por lo tanto, no requieren la copia a y desde una colección nativa al pasar a un miembro de la API de Android.

Puede pasar cualquier implementación de interfaz a un método Android que acepte esa interfaz; por ejemplo, pasar una `List<int>` al constructor [ArrayAdapter&lt;int&gt;(Context, int, IList&lt;int&gt;)](xref:Android.Widget.ArrayAdapter`1) . *Sin embargo*, para todas las implementaciones *excepto* las implementaciones de Android. Runtime, esto implica *copiar* la lista de la máquina virtual mono en la máquina virtual en tiempo de ejecución de Android. Si la lista se cambia más adelante en el tiempo de ejecución de Android (por ejemplo, mediante la invocación del [&gt;ArrayAdapter&lt;t. Add (T)](xref:Android.Widget.ArrayAdapter`1.Add*) Method), esos cambios *no* estarán visibles en el código administrado. Si se usara un `JavaList<int>`, los cambios serían visibles.

Rephrase, las implementaciones de interfaz de colecciones que *no* son una de las **clases auxiliares**enumeradas anteriormente solo serializan [in]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```

### <a name="properties"></a>Propiedades

Los métodos de Java se transforman en propiedades, si es necesario:

- El par de métodos de Java `T getFoo()` y `void setFoo(T)` se transforman en la propiedad `Foo`. Ejemplo: [Activity. Intent](xref:Android.App.Activity.Intent).

- El método de `getFoo()` Java se transforma en la propiedad foo de solo lectura. Ejemplo: [context. packagename](xref:Android.Content.Context.PackageName).

- No se generan propiedades de solo establecimiento.

- *No* se generan propiedades si el tipo de propiedad sería una matriz.

### <a name="events-and-listeners"></a>Eventos y agentes de escucha

Las API de Android se basan en Java y sus componentes siguen el patrón de Java para enlazar agentes de escucha de eventos. Este patrón tiende a ser engorroso, ya que requiere que el usuario cree una clase anónima y declare los métodos que se van a invalidar; por ejemplo, esto es lo que se haría en Android con Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

El código equivalente en C# el uso de eventos sería:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Tenga en cuenta que los dos mecanismos anteriores están disponibles con Xamarin. Android. Puede implementar una interfaz de escucha y asociarla a View. SetOnClickListener, o puede adjuntar un delegado creado a través de cualquiera de C# los paradigmas habituales al evento click.

Cuando el método de devolución de llamada del agente de escucha tiene un valor devuelto void, se crean elementos de API basados en un [TEventArgs de&gt;EventHandler&lt;](xref:System.EventHandler`1) . Generamos un evento como el ejemplo anterior para estos tipos de agente de escucha. Sin embargo, si la devolución de llamada del agente de escucha devuelve un valor distinto de void y no **booleano** , no se usan eventos ni EventHandlers. En su lugar, se genera un delegado específico para la firma de la devolución de llamada y se agregan propiedades en lugar de eventos. La razón es tratar con el orden de invocación de delegado y el control de devolución. Este enfoque refleja lo que se hace con la API de Xamarin. iOS.

C#los eventos o propiedades solo se generan automáticamente si el método de registro de eventos de Android:

1. Tiene un prefijo `set`, por ejemplo, [ *set*OnClickListener](xref:Android.Views.View.SetOnClickListener*).

1. Tiene un tipo de valor devuelto `void`.

1. Solo acepta un parámetro, el tipo de parámetro es una interfaz, la interfaz solo tiene un método y el nombre de la interfaz finaliza en `Listener`, por ejemplo, [View. OnClick](xref:Android.Views.View.IOnClickListener).

Además, si el método de interfaz del agente de escucha tiene un tipo de valor devuelto **booleano** en lugar de **void**, la subclase *EventArgs* generada contendrá una propiedad *controlada* . El valor de la propiedad *Handled* se usa como el valor devuelto para el método *Listener* y su valor predeterminado es `true`.

Por ejemplo, el método Android [View. setOnKeyListener ()](xref:Android.Views.View.SetOnKeyListener*) acepta la interfaz [View. OnKeyListener](xref:Android.Views.View.IOnKeyListener) y el método [View. OnKeyListener. onKey (View, int, KeyEvent)](xref:Android.Views.View.IOnKeyListener.OnKey*) tiene un tipo de valor devuelto booleano. Xamarin. Android genera un evento [View. KeyPress](xref:Android.Views.View.KeyPress) correspondiente, que es un [EventHandler&lt;view. KeyEventArgs&gt;](xref:Android.Views.View.KeyEventArgs).
A su vez, la clase *KeyEventArgs* tiene una propiedad [View. KeyEventArgs. Handled](xref:Android.Views.View.KeyEventArgs.Handled) , que se utiliza como valor devuelto para el método *View. OnKeyListener. onKey ()* .

Tenemos previsto agregar sobrecargas para otros métodos y constructores para exponer la conexión basada en el delegado. Además, los agentes de escucha con varias devoluciones de llamada requieren una inspección adicional para determinar si la implementación de devoluciones de llamada individuales es razonable, por lo que se convierten a medida que se identifican. Si no hay ningún evento correspondiente, los agentes de escucha deben usarse en, pero traiga el que cree podría tener el uso del C#delegado en nuestra atención. También hemos realizado algunas conversiones de interfaces sin el sufijo "Listener" cuando estaba claro que se beneficiarían de una alternativa de delegado.

Todas las interfaces de agentes de escucha implementan el [`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
interfaz, debido a los detalles de implementación del enlace, por lo que las clases de agente de escucha deben implementar esta interfaz. Esto se puede hacer implementando la interfaz del agente de escucha en una subclase de [java. lang. Object](xref:Java.Lang.Object) o cualquier otro objeto de Java ajustado, como una actividad de Android.

### <a name="runnables"></a>Runnables

Java emplea la interfaz [java. lang. Runnable](xref:Java.Lang.Runnable) para proporcionar un mecanismo de delegación. La clase [java. lang. Thread](xref:Java.Lang.Thread) es un consumidor destacado de esta interfaz. Android también ha empleado la interfaz en la API.
[Activity. runOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) y [View.post ()](xref:Android.Views.View.Post*) son ejemplos importantes.

La interfaz de `Runnable` contiene un único método void, [Run ()](xref:Java.Lang.Runnable.Run). Por lo tanto, se presta a enlazar C# como un delegado [System. Action](xref:System.Action) . Hemos proporcionado sobrecargas en el enlace que aceptan un `Action` parámetro para todos los miembros de la API que consumen una `Runnable` en la API nativa, por ejemplo, [Activity. RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) y [View.post ()](xref:Android.Views.View.Post*).

Hemos dejado las sobrecargas de [IRunnable](xref:Java.Lang.IRunnable) en lugar de reemplazarlas, ya que varios tipos implementan la interfaz y, por tanto, se pueden pasar como runnables directamente.

### <a name="inner-classes"></a>Clases internas

Java tiene dos tipos diferentes de [clases anidadas](https://download.oracle.com/javase/tutorial/java/javaOO/nested.html): clases anidadas estáticas y clases no estáticas.

Las clases anidadas estáticas de Java C# son idénticas a los tipos anidados.

Las clases anidadas no estáticas, también denominadas *clases internas*, son significativamente diferentes. Contienen una referencia implícita a una instancia de su tipo envolvente y no pueden contener miembros estáticos (entre otras diferencias fuera del ámbito de esta información general).

Cuando se trata de un enlace C# y uso, las clases anidadas estáticas se tratan como tipos anidados normales. Mientras tanto, las clases internas tienen dos diferencias importantes:

1. La referencia implícita al tipo contenedor se debe proporcionar explícitamente como un parámetro de constructor.

1. Al heredar de una clase interna, la clase interna *debe* estar anidada dentro de un tipo que herede del tipo contenedor de la clase interna base y el tipo derivado debe proporcionar un constructor del mismo tipo que el C# tipo contenedor.

Por ejemplo, considere la clase interna [Android. Service. Wallpaper. WallpaperService. Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine) . Dado que es una clase interna, el [constructor WallpaperService. Engine ()](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) toma una referencia a una instancia de [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) (Compare y contrasta con el constructor Java WallpaperService. Engine (), que no toma ningún parámetro).

Una derivación de ejemplo de una clase interna es CubeWallpaper. CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
    public override WallpaperService.Engine OnCreateEngine ()
    {
        return new CubeEngine (this);
    }

    class CubeEngine : WallpaperService.Engine {
        public CubeEngine (CubeWallpaper s)
                : base (s)
        {
        }
    }
}
```

Observe cómo `CubeWallpaper.CubeEngine` está anidado dentro de `CubeWallpaper`, `CubeWallpaper` hereda de la clase contenedora de `WallpaperService.Engine`y `CubeWallpaper.CubeEngine` tiene un constructor que toma el tipo declarativo, `CubeWallpaper` en este caso, todo tal como se especificó anteriormente.

### <a name="interfaces"></a>Interfaces

Las interfaces Java pueden contener tres conjuntos de miembros, dos de los cuales causan C#problemas de:

1. Métodos

1. Tipos

1. Campos

Las interfaces de Java se traducen en dos tipos:

1. Una interfaz (opcional) que contiene declaraciones de método. Esta interfaz tiene el mismo nombre que la interfaz Java, con la *excepción* de que también tiene un prefijo ' *I* '.

1. Una clase estática (opcional) que contiene los campos declarados dentro de la interfaz java.

Los tipos anidados se "reubican" para ser elementos del mismo nivel de la interfaz envolvente en lugar de tipos anidados, con el nombre de la interfaz envolvente como prefijo.

Por ejemplo, considere la interfaz [Android. os.](xref:Android.OS.Parcelable) comparable.
La interfaz *empaquetable* contiene métodos, tipos anidados y constantes. Los métodos de interfaz que se pueden *empaquetar* se colocan en la interfaz de [Android. os. IParcelable](xref:Android.OS.IParcelable) .
Las constantes de interfaz que se pueden *empaquetar* se colocan en el tipo [Android. os. ParcelableConsts](xref:Android.OS.ParcelableConsts) . Los tipos anidados [Android. os. ClassLoaderCreator\<t >](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) y [Android. os. Parcel. Creator\<t >](https://developer.android.com/reference/android/os/Parcelable.Creator.html) no están enlazados actualmente debido a las limitaciones de la compatibilidad con genéricos. Si se admiten, estarán presentes como las interfaces *Android. os. IParcelableClassLoaderCreator* y *Android. os. IParcelableCreator* . Por ejemplo, la interfaz anidada [Android. os. IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) está enlazada como la interfaz [Android. os. IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) .

> [!NOTE]
> A partir de Xamarin. Android 1,9, las constantes de la interfaz de Java se _duplican_ en un esfuerzo por simplificar el traslado de código Java. Esto ayuda a mejorar el traslado del código Java que se basa en las constantes de la interfaz del [proveedor de Android](https://developer.android.com/reference/android/provider/package-summary.html) .

Además de los tipos anteriores, hay cuatro cambios más:

1. Se genera un tipo con el mismo nombre que la interfaz de Java para contener constantes.

1. Los tipos que contienen constantes de interfaz también contienen todas las constantes que proceden de las interfaces Java implementadas.

1. Todas las clases que implementan una interfaz de Java que contiene constantes obtienen un nuevo tipo InterfaceConsts anidado que contiene constantes de todas las interfaces implementadas.

1. El tipo de *desventajas* es ahora obsoleto.

En el caso de la interfaz *Android. os.* recreable, esto significa que ahora habrá un tipo [*Android. os.* ](xref:Android.OS.Parcelable) que se puede empaquetar para contener las constantes. Por ejemplo, la constante [parcelable. CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) se enlazará como la constante [*Parcel. ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor) , en lugar de como la constante *ParcelableConsts. ContentsFileDescriptor* .

En el caso de las interfaces que contienen constantes que implementan otras interfaces que contienen todavía más constantes, ahora se genera la Unión de todas las constantes. Por ejemplo, la interfaz [Android. Provider. mediastore. video. Videocolumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) implementa la interfaz [Android. Provider. mediastore. MediaColumns](xref:Android.Provider.MediaStore.MediaColumns) . Sin embargo, antes de 1,9, el tipo [Android. Provider. mediastore. video. VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) no tiene forma de acceder a las constantes declaradas en [Android. Provider. mediastore. MediaColumnsConsts](xref:Android.Provider.MediaStore.MediaColumnsConsts).
Como resultado, la expresión Java *mediastore. video. Videocolumns. title* debe estar enlazada a la C# expresión *mediastore. video. MediaColumnsConsts. title* , que es difícil de detectar sin leer una gran cantidad de documentación de Java. En 1,9, la expresión C# equivalente será [*mediastore. video. videocolumns. title*](xref:Android.Provider.MediaStore.Video.VideoColumns.Title).

Además, tenga en cuenta el tipo [Android. os. bundle](xref:Android.OS.Bundle) , que implementa la interfaz que se puede *empaquetar* de Java. Puesto que implementa la interfaz, se puede obtener acceso a todas las constantes de esa interfaz "a" a través del tipo de agrupación, por ejemplo, *bundle. CONTENTS_FILE_DESCRIPTOR* es una expresión Java perfectamente válida.
Anteriormente, para migrar esta expresión C# a, sería necesario examinar todas las interfaces que se implementan para ver de qué tipo procede *CONTENTS_FILE_DESCRIPTOR* . A partir de Xamarin. Android 1,9, las clases que implementan interfaces de Java que contienen constantes tendrán un tipo *InterfaceConsts* anidado, que contendrá todas las constantes de interfaz heredadas. Esto permitirá convertir *bundle. CONTENTS_FILE_DESCRIPTOR* en [*bundle. InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor).

Por último, los tipos con un sufijo de *inconvenientes* como *Android. os. ParcelableConsts* ahora están obsoletos, además de los tipos anidados de InterfaceConsts recién incorporados. Se quitarán en Xamarin. Android 3,0.

## <a name="resources"></a>Recursos

Las imágenes, las descripciones de diseño, los blobs binarios y los diccionarios de cadenas se pueden incluir en la aplicación como [archivos de recursos](https://developer.android.com/guide/topics/resources/providing-resources.html).
Varias API de Android están diseñadas para [funcionar en los identificadores de recursos en](https://developer.android.com/guide/topics/resources/accessing-resources.html) lugar de tratar directamente con imágenes, cadenas o blobs binarios.

Por ejemplo, una aplicación de Android de ejemplo que contiene un diseño de la interfaz de usuario (`main.axml`), una cadena de tabla de internacionalización (`strings.xml`) y algunos iconos (`drawable-*/icon.png`) conservaría sus recursos en el directorio "Resources" de la aplicación:

```
Resources/
    drawable-hdpi/
        icon.png

    drawable-ldpi/
        icon.png

    drawable-mdpi/
        icon.png

    layout/
        main.axml

    values/
        strings.xml
```

Las API nativas de Android no funcionan directamente con los nombres de archivo, sino que operan en los identificadores de recursos. Al compilar una aplicación de Android que usa recursos, el sistema de compilación empaquetará los recursos para su distribución y generará una clase llamada `Resource` que contiene los tokens para cada uno de los recursos incluidos. Por ejemplo, para el diseño de recursos anterior, esto es lo que expondría la clase de R:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

A continuación, usaría `Resource.Drawable.icon` para hacer referencia al archivo de `drawable/icon.png` o `Resource.Layout.main` para hacer referencia al archivo de `layout/main.xml` o `Resource.String.first_string` para hacer referencia a la primera cadena del archivo de diccionario `values/strings.xml`.

## <a name="constants-and-enumerations"></a>Constantes y enumeraciones

Las API nativas de Android tienen muchos métodos que toman o devuelven un valor int que se debe asignar a un campo constante para determinar lo que significa int. Para usar estos métodos, el usuario debe consultar la documentación para ver qué constantes son valores adecuados, lo que es menor que el ideal.

Por ejemplo, considere [Activity. requestWindowFeature (int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

En estos casos, se desea agrupar las constantes relacionadas en una enumeración de .NET y volver a asignar el método para tomar la enumeración en su lugar.
Al hacerlo, podemos ofrecer una selección de IntelliSense de los valores posibles.

El ejemplo anterior se convierte en: [Activity. RequestWindowFeature (WindowFeatures featureId)](xref:Android.App.Activity.RequestWindowFeature*).

Tenga en cuenta que se trata de un proceso muy manual para averiguar qué constantes pertenecen juntas y qué API consumen estas constantes. Registre los errores de las constantes usadas en la API que se expresen mejor como una enumeración.
