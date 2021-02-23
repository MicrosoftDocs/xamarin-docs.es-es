---
title: Navegación en una aplicación de Xamarin.Forms
description: En este artículo se explica cómo convertir una aplicación de Xamarin.Forms Shell, capaz de almacenar una sola nota, en una aplicación capaz de almacenar varias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 030204F7-E9E4-4AE3-B9F7-915B697D0171
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c218c7108ec70ab1ccd1b048e1655dba02c48660
ms.sourcegitcommit: 66ca2680c8fde18a55ce5daa3818edeeb9ba219b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100569714"
---
# <a name="perform-navigation-in-a-xamarinforms-application"></a>Navegación en una aplicación de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)

En este inicio rápido aprenderá a:

- Agregar páginas adicionales a una aplicación de Xamarin.Forms Shell.
- Realizar la navegación entre las páginas.
- Usar el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

En este inicio rápido se describe cómo convertir una aplicación de Xamarin.Forms Shell multiplataforma, capaz de almacenar una sola nota, en una aplicación capaz de almacenar varias notas. A continuación se muestra la aplicación final:

[![Página Notas](navigation-images/screenshots1-sml.png)](navigation-images/screenshots1.png#lightbox "Página Notas")
[![Página de entrada de nota](navigation-images/screenshots2-sml.png)](navigation-images/screenshots2.png#lightbox "Página de entrada de nota")

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](app.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio. En la ventana de inicio, haga clic en la solución **Notes** en la lista de proyectos o soluciones recientes, o bien haga clic en **Abrir un proyecto o una solución** y, en el cuadro de diálogo **Abrir proyecto o solución**, seleccione el archivo de solución del proyecto Notes:

    ![Abrir solución](navigation-images/vs/open-solution.png)

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **Notes** y seleccione **Agregar > Nueva carpeta**:

    ![Agregar nueva carpeta](navigation-images/vs/add-new-folder.png)

3. En el **Explorador de soluciones**, asigne el nombre **Modelos** a la nueva carpeta:

    ![Carpeta Modelos](navigation-images/vs/name-folder.png)

4. En el **Explorador de soluciones**, seleccione la carpeta **Modelos**, haga clic con el botón derecho y seleccione **Agregar > Clase…** :

    ![Agregar nuevo archivo](navigation-images/vs/add-new-models-file.png)

5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Clase**, asigne el nombre **Note** al nuevo archivo y haga clic en el botón **Agregar**:

    ![Agregar clase Note](navigation-images/vs/add-note-class.png)

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

    Presione **CTRL+S** para guardar los cambios en **Notes.cs**.  

7. En el **Explorador de soluciones**, en el proyecto **Notes**, seleccione la carpeta **Vistas**, haga clic con el botón derecho y seleccione **Agregar > Nuevo elemento…** . En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Elementos de Visual C# > Xamarin.Forms > Página de contenido**, asigne el nombre **NoteEntryPage** al nuevo archivo y haga clic en el botón **Agregar**:

    ![Add Xamarin.Forms ContentPage](navigation-images/vs/add-note-entry-page.png)

    Esto agregará una página nueva denominada **NoteEntryPage** a la carpeta **Vistas** del proyecto. Esta página se utilizará para la entrada de notas.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos objetos [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, `Editor` usa el enlace de datos para enlazar con la propiedad `Text` del modelo `Note`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml**.

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
                      BindingContext = note;
                  }
                  catch (Exception)
                  {
                      Console.WriteLine("Failed to load note.");
                  }
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      Este código almacena una instancia de `Note`, que representa una única nota, en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. La clase se decora con un atributo `QueryPropertyAttribute` que permite pasar datos a la página, durante la navegación, a través de parámetros de consulta. El primer argumento de `QueryPropertyAttribute` especifica el nombre de la propiedad que recibirá los datos, mientras que el segundo argumento especifica el identificador del parámetro de consulta. Por lo tanto, el elemento `QueryParameterAttribute` del código anterior especifica que la propiedad `ItemId` recibirá los datos pasados al parámetro de consulta `ItemId` del URI especificado en una llamada al método `GoToAsync`. Luego, la propiedad `ItemId` llama al método `LoadNote` para crear un objeto `Note` a partir del archivo en el dispositivo y establece el `BindingContext` de la página en el objeto `Note`.

      Cuando se presiona el objeto [`Button`](xref:Xamarin.Forms.Button) **Guardar** se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en un archivo nuevo con un nombre de archivo generado de forma aleatoria, o bien en un archivo existente si se va a actualizar una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local. Después, el método regresa a la página anterior. Al pulsar el objeto `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y regresa a la página anterior. Para información sobre la navegación, consulte [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml.cs**.

      > [!WARNING]
      > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

10. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **NotesPage.xaml** en la carpeta **Vistas**.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en los objetos [`CollectionView`](xref:Xamarin.Forms.CollectionView) y [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). `CollectionView` usa el enlace de datos para mostrar las notas que recupera la aplicación. Al seleccionar una nota, navegará hasta el `NoteEntryPage` donde se puede modificar la nota. Como alternativa, se puede presionar el objeto `ToolbarItem` para crear una nota. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml**.

12. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **NotesPage.xaml** en la carpeta **Vistas** y abra **NotesPage.xaml.cs**.

13. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se ejecuta el método `OnAppearing`, que rellena el objeto [`CollectionView`](xref:Xamarin.Forms.CollectionView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), se ejecuta el controlador de eventos `OnAddClicked`. Con este método se navega al `NoteEntryPage`. Cuando se selecciona un elemento de `CollectionView`, se ejecuta el controlador de eventos `OnSelectionChanged`. Con este método se navega al `NoteEntryPage`, siempre que se seleccione un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView), se pase a la propiedad `Filename` del `Note` seleccionado como un parámetro de consulta a la página. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml.cs**.

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

14. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **AppShell.xaml** y abra **AppShell.xaml.cs**. Después, reemplace el código existente con el siguiente:

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    En este código se registra una ruta para `NoteEntryPage`, que no se representa en la jerarquía visual de Shell (**AppShell.xaml**). Luego, es posible navegar a esta página a través de la navegación basada en el URI, con el método `GoToAsync`.

    Presione **CTRL+S** para guardar los cambios en **AppShell.xaml.cs**.

15. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **App.xaml** y abra **App.xaml.cs**. Después, reemplace el código existente con el siguiente:

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

    Este código agrega una declaración de espacio de nombres para el espacio de nombres `System.IO` y una declaración para una propiedad `FolderPath` estática de tipo `string`. La propiedad `FolderPath` se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código inicializa la propiedad `FolderPath` en el constructor `App` e inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en el objeto `Shell` en subclase.

    Presione **CTRL+S** para guardar los cambios en **App.xaml.cs**.

16. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    En **NotesPage** presione el botón **Agregar** para navegar hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba varias notas, de longitud variable, para observar el comportamiento de la aplicación. Cierre la aplicación y vuelva a iniciarla para asegurarse de que las notas que escribió se guardaron en el dispositivo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac. En la ventana de inicio, haga clic en **Abrir** y, en el cuadro de diálogo, seleccione el archivo de solución para el proyecto Notes:

    ![Abrir solución](navigation-images/vsmac/open-solution.png)

2. En el **Panel de solución**, haga clic con el botón derecho en el proyecto **Notes** y seleccione **Agregar > Nueva carpeta**:

    ![Agregar nueva carpeta](navigation-images/vsmac/add-new-folder.png)

3. En el cuadro de diálogo **Nueva carpeta**, asigne el nombre **Modelos** a la carpeta nueva:

    ![Carpeta Modelos](navigation-images/vsmac/name-folder.png)

4. En el **Panel de solución**, seleccione la carpeta **Modelos**, haga clic con el botón derecho y seleccione **Agregar > Nueva clase...** :

    ![Agregar nuevo archivo](navigation-images/vsmac/add-new-models-file.png)

5. En el cuadro de diálogo **Nuevo archivo**, seleccione **General > Clase vacía**, asigne el nombre **Note** al nuevo archivo y haga clic en el botón **Nuevo**:

    ![Agregar clase Note](navigation-images/vsmac/add-note-class.png)

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

    Para guardar los cambios en **Note.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

7. En el **Panel de solución**, seleccione el proyecto **Notes**, haga clic con el botón derecho y seleccione **Agregar > Nuevo archivo**. En el cuadro de diálogo **Nuevo archivo**, seleccione **Formularios > XAML ContentPage de Forms**, asigne el nombre **NoteEntryPage** al nuevo archivo, y haga clic en el botón **Nuevo**:

    ![Add Xamarin.Forms ContentPage](navigation-images/vsmac/add-note-entry-page.png)

    Esto agregará una página nueva denominada **NoteEntryPage** a la carpeta **Vistas** del proyecto. Esta página se utilizará para la entrada de notas.

8. En **NoteEntryPage.xaml**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de una instancia de [`Editor`](xref:Xamarin.Forms.Editor) para la entrada de texto y dos objetos [`Button`](xref:Xamarin.Forms.Button) que dirigen la aplicación para guardar o eliminar un archivo. Las dos instancias de `Button` se disponen horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid), mientras que `Editor` y `Grid` se disponen en vertical en [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, `Editor` usa el enlace de datos para enlazar con la propiedad `Text` del modelo `Note`. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

      Para guardar los cambios en **NoteEntryPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

9. En **NoteEntryPage.xaml.cs**, quite todo el código de plantilla y reemplácelo por el siguiente:

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
                      BindingContext = note;
                  }
                  catch (Exception)
                  {
                      Console.WriteLine("Failed to load note.");
                  }
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      Este código almacena una instancia de `Note`, que representa una única nota, en el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. La clase se decora con un atributo `QueryPropertyAttribute` que permite pasar datos a la página, durante la navegación, a través de parámetros de consulta. El primer argumento de `QueryPropertyAttribute` especifica el nombre de la propiedad que recibirá los datos, mientras que el segundo argumento especifica el identificador del parámetro de consulta. Por lo tanto, el elemento `QueryParameterAttribute` del código anterior especifica que la propiedad `ItemId` recibirá los datos pasados al parámetro de consulta `ItemId` del URI especificado en una llamada al método `GoToAsync`. Luego, la propiedad `ItemId` llama al método `LoadNote` para crear un objeto `Note` a partir del archivo en el dispositivo y establece el `BindingContext` de la página en el objeto `Note`.

      Cuando se presiona el objeto [`Button`](xref:Xamarin.Forms.Button) **Guardar** se ejecuta el controlador de eventos `OnSaveButtonClicked`, que guarda el contenido de `Editor` en un archivo nuevo con un nombre de archivo generado de forma aleatoria, o bien en un archivo existente si se va a actualizar una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local. Después, el método regresa a la página anterior. Al pulsar el objeto `Button` **Eliminar**, se ejecuta el controlador de eventos `OnDeleteButtonClicked`, que elimina el archivo, siempre que exista, y regresa a la página anterior. Para información sobre la navegación, consulte [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md).

      Para guardar los cambios en **NoteEntryPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

      > [!WARNING]
      > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

10. En el **Panel de solución**, en el proyecto **Notes**, abra **NotesPage.xaml** en la carpeta **Vistas**.

11. En **NotesPage.xaml**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Este código define mediante declaración la interfaz de usuario de la página, que consiste en los objetos [`CollectionView`](xref:Xamarin.Forms.CollectionView) y [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). `CollectionView` usa el enlace de datos para mostrar las notas que recupera la aplicación. Al seleccionar una nota, navegará hasta el `NoteEntryPage` donde se puede modificar la nota. Como alternativa, se puede presionar el objeto `ToolbarItem` para crear una nota. Para obtener más información sobre el enlace de datos, vea [Enlace de datos](deepdive.md#data-binding) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

12. En el **Panel de solución**, en el proyecto **Notes**, expanda **NotesPage.xaml** en la carpeta **Vistas** y abra **NotesPage.xaml.cs**.

13. En **NotesPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el siguiente:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se ejecuta el método `OnAppearing`, que rellena el objeto [`CollectionView`](xref:Xamarin.Forms.CollectionView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), se ejecuta el controlador de eventos `OnAddClicked`. Con este método se navega al `NoteEntryPage`. Cuando se selecciona un elemento de `CollectionView`, se ejecuta el controlador de eventos `OnSelectionChanged`. Con este método se navega al `NoteEntryPage`, siempre que se seleccione un elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView), se pase a la propiedad `Filename` del `Note` seleccionado como un parámetro de consulta a la página. Para obtener más información sobre la navegación, vea [Navegación](deepdive.md#navigation) en [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md).

    Para guardar los cambios en **NotesPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

14. En el **Panel de solución**, en el proyecto **Notes**, expanda **AppShell.xaml** y abra **AppShell.xaml.cs**. Después, reemplace el código existente con el siguiente:

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    En este código se registra una ruta para `NoteEntryPage`, que no se representa en la jerarquía visual de Shell. Luego, es posible navegar a esta página a través de la navegación basada en el URI, con el método `GoToAsync`.

    Para guardar los cambios en **AppShell.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

15. En el **Panel de solución**, en el proyecto **Notes**, expanda **App.xaml** y abra **App.xaml.cs**. Después, reemplace el código existente con el siguiente:

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

    Este código agrega una declaración de espacio de nombres para el espacio de nombres `System.IO` y una declaración para una propiedad `FolderPath` estática de tipo `string`. La propiedad `FolderPath` se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código inicializa la propiedad `FolderPath` en el constructor `App` e inicializa la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en el objeto `Shell` en subclase.

    Para guardar los cambios en **App.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

16. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    En **NotesPage** presione el botón **Agregar** para navegar hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba varias notas, de longitud variable, para observar el comportamiento de la aplicación. Cierre la aplicación y vuelva a iniciarla para asegurarse de que las notas que escribió se guardaron en el dispositivo.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Agregar páginas adicionales a una aplicación de Xamarin.Forms Shell.
- Realizar la navegación entre las páginas.
- Usar el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

Continúe en el inicio rápido siguiente para modificar la aplicación de modo que almacene sus datos en una base de datos SQLite.NET local.

> [!div class="nextstepaction"]
> [Siguiente](database.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)
- [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md)
