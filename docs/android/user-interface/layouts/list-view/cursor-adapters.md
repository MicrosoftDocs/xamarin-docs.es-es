---
title: Uso de CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: 81ce3a31a151c8783e09bc42f2dcc2c64168a2e4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457580"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>Uso de CursorAdapters con Xamarin. Android

Android proporciona clases de adaptador específicamente para Mostrar datos de una consulta de base de datos SQLite:

 **SimpleCursorAdapter** : similar a un `ArrayAdapter` porque se puede usar sin subclases. Simplemente proporcione los parámetros necesarios (como un cursor y la información de diseño) en el constructor y, a continuación, asígnelo a `ListView` .

 **CursorAdapter** : una clase base de la que se puede heredar cuando se necesita más control sobre el enlace de valores de datos a los controles de diseño (por ejemplo, ocultar o mostrar controles o cambiar sus propiedades).

Los adaptadores de cursor proporcionan una forma de alto rendimiento para desplazarse a través de las largas listas de datos que se almacenan en SQLite. El código utilizado debe definir una consulta SQL en un `Cursor` objeto y, a continuación, describir cómo crear y rellenar las vistas de cada fila.

## <a name="creating-an-sqlite-database"></a>Crear una base de datos de SQLite

Para mostrar los adaptadores de cursores se requiere una implementación de base de datos SQLite simple. El código de **SimpleCursorTableAdapter/VegetableDatabase. CS** contiene el código y SQL para crear una tabla y rellenarla con algunos datos.
`VegetableDatabase`Aquí se muestra la clase completa:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

Se `VegetableDatabase` creará una instancia de la clase en el `OnCreate` método de la `HomeScreen` actividad. La `SQLiteOpenHelper` clase base administra la configuración del archivo de base de datos y garantiza que el SQL en su `OnCreate` método solo se ejecuta una vez. Esta clase se utiliza en los dos ejemplos siguientes para `SimpleCursorAdapter` y `CursorAdapter` .

La consulta de cursor *debe* tener una columna `_id` de enteros para `CursorAdapter` que funcione. Si la tabla subyacente no tiene una columna de enteros denominada `_id` , utilice un alias de columna para otro entero único en `RawQuery` que conforma el cursor. Consulte los [documentos de Android](xref:Android.Widget.CursorAdapter) para obtener más información.

### <a name="creating-the-cursor"></a>Crear el cursor

En los ejemplos `RawQuery` se usa para convertir una consulta SQL en un `Cursor` objeto. La lista de columnas que se devuelve desde el cursor define las columnas de datos que están disponibles para su presentación en el adaptador de cursor. Aquí se muestra el código que crea la base de datos en el método **SimpleCursorTableAdapter/HomeScreen. CS** `OnCreate` :

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Cualquier código que llame a `StartManagingCursor` también debe llamar a `StopManagingCursor` . En los ejemplos `OnCreate` se usa para iniciar y `OnDestroy` para cerrar el cursor. El `OnDestroy` método contiene este código:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Una vez que una aplicación tiene una base de datos SQLite disponible y ha creado un objeto Cursor como se muestra, puede utilizar `SimpleCursorAdapter` o una subclase de `CusorAdapter` para mostrar las filas en un `ListView` .

## <a name="using-simplecursoradapter"></a>Usar SimpleCursorAdapter

`SimpleCursorAdapter` es como `ArrayAdapter` , pero especializado para su uso con SQLite. No requiere creación de subclases: solo tiene que establecer algunos parámetros simples al crear el objeto y, a continuación, asignarlo a la `ListView` `Adapter` propiedad de.

Los parámetros para el constructor SimpleCursorAdapter son:

 **Context** : referencia a la actividad contenedora.

 **Diseño** : el identificador de recurso de la vista de fila que se va a usar.

 **ICursor** : un cursor que contiene la consulta de SQLite para los datos que se van a mostrar.

 **De** la matriz de cadenas: matriz de cadenas que corresponden a los nombres de las columnas del cursor.

 **En** matriz de enteros: matriz de identificadores de diseño que corresponden a los controles del diseño de fila. El valor de la columna especificada en la `from` matriz se enlazará al ControlID especificado en esta matriz en el mismo índice.

Las `from` `to` matrices y deben tener el mismo número de entradas porque forman una asignación desde el origen de datos a los controles de diseño de la vista.

El código de ejemplo **SimpleCursorTableAdapter/HomeScreen. CS** es `SimpleCursorAdapter` similar al siguiente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` es una manera rápida y sencilla de mostrar los datos de SQLite en un `ListView` . La principal limitación es que solo puede enlazar valores de columna a controles de visualización, no permite cambiar otros aspectos del diseño de fila (por ejemplo, mostrar u ocultar controles o cambiar propiedades).

## <a name="subclassing-cursoradapter"></a>Subclase de CursorAdapter

Una `CursorAdapter` subclase tiene las mismas ventajas de rendimiento que `SimpleCursorAdapter` para mostrar los datos de SQLite, pero también proporciona un control completo sobre la creación y el diseño de cada vista de fila. La `CursorAdapter` implementación es muy diferente de la subclase `BaseAdapter` porque no invalida `GetView` , o el `GetItemId` `Count` `this[]` indizador.

Dada una base de datos de SQLite en funcionamiento, solo necesita invalidar dos métodos para crear una `CursorAdapter` subclase:

- **BindView** : dada una vista, actualícelo para mostrar los datos en el cursor proporcionado.

- **NewView** : se llama cuando `ListView` requiere una nueva vista para mostrar. El se encargará `CursorAdapter` de las vistas de reciclaje (a diferencia del `GetView` método en los adaptadores normales).

Las subclases de adaptador de ejemplos anteriores tienen métodos para devolver el número de filas y para recuperar el elemento actual: el no `CursorAdapter` requiere estos métodos porque esa información se puede obtener del propio cursor. Al dividir la creación y el rellenado de cada vista en estos dos métodos, el `CursorAdapter` aplica la reutilización de la vista. Esto contrasta con un adaptador normal en el que es posible omitir el `convertView` parámetro del `BaseAdapter.GetView` método.

### <a name="implementing-the-cursoradapter"></a>Implementar CursorAdapter

El código de **CursorTableAdapter/HomeScreenCursorAdapter. CS** contiene una `CursorAdapter` subclase. Almacena una referencia de contexto pasada en el constructor para que pueda tener acceso a `LayoutInflater` en el `NewView` método. La clase completa tiene el siguiente aspecto:

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```

### <a name="assigning-the-cursoradapter"></a>Asignación de CursorAdapter

En el `Activity` que mostrará el `ListView` , cree el cursor y `CursorAdapter` , a continuación, asígnelo a la vista de lista.

El código que realiza esta acción en el método **CursorTableAdapter/HomeScreen. CS** `OnCreate` se muestra aquí:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

El `OnDestroy` método contiene la `StopManagingCursor` llamada al método que se ha descrito anteriormente.

## <a name="related-links"></a>Vínculos relacionados

- [SimpleCursorTableAdapter (ejemplo)](/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (ejemplo)](/samples/xamarin/monodroid-samples/cursortableadapter)