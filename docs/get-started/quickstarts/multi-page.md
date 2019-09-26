---
title: Navegación en una aplicación de varias páginas de Xamarin.Forms
description: En este artículo se explica cómo activar la aplicación de una sola página, capaz de almacenar una sola nota, en una aplicación de varias páginas, capaz de almacenar varias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68653796"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Realizar la navegación en una aplicación de Xamarin. Forms de varias páginas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

En esta guía de inicio rápido, obtendrá información sobre cómo:

- Agregue páginas adicionales a una solución de Xamarin. Forms.
- Realizar la navegación entre las páginas.
- Utilice el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

En la guía de inicio rápido se explica cómo convertir una aplicación de Xamarin. Forms multiplataforma de una sola página, capaz de almacenar una sola nota en una aplicación de varias páginas, capaz de almacenar varias notas. A continuación se muestra la aplicación final:

[![](multi-page-images/screenshots1-sml.png "")](multi-page-images/screenshots1.png#lightbox "Página notas")
[![](multi-page-images/screenshots2-sml.png "Página de entrada de nota") de página de notas](multi-page-images/screenshots2.png#lightbox "Página de entrada de nota")

### <a name="prerequisites"></a>Requisitos previos

Antes de intentar esta guía de inicio rápido, debe completar correctamente el [Inicio rápido anterior](single-page.md) . También puede descargar el [ejemplo de inicio rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) y usarlo como punto de partida para esta guía de inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio. En la ventana Inicio, haga clic en la solución **notas** en la lista proyectos o soluciones recientes, o haga clic en **abrir un proyecto o solución**y, en el cuadro de diálogo **Abrir proyecto o solución** , seleccione el archivo de solución para el proyecto de notas:

    ![](multi-page-images/vs/open-solution.png "Abrir proyecto")

2. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **notas** y seleccione **Agregar > nueva carpeta**:

    ![](multi-page-images/vs/add-new-item.png "Agregar nuevo elemento")

3. En **Explorador de soluciones**, asigne a la nueva carpeta el nombre **modelos**:

    ![](multi-page-images/vs/name-folder.png "Carpeta modelos")

4. En **Explorador de soluciones**, seleccione la carpeta **modelos** , haga clic con el botón derecho y seleccione **Agregar > nuevo elemento.** ..:

    ![](multi-page-images/vs/add-new-models-file.png "Agregar nuevo archivo")

5. En el cuadro de diálogo **Agregar nuevo elemento** , seleccione  **C# elementos visuales > clase**, asigne un nombre a la **Nota**del nuevo archivo y haga clic en el botón **Agregar** :

    ![](multi-page-images/vs/add-note-class.png "Agregar clase de nota")

    Esto agregará una clase denominada **Note** a la carpeta **Models** del proyecto **Notes** .

6. En **Note.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Esta clase define un `Note` modelo que almacenará los datos de cada nota en la aplicación.    

    Guarde los cambios en **Note.CS** presionando **Ctrl + S**y cierre el archivo.

7. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **notas** y seleccione **Agregar > nuevo elemento.** .. En el cuadro de diálogo **Agregar nuevo elemento** , seleccione  **C# elementos visuales > Xamarin. Forms > Página de contenido**, asigne al nuevo archivo el nombre **NoteEntryPage**y haga clic en el botón **Agregar** :

    ![](multi-page-images/vs/add-note-entry-page.png "Adición de Xamarin. Forms ContentPage")

    Esto agregará una nueva página denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda página de la aplicación.

8. En **NoteEntryPage. Xaml**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de [`Editor`](xref:Xamarin.Forms.Editor) un para la entrada de texto [`Button`](xref:Xamarin.Forms.Button) y dos instancias que dirigen a la aplicación para guardar o eliminar un archivo. Las dos `Button` instancias se colocan horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid)un, con `Editor` el `Grid` y dispuestos verticalmente en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, utiliza el `Editor` enlace de datos para enlazar a `Text` la propiedad del `Note` modelo. Para más información sobre el enlace de datos, consulte [enlace de datos](deepdive.md#data-binding) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage. Xaml** presionando **Ctrl + S**y cierre el archivo.

9. En **NoteEntryPage.Xaml.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

      Este código almacena una `Note` instancia de, que representa una sola nota, en [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la de la página. Cuando se [`Button`](xref:Xamarin.Forms.Button) presiona la tecla guardar `OnSaveButtonClicked` , se ejecuta el controlador de eventos, `Editor` que guarda el contenido de en un nuevo archivo con un nombre de archivo generado aleatoriamente o en un archivo existente si se está actualizando una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local para la aplicación. A continuación, el método navega de nuevo a la página anterior. Cuando se presiona la tecla `OnDeleteButtonClicked` **Supr** `Button` , se ejecuta el controlador de eventos, lo que elimina el archivo, siempre que exista, y navega de nuevo a la página anterior. Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.Xaml.CS** presionando **Ctrl + S**y cierre el archivo.

      > [!WARNING]
      > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

10. En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **notas** y seleccione **Agregar > nuevo elemento.** .. En el cuadro de diálogo **Agregar nuevo elemento** , seleccione  **C# elementos visuales > Xamarin. Forms > Página de contenido**, asigne al nuevo archivo el nombre **NotesPage**y haga clic en el botón **Agregar** .

      Esto agregará una página denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage. Xaml**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Este código define la interfaz de usuario de la página de forma declarativa, que consta [`ListView`](xref:Xamarin.Forms.ListView) de un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)y un. Utiliza `ListView` el enlace de datos para mostrar las notas recuperadas por la aplicación y, al seleccionar una nota, se desplazará `NoteEntryPage` al lugar donde se puede modificar la nota. Como alternativa, se puede crear una nueva nota al presionar `ToolbarItem`. Para más información sobre el enlace de datos, consulte [enlace de datos](deepdive.md#data-binding) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage. Xaml** presionando **Ctrl + S**y cierre el archivo.

12. En **NotesPage.Xaml.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se `OnAppearing` ejecuta el método, que rellena el [`ListView`](xref:Xamarin.Forms.ListView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona, se `OnNoteAddedClicked` ejecuta el controlador de eventos. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) `NoteEntryPage`Este método navega a, estableciendo el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en una nueva `Note` instancia de. Cuando se selecciona un elemento `ListView` en el, `OnListViewItemSelected` se ejecuta el controlador de eventos. `NoteEntryPage`Este método navega a, estableciendo el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en la instancia seleccionada `Note` . Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage.Xaml.CS** presionando **Ctrl + S**y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

13. En **Explorador de soluciones**, haga doble clic en **app.Xaml.CS** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

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

    Este código agrega una declaración de espacio de `System.IO` nombres para el espacio de nombres y agrega una `FolderPath` declaración para una `string`propiedad estática de tipo. La `FolderPath` propiedad se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código `FolderPath` inicializa la propiedad en el `App` constructor e inicializa la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propiedad como un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda una instancia de `NotesPage`. Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

14. En **Explorador de soluciones**, en el proyecto **notas** , haga clic con el botón derecho en **mainpage. Xaml**y seleccione **eliminar**. En el cuadro de diálogo que aparece, presione el botón **Aceptar** para quitar el archivo del disco duro.

    Esto quita una página que ya no se usa.

15. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación se devolverá a la **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac. En la ventana Inicio, haga clic en **abrir**y, en el cuadro de diálogo, seleccione el archivo de solución para el proyecto notas:

    ![](multi-page-images/vsmac/open-solution.png "Abrir solución")

2. En el **Panel de solución**, seleccione el proyecto **notas** , haga clic con el botón derecho y seleccione **Agregar > nueva carpeta**:

    ![](multi-page-images/vsmac/add-new-folder.png "Agregar nueva carpeta")

3. En el **Panel de solución**, asigne a la nueva carpeta el nombre **modelos**:

    ![](multi-page-images/vsmac/name-folder.png "Carpeta modelos")

4. En el **Panel de solución**, seleccione la carpeta **modelos** , haga clic con el botón derecho y seleccione **Agregar > nuevo archivo...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Agregar nuevo archivo")

5. En el cuadro de diálogo **nuevo archivo** , seleccione **General > clase vacía**, asigne un nombre a la **Nota**del nuevo archivo y haga clic en el botón **nuevo** :

    ![](multi-page-images/vsmac/add-note-class.png "Agregar clase de nota")

    Esto agregará una clase denominada **Note** a la carpeta **Models** del proyecto **Notes** .

6. En **Note.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Esta clase define un `Note` modelo que almacenará los datos de cada nota en la aplicación.

    Guarde los cambios en **Note.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

7. En el **Panel de solución**, seleccione el proyecto **notas** , haga clic con el botón derecho y seleccione **Agregar > nuevo archivo..** .. En el cuadro de diálogo **nuevo archivo** , seleccione **Forms > Forms ContentPage XAML**, asigne al nuevo archivo el nombre **NoteEntryPage**y haga clic en el botón **nuevo** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Adición de Xamarin. Forms ContentPage")

    Esto agregará una nueva página denominada **NoteEntryPage** a la carpeta raíz del proyecto. Esta página será la segunda página de la aplicación.

8. En **NoteEntryPage. Xaml**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

      Este código define mediante declaración la interfaz de usuario para la página, que consta de [`Editor`](xref:Xamarin.Forms.Editor) un para la entrada de texto [`Button`](xref:Xamarin.Forms.Button) y dos instancias que dirigen a la aplicación para guardar o eliminar un archivo. Las dos `Button` instancias se colocan horizontalmente en [`Grid`](xref:Xamarin.Forms.Grid)un, con `Editor` el `Grid` y dispuestos verticalmente en un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Además, utiliza el `Editor` enlace de datos para enlazar a `Text` la propiedad del `Note` modelo. Para más información sobre el enlace de datos, consulte [enlace de datos](deepdive.md#data-binding) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage. Xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

9. En **NoteEntryPage.Xaml.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

      Este código almacena una `Note` instancia de, que representa una sola nota, en [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la de la página. Cuando se [`Button`](xref:Xamarin.Forms.Button) presiona la tecla guardar `OnSaveButtonClicked` , se ejecuta el controlador de eventos, `Editor` que guarda el contenido de en un nuevo archivo con un nombre de archivo generado aleatoriamente o en un archivo existente si se está actualizando una nota. En ambos casos, el archivo se almacena en la carpeta de datos de la aplicación local para la aplicación. A continuación, el método navega de nuevo a la página anterior. Cuando se presiona la tecla `OnDeleteButtonClicked` **Supr** `Button` , se ejecuta el controlador de eventos, lo que elimina el archivo, siempre que exista, y navega de nuevo a la página anterior. Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

      Guarde los cambios en **NoteEntryPage.Xaml.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

      > [!WARNING]
      > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

10. En el **Panel de solución**, seleccione el proyecto **notas** , haga clic con el botón derecho y seleccione **Agregar > nuevo archivo..** .. En el cuadro de diálogo **nuevo archivo** , seleccione **Forms > Forms ContentPage XAML**, asigne al nuevo archivo el nombre **NotesPage**y haga clic en el botón **nuevo** .

      Esto agregará una página denominada **NotesPage** a la carpeta raíz del proyecto. Esta página será la página raíz de la aplicación.

11. En **NotesPage. Xaml**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Este código define la interfaz de usuario de la página de forma declarativa, que consta [`ListView`](xref:Xamarin.Forms.ListView) de un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)y un. Utiliza `ListView` el enlace de datos para mostrar las notas recuperadas por la aplicación y, al seleccionar una nota, se desplazará `NoteEntryPage` al lugar donde se puede modificar la nota. Como alternativa, se puede crear una nueva nota al presionar `ToolbarItem`. Para más información sobre el enlace de datos, consulte [enlace de datos](deepdive.md#data-binding) en la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage. Xaml** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

12. En **NotesPage.Xaml.CS**, quite todo el código de plantilla y reemplácelo por el código siguiente:

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

    Este código define la funcionalidad de `NotesPage`. Cuando aparece la página, se `OnAppearing` ejecuta el método, que rellena el [`ListView`](xref:Xamarin.Forms.ListView) con las notas que se han recuperado de la carpeta de datos de la aplicación local. Cuando se presiona, se `OnNoteAddedClicked` ejecuta el controlador de eventos. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) `NoteEntryPage`Este método navega a, estableciendo el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en una nueva `Note` instancia de. Cuando se selecciona un elemento `ListView` en el, `OnListViewItemSelected` se ejecuta el controlador de eventos. `NoteEntryPage`Este método navega a, estableciendo el [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` en la instancia seleccionada `Note` . Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **NotesPage.Xaml.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

13. En el **Panel de solución**, haga doble clic en **app.Xaml.CS** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

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

    Este código agrega una declaración de espacio de `System.IO` nombres para el espacio de nombres y agrega una `FolderPath` declaración para una `string`propiedad estática de tipo. La `FolderPath` propiedad se usa para almacenar la ruta de acceso en el dispositivo en el que se almacenarán los datos de la nota. Además, el código `FolderPath` inicializa la propiedad en el `App` constructor e inicializa la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propiedad como un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda una instancia de `NotesPage`. Para obtener más información sobre la navegación, vea [exploración](deepdive.md#navigation) en profundidad de la guía de [Inicio rápido de Xamarin. Forms](deepdive.md).

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

14. En el **Panel de solución**, en el proyecto **notas** , haga clic con el botón derecho en **mainpage. Xaml**y seleccione **quitar**. En el cuadro de diálogo que aparece, presione el botón **eliminar** para quitar el archivo del disco duro.

    Esto quita una página que ya no se usa.

15. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación se devolverá a la **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, aprendió a:

- Agregue páginas adicionales a una solución de Xamarin. Forms.
- Realizar la navegación entre las páginas.
- Utilice el enlace de datos para sincronizar datos entre los elementos de la interfaz de usuario y su origen de datos.

Para modificar la aplicación de modo que almacene sus datos en una base de datos de SQLite.NET local, continúe con el inicio rápido siguiente.

> [!div class="nextstepaction"]
> [Siguiente](database.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Análisis detallado de Xamarin. Forms](deepdive.md)
