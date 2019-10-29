---
title: Contactos y ContactsUI en Xamarin. iOS
description: En este artículo se explica cómo trabajar con los nuevos marcos de interfaz de usuario contactos y contactos en una aplicación Xamarin. iOS. Estos marcos de trabajo reemplazan la interfaz de usuario de libreta de direcciones existente y la libreta de direcciones utilizada en versiones anteriores de iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 438ed93bafa37496e6a97ea2fe98ca6a515682cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032566"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contactos y ContactsUI en Xamarin. iOS

_En este artículo se explica cómo trabajar con los nuevos marcos de interfaz de usuario contactos y contactos en una aplicación Xamarin. iOS. Estos marcos de trabajo reemplazan la interfaz de usuario de libreta de direcciones existente y la libreta de direcciones utilizada en versiones anteriores de iOS._

Con la introducción de iOS 9, Apple ha publicado dos nuevos marcos de trabajo, `Contacts` y `ContactsUI`, que reemplazan los marcos de interfaz de usuario existentes de libreta de direcciones y de libreta de direcciones usados por iOS 8 y versiones anteriores.

Los dos nuevos marcos de trabajo contienen la siguiente funcionalidad:

- [**Contactos**](#contacts) : proporciona acceso a los datos de la lista de contactos del usuario.
  Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro para subprocesos y de solo lectura.

- [**ContactsUI**](#contactsui) : proporciona elementos de interfaz de usuario de Xamarin. iOS para mostrar, editar, seleccionar y crear contactos en dispositivos iOS.

[![](contacts-images/add01.png "An example Contact Sheet on an iOS device")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Los marcos de `AddressBook` y `AddressBookUI` existentes que usan iOS 8 (y versiones anteriores) han quedado desusados en iOS 9 y deben reemplazarse por los nuevos `Contacts` y `ContactsUI` Marcos lo antes posible para cualquier aplicación de Xamarin. iOS existente. Las nuevas aplicaciones deben escribirse en los nuevos marcos de trabajo.

En las secciones siguientes, echaremos un vistazo a estos nuevos marcos y cómo implementarlos en una aplicación Xamarin. iOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>El marco contactos

El marco Contacts proporciona acceso a Xamarin. iOS a la información de contacto del usuario. Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro para subprocesos y de solo lectura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Objetos de contacto

La clase `CNContact` proporciona acceso seguro para subprocesos y de solo lectura a las propiedades de un contacto como el nombre, la dirección o los números de teléfono. `CNContact` funciones como un `NSDictionary` y contiene varias colecciones de propiedades de solo lectura (como direcciones o números de teléfono):

[![](contacts-images/contactobjects.png "Contact Object overview")](contacts-images/contactobjects.png#lightbox)

En el caso de las propiedades que pueden tener varios valores (como la dirección de correo electrónico o los números de teléfono), se representarán como una matriz de objetos de `NSLabeledValue`. `NSLabeledValue` es una tupla segura para subprocesos que consta de un conjunto de solo lectura de etiquetas y valores en los que la etiqueta define el valor para el usuario (por ejemplo, un correo electrónico doméstico o de trabajo). El marco de contactos proporciona una selección de etiquetas predefinidas (a través de las clases estáticas `CNLabelKey` y `CNLabelPhoneNumberKey`) que puede usar en la aplicación o la opción de definir etiquetas personalizadas para sus necesidades.

En cualquier aplicación de Xamarin. iOS que necesite ajustar los valores de un contacto existente (o crear otros nuevos), use la versión `NSMutableContact` de la clase y sus subclases (como `CNMutablePostalAddress`).

Por ejemplo, el código siguiente creará un nuevo contacto y lo agregará a la colección de contactos del usuario:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Si este código se ejecuta en un dispositivo iOS 9, se agregará un nuevo contacto a la colección del usuario. Por ejemplo:

[![](contacts-images/add01.png "A new contact added to the user's collection")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Ponerse en contacto con el formato y la localización

El marco contactos contiene varios objetos y métodos que pueden ayudarle a dar formato y localizar el contenido que se va a mostrar al usuario. Por ejemplo, el siguiente código formatearía correctamente un nombre de contacto y una dirección de correo para mostrar:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

En el caso de las etiquetas de propiedades que se mostrarán en la interfaz de usuario de la aplicación, el marco de trabajo de contacto tiene métodos para localizar también esas cadenas. De nuevo, esto se basa en la configuración regional actual del dispositivo iOS en el que se ejecuta la aplicación. Por ejemplo:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Capturando contactos existentes

Mediante el uso de una instancia de la clase `CNContactStore`, puede obtener información de contacto de la base de datos de contactos del usuario. La `CNContactStore` contiene todos los métodos necesarios para capturar o actualizar contactos y grupos de la base de datos. Dado que estos métodos son sincrónicos, se recomienda que los ejecute en un subproceso en segundo plano para evitar el bloqueo de la interfaz de usuario.

Mediante el uso de predicados (creados a partir de la clase `CNContact`), puede filtrar los resultados devueltos al obtener contactos de la base de datos. Para capturar solo los contactos que contienen la cadena `Appleseed`, use el código siguiente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Los predicados genéricos y compuestos no son compatibles con el marco Contacts.

Por ejemplo, para limitar la captura solo a las propiedades **GivenName** y **FamilyName** del contacto, use el código siguiente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Por último, para buscar en la base de datos y devolver los resultados, use el código siguiente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Si este código se ejecutó después del ejemplo que hemos creado en la sección del **objeto Contacts** anterior, devolvería el contacto "John Appleseed" que acabamos de crear.

### <a name="contact-access-privacy"></a>Privacidad de acceso de contacto

Dado que los usuarios finales pueden conceder o denegar el acceso a su información de contacto por aplicación, la primera vez que realice una llamada al `CNContactStore`, se mostrará un cuadro de diálogo en el que se le pedirá que permita el acceso a su aplicación.

La solicitud de permiso solo se presentará una vez, la primera vez que se ejecute la aplicación, y las ejecuciones posteriores o llamadas al `CNContactStore` usarán el permiso que el usuario seleccionó en ese momento.

Debe diseñar la aplicación para que controle correctamente el usuario que deniega el acceso a su base de datos de contactos.

#### <a name="fetching-partial-contacts"></a>Captura de contactos parciales

Un _contacto parcial_ es un contacto que solo se han capturado algunas de las propiedades disponibles del almacén de contactos para. Si intenta obtener acceso a una propiedad que no se ha capturado anteriormente, se producirá una excepción.

Puede comprobar fácilmente si un contacto determinado tiene la propiedad deseada mediante los métodos `IsKeyAvailable` o `AreKeysAvailable` de la instancia de `CNContact`. Por ejemplo:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> Los métodos `GetUnifiedContact` y `GetUnifiedContacts` de la clase `CNContactStore` _solo_ devuelven un contacto parcial limitado a las propiedades solicitadas de las claves de captura proporcionadas.

### <a name="unified-contacts"></a>Contactos unificados

Un usuario puede tener distintos orígenes de información de contacto para una sola persona en su base de datos de contactos (como iCloud, Facebook o Google Mail). En las aplicaciones de iOS y OS X, esta información de contacto se vinculará automáticamente y se mostrará al usuario como un solo _contacto unificado_:

[![](contacts-images/unified01.png "Unified Contacts overview")](contacts-images/unified01.png#lightbox)

Este contacto unificado es una vista temporal y en memoria de la información de contacto de los vínculos a la que se le dará su propio identificador único (que debe usarse para volver a capturar el contacto si es necesario). De forma predeterminada, el marco de contactos devolverá un contacto unificado siempre que sea posible.

### <a name="creating-and-updating-contacts"></a>Crear y actualizar contactos

Como vimos en la sección de [objetos de contacto](#Contact_Objects) anterior, se usa una `CNContactStore` y una instancia de un `CNMutableContact` para crear nuevos contactos que se escriben después en la base de datos de contacto del usuario mediante un `CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

También se puede usar un `CNSaveRequest` para almacenar en caché varios cambios de contactos y grupos en una sola operación y procesar por lotes esas modificaciones en el `CNContactStore`.

Para actualizar un contacto no mutable obtenido a partir de una operación de captura, primero debe solicitar una copia mutable que luego modifique y vuelva a guardar en el almacén de contactos. Por ejemplo:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Notificaciones de cambio de contacto

Cada vez que se modifica un contacto, el almacén de contactos envía una `CNContactStoreDidChangeNotification` al centro de notificaciones predeterminado. Si ha almacenado en memoria caché o está mostrando actualmente algún contacto, deberá actualizar los objetos del almacén de contactos (`CNContactStore`).

### <a name="containers-and-groups"></a>Contenedores y grupos

Los contactos de un usuario pueden existir localmente en el dispositivo del usuario o como contactos sincronizados con el dispositivo desde una o varias cuentas de servidor (como Facebook o Google). Cada grupo de contactos tiene su propio _contenedor_ y un contacto determinado solo puede existir en un contenedor.

[![](contacts-images/containers01.png "Containers and Groups overview")](contacts-images/containers01.png#lightbox)

Algunos contenedores permiten organizar los contactos en uno o varios _grupos_ o _subgrupos_. Este comportamiento depende de la memoria auxiliar de un contenedor determinado. Por ejemplo, iCloud solo tiene un contenedor, pero puede tener muchos grupos (pero no subgrupos). Microsoft Exchange, por otro lado, no admite grupos, pero puede tener varios contenedores (uno para cada carpeta de Exchange).

[![](contacts-images/containers02.png "Overlap within Containers and Groups")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>El marco de ContactsUI

En situaciones en las que la aplicación no necesita presentar una interfaz de usuario personalizada, puede usar el marco de trabajo de ContactsUI para presentar los elementos de la interfaz de usuario para mostrar, editar, seleccionar y crear contactos en la aplicación de Xamarin. iOS.

Mediante el uso de controles integrados de Apple no solo se reduce la cantidad de código que se debe crear para admitir contactos en la aplicación de Xamarin. iOS, pero se presenta una interfaz coherente para los usuarios de la aplicación.

### <a name="the-contact-picker-view-controller"></a>Controlador de vista del selector de contactos

El controlador de vista del selector de contactos (`CNContactPickerViewController`) administra la vista de selector de contactos estándar que permite al usuario seleccionar un contacto o una propiedad de contacto de la base de datos de contacto del usuario. El usuario puede seleccionar uno o más contactos (según su uso) y el controlador de vista del selector de contactos no solicita permiso antes de mostrar el selector.

Antes de llamar a la clase `CNContactPickerViewController`, se definen las propiedades que el usuario puede seleccionar y definir predicados para controlar la visualización y selección de las propiedades de contacto.

Use una instancia de la clase que herede de `CNContactPickerDelegate` para responder a la interacción del usuario con el selector. Por ejemplo:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Para permitir que el usuario seleccione una dirección de correo electrónico de los contactos de su base de datos, puede usar el código siguiente:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>El controlador de vista de contactos

La clase Contact View Controller (`CNContactViewController`) proporciona un controlador para presentar una vista de contacto estándar al usuario final. La vista de contactos puede mostrar nuevos contactos nuevos, desconocidos o existentes, y se debe especificar el tipo antes de que se muestre la vista mediante una llamada al constructor estático correcto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Por ejemplo:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con los marcos de interfaz de usuario de contacto y contacto en una aplicación de Xamarin. iOS. En primer lugar, se han tratado los distintos tipos de objetos que proporciona el marco de trabajo de contacto y cómo se usan para crear nuevos o tener acceso a contactos existentes. También examinó el marco de trabajo de interfaz de usuario de contacto para seleccionar contactos existentes y Mostrar información de contacto.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de contactos](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [Novedades de iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referencia del marco de contactos](https://developer.apple.com/documentation/contacts?language=objc)
- [Referencia de ContactsUI Framework](https://developer.apple.com/documentation/contactsui?language=objc)
