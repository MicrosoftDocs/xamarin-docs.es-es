---
title: 'title: "Creación de una aplicación de Xamarin.Forms de una página" description: "En este artículo se explica cómo crear una aplicación de Xamarin.Forms multiplataforma de una página que permite escribir una nota y conservarla en el almacenamiento del dispositivo."'
description: 'zone_pivot_groups: platform-dev16 ms.topic: quickstart ms.prod: xamarin ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 04/01/2019 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b9a3017fc8188d3669b64d95c968b2d0a5325358
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84136076"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Creación de una aplicación de Xamarin.Forms de una página

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

En este inicio rápido aprenderá a:

- Crear una aplicación de Xamarin.Forms multiplataforma.
- Definir la interfaz de usuario para una página mediante el lenguaje XAML.
- Interactuar con los elementos de la interfaz de usuario XAML desde el código.

El inicio rápido le guía sobre cómo crear una aplicación de Xamarin.Forms multiplataforma que permite escribir una nota y conservarla en el almacenamiento del dispositivo. A continuación se muestra la aplicación final:

[![](single-page-images/screenshots-sml.png "Notes Application")](single-page-images/screenshots.png#lightbox "Notes Application")

::: zone pivot="windows"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2019 (última versión), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada.
- Conocimientos de C#.
- (opcional) Un equipo Mac emparejado para compilar la aplicación en iOS.

Para más información sobre estos requisitos previos, vea [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Introducción a Visual Studio 2019

1. Inicie Visual Studio 2019 y, en la ventana de inicio, haga clic en **Crear un proyecto nuevo** para crear un proyecto:

    ![](single-page-images/vs/new-solution-2019.png "New Project")

2. En la ventana **Crear un proyecto nuevo**, seleccione **Móvil** en la lista desplegable **Tipo de proyecto**, elija la plantilla **Aplicación móvil (Xamarin.Forms)** y haga clic en el botón **Siguiente**:

    ![](single-page-images/vs/new-project-2019.png "Cross-Platform Project Templates")

3. En el cuadro de diálogo **Configure su nuevo proyecto**, establezca **Nombre del proyecto** en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear**:

    ![](single-page-images/vs/configure-project.png "Configure your Project")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

4. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **En blanco** y después en el botón **Aceptar**:

    ![](single-page-images/vs/new-app-2019.png "New Cross-Platform App")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

5. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml-2019.png "Open MainPage.xaml")

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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Label`](xref:Xamarin.Forms.Label) para mostrar texto, una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, vea [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

7. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind-2019.png "Open MainPage.xaml.cs")

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

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al presionar el control [`Button`](xref:Xamarin.Forms.Button) **Guardar**, se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al presionar el control `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Build Succeeded")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Visual Studio Android Toolbar")

    [![](single-page-images/vs/notes-android.png "Notes in the Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Set iOS as Startup Project")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Visual Studio iOS Toolbar")

    [![](single-page-images/vs/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vs/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2017, con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada.
- Conocimientos de C#.
- (opcional) Un equipo Mac emparejado para compilar la aplicación en iOS.

Para más información sobre estos requisitos previos, vea [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Introducción a Visual Studio 2017

1. Para crear un proyecto, inicie Visual Studio 2017 y, en la página de inicio, haga clic en **Crear nuevo proyecto...** :

    ![](single-page-images/vs/new-solution.png "New Project")

2. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Multiplataforma**, seleccione la plantilla **Aplicación móvil (Xamarin.Forms)** , establezca el nombre en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-project.png "Cross-Platform Project Templates")

    > [!IMPORTANT]
    > Los fragmentos de XAML y C# en este inicio rápido requieren que la solución se denomine **Notes**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este inicio rápido en la solución.

3. En el cuadro de diálogo **New Cross Platform App** (Nueva aplicación multiplataforma), haga clic en **Aplicación en blanco**, seleccione **.NET Standard** como estrategia de código de uso compartido y haga clic en el botón **Aceptar**:

    ![](single-page-images/vs/new-app.png "New Cross-Platform App")

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **Explorador de soluciones**, en el proyecto **Notes**, haga doble clic en **MainPage.xaml** para abrirlo:

    ![](single-page-images/vs/open-mainpage-xaml.png "Open MainPage.xaml")

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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Label`](xref:Xamarin.Forms.Label) para mostrar texto, una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, vea [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml** presionando **CTRL+S** y cierre el archivo.

6. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Open MainPage.xaml.cs")

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

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al presionar el control [`Button`](xref:Xamarin.Forms.Button) **Guardar**, se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al presionar el control `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** presionando **CTRL+S** y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilar el tutorial rápido

1. En Visual Studio, seleccione el elemento de menú **Compilación > Compilar solución** (o presione F6). La solución se compilará y aparecerá un mensaje de operación correcta en la barra de estado de Visual Studio:

      ![](single-page-images/vs/build-succeeded.png "Build Succeeded")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

    ![](single-page-images/vs/android-start.png "Visual Studio Android Toolbar")

    [![](single-page-images/vs/notes-android.png "Notes in the Android Emulator")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Los pasos siguientes solo deben realizarse si se tiene un equipo [Mac emparejado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que cumpla los requisitos del sistema para el desarrollo de Xamarin.Forms.

3. En la barra de herramientas de Visual Studio, haga clic con el botón derecho en el proyecto **Notes.iOS** y seleccione **Establecer como proyecto de inicio**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Set iOS as Startup Project")

4. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular que parece un botón de reproducción) para iniciar la aplicación en la instancia elegida de [iOS Simulator](~/tools/ios-simulator/index.md):

    ![](single-page-images/vs/ios-start.png "Visual Studio iOS Toolbar")

    [![](single-page-images/vs/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vs/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio para Mac (última versión) con compatibilidad con la plataforma iOS y Android.
- Xcode (última versión).
- Conocimientos de C#.

Para más información sobre estos requisitos previos, vea [Instalación de Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Introducción a Visual Studio para Mac

1. Para crear un proyecto, inicie Visual Studio para Mac y, en la ventana de inicio, haga clic en **Nuevo**:

    ![](single-page-images/vsmac/new-project.png "New Solution")

2. En el cuadro de diálogo **Elija una plantilla para el nuevo proyecto**, haga clic en **Multiplataforma > Aplicación**, seleccione la plantilla **Aplicación de Forms en blanco** y haga clic en el botón **Siguiente**:

    ![](single-page-images/vsmac/choose-template.png "Choose a Template")

3. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, asigne a la nueva aplicación el nombre **Notes**, asegúrese de que el botón de radio **Usar .NET Standard** esté seleccionado y haga clic en el botón **Siguiente**:    

    ![](single-page-images/vsmac/configure-app.png "Configure the Forms Application")

4. En el cuadro de diálogo **Configurar su nueva aplicación de Forms en blanco**, deje los nombres de solución y de proyecto establecidos en **Notes**, elija una ubicación adecuada para el proyecto y haga clic en el botón **Crear** para crearlo:

    ![](single-page-images/vsmac/configure-project.png "Configure the Forms Project")

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

    Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Label`](xref:Xamarin.Forms.Label) para mostrar texto, una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Label`, `Editor` y `Grid` se disponen verticalmente en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Para obtener más información sobre la creación de la interfaz de usuario, vea [Interfaz de usuario](deepdive.md#user-interface) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

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

    Este código define un campo `_fileName`, que hace referencia a un archivo denominado `notes.txt` que almacenará los datos de la nota en la carpeta de datos de la aplicación local para la aplicación. Cuando se ejecuta el constructor de páginas, el archivo se lee, si existe, y se muestra en el [`Editor`](xref:Xamarin.Forms.Editor). Al presionar el control [`Button`](xref:Xamarin.Forms.Button) **Guardar**, se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en el archivo. Al presionar el control `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y quita cualquier texto de `Editor`. Para obtener más información sobre la interacción del usuario, vea [Respuesta a la interacción del usuario](deepdive.md#responding-to-user-interaction) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **MainPage.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

### <a name="building-the-quickstart"></a>Compilación del inicio rápido

1. En Visual Studio para Mac, seleccione el elemento de menú **Compilación > Compilar todo** (o pulse **&#8984; + B**). El proyecto se compilará y aparecerá un mensaje de operación correcta en la barra de herramientas de Visual Studio para Mac.

      ![](single-page-images/vsmac/build-successful.png "Build Successful")

    Si hay errores, repita los pasos anteriores y corrija los errores hasta que la solución se compile correctamente.

2. En el **Panel de solución**, seleccione el proyecto **Notes.iOS**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Set iOS as Startup Project")

3. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de iOS Simulator:

      ![](single-page-images/vsmac/start.png "Visual Studio for Mac Toolbar")

      [![](single-page-images/vsmac/notes-ios.png "Notes in the iOS Simulator")](single-page-images/vsmac/notes-ios-large.png#lightbox "Notes in the iOS Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

4. En el **Panel de solución**, seleccione el proyecto **Notes.Droid**, haga clic con el botón derecho y seleccione **Establecer como proyecto de inicio**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Set Android as Startup Project")

5. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en la instancia elegida de Android Emulator:

      [![](single-page-images/vsmac/notes-android.png "Notes in the Android Emulator")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Escriba una nota y pulse el botón **Guardar**.

    Para obtener más información sobre cómo se inicia la aplicación en cada plataforma, vea [Inicio de la aplicación en cada plataforma](deepdive.md#launching-the-application-on-each-platform) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Crear una aplicación de Xamarin.Forms multiplataforma.
- Definir la interfaz de usuario para una página mediante el lenguaje XAML.
- Interactuar con los elementos de la interfaz de usuario XAML desde el código.

Para convertir esta aplicación de página única en una de varias páginas, continúe con el siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](multi-page.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
