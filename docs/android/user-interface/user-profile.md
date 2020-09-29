---
title: Perfil de usuario
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2018
ms.openlocfilehash: 1b7202a199315bf0be1664bf34725c26d12a66c8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458086"
---
# <a name="user-profile"></a>Perfil de usuario

Android ha admitido la enumeración de contactos con el proveedor de [ContactsContract](xref:Android.Provider.ContactsContract) desde el nivel de API 5. Por ejemplo, la enumeración de contactos es tan sencilla como usar la clase [ContactContracts. contacts](xref:Android.Provider.ContactsContract.Contacts) tal como se muestra en el ejemplo de código siguiente:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id,
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

A partir de Android 4 (nivel de API 14), la clase [ContactsContact. Profile](xref:Android.Provider.ContactsContract.Profile) está disponible a través del `ContactsContract` proveedor. El `ContactsContact.Profile` proporciona acceso al perfil personal para el propietario de un dispositivo, que incluye los datos de contacto, como el nombre y el número de teléfono del propietario del dispositivo.

## <a name="required-permissions"></a>Permisos necesarios

Para leer y escribir datos de contacto, las aplicaciones deben solicitar los `READ_CONTACTS` `WRITE_CONTACTS` permisos y, respectivamente.
Además, para leer y editar el perfil de usuario, las aplicaciones deben solicitar los `READ_PROFILE` `WRITE_PROFILE` permisos y.

## <a name="updating-profile-data"></a>Actualizar datos de perfil

Una vez que se han establecido estos permisos, una aplicación puede usar las técnicas normales de Android para interactuar con los datos del perfil de usuario. Por ejemplo, para actualizar el nombre para mostrar del perfil, llame a [ContentResolver devuelvan. Update](xref:Android.Content.ContentResolver.Update*) con un `Uri` recuperado a través de la propiedad [ContactsContract. Profile. ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri) , como se muestra a continuación:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Leyendo datos de perfil

La emisión de una consulta a [ContactsContact. Profile. ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) vuelve a leer los datos del perfil. Por ejemplo, el código siguiente leerá el nombre para mostrar del perfil de usuario:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>Desplazarse hasta el perfil de usuario

Por último, para desplazarse hasta el perfil de usuario, cree un intento con una `ActionView` acción y `ContactsContract.Profile.ContentUri` , a continuación, páselo al método de la siguiente `StartActivity` manera:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

Al ejecutar el código anterior, el perfil de usuario se muestra como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla del perfil que muestra el perfil de usuario John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Trabajar con el perfil de usuario es similar a interactuar con otros datos en Android y ofrece un nivel adicional de personalización del dispositivo.

## <a name="related-links"></a>Vínculos relacionados

- [ContactsProviderDemo (ejemplo)](/samples/xamarin/monodroid-samples/contactsproviderdemo)