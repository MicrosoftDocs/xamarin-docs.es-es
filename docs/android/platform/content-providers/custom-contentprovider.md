---
title: Creación de un ContentProvider personalizado
description: En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: f72baaa4c74eb4bf0bb5eec64211d6ea2b18076c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756677"
---
# <a name="creating-a-custom-contentprovider"></a>Creación de un ContentProvider personalizado

_En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos._

## <a name="about-contentproviders"></a>Acerca de ContentProviders

Una clase de proveedor de contenido debe `ContentProvider`heredar de. Debe constar de un almacén de datos interno que se utiliza para responder a las consultas y debe exponer los URI y los tipos MIME como constantes para ayudar a utilizar el código para realizar solicitudes válidas de datos.

### <a name="uri-authority"></a>URI (autoridad)

`ContentProviders`se tiene acceso a ellos en Android mediante un URI. Una aplicación que expone un `ContentProvider` establece los identificadores URI a los que responderá en su archivo **archivo AndroidManifest. XML** . Cuando se instala la aplicación, estos URI se registran para que otras aplicaciones puedan tener acceso a ellos.

En mono para Android, la clase de proveedor de contenido debe `[ContentProvider]` tener un atributo para especificar el URI (o URI) que se debe agregar a **archivo AndroidManifest. XML**.

### <a name="mime-type"></a>Tipo MIME

El formato típico de los tipos MIME consta de dos partes. Android `ContentProviders` suele usar estas dos cadenas para la primera parte del tipo MIME:

1. `vnd.android.cursor.item`para representar una sola fila, use la `ContentResolver.CursorItemBaseType` constante en el código. &ndash;

1. `vnd.android.cursor.dir`en el caso de varias filas `ContentResolver.CursorDirBaseType` , utilice la constante en el código. &ndash;

La segunda parte del tipo MIME es específica de la aplicación y debe usar un estándar DNS inverso con un `vnd.` prefijo. En el código de `vnd.com.xamarin.sample.Vegetables`ejemplo se usa.

### <a name="data-model-metadata"></a>Metadatos del modelo de datos

El consumo de aplicaciones necesita construir consultas URI para tener acceso a diferentes tipos de datos. El URI base se puede expandir para hacer referencia a una tabla de datos determinada y también puede incluir parámetros para filtrar los resultados. También se deben declarar las columnas y cláusulas utilizadas con el cursor resultante para mostrar los datos.

Para asegurarse de que solo se construyen las consultas URI válidas, es un valor personalizado para proporcionar las cadenas válidas como valores constantes. Esto facilita el `ContentProvider` acceso a porque hace que los valores se puedan detectar a través de la finalización del código y evita errores tipográficos en las cadenas.

En el ejemplo anterior, `android.provider.ContactsContract` la clase exponía los metadatos de los datos de contactos. En nuestro Custom `ContentProvider` , solo se expondrán las constantes de la propia clase.

## <a name="implementation"></a>Implementación

Hay tres pasos para crear y utilizar un personalizado `ContentProvider`:

1. **Crear una clase de base de datos** &ndash; Implemente `SQLiteOpenHelper`.

2. **Cree una `ContentProvider` clase** &ndash; que `ContentProvider` implemente con una instancia de la base de datos, metadatos expuestos como métodos y valores constantes para tener acceso a los datos.

3. **El`ContentProvider` acceso a a través de su URI** &ndash; rellena `ContentProvider`un `CursorAdapter` mediante el, al que se tiene acceso a través de su URI.

Tal y como se `ContentProviders` ha explicado anteriormente, se puede usar desde otras aplicaciones que no sean donde están definidas. En este ejemplo, los datos se consumen en la misma aplicación, pero tenga en cuenta que otras aplicaciones también pueden tener acceso a él siempre que conozcan el URI e información sobre el esquema (que normalmente se expone como valores constantes).

## <a name="create-a-database"></a>Crear una base de datos

La `ContentProvider` mayoría de las implementaciones se basarán en una `SQLite` base de datos. El código de base de datos de ejemplo de **SimpleContentProvider/VegetableDatabase. CS** crea una base de datos de dos columnas muy simple, como se muestra a continuación:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

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
  {
    throw new NotImplementedException();
  }
}
```

La implementación de base de datos en sí no necesita ninguna consideración especial que se `ContentProvider`exponga con, sin embargo, si piensa `ContentProvider's` enlazar los `ListView` datos a un control, una columna de `_id` entero única denominada debe formar parte de la conjunto de resultados. Vea el documento sobre [ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más detalles sobre el uso `ListView` del control.

## <a name="create-the-contentprovider"></a>Crear ContentProvider

En el resto de esta sección se proporcionan instrucciones paso a paso sobre cómo se compiló la clase de ejemplo **SimpleContentProvider/VegetableProvider. CS** .

### <a name="initialize-the-database"></a>Inicializar la base de datos

El primer paso es crear una subclase `ContentProvider` y agregar la base de datos que se va a utilizar.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

El resto del código formará la implementación del proveedor de contenido real que permite detectar y consultar los datos.

## <a name="add-metadata-for-consumers"></a>Agregar metadatos para consumidores

Hay cuatro tipos diferentes de metadatos que se van a exponer en la `ContentProvider` clase. Solo se requiere la autoridad; el resto se realiza por Convención.

- **Entidad de certificación** El atributo se debe agregar a la clase para que se registre con Android cuando se instale la aplicación. &ndash; `ContentProvider`

- **Identificador URI** &ndash; Seexponecomounaconstanteparaqueseafácilde`CONTENT_URI` usar en el código. Debe coincidir con la autoridad, pero incluir el esquema y la ruta de acceso base.

- **Tipos MIME** &ndash; Las listas de resultados y los resultados únicos se tratan como tipos de contenido diferentes, por lo que definimos dos tipos MIME para representarlos.

- **InterfaceConsts** &ndash; Proporcione un valor constante para cada nombre de columna de datos, de modo que el código utilizado pueda detectar fácilmente y hacer referencia a ellos sin arriesgarse a que se produzcan errores tipográficos.

En este código se muestra cómo se implementa cada uno de estos elementos y se agregan a la definición de la base de datos del paso anterior:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```

## <a name="implement-the-uri-parsing-helper"></a>Implementar la aplicación auxiliar de análisis de URI

Dado que el código de consumo utiliza los URI para realizar `ContentProvider`solicitudes de un, es necesario poder analizar esas solicitudes para determinar qué datos se deben devolver. La `UriMatcher` clase puede ayudar a analizar los URI, una vez que se ha inicializado con los patrones de `ContentProvider` URI que admite.

`UriMatcher` En el ejemplo se inicializará con dos URI:

1. *"com. Xamarin. sample. VegetableProvider/verduras"* &ndash; solicitud para devolver la lista completa de verduras.

2. *"com. Xamarin. sample. VegetableProvider/verduras/\#"* &ndash; , donde \# es un marcador de posición para un parámetro numérico `_id` (el de la fila de la base de datos). También se puede usar un\*marcador de posición de asterisco ("") para hacer coincidir un parámetro de texto.

En el código usamos las constantes para hacer referencia a los valores de metadatos como la autoridad\_y la ruta de acceso base. Los códigos de retorno se utilizarán en métodos que realizan análisis de URI, para determinar qué datos se devuelven.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Este código es privado para la `ContentProvider` clase. Consulte [la documentación de UriMatcher de Google](xref:Android.Content.UriMatcher) para obtener más información.

## <a name="implement-the-querymethod"></a>Implementación de QueryMethod

El `ContentProvider` método más sencillo para implementar es el `Query` método. `UriMatcher` En la implementación siguiente se usa para analizar `uri` el parámetro y llamar al método de base de datos correcto. Si contiene un parámetro de identificador, se analiza el entero (mediante `LastPathSegment`) y se usa en la consulta de la base de datos. `uri`

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

También `GetType` se debe reemplazar el método. Se puede llamar a este método para determinar el tipo de contenido que se devolverá para un URI determinado.
Esto puede indicar a la aplicación que lo consume cómo controlar los datos.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```

## <a name="implement-the-other-overrides"></a>Implementar las otras invalidaciones

Nuestro sencillo ejemplo no permite la edición o eliminación de datos, pero los métodos INSERT, Update y DELETE deben implementarse de modo que se agreguen sin una implementación:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

Esto completa la implementación básica `ContentProvider` . Una vez instalada la aplicación, los datos que expone estarán disponibles tanto dentro de la aplicación como en cualquier otra aplicación que conozca el URI para hacer referencia a ella.

## <a name="access-the-contentprovider"></a>Acceso a ContentProvider

`VegetableProvider` Una vez implementado, el acceso a él se realiza del mismo modo que el proveedor de contactos al principio de este documento: obtener un cursor con el URI especificado y, a continuación, utilizar un adaptador para tener acceso a los datos.

## <a name="bind-a-listview-to-a-contentprovider"></a>Enlazar un control ListView a un ContentProvider

Para rellenar un `ListView` con datos, usamos el URI que corresponde a la lista sin filtrar de verduras. En el código, usamos el valor `VegetableProvider.CONTENT_URI`constante, que sabemos que se resuelve como. `com.xamarin.sample.vegetableprovider/vegetables` Nuestra `VegetableProvider.Query` implementación devolverá un cursor que se puede enlazar `ListView`a.

El código de `SimpleContentProvider/HomeScreen.cs` muestra lo sencillo que es mostrar los datos de un `ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

La aplicación resultante tiene el siguiente aspecto:

[![Captura de pantalla de la lista de verduras, frutas, frutos de flores, leguminosas, bulbos, tubérculos](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar un único elemento de un ContentProvider

Una aplicación consumidora también podría querer tener acceso a filas de datos individuales, lo que se puede hacer mediante la construcción de un URI diferente que haga referencia a una fila concreta (por ejemplo,).

Use `ContentResolver` directamente para tener acceso a un único elemento, mediante la creación de un URI `Id`con el requerido.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

El método completo tiene el siguiente aspecto:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```

## <a name="related-links"></a>Vínculos relacionados

- [SimpleContentProvider (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
