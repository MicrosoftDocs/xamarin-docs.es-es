---
title: Calificadores de recursos y opciones de visualización
description: En este tema se explica cómo definir los recursos que se usarán solo cuando se encuentren coincidencias con algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena calificado con el idioma. Un recurso de cadena se puede definir como valor predeterminado, con otros recursos alternativos definidos que se usarán para otros idiomas. Todos los tipos de recursos se pueden calificar, incluido el propio diseño.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6ae10e73c3f41226560beb8cf8c50608fc6adf3f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762618"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Opciones de visualización y calificadores de recursos

_En este tema se explica cómo definir los recursos que se usarán solo cuando se encuentren coincidencias con algunos valores de calificador. Un ejemplo sencillo es un recurso de cadena calificado con el idioma. Un recurso de cadena se puede definir como valor predeterminado, con otros recursos alternativos definidos que se usarán para otros idiomas. Todos los tipos de recursos se pueden calificar, incluido el propio diseño._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opciones de calificador de recursos

Se puede tener acceso a las **Opciones del calificador de recursos** haciendo clic en el icono de puntos suspensivos a la derecha del botón modo **horizontal** :

[![Opciones de calificador de recursos](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Este cuadro de diálogo presenta menús desplegables para los siguientes calificadores de recursos:

- **Idioma** de &ndash; Muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma o región.

- **Modo de interfaz de usuario** Muestra los modos de visualización (por ejemplo, el **Dock de automóviles** y el acoplamiento de escritorio), así como las direcciones de diseño. &ndash;

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo en los que puede seleccionar y configurar calificadores de recursos (como se explica a continuación).

### <a name="language"></a>Idioma

En el menú desplegable **idioma** se muestran solo los idiomas que tienen definidos los recursos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, también hay una opción **Agregar idioma o región...** que le permite agregar un nuevo idioma a la lista:

[![Agregar idioma o región](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Al hacer clic en **Agregar idioma o región...** , se abre el cuadro de diálogo **Seleccionar idioma** para mostrar las listas desplegables de idiomas y regiones disponibles:

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "Lista de idiomas")

En este ejemplo, hemos elegido **fr (Francés)** como idioma y **ser** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que el campo **región** es opcional porque se pueden especificar muchos idiomas sin tener en cuenta las regiones específicas. Cuando se vuelve a abrir el menú desplegable de **idioma** , se muestra el recurso de idioma o región recién agregado:

![Idioma y región elegidos](resource-qualifiers-images/vs/11-language-region-added.png "Idioma y región elegidos")

Tenga en cuenta que si agrega un nuevo lenguaje pero no crea nuevos recursos, el idioma agregado ya no se mostrará la próxima vez que abra el proyecto.

### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el menú desplegable **modo de interfaz de usuario** , se muestra una lista de los modos, como **normal**, **coche Dock**, **mesa acoplada**, **televisión**, **dispositivo**y **inspección**:

[![Menú modo de interfaz de usuario](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Debajo de esta lista están los modos nocturnos **no nocturno** y **nocturno**, seguidos de las direcciones de diseño de **izquierda a derecha** y de **derecha a izquierda** (para obtener información sobre las opciones de **izquierda a derecha** y **de derecha a izquierda** , vea [ LayoutDirection](xref:Android.Util.LayoutDirection)).
Los últimos elementos del cuadro de diálogo **Opciones de calificador de recursos** son las **pantallas redondas** (para su uso con el desgaste de Android) o **no las pantallas redondas**.
Para obtener información acerca de las pantallas redondas y no redondas, vea [diseños](https://developer.android.com/training/wearables/ui/layouts.html).
Para obtener más información sobre los modos de interfaz de usuario de Android, vea [UiModeManager](xref:Android.App.UiModeManager).

## <a name="action-bar-settings"></a>Configuración de Barra de acciones

El icono de configuración de la **barra de acciones** está disponible a la izquierda del icono de pincel (editor de temas):

![Configuración de barra de acciones](resource-qualifiers-images/vs/14-action-bar.png "Configuración de barra de acciones")

Este icono abre un cuadro de diálogo elemento flotante que proporciona una manera de seleccionar uno de los tres modos de Barra de acciones:

- **Estándar** de &ndash; Consta de un logotipo, un icono y un texto de título con un subtítulo opcional.

- **Lista** de &ndash; Modo de navegación de lista. En lugar de texto de título estático, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

- **Pestañas** &ndash; Modo de navegación por tabulación. En lugar del texto del título estático, este modo presenta una serie de pestañas para la navegación dentro de la actividad.

## <a name="themes"></a>Temas

En el menú desplegable de **tema** se muestran todos los temas definidos en el proyecto. Al seleccionar **más temas** , se abre un cuadro de diálogo con una lista de todos los temas disponibles en el Android SDK instalado, como se muestra a continuación:

[![Lista de más temas](resource-qualifiers-images/vs/15-theme-menu-sml.png "Lista de más temas")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Cuando se selecciona un tema, el Superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hace permanente solo si se hace clic en el botón **Aceptar** del cuadro de diálogo **tema** . Una vez que se ha seleccionado un tema, se incluirá en el menú desplegable del **tema** , como se muestra a continuación:

El ![tema claro ya está disponible] El (resource-qualifiers-images/vs/16-light-theme.png "tema claro ya está disponible")

## <a name="android-version"></a>Versión de Android

El selector de **versión** de Android establece la versión de Android que se usa para representar el diseño en el diseñador. El selector muestra todas las versiones que son compatibles con la versión de .NET Framework de destino del proyecto:

![Lista de versiones de Android](resource-qualifiers-images/vs/17-android-version.png "Lista de versiones de Android")

La versión de .NET Framework de destino se puede establecer en la configuración del proyecto en **propiedades > aplicación > compilar con la versión de Android**. Para obtener más información acerca de la versión de .NET Framework de destino, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinado por la versión de .NET Framework de destino del proyecto. Esto también se aplica a las propiedades disponibles en la **ventana Propiedades**. La lista de widgets disponible *no* está determinada por el valor seleccionado en el selector de **versión** de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4,4, todavía puede seleccionar Android 6,0 en el selector de versión de la barra de herramientas para ver el aspecto del proyecto en Android 6,0, pero no podrá agregar widgets que sean específicos de Android 6,0 &ndash; .  seguirá estando limitado a los widgets que están disponibles en Android 4,4.

Para obtener más información sobre los tipos de recursos, vea [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opciones de calificador de recursos

Se puede tener acceso a las **Opciones del calificador de recursos** haciendo clic en el icono de puntos suspensivos a la derecha del botón modo **horizontal** :

[![Opciones de calificador de recursos](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Este cuadro de diálogo presenta menús desplegables para los siguientes calificadores de recursos:

- **Idioma** de &ndash; Muestra los recursos de idioma disponibles y ofrece una opción para agregar nuevos recursos de idioma o región.

- **Modo de interfaz de usuario** Muestra los modos de visualización (por ejemplo, el **Dock de automóviles** y el acoplamiento de escritorio), así como las direcciones de diseño. &ndash;

Cada uno de estos menús desplegables abre nuevos cuadros de diálogo en los que puede seleccionar y configurar calificadores de recursos (como se explica a continuación).

### <a name="language"></a>Idioma

En el menú desplegable **idioma** se muestran solo los idiomas que tienen definidos los recursos (o **todos los idiomas**, que es el valor predeterminado). Sin embargo, también hay una opción **Agregar idioma o región...** que le permite agregar un nuevo idioma a la lista:

[![Agregar idioma o región](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Al hacer clic en **Agregar idioma o región...** , se abre el cuadro de diálogo **Seleccionar idioma** para mostrar las listas desplegables de idiomas y regiones disponibles:

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

En este ejemplo, hemos elegido **fr (Francés)** como idioma y **ser** (Bélgica) para el dialecto regional de francés. Tenga en cuenta que el campo **región** es opcional porque se pueden especificar muchos idiomas sin tener en cuenta las regiones específicas. Cuando se vuelve a abrir el menú desplegable de **idioma** , se muestra el recurso de idioma o región recién agregado:

[![Idioma y región elegidos](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Tenga en cuenta que si agrega un nuevo lenguaje pero no crea nuevos recursos, el idioma agregado ya no se mostrará la próxima vez que abra el proyecto.

### <a name="ui-mode"></a>Modo de interfaz de usuario

Al hacer clic en el menú desplegable **modo de interfaz de usuario** , se muestra una lista de los modos, como **normal**, **coche Dock**, **mesa acoplada**, **televisión**, **dispositivo**y **inspección**:

[![Menú modo de interfaz de usuario](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Debajo de esta lista se encuentran los modos nocturnos **no nocturno** y **nocturno**, seguidos de las direcciones de diseño de **izquierda a derecha** y de **derecha a izquierda**. El último par de opciones permite seleccionar **pantallas redondas** o **pantallas rectangulares** (útiles para dispositivos de desgaste de Android).

Para obtener más información sobre los modos de interfaz de usuario de Android, vea [UiModeManager](xref:Android.App.UiModeManager).
Para obtener información sobre las opciones de **izquierda a derecha** y de **derecha a izquierda** , vea [LayoutDirection](xref:Android.Util.LayoutDirection).

## <a name="action-bar-settings"></a>Configuración de Barra de acciones

El icono de configuración de la **barra de acciones** está disponible a la izquierda del icono de pincel (editor de temas):

[![Configuración de Barra de acciones](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Este icono abre un cuadro de diálogo elemento flotante que proporciona una manera de seleccionar uno de los tres modos de Barra de acciones:

- **Estándar** de &ndash; Consta de un logotipo, un icono y un texto de título con un subtítulo opcional.

- **Lista** de &ndash; Modo de navegación de lista. En lugar de texto de título estático, este modo presenta un menú de lista para la navegación dentro de la actividad (es decir, se puede presentar al usuario como una lista desplegable).

- **Pestañas** &ndash; Modo de navegación por tabulación. En lugar del texto del título estático, este modo presenta una serie de pestañas para la navegación dentro de la actividad.

## <a name="themes"></a>Temas

En el menú desplegable de **tema** se muestran todos los temas definidos en el proyecto. Al seleccionar **más temas** , se abre un cuadro de diálogo con una lista de todos los temas disponibles en el Android SDK instalado, como se muestra a continuación:

[![Lista de más temas](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Cuando se selecciona un tema, el Superficie de diseño se actualiza para mostrar el efecto del nuevo tema. Tenga en cuenta que este cambio se hace permanente solo si se hace clic en el botón **Aceptar** del cuadro de diálogo **tema** . Una vez que se ha seleccionado un tema, se incluirá en el menú desplegable del **tema** , como se muestra a continuación:

[![El tema claro ya está disponible](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versión de Android

El selector de **versión** de Android establece la versión de Android que se usa para representar el diseño en el diseñador. El selector muestra todas las versiones que son compatibles con la versión de .NET Framework de destino del proyecto:

[![Lista de versiones de Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

La versión de .NET Framework de destino se puede establecer en la configuración del proyecto en la sección **Opciones del proyecto > Compilar > general** . Para obtener más información acerca de la versión de .NET Framework de destino, consulte [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

El conjunto de widgets disponibles en el cuadro de herramientas viene determinado por la versión de .NET Framework de destino del proyecto. Esto también se aplica a las propiedades disponibles en el **Panel de propiedades**. La lista de widgets disponible *no* está determinada por el valor seleccionado en el selector de **versión** de la barra de herramientas. Por ejemplo, si establece la versión de destino del proyecto en Android 4,4, todavía puede seleccionar Android 6,0 en el selector de versión de la barra de herramientas para ver el aspecto del proyecto en Android 6,0, pero no podrá agregar widgets que sean específicos de Android 6,0 &ndash; .  seguirá estando limitado a los widgets que están disponibles en Android 4,4.

Para obtener más información sobre los tipos de recursos, vea [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
