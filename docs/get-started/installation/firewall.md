---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 71b2754d19d00b7bf4860acd96bfb7ad8dec4ce5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132996"
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

### <a name="nuget-including-xamarinforms"></a>NuGet (incluido Xamarin.Forms)

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
