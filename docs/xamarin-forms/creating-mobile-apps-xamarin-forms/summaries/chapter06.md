---
title: Resumen del capítulo 6. Clics de botón
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 6. Clics de botón'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a95f4a88ea0adff78475dab8699308fbe49aa46
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370213"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumen del capítulo 6. Clics de botón

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

[`Button`](xref:Xamarin.Forms.Button) es la vista que permite al usuario iniciar un comando. Un elemento `Button` se identifica mediante texto (y, opcionalmente, una imagen, como se muestra en [Capítulo 13, Mapas de bits](chapter13.md)). Por consiguiente, `Button` define muchas de las mismas propiedades que `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` también define tres propiedades que rigen la apariencia de su borde, pero la compatibilidad de estas propiedades y su independencia mutua son específicas de la plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) de tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) de tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) de tipo `Double`

`Button` también hereda todas las propiedades de `VisualElement` y `View`, incluidas `BackgroundColor`, `HorizontalOptions`y `VerticalOptions`.

## <a name="processing-the-click"></a>Procesamiento del clic

La clase `Button` define un evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) que se desencadena cuando el usuario pulsa `Button`. El controlador `Click` es de tipo `EventHandler`. El primer argumento es el objeto `Button` que genera el evento; el segundo argumento es un objeto `EventArgs` que no proporciona información adicional.

En el ejemplo [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) se muestra el control de `Clicked` sencillo.

## <a name="sharing-button-clicks"></a>Uso compartido de los clics del botón

Varias vistas de `Button` pueden compartir el mismo controlador de `Clicked`, pero el controlador generalmente debe determinar qué `Button` es responsable de un evento determinado. Un enfoque consiste en almacenar los distintos objetos `Button` como campos y comprobar cuál es el que activa el evento en el controlador.

En el ejemplo [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) se muestra esta técnica. El programa también muestra cómo establecer la propiedad [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) de `Button` a `false` cuando ya no sirve presionar `Button`. Un elemento `Button` deshabilitado no genera un evento `Clicked`.

## <a name="anonymous-event-handlers"></a>Controladores de eventos anónimos

Es posible definir controladores de `Clicked` como funciones lambda anónimas, como se muestra en el ejemplo [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas). Sin embargo, los controladores anónimos no se pueden compartir sin código de reflexión desordenado.

## <a name="distinguishing-views-with-ids"></a>Distinción de las vistas con identificadores

También se pueden distinguir varios objetos `Button` si se establece la propiedad [`StyleId`](xref:Xamarin.Forms.Element.StyleId) o [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) en `string`. Esta propiedad está definida por `Element`, pero no se usa en Xamarin.Forms. Está diseñada para utilizarse únicamente por los programas de la aplicación.

En el ejemplo [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) se usa el mismo controlador de eventos para las 10 claves numéricas de un teclado numérico y se distingue entre ellas con la propiedad `StyleId`:

[![Captura de pantalla triple del teclado más sencillo](images/ch06fg04-small.png "Calculadora")](images/ch06fg04-large.png#lightbox "Calculadora")

## <a name="saving-transient-data"></a>Guardado de datos transitorios

Muchas aplicaciones necesitan guardar datos cuando se termina un programa y volver a cargarlos cuando el programa se inicia de nuevo. La clase [`Application`](xref:Xamarin.Forms.Application) define varios miembros que ayudan al programa a guardar y restaurar datos transitorios:

- La propiedad [`Properties`](xref:Xamarin.Forms.Application.Properties) es un diccionario con claves de `string` y elementos de `object`. El contenido del diccionario se guarda automáticamente en el almacenamiento local de la aplicación antes de la finalización del programa y se vuelve a cargar cuando se inicia el programa.
- La clase `Application` define tres métodos virtuales protegidos que reemplaza la clase `App` estándar del programa: [`OnStart`](xref:Xamarin.Forms.Application.OnStart), [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) y [`OnResume`](xref:Xamarin.Forms.Application.OnResume). Hacen referencia a los eventos del *ciclo de vida de la aplicación*.
- El método [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) guarda el contenido del diccionario.

No es necesario llamar a `SavePropertiesAsync`. El contenido del diccionario se guarda automáticamente antes de la finalización del programa y se recupera antes del inicio del programa. Resulta útil durante las pruebas de los programas para guardar datos si el programa se bloquea.

También es útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una propiedad estática que devuelve el objeto `Application` actual que se puede usar para obtener el diccionario de `Properties`.

El primer paso es identificar todas las variables de la página que desea conservar cuando finaliza el programa. Si conoce todos los lugares en los que cambian esas variables, puede agregarlos simplemente al diccionario de `Properties` en ese momento. En el constructor de la página, puede establecer las variables del diccionario de `Properties` si la clave existe.

Un programa mayor probablemente necesitará tratar con los eventos del ciclo de vida de la aplicación. El más importante es el método `OnSleep`. Una llamada a este método indica que el programa ha dejado el primer plano. Es posible que el usuario haya presionado el botón **Inicio** en el dispositivo, que muestre todas las aplicaciones o que esté cerrando el teléfono. Una llamada a `OnSleep` es la única notificación que un programa recibe antes de que finalice. El programa debe aprovechar esta oportunidad para asegurarse de que el diccionario de `Properties` esté actualizado.

Una llamada a `OnResume` indica que el programa no terminó después de la última llamada a `OnSleep` pero que ahora se está ejecutando en primer plano de nuevo. El programa podría usar esta oportunidad para actualizar las conexiones a Internet (por ejemplo).

Se produce una llamada a `OnStart` durante el inicio del programa. No es necesario esperar hasta esta llamada de método para tener acceso al diccionario de `Properties` porque ya se ha restaurado el contenido al llamar al constructor `App`.

El ejemplo [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) es muy similar a **SimplestKeypad**, salvo que el programa utiliza la invalidación `OnSleep` para guardar la entrada del teclado actual y el constructor de página para restaurar los datos.

> [!NOTE]
> Otro enfoque para guardar la configuración del programa lo proporciona la clase [Preferences](~/essentials/preferences.md) de Xamarin.Essentials.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 6 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Ejemplos del capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Ejemplos de F# del capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Botón de Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
