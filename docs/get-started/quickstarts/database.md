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
ms.openlocfilehash: 2cd4726566e73aece5d0deef90ad1feedefaa2d8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249678"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Almacenar datos en una base de datos de SQLite.NET local

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

En esta guía de inicio rápido, obtendrá información sobre cómo:

- Use el administrador de paquetes de NuGet para agregar un paquete de NuGet a un proyecto.
- Almacenar datos localmente en una base de datos de SQLite.NET.

En la guía de inicio rápido se explica cómo almacenar datos en una base de datos de SQLite.NET local. A continuación se muestra la aplicación final:

[![](database-images/screenshots1-sml.png "")](database-images/screenshots1.png#lightbox "Página notas")
[![](database-images/screenshots2-sml.png "Página de entrada de nota") de página de notas](database-images/screenshots2.png#lightbox "Página de entrada de nota")

## <a name="prerequisites"></a>Requisitos previos

Antes de intentar esta guía de inicio rápido, debe completar correctamente el [Inicio rápido anterior](multi-page.md) . También puede descargar el [ejemplo de inicio rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) y usarlo como punto de partida para esta guía de inicio rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Actualizar la aplicación con Visual Studio

1. Inicie Visual Studio y abra la solución notas.

2. En **Explorador de soluciones**, seleccione el proyecto **notas** , haga clic con el botón derecho y seleccione **administrar paquetes NuGet..** .:

    ![](database-images/vs/add-nuget-packages.png "Agregue paquetes NuGet")    

3. En el **Administrador de paquetes NuGet**, seleccione la pestaña **Examinar**, busque el paquete NuGet **sqlite-net-pcl**, selecciónelo y haga clic en el botón **Instalar** para agregarlo al proyecto:

    ![](database-images/vs/add-package.png "Agregar paquete")

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autores:** Frank A. Krueger
    > - **Id.:** sqlite-net-pcl
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En **Explorador de soluciones**, en el proyecto **notas** , Abra **Note.CS** en la carpeta **modelos** y reemplace el código existente por el código siguiente:

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

    Esta clase define un `Note` modelo que almacenará los datos de cada nota en la aplicación. La `ID` propiedad se marca con `PrimaryKey` los `AutoIncrement` atributos y para asegurarse de `Note` que cada instancia de la base de datos SQLite.net tendrá un identificador único proporcionado por SQLite.net.

    Guarde los cambios en **Note.CS** presionando **Ctrl + S**y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

5. En **Explorador de soluciones**, agregue una nueva carpeta denominada **Data** al proyecto **Notes** .

6. En **Explorador de soluciones**, en el proyecto **notas** , agregue una nueva clase denominada **NoteDatabase** a la carpeta de **datos** .

7. En **NoteDatabase.CS**, reemplace el código existente por el código siguiente:

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

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminar datos de la misma. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La `App` clase proporcionará esta ruta de acceso en el paso siguiente.

    Guarde los cambios en **NoteDatabase.CS** presionando **Ctrl + S**y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

8. En **Explorador de soluciones**, en el proyecto **notas** , haga doble clic en **app.Xaml.CS** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

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

    Este código define una `Database` propiedad que crea una nueva `NoteDatabase` instancia como singleton, pasando el nombre de archivo de la base de datos `NoteDatabase` como argumento al constructor. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** presionando **CTRL+S** y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

9. En **Explorador de soluciones**, en el proyecto **notas** , haga doble clic en **NotesPage.Xaml.CS** para abrirlo. A continuación, `OnAppearing` Reemplace el método por el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena el [`ListView`](xref:Xamarin.Forms.ListView) con cualquier nota almacenada en la base de datos.

    Guarde los cambios en **NotesPage.Xaml.CS** presionando **Ctrl + S**y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

10. En **Explorador de soluciones**, haga doble clic en **NoteEntryPage.Xaml.CS** para abrirlo. A continuación, `OnSaveButtonClicked` Reemplace `OnDeleteButtonClicked` los métodos y por el código siguiente:

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

      Almacena una `Note` instancia de, que representa una sola nota, en la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. `NoteEntryPage` Cuando se `OnSaveButtonClicked` ejecuta el controlador de eventos, `Note` la instancia se guarda en la base de datos y la aplicación se desplaza a la página anterior. Cuando se `OnDeleteButtonClicked` ejecuta el controlador de eventos, `Note` la instancia se elimina de la base de datos y la aplicación se desplaza a la página anterior.

      Guarde los cambios en **NoteEntryPage.Xaml.CS** presionando **Ctrl + S**y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación se devolverá a la **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Actualizar la aplicación con Visual Studio para Mac

1. Inicie Visual Studio para Mac y abra el proyecto de notas.

2. En el **Panel de solución**, seleccione el proyecto **notas** , haga clic con el botón derecho y seleccione **Agregar > agregar paquetes NuGet.** ..:

    ![](database-images/vsmac/add-nuget-packages.png "Agregue paquetes NuGet")    

3. En la ventana **Agregar paquetes**, busque el paquete NuGet **sqlite-net-pcl**, selecciónelo y haga clic en el botón **Agregar paquete** para agregarlo al proyecto:

    ![](database-images/vsmac/add-package.png "Agregar paquete")

    > [!NOTE]
    > Hay varios paquetes NuGet con nombres similares. El paquete correcto tiene estos atributos:
    > - **Autor:** Frank A. Krueger
    > - **Id.:** sqlite-net-pcl
    > - **Vínculo de NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > A pesar del nombre del paquete, este paquete NuGet puede usarse en proyectos de .NET Standard.

    Este paquete se usará para incorporar operaciones de bases de datos en la aplicación.

4. En el **Panel de solución**, en el proyecto **notas** , Abra **Note.CS** en la carpeta **modelos** y reemplace el código existente por el código siguiente:

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

    Esta clase define un `Note` modelo que almacenará los datos de cada nota en la aplicación. La `ID` propiedad se marca con `PrimaryKey` los `AutoIncrement` atributos y para asegurarse de `Note` que cada instancia de la base de datos SQLite.net tendrá un identificador único proporcionado por SQLite.net.

    Guarde los cambios en **Note.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

5. En el **Panel de solución**, agregue una nueva carpeta denominada **Data** al proyecto **Notes** .

6. En el **Panel de solución**, en el proyecto **notas** , agregue una nueva clase denominada **NoteDatabase** a la carpeta de **datos** .

7. En **NoteDatabase.CS**, reemplace el código existente por el código siguiente:

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

    Esta clase contiene código para crear la base de datos, leer datos de ella, escribir datos en ella y eliminar datos de la misma. El código usa API asincrónicas de SQLite.NET que mueven las operaciones de base de datos a subprocesos en segundo plano. Además, el constructor `NoteDatabase` toma la ruta de acceso del archivo de base de datos como un argumento. La `App` clase proporcionará esta ruta de acceso en el paso siguiente.

    Guarde los cambios en **NoteDatabase.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

8. En el **Panel de solución**, en el proyecto **notas** , haga doble clic en **app.Xaml.CS** para abrirlo. A continuación, reemplace el código existente por el código siguiente:

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

    Este código define una `Database` propiedad que crea una nueva `NoteDatabase` instancia como singleton, pasando el nombre de archivo de la base de datos `NoteDatabase` como argumento al constructor. La ventaja de exponer la base de datos como un singleton es que se crea una conexión de base de datos única que se mantiene abierta mientras la aplicación se ejecuta, lo que evita el gasto de abrir y cerrar el archivo de base de datos cada vez que se realiza una operación de base de datos.

    Guarde los cambios en **App.xaml.cs** eligiendo **Archivo > Guardar** (o presionando **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

9. En el **Panel de solución**, en el proyecto **notas** , haga doble clic en **NotesPage.Xaml.CS** para abrirlo. A continuación, `OnAppearing` Reemplace el método por el código siguiente:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Este código rellena el [`ListView`](xref:Xamarin.Forms.ListView) con cualquier nota almacenada en la base de datos.

    Guarde los cambios en **NotesPage.Xaml.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

    > [!WARNING]
    > Si intenta compilar la aplicación en este momento, se producirán errores que se corregirán en los pasos siguientes.

10. En el **Panel de solución**, haga doble clic en **NoteEntryPage.Xaml.CS** para abrirlo. A continuación, `OnSaveButtonClicked` Reemplace `OnDeleteButtonClicked` los métodos y por el código siguiente:

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

      Almacena una `Note` instancia de, que representa una sola nota, en la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página. `NoteEntryPage` Cuando se `OnSaveButtonClicked` ejecuta el controlador de eventos, `Note` la instancia se guarda en la base de datos y la aplicación se desplaza a la página anterior. Cuando se `OnDeleteButtonClicked` ejecuta el controlador de eventos, `Note` la instancia se elimina de la base de datos y la aplicación se desplaza a la página anterior.

      Guarde los cambios en **NoteEntryPage.Xaml.CS** eligiendo **archivo > Guardar** (o presionando  **&#8984; + S**) y cierre el archivo.

11. Compile y ejecute el proyecto en cada plataforma. Para obtener más información, vea [crear la guía de inicio rápido](single-page.md#building-the-quickstart).

    En el **NotesPage** , presione **+** el botón para navegar hasta el **NoteEntryPage** y escriba una nota. Después de guardar la nota, la aplicación se devolverá a la **NotesPage**.

    Escriba un número de notas, de longitud variable, para observar el comportamiento de la aplicación.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, aprendió a:

- Use el administrador de paquetes de NuGet para agregar un paquete de NuGet a un proyecto.
- Almacenar datos localmente en una base de datos de SQLite.NET.

Para aplicar estilo a la aplicación con estilos XAML, continúe con la siguiente guía de inicio rápido.

> [!div class="nextstepaction"]
> [Siguiente](styling.md)

## <a name="related-links"></a>Vínculos relacionados

- [Notes (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Análisis detallado de Xamarin. Forms](deepdive.md)
