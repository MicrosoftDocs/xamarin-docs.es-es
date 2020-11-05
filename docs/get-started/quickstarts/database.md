---
title: Almacenamiento de datos en una base de datos local de SQLite.NET
description: En este artículo se explica cómo almacenar datos en una base de datos de SQLite.NET local.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0992f7db22d79e58db0306bc09329f2852ef9489
ms.sourcegitcommit: 145bd7550d19088c84949ecf5b1cc39002183234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2020
ms.locfileid: "92897525"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Almacenamiento de datos en una base de datos de SQLite.NET local

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

En este inicio rápido aprenderá a:

- Usar el Administrador de paquetes de NuGet para agregar un paquete NuGet a un proyecto.
- Almacenar datos de forma local en una base de datos de SQLite.NET.

En el inicio rápido se le guía sobre cómo almacenar datos en una base de datos de SQLite.NET local. A continuación se muestra la aplicación final:

[![Página Notas](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox "Página Notas")
[![Página de entrada de nota](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox "Página de entrada de nota")

## <a name="prerequisites"></a>Requisitos previos

Antes de intentar este inicio rápido, debe completar correctamente el [inicio rápido](multi-page.md) anterior. También puede descargar el [ejemplo del inicio rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) y usarlo como punto de partida para este.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución Notes.

2. En el **Explorador de soluciones** , seleccione el proyecto **Notes** , haga clic con el botón derecho y, después, seleccione **Administrar paquetes NuGet…** :

    ![Agregar paquetes NuGet](database-images/vs/add-nuget-packages.png)    

3. En el **Administrador de paquetes NuGet** , seleccione la pestaña **Examinar** , busque el paquete NuGet **sqlite-net-pcl** , selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![Agregar paquete](database-images/vs/add-package.png)

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Propietarios:** praeclarum
    > - **Autores:** SQLite-net
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En el **Explorador de soluciones** , en el proyecto **Notes** , abra **Note.cs** en la carpeta **Modelos** y reemplace el código existente por el siguiente:

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

    Presione **CTRL+S** para guardar los cambios en **Note.cs** y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

5. En el **Explorador de soluciones** , agregue una nueva carpeta denominada **Datos** al proyecto **Notes**.

6. En el **Explorador de soluciones** , en el proyecto **Notes** , agregue una nueva clase denominada **NoteDatabase** a la carpeta **Datos**.

7. En **NoteDatabase.cs** , reemplace el código existente con el siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el paso siguiente.

    Presione **CTRL+S** para guardar los cambios en **NoteDatabase.cs** y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

8. En el **Explorador de soluciones** , en el proyecto **Notes** , haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

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
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Este código define una propiedad `Database` que crea una instancia de `NoteDatabase` como singleton, y pasa el nombre de archivo de la base de datos como argumento al constructor `NoteDatabase`. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

9. En el **Explorador de soluciones** , en el proyecto **Notes** , haga doble clic en **NotesPage.xaml.cs** para abrirlo. Después, reemplace el método `OnAppearing` con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena el elemento [`ListView`](xref:Xamarin.Forms.ListView) con todas las notas almacenadas en la base de datos.

    Presione **CTRL+S** para guardar los cambios en **NotesPage.xaml.cs** y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

10. En el **Explorador de soluciones** , haga doble clic en **NoteEntryPage.xaml.cs** para abrirlo. Después, reemplace los métodos `OnSaveButtonClicked` y `OnDeleteButtonClicked` con el código siguiente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` almacena una instancia de `Note`, que representa una única nota, en el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se ejecuta el controlador de eventos `OnSaveButtonClicked`, la instancia de `Note` se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando se ejecuta el controlador de eventos `OnDeleteButtonClicked`, la instancia de `Note` se elimina de la base de datos y la aplicación regresa a la página anterior.

      Presione **CTRL+S** para guardar los cambios en **NoteEntryPage.xaml.cs** y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto Notes.

2. En el **Panel de solución** , seleccione el proyecto **Notes** , haga clic con el botón derecho y seleccione **Agregar > Agregar paquetes NuGet...** :

    ![Agregar paquetes NuGet](database-images/vsmac/add-nuget-packages.png)    

3. En la ventana **Agregar paquetes** , busque el paquete NuGet **sqlite-net-pcl** , selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![Agregar paquete](database-images/vsmac/add-package.png)

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Propietarios:** praeclarum
    > - **Autores:** SQLite-net
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En el **Panel de solución** , en el proyecto **Notes** , abra **Note.cs** en la carpeta **Modelos** y reemplace el código existente por el siguiente:

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

    Para guardar los cambios en **Note.cs** , seleccione **Archivo > Guardar** (o bien presione **&#8984; + S** ) y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

5. En el **Panel de solución** , agregue una nueva carpeta denominada **Datos** al proyecto **Notes**.

6. En el **Panel de solución** , en el proyecto **Notes** , agregue una nueva clase denominada **NoteDatabase** a la carpeta **Datos**.

7. En **NoteDatabase.cs** , reemplace el código existente con el siguiente:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminarlos. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La clase `App` proporcionará esta ruta de acceso en el paso siguiente.

    Para guardar los cambios en **NoteDatabase.cs** , seleccione **Archivo > Guardar** (o bien presione **&#8984; + S** ) y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

8. En el **Panel de solución** , en el proyecto **Notes** , haga doble clic en **App.xaml.cs** para abrirlo. Después, reemplace el código existente con el siguiente:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

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
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Este código define una propiedad `Database` que crea una instancia de `NoteDatabase` como singleton, y pasa el nombre de archivo de la base de datos como argumento al constructor `NoteDatabase`. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S** ) y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

9. En el **Panel de solución** , en el proyecto **Notes** , haga doble clic en **NotesPage.xaml.cs** para abrirlo. Después, reemplace el método `OnAppearing` con el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena el elemento [`ListView`](xref:Xamarin.Forms.ListView) con todas las notas almacenadas en la base de datos.

    Para guardar los cambios en **NotesPage.xaml.cs** , seleccione **Archivo > Guardar** (o bien presione **&#8984; + S** ) y cierre el archivo.

    > [!WARNING]
    > Si en este momento intenta compilar la aplicación, se producirán errores que se corregirán en pasos posteriores.

10. En el **Panel de solución** , haga doble clic en **NoteEntryPage.xaml.cs** para abrirlo. Después, reemplace los métodos `OnSaveButtonClicked` y `OnDeleteButtonClicked` con el código siguiente:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` almacena una instancia de `Note`, que representa una única nota, en el objeto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. Cuando se ejecuta el controlador de eventos `OnSaveButtonClicked`, la instancia de `Note` se guarda en la base de datos y la aplicación regresa a la página anterior. Cuando se ejecuta el controlador de eventos `OnDeleteButtonClicked`, la instancia de `Note` se elimina de la base de datos y la aplicación regresa a la página anterior.

      Para guardar los cambios en **NoteEntryPage.xaml.cs** , seleccione **Archivo > Guardar** (o bien presione **&#8984; + S** ) y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para más información, vea [Compilación del inicio rápido](single-page.md#building-the-quickstart).

    En **NotesPage** presione el botón **+** para ir hasta **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación volverá a **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a:

- Usar el Administrador de paquetes de NuGet para agregar un paquete NuGet a un proyecto.
- Almacenar datos de forma local en una base de datos de SQLite.NET.

Para aplicar estilo a la aplicación con estilos XAML, continúe con el inicio rápido siguiente.

> [!div class="nextstepaction"]
> [Siguiente](styling.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Análisis detallado de inicio rápido de Xamarin.Forms](deepdive.md)
