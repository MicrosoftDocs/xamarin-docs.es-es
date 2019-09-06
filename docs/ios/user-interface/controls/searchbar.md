---
title: Barras de búsqueda en Xamarin. iOS
description: En este documento se describe cómo usar las barras de búsqueda en Xamarin. iOS. Describe cómo crear barras de búsqueda mediante programación y en un guión gráfico.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 36e339139a0a7f853a770fdb188b5f03ee93f7ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283359"
---
# <a name="search-bars-in-xamarinios"></a>Barras de búsqueda en Xamarin. iOS

UISearchBar se usa para buscar en una lista de valores.

Contiene tres componentes principales:

- Campo que se usa para escribir texto. Los usuarios pueden usarla para escribir el término de búsqueda.
- Botón borrar, para quitar cualquier texto del campo de búsqueda.
- Botón Cancelar para salir de la función de búsqueda.

![Barra de búsqueda](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementar la barra de búsqueda

Para implementar la barra de búsqueda, empiece por crear una instancia de una nueva:

```csharp
searchBar = new UISearchBar();
```

Y, a continuación, colóquelo. En el ejemplo siguiente se muestra cómo colocarlo en la barra de navegación o en el HeaderView de una tabla:

```csharp
NavigationItem.TitleView = searchBar;

// or

TableView.TableHeaderView = searchBar;
```

Establecer propiedades en la barra de búsqueda:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propiedades de la barra de búsqueda](searchbar-images/image6.png)

Provoca el `SearchButtonClicked` evento cuando se presiona el botón Buscar. Se llamará a la lógica de búsqueda:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Para obtener información sobre la administración de la presentación de la barra de búsqueda y los resultados de la búsqueda, consulte la receta del [controlador de búsqueda](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) .

## <a name="using-the-search-bar-in-the-designer"></a>Usar la barra de búsqueda en el diseñador

El diseñador ofrece dos opciones para implementar una barra de búsqueda en el diseñador.

- Barra de búsqueda
- Barra de búsqueda con el controlador de pantalla de búsqueda (desusado)

![Controles de barra de búsqueda en el diseñador](searchbar-images/image2.png)

Usar el panel de propiedades para establecer las propiedades de la barra de búsqueda

![Diseñador de propiedades de barra de búsqueda](searchbar-images/image3.png)

Estas propiedades se explican a continuación:

- **Texto, marcador de posición, preguntar** : estas propiedades se usan para sugerir e indicar cómo deben usar los usuarios la barra de búsqueda. Por ejemplo, si la aplicación muestra una lista de tiendas, podría usar la propiedad prompt para informar a los usuarios de que pueden "escribir una ciudad, un nombre de caso o código postal".
- **Estilo de búsqueda** : puede establecer que la barra de búsqueda sea **prominente** o **mínima**. El uso de los prominentes hará que se Matize todo lo demás en la pantalla, excepto en el caso de la barra de búsqueda, lo que hará que el foco se dibuje en la barra de búsqueda. La barra de búsqueda de estilo mínimo se combinará con su entorno.
- **Capacidades** : al habilitar estas propiedades solo se muestra el elemento de la interfaz de usuario. La funcionalidad se debe implementar para ello mediante la generación del evento correcto como se detalla en los documentos de la API de la [barra de búsqueda](xref:UIKit.UISearchBar) .
  - Muestra el botón resultados de la búsqueda/marcadores: muestra un icono de resultados de la búsqueda o marcadores en la barra de búsqueda.
  - Muestra el botón Cancelar: permite a los usuarios salir de la función de búsqueda. Se recomienda seleccionar esta opción.
  - Muestra la barra de ámbito: permite a los usuarios limitar el ámbito de la búsqueda. Por ejemplo, al buscar en la aplicación música, el usuario puede seleccionar si quiere buscar Apple Music o su biblioteca para una canción o un intérprete determinados. Para mostrar varias opciones, agregue una matriz de títulos a la propiedad **ScopeBarTitles** .
  ![Títulos de ámbito de la barra de búsqueda](searchbar-images/image4.png)

- **Comportamiento de texto** : estas opciones se usan para abordar cómo se da formato a la entrada del usuario cuando se escriben. El uso de mayúsculas establecerá el inicio de cada palabra o frase, o todos los caracteres en mayúsculas. Corrección y revisión ortográfica con preguntar al usuario sobre las sugerencias de palabras que escriben.
- **Teclado** : controla el estilo de teclado que se muestra para la entrada y, por tanto, las teclas que están disponibles en el teclado. Esto incluye el panel numérico, el panel de teléfono, el correo electrónico y la dirección URL junto con otras opciones.
- **Apariencia** : controla el estilo de apariencia del teclado y será oscuro o claro.
- **Tecla Retorno** : cambie la etiqueta de la tecla retorno para reflejar mejor la acción que se llevará a cabo. Los valores admitidos son Go, join, Next, Route, done y Search.
- **Secure** : identifica si la entrada está enmascarada (por ejemplo, para una entrada de contraseña).

## <a name="related-links"></a>Vínculos relacionados

- [Controlador de búsqueda](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
