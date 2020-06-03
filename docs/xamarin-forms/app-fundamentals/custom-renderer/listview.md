---
title: ''
description: Una ListView de Xamarin.Forms es una vista que muestra una colección de datos como una lista vertical. En este artículo se muestra cómo crear un representador personalizado que encapsula los controles de lista específica de la plataforma y los diseños de celda nativa, lo que permite tener más control sobre el rendimiento del control de lista nativa.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3403948c2853289610a73bb36073f09c0c86137d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135309"
---
# <a name="customizing-a-listview"></a>Personalización de una ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)

_Una ListView de Xamarin.Forms es una vista que muestra una colección de datos como una lista vertical. En este artículo se muestra cómo crear un representador personalizado que encapsula los controles de lista específica de la plataforma y los diseños de celda nativa, lo que permite tener más control sobre el rendimiento del control de lista nativa._

Todas las vistas de Xamarin.Forms tienen un representador que las acompaña para cada plataforma y que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa un [`ListView`](xref:Xamarin.Forms.ListView), se crea en iOS la instancia de la clase `ListViewRenderer`, que a su vez crea una instancia del control `UITableView` nativo. En la plataforma de Android, la clase `ListViewRenderer` crea una instancia de un control `ListView` nativo. En Plataforma universal de Windows (UWP), la clase `ListViewRenderer` crea una instancia de un control `ListView` nativo. Para obtener más información sobre el representador y las clases de control nativo a las que se asignan los controles de Xamarin.Forms, vea [Clases base y controles nativos del representador](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

El siguiente diagrama muestra la relación entre el control [`ListView`](xref:Xamarin.Forms.ListView) y los controles nativos correspondientes que lo implementan:

![](listview-images/listview-classes.png "Relationship Between the ListView Control and the Implementing Native Controls")

El proceso de representación puede aprovecharse para implementar las personalizaciones específicas de la plataforma creando un representador personalizado para una [`ListView`](xref:Xamarin.Forms.ListView) en cada plataforma. Para hacerlo, siga este procedimiento:

1. [Cree](#Creating_the_Custom_ListView_Control) un control personalizado de Xamarin.Forms.
1. [Consuma](#Consuming_the_Custom_Control) el control personalizado de Xamarin.Forms.
1. [Cree](#Creating_the_Custom_Renderer_on_each_Platform) el representador personalizado para el control en cada plataforma.

Ahora se analizará en detalle cada elemento, para implementar un representador `NativeListView` que aproveche las ventajas de los diseños de celda nativos y los controles de lista específicos de la plataforma. Este escenario es útil al migrar una aplicación nativa existente que contiene la lista y el código de la celda que se puede volver a usar. Además, permite la personalización detallada de las características de control de lista que pueden afectar al rendimiento, como la virtualización de datos.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Crear el control ListView personalizado

Se puede crear un control personalizado [`ListView`](xref:Xamarin.Forms.ListView) mediante la creación de subclases de la clase `ListView`, como se muestra en el siguiente ejemplo de código:

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

El `NativeListView` se crea en el proyecto de biblioteca de .NET Standard y define la API para el control personalizado. Este control expone una propiedad `Items` que se usa para rellenar el `ListView` con los datos y que puede enlazarse a datos para fines de presentación. También expone un evento `ItemSelected` que se desencadena cada vez que se selecciona un elemento en un control de lista nativo específico de la plataforma. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Uso del control personalizado

En XAML puede hacerse referencia al control personalizado `NativeListView` en el proyecto de biblioteca de .NET Standard declarando un espacio de nombres para su ubicación y usando el prefijo del espacio de nombres en el control. El siguiente ejemplo de código muestra cómo se puede usar el control personalizado `NativeListView` en una página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*" />
            </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
            <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
          </Grid>
      </ContentPage.Content>
</ContentPage>
```

El prefijo de espacio de nombres `local` puede tener cualquier nombre. Pero los valores `clr-namespace` y `assembly` tienen que coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se usa para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo se puede usar el control personalizado `NativeListView` en una página C#:

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

El control personalizado `NativeListView` utiliza los representadores personalizados específicos de la plataforma para mostrar una lista de datos que se rellena mediante la propiedad `Items`. Cada fila de la lista contiene tres elementos de datos, un nombre, una categoría y un nombre de archivo de imagen. El diseño de cada fila de la lista se define mediante el representador personalizado específico de la plataforma.

> [!NOTE]
> Dado que el control personalizado `NativeListView` se representa mediante controles de lista específicos de la plataforma que incluyen capacidad de desplazamiento, el control personalizado no debe hospedarse en los controles de diseño desplazable, como [`ScrollView`](xref:Xamarin.Forms.ScrollView).

Ahora se puede agregar un representador personalizado a cada proyecto de aplicación para crear controles de lista específicos de la plataforma y diseños de celda nativos.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creación del representador personalizado en cada plataforma

El proceso para crear la clase del representador personalizado es el siguiente:

1. Cree una subclase de la clase `ListViewRenderer` que representa el control personalizado.
1. Invalide el método `OnElementChanged` que representa el control personalizado y escriba lógica para personalizarlo. Se llama a este método cuando se crea el correspondiente [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin.Forms.
1. Agregue un atributo `ExportRenderer` a la clase de representador personalizada para especificar que se usará para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no hay un representador personalizado registrado, se usa el representador predeterminado de la clase base de la celda.

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](listview-images/solution-structure.png "NativeListView Custom Renderer Project Responsibilities")

El control personalizado `NativeListView` se representa mediante clases de representador específicas de la plataforma, que se derivan de la clase `ListViewRenderer` de cada plataforma. Esto da lugar a que cada control personalizado `NativeListView` se represente con diseños de celda nativos y controles de lista específicos de la plataforma, como se muestra en las siguientes capturas de pantalla:

![](listview-images/screenshots.png "NativeListView on each Platform")

La clase `ListViewRenderer` expone el método `OnElementChanged`, al que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control nativo correspondiente. Este método toma un parámetro `ElementChangedEventArgs` que contiene propiedades `OldElement` y `NewElement`. Estas propiedades representan al elemento de Xamarin.Forms al que *estaba* asociado el representador y al elemento de Xamarin.Forms al que *está* asociado el representador, respectivamente. En la aplicación de ejemplo, la propiedad `OldElement` es `null` y la propiedad `NewElement` contiene una referencia a la instancia de `NativeListView`.

El lugar para realizar la personalización de controles nativos es una versión reemplazada del método `OnElementChanged` en cada clase de representador específica de la plataforma. Una referencia con tipo para el control nativo que se usa en la plataforma puede obtenerse a través de la propiedad `Control`. Además, mediante la propiedad Xamarin.Forms se puede obtener una referencia al control de `Element` que se representa.

Debe tener cuidado al suscribirse a los controladores de eventos en el método `OnElementChanged`, como se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Solo se debe configurar el control nativo y suscribir a los controladores de eventos cuando se adjunta el representador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, solo se debe cancelar la suscripción de los controladores de eventos que se han suscrito cuando cambia el elemento al que está asociado el representador. Adoptar este enfoque facilita crear un representador personalizado que no sufra pérdidas de memoria.

Una versión invalidada del método `OnElementPropertyChanged`, en cada clase de representador específico de la plataforma, es el lugar para responder a los cambios de propiedad enlazable en el control personalizado de Xamarin.Forms. Siempre se debe realizar una comprobación de la propiedad que ha modificado, ya que esta invalidación se puede llamar varias veces.

Cada clase de representador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se representa y el nombre de tipo del representador personalizado. El prefijo `assembly` para el atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se describe la implementación de cada clase de representador personalizado específico de plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

El siguiente ejemplo de código muestra el representador personalizado para la plataforma iOS:

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

El control `UITableView` se configura mediante la creación de una instancia de la clase `NativeiOSListViewSource`, siempre que se adjunte el representador personalizado a un nuevo elemento de Xamarin.Forms. Esta clase proporciona datos para el control `UITableView` invalidando los métodos `RowsInSection` y `GetCell` desde la clase `UITableViewSource` y exponiendo una propiedad `Items` que contiene la lista de datos que se mostrarán. La clase también proporciona una invalidación del método `RowSelected` que invoca el evento `ItemSelected` proporcionado por el control personalizado `NativeListView`. Para obtener más información sobre las invalidaciones de método, vea [Subclassing UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Creación de subclases de UITableViewSource). El método `GetCell` devuelve un `UITableCellView` que se rellena con datos para cada fila de la lista y se muestra en el siguiente ejemplo de código:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Este método crea una instancia de `NativeiOSListViewCell` para cada fila de datos que se mostrará en la pantalla. La instancia de `NativeiOSCell` define el diseño de cada celda y los datos de la celda. Cuando una celda desaparezca de la pantalla debido al desplazamiento, la celda estará disponible para su reutilización. Esto evita desperdiciar memoria garantizando que solo hay instancias de `NativeiOSCell` para los datos que se muestran en la pantalla, en lugar de todos los datos en la lista. Para obtener más información sobre la reutilización de celdas, vea [Cell Reuse](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Reutilización de celdas). El método `GetCell` también lee la propiedad `ImageFilename` de cada fila de datos, siempre que exista, y lee la imagen y la almacena como una instancia de `UIImage` antes de actualizar la instancia de `NativeiOSListViewCell` con los datos (nombre, categoría e imagen) de la fila.

La clase `NativeiOSListViewCell` define el diseño de cada celda y se muestra en el siguiente ejemplo de código:

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Esta clase define los controles utilizados para representar el contenido de la celda y su diseño. El constructor `NativeiOSListViewCell` crea instancias de controles de `UILabel` y `UIImageView` e inicializa su apariencia. Estos controles se usan para mostrar datos de cada fila, y el método `UpdateCell` se usa para establecer estos datos en las instancias de `UILabel` y `UIImageView`. El método `LayoutSubviews` invalidado establece la ubicación de estas instancias especificando sus coordenadas dentro de la celda.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el control personalizado

Si la propiedad `NativeListView.Items` cambia debido a elementos que se agregan o se quitan de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse invalidando el método `OnElementPropertyChanged`, que se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

El método crea una nueva instancia de la clase `NativeiOSListViewSource` que proporciona datos para el control `UITableView`, siempre que la propiedad enlazable `NativeListView.Items` haya cambiado.

### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

En el ejemplo de código siguiente se muestra el representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

El control nativo `ListView` se configura siempre y cuando el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms. Esta configuración implica la creación de una instancia de la clase `NativeAndroidListViewAdapter` que proporciona datos al control `ListView` nativo y el registro de un controlador de eventos para procesar el evento `ItemClick`. A su vez, este controlador invocará el evento `ItemSelected` proporcionado por el control personalizado `NativeListView`. Se cancela la suscripción del evento `ItemClick` solo si cambia el representador al que está adjunto el elemento de Xamarin.Forms.

El `NativeAndroidListViewAdapter` deriva de la clase `BaseAdapter` y expone una propiedad `Items` que contiene la lista de datos que se mostrarán, además de invalidar los métodos `Count`, `GetView`, `GetItemId` y `this[int]`. Para obtener más información sobre estas invalidaciones de método, vea [Implementing a ListAdapter](~/android/user-interface/layouts/list-view/populating.md) (Implementación de un ListAdapter). El método `GetView` devuelve una vista para cada fila, que se rellena con datos y se muestra en el siguiente ejemplo de código:

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

Se llama al método `GetView` para devolver la celda que se va a representar, como una `View`, para cada fila de datos en la lista. Crea una instancia de `View` para cada fila de datos que se mostrará en la pantalla, con la apariencia de la instancia de `View` que se define en un archivo de diseño. Cuando una celda desaparezca de la pantalla debido al desplazamiento, la celda estará disponible para su reutilización. Esto evita desperdiciar memoria garantizando que solo hay instancias de `View` para los datos que se muestran en la pantalla, en lugar de todos los datos en la lista. Para obtener más información sobre la reutilización de vistas, vea [Row View Re-use](~/android/user-interface/layouts/list-view/populating.md) (Reutilización de vistas de fila).

El método `GetView` también rellena la instancia `View` con datos, incluyendo la lectura de los datos de imagen del nombre de archivo especificado en la propiedad `ImageFilename`.

El diseño de cada celda mostrada por el `ListView` nativo se define en el archivo de diseño `NativeAndroidListViewCell.axml`, que aumenta el método `LayoutInflater.Inflate`. En el siguiente ejemplo de código se muestra la definición del diseño:

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el control personalizado

Si la propiedad `NativeListView.Items` cambia debido a elementos que se agregan o se quitan de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse invalidando el método `OnElementPropertyChanged`, que se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

El método crea una nueva instancia de la clase `NativeAndroidListViewAdapter` que proporciona datos para el control `ListView` nativo, siempre que el la propiedad enlazable `NativeListView.Items` haya cambiado.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creación del representador personalizado en UWP

En el siguiente ejemplo de código se muestra el representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

El control nativo `ListView` se configura siempre y cuando el representador personalizado esté asociado a un nuevo elemento de Xamarin.Forms. Esta configuración implica configurar el modo en que el control `ListView` nativo responderá a los elementos seleccionados, rellenar los datos mostrados por el control, definir la apariencia y el contenido de cada celda y registrar un controlador de eventos para procesar el evento `SelectionChanged`. A su vez, este controlador invocará el evento `ItemSelected` proporcionado por el control personalizado `NativeListView`. Se cancela la suscripción del evento `SelectionChanged` solo si cambia el representador al que está adjunto el elemento de Xamarin.Forms.

La apariencia y el contenido de cada celda `ListView` nativa se definen mediante un `DataTemplate` denominado `ListViewItemTemplate`. Este `DataTemplate` se almacena en el diccionario de recursos de nivel de aplicación y se muestra en el siguiente ejemplo de código:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

El `DataTemplate` especifica los controles utilizados para mostrar el contenido de la celda y su diseño y apariencia. Dos controles de `TextBlock` y un control de `Image` se usan para mostrar el contenido de la celda mediante el enlace de datos. Además, una instancia de `ConcatImageExtensionConverter` se utiliza para concatenar la extensión de archivo `.jpg` para cada nombre de archivo de imagen. Esto garantiza que el control `Image` puede cargar y representar la imagen cuando se establece su propiedad `Source`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Responde a un cambio de propiedad en el control personalizado

Si la propiedad `NativeListView.Items` cambia debido a elementos que se agregan o se quitan de la lista, el representador personalizado debe responder mostrando los cambios. Esto puede realizarse invalidando el método `OnElementPropertyChanged`, que se muestra en el siguiente ejemplo de código:

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

El método rellena el control `ListView` nativo con los datos modificados, siempre que la propiedad `NativeListView.Items` enlazable haya cambiado.

## <a name="summary"></a>Resumen

En este artículo se mostró cómo crear un representador personalizado que encapsula los controles de lista específica de la plataforma y los diseños de celda nativa, lo que permite tener más control sobre el rendimiento del control de lista nativa.

## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererListView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview) (CustomRendererListView [ejemplo])
