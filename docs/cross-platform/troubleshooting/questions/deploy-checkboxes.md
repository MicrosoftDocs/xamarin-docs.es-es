---
title: Implementar casillas deshabilitadas en el Administrador de configuración
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 82ff1a684ffad75a301f0db6b0f8e3116be6746d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285060"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implementar casillas deshabilitadas en el Administrador de configuración

A partir de Xamarin 3.5, los proyectos de Xamarin.iOS se implementan automáticamente cada vez que pulsa el botón **Iniciar** de barra de herramientas o se selecciona el elemento de menú **Depurar > Iniciar depuración**. Tendrá que establecer el proyecto de aplicación de Xamarin. iOS deseado como proyecto de **Inicio** antes de ejecutar cualquiera de estos comandos.

Debido a esto, las casillas de **Implementar** están deshabilitadas a propósito en el Administrador de configuración de Visual Studio para los proyectos de Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Configuration Manager de Visual Studio que muestra la casilla de verificación ' deploy ' deshabilitada para un proyecto de Xamarin. iOS en Xamarin 3,5")

Este cambio elimina un error que podía aparecer en versiones anteriores de Xamarin (versión 3.3 y anteriores) cuando no se establecía la implementación del proyecto de aplicación de Xamarin.iOS:

![](deploy-checkboxes-images/error.png "Cuadro de diálogo de error: El proyecto iPhoneApp1 debe implementarse antes de que se pueda iniciar. Compruebe que el proyecto está seleccionado para implementarse en la Configuration Manager de la solución.")
