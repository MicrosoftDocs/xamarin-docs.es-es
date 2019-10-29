---
title: Diseños con pestañas con barra
description: En esta guía se presenta y explica cómo usar las API de barra para crear una interfaz de usuario con pestañas en una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 33afa963cba2e341f23326c6a7814f97f88b6870
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028769"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Diseños con pestañas con barra

_En esta guía se presenta y explica cómo usar las API de barra para crear una interfaz de usuario con pestañas en una aplicación de Xamarin. Android._

## <a name="overview"></a>Información general

La barra de acción es un patrón de interfaz de usuario de Android que se usa para proporcionar una interfaz de usuario coherente para características clave como pestañas, identidad de la aplicación, menús y búsqueda. En Android 3,0 (nivel de API 11), Google presentó las API de barra en la plataforma Android. Las API de barra introducen temas de interfaz de usuario para proporcionar una apariencia y un funcionamiento coherentes, y clases que permiten las interfaces de usuario con pestañas. En esta guía se explica cómo agregar pestañas de Barra de acciones a una aplicación de Xamarin. Android. También se explica cómo usar la biblioteca de compatibilidad de Android V7 para reportar las pestañas de barra a las aplicaciones de Xamarin. Android dirigidas a Android 2,1 a Android 2,3. 

Tenga en cuenta que `Toolbar` es un componente de barra de acciones más reciente y más generalizado que debe usar en lugar de `ActionBar` (`Toolbar` se diseñó para reemplazar `ActionBar`). Para obtener más información, vea [barra de herramientas](~/android/user-interface/controls/tool-bar/index.md). 

## <a name="requirements"></a>Requisitos

Cualquier aplicación de Xamarin. Android que tenga como destino el nivel de API 11 (Android 3,0) o superior tiene acceso a las API de barra como parte de las API nativas de Android. 

Algunas de las API de barra se han trasladado al nivel de API 7 (Android 2,1) y están disponibles a través de la [biblioteca de AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)de la versión V7, que está disponible para las aplicaciones de Xamarin. Android a través del paquete [biblioteca de compatibilidad de Android-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) .

## <a name="introducing-tabs-in-the-actionbar"></a>Introducción a las pestañas de barra

La barra de acción intenta mostrar todas sus pestañas simultáneamente y hacer que todas las pestañas tengan el mismo tamaño en función del ancho de la etiqueta de la pestaña más ancha. Esto se muestra en la siguiente captura de pantalla: 

![Captura de pantalla de ejemplo de barra con todas las pestañas con el mismo tamaño mostradas](with-action-bar-images/image1.png)

Cuando el barra no puede mostrar todas las pestañas, se configuran las pestañas en una vista con desplazamiento horizontal. El usuario puede deslizar el dedo hacia la izquierda o la derecha para ver las pestañas restantes. Esta captura de pantalla de Google Play muestra un ejemplo de esto: 

![Captura de pantalla de ejemplo de pestañas en una vista con desplazamiento horizontal](with-action-bar-images/image2.png)

Cada pestaña de la barra de acciones debe estar asociada a un [*fragmento*](~/android/platform/fragments/index.md). Cuando el usuario selecciona una pestaña, la aplicación mostrará el fragmento que está asociado a la ficha. Barra no es responsable de mostrar el fragmento adecuado al usuario. En su lugar, barra notificará a una aplicación los cambios de estado en una pestaña a través de una clase que implementa la interfaz barra. ITabListener. Esta interfaz proporciona tres métodos de devolución de llamada que Android invocará cuando cambie el estado de la pestaña: 

- **OnTabSelected** : se llama a este método cuando el usuario selecciona la pestaña. Debe mostrar el fragmento.

- **OnTabReselected** : se llama a este método cuando la pestaña ya está seleccionada pero el usuario la selecciona de nuevo. Esta devolución de llamada se usa normalmente para actualizar o actualizar el fragmento mostrado.

- **OnTabUnselected** : se llama a este método cuando el usuario selecciona otra pestaña. Esta devolución de llamada se utiliza para guardar el estado en el fragmento mostrado antes de desaparecer.

Xamarin. Android encapsula el `ActionBar.ITabListener` con eventos en la clase `ActionBar.Tab`. Las aplicaciones pueden asignar controladores de eventos a uno o varios de estos eventos. Hay tres eventos (uno para cada método en `ActionBar.ITabListener`) que generará una pestaña de la barra de acciones: 

- TabSelected
- TabReselected
- TabUnselected

### <a name="adding-tabs-to-the-actionbar"></a>Agregar pestañas a barra

Barra es nativo para Android 3,0 (nivel de API 11) y superior y está disponible para cualquier aplicación de Xamarin. Android que tenga como mínimo esta API. 

En los pasos siguientes se muestra cómo agregar pestañas de barra a una actividad de Android: 

1. En el método `OnCreate` de una &ndash; de actividad *antes de inicializar los widgets de interfaz de usuario* &ndash; una aplicación debe establecer el `NavigationMode` del `ActionBar` en `ActionBar.NavigationModeTabs` como se muestra en este fragmento de código:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Cree una nueva pestaña con `ActionBar.NewTab()`.

3. Asigne controladores de eventos o proporcione una implementación de `ActionBar.ITabListener` personalizada que responderá a los eventos que se producen cuando el usuario interactúa con las pestañas de barra.

4. Agregue la pestaña que se creó en el paso anterior al `ActionBar`.

El código siguiente es un ejemplo del uso de estos pasos para agregar pestañas a una aplicación que usa controladores de eventos para responder a los cambios de estado: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```

#### <a name="event-handlers-vs-actionbaritablistener"></a>Controladores de eventos frente a barra. ITabListener

Las aplicaciones deben usar controladores de eventos y `ActionBar.ITabListener` para distintos escenarios. Los controladores de eventos ofrecen una determinada cantidad de comodidad sintáctica; evitan tener que crear una clase e implementar `ActionBar.ITabListener`. Esta comodidad tiene un costo &ndash; Xamarin. Android realiza esta transformación, ya que crea una clase e implementa `ActionBar.ITabListener`. Esto es correcto cuando una aplicación tiene un número limitado de pestañas. 

Al tratar con muchas pestañas o compartir la funcionalidad común entre las pestañas de barra, puede ser más eficaz en cuanto a memoria y rendimiento para crear una clase personalizada que implemente `ActionBar.ITabListener`y compartir una única instancia de la clase. Esto reducirá el número de GREF que usa una aplicación de Xamarin. Android. 

### <a name="backwards-compatibility-for-older-devices"></a>Compatibilidad con versiones anteriores de dispositivos antiguos

La [biblioteca de compatibilidad de Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) Reports barra pestañas en Android 2,1 (nivel de API 7). Se puede acceder a las pestañas en una aplicación de Xamarin. Android una vez que este componente se ha agregado al proyecto.

Para usar barra, una actividad debe subclase `ActionBarActivity` y usar el tema AppCompat tal y como se muestra en el siguiente fragmento de código:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Una actividad puede obtener una referencia a su barra de la propiedad `ActionBarActivity.SupportingActionBar`. En el fragmento de código siguiente se muestra un ejemplo de la configuración de barra en una actividad:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```

## <a name="summary"></a>Resumen

En esta guía se describe cómo crear una interfaz de usuario con pestañas en Xamarin. Android mediante barra. Hemos explicado cómo agregar pestañas a barra y cómo una actividad puede interactuar con eventos de pestaña a través de la interfaz de `ActionBar.ITabListener`. También vimos cómo el paquete AppCompat de la biblioteca de compatibilidad de Android V7 traslada las pestañas de barra a versiones anteriores de Android. 

## <a name="related-links"></a>Vínculos relacionados

- [ActionBarTabs (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Patrón de Barra de acciones](https://developer.android.com/design/patterns/actionbar.html)
- [AppCompat de Android V7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Biblioteca de compatibilidad de Xamarin. Android, paquete de NuGet de AppCompat de la V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
