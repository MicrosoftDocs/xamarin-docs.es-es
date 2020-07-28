---
title: Navegación en una aplicación Xamarin.Forms de varias páginas
description: En este artículo se explica cómo convertir una aplicación de página única, capaz de almacenar una sola nota, en una aplicación de varias páginas, capaz de almacenar varias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8858a66d2caeea815bf06d5d2741bfc3623598f5
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933736"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Navegación en una aplicación Xamarin.Forms de varias páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

En este inicio rápido aprenderá a:

- Agregar páginas adicionales a una solución Xamarin.Forms.
- Realizar la navegación entre las páginas.
- Usar el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

En este inicio rápido se describe cómo convertir una aplicación Xamarin.Forms multiplataforma de una página, capaz de almacenar una sola nota, en una aplicación de varias páginas, capaz de almacenar varias notas. A continuación se muestra la aplicación final:

[![Página Notas](multi-page-images/screenshots1-sml.png)](multi-page-images/screenshots1.png#lightbox "Página Notas")
[![Página de entrada de nota](multi-page-images/screenshots2-sml.png)](multi-page-images/screenshots2.png#lightbox "Página de entrada de nota")

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](single-page.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio. En la ventana de inicio, haga clic en la solución **Notes** en la lista de proyectos o soluciones recientes, o bien haga clic en **Abrir un proyecto o una solución** y, en el cuadro de diálogo **Abrir proyecto o solución**, seleccione el archivo de solución del proyecto Notes:

    ![Abrir proyecto](multi-page-images/vs/open-solution.png)

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Notes** y seleccione **Agregar > Nueva carpeta**:

    ![Agregar nuevo elemento](multi-page-images/vs/add-new-item.png)

3. En el **Explorador de soluciones**, asigne el nombre **Modelos** a la nueva carpeta:

    ![Carpeta Modelos](multi-page-images/vs/name-folder.png)

4. En el **Explorador de soluciones**, seleccione la carpeta **Modelos**, haga clic con el botón derecho y seleccione **Agregar > Nuevo elemento...** :

    ![Agregar nuevo archivo](multi-page-images/vs/add-new-models-file.png)

5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Clase**, asigne el nombre **Note** al nuevo archivo y haga clic en el botón **Agregar**:

    ![Agregar clase Note](multi-page-images/vs/add-note-class.png)

    Esto agregará una clase denominada **Note** a la carpeta **Modelos** del proyecto **Notes**.

6. En **Note.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un modelo `Note` que almacenará los datos sobre cada nota en la aplicación.    

    Presione **CTRL+S** para guardar los cambios en **Note.cs** y cierre el archivo.

7. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Notes** y seleccione **Agregar > Nuevo elemento...** . En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página de contenido**, asigne el nombre **NoteEntryPage** al nuevo archivo y haga clic en el botón **Agregar**:

    ![Adición de ContentPage de Xamarin.Forms](multi-page-images/vs/add-note-entry-page.png)

    Esto agregará una página nueva denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda de la aplicación.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, `Editor` usa el enlace de datos para enlazar con la propiedad `Text` del modelo `Note`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml** y cierre el archivo.

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Este código almacena una instancia de `Note`, que representa una única nota, en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se presiona el objeto [`Button`](xref:Xamarin.Forms.Button) **Guardar** se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en un archivo nuevo con un nombre de archivo generado de forma aleatoria, o bien en un archivo existente si se va a actualizar una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local. Después, el método regresa a la página anterior. Al pulsar el objeto `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y regresa a la página anterior. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml.cs** y cierre el archivo.

      > [!WARNING]
      > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

10. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Notes** y seleccione **Agregar > Nuevo elemento...** . En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página de contenido**, asigne el nombre **NotesPage** al nuevo archivo y haga clic en el botón **Agregar**.

      Esto agregará una página nueva denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en los objetos [`ListView`](xref:Xamarin.Forms.ListView) y [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). En `ListView` se usa el enlace de datos para mostrar las notas recuperadas por la aplicación y, al seleccionar una nota, se navegará a `NoteEntryPage` donde la nota se puede modificar. Como alternativa, se puede presionar el objeto `ToolbarItem` para crear una nota. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml** y cierre el archivo.

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se ejecuta el método `OnAppearing`, que rellena el objeto [`ListView`](xref:Xamarin.Forms.ListView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), se ejecuta el controlador de eventos `OnNoteAddedClicked`. Este método navega hasta `NoteEntryPage`, y establece el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en una instancia nueva de `Note`. Cuando se selecciona un elemento de `ListView`, se ejecuta el controlador de eventos `OnListViewItemSelected`. Este método navega hasta `NoteEntryPage`, y establece el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en la instancia de `Note` seleccionada. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml.cs** y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

13. En el **Explorador de soluciones**, haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Este código agrega una declaración de espacio de nombres para el espacio de nombres `System.IO` y una declaración para una propiedad `FolderPath` estática de tipo `string`. La propiedad `FolderPath` se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código inicializa la propiedad `FolderPath` en el constructor `App` e inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) para que sea un elemento [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospede una instancia de `NotesPage`. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

14. En el **Explorador de soluciones**, en el proyecto **Notes**, haga clic con el botón derecho en **MainPage.xaml** y seleccione **Eliminar**. En el cuadro de diálogo que aparece, presione el botón **Aceptar** para quitar el archivo del disco duro.

    Esto quita una página que ya no se usa.

15. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac. En la ventana de inicio, haga clic en **Abrir** y, en el cuadro de diálogo, seleccione el archivo de solución para el proyecto Notes:

    ![Abrir solución](multi-page-images/vsmac/open-solution.png)

2. En el **Panel de solución**, seleccione el proyecto **Notes**, haga clic con el botón derecho y seleccione **Agregar > Nueva carpeta**:

    ![Agregar nueva carpeta](multi-page-images/vsmac/add-new-folder.png)

3. En el **Panel de solución**, asigne el nombre **Modelos** a la nueva carpeta:

    ![Carpeta Modelos](multi-page-images/vsmac/name-folder.png)

4. En el **Panel de solución**, seleccione la carpeta **Modelos**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo...** :

    ![Agregar nuevo archivo](multi-page-images/vsmac/add-new-models-file.png)

5. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne el nombre **Note** al nuevo archivo y haga clic en el botón **Nuevo**:

    ![Agregar clase Note](multi-page-images/vsmac/add-note-class.png)

    Esto agregará una clase denominada **Note** a la carpeta **Modelos** del proyecto **Notes**.

6. En **Note.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un modelo `Note` que almacenará los datos sobre cada nota en la aplicación.

    Para guardar los cambios en **Note.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

7. En el **Panel de solución**, seleccione el proyecto **Notes**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo**. En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > XAML ContentPage de Forms**, asigne el nombre **NoteEntryPage** al nuevo archivo, y haga clic en el botón **Nuevo**:

    ![Adición de ContentPage de Xamarin.Forms](multi-page-images/vsmac/add-note-entry-page.png)

    Esto agregará una página nueva denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda de la aplicación.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos instancias de [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, `Editor` usa el enlace de datos para enlazar con la propiedad `Text` del modelo `Note`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Para guardar los cambios en **NoteEntryPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Este código almacena una instancia de `Note`, que representa una única nota, en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se presiona el objeto [`Button`](xref:Xamarin.Forms.Button) **Guardar** se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en un archivo nuevo con un nombre de archivo generado de forma aleatoria, o bien en un archivo existente si se va a actualizar una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local. Después, el método regresa a la página anterior. Al pulsar el objeto `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y regresa a la página anterior. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Para guardar los cambios en **NoteEntryPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

      > [!WARNING]
      > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

10. En el **Panel de solución**, seleccione el proyecto **Notes**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo**. En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > XAML ContentPage de Forms**, asigne el nombre **NotesPage** al nuevo archivo y haga clic en el botón **Nuevo**.

      Esto agregará una página nueva denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en los objetos [`ListView`](xref:Xamarin.Forms.ListView) y [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). En `ListView` se usa el enlace de datos para mostrar las notas recuperadas por la aplicación y, al seleccionar una nota, se navegará a `NoteEntryPage` donde la nota se puede modificar. Como alternativa, se puede presionar el objeto `ToolbarItem` para crear una nota. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

12. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se ejecuta el método `OnAppearing`, que rellena el objeto [`ListView`](xref:Xamarin.Forms.ListView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), se ejecuta el controlador de eventos `OnNoteAddedClicked`. Este método navega hasta `NoteEntryPage`, y establece el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en una instancia nueva de `Note`. Cuando se selecciona un elemento de `ListView`, se ejecuta el controlador de eventos `OnListViewItemSelected`. Este método navega hasta `NoteEntryPage`, y establece el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en la instancia de `Note` seleccionada. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

13. En el **Panel de solución**, haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Este código agrega una declaración de espacio de nombres para el espacio de nombres `System.IO` y una declaración para una propiedad `FolderPath` estática de tipo `string`. La propiedad `FolderPath` se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código inicializa la propiedad `FolderPath` en el constructor `App` e inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) para que sea un elemento [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospede una instancia de `NotesPage`. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

14. En el **Panel de solución**, en el proyecto **Notes**, haga clic con el botón derecho en **MainPage.xaml** y seleccione **Quitar**. En el cuadro de diálogo que aparece, presione el botón **Eliminar** para quitar el archivo del disco duro.

    Esto quita una página que ya no se usa.

15. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Agregar páginas adicionales a una solución Xamarin.Forms.
- Realizar la navegación entre las páginas.
- Usar el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

Para modificar la aplicación de modo que almacene sus datos en una base de datos de SQLite.NET local, continúe con el inicio rápido siguiente.

> [!div class="nextstepaction"]
> [Siguiente](database.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
