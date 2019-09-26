---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "61047853"
---
## <a name="firewall-configuration"></a>Configuración del firewall

Para que las pruebas se envíen a Xamarin Test Cloud, el equipo que envía las pruebas debe ser capaz de comunicarse con los servidores de Test Cloud. Los firewalls deben configurarse para permitir el tráfico de red hacia y desde los servidores ubicados en **testcloud.Xamarin.com** en los puertos 80 y 443. Este punto de conexión está administrado por DNS y la dirección IP está sujeta a cambios. 

En algunas situaciones, una prueba (o un dispositivo que ejecuta la prueba) debe comunicarse con los servidores Web protegidos por un firewall. En este escenario, el Firewall debe estar configurado para permitir el tráfico desde las siguientes direcciones IP:

* **195.249.159.238**
* **195.249.159.239**
