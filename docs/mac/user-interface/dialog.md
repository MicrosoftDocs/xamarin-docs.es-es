---
title: Cuadros de diálogo en Xamarin. Mac
description: En este artículo se explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación de Xamarin. Mac. Describe la creación de ventanas modales en Xcode e Interface Builder, el trabajo con cuadros de diálogo estándar e interacción con estos C# controles en el código.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 723f751b93af304c7d70fb1250dbda2f0220e77e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292213"
---
# <a name="dialogs-in-xamarinmac"></a>Cuadros de diálogo en Xamarin. Mac

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a los mismos cuadros de diálogo y ventanas modales que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las ventanas modales (o, opcionalmente, C# crearlas directamente en el código).

Aparece un cuadro de diálogo en respuesta a una acción del usuario y, por lo general, proporciona maneras en que los usuarios pueden completar la acción. Un cuadro de diálogo requiere una respuesta del usuario antes de que se pueda cerrar.

Windows se puede usar en un estado no modal (por ejemplo, un editor de texto que puede tener varios documentos abiertos a la vez) o modal (como un cuadro de diálogo de exportación que se debe descartar para que la aplicación pueda continuar).

[![](dialog-images/dialog03.png "Cuadro de diálogo abrir")](dialog-images/dialog03.png#lightbox)

En este artículo, trataremos los aspectos básicos del trabajo con cuadros de diálogo y ventanas modales en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en Este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) también, en él se explican `Export` los `Register` comandos y que se usan para C# conectar las clases a Objetos de Objective-C y elementos de la interfaz de usuario.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introducción a los cuadros de diálogo

Aparece un cuadro de diálogo en respuesta a una acción del usuario (por ejemplo, al guardar un archivo) y proporciona una manera para que los usuarios puedan completar esa acción. Un cuadro de diálogo requiere una respuesta del usuario antes de que se pueda cerrar.

Según Apple, hay tres maneras de presentar un cuadro de diálogo:

- **Documento modal** : un cuadro de diálogo modal de documento impide que el usuario haga nada más dentro de un documento determinado hasta que se descarte.
- **Aplicación modal** : un cuadro de diálogo modal de aplicación impide que el usuario interactúe con la aplicación hasta que se descarte.
- No **modal** Un cuadro de diálogo no modal permite a los usuarios cambiar la configuración en el cuadro de diálogo mientras sigue interactuando con la ventana de documento.

### <a name="modal-window"></a>Ventana modal

Cualquier estándar `NSWindow` se puede usar como un cuadro de diálogo personalizado mostrándolo de forma modal:

[![](dialog-images/modal01.png "Ventana modal de ejemplo")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Hojas de diálogo modales de documento

Una _hoja_ es un cuadro de diálogo modal que se adjunta a una ventana de documento determinada, lo que impide que los usuarios interactúen con la ventana hasta que descartan el cuadro de diálogo. Una hoja se adjunta a la ventana de la que emerge y solo se puede abrir una hoja para una ventana en un momento dado.

[![](dialog-images/sheet08.png "Hoja modal de ejemplo")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Ventanas de preferencias

Una ventana de preferencias es un cuadro de diálogo no modal que contiene la configuración de la aplicación que el usuario cambia con poca frecuencia. Las ventanas de preferencias suelen incluir una barra de herramientas que permite al usuario cambiar entre diferentes grupos de configuración:

[![](dialog-images/dialog02.png "Ventana de preferencias de ejemplo")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Abrir cuadro de diálogo

El cuadro de diálogo Abrir proporciona a los usuarios una manera coherente de buscar y abrir un elemento en una aplicación:

[![](dialog-images/dialog03.png "Cuadro de diálogo abrir")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Cuadros de diálogo de configuración de impresión y página

macOS proporciona cuadros de diálogo estándar de impresión y configuración de página que la aplicación puede mostrar para que los usuarios puedan tener una experiencia de impresión coherente en todas las aplicaciones que usen.

El cuadro de diálogo Imprimir se puede mostrar como un cuadro de diálogo flotante libre:

[![](dialog-images/print01.png "Cuadro de diálogo Imprimir")](dialog-images/print01.png#lightbox)

O bien, se puede mostrar como una hoja:

[![](dialog-images/print02.png "Hoja de impresión")](dialog-images/print02.png#lightbox)

El cuadro de diálogo Configurar página puede mostrarse como un cuadro de diálogo flotante libre:

[![](dialog-images/print03.png "Cuadro de diálogo Configurar página")](dialog-images/print03.png#lightbox)

O bien, se puede mostrar como una hoja:

[![](dialog-images/print04.png "Una hoja de configuración de página")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Cuadro de diálogo guardar

El cuadro de diálogo guardar proporciona a los usuarios una manera coherente de guardar un elemento en una aplicación. El cuadro de diálogo Guardar tiene dos Estados: **Mínima** (también conocido como contraído):

[![](dialog-images/save01.png "Cuadro de diálogo guardar")](dialog-images/save01.png#lightbox)

Y el estado **expandido** :

[![](dialog-images/save02.png "Cuadro de diálogo Guardar expandido")](dialog-images/save02.png#lightbox)

El cuadro de diálogo Guardar **mínimo** también puede mostrarse como una hoja:

[![](dialog-images/save03.png "Una hoja de almacenamiento mínima")](dialog-images/save03.png#lightbox)

Como puede ser el cuadro de diálogo Guardar **expandido** :

[![](dialog-images/save04.png "Una hoja de guardado expandida")](dialog-images/save04.png#lightbox)

Para obtener más información, consulte la sección [Diálogos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) de las [directrices de la interfaz humana de Apple OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Agregar una ventana modal a un proyecto

Además de la ventana principal del documento, es posible que una aplicación de Xamarin. Mac necesite Mostrar otros tipos de ventanas al usuario, como preferencias o paneles del inspector.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **Explorador de soluciones**, abra el `Main.storyboard` archivo para editarlo en el Interface Builder de Xcode.
2. Arrastre un nuevo **controlador de vista** al superficie de diseño:

    [![](dialog-images/new01.png "Seleccionar un controlador de vista de la biblioteca")](dialog-images/new01.png#lightbox)
3. En el **Inspector de identidad**, `CustomDialogController` escriba para el **nombre de clase**: 

    [![](dialog-images/new02.png "Establecer el nombre de clase")](dialog-images/new02.png#lightbox)
4. Vuelva a Visual Studio para Mac, permita que se sincronice con Xcode y cree el `CustomDialogController.h` archivo.
5. Vuelva a Xcode y diseñe la interfaz: 

    [![](dialog-images/new03.png "Diseño de la interfaz de usuario en Xcode")](dialog-images/new03.png#lightbox)
6. Cree un **segue modal** desde la ventana principal de la aplicación al nuevo controlador de vista mediante el arrastre del control desde el elemento de la interfaz de usuario que abrirá el cuadro de diálogo en la ventana del cuadro de diálogo. Asigne el **identificador** `ModalSegue`: 

    [![](dialog-images/new06.png "Un segue modal")](dialog-images/new06.png#lightbox)
7. Conecte todas **las acciones** y **salidas**: 

    [![](dialog-images/new04.png "Configuración de una acción")](dialog-images/new04.png#lightbox)
8. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos con Xcode.

Haga que `CustomDialogController.cs` el archivo tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Este código expone algunas propiedades para establecer el título y la descripción del cuadro de diálogo y algunos eventos para reaccionar al cuadro de diálogo que se va a cancelar o aceptar.

Después, edite `ViewController.cs` el archivo, invalide el `PrepareForSegue` método y haga que tenga un aspecto similar al siguiente:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Este código inicializa el segue que definimos en la Interface Builder de Xcode en el cuadro de diálogo y configura el título y la descripción. También controla la opción que el usuario realiza en el cuadro de diálogo.

Podemos ejecutar la aplicación y mostrar el cuadro de diálogo personalizado:

[![](dialog-images/new05.png "Cuadro de diálogo de ejemplo")](dialog-images/new05.png#lightbox)

Para obtener más información sobre el uso de Windows en una aplicación de Xamarin. Mac, consulte nuestra documentación sobre [Cómo trabajar con Windows](~/mac/user-interface/window.md) .

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Crear una hoja personalizada

Una _hoja_ es un cuadro de diálogo modal que se adjunta a una ventana de documento determinada, lo que impide que los usuarios interactúen con la ventana hasta que descartan el cuadro de diálogo. Una hoja se adjunta a la ventana de la que emerge y solo se puede abrir una hoja para una ventana en un momento dado. 

Para crear una hoja personalizada en Xamarin. Mac, haga lo siguiente:

1. En el **Explorador de soluciones**, abra el `Main.storyboard` archivo para editarlo en el Interface Builder de Xcode.
2. Arrastre un nuevo **controlador de vista** al superficie de diseño:

    [![](dialog-images/new01.png "Seleccionar un controlador de vista de la biblioteca")](dialog-images/new01.png#lightbox)
3. Diseñar la interfaz de usuario:

    [![](dialog-images/sheet01.png "Diseño de la interfaz de usuario")](dialog-images/sheet01.png#lightbox)
4. Cree una **hoja segue** desde la ventana principal al nuevo controlador de vista: 

    [![](dialog-images/sheet02.png "Seleccionar el tipo de segue de hoja")](dialog-images/sheet02.png#lightbox)
5. En el **Inspector de identidad**, asigne un nombre a la **clase** `SheetViewController`del controlador de vista: 

    [![](dialog-images/sheet03.png "Establecer el nombre de clase")](dialog-images/sheet03.png#lightbox)
6. Defina las **salidas** y **las acciones**necesarias: 

    [![](dialog-images/sheet04.png "Definición de las salidas y acciones necesarias")](dialog-images/sheet04.png#lightbox)
7. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos.

Después, edite `SheetViewController.cs` el archivo y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

A continuación, edite el `ViewController.cs` archivo, edite el `PrepareForSegue` método y haga que tenga el aspecto siguiente:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Si ejecutamos la aplicación y se abre la hoja, se adjuntará a la ventana:

[![](dialog-images/sheet08.png "Una hoja de ejemplo")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Crear un cuadro de diálogo de preferencias

Antes de diseñar la vista de preferencias en Interface Builder, es necesario agregar un tipo segue personalizado para controlar el cambio de las preferencias. Agregue una nueva clase al proyecto y llámela `ReplaceViewSeque`. Edite la clase y haga que tenga el aspecto siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Con el segue personalizado creado, podemos agregar una nueva ventana en la Interface Builder de Xcode para administrar nuestras preferencias.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **Explorador de soluciones**, abra el `Main.storyboard` archivo para editarlo en el Interface Builder de Xcode.
2. Arrastre un nuevo **controlador de ventana** al superficie de diseño:

    [![](dialog-images/pref01.png "Seleccionar un controlador de ventana de la biblioteca")](dialog-images/pref01.png#lightbox)
3. Organice la ventana cerca del diseñador de la **barra de menús** :

    [![](dialog-images/pref02.png "Agregar la nueva ventana")](dialog-images/pref02.png#lightbox)
4. Cree copias del controlador de vista asociado, ya que habrá pestañas en la vista de preferencias:

    [![](dialog-images/pref03.png "Agregar los controladores de vista necesarios")](dialog-images/pref03.png#lightbox)
5. Arrastre un **controlador de barra de herramientas** nuevo desde la **biblioteca**:

    [![](dialog-images/pref04.png "Seleccionar un controlador de barra de herramientas de la biblioteca")](dialog-images/pref04.png#lightbox)
6. Y colóquelo en la ventana del Superficie de diseño:

    [![](dialog-images/pref05.png "Agregar un nuevo controlador de barra de herramientas")](dialog-images/pref05.png#lightbox)
7. Diseño del diseño de la barra de herramientas:

    [![](dialog-images/pref06.png "Diseño de la barra de herramientas")](dialog-images/pref06.png#lightbox)
8. Control: haga clic y arrastre desde cada botón de la **barra de herramientas** hasta las vistas que creó anteriormente. Seleccione un tipo de segue **personalizado** :

    [![](dialog-images/pref07.png "Establecimiento del tipo segue")](dialog-images/pref07.png#lightbox)
9. Seleccione el nuevo segue y establezca la **clase** en `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Establecimiento de la clase segue")](dialog-images/pref08.png#lightbox)
10. En el **Diseñador** de la barra de menús del superficie de diseño, en el menú de la aplicación seleccione **preferencias...** , control-haga clic y arrastre hasta la ventana de preferencias para crear un segue de **presentación** :

    [![](dialog-images/pref09.png "Establecimiento del tipo segue")](dialog-images/pref09.png#lightbox)
11. Guarde los cambios y vuelva a Visual Studio para Mac para sincronizarlos.

Si ejecutamos el código y seleccionamos las **preferencias...** en el menú de la **aplicación**, se mostrará la ventana:

[![](dialog-images/pref10.png "Ventana de preferencias de ejemplo")](dialog-images/pref10.png#lightbox)

Para obtener más información sobre cómo trabajar con ventanas y barras de herramientas, consulte la documentación de las [barras de herramientas](~/mac/user-interface/toolbar.md) y [ventanas](~/mac/user-interface/window.md) .

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Guardar y cargar preferencias

En una aplicación típica de macOS, cuando el usuario realiza cambios en cualquiera de las preferencias de usuario de la aplicación, esos cambios se guardan automáticamente. La forma más fácil de controlarlo en una aplicación de Xamarin. Mac es crear una sola clase para administrar todas las preferencias del usuario y compartirla en todo el sistema.

En primer lugar, agregue `AppPreferences` una nueva clase al proyecto y herede `NSObject`de. Las preferencias se diseñarán para usar el [enlace de datos y el código de valor de clave](~/mac/app-fundamentals/databinding.md) , lo que hará que el proceso de creación y mantenimiento de los formularios de preferencias sea mucho más sencillo. Puesto que las preferencias se componen de una pequeña cantidad de tipos de valores simples, use `NSUserDefaults` el integrado para almacenar y recuperar valores.

Edite `AppPreferences.cs` el archivo y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Esta clase contiene algunas rutinas `SaveInt`auxiliares, como, `LoadInt`, `SaveColor` `NSUserDefaults` , `LoadColor`, etc. para facilitar el trabajo. Además, puesto `NSUserDefaults` que no tiene una manera integrada de controlar `NSColors`, los métodos y `NSColorToHexString` `NSColorFromHexString` se usan para convertir los colores en cadenas hexadecimales basadas en Web (`#RRGGBBAA` donde `AA` es la transparencia alfa) que se pueden almacenar y recuperar fácilmente.

En el `AppDelegate.cs` archivo, cree una instancia del objeto **AppPreferences** que se usará para toda la aplicación:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Preferencias de cableado para vistas de preferencias

A continuación, conecte la clase de preferencia a los elementos de la interfaz de usuario en la ventana de preferencias y las vistas creadas anteriormente. En Interface Builder, seleccione un controlador de vista de preferencias y cambie al **Inspector de identidad**, cree una clase personalizada para el controlador: 

[![](dialog-images/prefs12.png "Inspector de identidad")](dialog-images/prefs12.png#lightbox)

Vuelva a Visual Studio para Mac para sincronizar los cambios y abrir la clase recién creada para su edición. Haga que la clase tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Tenga en cuenta que esta clase ha hecho dos cosas: En primer lugar, hay una `App` propiedad auxiliar para facilitar el acceso a **AppDelegate** . En segundo lugar `Preferences` , la propiedad expone la clase **AppPreferences** global para el enlace de datos con cualquier control de IU colocado en esta vista.

A continuación, haga doble clic en el archivo de guion gráfico para volver a abrirlo en Interface Builder (y ver los cambios que se realizaron anteriormente). Arrastre los controles de interfaz de usuario necesarios para compilar la interfaz de preferencias en la vista. Para cada control, cambie al **Inspector de enlace** y enlace a las propiedades individuales de la clase **AppPreference** :

[![](dialog-images/prefs13.png "Inspector de enlace")](dialog-images/prefs13.png#lightbox)

Repita los pasos anteriores para todos los paneles (controladores de vista) y las propiedades de preferencia necesarias.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Aplicar cambios de preferencias a todas las ventanas abiertas

Como se indicó anteriormente, en una aplicación típica de macOS, cuando el usuario realiza cambios en cualquiera de las preferencias de usuario de la aplicación, esos cambios se guardan automáticamente y se aplican a las ventanas que el usuario puede haber abierto en la aplicación.

La planeación y el diseño cuidadosos de las preferencias de la aplicación y Windows permitirán que este proceso se realice de forma transparente y transparente al usuario final, con una cantidad mínima de trabajo de codificación.

Para cualquier ventana que vaya a consumir preferencias de la aplicación, agregue la siguiente propiedad auxiliar a su controlador de vista de contenido para facilitar el acceso a **AppDelegate** :

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

A continuación, agregue una clase para configurar el contenido o el comportamiento en función de las preferencias del usuario:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Debe llamar al método de configuración cuando la ventana se abre por primera vez para asegurarse de que se ajusta a las preferencias del usuario:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Después, edite `AppDelegate.cs` el archivo y agregue el siguiente método para aplicar cualquier cambio de preferencia a todas las ventanas abiertas:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

A continuación, agregue `PreferenceWindowDelegate` una clase al proyecto y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

Esto hará que se envíen los cambios de preferencia a todas las ventanas abiertas cuando se cierre la ventana de preferencias.

Por último, edite el controlador de la ventana de preferencias y agregue el delegado creado anteriormente:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Con todos estos cambios en su lugar, si el usuario edita las preferencias de la aplicación y cierra la ventana de preferencias, los cambios se aplicarán a todas las ventanas abiertas:

[![](dialog-images/prefs14.png "Ventana de preferencias de ejemplo")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Cuadro de diálogo abrir

El cuadro de diálogo Abrir proporciona a los usuarios una manera coherente de buscar y abrir un elemento en una aplicación. Para mostrar un cuadro de diálogo abierto en una aplicación de Xamarin. Mac, use el código siguiente:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

En el código anterior, se abre una nueva ventana de documento para mostrar el contenido del archivo. Tendrá que reemplazar este código por la funcionalidad que requiere la aplicación.

Las siguientes propiedades están disponibles cuando se trabaja con `NSOpenPanel`un:

- **CanChooseFiles** : Si `true` el usuario puede seleccionar archivos.
- **CanChooseDirectories** : Si `true` el usuario puede seleccionar directorios.
- **AllowsMultipleSelection** : Si `true` el usuario puede seleccionar más de un archivo a la vez.
- **ResolveAliases** : Si `true` selecciona y alias, se resuelve en la ruta de acceso del archivo original.
- **AllowedFileTypes** : es una matriz de cadenas de tipos de archivo que el usuario puede seleccionar como extensión o como _UTI_. El valor predeterminado es `null`, que permite abrir cualquier archivo.

El `RunModal ()` método muestra el cuadro de diálogo abrir y permite al usuario seleccionar archivos o directorios (según se especifica en las propiedades) `1` y devuelve si el usuario hace clic en el botón **abrir** .

El cuadro de diálogo Abrir devuelve los archivos o directorios seleccionados del usuario como una matriz de direcciones `URL` URL en la propiedad.

Si ejecutamos el programa y seleccionamos el elemento **abrir...** en el menú **archivo** , se muestra lo siguiente: 

[![](dialog-images/dialog03.png "Cuadro de diálogo abrir")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>Cuadros de diálogo de configuración de impresión y página

macOS proporciona cuadros de diálogo estándar de impresión y configuración de página que la aplicación puede mostrar para que los usuarios puedan tener una experiencia de impresión coherente en todas las aplicaciones que usen.

En el código siguiente se mostrará el cuadro de diálogo de impresión estándar:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Si establecemos la `ShowPrintAsSheet` propiedad en `false`, ejecutamos la aplicación y mostramos el cuadro de diálogo de impresión, se mostrará lo siguiente:

[![](dialog-images/print01.png "Cuadro de diálogo Imprimir")](dialog-images/print01.png#lightbox)

Si establece la `ShowPrintAsSheet` propiedad en `true`, ejecuta la aplicación y muestra el cuadro de diálogo Imprimir, se mostrará lo siguiente:

[![](dialog-images/print02.png "Hoja de impresión")](dialog-images/print02.png#lightbox)

El siguiente código mostrará el cuadro de diálogo de diseño de página:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Si establecemos la `ShowPrintAsSheet` propiedad en `false`, ejecutamos la aplicación y mostramos el cuadro de diálogo diseño de impresión, se mostrará lo siguiente:

[![](dialog-images/print03.png "Cuadro de diálogo Configurar página")](dialog-images/print03.png#lightbox)

Si establece la `ShowPrintAsSheet` propiedad en `true`, ejecuta la aplicación y muestra el cuadro de diálogo diseño de impresión, se mostrará lo siguiente:

[![](dialog-images/print04.png "Una hoja de configuración de página")](dialog-images/print04.png#lightbox)

Para obtener más información sobre cómo trabajar con los cuadros de diálogo de impresión y configuración de páginas, consulte la documentación de [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) y [Introducción a la impresión](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) de Apple.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Cuadro de diálogo guardar

El cuadro de diálogo guardar proporciona a los usuarios una manera coherente de guardar un elemento en una aplicación.

En el código siguiente se mostrará el cuadro de diálogo estándar guardar:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

La `AllowedFileTypes` propiedad es una matriz de cadenas de tipos de archivo que el usuario puede seleccionar para guardar el archivo como. El tipo de archivo puede especificarse como una extensión o _UTI_. El valor predeterminado es `null`, que permite el uso de cualquier tipo de archivo.

Si establecemos la `ShowSaveAsSheet` propiedad en `false`, ejecutará la aplicación y seleccionamos **Guardar como...** en el menú **archivo** , se mostrará lo siguiente:

[![](dialog-images/save01.png "Cuadro de diálogo guardar")](dialog-images/save01.png#lightbox)

El usuario puede expandir el cuadro de diálogo:

[![](dialog-images/save02.png "Cuadro de diálogo Guardar expandido")](dialog-images/save02.png#lightbox)

Si establecemos la `ShowSaveAsSheet` propiedad en `true`, ejecutará la aplicación y seleccionamos **Guardar como...** en el menú **archivo** , se mostrará lo siguiente:

[![](dialog-images/save03.png "Una hoja de guardar")](dialog-images/save03.png#lightbox)

El usuario puede expandir el cuadro de diálogo:

[![](dialog-images/save04.png "Una hoja de guardado expandida")](dialog-images/save04.png#lightbox)

Para obtener más información sobre cómo trabajar con el cuadro de diálogo Guardar, vea la documentación de [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) de Apple.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha realizado una visión detallada del trabajo con ventanas modales, hojas y cuadros de diálogo estándar del sistema en una aplicación de Xamarin. Mac. Vimos los distintos tipos y usos de ventanas, hojas y cuadros de diálogo modales, cómo crear y mantener ventanas y hojas modales en la Interface Builder de Xcode y cómo trabajar con ventanas, hojas y cuadros de diálogo C# modales en el código.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menús](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barras de herramientas](~/mac/user-interface/toolbar.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introducción a las hojas](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introducción a la impresión](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
