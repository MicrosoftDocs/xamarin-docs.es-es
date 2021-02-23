---
title: Inicio rápido de la creación de una aplicación de Xamarin.Forms
description: En este artículo se explica cómo crear una aplicación de Xamarin.Forms Shell, que reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 359F5012-4409-42A7-BEDF-C1DB9AF86DED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d21daa89322021e80a46753c864da3cb671b2d6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818724"
---
# <a name="create-a-xamarinforms-application-quickstart"></a>Inicio rápido de la creación de una aplicación de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)

En este inicio rápido aprenderá a:

- Crear una aplicación de Xamarin.Forms Shell.
- Definir la interfaz de usuario para una página mediante el lenguaje XAML e interactuar con elementos de XALM del código.
- Describir la jerarquía visual de una aplicación de Shell mediante la creación de subclase de la clase `Shell`.

El inicio rápido lo guía sobre cómo crear una aplicación de Xamarin.Forms Shell multiplataforma que permite escribir una nota y conservarla en el almacenamiento del dispositivo. A continuación se muestra la aplicación final:

[![Aplicación de notas](app-images/screenshots1-sml.png)](app-images/screenshots1.png#lightbox)
[![Página Acerca de Notas](app-images/screenshots2-sml.png)](app-images/screenshots2.png#lightbox)

::: zone pivot="windows"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2019 (última versión), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada.
- Conocimientos de C#.
- (opcional) Un equipo Mac emparejado para compilar la aplicación en iOS.

Para más información sobre estos requisitos previos, vea [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introducción a Visual Studio 2019

1. Inicie Visual Studio 2019 y, en la ventana de inicio, haga clic en **Crear un proyecto nuevo** para crear un proyecto:

    ![Nueva solución](app-images/vs/new-solution.png)

2. En la ventana **Crear un proyecto nuevo**, seleccione **Móvil** en la lista desplegable **Tipo de proyecto**, elija la plantilla **Aplicación móvil (Xamarin.Forms)** y haga clic en el botón **Siguiente**:

    ![Elegir una plantilla](app-images/vs/new-project.png)

3. En el cuadro de diálogo **Configure su nuevo proyecto**, establezca **Nombre del proyecto** en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear**:   

    ![Configuración de la aplicación de Shell](app-images/vs/configure-project.png)

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que tanto la solución como el proyecto se denominen **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

4. En el cuadro de diálogo **Nueva aplicación móvil**, seleccione la plantilla **Tabbed** (Con pestañas) y haga clic en el botón **Crear**:

    ![Creación de la aplicación de Shell](app-images/vs/create-project.png)

    Una vez creado el proyecto, cierre el archivo **GettingStarted.txt**.

    Para más información sobre la biblioteca de .NET Standard creada, consulte [Anatomía de una aplicación de Xamarin.Forms ](deepdive.md#anatomy-of-a-xamarinforms-application) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

5. En el **Explorador de soluciones**, en el proyecto **Notes**, elimine las carpetas siguientes y su contenido:

    - **Modelos**
    - **Servicios**
    - **ViewModels**
    - **Vistas**

6. En el **Explorador de soluciones**, en el proyecto **Notes**, elimine **GettingStarted.txt**.

7. En el **Explorador de soluciones**, en el proyecto **Notes**, agregue una carpeta nueva denominada **Vistas**.

8. En el **Explorador de soluciones**, en el proyecto **Notes**, seleccione la carpeta **Vistas**, haga clic con el botón derecho y seleccione **Agregar > Nuevo elemento…** . En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página de contenido**, asigne el nombre **NotesPage** al archivo nuevo y haga clic en el botón **Agregar**:

    ![Agregar NotesPage](app-images/vs/add-notespage.png)

    Esto agregará una página nueva denominada **NotesPage** en la carpeta **Vistas**. Esta página será la página principal de la aplicación.

9. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml** para abrirlo:

    ![Abrir NotesPage.xaml](app-images/vs/open-notespage-xaml.png)

10. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos objetos [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Los dos objetos `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml**.  

12. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml.cs** para abrirlo:

    ![Abrir NotesPage.xaml.cs](app-images/vs/open-notespage-codebehind.png)

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al presionar el control [`Button`](xref:Xamarin.Forms.Button) **Guardar**, se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al presionar el control `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para más información sobre la interacción del usuario, consulte [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml.cs**.    

13. En el **Explorador de soluciones**, en el proyecto **Notes**, seleccione la carpeta **Vistas**, haga clic con el botón derecho y seleccione **Agregar > Nuevo elemento…** . En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página diálogo**, asigne el nombre **AboutPage** al archivo nuevo y haga clic en el botón **Agregar**:

    ![Agregar AboutPage](app-images/vs/add-aboutpage.png)

    Esto agregará una página nueva denominada **AboutPage** en la carpeta **Vistas**.

14. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **AboutPage.xaml** para abrirlo:

    ![Abrir AboutPage.xaml](app-images/vs/open-aboutpage-xaml.png)

15. En **AboutPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de un [`Image`](xref:Xamarin.Forms.Image), dos objetos [`Label`](xref:Xamarin.Forms.Label) que muestran texto y un [`Button`](xref:Xamarin.Forms.Button). Los dos objetos `Label` y `Button` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout), mientras que `Image` y `StackLayout` se disponen en horizontal en [`Grid`](xref:Xamarin.Forms.Grid). Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **AboutPage.xaml**.    

16. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **AboutPage.xaml.cs** para abrirlo:

    ![Abrir AboutPage.xaml.cs](app-images/vs/open-aboutpage-codebehind.png)

17. En **AboutPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    Este código define el controlador de eventos `OnButtonClicked`, que se ejecuta cuando se presiona [`Button`](xref:Xamarin.Forms.Button) **Learn more**. Al presionar el botón, se inicia un explorador web y se muestra la página representada por el argumento de URI para el método `OpenAsync`. Para más información sobre la interacción del usuario, consulte [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **AboutPage.xaml.cs**.

18. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **AppShell.xaml** para abrirlo:

    ![Abrir AppShell.xaml](app-images/vs/open-appshell-xaml.png)

19. En **AppShell.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    Este código define mediante declaración la jerarquía visual de la aplicación, que consta de un elemento `TabBar` que contiene dos objetos `ShellContent`. Estos objetos no representan ningún elemento de interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario del contenido. Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **AppShell.xaml**.    

20. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **AppShell.xaml** y haga doble clic en **AppShell.xaml.cs** para abrirlo:

    ![Abrir AppShell.xaml.cs](app-images/vs/open-appshell-codebehind.png)

21. En **AppShell.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    Presione **CTRL+S** para guardar los cambios en **AppShell.xaml.cs**.

22. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **App.xaml** para abrirlo:

    ![Abrir App.xaml](app-images/vs/open-app-xaml.png)

23. En **App.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    Este código define mediante declaración una clase `App`, que es responsable de crear instancias de la aplicación.

    Presione **CTRL+S** para guardar los cambios en **App.xaml**.    

24. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo:

    ![Abrir App.xaml.cs](app-images/vs/open-app-codebehind.png)

25. En **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Este código define el código subyacente de la clase `App`, que es responsable de crear instancias de la aplicación. Inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en el objeto `Shell` en subclase.

    Presione **CTRL+S** para guardar los cambios en **App.xaml.cs**.    

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![Compilación correcta](app-images/vs/build-successful.png)

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      ![Barra de herramientas de Android en Visual Studio](app-images/vs/android-start.png)

      ![Notes en Android Emulator](app-images/vs/notes1-android.png)

    Escriba una nota y pulse el botón **Guardar**. A continuación, cierre la aplicación y vuelva a iniciarla para asegurarse de que se vuelve a cargar la nota que escribió.

    Presione el icono de la pestaña **Acerca de** para ir a `AboutPage`:

      ![Página Acerca de Notas en Android Emulator](app-images/vs/notes2-android.png)

    Presione el botón **Más información** para abrir la página web de los inicios rápidos.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launch-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.    

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![Establecer Notes.iOS como proyecto de inicio](app-images/vs/set-startup-project-ios.png)

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

      ![Barra de herramientas de iOS en Visual Studio](app-images/vs/ios-start.png)

      [![Notes en el simulador de iOS](app-images/vs/notes1-ios.png)](app-images/vs/notes1-ios-large.png#lightbox)

    Escriba una nota y pulse el botón **Guardar**. A continuación, cierre la aplicación y vuelva a iniciarla para asegurarse de que se vuelve a cargar la nota que escribió.

    Presione el icono de la pestaña **Acerca de** para ir a `AboutPage`:

      [![Página Acerca de Notas en iOS Simulator](app-images/vs/notes2-ios.png)](app-images/vs/notes2-ios-large.png#lightbox)

    Presione el botón **Más información** para abrir la página web de los inicios rápidos.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launch-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio para Mac (última versión) con compatibilidad con la plataforma iOS y Android.
- Xcode (última versión).
- Conocimientos de C#.

Para más información sobre estos requisitos previos, vea [Instalación de Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

1. Para crear un proyecto, inicie Visual Studio para Mac y, en la ventana de inicio, haga clic en **Nuevo**:

    ![Nueva solución](app-images/vsmac/new-project.png)

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms de shell** y haga clic en el botón **Siguiente**:

    ![Elegir una plantilla](app-images/vsmac/choose-template.png)

3. En el cuadro de diálogo **Configure your Shell Forms app** (Configurar la aplicación de Forms de shell), asigne el nombre **Notes** a la aplicación nueva y haga clic en el botón **Siguiente**:    

    ![Configuración de la aplicación de Shell](app-images/vsmac/configure-app.png)

4. En el cuadro de diálogo **Configure your new Shell Forms app** (Configurar su nueva aplicación de Forms de shell), deje los nombres de solución y de proyecto establecidos en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crearlo:

    ![Configurar el proyecto de shell](app-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que tanto la solución como el proyecto se denominen **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

    Para más información sobre la biblioteca de .NET Standard creada, consulte [Anatomía de una aplicación de Xamarin.Forms ](deepdive.md#anatomy-of-a-xamarinforms-application) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

5. En el **Panel de solución**, en el proyecto **Notes**, elimine las carpetas siguientes y su contenido:

    - **Modelos**
    - **Servicios**
    - **ViewModels**
    - **Vistas**

6. En el **Panel de solución**, en el proyecto **Notes**, elimine **GettingStarted.txt**.

7. En el **Panel de solución**, en el proyecto **Notes**, agregue una carpeta nueva denominada **Vistas**.

8. En el **Panel de solución**, en el proyecto **Notes**, seleccione la carpeta **Vistas**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo…** . En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > XAML ContentPage de Forms**, asigne el nombre **NotesPage** al archivo nuevo y haga clic en el botón **Nuevo**:

    ![Agregar NotesPage](app-images/vsmac/add-notespage.png)

    Esto agregará una página nueva denominada **NotesPage** en la carpeta **Vistas**. Esta página será la página principal de la aplicación.

9. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml** para abrirlo:

    ![Abrir NotesPage.xaml](app-images/vsmac/open-notespage-xaml.png)

10. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos objetos [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Los dos objetos `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

12. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **NotesPage.xaml.cs** para abrirlo:

    ![Abrir NotesPage.xaml.cs](app-images/vsmac/open-notespage-codebehind.png)

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al presionar el control [`Button`](xref:Xamarin.Forms.Button) **Guardar**, se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al presionar el control `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para más información sobre la interacción del usuario, consulte [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

13. En el **Panel de solución**, en el proyecto **Notes**, seleccione la carpeta **Vistas**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo…** . En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > XAML ContentPage de Forms**, asigne el nombre **AboutPage** al archivo nuevo y haga clic en el botón **Nuevo**:

    ![Agregar AboutPage](app-images/vsmac/add-aboutpage.png)

14. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **AboutPage.xaml** para abrirlo:

    ![Abrir AboutPage.xaml](app-images/vsmac/open-aboutpage-xaml.png)

    Esto agregará una página nueva denominada **AboutPage** en la carpeta **Vistas**.

15. En **AboutPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consta de un [`Image`](xref:Xamarin.Forms.Image), dos objetos [`Label`](xref:Xamarin.Forms.Label) que muestran texto y un [`Button`](xref:Xamarin.Forms.Button). Los dos objetos `Label` y `Button` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout), mientras que `Image` y `StackLayout` se disponen en horizontal en [`Grid`](xref:Xamarin.Forms.Grid). Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Para guardar los cambios en **AboutPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

16. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **AboutPage.xaml.cs** para abrirlo:

    ![Abrir AboutPage.xaml.cs](app-images/vsmac/open-aboutpage-codebehind.png)

17. En **AboutPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    Este código define el controlador de eventos `OnButtonClicked`, que se ejecuta cuando se presiona [`Button`](xref:Xamarin.Forms.Button) **Learn more**. Al presionar el botón, se inicia un explorador web y se muestra la página representada por el argumento de URI para el método `OpenAsync`. Para más información sobre la interacción del usuario, consulte [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en el [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Para guardar los cambios en **AboutPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

18. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **AppShell.xaml** para abrirlo:

    ![Abrir AppShell.xaml](app-images/vsmac/open-appshell-xaml.png)

19. En **AppShell.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    Este código define mediante declaración la jerarquía visual de la aplicación, que consta de un elemento `TabBar` que contiene dos objetos `ShellContent`. Estos objetos no representan ningún elemento de interfaz de usuario, sino más bien la organización de la jerarquía visual de la aplicación. Shell tomará estos elementos y generará la interfaz de usuario del contenido. Para más información sobre la creación de la interfaz de usuario, consulte [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

    Para guardar los cambios en **AppShell.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

20. En el **Panel de solución**, en el proyecto **Notes**, expanda **AppShell.xaml** y haga doble clic en **AppShell.xaml.cs** para abrirlo:

    ![Abrir AppShell.xaml.cs](app-images/vsmac/open-appshell-codebehind.png)

21. En **AppShell.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    Para guardar los cambios en **AppShell.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

22. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **App.xaml** para abrirlo:

    ![Abrir App.xaml](app-images/vsmac/open-app-xaml.png)

23. En **App.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    Este código define mediante declaración una clase `App`, que es responsable de crear instancias de la aplicación.

    Para guardar los cambios en **App.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

24. En el **Panel de solución**, en el proyecto **Notes**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo:

    ![Abrir App.xaml.cs](app-images/vsmac/open-app-codebehind.png)

25. En **App.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Este código define el código subyacente de la clase `App`, que es responsable de crear instancias de la aplicación. Inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en el objeto `Shell` en subclase.

    Para guardar los cambios en **App.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

### <a name="building-the-quickstart"></a>Compilación del inicio rápido

1. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). El proyecto se compilará y aparecerá un mensaje de operación correcta en la barra de herramientas de Visual Studio para Mac:

      ![Compilación correcta](app-images/vsmac/build-successful.png)

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En el **Panel de solución**, seleccione el proyecto **Notes.iOS**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![Establecer iOS como proyecto de inicio](app-images/vsmac/set-startup-project-ios.png)

3. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de iOS Simulator:

      ![Barra de herramientas de Visual Studio para Mac](app-images/vsmac/start.png)

      ![Notas en el simulador de iOS](app-images/vsmac/notes1-ios.png)

    Escriba una nota y pulse el botón **Guardar**. A continuación, cierre la aplicación y vuelva a iniciarla para asegurarse de que se vuelve a cargar la nota que escribió.

    Presione el icono de la pestaña **Acerca de** para ir a `AboutPage`:

      ![Página Acerca de Notas en iOS Simulator](app-images/vsmac/notes2-ios.png)

    Presione el botón **Más información** para abrir la página web de los inicios rápidos.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launch-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **Panel de solución**, seleccione el proyecto **Notes.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![Establecer Android como proyecto de inicio](app-images/vsmac/set-startup-project-android.png)

5. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      ![Notes en Android Emulator](app-images/vsmac/notes1-android.png)

    Escriba una nota y pulse el botón **Guardar**. A continuación, cierre la aplicación y vuelva a iniciarla para asegurarse de que se vuelve a cargar la nota que escribió.

    Presione el icono de la pestaña **Acerca de** para ir a `AboutPage`:

      ![Página Acerca de Notas en Android Emulator](app-images/vsmac/notes2-android.png)

    Presione el botón **Más información** para abrir la página web de los inicios rápidos.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launch-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Crear una aplicación de Xamarin.Forms Shell.
- Definir la interfaz de usuario para una página mediante el lenguaje XAML e interactuar con elementos de XALM del código.
- Describir la jerarquía visual de una aplicación de Shell mediante la creación de subclase de la clase `Shell`.

Continúe en el inicio rápido siguiente para agregar más páginas a esta aplicación de Xamarin.Forms Shell.

> [!div class="nextstepaction"]
> [Siguiente](navigation.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)
- [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md)
