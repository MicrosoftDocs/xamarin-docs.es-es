---
title: Configuración de una aplicación en iTunes Connect
description: Este artículo describe los pasos necesarios para configurar y mantener una aplicación Xamarin.iOS en iTunes Connect para que se pueda publicar y distribuir en la App Store.
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 910f91478eeb6894e11748c929cc94bc2c0dbe64
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283591"
---
# <a name="configuring-an-app-in-itunes-connect"></a>Configuración de una aplicación en iTunes Connect

> [!IMPORTANT]
> Apple [ha comunicado](https://developer.apple.com/ios/submit/) que, a partir de marzo de 2019, las aplicaciones y actualizaciones que se envíen al App Store deberán haberse compilado con el SDK de iOS 12.1 o posterior, incluido en Xcode 10.1 y versiones posteriores.
> Las aplicaciones también deberán admitir los tamaños de pantalla del iPhone XS y el iPad Pro de 12.9".

iTunes Connect es un conjunto de herramientas basadas en web que, entre otras cosas, permiten administrar aplicaciones de iOS en la App Store. Una aplicación Xamarin.iOS deberá estar correctamente instalada y configurada en iTunes Connect para poder enviarla a Apple para su revisión y publicación como una aplicación gratuita o para su venta en la App Store.

iTunes Connect puede utilizarse para lo siguiente:

- Establecer el nombre de la aplicación (como se muestra en la App Store).
- Proporcionar capturas de pantalla o vídeos de la aplicación en acción en los dispositivos iOS con los que es compatible.
- Proporcionar una descripción clara y concisa de la aplicación con sus características y beneficios para el usuario final.
- Proporcionar categorías y subcategorías para ayudar al usuario a encontrar la aplicación en la App Store.
- Proporcionar cualquier palabra clave que pueda ayudar más a un usuario a encontrar la aplicación.
- Proporcionar direcciones URL a su sitio web de contacto y soporte técnico (requerido por Apple).
- Establecer la clasificación de la aplicación, que se usa para informar de controles parentales en la App Store.
- Seleccionar un precio de venta o especificar que la aplicación se publicará de forma gratuita.
- Configurar tecnologías opcionales de la App Store como Game Center y compras desde la aplicación.

Además, la aplicación también debe tener ilustraciones atractivas y de alta resolución disponibles en caso de que Apple decida destacarla en la App Store. Para obtener más información, consulte la [Guía del desarrollador de iTunes Connect](https://developer.apple.com/support/itunes-connect/) de Apple.

## <a name="managing-agreements-tax-and-banking"></a>Administración de contratos, impuestos y banca

La sección **Contratos, impuestos y banca** de iTunes Connect se usa para proporcionar información financiera necesaria relacionada con las retenciones de impuestos y los pagos a los desarrolladores de iTunes, y para realizar un seguimiento del estado de los acuerdos que tenga con Apple. Antes de publicar una aplicación de iOS en la App Store (ya sea de forma gratuita o para su venta), debe disponer de los acuerdos adecuados y haber aceptado las modificaciones a los acuerdos existentes.

[![](itunesconnect-images/agreement01.png "Administración de contratos, impuestos y banca")](itunesconnect-images/agreement01.png#lightbox)

Desde aquí, puede:

- Proporcionar un **Agente de equipo** y definir otros roles de usuario para la cuenta de iTunes Connect, como **Administrador** o **Finanzas**.
- Inscribirse en y mantener **contratos** que permitan a una organización distribuir aplicaciones en la App Store.
- Especificar el nombre de la **entidad jurídica** (nombre de vendedor en la App Store) utilizado para hacer que coincidan los contratos, la información bancaria y la información fiscal que están asociados a su organización.
- Proporcionar información de **banca** e **impuestos** si va a vender aplicaciones a través del App Store.

De nuevo, esta información _debe_ establecerse correctamente, ocupar su lugar y debe estar actualizada antes de enviar una aplicación de iOS a iTunes Connect para su revisión y publicación. Para obtener más información, consulte la documentación de [Administración de contratos, impuestos y banca](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1) de Apple.

<a name="creating" />

## <a name="creating-an-itunes-connect-record"></a>Crear un registro de iTunes Connect

Antes de que una aplicación Xamarin.iOS se pueda cargar en iTunes Connect para su distribución a través del App Store, debe crear un registro de la aplicación en iTunes Connect. Este registro incluye toda la información acerca de la aplicación, tal como aparecerá en la App Store (en tantos idiomas como sea necesario) y toda la información necesaria para administrar la aplicación a través del proceso de distribución. Además, se utilizará para configurar las tecnologías de la App Store, como el Ad App Network o el Game Center.

Para agregar una aplicación de iOS en iTunes Connect, debe ser el **agente de equipo** o un usuario con un rol de **administrador** o **técnico**.

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**:

    [![](itunesconnect-images/add01.png "Clic en Mis aplicaciones")](itunesconnect-images/add01.png#lightbox)
2. Haga clic en el **+** en la esquina superior izquierda y seleccione **Nueva aplicación de iOS**:

    [![](itunesconnect-images/add02.png "Agregar una nueva aplicación de iOS")](itunesconnect-images/add02.png#lightbox)
3. iTunes Connect mostrará el cuadro de diálogo **Nueva aplicación de iOS**:

    [![](itunesconnect-images/add03.png "Cuadro de diálogo Nueva aplicación de iOS")](itunesconnect-images/add03.png#lightbox)
4. Escriba el **nombre** y **número de versión** de la aplicación como desea que se muestren en la App Store.
5. Seleccione el **idioma principal**.
6. Escriba un número de **SKU**, se trata de un identificador único constante que se utiliza para el seguimiento de la aplicación. No se mostrará al usuario final y _no_ puede cambiarse una vez se haya creado la aplicación.
7. Seleccione el **Id. de paquete** para la aplicación que creó en el centro de desarrollo al aprovisionar la aplicación. Este mismo identificador de paquete deberá utilizarse al firmar el paquete de distribución en Visual Studio para Mac o Visual Studio. Para obtener más información, consulte nuestra documentación sobre [crear un perfil de distribución](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) y [seleccionar un perfil de distribución en un proyecto de Xamarin.iOS](~/ios/get-started/installation/device-provisioning/index.md).
8. Haga clic en el botón **Crear** para generar el nuevo registro de iTunes Connect para la aplicación.

La nueva aplicación se creará en iTunes Connect y estará lista para que rellene la información necesaria, como la descripción, los precios, las categorías, las clasificaciones, etc.:

[![](itunesconnect-images/add04.png "Creación de la nueva aplicación en iTunes Connect")](itunesconnect-images/add04.png#lightbox)

<a name="managing" />

## <a name="managing-app-videos-and-screenshots"></a>Administración de vídeos y capturas de pantalla de aplicaciones

Uno de los elementos más importantes para publicitar correctamente su aplicación de iOS en la App Store es un gran conjunto de capturas de pantalla y, opcionalmente, mostrar vistas previas en vídeo. Use vistas reales de la aplicación en ejecución que resalten la interacción del usuario y presenten sus características exclusivas. Use vistas previas en vídeo de aplicación para dar a los usuarios una idea de cómo se usa la aplicación.

Apple sugiere lo siguiente al realizar capturas de pantalla:

- Optimice la captura de pantalla para una mejor presentación en los dispositivos iOS compatibles con la aplicación y asegúrese de que el contenido es legible.
- No enmarque la captura de pantalla de una imagen de dispositivo iOS.
- Muestre la vista real de la aplicación, usando la pantalla completa, sin gráficos ni bordes alrededor de la captura de pantalla.
- Quite siempre la barra de estado de las capturas de pantalla, ya que iTunes Connect espera capturas de pantalla de dimensiones que excluyen ese área.
- Cuando sea posible, realice capturas de pantalla en hardware de iOS Retina real y de alta resolución (no en un simulador de iOS).
- La primera captura de pantalla aparece como un resultado de búsqueda en App Store en iPhone y iPad si no hay disponible ningún vídeo de vista previa de la aplicación, por lo que se recomienda que coloque la mejor captura en primer lugar.
- Use las cinco capturas de pantalla para contar la historia de la aplicación y resaltar momentos que la vuelvan atractiva.

Apple requiere que las capturas de pantalla y los vídeos se proporcionen en cada tamaño de pantalla y resolución que admita la aplicación. Además, deben proporcionarse versiones en vertical y horizontal, según las orientaciones que admita.

Los siguientes tamaños y resoluciones son necesarias actualmente:

[!include[](~/ios/includes/table-itunesconnect.md)]

### <a name="editing-screenshots-in-itunes-connect"></a>Edición de capturas de pantalla en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Versiones**.
4. Desplácese hasta la sección **Capturas de pantalla**.
5. Seleccione el **tamaño de la imagen** y arrastre las imágenes necesarias (hasta 5 por cada tamaño de pantalla):

    [![](itunesconnect-images/screenshot01.png "Selección del tamaño de la imagen y arrastrar las imágenes necesarias")](itunesconnect-images/screenshot01.png#lightbox)
6. Repita el proceso para todos los tamaños de pantalla necesarios.
7. Haga clic en el botón **Guardar** situado en la parte superior de la pantalla para guardar los cambios.

> [!NOTE]
> Nota: Apple rechazará su envío si las capturas de pantalla o el vídeo de vista previa de la aplicación no coinciden con las funcionalidades de la aplicación actual.

<a name="metadata" />

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>Administración de nombres, descripciones, novedades, palabras clave y direcciones URL

Esta sección del registro de aplicación de iTunes Connect proporciona información localizada sobre la aplicación, lo que hace, las modificaciones de las nuevas versiones, palabras clave utilizadas en la búsqueda y URL de soporte técnico de iAd u otros servicios de soporte técnico.

### <a name="app-name"></a>Nombre de la aplicación

Elija un nombre descriptivo para la aplicación que refleje lo que hace. Procure que sea lo más corto y conciso posible. El nombre de la aplicación desempeña un papel fundamental en la forma en que los usuarios la buscarán y descubrirán, por lo que se recomienda que sea un nombre sencillo y fácil de recordar. Preste especial atención a cómo aparece el nombre cuando se visualiza en un dispositivo iOS (iPad, iPhone y iPod touch).

Apple sugiere las siguientes directrices al elegir el nombre de la aplicación:

- Haga que sea corto, simple y fácil de recordar.
- Asegúrese de que no infringe el copyright o las marcas comerciales de un tercero.
- Hágalo coincidir con la funcionalidad de la aplicación.
- Proporcione nombres localizados para mercados externos cuando proceda.

### <a name="description"></a>DESCRIPCIÓN

Escriba una descripción clara, concisa e informativa de la aplicación y sus características. Las primeras líneas son las más importantes y le dan una oportunidad para causar una primera impresión excelente y para atraer al usuario. Describa lo que hace que su aplicación sea especial y la diferencie de otras similares.

Apple sugiere lo siguiente para escribir la descripción de la aplicación:

- Incluya uno o dos párrafos de apertura breves y una breve lista con viñetas de las características principales.
- Proporcione descripciones localizadas para mercados externos cuando proceda.
- Incluya opiniones de usuarios, premios o testimonios solo al final, si es que lo hace.
- Utilice saltos de línea y viñetas para mejorar la legibilidad.
- Tenga en cuenta cómo se muestra la descripción de la aplicación en la App Store en cada tipo de dispositivo, para asegurarse de que las oraciones más importantes de la descripción sean legibles.

### <a name="whats-new"></a>Novedades

Al cargar una nueva versión de la aplicación, estará disponible un campo **Novedades en esta versión** que se debe completar exhaustiva y cuidadosamente.

Apple sugiere las siguientes directrices al rellenar la información de Novedades en esta versión:

- Agregue mensajes para animar a los usuarios a actualizar.
- Muestre elementos en orden de importancia y destaque los cambios y las correcciones de errores.
- Presente los cambios usando un lenguaje cercano y natural en lugar de lenguaje técnico.

### <a name="keywords"></a>Palabras clave

Las palabras clave estratégicas y detalladas relativas a la funcionalidades de la aplicación ayudan a los usuarios a encontrarla fácilmente al realizar búsquedas en la App Store. Además, si la aplicación utiliza anuncios iAd, iAd App Network utiliza las palabras clave al elegir los anuncios que aparecerán en la aplicación.

Apple sugiere lo siguiente al decidir las palabras clave:

- No utilice los nombres de aplicaciones de la competencia, nombres de empresa o de productos, o nombres de marcas registradas.
- No utilice palabras o términos genéricos.
- Evite términos obscenos o palabras irrelevantes como nombres de famosos.
- Localice las palabras clave para los mercados externos cuando proceda.

### <a name="urls"></a>Direcciones URL

Apple requiere que el desarrollador proporcione un vínculo a su sitio web para proporcionar servicio técnico para resolver cualquier problema o duda que un usuario pueda tener sobre la aplicación. También requiere un vínculo a la Directiva de privacidad de la aplicación (que, de nuevo, se debe hospedar en su sitio web).

Opcionalmente, puede proporcionar un vínculo a información de marketing alojada en su sitio web que puede utilizarse para proporcionar más información acerca de la aplicación de la que se proporciona en la App Store.

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>Edición de nombres, descripciones, novedades, palabras clave y direcciones URL en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Versiones**.
4. Desplácese hasta la sección **Nombre**.
5. Rellene toda la información necesaria:

    [![](itunesconnect-images/name01.png "Edición de nombres, descripciones, novedades, palabras clave y direcciones URL en iTunes Connect")](itunesconnect-images/name01.png#lightbox)
6. Haga clic en el botón **Guardar** situado en la parte superior de la pantalla para guardar los cambios.

> [!IMPORTANT]
> Nota: Apple rechazará su envío si el nombre, la descripción, las novedades, las palabras clave o los vínculos no coinciden con las funcionalidades de la aplicación actual.

<a name="general" />

## <a name="maintaining-general-app-information"></a>Mantener la información general de la aplicación

Esta sección del registro de aplicación de iTunes Connect proporciona el identificador único de la aplicación (asignado por Apple), las categorías a las que pertenece la aplicación, su clasificación, copyright e información sobre la compañía que publica la aplicación.

### <a name="app-icon"></a>Icono de la aplicación

> [!IMPORTANT]
> Los iconos de aplicaciones ya no se envían a través de iTunes Connect. Deben enviarse a través del conjunto de imágenes **AppIcon** del archivo **Assets.xcassets** del proyecto. Para obtener más información, consulte la guía [App Store Icon](~/ios/app-fundamentals/images-icons/app-store-icon.md) (Icono de la App Store).

El icono de la aplicación es la cara de la aplicación para los usuarios, por lo que debe ser fácil de recordar y mostrarse correctamente en un tamaño pequeño. Los iconos fáciles de recordar se reconocen inmediatamente, y son simples y limpios.

Apple sugiere las siguientes directrices al diseñar el icono de la aplicación:

- Haga que el icono sea adecuado para la aplicación.
- Cree un icono que sea coherente con el diseño de la aplicación.
- Evite usar palabras en su icono.
- Piense de manera global: se usa un único icono de aplicación en todos los territorios de la tienda.

Se precisa una imagen de 1024 x 1024 píxeles para el icono de la aplicación que se mostrará en la App Store.

Para obtener más información, consulte las [Directrices de la interfaz humana de iOS](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556) de Apple y la descripción de la sección Iconos de aplicación grandes en la documentación sobre [información general de la aplicación](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7)

### <a name="app-id"></a>Identificador de la aplicación

Se trata de un número de identificación único asignado a la aplicación por Apple cuando se crea un registro de iTunes Connect. Puede utilizar este número al llamar a varias interfaces basadas en web que Apple proporciona, incluida la información de la App Store en su sitio web.

### <a name="version-number"></a>Número de versión

Es la versión activa actual de la aplicación que se muestra al usuario en la App Store.

### <a name="category-and-sub-category"></a>Categoría y subcategoría

Un aspecto importante de la detectabilidad de la aplicación es la categoría en la que aparece en la App Store. Las categorías permiten a los usuarios examinar una colección de aplicaciones y buscar las que les interesan. iTunes Connect le permite asignar un máximo de dos categorías diferentes al definir la aplicación. Asegúrese de elegir cuidadosamente las categorías que mejor describen la función principal de la aplicación.

### <a name="ratings"></a>Clasificaciones

Todas las aplicaciones deben tener una clasificación en la App Store. Esta clasificación se usa para informar al control parental y limitar el acceso a los niños basándose en el tipo y el contenido de las aplicaciones. Al definir la aplicación, iTunes Connect proporciona una lista de descriptores de contenido con la que se configura la frecuencia con la que aparece el contenido en la aplicación. Estas selecciones se convierten en la clasificación que se muestra en la App Store.

Al crear aplicaciones para niños, la App Store tiene una categoría especial para los niños menores de 12 años. Incluso si la aplicación no está dirigida específicamente a niños, ayudará a sus clientes a elegir correctamente, al proporcionar la clasificación de contenido adecuada.

> [!IMPORTANT]
> Nota: Apple rechazará cualquier envío de aplicaciones que encuentre obsceno, pornográfico, ofensivo o difamatorio.

### <a name="copyright-and-company-information"></a>Información de copyright y de la empresa

Apple permite proporcionar información de copyright para la aplicación y requiere información acerca de la empresa que publica la aplicación, como su dirección e información de contacto (que es necesaria para aplicaciones publicadas en la App Store coreana). Esta información se mostrará en la App Store según sea necesario.

### <a name="editing-general-app-information-in-itunes-connect"></a>Editar información general de la aplicación en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Versiones**.
4. Desplácese hasta la sección **Información general de la aplicación**.
5. Rellene toda la información necesaria:

    [![](itunesconnect-images/general01.png "Edición de información general de la aplicación en iTunes Connect")](itunesconnect-images/general01.png#lightbox)
6. Haga clic en el botón **Editar** en **Clasificación** para establecer la información de clasificación:

    [![](itunesconnect-images/general02.png "Edición de la clasificación")](itunesconnect-images/general02.png#lightbox)
7. Haga clic en el botón **Guardar** situado en la parte superior de la pantalla para guardar los cambios.

> [!NOTE]
> Nota: Apple rechazará su envío si las categorías o las clasificaciones no coinciden con la funcionalidad actual de la aplicación.

<a name="game-center" />

## <a name="maintaining-game-center-information"></a>Mantener la información del Game Center

Para las aplicaciones de juegos de iOS que son compatibles con el Game Center de Apple, se puede proporcionar información como las tablas de puntuación y los logros en el juego que están disponibles para el usuario, y si la aplicación es compatible con varios jugadores a través de una conexión de red.

### <a name="editing-game-center-information-in-itunes-connect"></a>Editar información de Game Center en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Versiones**.
4. Desplácese hasta la sección **Game Center**.
5. Cambie el botón en la sección **Game Center** a **Activado**.
6. Rellene toda la información necesaria:

    [![](itunesconnect-images/gamecenter01.png "Edición de información de Game Center en iTunes Connect")](itunesconnect-images/gamecenter01.png#lightbox)
7. Haga clic en el botón **Guardar** situado en la parte superior de la pantalla para guardar los cambios.

Utilice la pestaña **Game Center** para activar el Game Center y mantener las **tablas de puntuación** o los **logros** disponibles para esta aplicación:

[![](itunesconnect-images/gamecenter02.png "Active Game Center")](itunesconnect-images/gamecenter02.png#lightbox)

[![](itunesconnect-images/gamecenter03.png "Mantenimiento de las tablas de puntuación o los logros disponibles para esta aplicación")](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>Mantener la información de las revisiones de aplicaciones

Utilice esta sección para proporcionar la información necesaria al personal de Apple que va a revisar la aplicación, como la información de contacto (en el caso de que el técnico tenga alguna pregunta), las cuentas de demostración que puedan ser necesarias y las notas que puedan ayudar a la persona que realiza la comprobación a revisar la aplicación correctamente.

### <a name="editing-app-review-information-in-itunes-connect"></a>Editar la información de las revisiones de aplicaciones en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Versiones**.
4. Desplácese hasta la sección **Información de revisiones de la aplicación**.
5. Rellene toda la información necesaria:

    [![](itunesconnect-images/review01.png "Edición de la información de las revisiones de aplicaciones en iTunes Connect")](itunesconnect-images/review01.png#lightbox)
6. Seleccione cómo quiere que se publique la aplicación en la App Store después de que se haya revisado correctamente:

    [![](itunesconnect-images/review02.png "Edición de la información de versiones en iTunes Connect")](itunesconnect-images/review02.png#lightbox)
7. Haga clic en el botón **Guardar** situado en la parte superior de la pantalla para guardar los cambios.


## <a name="maintaining-pricing-information"></a>Mantener la información de precio

Si tiene previsto poner a la venta la aplicación, debe establecer el precio de venta seleccionando uno de los niveles de precios disponibles de Apple y la fecha en la que el precio determinado entrará en vigor. Por ejemplo, en el momento de redactar este artículo, el **Nivel 1** de precios es similar al siguiente:

[![](itunesconnect-images/price01.png "Mantenimiento de la información de precio")](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>Descuento educativo

Active esta casilla si desea que su aplicación se ofrezca con un descuento para las instituciones educativas cuando compran varias copias a la vez. Los detalles del descuento se encuentran en la versión más reciente del **Contrato de aplicaciones de pago**, que debe aceptar antes de que esta aplicación esté disponible para los clientes de instituciones educativas.

### <a name="custom-business-to-business-application"></a>Aplicación de negocio a negocio personalizada

Una aplicación que está configurada como una **Aplicación de negocio a negocio personalizada** solo estará disponible para los clientes del **Programa de compras por volumen** que especifique en iTunes Connect para los territorios aplicables (por ejemplo, los clientes del Programa de compras por volumen de Estados Unidos deben usar el Programa de compras por volumen para empresas de la App Store estadounidense).

Las aplicaciones de negocio a negocio personalizadas no estarán disponibles para instituciones educativas ni clientes generales de la App Store. Para obtener más información sobre el *Programa de compras por volumen para empresas de la App Store*, visite la página [Preguntas más frecuentes](http://vpp.itunes.apple.com/faq) de Apple. Para obtener más información acerca de cómo los clientes pueden registrarse en el **Programa de compras por volumen**, visite la página [Programas de implementación](http://enroll.vpp.itunes.apple.com) de Apple.

### <a name="editing-pricing-information-in-itunes-connect"></a>Editar información de precio en iTunes Connect

Siga estos pasos en [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Haga clic en **Mis aplicaciones**.
2. Haga clic en el **icono** de la aplicación.
3. Seleccione la pestaña **Precios**:

    [![](itunesconnect-images/price02.png "Edición de la información de precio en iTunes Connect")](itunesconnect-images/price02.png#lightbox)
4. Seleccione un **fecha de disponibilidad**.
5. Seleccione el precio deseado desde la lista desplegable **Nivel de precio**.
6. Opcionalmente, puede habilitar **Descuentos educativos**.
7. También puede definir como una **Aplicación de negocio a negocio personalizada**.
8. Haga clic en el botón **Guardar** para guardar los cambios.

<a name="iap" />

## <a name="maintaining-in-app-purchase-information"></a>Mantener la información de compra en la aplicación

Si va a vender productos virtuales internos de la aplicación desde la aplicación (por ejemplo, nuevos niveles de juego o características de la aplicación) utilizará esta sección para crear y mantener los artículos de compra.

[![](itunesconnect-images/inapp01.png "Mantenimiento de la información de compra en la aplicación")](itunesconnect-images/inapp01.png#lightbox)

Para obtener más información sobre cómo trabajar con las compras desde la aplicación en una aplicación de Xamarin.iOS, consulte nuestra documentación sobre las [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md).

## <a name="viewing-application-reviews"></a>Ver las revisiones de aplicaciones

Una vez que la aplicación se ha publicado en la App Store, los usuarios que la compran o descargan de forma gratuita pueden escribir comentarios sobre ella y dejar clasificaciones por estrellas. Utilice esta sección para ver las revisiones. Por ejemplo:

[![](itunesconnect-images/reviews01.png "Consulta de las revisiones de aplicaciones")](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se describe cómo usar iTunes Connect para preparar una aplicación de Xamarin.iOS para su publicación en el App Store y cómo mantener toda la información mostrada sobre su aplicación en la tienda.

## <a name="related-links"></a>Vínculos relacionados

- [Working with Images (Trabajo con imágenes)](~/ios/app-fundamentals/images-icons/index.md)
- [Guía sobre los flujos de trabajo de desarrollo de aplicaciones iOS: distribución de aplicaciones](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Guía para desarrolladores de iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1)
