---
title: 'Xamarin.Essentials: Contactos'
description: La clase Contacts de Xamarin.Essentials permite a un usuario seleccionar un contacto y recuperar información sobre él.
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd239a8dcf192c0bdbc6265769208f4fc989bbbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434495"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials: Contactos

La clase **Contacts** permite a un usuario seleccionar un contacto y recuperar información sobre él.

![API de versión preliminar](~/media/shared/preview.png)

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

## <a name="picking-a-contact"></a>Selección de un contacto

Al llamar a `Contacts.PickContactAsync()`, aparecerá el cuadro de diálogo de contacto y permitirá al usuario recibir información sobre el usuario.


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var name = contact.Name;
    var contactType = contact.ContactType; // Unknown, Personal, Work
    var numbers = contact.Numbers; // List of phone numbers
    var emails = contact.Emails; // List of email addresses 
    
}
catch (Exception ex)
{
    // Handle exception here.
}
```


## <a name="api"></a>API

- [Código fuente de Contacts](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Documentación de Contacts API](xref:Xamarin.Essentials.Contacts)
