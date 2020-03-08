---
title: Trabajar con iconos e imágenes de tvOS en Xamarin
description: En este documento se describe cómo trabajar con iconos e imágenes en una aplicación de tvOS compilada con Xamarin. Se describen las imágenes de inicio, las imágenes superpuestas, el icono de la aplicación y mucho más.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b1b6d07b221f702b54833bd87161d6abbadbd4e8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915786"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Trabajar con iconos e imágenes de tvOS en Xamarin

Crear iconos e imágenes atractivas es una parte fundamental del desarrollo de una experiencia de usuario envolvente para las aplicaciones de Apple TV. En esta guía se explican los pasos necesarios para crear e incluir los recursos gráficos necesarios para las aplicaciones de Xamarin. tvOS:

- [Iniciar imagen](#Launch-Image) : se muestra una imagen de inicio cuando la aplicación se inicia por primera vez y se reemplaza por la primera pantalla de la aplicación una vez que se ha completado el inicio.
- [Imágenes por capas](#Layered-Images) : específicas de Apple TV, las nuevas imágenes superpuestas de Apple funcionan con el efecto de Parallax para crear un efecto 3D para los elementos seleccionados. Hay varias maneras de [crear imágenes por capas](#Creating-Layered-Images).
- [Icono de aplicación](#App-Icons) : los iconos son necesarios para no solo la pantalla principal de Apple TV, sino para la tienda de aplicaciones. Deben proporcionarse como una imagen en capas.
- [Imagen de estante superior](#Top-Shelf-Image) : Si la aplicación se coloca en la fila superior de la pantalla principal, necesitará una imagen de estante superior para resaltar las características de la aplicación. Opcionalmente, puede proporcionar [contenido dinámico dinámico](#Dynamic-Top-Shelf-Content) para resaltar el contenido de la aplicación.
- [Game Center imágenes](#Game-Center-Images) : Si la aplicación es un juego y usa Game Center, se necesitarán varias imágenes adicionales.
- [Configuración de imágenes de proyecto de Xamarin. tvOS](#Setting-Xamarin.tvOS-Project-Images) : describe los pasos necesarios para establecer el icono de la aplicación y la imagen de inicio de la aplicación Xamarin. tvOS.

> [!IMPORTANT]
> Todas las imágenes de Apple TV se encuentran en la resolución 1x (`@1x`) y _solo_ se deben usar imágenes de este tamaño. Los gráficos más grandes y de mayor resolución no solo tardan tiempo en descargar y usar más memoria y almacenamiento, pero tienen que cambiarse dinámicamente en tiempo de ejecución y afectarán negativamente al rendimiento del dibujo.

<a name="Launch-Image" />

## <a name="launch-image"></a>Imagen de inicio

La imagen de inicio es lo primero que se muestra cuando la aplicación de Xamarin. tvOS se inicia inicialmente en Apple TV y, como tal, cada aplicación de tvOS debe proporcionar una imagen de inicio. 

La imagen de inicio aparece rápidamente y da la impresión de que la aplicación es rápida y con capacidad de respuesta. Apple TV reemplazará la imagen de inicio por la primera pantalla de la aplicación en breve.

Las imágenes de inicio no son una oportunidad para anuncios o expresiones artísticas, solo existen para dar la impresión de que la aplicación se inicia rápidamente y está lista para usarse.

|Tamaño de la imagen de inicio|Notas|
|---|---|
|1920x1080px|Solo archivos. png no superpuestos|

Apple realiza las siguientes sugerencias para diseñar la imagen de inicio de la aplicación:

- **Casi idéntico a la primera pantalla** : la pantalla de inicio debe estar tan cerca de la primera pantalla de la aplicación como sea posible. La inclusión de distintos gráficos o elementos puede dar lugar a una "intermitencia" cuando aparece la primera pantalla.
- **Evite usar** imágenes de inicio de texto son estáticas y, por tanto, no se traducirán antes de mostrarse.
- **Inicio de minimizar** : dado que los usuarios de Apple TV cambian a menudo las aplicaciones, no debe atraer la atención al proceso de inicio de la aplicación.
- **No hay anuncios ni marcas** : la imagen de inicio no debe usarse como pantalla de información o incluir cualquier marca, a menos que sea parte estática de la primera pantalla de la aplicación. Los anuncios están estrictamente prohibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Establecimiento de la imagen de inicio

Para establecer la imagen de inicio del proyecto de tvOS, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo para su edición: 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. En el **Editor de recursos**, haga clic en el recurso `LaunchImages`: 

    [![](icons-images-images/asset02.png "The LaunchImages asset")](icons-images-images/asset02.png#lightbox)
3. Haga clic en la entrada **1x Apple TV** y seleccione la imagen de inicio o, si lo desea, arrastre una nueva imagen del sistema de archivos: 

    [![](icons-images-images/asset03.png "Select a Launch Image")](icons-images-images/asset03.png#lightbox)
4. Guarde los cambios.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imágenes superpuestas

Novedad de Apple TV, las imágenes superpuestas funcionan con el efecto de Parallax para producir un efecto en 3D que ayuda a mantener al usuario en el sofá mentalmente conectado al contenido en la pantalla de la habitación.

Las imágenes superpuestas contienen de dos (2) a cinco (5) capas separadas que se combinan para formar una imagen completa. A excepción de la capa de fondo, cada capa usa su orden Z junto con la transparencia para crear una ilusión de profundidad. Cuando el usuario interactúa con una imagen por capas, se escalan y superponen las capas ordenadas por Z más altas para crear este efecto.

[![](icons-images-images/layered01.png "Layered Images Z-ordered diagram")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Las imágenes superpuestas son necesarias para los iconos de la aplicación y son opcionales para otros elementos que pueden recibir el [foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (como la imagen de estante superior). Sin embargo, Apple sugiere el uso de imágenes superpuestas para cualquier imagen que pueda centrarse en la aplicación.

Apple realiza las siguientes sugerencias para diseñar imágenes por capas:

- **Hacer que la capa de fondo sea opaca** : la capa de fondo (capa 1) **debe** ser opaca o se producirá un error al intentar usar la imagen en capas en Apple TV. Todas las demás capas pueden contener varios niveles de transparencia para mejorar el efecto 3D.
- **Aislar los elementos de primer plano, medio y segundo plano** : Coloque los elementos destacados (como los caracteres de juego) en primer plano, use el centro para los elementos secundarios o las sombras. Por último, incluya un fondo neutro para proporcionar una fase para los niveles superiores.
- **Mantener el texto en primer plano** : a menos que desee que el texto se oculte en niveles superiores, generalmente debe estar en el nivel superior.
- **Usar la disposición en capas simple** : el efecto de Parallax se diseñó para que sea sutil, por lo que debe mantener los niveles mínimos para evitar que los discordante, los efectos no reales.
- **Incluir una zona segura** : como las capas superiores se pueden recortar durante un efecto de Parallax, debe crear un borde de zona segura en cada capa. Si consigue que el contenido esté demasiado cerca del borde de las capas, puede recortarse. Las capas superiores experimentarán más escalado y recortando que las capas inferiores. Vea la sección ajustar el tamaño de las [capas de imagen](#Sizing-Image-Layers) a continuación.
- **Vista previa** de las imágenes con capas a menudo se debe obtener una vista previa con frecuencia para asegurarse de que se produce el efecto 3D deseado y que no se va a recortar ningún contenido de los niveles individuales. Debe obtener una vista previa de las imágenes por capas en hardware de Apple TV real para asegurarse de que se representan según lo previsto.

Siempre que sea posible, siempre debe usar los controles `UIKit` integrados para mostrar las imágenes en capas, ya que obtendrán automáticamente el efecto parallax cuando entren en el foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Ajustar el tamaño de las capas de imagen

Es importante recordar incluir un borde de _zona segura_ en cada capa que componga la imagen por capas. Dado que las capas individuales se pueden escalar y recortar durante el efecto de Parallax, el contenido de las capas se puede recortar si está demasiado cerca del borde de la capa:

[![](icons-images-images/layered02.png "35 pixel border")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Crear imágenes superpuestas

tvOS funciona con imágenes superpuestas en los siguientes formatos:

- **Archivos de automóvil** : es un formato de catálogo de recursos propietario creado por Apple. No se crean directamente archivos de automóvil, sino que se crean en tiempo de compilación desde cualquier archivo LSR y se incluyen en el lote de aplicaciones.
- **Imágenes de LSR** : se trata de un formato de imagen de propiedad creado por Apple. Use el [complemento de Adobe Photoshop exportador de Parallax](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) o el [previsor de Parallax](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para crear y obtener una vista previa de las imágenes en capas en el formato LSR.
- **Assets. xcassets** : de dos (2) a cinco (5) estándar `.png` imágenes con formato incluidas en un catálogo de recursos que se compilarán en un coche o en una imagen en capas con formato LSR en tiempo de compilación.
- **Archivos de LCR** : se trata de un formato de archivo propietario creado por Apple. Los archivos de LCR están diseñados para usarse como contenido adicional descargado de uno de los servidores de contenido. El archivo LCR nunca debe incluirse en el lote de aplicaciones. Los archivos de LCR se generan a partir de archivos de LSR o Photoshop mediante la `layerutil` herramienta de línea de comandos incluida con Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>Vista previa de Parallax

Apple creó el [previsor de Parallax](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para obtener una vista previa y crear las imágenes en capas necesarias para los iconos de la aplicación y los elementos opcionales que permiten el foco. El previsor muestra cada capa que forma la imagen en capas completa:

[![](icons-images-images/layered03.png "The Parallax Previewer")](icons-images-images/layered03.png#lightbox)

Al obtener una vista previa de una imagen en capas, puede usar el mouse para girar la imagen y obtener una vista previa del efecto de Parallax. Use los botones **+** (más) y **-** (menos) para agregar y quitar capas.

Al crear una nueva imagen en capas, puede exportarse en el formato LSR y incluirse en el lote de la aplicación.

Para obtener más información sobre cómo crear y obtener una vista previa de las imágenes superpuestas, consulte la sección [creación de ilustraciones Parallax](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) de Apple de la [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Iconos de la aplicación

La aplicación Xamarin. tvOS necesitará no solo un icono de aplicación para la pantalla principal de Apple TV, sino también un icono para la tienda de aplicaciones. El icono de la aplicación es el primer cambio para crear una gran impresión para el usuario potencial y debe comunicar el propósito de la aplicación de un vistazo.

[![](icons-images-images/icon01.png "The App Icon")](icons-images-images/icon01.png#lightbox)

Cada aplicación debe proporcionar una versión pequeña y una grande de su icono de aplicación. El icono pequeño se usará en la pantalla principal de Apple TV cuando se instale la aplicación. La versión grande se usa en el App Store. El icono de la aplicación grande debe imitar la apariencia de la versión del icono pequeño.

|Icono pequeño||Icono grande||
|---|---|---|---|
|Tamaño real|400x240px|Size|1280x768px|
|Tamaño de zona segura|370x222px|||
|Tamaño sin foco|300x180px|||
|Tamaño centrado|370x222px|||

> [!IMPORTANT]
> Los iconos de la aplicación se deben proporcionar como **imágenes por capas**. Consulte la sección anterior de la [imagen en capas](#Layered-Images) para obtener más detalles.

Apple proporciona las siguientes sugerencias para crear iconos de aplicación:

- **Proporcionar un único punto de enfoque** : diseñe el icono con un solo punto de enfoque colocado directamente en el centro del icono.
- **Usar un fondo sencillo** : Mantenga el fondo del icono de forma simple para que las capas superiores destaquen. Considere la posibilidad de usar un color simple o un degradado sutil.
- **Limitar la cantidad de texto** : puesto que el nombre de la aplicación aparecerá debajo del icono cuando lo seleccione el usuario, solo debe incluir texto cuando sea esencial para el diseño del icono.
- **No usar capturas de pantallas** : las capturas de pantallas son demasiado complejas para un icono y no permiten al usuario ver el propósito de la aplicación de un vistazo.
- **Mantener iconos cuadrado** : tvOS aplica automáticamente una máscara que redondea sutilmente las esquinas de los iconos. No incluya este redondeo.
- **Separe las capas con cuidado** : el texto debe estar en la capa superior, en los elementos secundarios del centro y en un fondo neutro que permita que las capas superiores se destaquen.
- **Usar degradados y sombras con cuidado** : los degradados y las sombras pueden entrar en conflicto con el efecto parallax, por lo que deben usarse con cuidado. Los estilos de degradado de arriba a abajo simples y oscuros funcionan mejor. Normalmente, las sombras funcionan mejor como tonos nítidos.
- **Variar la transparencia** de la capa: Use distintos niveles de transparencia en los niveles superiores del icono de la aplicación para aumentar el efecto 3D. La capa de fondo debe ser opaca o se producirá un error.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Establecer los iconos de la aplicación

Para establecer los iconos de la aplicación necesarios para el proyecto tvOS, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo para su edición: 

    [![](icons-images-images/asset01.png "The Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. En el **Editor de recursos**, expanda el recurso `App Icon & Top Shelf Image`: 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. A continuación, expanda el recurso `App Icon - Small`: 

    [![](icons-images-images/asset05.png "Expand the App Icon - Small asset")](icons-images-images/asset05.png#lightbox)
4. A continuación, expanda el recurso `Back` y haga clic en la entrada `Contents`: 

    [![](icons-images-images/asset06.png "Then expand the Back asset")](icons-images-images/asset06.png#lightbox)
5. Haga clic en la **entrada 1x Apple TV** y seleccione un archivo de imagen.
6. Repita los pasos anteriores para los recursos de `Front` y `Middle`.
7. A continuación, repita los mismos pasos para definir el recurso de `App Icon - Large`.
8. Guarde los cambios.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagen de estante superior

Si el usuario ha colocado la aplicación Xamarin. tvOS en la fila superior de la pantalla de inicio de Apple TV, se mostrará una imagen de estante superior grande cuando el usuario seleccione la aplicación. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](icons-images-images/topshelf01.png "Top Shelf Image example")](icons-images-images/topshelf01.png#lightbox)

La imagen de estante superior se puede proporcionar como un solo archivo estático de `.png` o `.lsr` (consulte [creación de imágenes por capas](#Creating-Layered-Images)) o se puede crear dinámicamente en tiempo de ejecución como una sola fila de elementos que pueden recibir el foco (vea el contenido de [estante superior dinámico](#Dynamic-Top-Shelf-Content) a continuación).

|Tamaño de la imagen de estante superior|Notas|
|---|---|
|1920x720px|Archivo. png o. LSR en capas.|

Apple proporciona las siguientes sugerencias para crear las imágenes de estante superior:

- **Usar imágenes estáticas enriquecidas** : Si la aplicación no proporciona un contenido dinámico, su imagen de estante superior no será enfocable. Use esta imagen para resaltar las características de la aplicación o la marca.
- **Vínculo a contenido de la aplicación** : los diseños de estante dinámico dinámicos proporcionan un vínculo rápido al contenido que el usuario encuentra más importante en la aplicación. Use esta área para proporcionar un vínculo rápido para iniciar la aplicación e ir directamente al contenido determinado.
- **Exhibir el contenido más reciente: el** contenido de estante superior enriquecido puede dibujar un usuario en la aplicación y hacer que quiera usarlo más. Úselo como un área para mostrar el contenido más alto o más calificado.
- **Contenido personalizado** : los usuarios colocan sus aplicaciones favoritas o usadas con más frecuencia en la fila superior de la pantalla principal. Use la estantería superior para mostrar el contenido que más le interese.
- **No se permiten anuncios** : los anuncios están estrictamente prohibidos para mostrarse en la estante superior. Puede mostrar el contenido de compra más reciente, pero no se debe mostrar información de precios.

### <a name="setting-the-top-shelf-image"></a>Establecimiento de la imagen de estante superior

Para establecer la imagen de estante superior necesaria para el proyecto tvOS, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en `Assets.xcassets` para abrirlo para su edición: 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. En el **Editor de recursos**, expanda el recurso `App Icon & Top Shelf Image`: 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. Haga clic en el recurso de `Top Shelf Image`: 

    [![](icons-images-images/asset07.png "The Top Shelf Image asset")](icons-images-images/asset07.png#lightbox)
4. Haga clic en la **entrada 1x Apple TV** y seleccione un archivo de imagen.
5. Guarde los cambios.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Contenido dinámico de estante superior

En lugar de mostrar una imagen de estante superior estática, la estante superior puede contener una fila dinámica de [elementos enfocables](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) o un conjunto dinámico de banners de desplazamiento. Ambos estilos dinámicos le permiten resaltar el contenido proporcionado por la aplicación o saltar a sus características más usadas.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Fila de contenido con sección

Este tipo de contenido de estante superior dinámico presenta una sola fila de desplazamiento, los elementos que se puede enfocar opcionalmente divididos en secciones. Normalmente se usa para resaltar el contenido de la aplicación nuevo, favorito o recientemente visto.

El contenido se presenta como una sola lista de desplazamiento horizontal de contenido con una etiqueta que aparece en la parte actual del contenido seleccionado (que actualmente tiene el foco). Si el usuario selecciona un fragmento de contenido determinado, se iniciará la aplicación y se tomará directamente en ese contenido.

Se necesitarán los siguientes tamaños de contenido:

||Póster (2:3)|Cuadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamaño real|404x608px|608x608px|908x512px|
|Tamaño de zona segura|380x570px|570x570px|852x479px|
|Tamaño sin foco|333x500px|500x500px|782x440px|
|Tamaño centrado|380x570px|570x570px|852x479px|

Apple proporciona las siguientes sugerencias para la fila de contenido con sección:

- **Completar la fila** : debe proporcionar suficiente contenido para abarcar el ancho completo de la pantalla.
- **Escalado de imágenes mixtas** : la fila de contenido con sección se diseñó para contener una combinación de tamaños de imagen (de la lista proporcionada anteriormente). Sin embargo, si tiene que mezclar tamaños de imagen, tenga en cuenta que se aplicará un escalado adicional para normalizar la presentación de contenido.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Desplazar banners de bajorrelieve

Opcionalmente, la aplicación Xamarin. tvOS puede presentar su contenido en el estante superior como una colección de banners que se desplaza automáticamente y que se repite en la pantalla. Este estilo se usa normalmente para exhibir contenido nuevo y completo, como los nuevos programas de TV.

Además del desplazamiento automático, el usuario puede tomar el control de los titulares y desplazarse en cualquier dirección mediante el Remote Siri. Si se realiza un pequeño gesto circular en el Siri remoto cuando un banner está en el foco, se activará el efecto de Parallax para ese banner.

**Imagen de banner (extra ancha)**

|   |   |
|---|---|
|Tamaño real|1940x624px|
|Tamaño de zona segura|1740x620px|
|Tamaño sin foco|1740x560px|
|Tamaño centrado|1740x620px|

Los banners de bajorrelieve desplazables se pueden proporcionar como un `.png` estático o un archivo `.lsr` superpuesto.

Apple proporciona las siguientes sugerencias para los banners de bajorrelieve de desplazamiento:

- **Importe del contenido** : debe proporcionar un mínimo de tres (3) banners para que el desplazamiento parezca natural. No debe incluir más de ocho (8) banners o hacer que la navegación sea difícil para el usuario final.
- **Texto de contenido** : Si el banner requiere texto en, debe incluirse en la imagen del banner. Si usa imágenes por capas, el texto debe estar en el nivel superior.

Consulte la referencia de [TVServices Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) de Apple para más información sobre cómo agregar una extensión de estante superior a la aplicación para proporcionar contenido dinámico dinámico.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Imágenes de Game Center

Si la aplicación Xamarin. tvOS es un juego y ha incluido Game Center soporte técnico, se necesitarán varios recursos de imagen más:

- **Iconos de logros** : se requiere una imagen opaca para cada logro que se recortará automáticamente en un círculo. Los logros son elementos que no pueden recibir el foco.
- **Ilustración del panel** : se puede proporcionar una imagen opcional que aparecerá en la parte superior del panel de la aplicación en Game Center. Estas imágenes no tienen el foco.
- **Material gráfico de marcadores** : debe proporcionar entre una (1) a tres (3) 16:9 imágenes de relación de aspecto para cada marcador que admita la aplicación. Estos pueden ser estáticos `.png` o archivos `.lsr` superpuestos. La ilustración del marcador tiene el foco.

||Iconos de logros|Ilustración del panel|Material gráfico de marcador|
|---|---|---|---|
|Tamaño visible|200x200px|923x150px|N/D|
|Tamaño real|320x320px|N/D|659x371px|
|Tamaño de zona segura|N/D|N/D|618x348px|
|Tamaño sin foco|N/D|N/D|548x309px|
|Tamaño centrado|N/D|N/D|618x348px|

Para obtener más información sobre cómo trabajar con Game Center, consulte la [Guía de programación de Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)de Apple.

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabajar con imágenes

Como tvOS 9 es un subconjunto de iOS 9, las mismas técnicas que se usan para incluir y mostrar imágenes en una aplicación de Xamarin. iOS también funcionan para una aplicación Xamarin. tvOS. Para obtener más información, consulte la documentación para [Mostrar una imagen](~/ios/app-fundamentals/images-icons/displaying-an-image.md) .

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Configuración de imágenes de proyecto de Xamarin. tvOS

Como se indicó anteriormente, todas las aplicaciones de tvOS requieren una [imagen de inicio](#Launch-Image)y un [icono de aplicación](#App-Icons). En esta sección se describe cómo seleccionar el icono iniciar imagen y aplicación para el proyecto de aplicación Xamarin. tvOS una vez que se han establecido en un catálogo de recursos.

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el `Info.plist` para abrirlo para su edición: 

    [![](icons-images-images/info01.png "The Info.plist file")](icons-images-images/info01.png#lightbox)
2. En el **Editor info. plist**, seleccione el catálogo de recursos (configurado anteriormente en la sección [configuración de los iconos de la aplicación](#Setting-the-App-Icons) ) para los iconos de la **aplicación**: 

    [![](icons-images-images/info02.png "The Info.Plist Editor")](icons-images-images/info02.png#lightbox)
3. Después, seleccione el catálogo de activos (configurado anteriormente en la sección [configuración de la imagen de inicio](#Setting-the-Launch-Image) ) para las **imágenes de inicio**.
4. Guarde los cambios.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han tratado todos los tipos y tamaños de imagen que se usan en una aplicación Xamarin. tvOS. En primer lugar, incluye imágenes de inicio, imágenes superpuestas, iconos de aplicaciones, imágenes de estante superior e imágenes de Game Center. Después, se ha tratado el uso de imágenes en la aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
