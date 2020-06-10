---
title: Trabajar con listas de propiedades en Xamarin. iOS
description: En este documento se presenta el editor de la lista de propiedades gráficas y avanzadas (. plist) de Visual Studio para Mac para trabajar con info. plist y contitles. plist. Muestra la configuración de iconos e imágenes de inicio para las aplicaciones de iOS desde Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6c2b5869f647f65b932b6ec92f359f8a79402c8f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569301"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Trabajar con listas de propiedades en Xamarin. iOS

_En este documento se presenta el editor de la lista de propiedades gráficas y avanzadas (. plist) de Visual Studio para Mac para trabajar con info. plist y contitles. plist. Muestra la configuración de iconos e imágenes de inicio para las aplicaciones de iOS desde Visual Studio para Mac._

Visual Studio para Mac incluye un editor gráfico. plist que facilita la edición de las propiedades y capacidades de la aplicación. Visual Studio para Mac tiene dos. plist: `Info.plist` para editar las propiedades y los iconos de la aplicación, y `Entitlements.plist` para administrar las funcionalidades de la aplicación. En esta guía se presenta info. plist y se proporciona información general sobre cómo trabajar con ellos en Visual Studio para Mac. Para obtener información sobre los derechos. plist, vea la guía [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

## <a name="infoplist"></a>Info.plist

La lista de propiedades de información ( `Info.plist` ) es un archivo de iOS necesario que proporciona información sobre la configuración de la aplicación para el sistema. El editor personalizado de Visual Studio para Mac `Info.plist` incluye tres paneles que están controlados por pestañas en la parte inferior izquierda de la ventana del editor:

 [![](property-lists-images/tabs.png "The Info.plist editor tabs at the bottom left of the editor window")](property-lists-images/tabs.png#lightbox)

Cada panel controla propiedades diferentes, como se describe a continuación:

- **Panel aplicación** : una interfaz gráfica para establecer las propiedades comunes de la aplicación, así como iconos e imágenes de inicio. Especifique los modos de integración y de fondo de maps.
- **Panel avanzado** : el panel Opciones avanzadas es el lugar donde se especifican los tipos de documento, UTI y tipos de URL admitidos.
- **Panel de origen** : el panel de origen controla las propiedades menos comunes, así como las propiedades personalizadas de la aplicación.

En las tres secciones siguientes se investigan con más detalle las características de cada panel.

## <a name="application-panel"></a>Panel de la aplicación

Visual Studio para Mac incluye una interfaz gráfica para editar `Info.plist` entradas comunes para una aplicación:

1. Propiedades de la aplicación
1. Tipos de dispositivos compatibles
1. Orientaciones de soporte para cada tipo de dispositivo
1. Estilo y color de la barra de estado
1. Iconos y pantallas de inicio
1. Mapas y modos en segundo plano

Estos se describen con más detalle en las secciones siguientes.

 <a name="iOS_Application_Target"></a>

### <a name="ios-application-target"></a>Destino de la aplicación iOS

Esta sección contiene información importante que describe la aplicación.
El **identificador** almacenado aquí debe coincidir con el identificador de paquete que se especifica en iTunes Connect (para aplicaciones de App Store) y también en la lista de identificadores de aplicaciones del portal de aprovisionamiento de iOS y certificados de desarrollo y distribución.

 [![](property-lists-images/image24.png "iOS Application Target")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Implementación de dispositivos

 [![](property-lists-images/deployment.png "Device Deployment")](property-lists-images/deployment.png#lightbox)

Las secciones de información de **implementación** de dispositivos se muestran de manera selectiva, en función de la selección realizada en la lista desplegable **dispositivos** en la sección destino de la **aplicación** anterior. La lista desplegable **interfaz principal** se establece en **archivo mainstoryboard** en aplicaciones controladas por guiones gráficos. Si la interfaz de usuario se escribe por completo en el código, puede dejarse en blanco.

### <a name="supported-device-orientations"></a>Orientaciones de dispositivos compatibles

 Las **orientaciones de dispositivo compatibles** controlan el modo en que la aplicación responde a la rotación del dispositivo. Es muy común que las aplicaciones de iPhone/iPad solo admitan el uso de **vertical**o todo el **resto.** Por lo general, todas las aplicaciones de iPad excepto los juegos deben admitir todas las orientaciones.

### <a name="status-bar-styles"></a>Estilos de barra de estado

La sección de estilos de la **barra de estado** es una interfaz gráfica para editar una aplicación `UIStatusBarStyle` :

 [![](property-lists-images/status.png "Status Bar Styles")](property-lists-images/status.png#lightbox)

 <a name="Icons"></a>

### <a name="icons-launch-images-and-itunes-artwork"></a>Iconos, imágenes de inicio y material gráfico de iTunes

Puede encontrar información sobre el uso de iconos, imágenes y material gráfico en el archivo info. plist en la guía [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) .

### <a name="maps-integration-and-background-modes"></a>Integración de Maps y modos en segundo plano

`Info.plist`Contiene secciones especiales para especificar los modos de integración y de fondo de las asignaciones. La elección de las opciones que desea admitir agregará las propiedades necesarias a la aplicación.

 [![](property-lists-images/maps.png "Maps Integration")](property-lists-images/maps.png#lightbox)

Para obtener más información sobre cómo trabajar con mapas, consulte la guía de mapas de Xamarin [iOS](~/ios/user-interface/controls/ios-maps/index.md) .

 [![](property-lists-images/bging.png "Background Modes")](property-lists-images/bging.png#lightbox)

Para obtener más información sobre los modos en segundo plano, consulte la guía sobre el fondo [de Xamarin en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) .

## <a name="advanced-panel"></a>Panel avanzado

El panel avanzado controla los tipos de documentos y los esquemas de dirección URL que admite la aplicación.

 [![](property-lists-images/image34.png "Advanced Panel")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types"></a>

## <a name="document-types"></a>Tipos de documento

En el caso de las aplicaciones que admiten la apertura de tipos específicos de archivos, iOS proporciona la `CFBundleDocumentTypes` clave. Si queremos que nuestra aplicación admita determinados tipos de archivo conocidos, por ejemplo, archivos PDF, agregaremos el valor PDF a la clave. En esta sección se proporciona una manera cómoda de especificar los datos que se almacenarán en la `CFBundleDocumentTypes` clave del `Info.plist` archivo.

Consulte la documentación sobre el [registro de los tipos de archivo que admite la aplicación](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obtener más información sobre cómo configurar estos valores.

## <a name="utis"></a>UTI

A veces, una aplicación debe admitir la apertura de un tipo de archivo personalizado. Por ejemplo, es posible que quiera abrir archivos de imagen con una extensión personalizada *. XAM*. Para especificar un tipo de archivo personalizado, vamos a crear un identificador de tipo UTI-universal personalizado con la `UIExportedTypeDeclarations` clave. En la captura de pantalla siguiente se muestra cómo crear un UTI personalizado para la extensión. XAM:

 [![](property-lists-images/uti.png "UTIs Editor")](property-lists-images/uti.png#lightbox)

Del mismo modo que el tipo exportado UTI especificar un UTI personalizado específico para la aplicación, el *tipo importado UTI* ( `UIImportedTypeDeclarations` clave) especifica tipos personalizados admitidos pero no pertenecientes a la aplicación.

Para obtener más información sobre el uso de UTI personalizado, consulte la guía sobre los tipos de archivo de registro de Apple que [admite la aplicación](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) .

## <a name="custom-urls"></a>Direcciones URL personalizadas

El nombre de un esquema de dirección URL (también denominado Protocolo) es la primera parte de la dirección URL. Por ejemplo, `http://` y `https://` son esquemas de dirección URL comunes. Tiene la opción de crear un esquema de dirección URL personalizado para la aplicación. Los esquemas de URL personalizados se utilizan para comunicarse y enviar datos de un junto a otro con otras aplicaciones. En la captura de pantalla siguiente se muestra la creación de un nuevo esquema de dirección URL personalizado denominado `monkeys://` :

 [![](property-lists-images/url.png "Custom URLs")](property-lists-images/url.png#lightbox)

Para obtener más información sobre la implementación de esquemas de URL personalizados, consulte la [sección implementación de esquemas de URL personalizados de Apple de esta guía](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html) .

## <a name="source-panel"></a>Panel de código fuente

La pestaña **origen** del `Info.plist` archivo permite agregar o editar valores personalizados. Visual Studio para Mac proporciona una lista de las propiedades más comunes:

 [![](property-lists-images/image31.png "Adding a new property from a dropdown")](property-lists-images/image31.png#lightbox)

En el caso de las propiedades conocidas Visual Studio para Mac una lista de valores válidos, como se muestra en la siguiente captura de pantalla:

 [![](property-lists-images/image32.png "Select a value from a know value list")](property-lists-images/image32.png#lightbox)

Visual Studio para Mac también detecta el tipo de propiedad, como se muestra a continuación:

 [![](property-lists-images/image33.png "The available property types")](property-lists-images/image33.png#lightbox)

Revise los vínculos de [recursos relacionados con las aplicaciones](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) de Apple para obtener información adicional sobre las propiedades opcionales.

 <a name="Entitlements"></a>

## <a name="summary"></a>Resumen

En este artículo se ha mostrado el uso de los Editores gráficos y avanzados. plist para editar configuraciones de aplicaciones comunes, así como para especificar iconos e imágenes de inicio. También se ha introducido el `Entitlements.plist` para agregar y administrar las funcionalidades de la aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Recursos relacionados con la aplicación](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registrar los tipos de archivo que admite la aplicación](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementación de esquemas de URL personalizados](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Referencia de formato del catálogo de activos](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
