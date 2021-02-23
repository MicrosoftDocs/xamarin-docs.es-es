---
title: Almacenamiento de datos en una base de datos local de SQLite.NET
description: En este artículo se explica cómo almacenar datos en una base de datos de SQLite.NET local, desde una aplicación de Xamarin.Forms Shell.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: F669CE4E-1E1B-409F-BC1C-8364633EB7EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cfc479e6211692f3de7d78e2fd52cd80a2630660
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818409"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Almacenamiento de datos en una base de datos local de SQLite.NET

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

En este inicio rápido aprenderá a:

- Almacenar datos de forma local en una base de datos de SQLite.NET.

El inicio rápido lo guía durante el proceso de almacenamiento de datos en una base de datos de SQLite.NET local desde una aplicación de Xamarin.Forms Shell. A continuación se muestra la aplicación final:

[![Página Notas](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox)
[![Página de entrada de nota](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox)

## <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](navigation.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución Notes.

2. En el **Explorador de soluciones**, haga clic con el botón derecho en la solución **Notes** y seleccione **Administrar paquetes de NuGet para la solución…** :

    ![Administración de paquetes de NuGet](database-images/vs/manage-nuget-packages.png)    

3. En el **Administrador de paquetes de NuGet**, seleccione la pestaña **Examinar** y busque el paquete de NuGet **sqlite-net-pcl**.

    > [!WARNING]
    > Hay diversos paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autores:** SQLite-net
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    En el **Administrador de paquetes de NuGet**, seleccione el paquete **sqlite-net-pcl** correcto, active la casilla **Proyecto** y haga clic en el botón **Instalar** para agregarlo a la solución:

    ![Selección de sqlite-net-pcl](database-images/vs/select-package.png)

    Este paquete se usará para incorporar operaciones de base de datos en la aplicación y se agregará a todos los proyectos de la solución.

    Cierre el **Administrador de paquetes NuGet**.

4. En el **Explorador de soluciones**, en el proyecto **Notes**, abra **Note.cs** en la carpeta **Modelos** y reemplace el código existente por el siguiente:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un modelo `Note` que almacenará los datos sobre cada nota en la aplicación. La propiedad `ID` está marcada con los atributos `PrimaryKey` y `AutoIncrement` para garantizar que cada instancia de `Note` en la base de datos de SQLite.NET tenga un identificador único proporcionado por SQLite.NET.

    Presione **CTRL+S** para guardar los cambios en **Notes.cs**.

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

5. En el **Explorador de soluciones**, agregue una nueva carpeta denominada **Datos** al proyecto **Notes**.

6. En el **Explorador de soluciones**, en el proyecto **Notes**, agregue una nueva clase denominada **NoteDatabase** a la carpeta **Datos**.

7. En **NoteDatabase.cs**, reemplace el código existente con el siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el paso siguiente.

    Presione **CTRL+S** para guardar los cambios en **NoteDatabase.cs**.  

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

8. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

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

    Este código define una propiedad `Database` que crea una instancia de `NoteDatabase` como singleton, y pasa el nombre de archivo de la base de datos como argumento al constructor `NoteDatabase`. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Presione **CTRL+S** para guardar los cambios en **App.xaml.cs**.  

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

9. En el **Explorador de soluciones**, en el proyecto **Notes**, expanda **NotesPage.xaml** en la carpeta **Vistas** y abra **NotesPage.xaml.cs**. Después, reemplace los métodos `OnAppearing` y `OnSelectionChanged` con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    El método `OnAppearing` rellena el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todas las notas almacenadas en la base de datos. El método `OnSelectionChanged` navega hasta el objeto `NoteEntryPage` y pasa la propiedad `ID` del objeto `Note` seleccionado como un parámetro de consulta.

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml.cs**.  

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

10. En el **Explorador de soluciones**, expanda **NoteEntryPage.xaml** en la carpeta **Vistas** y abra **NoteEntryPage.xaml.cs**. Después, reemplace los métodos `LoadNote`, `OnSaveButtonClicked` y `OnDeleteButtonClicked` por el código siguiente:

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
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
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      `NoteEntryPage` utiliza el método `LoadNote` para recuperar la nota de la base de datos, cuyo identificador se pasó como parámetro de consulta a la página, y la almacena como un objeto `Note` en el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se ejecuta el controlador de eventos `OnSaveButtonClicked`, la instancia de `Note` se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando se ejecuta el controlador de eventos `OnDeleteButtonClicked`, la instancia de `Note` se elimina de la base de datos y la aplicación regresa a la página anterior.

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml.cs**.  

11. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    En **NotesPage**, presione el botón **Agregar** para navegar hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba varias notas, de longitud variable, para observar el comportamiento de la aplicación. Cierre la aplicación y vuelva a iniciarla para asegurarse de que las notas que escribió se guardaron en la base de datos.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra la solución Notes.

2. En el **Panel de solución**, haga clic con el botón derecho en la solución **Notes** y seleccione **Administrar paquetes de NuGet…** :

    ![Administración de paquetes de NuGet](database-images/vsmac/manage-nuget-packages.png)    

3. En el cuadro de diálogo **Administrar paquetes de NuGet**, seleccione la pestaña **Examinar** y busque el paquete de NuGet **sqlite-net-pcl**.

    > [!WARNING]
    > Hay diversos paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autores:** SQLite-net
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    En el cuadro de diálogo **Administrar paquetes de NuGet**, seleccione el paquete **sqlite-net-pcl** y haga clic en el botón **Agregar paquete** para agregarlo a la solución:

      ![Selección de sqlite-net-pcl](database-images/vsmac/select-package.png)

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En el cuadro de diálogo **Seleccionar proyectos**, asegúrese de que todas las casillas estén activadas y presione el botón **Aceptar**:

    ![Agregar paquete a todos los proyectos](database-images/vsmac/add-package.png)

    Esto agregará el paquete de NuGet a cada proyecto de la solución.

5. En el **Panel de solución**, en el proyecto **Notes**, abra **Note.cs** en la carpeta **Modelos** y reemplace el código existente por el siguiente:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Esta clase define un modelo `Note` que almacenará los datos sobre cada nota en la aplicación. La propiedad `ID` está marcada con los atributos `PrimaryKey` y `AutoIncrement` para garantizar que cada instancia de `Note` en la base de datos de SQLite.NET tenga un identificador único proporcionado por SQLite.NET.

    Para guardar los cambios en **Note.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

6. En el **Panel de solución**, agregue una nueva carpeta denominada **Datos** al proyecto **Notes**.

7. En el **Panel de solución**, en el proyecto **Notes**, agregue una nueva clase denominada **NoteDatabase** a la carpeta **Datos**.

8. En **NoteDatabase.cs**, reemplace el código existente con el siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el paso siguiente.

    Para guardar los cambios en **NoteDatabase.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

9. En el **Panel de solución**, en el proyecto **Notes**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

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

    Este código define una propiedad `Database` que crea una instancia de `NoteDatabase` como singleton, y pasa el nombre de archivo de la base de datos como argumento al constructor `NoteDatabase`. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Para guardar los cambios en **App.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

10. En el **Panel de solución**, en el proyecto **Notes**, expanda **NotesPage.xaml** en la carpeta **Vistas** y abra **NotesPage.xaml.cs**. Después, reemplace los métodos `OnAppearing` y `OnSelectionChanged` con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    El método `OnAppearing` rellena el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) con todas las notas almacenadas en la base de datos. El método `OnSelectionChanged` navega hasta el objeto `NoteEntryPage` y pasa la propiedad `ID` del objeto `Note` seleccionado como un parámetro de consulta.

    Para guardar los cambios en **NotesPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

    > [!WARNING]
    > Actualmente, la aplicación no se compilará debido a errores que se corregirán en los pasos subsiguientes.

11. En el **Panel de solución**, expanda **NoteEntryPage.xaml** en la carpeta **Vistas** y abra **NoteEntryPage.xaml.cs**. Después, reemplace los métodos `LoadNote`, `OnSaveButtonClicked` y `OnDeleteButtonClicked` por el código siguiente:

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
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
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      `NoteEntryPage` utiliza el método `LoadNote` para recuperar la nota de la base de datos, cuyo identificador se pasó como parámetro de consulta a la página, y la almacena como un objeto `Note` en el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se ejecuta el controlador de eventos `OnSaveButtonClicked`, la instancia de `Note` se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando se ejecuta el controlador de eventos `OnDeleteButtonClicked`, la instancia de `Note` se elimina de la base de datos y la aplicación regresa a la página anterior.

      Para guardar los cambios en **NoteEntryPage.xaml.cs**, seleccione **Archivo > Guardar** (o bien presione **&#8984; + S**).

12. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](app.md#building-the-quickstart).

    En **NotesPage**, presione el botón **Agregar** para navegar hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba varias notas, de longitud variable, para observar el comportamiento de la aplicación. Cierre la aplicación y vuelva a iniciarla para asegurarse de que las notas que escribió se guardaron en la base de datos.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Almacenar datos de forma local en una base de datos de SQLite.NET.

Continúe con el inicio rápido siguiente para aplicar estilos a la aplicación con los estilos XAML.

> [!div class="nextstepaction"]
> [Siguiente](styling.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Análisis detallado de inicio rápido de Xamarin.Forms Shell](deepdive.md)
