---
title: Implementación y prueba de aplicaciones de watchos con Xamarin
description: En este documento se describe cómo implementar y probar aplicaciones de watchos compiladas con Xamarin. Se proporciona una lista de comprobación de la implementación, se describen los identificadores de aplicación explícitos y los comodines y se examinan los grupos de aplicaciones.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 4e2ff46174d9dbb9171a470c389ffe301f6d0d60
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569652"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Implementación y prueba de aplicaciones de watchos con Xamarin

## <a name="deployment-checklist"></a>Lista de comprobación de implementación

Tanto si está implementando en una inspección de pruebas como si está cargando en la tienda de aplicaciones, debe completar los pasos de esta página:

- En el **centro de desarrollo de iOS**:
  - Se han creado los ID. de [aplicación](#App_IDs) .
  - [Grupos de aplicaciones](#App_Groups) configurados (si es necesario).
  - Perfiles de aprovisionamiento de distribución creados

- En la solución:

  - Compruebe que los [identificadores de agrupación y las referencias de proyecto](~/ios/watchos/get-started/installation.md) están establecidos.
  - Compruebe que los iconos están [configurados correctamente](~/ios/watchos/app-fundamentals/icons.md).
  - Compruebe que los números de versión de la agrupación coinciden en todos los proyectos.
  - Configure el archivo **contitles. plist** para los grupos de aplicaciones (si es necesario).

- A continuación, siga las instrucciones para lo siguiente:
  - [Implemente en un Apple Watch para las pruebas](~/ios/watchos/deploy-test/device.md), o bien
  - [Cargar en la tienda de aplicaciones](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"></a>

## <a name="app-ids"></a>Identificadores de aplicación

Como se describe en las [instrucciones de configuración](~/ios/watchos/get-started/installation.md), los tres proyectos de una aplicación de inspección tienen identificadores de lote relacionados, como:

- Proyecto Unificado de Xamarin. iOS:`com.xamarin.WatchKitCatalog`
- Proyecto de extensión WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`
- Proyecto de la aplicación de inspección:`com.xamarin.WatchKitCatalog.watchkitapp`

Los tres proyectos requieren un perfil de aprovisionamiento de distribución coincidente, ya sea mediante el uso de identificadores de aplicación explícitos para cada uno o un identificador de aplicación comodín.

### <a name="explicit-app-ids"></a>Identificadores de aplicación explícitos

Cree un **identificador de aplicación** para cada identificador de paquete de proyecto (que tendrá este aspecto en el centro de desarrollo de iOS):

![Los ID. de agrupación en el centro de desarrollo de iOS](images/appids-specific-sml.png)

Al crear o configurar los ID. de aplicación, no olvide habilitar las características específicas que requiere la aplicación. Esto podría incluir las notificaciones de envío y los grupos de aplicaciones.

Tendrá que crear un perfil de aprovisionamiento de distribución para cada identificador de aplicación.

### <a name="wildcard-app-id"></a>IDENTIFICADOR de aplicación comodín

Como alternativa, puede crear un identificador de **aplicación** comodín que coincida con los tres proyectos, como `com.xamarin.*` .

Tenga en cuenta que algunas características no se pueden usar con un identificador de aplicación comodín (por ejemplo, notificaciones de envío). Si la aplicación requiere estas características, debe crear identificadores de aplicación explícitos.

Para la distribución, solo necesitará crear un perfil de aprovisionamiento de distribución para el identificador de aplicación comodín.

<a name="App_Groups"></a>

## <a name="app-groups"></a>Grupos de aplicaciones

Puede usar un grupo de aplicaciones para compartir datos entre la aplicación iOS y la extensión Watch. Debe asegurarse de que la solución tenga:

- Configuró el **grupo de aplicaciones** en la sección **certificados, identificadores & de perfiles** del portal para desarrolladores de Apple.

- **Grupos de aplicaciones** habilitados (y proporcionado el identificador de **grupo de aplicaciones**) *tanto* en la aplicación iOS como en el identificador de la **aplicación** de la extensión Watch y en el archivo **contitles. plist**.

### <a name="certificates-identifiers--profiles"></a>Certificados, identificadores & perfiles

Para usar un grupo de aplicaciones, cree una entrada en la pantalla **grupos de aplicaciones** . En el ejemplo siguiente, se asigna un nombre al grupo con el mismo estilo DNS inverso que se usa normalmente para los identificadores de aplicación, pero con el `group.` prefijo (que es obligatorio):

![El identificador](images/appgroups-new-sml.png)

El grupo de aplicaciones aparecerá en la lista:

![Lista de identificadores](images/appgroups-setup-sml.png)

Una vez creado el grupo, se puede hacer referencia a él en la configuración del identificador de la **aplicación** . No olvide incluir tanto la aplicación iOS como los **identificadores de aplicación**de la extensión de inspección.

![Configuraciones disponibles](images/appgroups-sml.png)

**No** habilite grupos de aplicaciones en el Apple Watch identificador de la aplicación. No es necesario que esté habilitado en el propio reloj.

### <a name="entitlementsplist"></a>Entitlements.plist

Algunas características de la aplicación (por ejemplo, Los grupos de aplicaciones) requieren que se establezcan los derechos.
Haga doble clic para editar el archivo **contitles. plist** en estos proyectos:

- proyecto de aplicación de iOS
- Ver proyecto de extensión

.![Editor de derechos. plist](images/entitlements-plist-sml.png)

**No** habilite los derechos en el proyecto de la aplicación de inspección. No es necesario que esté habilitado en el propio reloj.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de envío de Apple WatchKit](https://developer.apple.com/app-store/watch/)
