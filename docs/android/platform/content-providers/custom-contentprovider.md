---
title: Creación de un ContentProvider personalizado
description: En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020525"
---
# <a name="creating-a-custom-contentprovider"></a>Creación de un ContentProvider personalizado

_En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear un ContentProvider personalizado y, a continuación, utilizar sus datos._

## <a name="about-contentproviders"></a>Acerca de ContentProviders

Una clase de proveedor de contenido debe heredar de `ContentProvider`. Debe constar de un almacén de datos interno que se utiliza para responder a las consultas y debe exponer los URI y los tipos MIME como constantes para ayudar a utilizar el código para realizar solicitudes válidas de datos.

### <a name="uri-authority"></a>URI (autoridad)

se tiene acceso a `ContentProviders` en Android mediante un URI. Una aplicación que expone un `ContentProvider` establece los identificadores URI a los que responderá en su archivo **archivo AndroidManifest. XML** . Cuando se instala la aplicación, estos URI se registran para que otras aplicaciones puedan tener acceso a ellos.

En mono para Android, la clase de proveedor de contenido debe tener un atributo `[ContentProvider]` para especificar el URI (o URI) que se debe agregar a **archivo AndroidManifest. XML**.

### <a name="mime-type"></a>Tipo MIME

El formato típico de los tipos MIME consta de dos partes. Los `ContentProviders` de Android suelen usar estas dos cadenas para la primera parte del tipo MIME:

1. `vnd.android.cursor.item` &ndash; para representar una sola fila, use la constante `ContentResolver.CursorItemBaseType` en el código.

1. `vnd.android.cursor.dir` &ndash; para varias filas, utilice la constante `ContentResolver.CursorDirBaseType` en el código.

La segunda parte del tipo MIME es específica de la aplicación y debe usar un estándar DNS inverso con un prefijo `vnd.`. En el código de ejemplo se usa `vnd.com.xamarin.sample.Vegetables`.

### <a name="data-model-metadata"></a>Metadatos del modelo de datos

El consumo de aplicaciones necesita construir consultas URI para tener acceso a diferentes tipos de datos. El URI base se puede expandir para hacer referencia a una tabla de datos determinada y también puede incluir parámetros para filtrar los resultados. También se deben declarar las columnas y cláusulas utilizadas con el cursor resultante para mostrar los datos.

Para asegurarse de que solo se construyen las consultas URI válidas, es un valor personalizado para proporcionar las cadenas válidas como valores constantes. Esto facilita el acceso al `ContentProvider` porque hace que los valores se puedan detectar a través de la finalización del código y evita errores tipográficos en las cadenas.

En el ejemplo anterior, la clase `android.provider.ContactsContract` expone los metadatos de los datos de contactos. En nuestro `ContentProvider` personalizado, solo se expondrán las constantes en la propia clase.

## <a name="implementation"></a>Implementación

Hay tres pasos para crear y utilizar un `ContentProvider`personalizado:

1. **Cree una clase de base de datos** &ndash; implementar `SQLiteOpenHelper`.

2. **Cree una clase `ContentProvider`** &ndash; implementar `ContentProvider` con una instancia de la base de datos, metadatos expuestos como valores constantes y métodos para tener acceso a los datos.

3. **Obtenga acceso a la `ContentProvider` a través de su uri** &ndash; rellene un `CursorAdapter` con el `ContentProvider`, al que se tiene acceso a través de su URI.

Como se explicó anteriormente, `ContentProviders` se pueden consumir desde aplicaciones distintas de las que se definen. En este ejemplo, los datos se consumen en la misma aplicación, pero tenga en cuenta que otras aplicaciones también pueden tener acceso a él siempre que conozcan el URI e información sobre el esquema (que normalmente se expone como valores constantes).

## <a name="create-a-database"></a>Crear una base de datos

La mayoría de las implementaciones de `ContentProvider` se basarán en una base de datos de `SQLite`. El código de base de datos de ejemplo de **SimpleContentProvider/VegetableDatabase. CS** crea una base de datos de dos columnas muy simple, como se muestra a continuación:

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

La implementación de la base de datos no necesita ninguna consideración especial que se exponga con un `ContentProvider`, sin embargo, si piensa enlazar los datos de `ContentProvider's` a un control de `ListView`, una columna de entero única denominada `_id` debe formar parte del conjunto de resultados. Vea el documento sobre [ListView y adaptadores](~/android/user-interface/layouts/list-view/index.md) para obtener más detalles sobre el uso del control de `ListView`.

## <a name="create-the-contentprovider"></a>Crear ContentProvider

En el resto de esta sección se proporcionan instrucciones paso a paso sobre cómo se compiló la clase de ejemplo **SimpleContentProvider/VegetableProvider. CS** .

### <a name="initialize-the-database"></a>Inicializar la base de datos

El primer paso es crear una subclase `ContentProvider` y agregar la base de datos que va a utilizar.

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

Hay cuatro tipos diferentes de metadatos que se van a exponer en la clase `ContentProvider`. Solo se requiere la autoridad; el resto se realiza por Convención.

- **Autoridad** &ndash; *debe* agregarse el atributo de `ContentProvider` a la clase para que se registre con Android cuando se instale la aplicación.

- **Uri** &ndash; la `CONTENT_URI` se expone como una constante para que sea fácil de usar en el código. Debe coincidir con la autoridad, pero incluir el esquema y la ruta de acceso base.

- Los **tipos mime** &ndash; listas de resultados y resultados únicos se tratan como tipos de contenido diferentes, por lo que se definen dos tipos MIME para representarlos.

- **InterfaceConsts** &ndash; proporcionan un valor constante para cada nombre de columna de datos, de modo que el código utilizado puede detectar fácilmente y hacer referencia a ellos sin arriesgarse a que se produzcan errores tipográficos.

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

Dado que el uso de código usa URI para realizar solicitudes de un `ContentProvider`, es necesario poder analizar esas solicitudes para determinar qué datos se van a devolver. La clase `UriMatcher` puede ayudar a analizar los URI, una vez que se ha inicializado con los patrones de URI que admite el `ContentProvider`.

El `UriMatcher` del ejemplo se inicializará con dos URI:

1. *"com. Xamarin. sample. VegetableProvider/verduras"* &ndash; solicitud para devolver la lista completa de verduras.

2. *"com. Xamarin. sample. VegetableProvider/verduras/\#"* &ndash; donde el \# es un marcador de posición para un parámetro numérico (el `_id` de la fila en la base de datos). También se puede usar un marcador de posición de asterisco ("\*") para hacer coincidir un parámetro de texto.

En el código usamos las constantes para hacer referencia a los valores de metadatos como la autoridad y la ruta de acceso\_BASE. Los códigos de retorno se utilizarán en métodos que realizan análisis de URI, para determinar qué datos se devuelven.

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

Este código es privado para la clase `ContentProvider`. Consulte [la documentación de UriMatcher de Google](xref:Android.Content.UriMatcher) para obtener más información.

## <a name="implement-the-querymethod"></a>Implementación de QueryMethod

El método de `ContentProvider` más sencillo que se va a implementar es el método `Query`. En la implementación siguiente se usa el `UriMatcher` para analizar el parámetro `uri` y llamar al método de base de datos correcto. Si el `uri` contiene un parámetro de identificador, se analiza el entero (mediante `LastPathSegment`) y se usa en la consulta de la base de datos.

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

También se debe invalidar el método `GetType`. Se puede llamar a este método para determinar el tipo de contenido que se devolverá para un URI determinado.
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

Esto completa la implementación de `ContentProvider` básica. Una vez instalada la aplicación, los datos que expone estarán disponibles tanto dentro de la aplicación como en cualquier otra aplicación que conozca el URI para hacer referencia a ella.

## <a name="access-the-contentprovider"></a>Acceso a ContentProvider

Una vez implementado el `VegetableProvider`, el acceso a él se realiza del mismo modo que el proveedor de contactos al principio de este documento: obtener un cursor mediante el URI especificado y, a continuación, utilizar un adaptador para tener acceso a los datos.

## <a name="bind-a-listview-to-a-contentprovider"></a>Enlazar un control ListView a un ContentProvider

Para rellenar un `ListView` con datos, usamos el URI que corresponde a la lista sin filtrar de verduras. En el código, usamos el valor constante `VegetableProvider.CONTENT_URI`, que sabemos que se resuelve en `com.xamarin.sample.vegetableprovider/vegetables`. Nuestra implementación de `VegetableProvider.Query` devolverá un cursor que se puede enlazar a la `ListView`.

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

[![captura de pantalla de la aplicación que muestra verduras, frutas, artículos de flores, leguminosas, bulbos, tubérculos](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperar un único elemento de un ContentProvider

Una aplicación consumidora también podría querer tener acceso a filas de datos individuales, lo que se puede hacer mediante la construcción de un URI diferente que haga referencia a una fila concreta (por ejemplo,).

Use `ContentResolver` directamente para tener acceso a un único elemento, mediante la creación de un URI con el `Id`requerido.

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
