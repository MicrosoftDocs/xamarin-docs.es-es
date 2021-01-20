---
title: Usar Xamarin. Android Designer
description: Este artículo es un tutorial de Xamarin. Android Designer. Muestra cómo crear una interfaz de usuario para una aplicación de explorador de color pequeña; Esta interfaz de usuario se crea completamente en el diseñador.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 1eed9691684bffbc9367c9b411a83876a52cf72f
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609772"
---
# <a name="using-the-xamarinandroid-designer"></a>Usar Xamarin. Android Designer

_Este artículo es un tutorial de Xamarin. Android Designer. Muestra cómo crear una interfaz de usuario para una aplicación de explorador de color pequeña; Esta interfaz de usuario se crea completamente en el diseñador._

## <a name="overview"></a>Información general

Las interfaces de usuario de Android se pueden crear mediante declaración usando archivos XML o mediante programación escribiendo código. Xamarin. Android Designer permite a los desarrolladores crear y modificar visualmente diseños declarativos, sin necesidad de modificar manualmente los archivos XML. El diseñador también proporciona comentarios en tiempo real que permiten al desarrollador evaluar los cambios de la interfaz de usuario sin tener que volver a implementar la aplicación en un dispositivo o en un emulador. Estas características del diseñador pueden acelerar enormemente el desarrollo de la interfaz de usuario de Android.
En este artículo se muestra cómo usar Xamarin. Android Designer para crear visualmente una interfaz de usuario.

> [!TIP]
> Las versiones más recientes de Visual Studio admiten la apertura de archivos .xml dentro de Android Designer.
>
> Android Designer admite tanto archivos .axml como .xml.

## <a name="walkthrough"></a>Tutorial

El objetivo de este tutorial es usar el Android Designer para crear una interfaz de usuario para una aplicación de explorador de color de ejemplo. La aplicación color Browser presenta una lista de colores, sus nombres y sus valores RGB. Aprenderá a agregar widgets al **superficie de diseño** , así como a diseñar estos widgets visualmente. Después, aprenderá a modificar widgets de forma interactiva en el **superficie de diseño** o mediante el panel de **propiedades** del diseñador. Por último, verá cómo se ve el diseño cuando la aplicación se ejecuta en un dispositivo o emulador.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Creación de un nuevo proyecto

El primer paso es crear un nuevo proyecto de Xamarin. Android. Inicie Visual Studio, haga clic en **nuevo proyecto...** y elija la plantilla **Visual C \# > Android > Android App (Xamarin)** .
Asigne a la nueva aplicación el nombre **DesignerWalkthrough** y haga clic en **Aceptar**.

[![Aplicación en blanco para Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

En el cuadro de diálogo **nueva aplicación de Android** , elija **aplicación vacía** y haga clic en **Aceptar**:

[![Selección de la plantilla aplicación en blanco de Android](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)

### <a name="adding-a-layout"></a>Agregar un diseño

El siguiente paso consiste en crear un **LinearLayout** que contendrá los elementos de la interfaz de usuario. Haga clic con el botón derecho en **recursos/diseño** en el **Explorador de soluciones** y seleccione **Agregar > nuevo elemento.**... En el cuadro de diálogo **Agregar nuevo elemento** , seleccione **diseño de Android**. Asigne un nombre al archivo **list_item** y haga clic en **Agregar**:

[![Nuevo diseño](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

El nuevo diseño de **list_item** se muestra en el diseñador. Observe que se muestran dos paneles &ndash; en el *Superficie de diseño* del **list_item** está visible en el panel izquierdo, mientras que su origen XML se muestra en el panel derecho. Puede intercambiar las posiciones de los paneles de **superficie de diseño** y de **origen** haciendo clic en el icono **intercambiar paneles** que se encuentra entre los dos paneles:

[![Vista de diseñador](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

En el menú **Ver** , haga clic en **otras ventanas > esquema del documento** para abrir el esquema del **documento**. El **esquema del documento** muestra que el diseño contiene actualmente un solo widget **LinearLayout** :

[![Esquema del documento](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

El siguiente paso consiste en crear la interfaz de usuario para la aplicación de explorador de color dentro de este `LinearLayout` .

### <a name="creating-the-list-item-user-interface"></a>Crear la interfaz de usuario de elemento de lista

Si el panel **cuadro de herramientas** no está visible, haga clic en la pestaña cuadro de **herramientas** de la izquierda. En el **cuadro de herramientas**, desplácese hacia abajo hasta la sección **imágenes & medios** y desplácese hacia abajo hasta encontrar un `ImageView` :

[![Buscar ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Como alternativa, puede escribir *ImageView* en la barra de búsqueda para buscar `ImageView` :

[![Búsqueda de ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Arrástrelo `ImageView` hasta el superficie de diseño (se `ImageView` usará para mostrar una muestra de color en la aplicación del explorador de color):

[![ImageView en canvas](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

A continuación, arrastre un `LinearLayout (Vertical)` Widget desde el **cuadro de herramientas** al diseñador. Observe que un contorno azul indica los límites del agregado `LinearLayout` . El **esquema del documento** muestra que es un elemento secundario de, que se `LinearLayout` encuentra en `imageView1 (ImageView)` :

[![Contorno azul](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Al seleccionar `ImageView` en el diseñador, el contorno azul se desplaza para rodear el `ImageView` . Además, la selección se mueve a `imageView1 (ImageView)` en el **esquema del documento**:

[![Seleccionar ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

A continuación, arrastre un `Text (Large)` Widget desde el **cuadro de herramientas** hasta el recién agregado `LinearLayout` . Observe que el diseñador usa resaltados verdes para indicar dónde se insertará el nuevo widget:

[![Iluminaciones verdes](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Después, agregue un `Text (Small)` Widget debajo del `Text (Large)` Widget:

[![Agregar widget de texto pequeño](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

En este punto, la superficie del diseñador debe ser similar a la captura de pantalla siguiente:

[![Captura de pantalla muestra la superficie del diseñador con el cuadro de herramientas, el esquema del documento y el área de diseño con texto pequeño seleccionado.](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Si los dos `textView` widgets no están dentro `linearLayout1` , puede arrastrarlos a `linearLayout1` en el **esquema del documento** y colocarlos de modo que aparezcan como se muestra en la captura de pantalla anterior (con sangría debajo `linearLayout1` ).

### <a name="arranging-the-user-interface"></a>Organizar la interfaz de usuario

El siguiente paso consiste en modificar la interfaz de usuario para mostrar la a la `ImageView` izquierda, con los dos `TextView` widgets apilados a la derecha de `ImageView` .

1. Seleccione `ImageView`.

2. En el **ventana Propiedades**, escriba *ancho* en el cuadro de búsqueda y busque el **ancho del diseño**.

3. Cambie la configuración de **ancho de diseño** a `wrap_content` :

![Establecer ajustar contenido](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Otra manera de cambiar la `Width` configuración es hacer clic en el triángulo del lado derecho del widget para alternar el valor de ancho a `wrap_content` :

![Arrastre para establecer el ancho](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Al hacer clic en el triángulo de nuevo, se devuelve el `Width` valor a `match_parent` . A continuación, vaya al panel **esquema del documento** y seleccione la raíz `LinearLayout` :

[![Seleccionar LinearLayout raíz](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Con la raíz `LinearLayout` seleccionada, vuelva al panel de **propiedades** , escriba *orientación* en el cuadro de búsqueda y busque la configuración de **orientación** . Cambie la **orientación** a `horizontal` :

![Seleccionar orientación horizontal](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

En este punto, la superficie del diseñador debe ser similar a la captura de pantalla siguiente.
Observe que los `TextView` widgets se han colocado a la derecha de `ImageView` :

[![Captura de pantalla muestra la superficie del diseñador con el cuadro de herramientas, el esquema del documento y el área de diseño.](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificar el espaciado

El siguiente paso consiste en modificar la configuración del relleno y los márgenes en la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione `ImageView` en la superficie de diseño. En el panel **propiedades** , escriba `min` en el cuadro de búsqueda. Escriba `70dp` para **altura mínima** y `50dp` para **ancho mínimo**:

[![Establecimiento del alto y ancho](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

En el panel **propiedades** , escriba `padding` en el cuadro de búsqueda y escriba `10dp` para **relleno**. Estos `minHeight` `minWidth` valores y la `padding` configuración agregan relleno alrededor de todos los lados de `ImageView` y lo alargan verticalmente. Observe que el esquema XML cambia a medida que se escriben estos valores:

[![Establecer relleno](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

La configuración de los márgenes inferior, izquierdo, derecho y superior se puede establecer de forma independiente mediante la especificación de valores en los campos de **relleno hacia abajo**, **relleno izquierdo**, **relleno derecho** y **relleno superior** , respectivamente.
Por ejemplo, establezca el campo **relleno izquierdo** en `5dp` y los campos **parte inferior**, **relleno derecho** y **relleno superior** para `10dp` :

[![Configuración de relleno personalizada](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

A continuación, ajuste la posición del `LinearLayout` widget que contiene los dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1` . En la ventana **propiedades** , escriba `margin` en el cuadro de búsqueda. Establezca el margen **inferior del diseño**, el margen de diseño a la **izquierda** y el **margen de diseño arriba** en `5dp` . Establezca **margen de diseño** a la derecha en `0dp` :

[![Establecer márgenes](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Quitar la imagen predeterminada

Dado que `ImageView` se usa para mostrar los colores (en lugar de las imágenes), el siguiente paso es quitar el origen de imagen predeterminado agregado por la plantilla.

1. Seleccione `ImageView` en la **superficie del diseñador**.

2. En **propiedades**, escriba *src* en el cuadro de búsqueda.

3. Haga clic en el cuadrado pequeño situado a la derecha del valor de la propiedad **src** y seleccione **restablecer**:

[![Borrar el valor src de ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Esto quita `android:src="@android:drawable/ic_menu_gallery"` del XML de origen para eso `ImageView` .

### <a name="adding-a-listview-container"></a>Agregar un contenedor de ListView

Ahora que se ha definido el diseño de **list_item** , el paso siguiente consiste en agregar un `ListView` al diseño principal. Contendrá `ListView` una lista de **list_item**. 

En el **Explorador de soluciones**, Abra **Resources/layout/activity_main. axml**. En el **cuadro de herramientas**, busque el `ListView` widget y arrástrelo hasta el **superficie de diseño**. `ListView`En el diseñador estará en blanco excepto las líneas azules que conforman el borde cuando está seleccionado. Puede ver el **esquema del documento** para comprobar que **ListView** se ha agregado correctamente:

[![Nuevo ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

De forma predeterminada, `ListView` se proporciona el `Id` valor `@+id/listView1` .
Mientras `listView1` aún está seleccionado en el **esquema del documento**, abra el panel de **propiedades** , haga clic en **organizar por** y seleccione **categoría**.
Abra **Main**, busque la propiedad **ID** y cambie su valor a `@+id/myListView` :

[![Cambiar el nombre de ID a mi ListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

En este momento, la interfaz de usuario está lista para usarse.

### <a name="running-the-application"></a>Ejecución de la aplicación

Abra **MainActivity.CS** y reemplace su código por lo siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Este código usa un `ListView` adaptador personalizado para cargar información de color y para mostrar estos datos en la interfaz de usuario que se acaba de crear. Para que este ejemplo sea breve, la información de color está codificada de forma rígida en una lista, pero el adaptador se podría modificar para extraer la información de color de un origen de datos o para calcularla sobre la marcha. Para obtener más información acerca de los `ListView` adaptadores, vea [ListView](~/android/user-interface/layouts/list-view/index.md).

Compile y ejecute la aplicación. La captura de pantalla siguiente es un ejemplo de cómo aparece la aplicación cuando se ejecuta en un dispositivo:

[![Captura de pantalla final](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Creación de un nuevo proyecto

El primer paso es crear un nuevo proyecto de Xamarin. Android.

Inicie Visual Studio para Mac y haga clic en **nuevo proyecto.**... Seleccione la plantilla **aplicación de Android** y haga clic en **siguiente**:

[![Aplicación en blanco para Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Asigne a la nueva aplicación el nombre **DesignerWalkthrough**. En **plataformas de destino**, seleccione **más reciente y más** y haga clic en **siguiente**:

[![Aplicación de nombre](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

En la siguiente pantalla del cuadro de diálogo, haga clic en **crear**.

### <a name="adding-a-layout"></a>Agregar un diseño

El siguiente paso consiste en crear un **LinearLayout** que contendrá los elementos de la interfaz de usuario.

En Visual Studio para Mac, haga clic con el botón derecho en **recursos/diseño** en el panel de **solución** y seleccione **Agregar > nuevo archivo..**.. En el cuadro de diálogo **nuevo archivo** , seleccione **Android > layout**. Asigne un nombre al archivo **list_item** y haga clic en **nuevo**:

[![Nuevo diseño](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Una vez agregado este archivo, el nuevo diseño de **list_item** se muestra en la **superficie de diseño** (si ve el mensaje, *este proyecto contiene recursos que no se compilaron correctamente, la representación podría verse afectada*, haga clic en **compilar >** compilar todo para compilar el proyecto):

[![Vista de diseñador](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Haga clic en la pestaña **origen** en la parte inferior del diseñador para ver el origen XML de este diseño. Al hacer clic en la pestaña **esquema del documento** de la derecha, se muestra que el diseño contiene actualmente un solo widget **LinearLayout** :

[![XML del diseñador](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

El siguiente paso consiste en crear la interfaz de usuario para la aplicación de explorador de color.

### <a name="creating-the-list-item-user-interface"></a>Crear la interfaz de usuario de elemento de lista

Haga clic en la pestaña **Diseñador** en la parte inferior de la pantalla para volver a la **superficie del diseñador**. En el panel **cuadro de herramientas** de la derecha, desplácese hacia abajo hasta la sección **imágenes & medios** y busque `ImageView` :

[![Buscar ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Como alternativa, puede escribir *ImageView* en la barra de búsqueda para buscar `ImageView` :

[![Búsqueda de ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Arrástrelo `ImageView` hasta el **superficie de diseño** (se `ImageView` usará para mostrar una muestra de color en la aplicación del explorador de color):

[![ImageView en canvas](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

A continuación, arrastre un `LinearLayout (Vertical)` Widget desde el **cuadro de herramientas** hasta el **superficie de diseño**. Observe que un contorno azul indica los límites del agregado `LinearLayout` . El **esquema del documento** muestra que es un elemento secundario de, que se `LinearLayout` encuentra a continuación `imageView1 (ImageView)` :

[![Contorno azul](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Al seleccionar `ImageView` en el diseñador, el contorno azul se desplaza para rodear el `ImageView` . Además, la selección se mueve a `imageView1 (ImageView)` en el **esquema del documento**:

[![Seleccionar ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

A continuación, arrastre un `Text (Large)` Widget desde el **cuadro de herramientas** hasta el recién agregado `LinearLayout` . Tenga en cuenta que al arrastrar el mouse hasta el **superficie de diseño**, se resalta dónde se insertará el nuevo widget.
El `Text (Large)` Widget debe estar ubicado en `linearLayout1` , tal y como se muestra aquí:

[![Agregar widget de texto grande](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Después, agregue un `Text (Small)` Widget debajo del `Text (Large)` Widget. En este momento, el **superficie de diseño** debe parecerse a la siguiente captura de pantalla:

[![Agregar widget de texto pequeño](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Si los dos `textView` widgets no están dentro `linearLayout1` , puede arrastrarlos a `linearLayout1` en el **esquema del documento** y colocarlos de modo que aparezcan como se muestra en la captura de pantalla anterior (con sangría debajo `linearLayout1` ).

### <a name="arranging-the-user-interface"></a>Organizar la interfaz de usuario

El siguiente paso consiste en modificar la interfaz de usuario para mostrar la a la `ImageView` izquierda, con los dos `TextView` widgets apilados a la derecha de `ImageView` .

1. Con el `ImageView` seleccionado, haga clic en la pestaña **propiedades** .

2. Justo debajo de la pestaña **propiedades** , haga clic en **diseño**.

3. Desplácese hacia abajo hasta **vista** y cambie la `Width` configuración a  `wrap_content` :

[![Establecer ajustar contenido](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Otra manera de cambiar la `Width` configuración es hacer clic en el triángulo del lado derecho del widget para alternar el valor de ancho a `wrap_content` :

[![Arrastre para establecer el ancho](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Al hacer clic en el triángulo de nuevo, se devuelve el `Width` valor a `match_parent` . A continuación, vaya al panel **esquema del documento** y seleccione la raíz `LinearLayout` :

[![Seleccionar LinearLayout raíz](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Con la raíz `LinearLayout` seleccionada, vuelva a la pestaña **propiedades** y haga clic en **Widget**. Cambie la `Orientation` configuración a `horizontal` como se muestra a continuación. En este momento, el **superficie de diseño** debe ser similar a la captura de pantalla siguiente. Observe que los `TextView` widgets se han colocado a la derecha de `ImageView` :

[![Seleccionar orientación horizontal](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)

### <a name="modifying-the-spacing"></a>Modificar el espaciado

El siguiente paso consiste en modificar la configuración del relleno y los márgenes en la interfaz de usuario para proporcionar más espacio entre los widgets. Seleccione el `ImageView` y haga clic en la pestaña **diseño** en **propiedades**. Cambie `Min Width` a `50dp` , `Min Height` a `70dp` y `Padding` a `10dp` .
Esto aplica el relleno alrededor de todos los lados de `ImageView` y lo alarga verticalmente:

[![Establecer relleno](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

La configuración de relleno superior, derecho, inferior y izquierdo se puede establecer de forma independiente especificando valores en los `Top` campos de relleno,, `Right` `Bottom` y `Left` , respectivamente. Por ejemplo, establezca el `Left` valor de relleno en `5dp` y `Top` los `Right` valores de relleno, y `Bottom` en `10dp` . Tenga en cuenta que la `Padding` configuración cambia a una lista separada por comas de estos valores:

[![Configuración de relleno personalizada](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

A continuación, ajuste la posición del `LinearLayout` widget que contiene los dos `TextView` widgets. En el **esquema del documento**, seleccione `linearLayout1` . En el panel **propiedades** , seleccione la pestaña **diseño** . Desplácese hacia abajo hasta la sección **vista** y establezca los `Left` `Top` márgenes,, y `Right` `Bottom` en `5dp` , `5dp` , `0dp` y, `5dp` respectivamente:

[![Establecer márgenes](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Quitar la imagen predeterminada

Dado que `ImageView` se usa para mostrar los colores (en lugar de las imágenes), el siguiente paso es quitar el origen de imagen predeterminado agregado por la plantilla.

1. Seleccione `ImageView`.

2. Haga clic en la pestaña **Widget** en **propiedades**.

3. Desactive la `Src` opción para que esté en blanco:

[![Borrar el valor src de ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Esto quita `android:src="@android:drawable/ic_menu_gallery"` del XML de origen para eso `ImageView` .

### <a name="adding-a-listview-container"></a>Agregar un contenedor de ListView

Ahora que se ha definido el diseño de **list_item** , el paso siguiente consiste en agregar un `ListView` al diseño principal. Contendrá `ListView` una lista de **list_item**. 

En el **Explorador de soluciones**, Abra **Resources/layout/main. axml**.
Haga clic en el `Button` Widget (si existe) y elimínelo. En el **cuadro de herramientas**, busque el `ListView` widget y arrástrelo hasta el **superficie de diseño**.
`ListView`En el diseñador estará en blanco excepto las líneas azules que conforman el borde cuando está seleccionado. Puede ver el **esquema del documento** para comprobar que **ListView** se ha agregado correctamente:

[![Nuevo ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

De forma predeterminada, `ListView` se proporciona el `Id` valor `@+id/listView1` .
Mientras `listView1` aún está seleccionado en el **esquema del documento**, abra el panel de **propiedades** , haga clic en **organizar por** y seleccione **categoría**.
Abra **Main**, busque la propiedad **ID** y cambie su valor a `@+id/myListView` :

[![Cambiar el nombre de ID a mi ListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

En este momento, la interfaz de usuario está lista para usarse.

### <a name="running-the-application"></a>Ejecución de la aplicación

Abra **MainActivity.CS** y reemplace su código por lo siguiente:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

Este código usa un `ListView` adaptador personalizado para cargar información de color y para mostrar estos datos en la interfaz de usuario que se acaba de crear. Para que este ejemplo sea breve, la información de color está codificada de forma rígida en una lista, pero el adaptador se podría modificar para extraer la información de color de un origen de datos o para calcularla sobre la marcha. Para obtener más información acerca de los `ListView` adaptadores, vea [ListView](~/android/user-interface/layouts/list-view/index.md).

Compile y ejecute la aplicación. La captura de pantalla siguiente es un ejemplo de cómo aparece la aplicación cuando se ejecuta en un dispositivo:

[![Captura de pantalla final](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----

## <a name="summary"></a>Resumen

En este artículo se explica el proceso de uso de Xamarin. Android Designer en Visual Studio para crear una interfaz de usuario para una aplicación básica.
Se ha mostrado cómo crear la interfaz para un único elemento de una lista, y se ha mostrado cómo agregar widgets y colocarlos visualmente.
También se explica cómo asignar recursos y, después, establecer diversas propiedades en esos widgets.
