---
title: Inicio rápido de Hello, iOS Multiscreen
description: En este documento se muestra cómo ampliar la aplicación de ejemplo Phoneword para agregar una segunda pantalla, para lo que se describe el modelo de diseño Model-View-Controller, la navegación de iOS y otros conceptos de desarrollo básicos de iOS a lo largo del proceso.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: c89a3616bfa239ba919ae9750082bcef48c9f890
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73023375"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Inicio rápido de Hello, iOS Multiscreen

En esta parte del tutorial se agregará una segunda pantalla a la aplicación Phoneword en la que se mostrará un historial de los números de teléfono a los que se llamó con la aplicación. La aplicación final tendrá una segunda pantalla en que se muestra el historial de llamadas, como se ilustra en las capturas de pantalla siguientes:

[![](hello-ios-multiscreen-quickstart-images/00.png "The final application will have a second screen that displays the call history, as illustrated by this screenshot")](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

En el [análisis detallado incluido](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md), revisaremos la aplicación que se ha creado y explicaremos la arquitectura, la navegación y otros conceptos nuevos de iOS que surjan durante el proceso.

## <a name="requirements"></a>Requisitos

Este guía parte del punto en que se quedó el documento Hello, iOS, y es necesario completar el [Inicio rápido de Hello, iOS](~/ios/get-started/hello-ios/index.md). Descargue la versión completa de la aplicación Phoneword desde el [Ejemplo de Hello, iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Tutorial para macOS

En este tutorial se agregará una pantalla Historial de llamadas a nuestra aplicación **Phoneword**.

1. Abra la aplicación **Phoneword** en Visual Studio para Mac. Si es necesario, la aplicación completa de Phoneword de la guía [Tutorial de Hello, iOS](~/ios/get-started/hello-ios/index.md) puede descargarse [aquí](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

2. Abra el archivo **Main.storyboard** desde el **Panel de solución**:

    ![](hello-ios-multiscreen-quickstart-images/02new.png "The Main.storyboard in the iOS Designer")

3. Arrastre un **controlador de navegación** desde el **cuadro de herramientas** a la superficie de diseño (es posible que tenga que reducir el tamaño para ajustarlo todo en la superficie de diseño):

    ![](hello-ios-multiscreen-quickstart-images/03new.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Arrastre el objeto **Sourceless Segue** (que es la flecha gris a la izquierda del controlador de vista única) hasta el **controlador de navegación** para cambiar el punto inicial de la aplicación:

    ![](hello-ios-multiscreen-quickstart-images/04new.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Seleccione el **controlador de vista raíz** existente; para ello, haga clic en la barra inferior y pulse **Eliminar** para quitarlo de la superficie de diseño.
A continuación, mueva la escena de **Phoneword** junto al **controlador de navegación**:

    ![](hello-ios-multiscreen-quickstart-images/05new.png "Move the Phoneword scene next to the navigation controller")

6. Establezca el **ViewController** como el **controlador de vista raíz** del controlador de navegación. Pulse la tecla **Ctrl** y haga clic dentro del **controlador de navegación**. Debe aparecer una línea azul. A continuación, sin dejar de pulsar la tecla **Ctrl**, arrástrelo desde el **controlador de navegación** a la escena de **Phoneword** y suéltelo. Esto se denomina _Ctrl y arrastrar_:

    ![](hello-ios-multiscreen-quickstart-images/06.png "Drag from the navigation controller to the Phoneword scene and release")

7. Desde el elemento flotante, establezca la relación con **Raíz**:

    ![](hello-ios-multiscreen-quickstart-images/07new.png "Setting the relationship to Root")

    **ViewController** es ahora el **controlador de vista raíz del controlador de navegación:**

    ![](hello-ios-multiscreen-quickstart-images/08.png "The ViewController is now the navigation controllers Root view controller")

8. Haga doble clic en el barra **Título** de la pantalla **Phoneword** y cambie el **título** por **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/09.png "Change the Title to 'Phoneword'")

9. Arrastre un **botón** desde el **cuadro de herramientas** y colóquelo debajo del **botón Llamada**. Arrastre los controladores para hacer que el **botón** nuevo tenga el mismo ancho que el **botón Llamada**:

    ![](hello-ios-multiscreen-quickstart-images/10new.png "Make the new Button the same width as the Call Button")

10. En el **Panel de propiedades**, cambie el **nombre** del botón por **CallHistoryButton** y cambie el **título** por **Historial de llamadas**:

    ![](hello-ios-multiscreen-quickstart-images/11new.png "Change the Name of the Button to CallHistoryButton and change the Title to Call History")

11. Cree la pantalla **Historial de llamadas**. Desde el **Cuadro de herramientas**, arrastre un **controlador de vista de tabla** a la superficie de diseño:

    ![](hello-ios-multiscreen-quickstart-images/12new.png "Drag a table view controller onto the design surface")

12. A continuación, haga clic en la barra negra situada en la parte inferior de la escena para seleccionar el **controlador de vista de tabla**. En el **Panel de propiedades**, cambie la clase del **controlador de vista de tabla** por `CallHistoryController` y pulse **Entrar**:

    ![](hello-ios-multiscreen-quickstart-images/13new.png "Change the table view controllers class to CallHistoryController")

    El Diseñador de iOS generará una clase auxiliar personalizada denominada `CallHistoryController` para administrar la jerarquía de la vista de contenido de esta pantalla. El archivo **CallHistoryController.cs** aparecerá en el **Panel de solución**:

    ![](hello-ios-multiscreen-quickstart-images/14new.png "The CallHistoryController.cs file in the Solution Pad")

13. Haga doble clic en el archivo **CallHistoryController.cs** para abrirlo y reemplace el contenido por el código siguiente:
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Guarde la aplicación (**⌘ + s**) y compílela (**⌘ + b**) para asegurarse de que no existen errores.

14. Cree un objeto _Segue_ (transición) entre la escena de **Phoneword** y la escena del **Historial de llamadas**.
  En la **escena de Phoneword**, seleccione el **botón Historial de llamadas**, pulse la tecla Ctrl y arrástrelo desde el **botón** a la escena del **Historial de llamadas**:

    ![](hello-ios-multiscreen-quickstart-images/15.png "Ctrl-drag from the Button to the Call History scene")

    En el elemento flotante **Segue de acción**, seleccione **Mostrar**.

    El Diseñador de iOS agregará un objeto Segue entre las dos escenas:

    ![](hello-ios-multiscreen-quickstart-images/17new.png "The Segue between the two scenes")

15. Agregue un **título** al **controlador de vista de tabla**; para ello, seleccione la barra negra situada en la parte inferior de la escena y cambie el **título del controlador de vista** por **Historial de llamadas** en el **Panel de propiedades**:

    ![](hello-ios-multiscreen-quickstart-images/18new.png "Change the view controller title to Call History in the Properties Pad")

16. Cuando la aplicación se ejecute, el **botón Historial de llamadas** se abrirá en la pantalla **Historial de llamadas**, pero la vista de tabla estará vacía porque no hay ningún código para mostrar los números de teléfono ni del cual realizar un seguimiento.

    Esta aplicación almacenará los números de teléfono como una lista de cadenas.

    Agregue una directiva de `using` para `System.Collections.Generic` en la parte superior de **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifique la clase de `ViewController` con el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Aquí suceden algunas cosas:

    - La variable `translatedNumber` se movió desde el método `ViewDidLoad` a una _variable de nivel de clase_.
    - El código **CallButton** se modificó para agregar los números marcados a la lista de números de teléfono mediante una llamada a `PhoneNumbers.Add(translatedNumber)`.
    - Se agregó el método `PrepareForSegue`.

    Guarde y compile la aplicación para asegurarse de que no existen errores.

18. Pulse el botón **Iniciar** para iniciar la aplicación dentro del **Simulador de iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "Press the Start button to launch the application inside the iOS Simulator")

Enhorabuena por completar su primera aplicación multipantalla de Xamarin.iOS.

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Tutorial para Windows

En este tutorial se agregará una pantalla Historial de llamadas a nuestra aplicación **Phoneword**.

1. Abra la aplicación **Phoneword** en Visual Studio. Si es necesario, descargue la [aplicación completa de Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) de la guía [Tutorial de Hello, iOS](~/ios/get-started/hello-ios/index.md). Recuerde que es necesario conectarse a un equipo [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para utilizar el Diseñador de iOS y el Simulador de iOS.

2. Empiece por editar la interfaz de usuario. Abra el archivo **Main.storyboard** desde el **Explorador de soluciones** y asegúrese de establecer **Ver como** en _iPhone 6_:

    ![](hello-ios-multiscreen-quickstart-images/image1.png "The Main.storyboard in the iOS Designer")

3. Arrastre un **controlador de navegación** desde el **cuadro de herramientas** a la superficie de diseño:

    ![](hello-ios-multiscreen-quickstart-images/image2.png "Drag a navigation controller from the Toolbox onto the design surface")

4. Arrastre el objeto **Sourceless Segue** (que es la flecha gris a la izquierda de la escena de **Phoneword**) desde la escena de **Phoneword** al **controlador de navegación** para cambiar el punto inicial de la aplicación:

    ![](hello-ios-multiscreen-quickstart-images/image3.png "Drag the Sourceless Segue to the navigation controller to change the starting point of the application")

5. Haga clic en la barra negra para seleccionar el **controlador de vista raíz** y pulse **Eliminar** para quitarlo de la superficie de diseño.
  A continuación, mueva la escena de **Phoneword** junto al **controlador de navegación**:

    ![](hello-ios-multiscreen-quickstart-images/image4.png "Move the Phoneword scene next to the navigation controller")

6. Establezca el **ViewController** como el controlador de vista raíz del controlador de navegación. Pulse la tecla **Ctrl** y haga clic dentro del **controlador de navegación**. Debe aparecer una línea azul. A continuación, sin dejar de pulsar la tecla **Ctrl**, arrástrelo desde el **controlador de navegación** a la escena de **Phoneword** y suéltelo. Esto se denomina _Ctrl y arrastrar_:

    ![](hello-ios-multiscreen-quickstart-images/image5.png "Drag from the navigation controller to the Phoneword scene and release")

7. Desde el elemento flotante, establezca la relación con **Raíz**:

    ![](hello-ios-multiscreen-quickstart-images/image6.png "Set the relationship to Root")

    El **ViewController** es ahora nuestro **controlador de vista raíz del controlador de navegación.**

8. Haga doble clic en el barra **Título** de la pantalla **Phoneword** y cambie el **título** por **Phoneword**:

    ![](hello-ios-multiscreen-quickstart-images/image7.png "Change the Title to Phoneword")

9. Arrastre un **botón** desde el **cuadro de herramientas** y colóquelo debajo del **botón Llamada**. Arrastre los controladores para hacer que el **botón** nuevo tenga el mismo ancho que el **botón Llamada**:

    ![](hello-ios-multiscreen-quickstart-images/image8.png "Make the new Button the same width as the Call Button")

10. En el **Explorador de propiedades**, cambie el **nombre** del **botón** por `CallHistoryButton` y cambie el **título** por **Historial de llamadas**:

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Change the Name of the Button to 'CallHistoryButton' and the Title to 'Call History'")

11. Cree la pantalla **Historial de llamadas**. Desde el **Cuadro de herramientas**, arrastre un **controlador de vista de tabla** a la superficie de diseño:

    ![](hello-ios-multiscreen-quickstart-images/image10.png "Drag a table view controller onto the design surface")

12. Haga clic en la barra negra situada en la parte inferior de la escena para seleccionar el **controlador de vista de tabla**. En el **Explorador de propiedades**, cambie la clase del **controlador de vista de tabla** por `CallHistoryController` y pulse **Entrar**:

    ![](hello-ios-multiscreen-quickstart-images/image11.png "Change the table view controllers class to CallHistoryController")

    El Diseñador de iOS generará una clase auxiliar personalizada denominada `CallHistoryController` para administrar la jerarquía de la vista de contenido de esta pantalla. El archivo **CallHistoryController.cs** aparecerá en el **Explorador de soluciones**:

    ![](hello-ios-multiscreen-quickstart-images/image12.png "The CallHistoryController.cs file in the Solution Explorer")

13. Haga doble clic en el archivo **CallHistoryController.cs** para abrirlo y reemplace el contenido por el código siguiente:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Guarde la aplicación y compílela para asegurarse de que no existen errores. Puede omitir las advertencias de compilación por ahora.

14. Cree un objeto _Segue_ (transición) entre la escena de **Phoneword** y la escena del **Historial de llamadas**.
  En la **escena de Phoneword**, seleccione el **botón Historial de llamadas**, **pulse la tecla Ctrl y arrástrelo** desde el **botón** a la escena del **Historial de llamadas**:

    ![](hello-ios-multiscreen-quickstart-images/image13.png "Ctrl-drag from the Button to the Call History scene")

    En el elemento flotante **Segue de acción**, seleccione **Mostrar**:

    ![](hello-ios-multiscreen-quickstart-images/image14.png "Select Show as the segue type")

    El Diseñador de iOS agregará un objeto Segue entre las dos escenas:

    ![](hello-ios-multiscreen-quickstart-images/image15.png "The Segue between the two scenes")

15. Agregue un **título** al **controlador de vista de tabla**; para ello, seleccione la barra negra situada en la parte inferior de la escena y cambie **Controlador de vista > Título** por **Historial de llamadas** en el **Explorador de propiedades**:

    ![](hello-ios-multiscreen-quickstart-images/image16.png "Change the view controller Title to Call History")

16. Cuando la aplicación se ejecute, el **botón Historial de llamadas** se abrirá en la pantalla **Historial de llamadas**, pero la vista de tabla estará vacía porque no hay ningún código para mostrar los números de teléfono ni del cual realizar un seguimiento.

    Esta aplicación almacenará los números de teléfono como una lista de cadenas.

    Agregue una directiva de `using` para `System.Collections.Generic` en la parte superior de **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifique la clase de `ViewController` con el código siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Aquí suceden algunas cosas.
    - La variable `translatedNumber` se ha movido desde el método `ViewDidLoad` a una _variable de nivel de clase_.
    - El código **CallButton** se modificó para agregar los números marcados a la lista de números de teléfono mediante una llamada a `PhoneNumbers.Add(translatedNumber)`.
    - Se agregó el método `PrepareForSegue`.

    Guarde y compile la aplicación para asegurarse de que no existen errores.

    Guarde y compile la aplicación para asegurarse de que no existen errores.

18. Pulse el botón **Iniciar** para iniciar nuestra aplicación dentro del **Simulador de iOS**:

    ![](hello-ios-multiscreen-quickstart-images/19.png "The first screen of the sample app")

Enhorabuena por completar su primera aplicación multipantalla de Xamarin.iOS.

::: zone-end

La aplicación ahora puede controlar la navegación con ambos objetos Segue de guion gráfico y mediante el código. Ahora es el momento de analizar minuciosamente las herramientas y los conocimientos que acaba de aprender en el [Análisis detallado de Multipantalla de Hello, Android](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Vínculos relacionados

- [Hola, iOS (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Directrices de la interfaz humana de iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portal de aprovisionamiento de iOS](https://developer.apple.com/ios/manage/overview/index.action)
