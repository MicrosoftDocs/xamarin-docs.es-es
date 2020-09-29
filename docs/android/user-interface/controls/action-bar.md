---
title: Barra para Xamarin. Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 45b6c080f6e50efc4648c5b2eab6db0634dee7f1
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457372"
---
# <a name="actionbar-for-xamarinandroid"></a>Barra para Xamarin. Android

Cuando `TabActivity` se usa, el código para crear los iconos de pestaña no tiene ningún efecto cuando se ejecuta en el marco de trabajo de Android 4,0. Aunque funcionalmente funciona como en las versiones de Android anteriores a 2,3, la `TabActivity` propia clase ha quedado en desuso en 4,0. Se ha introducido una nueva forma de crear una interfaz con pestañas que usa el Barra de acciones, que trataremos A continuación.

## <a name="action-bar-tabs"></a>Barra de acciones pestañas

El Barra de acciones incluye compatibilidad para agregar interfaces con pestañas en Android 4,0.
En la captura de pantalla siguiente se muestra un ejemplo de este tipo de interfaz.

[![Captura de pantalla de la aplicación que se ejecuta en un emulador; se muestran dos pestañas](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Para crear pestañas en el Barra de acciones, primero es necesario establecer su `NavigationMode` propiedad para admitir pestañas. En Android 4, `ActionBar` hay una propiedad disponible en la clase de actividad, que se puede usar para establecer lo siguiente `NavigationMode` :

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Una vez hecho esto, podemos crear una pestaña llamando al `NewTab` método en el barra de acciones. Con esta instancia de la pestaña, podemos llamar a los `SetText` `SetIcon` métodos y para establecer el texto y el icono de la etiqueta de la pestaña; estas llamadas se realizan en orden en el código que se muestra a continuación:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Sin embargo, antes de poder agregar la pestaña, es necesario controlar el `TabSelected` evento. En este controlador, podemos crear el contenido de la pestaña. Barra de acciones pestañas están diseñadas para trabajar con *fragmentos*, que son clases que representan una parte de la interfaz de usuario en una actividad. En este ejemplo, la vista del fragmento contiene una única `TextView` , que se infla en nuestra `Fragment` subclase de esta manera:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);

        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";

        return view;
    }
}
```

El argumento de evento que se pasa en el `TabSelected` evento es de tipo `TabEventArgs` , que incluye una `FragmentTransaction` propiedad que se puede usar para agregar el fragmento tal y como se muestra a continuación:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Por último, podemos agregar la pestaña al Barra de acciones llamando al `AddTab` método como se muestra en este código:

```csharp
this.ActionBar.AddTab (tab);
```

Para obtener el ejemplo completo, vea el proyecto *HelloTabsICS* en el código de ejemplo de este documento.

## <a name="shareactionprovider"></a>ShareActionProvider

La `ShareActionProvider` clase permite que se lleve a cabo una acción de uso compartido desde un barra de acciones. Se encarga de crear una vista de acción con una lista de aplicaciones que pueden controlar un intento de uso compartido y mantener un historial de las aplicaciones usadas anteriormente para facilitar el acceso a ellas más adelante desde el Barra de acciones. Esto permite que las aplicaciones compartan datos a través de una experiencia de usuario coherente en Android.

### <a name="image-sharing-example"></a>Ejemplo de uso compartido de imágenes

Por ejemplo, a continuación se muestra una captura de pantalla de un Barra de acciones con un elemento de menú para compartir una imagen (tomada del ejemplo [ShareActionProvider](/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Cuando el usuario puntea el elemento de menú en el Barra de acciones, el ShareActionProvider carga la aplicación para controlar un intento que está asociado a `ShareActionProvider` . En este ejemplo, la aplicación de mensajería se ha usado previamente, por lo que se presenta en el Barra de acciones.

[![Captura de pantalla del icono de la aplicación de mensajería en el Barra de acciones](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

Cuando el usuario hace clic en el elemento en el Barra de acciones, se inicia la aplicación de mensajería que contiene la imagen compartida, como se muestra a continuación:

[![Captura de pantalla de la aplicación de mensajería que muestra la imagen de Monkey](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>Especificar la clase del proveedor de acciones

Para usar `ShareActionProvider` , establezca el `android:actionProviderClass` atributo en un elemento de menú en el XML para el menú del barra de acciones como se indica a continuación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```

### <a name="inflating-the-menu"></a>Inflar el menú

Para inflar el menú, se invalida `OnCreateOptionsMenu` en la subclase de actividad. Una vez que tenemos una referencia al menú, podemos obtener el `ShareActionProvider` de la `ActionProvider` propiedad del elemento de menú y, a continuación, usar el método SetShareIntent para establecer la `ShareActionProvider` intención de, como se muestra a continuación:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       

    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```

### <a name="creating-the-intent"></a>Crear el intento

Usará `ShareActionProvider` la intención, pasada al `SetShareIntent` método en el código anterior, para iniciar la actividad adecuada. En este caso, se crea un intento de enviar una imagen mediante el código siguiente:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

La imagen del ejemplo de código anterior se incluye como un recurso con la aplicación y se copia en una ubicación públicamente accesible cuando se crea la actividad, por lo que será accesible para otras aplicaciones, como la aplicación de mensajería. El código de ejemplo que acompaña a este artículo contiene el código fuente completo de este ejemplo, que ilustra su uso.

## <a name="related-links"></a>Vínculos relacionados

- [Pestañas Hello ICS (ejemplo)](/samples/xamarin/monodroid-samples/hellotabsics)
- [Demostración de ShareActionProvider (ejemplo)](/samples/xamarin/monodroid-samples/shareactionproviderdemo)