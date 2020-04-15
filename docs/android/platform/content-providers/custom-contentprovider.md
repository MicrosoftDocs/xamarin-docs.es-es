---
title: Creación de un ContentProvider personalizado
description: En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear una instancia personalizada de ContentProvider y luego consumir sus datos.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020525"
---
# <a name="creating-a-custom-contentprovider"></a>Creación de un ContentProvider personalizado

_En la sección anterior se mostró cómo consumir datos de una implementación de ContentProvider integrada. En esta sección se explicará cómo crear una instancia personalizada de ContentProvider y luego consumir sus datos._

## <a name="about-contentproviders"></a>Acerca de ContentProvider

Una clase de proveedor de contenido debe heredar de `ContentProvider`. Debe constar de un almacén de datos interno que se utiliza para responder a las consultas y debe exponer los URI y los tipos MIME como constantes para ayudar al código de consumo a realizar solicitudes de datos válidas.

### <a name="uri-authority"></a>URI (autoridad)

En Android, se accede a las instancias de `ContentProviders` mediante un URI. Una aplicación que expone una instancia de `ContentProvider` establece los URI a los que responderá en su archivo **AndroidManifest.xml**. Cuando se instala la aplicación, se registran estos URI para que otras aplicaciones puedan acceder a ellos.

En Mono para Android, la clase de proveedor de contenido debe tener un atributo `[ContentProvider]` para especificar el URI o los URI que deben agregarse a **AndroidManifest.xml**.

### <a name="mime-type"></a>Tipo MIME

El formato típico de los tipos MIME consta de dos partes. Las instancias de `ContentProviders` de Android suelen usar estas dos cadenas para la primera parte del tipo MIME:

1. `vnd.android.cursor.item`: para representar una sola fila, use la constante `ContentResolver.CursorItemBaseType` en el código.

1. `vnd.android.cursor.dir`: para varias filas, use la constante `ContentResolver.CursorDirBaseType` en el código.

La segunda parte del tipo MIME es específica de la aplicación y debe usar un estándar DNS inverso con un prefijo `vnd.`. El código de ejemplo utiliza `vnd.com.xamarin.sample.Vegetables`.

### <a name="data-model-metadata"></a>Metadatos del modelo de datos

Las aplicaciones de consumo necesitan construir consultas de URI para acceder a diferentes tipos de datos. El URI base se puede expandir para hacer referencia a una tabla de datos determinada y también puede incluir parámetros para filtrar los resultados. También se deben declarar las columnas y cláusulas utilizadas con el cursor resultante para mostrar los datos.

Para asegurarse de que solo se construyen consultas de URI válidas, es habitual proporcionar las cadenas válidas como valores constantes. Esto facilita el acceso a la instancia de `ContentProvider` porque hace que los valores se puedan detectar a través de la ejecución del código y evita errores tipográficos en las cadenas.

En el ejemplo anterior, la clase `android.provider.ContactsContract` expone los metadatos de los datos de contactos. En nuestro `ContentProvider` personalizado, solo se expondrán las constantes en la propia clase.

## <a name="implementation"></a>Implementación

Se deben seguir tres pasos para crear y consumir una instancia de `ContentProvider` personalizada:

1. **Crear una clase de base de datos**: implementar `SQLiteOpenHelper`.

2. **Crear una clase `ContentProvider`** : implementar `ContentProvider` con una instancia de la base de datos, los metadatos expuestos como valores constantes y los métodos para acceder a los datos.

3. **Acceder a la instancia de `ContentProvider` a través de su URI**: rellenar un elemento `CursorAdapter` mediante la instancia de `ContentProvider`, a la que se accede a través de su URI.

Como se explicó anteriormente, las instancias de `ContentProviders` se pueden consumir desde aplicaciones distintas de las que se definen. En este ejemplo, los datos se consumen en la misma aplicación, pero tenga en cuenta que otras aplicaciones también pueden acceder a ellos siempre que conozcan el URI y la información sobre el esquema (que normalmente se exponen como valores constantes).

## <a name="create-a-database"></a>Crear una base de datos

La mayoría de las implementaciones de `ContentProvider` se basarán en una base de datos de `SQLite`. El código de base de datos de ejemplo de **SimpleContentProvider/VegetableDatabase.cs** crea una base de datos de dos columnas muy simple, como se muestra a continuación:

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

La implementación de la base de datos no necesita que se exponga ninguna consideración especial con `ContentProvider`, sin embargo, si piensa enlazar los datos de `ContentProvider's` a un control `ListView`, es necesario que una columna de enteros única denominada `_id` forme parte del conjunto de resultados. Para obtener más información sobre el uso del control `ListView`, consulte el documento [ListViews y adaptadores](~/android/user-interface/layouts/list-view/index.md).

## <a name="create-the-contentprovider"></a>Creación de una instancia de ContentProvider

En el resto de esta sección se proporcionan instrucciones paso a paso sobre cómo se compiló la clase de ejemplo **SimpleContentProvider/VegetableProvider.cs**.

### <a name="initialize-the-database"></a>Inicialización de la base de datos

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

## <a name="add-metadata-for-consumers"></a>Adición de metadatos para consumidores

Hay cuatro tipos diferentes de metadatos que se van a exponer en la clase `ContentProvider`. Solo se requiere la autoridad; el resto se realiza por convención.

- **Autoridad**: el atributo `ContentProvider` debe *agregarse* a la clase para que se registre con Android cuando se instale la aplicación.

- **URI**: el elemento `CONTENT_URI` se expone como una constante para que sea fácil de usar en el código. Debe coincidir con la autoridad, pero incluir el esquema y la ruta de acceso base.

- **Tipos MIME**: las listas de resultados y los resultados únicos se tratan como tipos de contenido diferentes, por lo que se definen dos tipos MIME para representarlos.

- **InterfaceConsts**: proporcionan un valor constante para cada nombre de columna de datos, de modo que el código de consumo puede detectarlos fácilmente y hacerles referencia sin arriesgarse a que se produzcan errores tipográficos.

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

## <a name="implement-the-uri-parsing-helper"></a>Implementación del asistente de análisis de URI

Dado que el código de consumo usa los URI para realizar solicitudes de una instancia de `ContentProvider`, es necesario poder analizar esas solicitudes para determinar qué datos se van a devolver. La clase `UriMatcher` puede ayudar a analizar los URI, una vez que se haya inicializado con los patrones de URI que la instancia de `ContentProvider` admite.

El elemento `UriMatcher` del ejemplo se inicializará con dos URI:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* : solicitud para devolver la lista completa de verduras (vegetables).

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* : donde \# es un marcador de posición para un parámetro numérico (`_id` de la fila en la base de datos). También se puede usar un marcador de posición de asterisco ("\*") para que coincida con un parámetro de texto.

En el código, se usan las constantes para hacer referencia a los valores de metadatos como AUTHORITY y BASE\_PATH. Los códigos de retorno se utilizarán en métodos que realizan el análisis de URI, para determinar qué datos se devuelven.

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

Este código es completamente privado para la clase `ContentProvider`. Consulte la [documentación de UriMatcher de Google](xref:Android.Content.UriMatcher) para obtener más información.

## <a name="implement-the-querymethod"></a>Implementación de QueryMethod

El método `ContentProvider` más sencillo de implementar es el método `Query`. En la implementación siguiente se usa el elemento `UriMatcher` para analizar el parámetro `uri` y llamar al método de base de datos correcto. Si `uri` contiene un parámetro de id., se analiza el entero (mediante `LastPathSegment`) y se usa en la consulta de la base de datos.

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

El método `GetType` también debe reemplazarse. Se puede llamar a este método para determinar el tipo de contenido que se devolverá para un URI determinado.
Con ello se puede indicar a la aplicación de consumo cómo controlar los datos.

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

## <a name="implement-the-other-overrides"></a>Implementación de los demás reemplazos

Nuestro sencillo ejemplo no permite la edición o eliminación de datos, pero deben implementarse los métodos de inserción, actualización y eliminación, por lo que deben agregarse sin una implementación:

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

Esto completa la implementación básica de `ContentProvider`. Una vez instalada la aplicación, los datos que expone estarán disponibles tanto dentro de la aplicación como en cualquier otra aplicación que conozca el URI para hacerle referencia.

## <a name="access-the-contentprovider"></a>Acceso a ContentProvider

Una vez implementado el elemento `VegetableProvider`, se accede del mismo modo que el proveedor de contactos al principio de este documento: se obtiene un cursor mediante el URI especificado y, a continuación, se utiliza un adaptador para acceder a los datos.

## <a name="bind-a-listview-to-a-contentprovider"></a>Enlace de un control ListView a una instancia de ContentProvider

Para rellenar un control `ListView` con datos, se usa el URI que corresponde a la lista sin filtrar de verduras (vegetables). En el código, usamos el valor constante `VegetableProvider.CONTENT_URI`, que sabemos que se resuelve en `com.xamarin.sample.vegetableprovider/vegetables`. Nuestra implementación de `VegetableProvider.Query` devolverá un cursor que se puede enlazar a `ListView`.

El código de `SimpleContentProvider/HomeScreen.cs` muestra lo sencillo que es mostrar los datos de una instancia de `ContentProvider`:

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

La aplicación resultante tiene este aspecto:

[![Captura de pantalla de la lista de Vegetables, Fruits, Flower Buds, Legumes, Bulbs, Tubers](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Recuperación de un único elemento de una instancia de ContentProvider

Una aplicación de consumo también puede querer acceder a filas de datos individuales, lo que se puede hacer mediante la construcción de un URI diferente que haga referencia a una fila concreta (por ejemplo).

Use `ContentResolver` directamente para acceder a un único elemento, mediante la creación de un URI con el `Id` requerido.

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
