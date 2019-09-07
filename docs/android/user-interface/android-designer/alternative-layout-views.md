---
title: Vistas de diseños alternativos
description: En este tema se explica cómo se pueden crear versiones de los diseños mediante calificadores de recursos. Por ejemplo, puede haber una versión de un diseño que solo se usa cuando el dispositivo está en modo horizontal y una versión de diseño que solo es para el modo vertical.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: c872baa99496352a1934d10356a1001b309aa63e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757403"
---
# <a name="alternative-layout-views"></a>Vistas de diseño alternativas

_En este tema se explica cómo puede usar los diseños de versión mediante calificadores de recursos. Por ejemplo, la creación de una versión de un diseño que solo se usa cuando el dispositivo está en modo horizontal y una versión de diseño que solo es para el modo vertical._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Crear diseños alternativos

Al hacer clic en el icono de **vista de diseño alternativo** (a la izquierda del **dispositivo**), se abre un panel de vista previa para mostrar los diseños alternativos disponibles en el proyecto. Si no hay diseños alternativos, se presenta la vista **predeterminada** : 

[![Panel vista de diseño alternativa](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "Panel vista de diseño alternativa")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Al hacer clic en el signo más verde situado junto a **nueva versión**, se abre el cuadro de diálogo **crear variación de diseño** para que pueda seleccionar los calificadores de recursos para esta variación de diseño: 

[![Crear variación de diseño](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Crear variación de diseño")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

En el ejemplo siguiente, el calificador de recurso para la orientación de la **pantalla** se establece en **horizontal**y el tamaño de la **pantalla** se cambia a **grande**. Esto crea una nueva versión de diseño denominada **Large-Land**:

[![Variación de tierras grandes](alternative-layout-views-images/vs/03-large-land-sml.png "Variación de tierras grandes")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Tenga en cuenta que en el panel de vista previa de la izquierda se muestran los efectos de las selecciones del calificador de recursos. Al hacer clic en **Agregar** , se crea el diseño alternativo y se cambia el diseñador a ese diseño. El panel de vista previa de la **vista de diseño alternativa** indica qué diseño se carga en el diseñador a través de un pequeño puntero a la derecha, como se indica en la siguiente captura de pantalla: 

[![Indicador de diseño cargado](alternative-layout-views-images/vs/04-new-layout-sml.png "Indicador de diseño cargado")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Editar diseños alternativos

Al crear diseños alternativos, a menudo es conveniente hacer un único cambio que se aplique a todas las versiones bifurcadas de un diseño. Por ejemplo, puede que desee cambiar el texto del botón a amarillo en todos los diseños. Si tiene un gran número de diseños y necesita propagar un solo cambio en todas las versiones, el mantenimiento puede llegar a ser engorroso y propenso a errores.

Para simplificar el mantenimiento de varias versiones de diseño, el diseñador proporciona un modo de **edición múltiple** que propaga los cambios en uno o más diseños. Cuando hay más de un diseño, se muestra el icono de **edición múltiple** : 

[![Icono de edición múltiple](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "Icono de edición múltiple")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Al hacer clic en el icono de **edición múltiple** , aparecen líneas que indican que los diseños están vinculados (como se muestra a continuación). es decir, cuando se realiza un cambio en un diseño, ese cambio se propaga a cualquier diseño vinculado. Para desvincular todos los diseños, haga clic en el icono con un círculo indicado en la siguiente captura de pantalla: 

[![Desvincular todos los diseños](alternative-layout-views-images/vs/06-multi-linked-sml.png "Desvincular todos los diseños")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Si tiene más de dos diseños, puede alternar selectivamente el botón Editar a la izquierda de cada vista previa del diseño para determinar qué diseños están vinculados entre sí. Por ejemplo, si desea realizar un único cambio que se propague a la primera y última de tres diseños, primero debe desvincular el diseño intermedio como se muestra aquí: 

[![Desvincular diseño central](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Desvincular diseño central")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

En este ejemplo, un cambio realizado en el diseño **predeterminado** o **largo** se propagará al otro diseño, pero no al diseño de **tierras grandes** .

### <a name="multi-edit-example"></a>Ejemplo de edición múltiple 

En general, cuando se realiza un cambio en un diseño, el mismo cambio se propaga a todos los demás diseños vinculados. Por ejemplo, agregar un nuevo `TextView` widget al diseño **predeterminado** y cambiar su cadena de texto a `Portrait` hará que se realice el mismo cambio en todos los diseños vinculados. Este es el aspecto del diseño **predeterminado** : 

[![Agregar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "Agregar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

También se agrega a la vista **de diseño de tierras grandes** porque está vinculada al diseño **predeterminado:** `TextView` 

[![TextView horizontal](alternative-layout-views-images/vs/09-landscape-textview-sml.png "TextView horizontal")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

Pero, ¿qué ocurre si desea realizar un cambio que es local a un solo diseño (es decir, no desea que el cambio se propague a ninguno de los otros diseños)? Para ello, debe desvincular el diseño que desee cambiar antes de modificarlo, como se explica a continuación. 

### <a name="making-local-changes"></a>Realizar cambios locales 

Supongamos que queremos que ambos diseños tengan el `TextView`agregado, pero también queremos cambiar la cadena de texto en el diseño de **tierras grandes** a `Landscape` en lugar `Portrait`de. Si realizamos este cambio en la **superficie grande** mientras ambos diseños están vinculados, el cambio se propagará de nuevo al diseño **predeterminado** . Por lo tanto, primero debe desvincular los dos diseños antes de efectuar el cambio. Cuando se modifica el texto en una **superficie grande** a `Landscape`, el diseñador marca este cambio con un marco rojo para indicar que el cambio es local para el diseño de **gran** capital y *no* se propaga de nuevo al diseño **predeterminado** : 

[![Cambio local](alternative-layout-views-images/vs/10-local-change-sml.png "Cambio local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

Al hacer clic en el diseño **predeterminado** para verlo, la `TextView` cadena de texto sigue establecida en `Portrait`. 

## <a name="handling-conflicts"></a>Controlar conflictos 

Si decide cambiar el color del texto del diseño **predeterminado** a verde, verá un icono de advertencia en el diseño vinculado. Al hacer clic en ese diseño, se abre el diseño para mostrar el conflicto. El widget que causó el conflicto se resalta con un marco rojo y se muestra el siguiente mensaje: *Los cambios recientes han provocado conflictos en este diseño alternativo*. 

[![Cambio conflictivo](alternative-layout-views-images/vs/11-conflicting-change-sml.png "Cambio conflictivo")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

Se muestra un *cuadro de conflicto* en la derecha del widget para explicar el conflicto: 

[![ADVERTENCIA de conflicto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

En el cuadro conflicto se muestra la lista de propiedades que han cambiado y se enumeran sus valores. Al hacer clic en **omitir conflicto** se aplica el cambio de propiedad solo a este widget. Al hacer clic en **aplicar** , se aplica el cambio de propiedad a este widget, así como al widget homólogo en el diseño **predeterminado** vinculado. Si se aplican todos los cambios de propiedad, el conflicto se descarta automáticamente. 

### <a name="view-group-conflicts"></a>Ver conflictos de grupos 

Los cambios de propiedad no son el único origen de conflictos. Se pueden detectar conflictos al insertar o quitar widgets. Por ejemplo, cuando el diseño de **tierras grandes** se desvincula del diseño **predeterminado** y el `TextView` en el diseño de **tierras grandes** se arrastra y se coloca sobre el `Button`, el diseñador marca el widget que se ha desplazado para indicar el conflicto

[![Ver conflicto de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Ver conflicto de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

Sin embargo, no hay ningún marcador en `Button`el. Aunque la posición de `Button` ha cambiado `Button` , no muestra los cambios aplicados que son específicos de la configuración **de gran superficie** . 

Si se `CheckBox` agrega un al diseño **predeterminado** , se genera otro conflicto y se muestra un icono de advertencia sobre el diseño **de tierras grandes** : 

[![Conflicto de casillas](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "Conflicto de casillas")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

Al hacer clic en el diseño de **tierras grandes,** se revela el conflicto. Se mostrará el mensaje siguiente: *Los cambios recientes han provocado conflictos en este diseño alternativo*: 

[![Conflicto de diseño Alt](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Conflicto de diseño Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Además, el cuadro conflicto muestra el siguiente mensaje:

[![Mensaje de conflicto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

La adición de `LinearLayout` provocaunconflictoporqueeldiseñodetierrasgrandestienecambios`CheckBox` en el que lo contiene. Sin embargo, en este caso, el cuadro conflicto muestra el widget que se acaba de insertar en el diseño `CheckBox`predeterminado ().

Si hace clic en **omitir conflicto**, el diseñador resuelve el conflicto, lo que permite arrastrar el widget mostrado en el cuadro de conflictos y colocarlo en el diseño en el que falta el widget (en este caso, el diseño de **tierras grandes** ): 

[![Conflicto de grupo resuelto](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "Conflicto de grupo resuelto")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Tal `Button` `LinearLayout`ycomo se mostró en el ejemplo anterior con, notieneunmarcadordecambiorojoporquesolotienecambiosqueseaplicaroneneldiseñodetierrasgrandes.`CheckBox`

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Crear diseños alternativos

Al hacer clic en el icono de **vista de diseño alternativo** (a la izquierda del **dispositivo**), se abre un panel de vista previa para mostrar los diseños alternativos disponibles en el proyecto. Si no hay diseños alternativos, se presenta la vista **predeterminada** : 

[![Panel vista de diseño alternativa](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Al hacer clic en el signo más verde situado junto a **nueva versión**, se abre el cuadro de diálogo **crear variación de diseño** para que pueda seleccionar los calificadores de recursos para esta variación de diseño: 

[![Crear variación de diseño](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

En el ejemplo siguiente, el calificador de recurso para la orientación de la **pantalla** se establece en **horizontal**y el tamaño de la **pantalla** se cambia a **grande**. Esto crea una nueva versión de diseño denominada **Large-Land**:

[![Variación de tierras grandes](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Tenga en cuenta que en el panel de vista previa de la izquierda se muestran los efectos de las selecciones del calificador de recursos. Al hacer clic en **Agregar** , se crea el diseño alternativo y se cambia el diseñador a ese diseño. El panel de vista previa de la **vista de diseño alternativa** indica qué diseño se carga en el diseñador a través de un pequeño puntero a la derecha, como se indica en la siguiente captura de pantalla: 

[![Indicador de diseño cargado](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Editar diseños alternativos

Al crear diseños alternativos, a menudo es conveniente hacer un único cambio que se aplique a todas las versiones bifurcadas de un diseño. Por ejemplo, puede que desee cambiar el texto del botón a amarillo en todos los diseños. Si tiene un gran número de diseños y necesita propagar un solo cambio en todas las versiones, el mantenimiento puede llegar a ser engorroso y propenso a errores.

Para simplificar el mantenimiento de varias versiones de diseño, el diseñador proporciona un modo de **edición múltiple** que propaga los cambios en uno o más diseños. Cuando hay más de un diseño, se muestra el icono de **edición múltiple** : 

[![Icono de edición múltiple](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Al hacer clic en el icono de **edición múltiple** , aparecen líneas que indican que los diseños están vinculados (como se muestra a continuación). es decir, cuando se realiza un cambio en un diseño, ese cambio se propaga a cualquier diseño vinculado. Para desvincular todos los diseños, haga clic en el icono con un círculo indicado en la siguiente captura de pantalla: 

[![Desvincular todos los diseños](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Si tiene más de dos diseños, puede alternar selectivamente el botón Editar a la izquierda de cada vista previa del diseño para determinar qué diseños están vinculados entre sí. Por ejemplo, si desea realizar un único cambio que se propague a la primera y última de tres diseños, primero debe desvincular el diseño intermedio como se muestra aquí: 

[![Desvincular diseño central](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

En este ejemplo, un cambio realizado en el diseño **predeterminado** o **largo** se propagará a otro diseño, pero no al diseño de **gran superficie** . 

### <a name="multi-edit-example"></a>Ejemplo de edición múltiple 

En general, cuando se realiza un cambio en un diseño, el mismo cambio se propaga a todos los demás diseños vinculados. Por ejemplo, agregar un nuevo `TextView` widget al diseño **predeterminado** y cambiar su cadena de texto a `Portrait` hará que se realice el mismo cambio en todos los diseños vinculados. Este es el aspecto del diseño **predeterminado** : 

[![Agregar TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

También se agrega a la vista **de diseño de tierras grandes** porque está vinculada al diseño **predeterminado:** `TextView` 

[![TextView horizontal](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

Pero, ¿qué ocurre si desea realizar un cambio que es local a un solo diseño (es decir, no desea que el cambio se propague a ninguno de los otros diseños)? Para ello, debe desvincular el diseño que desee cambiar antes de modificarlo, como se explica a continuación. 

### <a name="making-local-changes"></a>Realizar cambios locales 

Supongamos que queremos que ambos diseños tengan el `TextView`agregado, pero también queremos cambiar la cadena de texto en el diseño de **tierras grandes** a `Landscape` en lugar `Portrait`de. Si realizamos este cambio en la **superficie grande** mientras ambos diseños están vinculados, el cambio se propagará de nuevo al diseño **predeterminado** . Por lo tanto, primero debe desvincular los dos diseños antes de efectuar el cambio. Cuando se modifica el texto en una **superficie grande** a `Landscape`, el diseñador marca este cambio con un marco rojo para indicar que el cambio es local para el diseño de **gran** capital y *no* se propaga de nuevo al diseño **predeterminado** : 

[![Cambio local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Al hacer clic en el diseño **predeterminado** para verlo, la `TextView` cadena de texto sigue establecida en `Portrait`. 

## <a name="handling-conflicts"></a>Controlar conflictos 

Si decide cambiar el color del texto del diseño **predeterminado** a verde, verá un icono de advertencia en el diseño vinculado. Al hacer clic en ese diseño, se abre el diseño para mostrar el conflicto. El widget que causó el conflicto se resalta con un marco rojo y se muestra el siguiente mensaje: *Los cambios recientes han provocado conflictos en este diseño alternativo*. 

[![Cambio conflictivo](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Se muestra un *cuadro de conflicto* en la derecha del widget para explicar el conflicto: 

[![ADVERTENCIA de conflicto](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

En el cuadro conflicto se muestra la lista de propiedades que han cambiado y se enumeran sus valores. Al hacer clic en **omitir conflicto** se aplica el cambio de propiedad solo a este widget. Al hacer clic en **aplicar** , se aplica el cambio de propiedad a este widget, así como al widget homólogo en el diseño **predeterminado** vinculado. Si se aplican todos los cambios de propiedad, el conflicto se descarta automáticamente. 

### <a name="view-group-conflicts"></a>Ver conflictos de grupos 

Los cambios de propiedad no son el único origen de conflictos. Se pueden detectar conflictos al insertar o quitar widgets. Por ejemplo, cuando el diseño de **tierras grandes** se desvincula del diseño **predeterminado** y el `TextView` en el diseño de **tierras grandes** se arrastra y se coloca sobre el `Button`, el diseñador marca el widget que se ha desplazado para indicar el conflicto

[![Ver conflicto de grupo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

Sin embargo, no hay ningún marcador en `Button`el. Aunque la posición de `Button` ha cambiado `Button` , no muestra los cambios aplicados que son específicos de la configuración **de gran superficie** . 

Si se `CheckBox` agrega un al diseño **predeterminado** , se genera otro conflicto y se muestra un icono de advertencia sobre el diseño **de tierras grandes** : 

[![Conflicto de casillas](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

Al hacer clic en el diseño de **tierras grandes,** se revela el conflicto. Se mostrará el mensaje siguiente: *Los cambios recientes han provocado conflictos en este diseño alternativo*. 

[![Conflicto de diseño Alt](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

Además, el cuadro conflicto muestra el siguiente mensaje:

[![Mensaje de conflicto](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

La adición de `LinearLayout` provocaunconflictoporqueeldiseñodetierrasgrandestienecambios`CheckBox` en el que lo contiene. Sin embargo, en este caso, el cuadro conflicto muestra el widget que se acaba de insertar en el diseño `CheckBox`predeterminado ().

Si hace clic en **omitir conflicto**, el diseñador resuelve el conflicto, lo que permite arrastrar el widget mostrado en el cuadro de conflictos y colocarlo en el diseño en el que falta el widget (en este caso, el diseño de **tierras grandes** ): 

[![Conflicto de grupo resuelto](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

Tal `Button` `LinearLayout`ycomo se mostró en el ejemplo anterior con, notieneunmarcadordecambiorojoporquesolotienecambiosqueseaplicaroneneldiseñodetierrasgrandes.`CheckBox`

-----

### <a name="conflict-persistence"></a>Persistencia de conflictos

Los conflictos se guardan en el archivo de diseño como comentarios XML, como se muestra aquí:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Por lo tanto, cuando se cierra y se vuelve a abrir un proyecto, todos los conflictos seguirán siendo &ndash; incluso los que se han omitido.
