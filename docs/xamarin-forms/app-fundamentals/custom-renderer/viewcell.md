---
title: Personalización de ViewCell
description: Un ViewCell de Xamarin.Forms es una celda que se puede agregar a ListView o TableView y que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear un representador personalizado para un ViewCell que se hospeda dentro de un control ListView de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 6fd952fc929fc11d19d8ceb0153aa8d7702a21a7
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889224"
---
# <a name="customizing-a-viewcell"></a>Personalización de ViewCell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)

_Un ViewCell de Xamarin.Forms es una celda que se puede agregar a ListView o TableView y que contiene una vista definida por el desarrollador. En este artículo se muestra cómo crear un representador personalizado para un ViewCell que se hospeda dentro de un control ListView de Xamarin.Forms. Esto impide que se llame varias veces a los cálculos de diseño de Xamarin.Forms durante el desplazamiento de ListView._

Todos las celdas de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa una [`ViewCell`](xref:Xamarin.Forms.ViewCell), en iOS se crea la instancia de la clase `ViewCellRenderer`, que a su vez crea una instancia del control `UITableViewCell` nativo. En la plataforma Android, la clase `ViewCellRenderer` crea una instancia del control `View` nativo. En la Plataforma Universal de Windows (UWP), la clase `ViewCellRenderer` crea una instancia de `DataTemplate` nativa. Para obtener más información sobre las clases de control nativo que se asignan a los controles de Xamarin.Forms y el representador, vea [Renderer Base Classes and Native Controls](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) (Controles nativos y clases base del representador).

El siguiente diagrama ilustra la relación entre la [`ViewCell`](xref:Xamarin.Forms.ViewCell) y los controles nativos correspondientes que la implementan:

![](viewcell-images/viewcell-classes.png "Relación entre el Control ViewCell y los controles nativos de implementación")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma creando un representador personalizado para una [`ViewCell`](xref:Xamarin.Forms.ViewCell) en cada plataforma. El proceso para hacer esto es el siguiente:

1. [Cree](#Creating_the_Custom_Cell) una celda personalizada de Xamarin.Forms.
1. [Consuma](#Consuming_the_Custom_Cell) la celda personalizada de Xamarin.Forms.
1. [Cree](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para la celda en cada plataforma.

Cada elemento ahora se explicará a su vez, para implementar un representador `NativeCell` que aproveche las ventajas de un diseño específico de la plataforma para cada celda que se hospeda dentro de un control [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin.Forms. Esto impide que se llame varias veces a los cálculos de diseño de Xamarin.Forms durante el desplazamiento de `ListView`.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Creación de la celda personalizada

Se puede crear un control de celda personalizado mediante la creación de subclases de la clase [`ViewCell`](xref:Xamarin.Forms.ViewCell), como se muestra en el siguiente ejemplo de código:

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```

La clase `NativeCell` se crea en el proyecto de biblioteca de .NET Standard y define la API para la celda personalizada. La celda personalizada expone las propiedades `Name`, `Category` y `ImageFilename` que se pueden mostrar mediante el enlace de datos. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Consumo de la celda personalizada

En XAML puede hacerse referencia a la celda personalizada `NativeCell` en el proyecto de biblioteca de .NET Standard. Para ello, se declara un espacio de nombres para su ubicación y se usa el prefijo del espacio de nombres en el elemento de celda personalizado. El siguiente ejemplo de código muestra cómo la celda personalizada `NativeCell` puede utilizarse en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

El prefijo del espacio de nombres `local` puede tener cualquier nombre. Empero, los valores `clr-namespace` y `assembly` deben coincidir con los detalles del control personalizado. Una vez que se declare el espacio de nombres, el prefijo se utiliza para hacer referencia a la celda personalizada.

El siguiente ejemplo de código muestra cómo una página de C# puede consumir la celda personalizada `NativeCell`:

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Un control [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin.Forms se usa para mostrar una lista de los datos, que se rellena mediante la propiedad [`ItemSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource). La estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) intenta minimizar la velocidad de ejecución y el consumo de memoria de `ListView` mediante el reciclaje de las celdas de la lista. Para obtener más información, vea [Estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Cada fila de la lista contiene tres elementos de datos: un nombre, una categoría y un nombre de archivo de imagen. El diseño de cada fila de la lista está definido por el `DataTemplate` al que se hace referencia mediante la propiedad enlazable [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate). `DataTemplate` define que cada fila de datos en la lista será una `NativeCell` que muestra sus propiedades `Name`, `Category` y `ImageFilename` mediante el enlace de datos. Para obtener más información sobre el control `ListView`, vea [ListView de Xamarin.Forms](~/xamarin-forms/user-interface/listview/index.md).

Ahora se puede agregar un representador personalizado a cada proyecto de aplicación para personalizar el diseño específico de la plataforma para cada celda.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creación del representador personalizado en cada plataforma

El proceso de creación de la clase de representador personalizada es el siguiente:

1. Cree una subclase de la clase `ViewCellRenderer` que represente la celda personalizada.
1. Invalide el método específico de la plataforma que representa la celda personalizada y escriba una lógica para personalizarla.
1. Agregue un atributo `ExportRenderer` a la clase de representador personalizada para especificar que se utilizará para representar la celda personalizada de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no se registra un representador personalizado, se usará el representador predeterminado de la clase base del control. Con todo, los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un elemento [ViewCell](xref:Xamarin.Forms.ViewCell).

El siguiente diagrama ilustra las responsabilidades de cada proyecto en la aplicación de ejemplo, junto con las relaciones entre ellos:

![](viewcell-images/solution-structure.png "Responsabilidades del proyecto de representador personalizado NativeCell")

Las clases del representador específico de la plataforma, que se derivan de la clase `ViewCellRenderer` para cada plataforma, representan la celda personalizada `NativeCell`. Esto da como resultado que cada celda personalizada `NativeCell` se represente con diseño específico de la plataforma, como se muestra en las siguientes capturas de pantalla:

![](viewcell-images/screenshots.png "NativeCell en cada plataforma")

La clase `ViewCellRenderer` expone métodos específicos de la plataforma para representar la celda personalizada. Estos son el método `GetCell` en la plataforma iOS, el método `GetCellCore` en la plataforma Android y el método `GetTemplate` en UWP.

Cada clase de presentador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo de la celda de Xamarin.Forms que se representa y el nombre de tipo del representador personalizado. El prefijo `assembly` para el atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se describe la implementación de cada clase de representador personalizado específico de plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

El siguiente ejemplo de código muestra el representador personalizado para la plataforma de iOS:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

Se llama al método `GetCell` para crear cada celda que se mostrará. Cada celda es una instancia de `NativeiOSCell` que define el diseño de la celda y sus datos. La operación del método `GetCell` depende de la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView):

- Cuando la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView) sea [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), se invocará el método `GetCell` para cada celda. Se creará una instancia de `NativeiOSCell` para cada instancia de `NativeCell` que se muestre inicialmente en la pantalla. Cuando el usuario se desplace a través de la `ListView`, se volverán a usar las instancias de `NativeiOSCell`. Para obtener más información sobre la reutilización de celdas de iOS, vea [Reutilización de celda](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Este código de representador personalizado llevará a cabo cierta reutilización de celda cuando la [`ListView`](xref:Xamarin.Forms.ListView) se establezca para conservar las celdas.

  El método `UpdateCell` actualizará los datos mostrados por cada instancia de `NativeiOSCell`, ya sean recién creados o se vuelvan a utilizar, con los datos de cada instancia de `NativeCell`.

  > [!NOTE]
  > El método `OnNativeCellPropertyChanged` nunca se invocará cuando la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView) se establezca para conservar las celdas.

- Cuando la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView) sea [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), se invocará el método `GetCell` para cada celda que se muestra inicialmente en la pantalla. Se creará una instancia de `NativeiOSCell` para cada instancia de `NativeCell` que se muestre inicialmente en la pantalla. El método `UpdateCell` actualizará los datos mostrados por cada instancia de `NativeiOSCell` con los datos de cada instancia de `NativeCell`. Empero, el método `GetCell` no se invoca cuando el usuario se desplaza por la `ListView`. En su lugar, se reutilizarán las instancias de `NativeiOSCell`. Los eventos de `PropertyChanged` se generarán en la instancia de `NativeCell` cuando cambien sus datos y el controlador de eventos `OnNativeCellPropertyChanged` actualizará los datos en cada instancia de `NativeiOSCell` reutilizada.

El siguiente ejemplo de código muestra el método `OnNativeCellPropertyChanged` que se invoca cuando se provoca un evento `PropertyChanged`:

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Este método actualiza los datos que muestran las instancias de `NativeiOSCell` reutilizadas. Se realiza una comprobación para la propiedad que se ha modificado, ya que el método puede llamarse varias veces.

La clase `NativeiOSCell` define el diseño de cada celda y se muestra en el siguiente ejemplo de código:

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. La clase implementa la interfaz [`INativeElementView`](xref:Xamarin.Forms.INativeElementView), que es necesaria cuando [`ListView`](xref:Xamarin.Forms.ListView) usa estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement). Esta interfaz especifica que la clase debe implementar la propiedad [`Element`](xref:Xamarin.Forms.INativeElementView.Element), que debe devolver los datos de celda personalizada para las celdas recicladas.

El constructor `NativeiOSCell` inicializa la apariencia de las propiedades `HeadingLabel`, `SubheadingLabel` y `CellImageView`. Estas propiedades se utilizan para mostrar los datos almacenados en la instancia `NativeCell`, siendo llamado el método `UpdateCell` para establecer el valor de cada propiedad. Además, cuando [`ListView`](xref:Xamarin.Forms.ListView) usa la estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), el método `OnNativeCellPropertyChanged` puede actualizar los datos mostrados por las propiedades `HeadingLabel`, `SubheadingLabel` y `CellImageView` en el representador personalizado.

El diseño de la celda se realiza mediante la invalidación de `LayoutSubviews`, que establece las coordenadas de `HeadingLabel`, `SubheadingLabel` y `CellImageView` dentro de la celda.

### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

El siguiente ejemplo de código muestra el representador personalizado para la plataforma de Android:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

Se llama al método `GetCellCore` para crear cada celda que se mostrará. Cada celda es una instancia de `NativeAndroidCell` que define el diseño de la celda y sus datos. La operación del método `GetCellCore` depende de la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView):

- Cuando la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView) sea [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), se invocará el método `GetCellCore` para cada celda. Se creará una `NativeAndroidCell` para cada instancia de `NativeCell` que se muestre inicialmente en la pantalla. Cuando el usuario se desplace a través de la `ListView`, se volverán a usar las instancias de `NativeAndroidCell`. Para obtener más información sobre la reutilización de celdas en Android, vea [Reutilización de vista fila](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Tenga en cuenta que este código de representador personalizado llevará a cabo cierta reutilización de celda cuando la [`ListView`](xref:Xamarin.Forms.ListView) se establezca para conservar las celdas.

  El método `UpdateCell` actualizará los datos mostrados por cada instancia de `NativeAndroidCell`, ya sean recién creados o se vuelvan a utilizar, con los datos de cada instancia de `NativeCell`.

  > [!NOTE]
  > Tenga en cuenta que el método `OnNativeCellPropertyChanged` se invocará cuando [`ListView`](xref:Xamarin.Forms.ListView) se configure para conservar las celdas, pero no se actualizarán los valores de propiedad de `NativeAndroidCell`.

- Cuando la estrategia de almacenamiento en caché [`ListView`](xref:Xamarin.Forms.ListView) sea [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), se invocará el método `GetCellCore` para cada celda que se muestra inicialmente en la pantalla. Se creará una instancia de `NativeAndroidCell` para cada instancia de `NativeCell` que se muestre inicialmente en la pantalla. El método `UpdateCell` actualizará los datos mostrados por cada instancia de `NativeAndroidCell` con los datos de cada instancia de `NativeCell`. Empero, el método `GetCellCore` no se invoca cuando el usuario se desplaza por la `ListView`. En su lugar, se reutilizarán las instancias de `NativeAndroidCell`.  Los eventos de `PropertyChanged` se generarán en la instancia de `NativeCell` cuando cambien sus datos y el controlador de eventos `OnNativeCellPropertyChanged` actualizará los datos en cada instancia de `NativeAndroidCell` reutilizada.

El siguiente ejemplo de código muestra el método `OnNativeCellPropertyChanged` que se invoca cuando se provoca un evento `PropertyChanged`:

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Este método actualiza los datos que muestran las instancias de `NativeAndroidCell` reutilizadas. Se realiza una comprobación para la propiedad que se ha modificado, ya que el método puede llamarse varias veces.

La clase `NativeAndroidCell` define el diseño de cada celda y se muestra en el siguiente ejemplo de código:

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. La clase implementa la interfaz [`INativeElementView`](xref:Xamarin.Forms.INativeElementView), que es necesaria cuando [`ListView`](xref:Xamarin.Forms.ListView) usa estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement). Esta interfaz especifica que la clase debe implementar la propiedad [`Element`](xref:Xamarin.Forms.INativeElementView.Element), que debe devolver los datos de celda personalizada para las celdas recicladas.

El constructor `NativeAndroidCell` aumenta el diseño de `NativeAndroidCell` e inicializa las propiedades `HeadingTextView`, `SubheadingTextView` y `ImageView` a los controles en el diseño aumentado. Estas propiedades se utilizan para mostrar los datos almacenados en la instancia `NativeCell`, siendo llamado el método `UpdateCell` para establecer el valor de cada propiedad. Además, cuando la [`ListView`](xref:Xamarin.Forms.ListView) usa la estrategia de almacenamiento en caché [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), el método `OnNativeCellPropertyChanged` puede actualizar los datos mostrados por las propiedades `HeadingTextView`, `SubheadingTextView` y `ImageView` en el representador personalizado.

El siguiente ejemplo de código muestra la definición de diseño para el archivo de diseño de `NativeAndroidCell.axml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Este diseño especifica que dos controles de `TextView` y un control de `ImageView` se usan para mostrar el contenido de la celda. Los dos controles de `TextView` están orientados verticalmente dentro de un control de `LinearLayout`, con todos los controles contenidos en un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creación del representador personalizado en UWP

El siguiente ejemplo de código muestra el representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

Se llama al método `GetTemplate` para devolver la celda que se va a representar para cada fila de datos en la lista. Crea un `DataTemplate` para cada instancia de `NativeCell` que se mostrará en la pantalla, con el `DataTemplate` definiendo la apariencia y el contenido de la celda.

`DataTemplate` se almacena en el diccionario de recursos de nivel de aplicación y se muestra en el siguiente ejemplo de código:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

`DataTemplate` especifica los controles utilizados para mostrar el contenido de la celda y su diseño y apariencia. Dos controles de `TextBlock` y un control de `Image` se usan para mostrar el contenido de la celda mediante el enlace de datos. Además, una instancia de `ConcatImageExtensionConverter` se utiliza para concatenar la extensión de archivo `.jpg` para cada nombre de archivo de imagen. Esto garantiza que el control `Image` puede cargar y representar la imagen cuando se establece su propiedad `Source`.

## <a name="summary"></a>Resumen

En este artículo se mostró cómo crear un representador personalizado para un [`ViewCell`](xref:Xamarin.Forms.ViewCell) que se hospeda dentro de un control [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin.Forms. Esto impide que se llame varias veces a los cálculos de diseño de Xamarin.Forms durante el desplazamiento de `ListView`.


## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell) (CustomRendererViewCell [ejemplo])
