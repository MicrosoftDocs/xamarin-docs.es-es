---
title: Uso de ContentProvider de contactos
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: e83b9a594bad5ee3d29800988eb94812600da8a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643708"
---
# <a name="using-the-contacts-contentprovider"></a>Uso de ContentProvider de contactos

El código que utiliza los datos de Access `ContentProvider` expuestos por no requiere una referencia `ContentProvider` a la clase. En su lugar, se usa un URI para crear un cursor sobre los datos expuestos por `ContentProvider`. Android usa el URI para buscar en el sistema la aplicación que expone un `ContentProvider` con ese identificador. El URI es una cadena, normalmente en un formato DNS inverso como `com.android.contacts/data`.

En lugar de que los desarrolladores recuerden esta cadena, el proveedor de *contactos* de Android expone sus `android.provider.ContactsContract` metadatos en la clase. Esta clase se usa para determinar el URI de `ContentProvider` , así como los nombres de las tablas y columnas que se pueden consultar.

Algunos tipos de datos también requieren permisos especiales para obtener acceso a. La lista de contactos integrada requiere el `android.permission.READ_CONTACTS` permiso en el archivo **archivo AndroidManifest. XML** .

Hay tres maneras de crear un cursor a partir del URI:

1. **ManagedQuery ()** El enfoque preferido en Android 2,3 (nivel de API 10) y versiones anteriores `ManagedQuery` , devuelve un cursor y también administra automáticamente la actualización de los datos y el cierre del cursor. &ndash; Este método está en desuso en Android 3,0 (nivel de API 11).

1. **ContentResolver devuelvan. Query ()** &ndash; Devuelve un cursor no administrado, lo que significa que debe actualizarse y cerrarse explícitamente en el código.

1. **CursorLoader (). LoadInBackground ()** &ndash; introducido en Android 3,0 (nivel de API 11) `CursorLoader` , es ahora `ContentProvider` la manera preferida de usar. `CursorLoader``ContentResolver` consulta en un subproceso en segundo plano para que la interfaz de usuario no se bloquee.
   Se puede tener acceso a esta clase en versiones anteriores de Android mediante la biblioteca de compatibilidad de V4.


Cada uno de estos métodos tiene el mismo conjunto básico de entradas:

-  **Identificador URI** &ndash; Nombre completo`ContentProvider` del.
-  **Proyección** de &ndash; Especificación de las columnas que se van a seleccionar para el cursor.
-  **Selección** de Similar a una cláusula `WHERE`SQL. &ndash;
-  **SelectionArgs** &ndash; Parámetros que se van a sustituir en la selección.
-  **SortOrder** &ndash; Columnas por las que se va a ordenar.



## <a name="creating-inputs-for-a-query"></a>Crear entradas para una consulta

El `ContactsProvider` código de ejemplo realiza una consulta muy sencilla en el proveedor de contactos integrado de Android. No es necesario conocer los nombres de columna o URI reales; toda la información necesaria para consultar los contactos `ContentProvider` está disponible como constantes expuestas por la `ContactsContract` clase.

Independientemente del método que se use para recuperar el cursor, estos mismos objetos se utilizan como parámetros como se muestra en el archivo *ContactsProvider/ContactsAdapter. CS* :

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

En este ejemplo `selection`,, `selectionArgs` y `sortOrder` se omitirán estableciéndolo en `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Crear un cursor a partir de un URI del proveedor de contenido

Una vez creados los objetos de parámetro, se pueden usar en una de las tres maneras siguientes:



### <a name="using-a-managed-query"></a>Usar una consulta administrada

Las aplicaciones que tienen como destino Android 2,3 (nivel de API 10) o una versión anterior deben usar este método:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Este cursor será administrado por Android, por lo que no es necesario cerrarlo.



### <a name="using-contentresolver"></a>Usar ContentResolver devuelvan

El acceso `ContentProvider` directo a para obtener un cursor en puede realizarse de la siguiente manera: `ContentResolver`

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Este cursor no está administrado, por lo que debe cerrarse cuando ya no se necesite.
Asegúrese de que el código cierra un cursor abierto; de lo contrario, se producirá un error.

```csharp
cursor.Close();
```

Como alternativa, puede llamar `StartManagingCursor()` a y `StopManagingCursor()` a "administrar" el cursor. Los cursores administrados se desactivan automáticamente y se vuelven a consultar cuando se detienen y reinician las actividades.



### <a name="using-cursorloader"></a>Usar CursorLoader

Las aplicaciones compiladas para Android 3,0 (nivel de API 11) o más recientes deben usar este método:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

El `CursorLoader` garantiza que todas las operaciones de cursor se realicen en un subproceso en segundo plano y puede volver a usar de forma inteligente un cursor existente entre instancias de actividad cuando se reinicie una actividad (por ejemplo, debido a un cambio en la configuración) en lugar de volver a cargar los datos de nuevo.

Las versiones anteriores de Android también pueden `CursorLoader` usar la clase mediante las [bibliotecas de compatibilidad de V4](https://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Mostrar los datos del cursor con un adaptador personalizado

Para mostrar la imagen de contacto, usaremos un adaptador personalizado para que podamos resolver manualmente la `PhotoId` referencia a una ruta de acceso al archivo de imagen.

Para mostrar los datos con un adaptador personalizado, en el ejemplo `CursorLoader` se usa para recuperar todos los datos de contacto en una colección local en el método **FillContacts** de **ContactsProvider/ContactsAdapter. CS**:

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

A continuación, implemente los métodos de `contactList` BaseAdapter mediante la colección. El adaptador se implementa tal como lo haría con cualquier otra colección &ndash; , por lo que no hay ningún control especial en este caso porque los datos proceden de un: `ContentProvider`

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

Se muestra la imagen (si existe) mediante el URI del archivo de imagen en el dispositivo. La aplicación tiene el siguiente aspecto:

[![Captura de pantalla de la aplicación que muestra los contactos en un control ListView; se muestra una imagen a la izquierda de una entrada.](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

Con un patrón de código similar, la aplicación puede acceder a una amplia variedad de datos del sistema, como fotos, vídeos y música del usuario.
Algunos tipos de datos requieren permisos especiales para ser solicitados en el **archivo archivo AndroidManifest. XML**del proyecto.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Mostrar los datos del cursor con un SimpleCursorAdapter

También se puede mostrar el cursor con un `SimpleCursorAdapter` (aunque solo se mostrará el nombre, no la foto). Este código muestra cómo usar un `ContentProvider` con `SimpleCursorAdapter` (este código no aparece en el ejemplo):

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

Consulte los [adaptadores y los controles ListView](~/android/user-interface/layouts/list-view/index.md) para obtener más información sobre la `SimpleCursorAdapter`implementación de.


## <a name="related-links"></a>Vínculos relacionados

- [Demostración de ContactsAdapter (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
