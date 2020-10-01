---
title: Uso de ContentProvider de contactos
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: 8c3608837132e221bec12b2c00fb2dd0b1730e76
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454083"
---
# <a name="using-the-contacts-contentprovider"></a>Uso de ContentProvider de contactos

El código que utiliza a los datos expuestos por `ContentProvider` no requiere una referencia a la clase `ContentProvider`. En su lugar, se usa un URI para crear un cursor sobre los datos expuestos por `ContentProvider`. Android usa el URI para buscar en el sistema la aplicación que expone una instancia de `ContentProvider` con ese identificador. El URI es una cadena, normalmente en un formato de DNS inversa, como `com.android.contacts/data`.

En lugar de que los desarrolladores tengan que recordar esta cadena, el proveedor de *Contactos* de Android expone sus metadatos en la clase `android.provider.ContactsContract`. Esta clase se usa para determinar el URI de la instancia de `ContentProvider`, así como los nombres de las tablas y columnas que se pueden consultar.

Algunos tipos de datos también requieren un permiso especial para acceder a ellos. La lista de contactos integrada requiere el permiso `android.permission.READ_CONTACTS` en el archivo **AndroidManifest.Xml**.

Hay tres formas de crear un cursor a partir del URI:

1. **ManagedQuery ()** : el enfoque preferido en Android 2.3 (nivel de API 10) y versiones anteriores, un elemento `ManagedQuery` devuelve un cursor y también administra automáticamente la actualización de los datos y el cierre del cursor. Este método está en desuso en Android 3.0 (nivel de API 11).

1. **ContentResolver.Query()** : devuelve un cursor no administrado, lo que significa que debe actualizarse y cerrarse explícitamente en el código.

1. **CursorLoader().LoadInBackground()** : se introdujo en Android 3.0 (nivel de API 11), `CursorLoader` es ahora la manera preferida de consumir una instancia de `ContentProvider`. `CursorLoader` consulta un elemento `ContentResolver` en un subproceso en segundo plano para que la interfaz de usuario no se bloquee.
   Se puede acceder a esta clase en versiones anteriores de Android mediante la biblioteca de compatibilidad v4.

Cada uno de estos métodos tiene el mismo conjunto de entradas básico:

- **URI**: el nombre completo de la instancia de `ContentProvider`.
- **Proyección**: especificación de las columnas que se deben seleccionar para el cursor.
- **Selección**: similar a una cláusula SQL `WHERE`.
- **SelectionArgs**: los parámetros que se van a sustituir en la selección.
- **SortOrder**: columnas por las que se va a ordenar.

## <a name="creating-inputs-for-a-query"></a>Creación de entradas para una consulta

El código de ejemplo de `ContactsProvider` realiza una consulta muy sencilla en el proveedor de contactos integrado de Android. No es necesario conocer los nombres de columna o URI reales; toda la información necesaria para consultar la instancia de `ContentProvider` de contactos está disponible como constantes expuestas por la clase `ContactsContract`.

Independientemente del método que se use para recuperar el cursor, estos mismos objetos se utilizan como parámetros, como se muestra en el archivo *ContactsProvider/ContactsAdapter.cs*:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

En este ejemplo, los elementos `selection`, `selectionArgs` y `sortOrder` se omiten estableciéndolos en `null`.

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Creación de un cursor a partir del URI de un proveedor de contenido

Una vez creados los objetos de parámetro, se pueden usar de una de las tres maneras siguientes:

### <a name="using-a-managed-query"></a>Utilización de una consulta administrada

Las aplicaciones que tienen como destino Android 2.3 (nivel de API 10) o una versión anterior deben usar este método:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Android administrará este cursor, por lo que no es necesario cerrarlo.

### <a name="using-contentresolver"></a>Uso de ContentResolver

El acceso directo a `ContentResolver` para obtener un cursor en una instancia de `ContentProvider` puede realizarse de la siguiente manera:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Este cursor no está administrado, por lo que debe cerrarse cuando ya no se necesite.
Asegúrese de que el código cierra un cursor que está abierto; de lo contrario, se producirá un error.

```csharp
cursor.Close();
```

Como alternativa, puede llamar a `StartManagingCursor()` y `StopManagingCursor()` para "administrar" el cursor. Los cursores administrados se desactivan automáticamente y se vuelven a consultar cuando se detienen y reinician las actividades.

### <a name="using-cursorloader"></a>Uso de CursorLoader

Las aplicaciones que creadas para Android 3.0 (nivel de API 11) o una versión más reciente deben usar este método:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

El elemento `CursorLoader` garantiza que todas las operaciones de cursor se realizan en un subproceso en segundo plano, y puede volver a usar de forma inteligente un cursor existente entre instancias de actividad cuando se reinicia una actividad (por ejemplo, debido a un cambio en la configuración) en lugar de volver a cargar los datos de nuevo.

Las versiones anteriores de Android también pueden usar la clase `CursorLoader` mediante las [bibliotecas de compatibilidad v4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Visualización de los datos del cursor con un adaptador personalizado

Para mostrar la imagen de contacto, usaremos un adaptador personalizado, de modo que podamos resolver manualmente la referencia de `PhotoId` a una ruta de acceso del archivo de imagen.

Para mostrar los datos con un adaptador personalizado, en el ejemplo se usa un elemento `CursorLoader` para recuperar todos los datos de contacto en una colección local en el método **FillContacts** de **ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

A continuación, implemente los métodos de BaseAdapter mediante la colección `contactList`. El adaptador se implementa tal como lo haría con cualquier otra colección &ndash;, no hay ningún control especial aquí porque los datos proceden de una instancia de `ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

Se muestra la imagen (si existe) mediante el URI del archivo de imagen en el dispositivo. La aplicación tiene este aspecto:

[![Captura de pantalla de la aplicación que muestra los contactos en un componente ListView; se muestra una imagen a la izquierda de una entrada](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Con un patrón de código similar, la aplicación puede acceder a una amplia variedad de datos del sistema, como fotos, vídeos y música del usuario.
Algunos tipos de datos requieren permisos especiales para poder solicitarlos en el archivo **AndroidManifest.xml** del proyecto.

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Visualización de los datos del cursor con un elemento SimpleCursorAdapter

También se puede mostrar el cursor con un elemento `SimpleCursorAdapter` (aunque solo se mostrará el nombre, no la foto). Este código muestra cómo usar un elemento `ContentProvider` con `SimpleCursorAdapter` (este código no aparece en el ejemplo):

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Consulte [ListViews y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre la implementación de `SimpleCursorAdapter`.

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)