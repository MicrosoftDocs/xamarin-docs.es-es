---
title: Enlace de un proyecto de biblioteca de Eclipse
description: En este tutorial se explica cómo usar las plantillas de proyecto de Xamarin. Android para enlazar un proyecto de biblioteca de Android de Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 78b70ce70292e589aee4a1dbe56f3765552ece7a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757714"
---
# <a name="binding-an-eclipse-library-project"></a>Enlace de un proyecto de biblioteca de Eclipse

_En este tutorial se explica cómo usar las plantillas de proyecto de Xamarin. Android para enlazar un proyecto de biblioteca de Android de Eclipse._

## <a name="overview"></a>Información general

Mientras. Los archivos AAR se convierten cada vez en la norma para la distribución de la biblioteca de Android, en algunos casos es necesario crear un enlace para un *proyecto de biblioteca de Android*. Los proyectos de biblioteca de Android son proyectos especiales de Android que contienen código que se puede compartir y recursos a los que pueden hacer referencia los proyectos de aplicación de Android. Normalmente, se enlaza a un proyecto de biblioteca de Android cuando se crea la biblioteca en el IDE de Eclipse.
En este tutorial se proporcionan ejemplos de cómo crear un proyecto de biblioteca de Android. ZIP de la estructura de directorios de un proyecto de Eclipse.

Los proyectos de biblioteca de Android son diferentes de los proyectos de Android normales, ya que no se compilan en un APK y no se pueden implementar por sí mismos en un dispositivo. En su lugar, se debe hacer referencia a un proyecto de biblioteca de Android mediante un proyecto de aplicación de Android. Cuando se compila un proyecto de aplicación de Android, primero se compila el proyecto de biblioteca de Android. El proyecto de aplicación de Android se absorberá en el proyecto de biblioteca de Android compilado e incluirá el código y los recursos en APK para su distribución. Debido a esta diferencia, la creación de un enlace para un proyecto de biblioteca de Android es ligeramente diferente de la creación de un enlace para un Java. JAR o. Archivo AAR.

## <a name="walkthrough"></a>Tutorial

Para usar un proyecto de biblioteca de Android en un proyecto de enlace de Java de Xamarin. Android, primero es necesario compilar el proyecto de biblioteca de Android en Eclipse. En la captura de pantalla siguiente se muestra un ejemplo de un proyecto de biblioteca de Android después de la compilación: 

[![Proyecto de biblioteca de ejemplo en Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Observe que el código fuente del proyecto de biblioteca de Android se ha compilado en un temporal. Archivo jar llamado **Android-mapviewballoons. jar**y que los recursos se han copiado en la carpeta **bin/res/** . 

Una vez compilado el proyecto de biblioteca de Android en Eclipse, se puede enlazar mediante un proyecto de enlace de Java de Xamarin. Android. Primero a. Debe crearse un archivo ZIP que contenga las carpetas **bin** y **res** del proyecto de biblioteca de Android. Es importante quitar el subdirectorio intermedio que interviene para que los recursos residan en **bin/res**. En la captura de pantalla siguiente se muestra el contenido de uno de estos elementos. Archivo ZIP: 

[![Contenido de la biblioteca de Android Project. zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

El. Después, se agrega el archivo ZIP al proyecto de enlace de Java de Xamarin. Android, como se muestra en la siguiente captura de pantalla:

[![Zip agregado al proyecto de enlace de Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Observe que la acción de compilación de. El archivo ZIP se ha establecido automáticamente en **LibraryProjectZip**.

Si hay alguno. Archivos JAR requeridos por el proyecto de biblioteca de Android, deben agregarse a la carpeta **jar** del proyecto de biblioteca de enlace de Java y la **acción de compilación** establecida en **ReferenceJar**. Un ejemplo de esto puede verse en la siguiente captura de pantalla: 

[![Acción de compilación establecida en ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una vez completados estos pasos, el proyecto de enlace de Java de Xamarin. Android se puede usar tal y como se describió anteriormente en este documento.

> [!NOTE]
> En este momento no se admite la compilación de proyectos de la biblioteca de Android en otros IDE. Es posible que otros IDE no creen la misma estructura de directorios o archivos en la carpeta **bin** como Eclipse. 

## <a name="summary"></a>Resumen

En este artículo, se explica el proceso de enlazar un proyecto de biblioteca de Android. Hemos compilado el proyecto de biblioteca de Android en Eclipse y, luego, hemos creado un archivo zip desde las carpetas **bin** y **res** del proyecto de biblioteca de Android. A continuación, usamos este archivo zip para crear un proyecto de enlace de Java de Xamarin. Android. 
