---
title: Componentes y funcionalidad de RecyclerView
description: Información general del administrador de diseño de RecyclerView, el adaptador y el titular de la vista.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 823215b7cc1bac8a8f6bffc6e480400135e88ce8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028820"
---
# <a name="recyclerview-parts-and-functionality"></a>Componentes y funcionalidad de RecyclerView

`RecyclerView` controla algunas tareas internamente (como el desplazamiento y el reciclaje de vistas), pero es esencialmente un administrador que coordina las clases auxiliares para mostrar una colección. `RecyclerView` delega las tareas en las clases auxiliares siguientes:

- **`Adapter`** &ndash; infla los diseños de elementos (crea instancias del contenido de un archivo de diseño) y enlaza los datos a las vistas que se muestran dentro de un `RecyclerView`. El adaptador también informa de los eventos de clic de elemento.

- **`LayoutManager`** &ndash; mide y coloca las vistas de elementos dentro de un `RecyclerView` y administra la Directiva para el reciclaje de vistas.

- **`ViewHolder`** &ndash; busca y almacena las referencias de la vista. El titular de la vista también ayuda a detectar los clics de elementos y vistas.

- **`ItemDecoration`** &ndash; permite a una aplicación agregar desplazamientos especiales de dibujo y diseño a vistas específicas para dibujar divisores entre elementos, resaltados y límites de agrupación visual.

- **`ItemAnimator`** &ndash; define las animaciones que tienen lugar durante las acciones del elemento o cuando se realizan cambios en el adaptador.

En el diagrama siguiente se describe la relación entre las clases `RecyclerView`, `LayoutManager`y `Adapter`:

![Diagrama de RecyclerView que contiene LayoutManager, con el adaptador para tener acceso al conjunto de datos](parts-and-functionality-images/01-recyclerview-diagram.png)

Como se muestra en esta ilustración, el `LayoutManager` se puede considerar como intermediario entre el `Adapter` y el `RecyclerView`. El `LayoutManager` realiza llamadas en `Adapter` métodos en nombre del `RecyclerView`. Por ejemplo, el `LayoutManager` llama a un método `Adapter` cuando es el momento de crear una nueva vista para una posición de elemento determinada en el `RecyclerView`. El `Adapter` aumenta el diseño de ese elemento y crea una instancia de `ViewHolder` (no se muestra) para almacenar en caché las referencias a las vistas en esa posición. Cuando el `LayoutManager` llama al `Adapter` para enlazar un elemento determinado al conjunto de datos, el `Adapter` busca los datos de ese elemento, los recupera del conjunto de datos y los copia en la vista de elementos asociada.

Al usar `RecyclerView` en la aplicación, se requiere la creación de tipos derivados de las siguientes clases:

- **`RecyclerView.Adapter`** &ndash; proporciona un enlace del conjunto de datos de la aplicación (que es específico de la aplicación) a las vistas de elementos que se muestran en el `RecyclerView`. El adaptador sabe cómo asociar cada posición de vista-elemento del `RecyclerView` a una ubicación específica en el origen de datos. Además, el adaptador controla el diseño del contenido dentro de cada vista de elemento individual y crea el titular de la vista para cada vista. El adaptador también informa de los eventos de clic de elemento detectados por la vista de elementos.

- **`RecyclerView.ViewHolder`** &ndash; almacena en caché las referencias a las vistas en el archivo de diseño del elemento para que las búsquedas de recursos no se repitan innecesariamente. El titular de la vista también organiza los eventos de clic de elemento que se reenviarán al adaptador cuando un usuario puntee en la vista de elemento asociada del titular de la vista.

- **`RecyclerView.LayoutManager`** &ndash; coloca los elementos dentro de la `RecyclerView`. Puede usar uno de varios administradores de diseño predefinidos o puede implementar su propio administrador de diseño personalizado.
    `RecyclerView` delega la Directiva de diseño en el administrador de diseño, por lo que puede conectar otro administrador de diseño sin tener que realizar cambios significativos en la aplicación.

Además, también puede extender las clases siguientes para cambiar la apariencia y el funcionamiento de `RecyclerView` en la aplicación:

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Si no extiende `ItemDecoration` y `ItemAnimator`, `RecyclerView` utiliza implementaciones predeterminadas. En esta guía no se explica cómo crear clases personalizadas de `ItemDecoration` y `ItemAnimator`; para obtener más información sobre estas clases, vea [RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) y [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

<a name="recycling" />

## <a name="how-view-recycling-works"></a>Cómo funciona el reciclaje de vistas

`RecyclerView` no asigna una vista de elemento para cada elemento del origen de datos. En su lugar, asigna solo el número de vistas de elemento que caben en la pantalla y reutiliza esos diseños de elemento a medida que el usuario se desplaza. Cuando la vista se desplaza por primera vez fuera de la vista, pasa por el proceso de reciclaje que se muestra en la ilustración siguiente:

[Diagrama de![que ilustra los seis pasos del reciclaje de vistas](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Cuando una vista se desplaza fuera de la vista y ya no se muestra, se convierte en una *vista de rechazo*.

2. La vista de rechazo se coloca en un grupo y se convierte en una *vista de reciclaje*.
    Este grupo es una caché de vistas que muestran el mismo tipo de datos.

3. Cuando se va a mostrar un nuevo elemento, se toma una vista del grupo de reciclaje para su reutilización. Dado que esta vista debe volver a estar enlazada por el adaptador antes de mostrarse, se denomina *vista desfasada*.

4. La vista desfasada se recicla: el adaptador busca los datos para el siguiente elemento que se va a mostrar y copia estos datos en las vistas de este elemento. Las referencias de estas vistas se recuperan del titular de la vista asociado a la vista reciclada.

5. La vista reciclada se agrega a la lista de elementos de la `RecyclerView` que están a punto de ir en pantalla.

6. La vista reciclada pasa a la pantalla cuando el usuario desplaza el `RecyclerView` al siguiente elemento de la lista. Mientras tanto, otra vista se desplaza fuera de la vista y se recicla según los pasos anteriores.

Además de la reutilización de la vista de elementos, `RecyclerView` también utiliza otra optimización de eficacia: los titulares de la vista. Un *contenedor de vistas* es una clase simple que almacena en caché las referencias de la vista. Cada vez que el adaptador infla un archivo de diseño de elementos, también crea un contenedor de vistas correspondiente. El titular de la vista usa `FindViewById` para obtener referencias a las vistas en el archivo de diseño de elementos inflado. Estas referencias se usan para cargar datos nuevos en las vistas cada vez que se recicla el diseño para Mostrar datos nuevos.

## <a name="the-layout-manager"></a>Administrador de diseño

El administrador de diseño es responsable de colocar los elementos en la `RecyclerView` pantalla; determina el tipo de presentación (una lista o una cuadrícula), la orientación (si los elementos se muestran vertical u horizontalmente) y qué elementos de dirección deben mostrarse (en orden normal o en orden inverso). El administrador de diseño también es responsable de calcular el tamaño y la posición de cada elemento en la pantalla de **RecycleView** .

El administrador de diseño tiene un propósito adicional: determina la Directiva para Cuándo reciclar las vistas de elementos que ya no son visibles para el usuario.
Dado que el administrador de diseño es consciente de qué vistas están visibles (y cuáles no), está en la mejor posición para decidir cuándo se puede reciclar una vista. Para reciclar una vista, el administrador de diseño normalmente realiza llamadas al adaptador para reemplazar el contenido de una vista reciclada con datos diferentes, como se describió anteriormente en [Cómo funciona el reciclaje de vistas](#recycling).

Puede extender `RecyclerView.LayoutManager` para crear su propio administrador de diseño o puede usar un administrador de diseño predefinido. `RecyclerView` proporciona los siguientes administradores de diseño predefinidos:

- **`LinearLayoutManager`** &ndash; organiza los elementos en una columna que se puede desplazar verticalmente o en una fila que se puede desplazar horizontalmente.

- **`GridLayoutManager`** &ndash; muestra los elementos en una cuadrícula.

- **`StaggeredGridLayoutManager`** &ndash; muestra los elementos en una cuadrícula escalonada, donde algunos elementos tienen distintos altos y anchos.

Para especificar el administrador de diseño, cree una instancia del administrador de diseño elegido y páselo al método `SetLayoutManager`. Tenga en cuenta que *debe* especificar el administrador de diseño &ndash; `RecyclerView` no selecciona un administrador de diseño predefinido de forma predeterminada.

Para obtener más información sobre el administrador de diseño, vea la referencia de la [clase RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

## <a name="the-view-holder"></a>El titular de la vista

El titular de la vista es una clase que se define para almacenar en caché las referencias de la vista. El adaptador utiliza estas referencias de vista para enlazar cada vista a su contenido. Cada elemento del `RecyclerView` tiene una instancia asociada de contenedor de vistas que almacena en memoria caché las referencias de la vista para ese elemento. Para crear un contenedor de vistas, siga estos pasos para definir una clase que contenga el conjunto exacto de vistas por elemento:

1. Subclase `RecyclerView.ViewHolder`.
2. Implemente un constructor que busque y almacene las referencias de la vista.
3. Implemente las propiedades que el adaptador puede usar para tener acceso a estas referencias.

Un ejemplo detallado de una implementación de `ViewHolder` se presenta en [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obtener más información sobre `RecyclerView.ViewHolder`, consulte la referencia de la [clase RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

## <a name="the-adapter"></a>El adaptador

La mayoría del código de integración de la `RecyclerView` tiene lugar en el adaptador. `RecyclerView` requiere que proporcione un adaptador derivado de `RecyclerView.Adapter` para tener acceso al origen de datos y rellenar cada elemento con el contenido del origen de datos.
Dado que el origen de datos es específico de la aplicación, debe implementar la funcionalidad del adaptador que entienda cómo obtener acceso a los datos. El adaptador extrae información del origen de datos y la carga en cada elemento de la colección de `RecyclerView`.

En el dibujo siguiente se muestra cómo el adaptador asigna el contenido de un origen de datos a través de los titulares de la vista a vistas individuales dentro de cada elemento de fila del `RecyclerView`:

[Diagrama de![que ilustra el adaptador que conecta el origen de datos a ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

El adaptador carga cada fila de `RecyclerView` con datos para un elemento de fila determinado. En el caso de la posición de fila *p*, por ejemplo, el adaptador busca los datos asociados en la posición *p* en el origen de datos y copia estos datos en el elemento de fila de la posición *p* de la colección de `RecyclerView`.
En el dibujo anterior, por ejemplo, el adaptador usa el portaherramienta de la vista para buscar las referencias de la `ImageView` y `TextView` en esa posición, por lo que no tiene que llamar repetidamente a `FindViewById` para esas vistas cuando el usuario se desplaza por la colección y reutiliza las vistas.

Al implementar un adaptador, debe invalidar los siguientes métodos de `RecyclerView.Adapter`:

- **`OnCreateViewHolder`** &ndash; crea instancias del archivo de diseño del elemento y del portaherramienta de la vista.

- **`OnBindViewHolder`** &ndash; carga los datos en la posición especificada en las vistas cuyas referencias se almacenan en el titular de la vista especificado.

- **`ItemCount`** &ndash; devuelve el número de elementos del origen de datos.

El administrador de diseño llama a estos métodos mientras coloca los elementos dentro del `RecyclerView`.

## <a name="notifying-recyclerview-of-data-changes"></a>Notificar los cambios de datos de RecyclerView

`RecyclerView` no actualiza automáticamente su presentación cuando cambia el contenido de su origen de datos; el adaptador debe notificar a `RecyclerView` cuando haya un cambio en el conjunto de datos. El conjunto de datos puede cambiar de muchas maneras. por ejemplo, el contenido de un elemento puede cambiar o se puede modificar la estructura global de los datos.
`RecyclerView.Adapter` proporciona una serie de métodos a los que puede llamar para que `RecyclerView` responda a los cambios de datos de la manera más eficaz:

- **`NotifyItemChanged`** &ndash; indica que el elemento en la posición especificada ha cambiado.

- **`NotifyItemRangeChanged`** &ndash; indica que han cambiado los elementos del intervalo especificado de posiciones.

- **`NotifyItemInserted`** &ndash; indica que el elemento de la posición especificada se ha insertado recientemente.

- **`NotifyItemRangeInserted`** &ndash; indica que se han insertado recientemente los elementos del intervalo especificado de posiciones.

- **`NotifyItemRemoved`** &ndash; indica que se ha quitado el elemento de la posición especificada.

- **`NotifyItemRangeRemoved`** &ndash; indica que se han quitado los elementos del intervalo especificado de posiciones.

- **`NotifyDataSetChanged`** &ndash; indica que el conjunto de datos ha cambiado (fuerza una actualización completa).

Si sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a los métodos adecuados anteriores para actualizar `RecyclerView` de la manera más eficaz. Si no sabe exactamente cómo ha cambiado el conjunto de datos, puede llamar a `NotifyDataSetChanged`, que es mucho menos eficaz porque `RecyclerView` debe actualizar todas las vistas que son visibles para el usuario. Para obtener más información acerca de estos métodos, vea [RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

En el siguiente tema, [un ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), se implementa una aplicación de ejemplo para mostrar ejemplos de código reales de los elementos y la funcionalidad descritos anteriormente.

## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Ejemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Extender el ejemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
