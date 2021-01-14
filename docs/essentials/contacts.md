---
title: 'Xamarin.Essentials: Contactos'
description: La clase Contacts de Xamarin.Essentials permite a un usuario seleccionar un contacto y recuperar información sobre él.
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8e47fd77bedb701e1953d903091c77af31cb6012
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972297"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials: Contactos

La clase **Contacts** permite a un usuario seleccionar un contacto y recuperar información sobre él.

## <a name="get-started"></a>Introducción

[!include[](~/essentials/includes/get-started.md)]

Para acceder a la función de **Contacts**, se requiere la siguiente configuración específica para la plataforma.

# <a name="android"></a>[Android](#tab/android)

El permiso `ReadContacts` es necesario y se debe configurar en el proyecto Android. Se puede agregar de las siguientes maneras:

Abra el archivo **AssemblyInfo.cs** de la carpeta **Propiedades** y agregue lo siguiente:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadContacts)]
```

O BIEN, actualice el manifiesto de Android:

Abra el archivo **AndroidManifest.xml** de la carpeta **Propiedades** y agregue lo siguiente dentro del nodo **manifest**.

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" /> />
```

O haga clic con el botón derecho en el proyecto de Android y abra las propiedades del proyecto. En **Manifiesto de Android**, busque el área **Permisos requeridos:** y active este permiso. Esto actualizará automáticamente el archivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

En `Info.plist`, agregue las claves siguientes:

```xml
<key>NSContactsUsageDescription</key>
<string>This app needs access to contacts to pick a contact and get info.</string>
```

Asegúrese de actualizar el elemento `<string>` en la descripción específica de la aplicación, ya que se mostrará a los usuarios.

# <a name="uwp"></a>[UWP](#tab/uwp)

En `Package.appxmanifest`, en **Capacidades**, asegúrese de que la capacidad `Contact` está activada.

-----

## <a name="pick-a-contact"></a>Seleccionar un contacto

Al llamar a `Contacts.PickContactAsync()`, aparecerá el cuadro de diálogo de contacto y permitirá al usuario recibir información sobre el usuario.


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var id = contact.Id;
    var namePrefix = contact.NamePrefix;
    var givenName = contact.GivenName;
    var middleName = contact.MiddleName;
    var familyName = contact.FamilyName;
    var nameSuffix = contact.NameSuffix;
    var displayName = contact.DisplayName;
    var phones = contact.Phones; // List of phone numbers
    var emails = contact.Emails; // List of email addresses
}
catch (Exception ex)
{
    // Handle exception here.
}
```

## <a name="get-all-contacts"></a>Obtener todos los contactos

```csharp
ObservableCollection<Contact> contactsCollect = new ObservableCollection<Contact>();

try
{
    // cancellationToken parameter is optional
    var cancellationToken = default(CancellationToken);
    var contacts = await Contacts.GetAllAsync(cancellationToken);

    if (contacts == null)
        return;

    foreach (var contact in contacts)
        contactsCollect.Add(contact);
}
catch (Exception ex)
{
    // Handle exception here.
}
```

## <a name="platform-differences"></a>Diferencias entre plataformas

# <a name="android"></a>[Android](#tab/android)

- El parámetro `cancellationToken` del método `GetAllAsync` solo se usa en UWP.

# <a name="ios"></a>[iOS](#tab/ios)

- El parámetro `cancellationToken` del método `GetAllAsync` solo se usa en UWP.
- La plataforma iOS no admite la propiedad `DisplayName` de forma nativa, por lo que el valor `DisplayName` se construye como "GivenName FamilyName".

# <a name="uwp"></a>[UWP](#tab/uwp)

No hay diferencias entre las plataformas.

-----


## <a name="api"></a>API

- [Código fuente de Contacts](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Documentación de Contacts API](xref:Xamarin.Essentials.Contacts)
