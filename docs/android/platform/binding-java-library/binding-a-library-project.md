---
title: Enlace de un proyecto de biblioteca de Eclipse
description: En este tutorial se explica cómo usar las plantillas de proyecto de Xamarin.Android para enlazar un proyecto de biblioteca de Android de Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 54de1cde65e9c1ab9cb758c057a87c6b15c4da54
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853039"
---
# <a name="binding-an-eclipse-library-project"></a>Enlace de un proyecto de biblioteca de Eclipse

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

_En este tutorial se explica cómo usar las plantillas de proyecto de Xamarin.Android para enlazar un proyecto de biblioteca de Android de Eclipse._

## <a name="overview"></a>Información general

Aunque los archivos .AAR son, cada día más, la norma, para la distribución de la biblioteca de Android, en algunos casos, es necesario crear un enlace para un *proyecto de biblioteca de Android*. Los proyectos de biblioteca de Android son proyectos especiales de Android que contienen código que se puede compartir y recursos a los que pueden hacer referencia los proyectos de aplicaciones de Android. Normalmente, se enlaza a un proyecto de biblioteca de Android cuando se crea la biblioteca en el IDE de Eclipse.
En este tutorial se proporcionan ejemplos de cómo crear un archivo ZIP de proyecto de biblioteca de Android a partir de la estructura de directorios de un proyecto de Eclipse.

Los proyectos de biblioteca de Android son diferentes de otros proyectos de Android en que no se compilan en un APK y no se pueden implementar por sí mismos en un dispositivo. En su lugar, se debe hacer referencia a un proyecto de biblioteca de Android mediante un proyecto de aplicación de Android. Cuando se compila un proyecto de aplicación de Android, primero se compila el proyecto de biblioteca de Android. A continuación, el proyecto de aplicación de Android se absorberá en el proyecto de biblioteca de Android compilado e incluirá el código y los recursos en el APK para su distribución. Debido a esta diferencia, la creación de un enlace para un proyecto de biblioteca de Android es ligeramente diferente de la creación de un enlace para un archivo .AAR o .JAR de Java.

## <a name="walkthrough"></a>Tutorial

Para usar un proyecto de biblioteca de Android en un proyecto de enlace de Java de Xamarin.Android, antes es necesario compilar el proyecto de biblioteca de Android en Eclipse. En la captura de pantalla siguiente se muestra un ejemplo de un proyecto de biblioteca de Android después de la compilación: 

[![Proyecto de biblioteca de ejemplo en Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Observe que el código fuente del proyecto de biblioteca de Android se ha compilado en un archivo .JAR temporal denominado **android-mapviewballoons.jar** y que los recursos se han copiado a la carpeta **bin/res/crunch**. 

Una vez compilado el proyecto de biblioteca de Android en Eclipse, se puede enlazar mediante un proyecto de enlace de Java de Xamarin.Android. En primer lugar, se debe crear un archivo ZIP que contenga las carpetas **bin** y **res** del proyecto de biblioteca de Android. Es importante que quite el subdirectorio intermedio **crunch** para que los recursos residan en **bin/res**. En la captura de pantalla siguiente se muestra el contenido de uno de estos archivos ZIP: 

[![Contenido del archivo ZIP del proyecto de biblioteca de Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

A continuación, el archivo ZIP se agrega al proyecto de enlace de Java de Xamarin.Android, como se muestra en la siguiente captura de pantalla:

[![Archivo ZIP agregado al proyecto de enlace de Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Observe que la acción de compilación del archivo ZIP se ha establecido automáticamente en **LibraryProjectZip**.

Si hay algún archivo .JAR que el proyecto de biblioteca de Android necesite, se debe agregar a la carpeta **Jars** del proyecto de biblioteca de enlace de Java y la **Acción de compilación** se debe establecer en **ReferenceJar**. En la siguiente captura de pantalla se observa un ejemplo: 

[![Acción de compilación establecida en ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Una vez completados estos pasos, el proyecto de enlace de Java de Xamarin.Android se puede usar tal y como se describió anteriormente en este documento.

> [!NOTE]
> En este momento, no se admite la compilación de proyectos de biblioteca de Android en otros IDE. Es posible que otros IDE no creen la misma estructura de directorios o archivos en la carpeta **bin** que Eclipse. 

## <a name="summary"></a>Resumen

En este artículo se explica el proceso de enlazar un proyecto de biblioteca de Android. Hemos creado el proyecto de biblioteca de Android en Eclipse y, luego, hemos creado un archivo ZIP a partir de las carpetas **bin** y **res** del proyecto de biblioteca de Android. A continuación, usamos este archivo ZIP para crear un proyecto de enlace de Java de Xamarin.Android. 
