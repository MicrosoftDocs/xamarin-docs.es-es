---
title: Implementar casillas deshabilitadas en el Administrador de configuración
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: c3d0b8f2da971238b98253405f3b8fe08699ab56
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997220"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implementar casillas deshabilitadas en el Administrador de configuración

A partir de Xamarin 3.5, los proyectos de Xamarin.iOS se implementan automáticamente cada vez que pulsa el botón **Iniciar** de barra de herramientas o se selecciona el elemento de menú **Depurar > Iniciar depuración**. Tendrá que establecer el proyecto de aplicación de Xamarin. iOS deseado como proyecto de **Inicio** antes de ejecutar cualquiera de estos comandos.

Debido a esto, las casillas de **Implementar** están deshabilitadas a propósito en el Administrador de configuración de Visual Studio para los proyectos de Xamarin.iOS:

![Configuration Manager de Visual Studio que muestra la casilla implementar deshabilitada para un proyecto de Xamarin. iOS en Xamarin 3,5](deploy-checkboxes-images/configuration.png)

Este cambio elimina un error que podía aparecer en versiones anteriores de Xamarin (versión 3.3 y anteriores) cuando no se establecía la implementación del proyecto de aplicación de Xamarin.iOS:

![Cuadro de diálogo de error: El proyecto iPhoneApp1 debe implementarse para poder iniciarlo. Compruebe que el proyecto está seleccionado para su implementación en el administrador de configuración de la solución.](deploy-checkboxes-images/error.png)
