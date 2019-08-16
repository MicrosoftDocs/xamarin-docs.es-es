---
title: ¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms en un paquete NuGet más reciente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 0c0b5e04bafc748b48ea007162cfd7277cc23752
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528342"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms en un paquete NuGet más reciente?

En esta guía se usa la plantilla de biblioteca de .NET Standard de Xamarin. Forms como ejemplo, pero el mismo método general también funcionará para la plantilla de proyecto compartido de Xamarin. Forms. Esta guía se ha escrito con el ejemplo de actualización de Xamarin. Forms 1.5.1.6471 a 2.1.0.6529, pero se pueden realizar los mismos pasos para establecer otras versiones como valor predeterminado.

1. Copie la plantilla `.zip` original de:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Descomprima el `.zip` en una ubicación temporal.

3. Cambie todas las apariciones de la versión anterior del paquete de Xamarin. Forms a la nueva versión que le gustaría usar.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Ejemplo`<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Cambie el elemento "Name" del archivo de [plantilla de varios proyectos](https://msdn.microsoft.com/library/ms185308.aspx) principal (`Xamarin.Forms.PCL.vstemplate`) para que sea único. Por ejemplo:

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Vuelva a comprimir toda la carpeta de plantillas. Asegúrese de que coincide con la estructura de archivo original `.zip` del archivo. El `Xamarin.Forms.PCL.vstemplate` archivo debe estar en la parte superior `.zip` del archivo, no en ninguna carpeta.

6. Cree un subdirectorio "Mobile Apps" en la carpeta de plantillas de Visual Studio por usuario:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copie la nueva carpeta de plantillas comprimidas en el nuevo directorio "Mobile Apps".

8. Descargue el paquete de NuGet que coincida con la versión del paso 3. Por ejemplo, [http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (vea también [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) y cópielo en la subcarpeta correspondiente de la carpeta de extensiones de Visual Studio de Xamarin:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
