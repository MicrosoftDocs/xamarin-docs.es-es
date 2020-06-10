---
title: Complicaciones de watchos en Xamarin
description: En este documento se describe cómo trabajar con las complicaciones de watchos en Xamarin. Describe cómo agregar una complicación, escribir una complicación, plantillas y proporciona código de ejemplo.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/03/2017
ms.openlocfilehash: 139b58fd1953924d5a848fc79c3a1706afb760b0
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565658"
---
# <a name="watchos-complications-in-xamarin"></a>Complicaciones de watchos en Xamarin

_watchos permite a los desarrolladores escribir complicaciones personalizadas para ver caras_

En esta página se explican los diferentes tipos de complicaciones disponibles y cómo agregar una complicación a la aplicación watchos 3.

Tenga en cuenta que cada aplicación de watchos solo puede tener una complicación.

Para empezar, lea los [documentos de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar si la aplicación es adecuada para una complicación. Hay 5 `CLKComplicationFamily` tipos de pantalla entre los que elegir:

[![](complications-images/all-complications-sml.png "The 5 CLKComplicationFamily types available: Circular Small, Modular Small, Modular Large, Utilitarian Small, Utilitarian Large")](complications-images/all-complications.png#lightbox)

Las aplicaciones pueden implementar un solo estilo, o cinco, en función de los datos que se muestran.
También puede admitir el viaje de tiempo, proporcionando valores para los tiempos pasados o futuros a medida que el usuario convierta el Digital Crown.

<a name="adding"></a>

## <a name="adding-a-complication"></a>Agregar una complicación

### <a name="configuration"></a>Configuración

Las complicaciones se pueden agregar a una aplicación de inspección durante la creación o agregarse manualmente a una solución existente.

### <a name="add-new-project"></a>Agregar nuevo proyecto...

El Asistente para **Agregar nuevo proyecto...** incluye una casilla que creará automáticamente una clase de controlador de complicación y configurará el archivo **info. plist** :

![](complications-images/file-new-project-sml.png "The Include Complication checkbox")

### <a name="existing-projects"></a>Proyectos existentes

Para agregar una complicación a un proyecto existente:

1. Cree un nuevo archivo de clase **ComplicationController.CS** e implemente `CLKComplicationDataSource` .
2. Configure el archivo **info. plist** de la aplicación para exponer la complicación y la identidad que se admiten.

Estos pasos se describen con más detalle a continuación.

<a name="clkcomplicationcontroller"></a>

### <a name="clkcomplicationdatasource-class"></a>Clase CLKComplicationDataSource

La plantilla de C# siguiente incluye los métodos mínimos necesarios para implementar un `CLKComplicationDataSource` .

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

Siga las instrucciones para [escribir una complicación](#writing) para agregar código a esta clase.

### <a name="infoplist"></a>Info.plist

El archivo **info. plist** de la extensión de inspección debe especificar el nombre del `CLKComplicationDataSource` y las familias de complicaciones que desea admitir:

[![](complications-images/complications-config-sml.png "The complication family types")](complications-images/complications-config.png#lightbox)

La lista de entradas de **clase de origen de datos** mostrará los nombres de clase que `CLKComplicationDataSource` incluyen la subclase de subclase que incluye la lógica de complicaciones.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Toda la funcionalidad de complicación se implementa en una sola clase, reemplazando los métodos de la `CLKComplicationDataSource` clase abstracta (que implementa la `ICLKComplicationDataSource` interfaz).

### <a name="required-methods"></a>Métodos necesarios

Debe implementar los métodos siguientes para que se ejecute la complicación:

- `GetPlaceholderTemplate`: Devuelve la pantalla estática utilizada durante la configuración o cuando la aplicación no puede proporcionar un valor.
- `GetCurrentTimelineEntry`-Calcule la visualización correcta cuando se esté ejecutando la complicación.
- `GetSupportedTimeTravelDirections`: Devuelve opciones de, `CLKComplicationTimeTravelDirections` como `None` , `Forward` , `Backward` o `Forward | Backward` .

### <a name="privacy"></a>Privacidad

Complicaciones que muestran datos personales

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` o `HideOnLockScreen`

Si este método devuelve `HideOnLockScreen` , la complicación mostrará un icono o el nombre de la aplicación (y no los datos) cuando el reloj esté bloqueado.

### <a name="updates"></a>Actualizaciones

- `GetNextRequestedUpdateDate`-Devuelve una hora en la que el sistema operativo debe consultar la aplicación para ver los datos de visualización de complicaciones actualizados.

También puede forzar una actualización desde la aplicación de iOS.

### <a name="supporting-time-travel"></a>Viajes de tiempo de soporte técnico

La compatibilidad con el recorrido de tiempo es opcional y se controla mediante el `GetSupportedTimeTravelDirections` método. Si devuelve `Forward` , `Backward` o, `Forward | Backward` debe implementar los métodos siguientes:

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing"></a>

## <a name="writing-a-complication"></a>Escribir una complicación

Las complicaciones van desde la visualización de datos simples hasta la representación de imágenes y datos complicada con soporte técnico de viaje. El código siguiente muestra cómo crear una complicación sencilla de una sola plantilla.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de ejemplo

Este ejemplo solo admite la `UtilitarianLarge` plantilla, por lo que solo se puede seleccionar en caras de inspección específicas que admitan ese tipo de complicación. Al *seleccionar* complicaciones en un reloj, se muestra **mi complicación** y, cuando se *ejecuta* , se muestra el texto **minuto _hora_ ** (con una parte de la hora).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```

<a name="templates"></a>

## <a name="complication-templates"></a>Plantillas de complicación

Hay varias plantillas disponibles para cada estilo de complicación.
Las plantillas de **anillo** permiten mostrar un anillo de estilo de progreso alrededor de la complicación, que se puede usar para mostrar el progreso o algún otro valor de forma gráfica.

[Documentos de CLKComplicationTemplate de Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Circular pequeño

Todos estos nombres de clase de plantilla tienen el prefijo `CLKComplicationTemplateCircularSmall` :

- **RingImage** : muestra una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** : muestra una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** : solo se muestra una imagen pequeña.
- **SimpleText** : simplemente muestra un pequeño fragmento de texto.
- **StackImage** : muestra una imagen y una línea de texto, una encima de la otra.
- **StackText** : muestra dos líneas de texto.

### <a name="modular-small"></a>Modular pequeño

Todos estos nombres de clase de plantilla tienen el prefijo `CLKComplicationTemplateModularSmall` :

- **ColumnsText** : muestra una cuadrícula pequeña de valores de texto (2 filas y 2 columnas).
- **RingImage** : muestra una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** : muestra una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** : solo se muestra una imagen pequeña.
- **SimpleText** : simplemente muestra un pequeño fragmento de texto.
- **StackImage** : muestra una imagen y una línea de texto, una encima de la otra.
- **StackText** : muestra dos líneas de texto.

### <a name="modular-large"></a>Modular grande

Todos estos nombres de clase de plantilla tienen el prefijo `CLKComplicationTemplateModularLarge` :

- **Columnas** : muestra una cuadrícula de 3 filas con dos columnas, incluyendo opcionalmente una imagen a la izquierda de cada fila.
- **StandardBody** : muestra una cadena de encabezado en negrita, con dos filas de texto sin formato. Opcionalmente, el encabezado puede mostrar una imagen a la izquierda.
- **Tabla** : muestra una cadena de encabezado en negrita, con una cuadrícula de 2x2 de texto debajo. Opcionalmente, el encabezado puede mostrar una imagen a la izquierda.
- **TallBody** : muestra una cadena de encabezado en negrita, con una fuente mayor de texto por debajo de.

### <a name="utilitarian-small"></a>Utilitarian pequeño

Todos estos nombres de clase de plantilla tienen el prefijo `CLKComplicationTemplateUtilitarianSmall` :

- **Plano** : muestra una imagen y texto en una sola línea (el texto debe ser corto).
- **RingImage** : muestra una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** : muestra una sola línea de texto, con un anillo de progreso alrededor de ella.
- **Cuadrado** : muestra una imagen cuadrada (40px o 44px cuadrada para 38mm o 42mm Apple Watch respectivamente).

### <a name="utilitarian-large"></a>Utilitarian grande

Solo hay una plantilla para este estilo de complicación: `CLKComplicationTemplateUtilitarianLargeFlat` .
Muestra una sola imagen y texto, todo en una sola línea.

## <a name="related-links"></a>Vínculos relacionados

- [Documentos de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vídeo de WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
