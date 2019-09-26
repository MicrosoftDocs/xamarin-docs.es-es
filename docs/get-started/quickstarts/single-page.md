---
title: Creación de una aplicación de página única de Xamarin.Forms
description: En este artículo se explica cómo crear una aplicación de Xamarin. Forms multiplataforma de una sola página, lo que le permite escribir una nota y guardarla en el almacenamiento del dispositivo.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 48af81856ebe92a712b5d41d8d856bb00a526982
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68655005"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Crear una aplicación de Xamarin. Forms de una sola página

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

En esta guía de inicio rápido, obtendrá información sobre cómo:

- Cree una aplicación de Xamarin. Forms multiplataforma.
- Defina la interfaz de usuario para una página mediante el lenguaje XAML.
- Interactúe con los elementos de la interfaz de usuario XAML desde el código.

En la guía de inicio rápido se explica cómo crear una aplicación de Xamarin. Forms multiplataforma, que le permite escribir una nota y guardarla en el almacenamiento del dispositivo. A continuación se muestra la aplicación final:

[![](single-page-images/screenshots-sml.png "Aplicación de notas")](single-page-images/screenshots.png#lightbox "Aplicación de notas")

::: zone pivot="windows"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2019 (versión más reciente), con la carga de trabajo **desarrollo para dispositivos móviles con .net** instalada.
- Conocimiento de C#.
- opta Un equipo Mac emparejado para compilar la aplicación en iOS.

Para obtener más información acerca de estos requisitos previos, consulte [instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introducción a Visual Studio 2019

1. Inicie Visual Studio 2019 y, en la ventana Inicio, haga clic en **crear un nuevo proyecto** para crear un nuevo proyecto:

    ![](single-page-images/vs/new-solution-2019.png "Nuevo proyecto")

2. En la ventana **crear un nuevo proyecto** , seleccione **móvil** en el menú desplegable **tipo de proyecto** , seleccione la **aplicación móvil (plantilla de Xamarin. Forms** ) y haga clic en el botón **siguiente** :

    ![](single-page-images/vs/new-project-2019.png "Plantillas de proyectos multiplataforma")

3. En la ventana **configurar el nuevo proyecto** , establezca el **nombre del proyecto** en **notas**, elija una ubicación adecuada para el proyecto y haga clic en el botón **crear** :

    ![](single-page-images/vs/configure-project.png "Configurar el proyecto")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

4. En el cuadro de diálogo **nueva aplicación multiplataforma** , haga clic en **aplicación en blanco**y haga clic en el botón **Aceptar** :

    ![](single-page-images/vs/new-app-2019.png "Nueva aplicación multiplataforma")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

5. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml-2019.png "Abrir MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define la interfaz de usuario de la página de forma declarativa, que consta [`Label`](xref:Xamarin.Forms.Label) de un para mostrar texto [`Editor`](xref:Xamarin.Forms.Editor) , un para la entrada de [`Button`](xref:Xamarin.Forms.Button) texto y dos instancias que dirigen a la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre cómo crear la interfaz de usuario, consulte la [interfaz de usuario](deepdive.md#user-interface) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind-2019.png "Abrir MainPage.xaml.cs")

8. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [responder a la interacción del usuario](deepdive.md#responding-to-user-interaction) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Compilación satisfactoria")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Barra de herramientas de Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notas en el Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Establecer iOS como proyecto de inicio")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Barra de herramientas de iOS de Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2017, con la carga de trabajo **desarrollo para dispositivos móviles con .net** instalada.
- Conocimiento de C#.
- opta Un equipo Mac emparejado para compilar la aplicación en iOS.

Para obtener más información acerca de estos requisitos previos, consulte [instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introducción a Visual Studio 2017

1. Inicie Visual Studio 2017 y, en la página de inicio, haga clic en **crear nuevo proyecto...** para crear un nuevo proyecto:

    ![](single-page-images/vs/new-solution.png "Nuevo proyecto")

2. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)** , establezca el nombre en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-project.png "Plantillas de proyectos multiplataforma")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

3. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **Aplicación en blanco**, seleccione **.NET Standard** como estrategia de código de uso compartido y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-app.png "Nueva aplicación multiplataforma")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

5. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define la interfaz de usuario de la página de forma declarativa, que consta [`Label`](xref:Xamarin.Forms.Label) de un para mostrar texto [`Editor`](xref:Xamarin.Forms.Editor) , un para la entrada de [`Button`](xref:Xamarin.Forms.Button) texto y dos instancias que dirigen a la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre cómo crear la interfaz de usuario, consulte la [interfaz de usuario](deepdive.md#user-interface) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

6. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

7. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [responder a la interacción del usuario](deepdive.md#responding-to-user-interaction) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Compilación satisfactoria")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Barra de herramientas de Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notas en el Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Establecer iOS como proyecto de inicio")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Barra de herramientas de iOS de Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio para Mac (versión más reciente), con compatibilidad con la plataforma iOS y Android instalada.
- Xcode (versión más reciente).
- Conocimiento de C#.

Para obtener más información acerca de estos requisitos previos, consulte [instalación de Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

1. Inicie Visual Studio para Mac y, en la ventana Inicio, haga clic en **nuevo** para crear un nuevo proyecto:

    ![](single-page-images/vsmac/new-project.png "Nueva solución")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](single-page-images/vsmac/choose-template.png "Elija una plantilla")

3. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, asigne a la nueva aplicación el nombre **Notes**, asegúrese de que el botón de radio **Usar .NET Standard** esté seleccionado y haga clic en el botón **Siguiente**:    

    ![](single-page-images/vsmac/configure-app.png "Configurar la aplicación de Forms")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y de proyecto establecidos en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crearlo:

    ![](single-page-images/vsmac/configure-project.png "Configurar el proyecto de Forms")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que tanto la solución como el proyecto se denominen **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

5. En el **Panel de solución**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. En **MainPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Este código define la interfaz de usuario de la página de forma declarativa, que consta [`Label`](xref:Xamarin.Forms.Label) de un para mostrar texto [`Editor`](xref:Xamarin.Forms.Editor) , un para la entrada de [`Button`](xref:Xamarin.Forms.Button) texto y dos instancias que dirigen a la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre cómo crear la interfaz de usuario, consulte la [interfaz de usuario](deepdive.md#user-interface) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

7. En el **Panel de solución**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. En **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente código:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al pulsar el botón **Guardar** [`Button`](xref:Xamarin.Forms.Button), se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al pulsar el botón **Eliminar** `Button`, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [responder a la interacción del usuario](deepdive.md#responding-to-user-interaction) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilación del inicio rápido

1. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). El proyecto se compilará y aparecerá un mensaje de operación correcta en la barra de herramientas de Visual Studio para Mac.

      ![](single-page-images/vsmac/build-successful.png "Compilación correcta")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En el **Panel de solución**, seleccione el proyecto **Notes. iOS** , haga clic con el botón derecho y seleccione **establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Establecer iOS como proyecto de inicio")

3. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de iOS Simulator:

      ![](single-page-images/vsmac/start.png "Barra de herramientas de Visual Studio para Mac")

      [![](single-page-images/vsmac/notes-ios.png "Notas en el simulador de iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "Notas en el simulador de iOS")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

4. En el **Panel de solución**, seleccione el proyecto **Notes. Droid** , haga clic con el botón derecho y seleccione **establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Establecer Android como proyecto de inicio")

5. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      [![](single-page-images/vsmac/notes-android.png "Notas en el Android Emulator")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [iniciar la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, aprendió a:

- Cree una aplicación de Xamarin. Forms multiplataforma.
- Defina la interfaz de usuario para una página mediante el lenguaje XAML.
- Interactúe con los elementos de la interfaz de usuario XAML desde el código.

Para convertir esta aplicación de una sola página en una aplicación de varias páginas, continúe con la siguiente guía de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](multi-page.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Análisis detallado de Xamarin. Forms](deepdive.md)
