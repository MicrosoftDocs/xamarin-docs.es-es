---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 3b2b6a3f1aa575553964e71be3a64cec16bc616f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457333"
---
# <a name="xamarinandroid-gridlayout"></a>GridLayout de Xamarin. Android

`GridLayout`Es una nueva `ViewGroup` subclase que admite la colocación de vistas en una cuadrícula 2D, similar a una tabla HTML, como se muestra a continuación:

 [![GridLayout recortado que muestra cuatro celdas](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funciona con una jerarquía de vista plana, en la que las vistas secundarias establecen sus ubicaciones en la cuadrícula especificando las filas y las columnas en las que deben estar. De este modo, el *GridLayout* puede colocar las vistas en la cuadrícula sin necesidad de que las vistas intermedias proporcionen una estructura de tabla, como se muestra en las filas de la tabla que se usan en TableLayout. Al mantener una jerarquía plana, *GridLayout* puede diseñar más rápidamente sus vistas secundarias. Echemos un vistazo a un ejemplo para ilustrar lo que significa realmente este concepto en el código.

## <a name="creating-a-grid-layout"></a>Crear un diseño de cuadrícula

El siguiente XML agrega varios `TextView` controles a un *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

El diseño ajustará el tamaño de las filas y las columnas para que puedan ajustarse a su contenido, tal como se muestra en el diagrama siguiente:

 [![Diagrama de diseño que muestra dos celdas a la izquierda más pequeñas que a la derecha](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Esto da como resultado la siguiente interfaz de usuario cuando se ejecuta en una aplicación:

 [![Captura de pantalla de la aplicación GridLayoutDemo que muestra cuatro celdas](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>Especificar orientación

Observe que en el XML anterior, cada no `TextView` especifica una fila o columna. Cuando no se especifican, el `GridLayout` asigna cada vista secundaria en orden, en función de la orientación. Por ejemplo, vamos a cambiar la orientación del GridLayout del valor predeterminado, que es horizontal, a vertical similar a la siguiente:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

Ahora, el colocará `GridLayout` las celdas de arriba abajo en cada columna, en lugar de izquierda a derecha, como se muestra a continuación:

 [![Diagrama que ilustra cómo se colocan las celdas en orientación vertical](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Esto da como resultado la siguiente interfaz de usuario en tiempo de ejecución:

 [![Captura de pantalla de GridLayoutDemo con celdas colocadas en orientación vertical](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>Especificar la posición explícita

Si queremos controlar explícitamente las posiciones de las vistas secundarias en `GridLayout` , podemos establecer sus `layout_row` `layout_column` atributos y. Por ejemplo, el siguiente código XML hará que el diseño se muestre en la primera captura de pantalla (mostrada anteriormente), independientemente de la orientación.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```

### <a name="specifying-spacing"></a>Especificar el espaciado

Tenemos un par de opciones que proporcionarán espaciado entre las vistas secundarias de `GridLayout` . Podemos usar el `layout_margin` atributo para establecer el margen en cada vista secundaria directamente, como se muestra a continuación.

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Además, en Android 4, ya está disponible una nueva vista de espaciado de uso general denominada `Space` . Para usarlo, simplemente agréguelo como una vista secundaria.
Por ejemplo, el XML siguiente agrega una fila adicional al estableciendo el `GridLayout` valor `rowcount` de en 3, y agrega una `Space` vista que proporciona espaciado entre el `TextViews` .

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Este XML crea el espaciado en el `GridLayout` , tal y como se muestra a continuación:

 [![Captura de pantalla de GridLayoutDemo que ilustra celdas mayores con espaciado](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

La ventaja de usar la nueva `Space` vista es que permite el espaciado y no es necesario establecer atributos en cada vista secundaria.

### <a name="spanning-columns-and-rows"></a>Expandir columnas y filas

`GridLayout`También admite celdas que abarcan varias columnas y filas. Por ejemplo, suponga que agregamos otra fila que contiene un botón a `GridLayout` , tal y como se muestra a continuación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

Esto hará que la primera columna de `GridLayout` se estire para acomodar el tamaño del botón, como se ve aquí:

[![Captura de pantalla de GridLayoutDemo con un botón que abarca solo la primera columna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Para evitar que la primera columna se ajuste, podemos establecer el botón para que abarque dos columnas; para ello, establezca su ColumnSpan de la siguiente manera:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Esto da como resultado un diseño para `TextViews` que sea similar al diseño que teníamos anteriormente, con el botón agregado a la parte inferior de, `GridLayout` como se muestra a continuación:

 [![Captura de pantalla de GridLayoutDemo con el botón que abarca ambas columnas](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [GridLayoutDemo (ejemplo)](/samples/xamarin/monodroid-samples/gridlayoutdemo)