---
title: Guiones gráficos unificados en Xamarin. iOS
description: En este documento se describen los guiones gráficos unificados de Xamarin. iOS. Los guiones gráficos unificados permiten a los desarrolladores admitir varios tamaños de pantalla con una única definición de interfaz.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 02bc6fe7109f13629e776c800657846fca02641e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657130"
---
# <a name="unified-storyboards-in-xamarinios"></a>Guiones gráficos unificados en Xamarin. iOS

iOS 8 incluye un nuevo mecanismo más sencillo de usar para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferentes, se pueden crear vistas rápidas y receptivas en un estilo de "diseño-una, uso-varios".

Como el desarrollador ya no necesita crear un guion gráfico independiente y específico para dispositivos iPhone e iPad, tiene la flexibilidad de diseñar aplicaciones con una interfaz común y, a continuación, personalizar esa interfaz para clases de tamaño diferentes. De esta manera, una aplicación se puede adaptar a las ventajas de cada factor de forma y cada interfaz de usuario se puede optimizar para proporcionar la mejor experiencia.

<a name="size-classes" />

## <a name="size-classes"></a>Clases de tamaño

Antes de iOS 8, el desarrollador usaba `UIInterfaceOrientation` y `UIInterfaceIdiom` para diferenciar entre los modos vertical y horizontal, y entre los dispositivos iPhone e iPad. En iOS8, la orientación y el dispositivo se determinan mediante el uso de *clases de tamaño*.

Los dispositivos se definen mediante clases de tamaño, tanto en los ejes verticales como horizontales, y hay dos tipos de clases de tamaño en iOS 8:

-  **Normal** : se trata de un tamaño de pantalla grande (por ejemplo, un iPad) o un gadget que proporciona la impresión de un gran tamaño (por ejemplo, un`UIScrollView`
-  **Compacto** : es para dispositivos más pequeños (por ejemplo, un iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.


Si se usan juntos los dos conceptos, el resultado es una cuadrícula de 2 x 2 que define los diferentes tamaños posibles que se pueden usar en las distintas orientaciones, como se puede ver en el diagrama siguiente:

 [![](unified-storyboards-images/sizeclassgrid.png "Cuadrícula 2 x 2 que define los diferentes tamaños posibles que se pueden usar en las orientaciones normales y compactas.")](unified-storyboards-images/sizeclassgrid.png#lightbox)

El desarrollador puede crear un controlador de vista que use cualquiera de las cuatro posibilidades que daría como resultado diseños diferentes (como se muestra en los gráficos anteriores).

### <a name="ipad-size-classes"></a>Clases de tamaño de iPad

El iPad, debido al tamaño, tiene un tamaño de clase **normal** para ambas orientaciones.

 [![](unified-storyboards-images/image1.png "Clases de tamaño de iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Clases de tamaño de iPhone

El iPhone tiene clases de tamaño diferentes en función de la orientación del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Clases de tamaño de iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Cuando el dispositivo está en modo vertical, la pantalla tiene una clase **compacta** horizontalmente y **normal** verticalmente
-  Cuando el dispositivo está en modo horizontal, las clases de pantalla se invierten del modo vertical.

### <a name="iphone-6-plus-size-classes"></a>Clases de tamaño de iPhone 6 Plus

Los tamaños son los mismos que los de los iPhone anteriores cuando están en orientación vertical, pero diferentes en horizontal:

[![](unified-storyboards-images/iphone6sizeclasses.png "Clases de tamaño de iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Dado que el iPhone 6 Plus tiene una pantalla lo suficientemente grande, puede tener una clase de tamaño de ancho normal en el modo horizontal.

### <a name="support-for-a-new-screen-scale"></a>Compatibilidad con una nueva escala de pantalla

El iPhone 6 Plus usa una nueva pantalla de alta retina con un factor de escala de pantalla de 3,0 (tres veces la resolución de pantalla del iPhone original). Para proporcionar la mejor experiencia posible en estos dispositivos, incluya la nueva ilustración diseñada para esta escala de pantalla. En Xcode 6 y versiones posteriores, los catálogos de recursos pueden incluir imágenes en los tamaños 1x, 2x y 3x. simplemente agregue los recursos de imagen nuevos e iOS elegirá los activos correctos cuando se ejecute en un iPhone 6 Plus.

El comportamiento de carga de imágenes en iOS también `@3x` reconoce un sufijo en los archivos de imagen. Por ejemplo, si el desarrollador incluye un recurso de imagen (en diferentes resoluciones) en el paquete de la aplicación con los siguientes nombres `MonkeyIcon.png`de `MonkeyIcon@2x.png`archivo: `MonkeyIcon@3x.png`, y. En el iPhone 6 más, `MonkeyIcon@3x.png` la imagen se usará automáticamente si el desarrollador carga una imagen con el código siguiente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
O bien, si asignan la imagen a un elemento de la interfaz de `MonkeyIcon.png`usuario mediante `MonkeyIcon@3x.png` el diseñador de iOS como, se usará de nuevo automáticamente en el iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámico

El archivo de pantalla de inicio se muestra como una pantalla de presentación mientras se inicia una aplicación de iOS para proporcionar comentarios al usuario de que la aplicación se está iniciando realmente. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` recursos de imagen para cada tipo de dispositivo, orientación y resolución de pantalla en la que se ejecutaba la aplicación.

Como novedad de iOS 8, el desarrollador puede crear un único archivo `.xib` atómico en Xcode que use las clases de diseño y tamaño automáticos para crear una *pantalla de inicio dinámico* que funcione en todos los dispositivos, resoluciones y orientaciones. Esto no solo reduce la cantidad de trabajo necesario para que el desarrollador cree y mantenga todos los recursos de imagen necesarios, pero reduce el tamaño del paquete instalado de la aplicación.

## <a name="traits"></a>Rasgos

Los rasgos son propiedades que se pueden usar para determinar cómo cambia un diseño a medida que cambia su entorno. Están formados por un conjunto de propiedades ( `HorizontalSizeClass` y `VerticalSizeClass` basadas en `UIUserInterfaceSizeClass`), así como la expresión de interfaz ( `UIUserInterfaceIdiom`) y la escala de presentación.

Todos los Estados anteriores se incluyen en un contenedor al que Apple hace referencia como una colección de rasgos ( `UITraitCollection`), que no solo contiene las propiedades, sino también sus valores.

## <a name="trait-environment"></a>Entorno de rasgos

Los entornos de rasgos son una nueva interfaz de iOS 8 y pueden devolver una colección de rasgos para los objetos siguientes:

-  Pantallas ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Controladores de vista `UIViewController` ().
-  Vistas ( `UIView` ).
-  Controlador de presentación `UIPresentationController` ().


El desarrollador utiliza la colección de rasgos devuelta por un entorno de rasgos para determinar cómo se debe diseñar una interfaz de usuario.

Todos los entornos de rasgos hacen que una jerarquía se vea en el diagrama siguiente:

 [![](unified-storyboards-images/viewhierarchy.png "Diagrama de jerarquía de entornos de rasgos")](unified-storyboards-images/viewhierarchy.png#lightbox)

La colección de rasgos que cada uno de los entornos de rasgo anteriores debe fluir, de forma predeterminada, del entorno primario al secundario.

Además de obtener la colección de rasgos actual, el entorno de rasgos tiene `TraitCollectionDidChange` un método, que se puede invalidar en las subclases del controlador de vista o vista. El desarrollador puede usar este método para modificar cualquiera de los elementos de la interfaz de usuario que dependen de rasgos cuando esos rasgos han cambiado.

## <a name="typical-trait-collections"></a>Colecciones de rasgos típicas

En esta sección se tratarán los tipos típicos de colecciones de rasgos que el usuario experimentará al trabajar con iOS 8.

A continuación se muestra una colección de rasgos típica que el desarrollador podría ver en un iPhone:

|Propiedad|Valor|
|--- |--- |
|`HorizontalSizeClass`|Unidad|
|`VerticalSizeClass`|Estándar|
|`UserInterfaceIdom`|Teléfono|
|`DisplayScale`|2.0|

El conjunto anterior representaría una colección de rasgos completa, ya que tiene valores para todas sus propiedades de rasgo.

También es posible tener una colección de rasgos que no presente algunos de sus valores (a los que Apple hace referencia como no *especificado*):

|Propiedad|Valor|
|--- |--- |
|`HorizontalSizeClass`|Unidad|
|`VerticalSizeClass`|Sin especificar|
|`UserInterfaceIdom`|Sin especificar|
|`DisplayScale`|Sin especificar|

Sin embargo, por lo general, cuando el desarrollador solicita el entorno de rasgo para su colección de rasgos, devolverá una colección completa como se mostró en el ejemplo anterior.

Si un entorno de rasgos (por ejemplo, un controlador de vista o vista) no se encuentra dentro de la jerarquía de vistas actual, el desarrollador podría obtener los valores no especificados para una o varias de las propiedades de rasgo.

El desarrollador también obtendrá una colección de rasgos parcialmente cualificada si usa uno de los métodos de creación proporcionados por Apple, `UITraitCollection.FromHorizontalSizeClass`como, para crear una nueva colección.

Una operación que se puede realizar en varias colecciones de rasgos se compara entre sí, lo que implica la pregunta de una colección de rasgos si contiene otra. Lo que significa la *contención* es que, para cualquier rasgo especificado en la segunda colección, el valor debe coincidir exactamente con el valor de la primera colección.

Para probar dos rasgos, use el `Contains` método de que `UITraitCollection` pasa el valor del rasgo que se va a probar.

El desarrollador puede realizar las comparaciones manualmente en el código para determinar cómo diseñar vistas o controladores de vistas. Sin embargo `UIKit` , usa este método internamente para proporcionar parte de su funcionalidad, como en el proxy de apariencia, por ejemplo.

## <a name="appearance-proxy"></a>Proxy de apariencia

El proxy de apariencia se presentó en versiones anteriores de iOS para que los desarrolladores puedan personalizar las propiedades de sus vistas. Se ha ampliado en iOS 8 para admitir colecciones de rasgos.

Los proxies de apariencia ahora incluyen un nuevo `AppearanceForTraitCollection`método,, que devuelve un nuevo proxy de apariencia para la colección de rasgos especificada que se ha pasado. Las personalizaciones que realice el desarrollador en ese proxy de apariencia solo surtirán efecto en las vistas que se ajusten a la colección de rasgos especificada.

Por lo general, el desarrollador pasará una colección de rasgos especificada parcialmente `AppearanceForTraitCollection` al método, como uno que acaba de especificar una clase de tamaño horizontal de Compact, de modo que pueda personalizar cualquier vista de la aplicación que esté compactada horizontalmente.

## <a name="uiimage"></a>UIImage

Otra clase a la que Apple ha agregado la colección `UIImage`de rasgos es. En el pasado, el desarrollador tenía que especificar @1X una @2x versión y de cualquier recurso de gráfico de mapa de letra que se iba a incluir en la aplicación (por ejemplo, un icono).

iOS 8 se ha ampliado para permitir que el desarrollador incluya varias versiones de una imagen en un catálogo de imágenes basado en una colección de rasgos. Por ejemplo, el desarrollador podría incluir una imagen más pequeña para trabajar con una clase de rasgo compacto y una imagen de tamaño completo para cualquier otra colección.

Cuando una de las imágenes se usa dentro de una `UIImageView` clase, la vista de imagen muestra automáticamente la versión correcta de la imagen para su colección de rasgos. Si el entorno de rasgo cambia (por ejemplo, el usuario que cambia el dispositivo de vertical a horizontal), la vista de imagen seleccionará automáticamente el nuevo tamaño de la imagen para que coincida con la nueva colección de rasgos y cambiará su tamaño para que coincida con el de la versión actual de la imagen. indica.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha agregado una nueva clase a iOS 8 llamada `UIImageAsset` para ofrecer al desarrollador aún más control sobre la selección de imágenes.

Un recurso de imagen incluye todas las distintas versiones de una imagen y permite al desarrollador solicitar una imagen específica que coincida con una colección de rasgos que se ha pasado. Las imágenes se pueden agregar o quitar de un recurso de imagen, sobre la marcha.

Para obtener más información sobre los recursos de imagen, vea la documentación de [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) de Apple.

## <a name="combining-trait-collections"></a>Combinar colecciones de rasgos

Otra función que puede realizar un programador en las colecciones de rasgos es agregar dos juntos que darán como resultado la colección combinada, donde los valores no especificados de una colección se reemplazan por los valores especificados en un segundo. Esto se hace mediante el `FromTraitsFromCollections` método de la `UITraitCollection` clase.

Como se indicó anteriormente, si alguno de los rasgos no está especificado en una de las colecciones de rasgos y se especifica en otro, el valor se establecerá en la versión especificada. Sin embargo, si se especifican varias versiones de un valor determinado, el valor de la última colección de rasgos será el valor que se utiliza.

## <a name="adaptive-view-controllers"></a>Controladores de vistas adaptables

En esta sección se explican los detalles de cómo los controladores de vista y vista de iOS han adoptado los conceptos de rasgos y clases de tamaño para ser más adaptables de forma automática en las aplicaciones del desarrollador.

### <a name="split-view-controller"></a>Controlador de vista en dos paneles

Una de las clases de controlador de vista que ha cambiado más en iOS 8 es `UISplitViewController` la clase. En el pasado, el desarrollador solía usar un controlador de vista dividida en la versión de iPad de la aplicación y, a continuación, tendría que proporcionar una versión completamente diferente de la jerarquía de vistas para la versión de iPhone de la aplicación.

En iOS 8, la `UISplitViewController` clase está disponible en ambas plataformas (iPad y iPhone), lo que permite al desarrollador crear una jerarquía de controlador de vista que funcionará para iPhone y iPad.

Cuando un iPhone está en horizontal, el controlador de vista en dos paneles presenta sus vistas en paralelo, tal como lo haría en un iPad.

### <a name="overriding-traits"></a>Invalidar rasgos

Los entornos de rasgos se colocan en cascada desde el contenedor principal hasta los contenedores secundarios, como en el siguiente gráfico que muestra un controlador de vista en dos paneles en un iPad con la orientación horizontal:

 [![](unified-storyboards-images/cascadingclasses01.png "Un controlador de vista en dos paneles en un iPad con la orientación horizontal")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Dado que el iPad tiene una clase de tamaño normal en las orientaciones horizontal y vertical, la vista en dos paneles mostrará las vistas principal y de detalle.

En un iPhone, donde la clase de tamaño es compacta en ambas orientaciones, el controlador de vista en dos paneles solo muestra la vista de detalle, como se muestra a continuación:

 [![](unified-storyboards-images/cascadingclasses02.png "El controlador de vista en dos paneles solo muestra la vista de detalle")](unified-storyboards-images/cascadingclasses02.png#lightbox)

En una aplicación en la que el desarrollador desea mostrar tanto la vista maestra como la vista de detalle en un iPhone con la orientación horizontal, el desarrollador debe insertar un contenedor primario para el controlador de vista en dos paneles e invalidar la colección de rasgos. Tal como se muestra en el siguiente gráfico:

 [![](unified-storyboards-images/cascadingclasses03.png "El desarrollador debe insertar un contenedor primario para el controlador de vista en dos paneles e invalidar la colección de rasgos.")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Se establece como elemento primario del controlador de vista en dos paneles y `SetOverrideTraitCollection` se llama al método en la vista que pasa una nueva colección de rasgos y se destina al controlador de vista de división. `UIView` La nueva colección de rasgos invalida el `HorizontalSizeClass`, estableciéndolo en `Regular`, de modo que el controlador de vista en dos paneles muestre las vistas principal y de detalle en un iPhone en la orientación horizontal.

Tenga en cuenta `VerticalSizeClass` que se ha `unspecified`establecido en, lo que permite agregar la nueva colección de rasgos a la colección de rasgos en el elemento primario `Compact VerticalSizeClass` , lo que da como resultado una para el controlador de vistas divididas secundarias.

### <a name="trait-changes"></a>Cambios de rasgo

En esta sección se examinan, en detalle, el modo en que las colecciones de rasgos pasan cuando cambia el entorno de rasgo. Por ejemplo, cuando el dispositivo se gira de vertical a horizontal.

 [![](unified-storyboards-images/traittransitions01.png "Información general de los cambios de rasgos de vertical a horizontal")](unified-storyboards-images/traittransitions01.png#lightbox)

En primer lugar, iOS 8 realiza alguna configuración para preparar la transición. A continuación, el sistema anima el estado de transición. Por último, iOS 8 limpia todos los Estados temporales necesarios durante la transición.

iOS 8 proporciona varias devoluciones de llamada que el desarrollador puede usar para participar en el cambio de rasgo, tal como se muestra en la tabla siguiente:

|Phase|Callback|DESCRIPCIÓN|
|--- |--- |--- |
|Configuración|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Se llama a este método al principio de un cambio de rasgo antes de que una colección de rasgos se establezca en su nuevo valor.</li><li>Se llama al método cuando el valor de la colección de rasgos ha cambiado pero antes de que tenga lugar cualquier animación.</li></ul>|
|Animación|`WillTransitionToTraitCollection`|El Coordinador de transiciones que se pasa a este método `AnimateAlongside` tiene una propiedad que permite al desarrollador agregar animaciones que se ejecutarán junto con las animaciones predeterminadas.|
|Limpieza|`WillTransitionToTraitCollection`|Proporciona un método para que los desarrolladores incluyan su propio código de limpieza después de que tenga lugar la transición.|

El `WillTransitionToTraitCollection` método es ideal para animar los controladores de vista junto con los cambios de la colección de rasgos. El `WillTransitionToTraitCollection` método solo está disponible en los controladores de `UIViewController`vista () y no en otros entornos de `UIViews`rasgo, como.

Es excelente para trabajar con la `UIView` clase, donde el desarrollador desea actualizar la interfaz de usuario a medida que los rasgos cambian. `TraitCollectionDidChange`

### <a name="collapsing-the-split-view-controllers"></a>Contraer los controladores de vista en dos paneles

Ahora veamos más detenidamente lo que ocurre cuando un controlador de vista en dos paneles se contrae de una columna en una vista de una columna. Como parte de este cambio, hay dos procesos que deben realizarse:

-  De forma predeterminada, el controlador de vista en dos paneles usará el controlador de vista principal como vista después de que se produzca la contracción. El desarrollador puede invalidar este comportamiento invalidando `GetPrimaryViewControllerForCollapsingSplitViewController` el método `UISplitViewControllerDelegate` de y proporcionando cualquier controlador de vista que desee mostrar en el estado contraído.
-  El controlador de vista secundario tiene que combinarse en el controlador de vista principal. Por lo general, el desarrollador no tendrá que realizar ninguna acción para este paso; el controlador de vista en dos paneles incluye el control automático de esta fase basándose en el dispositivo de hardware. Sin embargo, puede haber algunos casos especiales en los que el desarrollador desee interactuar con este cambio. La llamada `CollapseSecondViewController` al método `UISplitViewControllerDelegate` de permite mostrar el controlador de vista principal cuando se produce la contracción, en lugar de la vista de detalles.


### <a name="expanding-the-split-view-controller"></a>Expandir el controlador de vista en dos paneles

Ahora veamos más detenidamente lo que ocurre cuando un controlador de vista en dos paneles se expande desde un estado contraído. Una vez más, hay dos fases que deben producirse:

-  En primer lugar, defina el nuevo controlador de vista principal. De forma predeterminada, el controlador de vista en dos paneles usará automáticamente el controlador de vista principal de la vista contraída. De nuevo, el desarrollador puede invalidar este comportamiento `GetPrimaryViewControllerForExpandingSplitViewController` mediante el método de. `UISplitViewControllerDelegate`
-  Una vez elegido el controlador de vista principal, se debe volver a crear el controlador de vista secundario. De nuevo, el controlador de vista en dos paneles incluye el control automático de esta fase basándose en el dispositivo de hardware. El desarrollador puede invalidar este comportamiento llamando al `SeparateSecondaryViewController` método `UISplitViewControllerDelegate` de.


En un controlador de vista en dos paneles, el controlador de vista principal desempeña una parte en la expansión y la contracción de las `CollapseSecondViewController` vistas `SeparateSecondaryViewController` mediante la implementación `UISplitViewControllerDelegate`de los métodos y de. `UINavigationController`implementa estos métodos para enviar y extraer automáticamente el controlador de vista secundario.

### <a name="showing-view-controllers"></a>Mostrar controladores de vista

Otro cambio que Apple ha realizado en iOS 8 es la forma en que el desarrollador muestra los controladores de vista. En el pasado, si la aplicación tuviera un controlador de vista hoja (por ejemplo, un controlador de vista de tabla) y el desarrollador mostrase un diferente (por ejemplo, en respuesta al punteo del usuario en una celda), la aplicación volvería a la jerarquía del controlador a la Controlador de vista de navegación y `PushViewController` llamar al método en él para mostrar la nueva vista.

Esto presentó un acoplamiento muy estrecho entre el controlador de navegación y el entorno en el que se estaba ejecutando. En iOS 8, Apple ha desacoplado esto proporcionando dos nuevos métodos:

-  `ShowViewController`: Se adapta para mostrar el nuevo controlador de vista en función de su entorno. Por ejemplo, en `UINavigationController` , simplemente se envía la nueva vista en la pila. En un controlador de vista en dos paneles, el nuevo controlador de vista se presentará en el lado izquierdo como el nuevo controlador de vista principal. Si no hay ningún controlador de vista de contenedor, la nueva vista se mostrará como un controlador de vista modal.
-  `ShowDetailViewController`: Funciona de manera similar a `ShowViewController`, pero se implementa en un controlador de vista en dos paneles para reemplazar la vista de detalles con el nuevo controlador de vistas que se pasa. Si el controlador de vista en dos paneles está contraído (como podría verse en una aplicación de iPhone), la llamada se redirigirá al `ShowViewController` método y la nueva vista se mostrará como el controlador de vista principal. De nuevo, si no hay ningún controlador de vista de contenedor, la nueva vista se mostrará como un controlador de vista modal.


Estos métodos funcionan empezando por el controlador de vista Hoja y pasando por la jerarquía de vistas hasta que encuentren el controlador de vista del contenedor adecuado para controlar la presentación de la nueva vista.

Los desarrolladores pueden `ShowViewController` implementar `ShowDetailViewController` y en sus propios controladores de vista personalizados para obtener la misma funcionalidad `UINavigationController` automatizada que proporciona y `UISplitViewController` .

### <a name="how-it-works"></a>Cómo funciona

En esta sección, echaremos un vistazo a cómo se implementan realmente estos métodos en iOS 8. En primer lugar, echemos un `GetTargetForAction` vistazo al nuevo método:

 [![](unified-storyboards-images/gettargetforaction.png "El nuevo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Este método recorre la cadena de jerarquía hasta que se encuentra el controlador de vista del contenedor correcto. Por ejemplo:

1.  Si se `ShowViewController` llama a un método, el primer controlador de vista de la cadena que implementa este método es el controlador de navegación, por lo que se usa como elemento primario de la nueva vista.
1.  Si se `ShowDetailViewController` llama a un método en su lugar, el controlador de vista en dos paneles es el primer controlador de vista para implementarlo, por lo que se usa como elemento primario.


El `GetTargetForAction` método funciona mediante la búsqueda de un controlador de vista que implementa una acción determinada y, a continuación, la pregunta a ese controlador de vista si desea recibir esa acción. Dado que este método es público, los desarrolladores pueden crear sus propios métodos personalizados que funcionen igual que `ShowViewController` los `ShowDetailViewController` métodos integrados y.

## <a name="adaptive-presentation"></a>Presentación adaptable

En iOS 8, Apple ha realizado también la adaptación `UIPopoverPresentationController`de las presentaciones de elemento flotante (). Por lo tanto, un controlador de vista de presentación de elemento flotante presentará automáticamente una vista de elemento flotante normal en una clase de tamaño normal, pero la mostrará en una clase de tamaño compacta horizontalmente (por ejemplo, en un iPhone).

Para dar cabida a los cambios en el sistema de guion gráfico unificado, se ha creado un nuevo objeto de controlador `UIPresentationController`para administrar los controladores de vista presentados:. Este controlador se crea a partir del momento en que se presenta el controlador de vista hasta que se descarta. Como es una clase de administración, se puede considerar una superclase sobre el controlador de vista a medida que responde a los cambios del dispositivo que afectan al controlador de vistas (como la orientación), que luego se devuelven al controlador de la vista que el controlador de presentación controla.

Cuando el desarrollador presenta un controlador de vista mediante `PresentViewController` el método, se pasa a `UIKit`la administración del proceso de presentación. UIKit controla (entre otras cosas) el controlador correcto para el estilo que se está creando, con la única excepción cuando un controlador de vista tiene el estilo `UIModalPresentationCustom`establecido en. Aquí, la aplicación puede proporcionar su propio PresentationController en lugar de usar el `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de presentación personalizados

Con un estilo de presentación personalizado, los desarrolladores tienen la opción de usar un controlador de presentación personalizado. Este controlador personalizado se puede usar para modificar la apariencia y el comportamiento de la vista a la que es Allied.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabajar con clases de tamaño

El proyecto Adaptive photos de Xamarin que se incluye en este artículo ofrece un ejemplo práctico del uso de clases de tamaño y controladores de vistas adaptables en una aplicación de interfaz unificada de iOS 8.

Mientras que la aplicación crea su interfaz de usuario completamente a partir del código, en lugar de usar el diseñador de IOS y crear un guión gráfico unificado, se aplican las mismas técnicas. Más adelante en este artículo, le mostraremos cómo usar clases de tamaño con un guión gráfico unificado y el diseñador de iOS en una aplicación de Xamarin.

Ahora, echemos un vistazo más de cerca a cómo el proyecto Adaptive photos está implementando algunas de las características de la clase size en iOS 8 para crear una aplicación adaptable.

### <a name="adapting-to-trait-environment-changes"></a>Adaptación a los cambios en el entorno de rasgo

Cuando se ejecuta la aplicación Adaptive photos en un iPhone, cuando el usuario gira el dispositivo de vertical a horizontal, el controlador de vista en dos paneles muestra la vista de maestro y detalles:

 [![](unified-storyboards-images/rotation.png "El controlador de vista en dos paneles mostrará la vista maestro y los detalles, tal como se muestra aquí.")](unified-storyboards-images/rotation.png#lightbox)

Esto se logra invalidando el `UpdateConstraintsForTraitCollection` método del controlador de vista y ajustando las restricciones en función del valor `VerticalSizeClass`de. Por ejemplo:

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Agregar animaciones de transición

Cuando el controlador de vista en dos paneles de la aplicación fotos adaptables pasa de contraído a expandido, las animaciones se agregan a las `WillTransitionToTraitCollection` animaciones predeterminadas invalidando el método del controlador de vista. Por ejemplo:

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Invalidar el entorno de rasgos

Como se muestra arriba, la aplicación de fotos adaptables obliga al controlador de vista en dos paneles a mostrar los detalles y las vistas maestras cuando el dispositivo iPhone está en la vista horizontal.

Esto se logra mediante el uso del código siguiente en el controlador de vista:

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Expandir y contraer el controlador de vista en dos paneles

A continuación, vamos a examinar cómo se ha implementado el comportamiento de expansión y contracción del controlador de vista de división en Xamarin. `AppDelegate`En, cuando se crea el controlador de vista en dos paneles, se asigna su delegado para controlar estos cambios:

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

El `SeparateSecondaryViewController` método prueba para ver si se está mostrando una foto y toma medidas en función de ese estado. Si no se muestra ninguna foto, se contrae el controlador de vista secundario para que se muestre el controlador de vista principal.

El `CollapseSecondViewController` método se usa al expandir el controlador de vista en dos paneles para ver si hay fotografías en la pila, en caso de que se contraiga de nuevo a esa vista.

### <a name="moving-between-view-controllers"></a>Moverse entre controladores de vista

A continuación, echemos un vistazo a cómo se mueve la aplicación Adaptive photos entre los controladores de vista. En la `AAPLConversationViewController` clase cuando el usuario selecciona una celda de la tabla `ShowDetailViewController` , se llama al método para mostrar la vista de detalles:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>Mostrar indicadores de divulgación

En la aplicación fotográfico adaptable hay varios lugares en los que los indicadores de divulgación se ocultan o se muestran en función de los cambios en el entorno de rasgo. Esto se controla con el código siguiente:

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Estos se implementan mediante `GetTargetViewControllerForAction` el método descrito en detalle anterior.

Cuando un controlador de vista de tabla muestra datos, usa los métodos implementados anteriormente para ver si se va a producir o no una inserciones y si se muestra o no el indicador de divulgación en consecuencia:

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nuevo `ShowDetailTargetDidChangeNotification` tipo

Apple ha agregado un nuevo tipo de notificación para trabajar con clases de tamaño y entornos de rasgos desde un controlador de `ShowDetailTargetDidChangeNotification`vista en dos paneles,. Esta notificación se envía cuando cambia la vista de detalles de destino de un controlador de vista en dos paneles, por ejemplo, cuando el controlador se expande o se contrae.

La aplicación Adaptive photos usa esta notificación para actualizar el estado del indicador de divulgación cuando cambia el controlador de vista de detalle:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Eche un vistazo más de cerca a la aplicación Adaptive photos para ver todas las formas en que las clases de tamaño, las colecciones de rasgos y los controladores de vistas adaptables se pueden usar para crear fácilmente una aplicación unificada en Xamarin. iOS.

## <a name="unified-storyboards"></a>Guiones gráficos unificados

Como novedad de iOS 8, los guiones gráficos unificados permiten al desarrollador crear un archivo de guion gráfico unificado que se puede mostrar en dispositivos iPhone y iPad mediante el destino de varias clases de tamaño. Mediante el uso de guiones gráficos unificados, el desarrollador escribe menos código específico de la interfaz de usuario y solo tiene un diseño de interfaz para crear y mantener.

Las ventajas principales de los guiones gráficos unificados son:

-  Use el mismo archivo de guion gráfico para iPhone y iPad.
-  Implementación inversa en iOS 6 e iOS 7.
-  Obtenga una vista previa del diseño de los distintos dispositivos, orientaciones y versiones del sistema operativo desde el diseñador de Xamarin iOS.

Esta característica es totalmente compatible con Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitar clases de tamaño

De forma predeterminada, cualquier nuevo proyecto de Xamarin. iOS nos dará tamaño a las clases. Para usar clases de tamaño y objetos segue adaptables dentro de un guion gráfico de un proyecto anterior, primero se debe convertir al formato de guion gráfico unificado Xcode 6 desde dentro del diseñador de iOS.

Para ello, abra el guion gráfico que se va a convertir en el diseñador de iOS y active la casilla **usar clases de tamaño** :

 [![](unified-storyboards-images/sizeclass01.png "Casilla Usar clases de tamaño")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS Designer confirmará que el desarrollador desea convertir el formato del guion gráfico para usar las clases de tamaño:

 [![](unified-storyboards-images/sizeclass02.png "Alerta usar clases de tamaño")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> También debe comprobarse el diseño automático para que las clases de tamaño funcionen correctamente.

### <a name="generic-device-types"></a>Tipos de dispositivos genéricos

Una vez que el guion gráfico se ha convertido para usar clases de tamaño, se volverá a mostrar en el Superficie de diseño y la **vista como** dispositivo será genérica:

 [![](unified-storyboards-images/sizeclass03.png "Ver como un tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Cuando se selecciona el tipo de dispositivo genérico, se cambia el tamaño de todos los controladores de vista a un cuadrado de 600 x 600. Este cuadrado representa los tamaños de cualquier ancho y cualquier alto. Cuando el diseñador de iOS está en este modo, cualquier edición se aplicará a todas las clases de tamaño.

El desarrollador también tiene la opción de ver la superficie de diseño como un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Visualización de la superficie de diseño como un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

O verlo como iPad:

 [![](unified-storyboards-images/sizeclass05.png "Ver la superficie de diseño como iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Seleccionar una clase de tamaño

El botón selector de clase de tamaño está en la esquina superior izquierda del Superficie de diseño (cerca de la vista como lista desplegable). Permite al desarrollador seleccionar las clases de tamaño que se están editando actualmente:

 [![](unified-storyboards-images/sizeclass06.png "Seleccionar una clase de tamaño")](unified-storyboards-images/sizeclass06.png#lightbox)

El selector presenta la selección de clase de tamaño como una cuadrícula de 3 x 3. Cada uno de los cuadrados de la cuadrícula representa una combinación de una clase width y una clase height. El cuadrado central selecciona la clase cualquier ancho/cualquier tamaño de alto (que es la vista predeterminada de un guión gráfico unificado). Cuando se selecciona este cuadrado, el desarrollador está editando el diseño predeterminado, que es heredado por todas las demás configuraciones.

El cuadrado situado en la esquina superior izquierda de la cuadrícula representa la clase de tamaño compacto/ancho compacto:

 [![](unified-storyboards-images/sizeclass07.png "La clase ancho compacto/tamaño de la altura compacta")](unified-storyboards-images/sizeclass07.png#lightbox)

Este modo corresponde a un iPhone en la orientación horizontal. El cuadrado situado en la esquina inferior derecha de la cuadrícula representa la clase de tamaño normal de ancho y alto normal, que representa un iPad:

 [![](unified-storyboards-images/sizeclass08.png "La clase de tamaño normal de ancho y alto normal")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar el diseño de un iPhone en orientación vertical, seleccione el cuadrado en la esquina inferior izquierda. Representa la clase de tamaño de ancho compacto/alto normal:

 [![](unified-storyboards-images/sizeclass09.png "La clase ancho compacto/tamaño de alto normal")](unified-storyboards-images/sizeclass09.png#lightbox)

Haga clic en el cuadrado para seleccionarlo y el Superficie de diseño cambiará el tamaño de los controladores de vista para que coincidan con la nueva selección:

 [![](unified-storyboards-images/sizeclass10.png "El Superficie de diseño cambiará el tamaño de los controladores de vista para que coincidan con la nueva selección, como se muestra")](unified-storyboards-images/sizeclass10.png#lightbox)

Vea la sección sobre la clase de tamaño de este artículo para obtener más información sobre las clases de tamaño y cómo afectan al diseño de iPhone y iPad.

### <a name="adaptive-segue-types"></a>Tipos de segue adaptables

Si el desarrollador ha usado guiones gráficos antes, estará familiarizado con los tipos de segue existentes delas inserciones, **modales** y **elemento flotante**. Cuando las clases de tamaño están habilitadas en un archivo de guion gráfico unificado, los siguientes tipos de segue adaptables (que corresponden a la nueva API de controlador de vista descritos anteriormente) están disponibles: **Mostrar** y **Mostrar detalles**.

> [!IMPORTANT]
> Cuando se habilitan las clases de tamaño, cualquier objetos segue existente se convertirá en los nuevos tipos.

Tome el ejemplo de una aplicación de iOS 8 que usa un guion gráfico unificado con un controlador de vista en dos paneles que tiene un menú de navegación de juegos simple en la vista maestra. Si el usuario hace clic en un botón de menú, el controlador de vista del elemento seleccionado debe mostrarse en la sección de detalles del controlador de vista en dos paneles cuando se ejecuta en un iPad. En un iPhone, el controlador de vista del elemento se debe insertar en la pila de navegación.

Para lograr este efecto, en el control del diseñador de iOS, haga clic en el botón y arrastre una línea hasta el controlador de vista que se va a mostrar. Cuando se suelte el botón del mouse, `Show Detail` seleccione en el menú emergente tipo de segue:

 [![](unified-storyboards-images/segue01.png "Seleccione Mostrar detalles en el menú emergente tipo de segue")](unified-storyboards-images/segue01.png#lightbox)

Se creará el nuevo segue entre el botón y el controlador de vista. Ahora ejecute la aplicación en el simulador de iPhone y se mostrará el menú principal:

 [![](unified-storyboards-images/segue02.png "Menú principal")](unified-storyboards-images/segue02.png#lightbox)

Haga clic en el botón **seleccionar juego** y el controlador de vista del elemento se insertará en la pila de navegación:

 [![](unified-storyboards-images/segue03.png "El controlador de vista de elementos se insertará en la pila de navegación como se muestra")](unified-storyboards-images/segue03.png#lightbox)

Detenga el simulador de iPhone y ejecute la aplicación en el simulador de iPad. Cambiar a la orientación horizontal y volver a mostrar el menú principal:

 [![](unified-storyboards-images/segue04.png "El menú principal que se muestra")](unified-storyboards-images/segue04.png#lightbox)

De nuevo, haga clic en el botón **seleccionar juego** y el controlador de vista del elemento se muestra en la sección de detalles del controlador de vista en dos paneles:

 [![](unified-storyboards-images/segue05.png "El controlador de vista de elementos mostrado en la sección de detalles del controlador de vista en dos paneles")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Exclusión de un elemento de una clase de tamaño

Hay ocasiones en las que un elemento determinado (como una vista, un control o una restricción) no es necesario dentro de una clase de tamaño específica. Para excluir un elemento de una clase de tamaño, seleccione el elemento que desee excluir en el **superficie de diseño**. Desplácese hasta la parte inferior del **Explorador de propiedades** y haga clic en el menú desplegable **engranaje** . Seleccione la combinación de **ancho** y **alto** del que se va a excluir el elemento:

[![](unified-storyboards-images/exclude-a.png "Seleccionar la combinación de ancho y alto")](unified-storyboards-images/exclude-a.png#lightbox)

Se agregará un nuevo *caso de exclusión* al elemento en la parte inferior del **Explorador de propiedades**. A continuación, desactive la casilla **instalado** de la clase de tamaño dado:

[![](unified-storyboards-images/exclude-b.png "Desactive la casilla instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Cambie el Superficie de diseño al ancho y el alto del que se excluyó el elemento, ya que se ha quitado de la clase de tamaño especificada, pero no del diseño de la interfaz de usuario completo:

 [![](unified-storyboards-images/exclude02.png "Cambiar el Superficie de diseño al ancho y el alto del que se excluyó el elemento")](unified-storyboards-images/exclude02.png#lightbox)

Volver a la clase any width/any height size y el elemento sigue en su lugar:

 [![](unified-storyboards-images/exclude03.png "Volver a la clase any width/any height size")](unified-storyboards-images/exclude03.png#lightbox)

Cuando la aplicación se ejecuta en el simulador de iPad, aparece el elemento:

 [![](unified-storyboards-images/exclude04.png "El elemento que se muestra cuando la aplicación en ejecución en el simulador de iPad")](unified-storyboards-images/exclude04.png#lightbox)

Y cuando la aplicación se ejecuta en el simulador de iPhone, falta el elemento:

 [![](unified-storyboards-images/exclude05.png "Falta el elemento cuando la aplicación en ejecución en el simulador de iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para quitar un caso de exclusión de un elemento, simplemente seleccione el elemento en el **superficie de diseño**, desplácese hasta la parte inferior del **Explorador de propiedades** y haga clic en el **-** botón situado junto al caso que se va a quitar.

Para ver una implementación de guiones gráficos unificados, consulte `UnifiedStoryboard` la aplicación de Xamarin iOS 8 de ejemplo asociada a este documento.

## <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámico

El archivo de pantalla de inicio se muestra como una pantalla de presentación mientras se inicia una aplicación de iOS para proporcionar comentarios al usuario de que la aplicación se está iniciando realmente. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` recursos de imagen para cada tipo de dispositivo, orientación y resolución de pantalla en la que se ejecutaba la aplicación. Por ejemplo, `Default@2x.png` `Default-Landscape@2x~ipad.png`,,,etc `Default-Portrait@2x~ipad.png`.

La factorización en los nuevos dispositivos iPhone 6 y iPhone 6 más (y en el próximo Apple Watch) con todos los dispositivos iPhone y iPad existentes, representa una gran variedad de tamaños variables, orientaciones y resoluciones `Default.png` de los recursos de imagen de la pantalla de inicio que deben crear y mantener. Además, estos archivos pueden ser bastante grandes y se "inflarán" el paquete de aplicaciones de entrega, lo que aumenta la cantidad de tiempo necesario para descargar la aplicación desde iTunes App Store (posiblemente para que se pueda entregar a través de una red de telefonía móvil) y el aumento de la cantidad de almacenamiento necesario en el dispositivo del usuario final.

Como novedad de iOS 8, el desarrollador puede crear un único archivo `.xib` atómico en Xcode que use las clases de diseño y tamaño automáticos para crear una *pantalla de inicio dinámico* que funcione en todos los dispositivos, resoluciones y orientaciones. Esto no solo reduce la cantidad de trabajo necesario para que el desarrollador cree y mantenga todos los recursos de imagen necesarios, pero reduce en gran medida el tamaño del paquete instalado de la aplicación.


Las pantallas de inicio dinámico tienen las siguientes limitaciones y consideraciones:

- Use solo `UIKit` clases.
- Use una vista raíz única que sea un `UIView` objeto `UIViewController` o.
- No realice ninguna conexión con el código de la aplicación (no agregue **acciones** ni **salidas**).
- No agregue `UIWebView` objetos.
- No utilice ninguna clase personalizada.
- No utilice atributos en tiempo de ejecución.

Teniendo en cuenta las instrucciones anteriores, echemos un vistazo a la adición de una pantalla de inicio dinámico a un proyecto existente de Xamarin iOS 8.

Haga lo siguiente:

1. Abra **Visual Studio para Mac** y cargue la **solución** para agregar la pantalla de inicio dinámico a.
2. En el **Explorador de soluciones**, haga clic con el `MainStoryboard.storyboard` botón derecho en el archivo y seleccione **abrir con** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Abrir con Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. En Xcode, seleccione **archivo** > **nuevo** > **archivo...** :

    [![](unified-storyboards-images/dls02.png "Seleccione Archivo/nuevo")](unified-storyboards-images/dls02.png#lightbox)
4. Seleccione la**pantalla de inicio** de la**interfaz** > de usuario de **iOS** > y haga clic en el botón **siguiente** :

    [![](unified-storyboards-images/dls03.png "Seleccione iOS/interfaz de usuario/pantalla de inicio")](unified-storyboards-images/dls03.png#lightbox)
5. Asigne un nombre `LaunchScreen.xib` al archivo y haga clic en el botón **crear** :

    [![](unified-storyboards-images/dls04.png "Asigne al archivo el nombre LaunchScreen. Xib")](unified-storyboards-images/dls04.png#lightbox)
6. Edite el diseño de la pantalla de inicio mediante la adición de elementos gráficos y el uso de restricciones de diseño para colocarlos en los dispositivos, las orientaciones y los tamaños de pantalla especificados:

    [![](unified-storyboards-images/dls05.png "Edición del diseño de la pantalla de inicio")](unified-storyboards-images/dls05.png#lightbox)
7. Guarde los cambios en `LaunchScreen.xib`.
8. Seleccione el **destino de las aplicaciones** y la pestaña **General** :

    [![](unified-storyboards-images/dls06.png "Seleccione el destino de las aplicaciones y la pestaña General")](unified-storyboards-images/dls06.png#lightbox)
9. Haga clic en el botón **elegir info. plist** , seleccione `Info.plist` para la aplicación Xamarin y haga clic en el botón **elegir** :

    [![](unified-storyboards-images/dls07.png "Seleccione el archivo info. plist de la aplicación Xamarin.")](unified-storyboards-images/dls07.png#lightbox)
10. En la sección iconos de la **aplicación e imágenes de inicio** , abra el menú desplegable de `LaunchScreen.xib` archivo de pantalla de **Inicio** y elija el creado anteriormente:

    [![](unified-storyboards-images/dls08.png "Elija el LaunchScreen. Xib")](unified-storyboards-images/dls08.png#lightbox)
11. Guarde los cambios en el archivo y vuelva a Visual Studio para Mac.
12. Espere a que Visual Studio para Mac termine de sincronizar los cambios con Xcode.
13. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta de **recursos** y seleccione **Agregar** > **Agregar archivos..** .:

    [![](unified-storyboards-images/dls09.png "Seleccione Agregar o agregar archivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Seleccione el `LaunchScreen.xib` archivo creado anteriormente y haga clic en el botón **abrir** :

    [![](unified-storyboards-images/dls10.png "Seleccione el archivo LaunchScreen. Xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile la aplicación.

### <a name="testing-the-dynamic-launch-screen"></a>Prueba de la pantalla de inicio dinámico

En Visual Studio para Mac, seleccione el simulador de retina de iPhone 4 y ejecute la aplicación. La pantalla de inicio dinámico se mostrará en el formato y la orientación correctos:

[![](unified-storyboards-images/dls11.png "La pantalla de inicio dinámico que se muestra en la orientación vertical")](unified-storyboards-images/dls11.png#lightbox)

Detenga la aplicación en Visual Studio para Mac y seleccione un dispositivo iPad iOS 8. Ejecute la aplicación y la pantalla de inicio tendrá el formato correcto para el dispositivo y la orientación:

[![](unified-storyboards-images/dls12.png "La pantalla de inicio dinámico que se muestra en la orientación horizontal")](unified-storyboards-images/dls12.png#lightbox)

Vuelva a Visual Studio para Mac y detenga la ejecución de la aplicación.

### <a name="working-with-ios-7"></a>Trabajar con iOS 7

Para mantener la compatibilidad con versiones anteriores de iOS 7, solo `Default.png` tiene que incluir los recursos de imagen habituales de la forma habitual en la aplicación iOS 8. iOS volverá al comportamiento anterior y usará esos archivos como pantalla de inicio cuando se ejecute en un dispositivo iOS 7.

Para ver una implementación de una pantalla de inicio dinámico en Xamarin, consulte la aplicación de ejemplo de [pantallas de inicio dinámicos](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen) de iOS 8 adjuntada a este documento.

## <a name="summary"></a>Resumen

En este artículo se ha examinado rápidamente las clases de tamaño y cómo afectan al diseño en dispositivos iPhone y iPad. Se ha explicado cómo funcionan los rasgos, los entornos de rasgos y las colecciones de rasgos con clases de tamaño para crear interfaces unificadas. Ha tenido un breve vistazo a los controladores de vistas adaptables y cómo funcionan con clases de tamaño dentro de interfaces unificadas. En ella, se examinó la implementación de clases de tamaño C# y de interfaces unificadas por completo desde el código dentro de una aplicación Xamarin iOS 8.

Por último, en este artículo se han tratado los conceptos básicos de la creación de guiones gráficos unificados con Xamarin iOS Designer que funcionará en dispositivos iOS y creará una única pantalla de inicio dinámico que se mostrará como pantalla de inicio en cada dispositivo iOS 8.

## <a name="related-links"></a>Vínculos relacionados

- [Fotos adaptables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [Pantallas de inicio dinámico (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Diseños dinámicos en iOS8: evolucione 2014 (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
