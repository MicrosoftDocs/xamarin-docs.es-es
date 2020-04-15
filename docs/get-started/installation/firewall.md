---
title: Instrucciones de configuración del firewall para Xamarin
description: En este documento se proporciona una lista de hosts que se deben permitir en la lista blanca del firewall para que Xamarin funcione en un entorno corporativo.
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: conceptdev
ms.author: crdun
ms.date: 07/17/2019
ms.openlocfilehash: 2b52dfd55194ec076f28f8c33e758a39d14f5943
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70291330"
---
# <a name="xamarin-firewall-configuration-instructions"></a>Instrucciones de configuración de firewall para Xamarin

_Lista de los hosts que se deben permitir en el firewall para que la plataforma de Xamarin funcione en la empresa._

Para que los productos de Xamarin se instalen y funcionen correctamente, determinados puntos de conexión deben ser accesibles para descargar las herramientas y las actualizaciones necesarias para el software. Si usted o la empresa tienen una configuración de firewall estricta, puede experimentar problemas con la instalación, las licencias, los componentes, etc. En este documento se indican algunos de los puntos de conexión conocidos que se deben permitir en el firewall para que Xamarin funcione. No se incluyen los puntos de conexión necesarios para las herramientas de otros fabricantes incluidas en la descarga. Si después de repasar esta lista sigue teniendo problemas, consulte las guías de solución de problemas de instalación de Apple o Android.

## <a name="endpoints-to-allow"></a>Puntos de conexión permitidos

### <a name="xamarin-installer"></a>Instalador de Xamarin

Para que el software se instale correctamente con la versión más reciente del instalador de Xamarin, tiene que agregar las siguientes direcciones conocidas:

- xamarin.com (manifiestos del instalador)
- dl.xamarin.com (ubicación de descarga del paquete)
- dl.google.com (para descargar el SDK de Android)
- download.oracle.com (JDK)
- visualstudio.com (configurar ubicación de descarga de paquetes)
- go.microsoft.com (configurar URL de resolución)
- aka.ms (configurar URL de resolución)

Si está usando un equipo Mac y tiene problemas de instalación de Xamarin.Android, asegúrese de que macOS pueda descargar Java.

### <a name="nuget-including-xamarinforms"></a>NuGet (incluye Xamarin.Forms)

Para obtener acceso a NuGet (Xamarin.Forms está empaquetado como NuGet), tiene que agregar las siguientes direcciones:

- www.nuget.org (para acceder a NuGet)
- globalcdn.nuget.org (descargas de NuGet)
- dl-ssl.google.com (componentes de Google para Android y Xamarin.Forms)

### <a name="software-updates"></a>Actualizaciones de software

Para garantizar que las actualizaciones de software se descarguen correctamente, tendrá que agregar las siguientes direcciones:

- software.xamarin.com (servicio de actualizador)
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Xamarin Mac Agent

Para conectar Visual Studio a un host de compilación de Mac mediante Xamarin Mac Agent, el puerto SSH tiene que estar abierto. De forma predeterminada, es el **puerto 22**.
