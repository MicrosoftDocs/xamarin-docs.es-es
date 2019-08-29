---
title: Localización de cadenas e imágenes
description: Las aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET.
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 54a1f32d4e2102654a6111dc1fb27bc1ba9aabcc
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119113"
---
# <a name="localization"></a>Localización

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

_Las aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos. NET._

## <a name="overview"></a>Información general

El mecanismo integrado para la localización de aplicaciones .NET usa [archivos RESX](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) y las clases de los espacios de nombres `System.Resources` y `System.Globalization`. Los archivos RESX que contienen las cadenas traducidas se insertan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado a las traducciones. Después, se puede recuperar el texto traducido en el código.

### <a name="sample-code"></a>Código de ejemplo

Hay dos ejemplos relacionados con este documento:

- [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) es una demostración muy simple de los conceptos explicados. Los fragmentos de código que se muestran a continuación son todos de este ejemplo.
- [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) es una aplicación básica de trabajo que usa estas técnicas de localización.

#### <a name="shared-projects-are-not-recommended"></a>No se recomiendan los proyectos compartidos

El ejemplo TodoLocalized incluye una [demostración del proyecto compartido](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/); sin embargo, debido a las limitaciones del sistema de compilación, los archivos de recursos no obtienen un archivo **.designer.cs** generado, lo cual interrumpe la capacidad de tener acceso a cadenas traducidas fuertemente tipadas en el código.

El resto de este documento se relaciona con los proyectos que utilizan la plantilla de biblioteca .NET Standard de Xamarin.Forms.

## <a name="globalizing-xamarinforms-code"></a>Globalización del código de Xamarin.Forms

La **globalización** de una aplicación es el proceso de universalizarla. Esto significa escribir código que pueda mostrar idiomas distintos.

Una de las partes principales de la globalización de una aplicación consiste en compilar la interfaz de usuario para que no haya texto *codificado de forma rígida*. Alternativamente, nada de lo que se muestra al usuario debe recuperarse de un conjunto de cadenas que se han traducido a su idioma elegido.

En este documento, examinaremos cómo usar los archivos RESX para almacenar estas cadenas y recuperarlas para su visualización según la preferencia del usuario.

Los ejemplos de destino son los idiomas inglés, francés, español, alemán, chino, japonés, ruso y portugués (Brasil). Las aplicaciones se pueden traducir a tantos idiomas como sea necesario.

> [!NOTE]
> En la Plataforma universal de Windows, los archivos RESW deben usarse para la localización de notificaciones push, en lugar de los archivos RESX. Para obtener más información, consulte [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="adding-resources"></a>Adición de recursos

El primer paso para la globalización de una aplicación de biblioteca .NET Standard de Xamarin.Forms es agregar los archivos de recursos RESX que se usarán para almacenar todo el texto que se usa en la aplicación. Necesitamos agregar un archivo RESX que contiene el texto predeterminado y, a continuación, agregar los archivos RESX adicionales para cada idioma que desee admitir.

#### <a name="base-language-resource"></a>Recurso de idioma base

El archivo de recursos base (RESX) contendrá las cadenas del idioma predeterminado (en los ejemplos se asume que el idioma predeterminado es el inglés). Agregue el archivo al proyecto de código común de Xamarin.Forms, haciendo clic con el botón derecho en el proyecto y eligiendo **Agregar > Nuevo archivo...**

Elija un nombre descriptivo, como **AppResources**, y pulse **Aceptar**.

[![Agregar archivo de recursos](text-images/resx-new-file-sml.png "Cuadro de diálogo Nuevo archivo")](text-images/resx-new-file.png#lightbox "New File Dialog")

Se agregarán dos archivos al proyecto:

- El archivo **AppResources.resx**, en el cual se almacenan cadenas traducibles en formato XML.
- El archivo **AppResources.designer.cs**, que declara una clase parcial para contener referencias a todos los elementos creados en el archivo XML RESX.

El árbol de la solución mostrará los archivos según su relación. El archivo RESX *debe* editarse para agregar nuevas cadenas traducibles; el archivo **.designer.cs** *no* se debe editar.

![](text-images/appresources-tree.png "Archivo AppResources.resx")

##### <a name="string-visibility"></a>Visibilidad de la cadena

De forma predeterminada, cuando se generan referencias fuertemente tipadas para las cadenas, serán de tipo `internal` para el ensamblado. Esto es así porque la herramienta de compilación predeterminada para los archivos RESX genera el archivo **.designer.cs** con propiedades de tipo `internal`.

Seleccione el archivo **AppResources.resx** y observe el panel **Propiedades** para ver dónde está configurada esta herramienta de compilación. La captura de pantalla siguiente muestra **Herramienta personalizada: ResXFileCodeGenerator**.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "Ventana de propiedades para AppResources.Resx")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

Para hacer que las propiedades de la cadena fuertemente tipada sean `public`, debe cambiar manualmente la configuración a **Herramienta personalizada: PublicResXFileCodeGenerator**, tal y como se muestra en la captura de pantalla siguiente:


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "Ventana de propiedades para AppResources.Resx")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "Panel de propiedades para AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

Este cambio es opcional y solo es necesario si desea hacer referencia a las cadenas localizadas en ensamblados diferentes (por ejemplo, si coloca los archivos RESX en un ensamblado diferente en el código). El ejemplo de este tema deja las cadenas de tipo `internal` porque ya están definidas en el mismo ensamblado de biblioteca estándar .NET Standard de Xamarin.Forms en que se usan.

Basta con establecer la herramienta personalizada en el archivo RESX base tal como se muestra anteriormente; no es necesario establecer *ninguna* herramienta de compilación en los archivos RESX específicos del idioma que se describen en las secciones siguientes.

##### <a name="editing-the-resx-file"></a>Edición del archivo RESX

Por desgracia, no hay ningún editor de RESX integrado en Visual Studio para Mac. La adición de nuevas cadenas traducibles requiere la adición de un elemento `data` XML nuevo para cada cadena. Cada elemento `data` puede contener lo siguiente:

- El atributo `name` (obligatorio) es la clave para esta cadena traducible. Debe ser un nombre de propiedad C# válido, por lo que no se permiten espacios ni caracteres especiales.
- El elemento `value` (obligatorio), que es la cadena real que se muestra en la aplicación.
- El elemento `comment` (opcional) puede contener instrucciones para el traductor, con explicaciones sobre cómo se utiliza esta cadena.
- El atributo `xml:space` (opcional) para controlar cómo se conserva el espaciado en la cadena.

Algunos elementos `data` de ejemplo se muestran aquí:

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

A medida que se escribe la aplicación, cada fragmento de texto que se muestra al usuario debe agregarse al archivo de recursos RESX base en un nuevo elemento `data`. Se recomienda incluir elementos `comment`, tantos como sea posible, para garantizar una traducción de alta calidad.

> [!NOTE]
> Visual Studio (incluida la edición gratuita Community) contiene un editor de RESX básico. Si tiene acceso a un equipo Windows, esta puede ser una manera cómoda de agregar y editar las cadenas en archivos RESX.

#### <a name="language-specific-resources"></a>Recursos específicos de idioma

Normalmente, la traducción real de las cadenas de texto predeterminadas no se llevará a cabo hasta que se hayan escrito grandes fragmentos de la aplicación (en cuyo caso el archivo RESX predeterminado contendrá una gran cantidad de cadenas). Sigue siendo una buena idea agregar los recursos específicos de idioma al principio del ciclo de desarrollo, rellenándolos opcionalmente con texto traducido automáticamente para ayudar a probar el código de localización.

Se agrega un archivo RESX adicional para cada idioma que se quiera admitir.
Los archivos de recursos específicos de idioma deben seguir una convención de nomenclatura específica: deben usar el mismo nombre de archivo que los archivos de recursos base (por ejemplo, **AppResources**) seguido por un punto (.) y, a continuación, el código de idioma. Estos son algunos ejemplos sencillos:

- **AppResources.fr.resx**: traducciones en francés.
- **AppResources.es.resx**: traducciones en español.
- **AppResources.de.resx**: traducciones en alemán.
- **AppResources.ja.resx**: traducciones en japonés.
- **AppResources.zh Hans.resx** - traducciones en chino (simplificado).
- **AppResources.zh Hant.resx** - traducciones en chino (tradicional).
- **AppResources.pt.resx**: traducciones en portugués.
- **AppResources.pt-BR.resx**: traducciones en portugués (de Brasil).

El patrón general consiste en usar códigos de idioma de dos letras, pero hay algunos ejemplos (por ejemplo, chino) donde se usa un formato diferente, y otros ejemplos (por ejemplo, portugués de Brasil) donde se requiere un identificador de configuración regional de cuatro caracteres.

Estos archivos de recursos específicos de idioma *no* requieren una clase parcial **. designer.cs** para poder agregarse como archivos XML normales, con **Acción de compilación: EmbeddedResource** establecida. Esta captura de pantalla muestra una solución que contiene los archivos de recursos específicos de idioma:

![](text-images/appresources-langs.png "Archivos de recursos específicos de idioma")

A medida que se desarrolla una aplicación y el archivo RESX base tiene texto agregado, debe enviarla a los traductores, que traducirán cada elemento `data` y devolverán un archivo de recursos específicos de idioma (con la convención de nomenclatura que se muestra) para incluir en la aplicación. A continuación, se muestran algunos ejemplos "traducidos automáticamente":

**AppResources.es.resx (español)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (japonés)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (portugués de Brasil)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

El traductor solo debe actualizar el elemento `value`; el elemento `comment` no está pensado para traducirse. Recuerde: al editar archivos XML, deben aplicarse secuencias de escape a los caracteres reservados, como `<`, `>`, `&` con `&lt;`, `&gt;` y `&amp;` si aparecen en `value` o `comment`.

<a name="incode" />

### <a name="using-resources-in-code"></a>Uso de recursos en el código

Las cadenas de los archivos de recursos RESX estarán disponibles para su uso en el código de la interfaz de usuario utilizando la clase `AppResources`. El elemento `name` asignado a cada cadena en el archivo RESX pasa a ser una propiedad de esa clase a la cual se puede hacer referencia en el código de Xamarin.Forms, tal como se muestra a continuación:

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

La interfaz de usuario en iOS, Android y la Plataforma universal de Windows (UWP) se representa del modo esperado, salvo que ahora es posible traducir la aplicación en varios idiomas porque el texto que se carga desde un recurso en vez de estar codificado de forma rígida. La siguiente captura de pantalla muestra la interfaz de usuario en cada plataforma antes de la traducción:

![](text-images/simple-example-english.png "Interfaces de usuario multiplataforma antes de la traducción")

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="testing-a-specific-language"></a>Prueba de un lenguaje específico

Puede resultar complicado cambiar el simulador o un dispositivo a idiomas distintos, especialmente durante el desarrollo, cuando se quieren probar rápidamente las distintas referencias culturales.

Puede forzar la carga de un idioma específico estableciendo el elemento `Culture` tal como se muestra en este fragmento de código:

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

Este enfoque, establecer la referencia cultural directamente en la clase `AppResources`, también se puede usar para implementar un selector de idioma dentro de la aplicación (en lugar de utilizar la configuración regional del dispositivo).

#### <a name="loading-embedded-resources"></a>Carga de recursos incrustados

El siguiente fragmento de código es útil cuando se intenta depurar problemas con los recursos incrustados (por ejemplo, los archivos RESX). Agregue este código a la aplicación (al principio del ciclo de vida de aplicación) y obtendrá una lista de todos los recursos incrustados en el ensamblado, que muestra el identificador del recurso completo:

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

En el archivo **AppResources.Designer.cs** (hacia la *línea 33*), se especifica el *nombre del administrador de recursos* completo (por ejemplo, `"UsingResxLocalization.Resx.AppResources"`) de forma similar al código siguiente:

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

Compruebe el **resultado de la aplicación** para los resultados del código de depuración que se muestra arriba, para confirmar que se indican los recursos correctos (es decir, `"UsingResxLocalization.Resx.AppResources"`).

Si no es así, la clase `AppResources` no podrá cargar sus recursos.
Compruebe lo siguiente para resolver los problemas que surgen cuando no se pueden encontrar los recursos:

- El espacio de nombres predeterminado para el proyecto coincide con el espacio de nombres raíz en el archivo **AppResources.Designer.cs**.
- Si el archivo **AppResources.resx** se encuentra en un subdirectorio, el nombre del subdirectorio debe formar parte del espacio de nombres *y* debe formar parte del identificador de recursos.
- El archivo **AppResources.resx** tiene la **Acción de compilación: EmbeddedResource**.
- La opción **Project Options > Source Code > .NET Naming Policies > Use Visual Studio-style resources names** (Opciones del proyecto > Código fuente > Directivas de nomenclatura de .NET > Usar nombres de recursos de estilo Visual Studio) está activada. Puede desactivarla, si lo prefiere, pero los espacios de nombres que se usan al hacer referencia a los recursos RESX tendrán que actualizarse en toda la aplicación.

#### <a name="doesnt-work-in-debug-mode-android-only"></a>No funciona en modo de DEPURACIÓN (solo Android)

Si las cadenas traducidas funcionan en las compilaciones de Android PUBLICADAS pero no durante la depuración, haga clic con el botón derecho en **Android Project** (Proyecto de Android), seleccione **Options > Build > Android Build** (Opciones > Compilar > Compilación de Android ) y asegúrese de que la opción **Fast assembly deployment** (Implementación de ensamblado rápido) NO esté activada. Esta opción provoca problemas con la carga de recursos y no debe usarse si se están probando aplicaciones localizadas.

### <a name="displaying-the-correct-language"></a>Visualización del idioma correcto

Hasta ahora hemos examinado cómo escribir código para que se puedan proporcionar traducciones, pero no para hacer que aparezcan en realidad. El código de Xamarin.Forms puede aprovechar recursos de .NET para cargar las traducciones en el idioma correcto, pero es necesario consultar el sistema operativo en cada plataforma para determinar el idioma que ha seleccionado el usuario.

Dado que se requiere algún código específico de la plataforma para obtener la preferencia de idioma del usuario, utilice un [servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para exponer esta información en la aplicación de Xamarin.Forms e implementarla para cada plataforma.

En primer lugar, defina una interfaz para exponer la referencia cultural preferida del usuario, de forma similar al código siguiente:

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

En segundo lugar, utilice [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) en la clase `App` de Xamarin.Forms para llamar a la interfaz y establecer la referencia cultural de nuestros recursos RESX en el valor correcto. Tenga en cuenta que no es necesario establecer manualmente este valor para la Plataforma universal de Windows, ya que el marco de trabajo de los recursos reconoce automáticamente el idioma seleccionado en estas plataformas.

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

El recurso `Culture` debe establecerse cuando la aplicación se carga por primera vez, para que se utilicen las cadenas de idioma correctas. Opcionalmente, puede actualizar este valor conforme a los eventos específicos de la plataforma que se generen en iOS o Android, si el usuario actualiza sus preferencias de idioma mientras se ejecuta la aplicación.

Las implementaciones para la interfaz `ILocalize` se muestran en la sección siguiente, [Código específico de la plataforma](#Platform-specific_Code). Estas implementaciones aprovechan esta clase auxiliar `PlatformCulture`:

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>Código específico de la plataforma

El código para detectar el idioma que se mostrará debe ser específico de la plataforma, porque iOS, Android y UWP exponen esta información de forma ligeramente distinta. El código para el servicio de dependencia `ILocalize` se proporciona a continuación para cada plataforma, junto con los requisitos específicos de la plataforma adicionales, para garantizar que el texto localizado se representa correctamente.

El código específico de la plataforma también debe controlar los casos donde el sistema operativo permite al usuario configurar un identificador de configuración regional que no es compatible con clase `CultureInfo` de .NET. En estos casos, se debe escribir código personalizado para detectar configuraciones regionales no compatibles y sustituir la mejor configuración regional compatible con .NET.

#### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

Los usuarios de iOS seleccionan su idioma preferido independientemente de la referencia cultural que da formato a la fecha y hora. Para cargar los recursos correctos para localizar una aplicación de Xamarin.Forms, basta con consultar la matriz `NSLocale.PreferredLanguages` para el primer elemento.

La siguiente implementación del servicio de dependencia `ILocalize` debe colocarse en el proyecto de aplicación de iOS. Dado que iOS usa caracteres de subrayado en lugar de guiones (que es la representación estándar de .NET) el código reemplaza el carácter de subrayado antes de crear instancias de la clase `CultureInfo`:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }

        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }

        string iOSToDotnetLanguage(string iOSLanguage)
        {
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }

        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Los bloques `try/catch` en el método `GetCurrentCultureInfo` imitan el comportamiento de reserva que se utiliza normalmente con los especificadores de configuración regional: si no se encuentra la coincidencia exacta, debe buscarse una coincidencia próxima basándose solo en el idioma (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidas en iOS, pero no corresponden a una `CultureInfo` válida en .NET; el código anterior intenta controlar esta cuestión.
>
> Por ejemplo, la pantalla de iOS **Ajustes > Idioma general &amp; Región**  le permite establecer el **Idioma** del teléfono como **inglés** pero la **Región** como **España**, lo cual da como resultado una cadena de configuración regional `"en-ES"`. Cuando falla la creación de `CultureInfo`, el código vuelve a utilizar solo las dos primeras letras para seleccionar el idioma que se mostrará.
>
> Los desarrolladores pueden modificar los métodos `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` para controlar los casos específicos necesarios para sus idiomas admitidos.

Algunos elementos de la interfaz de usuario definidos por el sistema los traduce automáticamente iOS, como el botón **Listo** del control `Picker`. Para obligar a iOS a traducir estos elementos, se debe indicar qué idiomas se admiten en el archivo **Info.plist**. Puede agregar estos valores a través de **Info.plist > Origen**, como se muestra aquí:

![Claves de localización en Info.plist](text-images/info-plist.png "Localization Keys in Info.plist")

De forma alternativa, abra el archivo **Info.plist** en un editor XML y edite los valores directamente:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

Una vez que haya implementado el servicio de dependencia y haya actualizado **Info.plist**, la aplicación de iOS podrá mostrar el texto traducido.

> [!NOTE]
> Tenga en cuenta que Apple trata el idioma portugués de un modo ligeramente distinto de lo que cabría esperar.
> En [su documentación](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) podemos leer una información relacionada, que indica la necesidad de _utilizar pt como identificador de idioma para el portugués que se utiliza en Brasil, y pt-PT como identificador de idioma para el portugués que se utiliza en Portugal_.
> Esto significa que cuando se elige el portugués en una configuración regional no estándar, el idioma de reserva será el portugués de Brasil en iOS, a menos que se escriba código para cambiar este comportamiento (como `ToDotnetFallbackLanguage` en el ejemplo anterior).

Para obtener más información sobre la localización de iOS, consulte [Localización de iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android expone la configuración regional seleccionada actualmente a través de `Java.Util.Locale.Default` y también usa un separador de carácter de subrayado en lugar de un guion (que será sustituido por el código siguiente). Agregue esta implementación de servicio de dependencia al proyecto de aplicación de Android:

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> Los bloques `try/catch` en el método `GetCurrentCultureInfo` imitan el comportamiento de reserva que se utiliza normalmente con los especificadores de configuración regional: si no se encuentra la coincidencia exacta, debe buscarse una coincidencia próxima basándose solo en el idioma (primer bloque de caracteres en la configuración regional).
>
> En el caso de Xamarin.Forms, algunas configuraciones regionales son válidas en Android, pero no corresponden a una `CultureInfo` válida en .NET; el código anterior intenta controlar esta cuestión.
>
> Los desarrolladores pueden modificar los métodos `iOSToDotnetLanguage` y `ToDotnetFallbackLanguage` para controlar los casos específicos necesarios para sus idiomas admitidos.

Una vez que este código se haya agregado al proyecto de aplicación de Android, podrá mostrar automáticamente las cadenas traducidas.

> [!WARNING]
> Si las cadenas traducidas funcionan en las compilaciones de Android PUBLICADAS pero no durante la depuración, haga clic con el botón derecho en **Android Project** (Proyecto de Android), seleccione **Options > Build > Android Build** (Opciones > Compilar > Compilación de Android ) y asegúrese de que la opción **Fast assembly deployment** (Implementación de ensamblado rápido) NO esté activada. Esta opción provoca problemas con la carga de recursos y no debe usarse si se están probando aplicaciones localizadas.

Para obtener más información sobre la localización de Android, consulte [Localización de Android](~/android/app-fundamentals/localization.md).

#### <a name="universal-windows-platform"></a>Plataforma universal de Windows

Los proyectos de la Plataforma universal de Windows (UWP) no requieren el servicio de dependencia. En su lugar, esta plataforma establece automáticamente la referencia cultural del recurso correctamente.

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

Expanda el nodo Propiedades en el proyecto de biblioteca de .NET Standard y haga doble clic en el archivo **AssemblyInfo.cs**. Agregue la siguiente línea al archivo para establecer el lenguaje de ensamblado de recursos independiente del idioma como inglés:

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

Esto informa al administrador de recursos acerca de la referencia cultural predeterminada de la aplicación y, por lo tanto, garantiza que las cadenas definidas en el archivo RESX independiente de idioma (**AppResources.resx**) se mostrarán cuando la aplicación se ejecute en una de las configuraciones regionales del inglés.

### <a name="example"></a>Ejemplo

Después de actualizar los proyectos específicos de plataforma, como se muestra anteriormente, y volver a compilar la aplicación con archivos RESX traducidos, las traducciones actualizadas estarán disponibles en cada aplicación. Esta es una captura de pantalla del código de ejemplo traducido a chino simplificado:

![](text-images/simple-example-hans.png "Interfaces de usuario multiplataforma traducidas a chino simplificado")

Para obtener más información sobre la localización de la plataforma universal de Windows, consulte [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/).

## <a name="localizing-xaml"></a>Localización de XAML

Al compilar una interfaz de usuario de Xamarin.Forms en XAML, el marcado tendrá un aspecto similar al siguiente, con cadenas insertadas directamente en el código XML:

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

El escenario ideal sería poder traducir los controles de interfaz de usuario directamente en XAML, lo cual puede hacerse mediante la creación de una *extensión de marcado*. El código para una extensión de marcado que expone los recursos RESX en XAML se muestra a continuación. Esta clase se debe agregar al código común de Xamarin.Forms (junto con las páginas XAML):

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

Las viñetas siguientes explican los elementos importantes en el código anterior:

- La clase se denomina `TranslateExtension` pero, por convención, podemos referirnos a ella como **Translate** en nuestro marcado.
- La clase implementa `IMarkupExtension`, que Xamarin.Forms requiere para que funcione.
- `"UsingResxLocalization.Resx.AppResources"` es el identificador de recurso para nuestros recursos RESX. Consta de nuestro espacio de nombres predeterminado, la carpeta donde se encuentran los archivos de recursos y el nombre del archivo RESX predeterminado.
- La clase `ResourceManager` se crea mediante `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)`, para determinar el ensamblado actual desde el cual se cargan los recursos, y se almacena en memoria caché en el campo `ResMgr` estático. Se crea como tipo `Lazy`, por lo que su creación se aplaza hasta que se usa en primer lugar en el método `ProvideValue`.
- `ci` usa el servicio de dependencia para obtener el idioma elegido del usuario desde el sistema operativo nativo.
- `GetString` es el método que recupera la cadena traducida real de los archivos de recursos. En la Plataforma universal de Windows, `ci` será un valor nulo porque la interfaz `ILocalize` no está implementada en estas plataformas. Esto equivale a llamar al método `GetString` solo con el primer parámetro. De forma alternativa, el marco de trabajo de los recursos reconocerá automáticamente la configuración regional y recuperará la cadena traducida desde el archivo RESX correspondiente.
- Se ha incluido el control de errores para ayudar a depurar los recursos que faltan iniciando una excepción (solo en modo `DEBUG`).

El fragmento de código XAML siguiente muestra cómo usar la extensión de marcado. Su funcionamiento requiere dos pasos:

1. Declarar el espacio de nombres `xmlns:i18n` personalizado en el nodo raíz. `namespace` y `assembly` deben tener exactamente la misma configuración del proyecto; en este ejemplo es idéntica, pero podría ser diferente en su proyecto específico.
2. Usar la sintaxis `{Binding}` en los atributos que normalmente deben contener el texto para llamar a la extensión de marcado `Translate`. La clave de recurso es el único parámetro necesario.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

La siguiente sintaxis más detallada también es válida para la extensión de marcado:

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>Localización de elementos específicos de plataforma

Aunque podemos gestionar la traducción de la interfaz de usuario en el código de Xamarin.Forms, algunos elementos deben realizarse en cada proyecto específico de plataforma. Esta sección explica cómo localizar los elementos siguientes:

- Nombre de la aplicación
- Imágenes

El proyecto de ejemplo incluye una imagen localizada denominada **flag.png**, a la cual se hace referencia en C# del modo siguiente:

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

También se hace referencia a la imagen de la bandera en el XAML, de este modo:

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

Todas las plataformas resolverán automáticamente las referencias de imágenes como éstas en las versiones localizadas de las imágenes, siempre y cuando se implementen las estructuras de proyecto que se explican a continuación.

### <a name="ios-application-project"></a>Proyecto de aplicación de iOS

iOS utiliza una denominación estándar, Proyectos de localización, o directorios **.lproj** que contienen recursos de cadena e imagen. Estos directorios pueden contener las versiones localizadas de imágenes que se usan en la aplicación y también el archivo **InfoPlist.strings** que se puede usar para localizar el nombre de la aplicación. Para obtener más información sobre la localización de iOS, consulte [Localización de iOS](~/ios/app-fundamentals/localization/index.md).

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra la aplicación de ejemplo de iOS con directorios **.lproj** específicos del idioma. El directorio del idioma español, denominado **es.lproj**, contiene las versiones localizadas de la imagen predeterminada, así como **flag.png**:

![](text-images/ios-resources.png "Directorios de proyecto de localización de iOS")

Cada directorio de idioma contiene una copia de **flag.png**, localizada para dicho idioma. Si no se proporciona ninguna imagen, el sistema operativo será la imagen del directorio de idioma predeterminado. Para obtener compatibilidad total con la pantalla Retina, debe proporcionar copias **@2x** y **@3x** de cada imagen.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de **InfoPlist.strings** es simplemente un único par clave-valor para configurar el nombre de la aplicación:

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

Cuando se ejecuta la aplicación, aparecen localizados el nombre de la aplicación y la imagen:

![](text-images/ios-imageicon.png "Localización de imagen y texto de la aplicación de ejemplo de iOS")

### <a name="android-application-project"></a>Proyecto de aplicación de Android

Android sigue un esquema diferente para almacenar imágenes localizadas, con directorios **drawable** y archivos **Strings** distintos con un sufijo de código de idioma. Cuando se requiere un código de configuración regional de cuatro letras (por ejemplo, zh-TW o pt-BR), tenga en cuenta que Android requiere una letra **r** adicional después del guion que precede al código de configuración regional (por ejemplo, zh-rTW o pt-rBR). Para obtener más información sobre la localización de Android, consulte [Localización de Android](~/android/app-fundamentals/localization.md).

#### <a name="images"></a>Imágenes

Esta captura de pantalla muestra el ejemplo de Android localizado con algunos directorios drawable y archivos Strings localizados:

![](text-images/android-resources.png "Directorios drawable y archivos Strings localizados de Android")

Tenga en cuenta que Android no usa los códigos zh-Hans y zh-Hant para chino simplificado y tradicional; en su lugar, solo admite los códigos específicos de país zh-CN y zh-TW.

Para admitir imágenes de una resolución distinta para las pantallas de alta densidad, cree carpetas de idioma adicionales con sufijos `-*dpi`, como **drawables-es-mdpi**, **drawables-es-xdpi**, **drawables-es-xxdpi**, y así sucesivamente. Consulte [Providing Alternative Android Resources](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) (Proporcionar recursos de Android alternativos) para obtener más información.

#### <a name="app-name"></a>Nombre de la aplicación

El contenido de **Strings.xml** es simplemente un único par clave-valor para configurar el nombre de la aplicación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

Actualice **MainActivity.cs** en el proyecto de aplicación de Android para que `Label` haga referencia a las cadenas XML.

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

La aplicación ahora localiza la imagen y el nombre de la aplicación. Esta es una captura de pantalla del resultado (en español):

![](text-images/android-imageicon.png "Localización de imagen y texto de la aplicación de ejemplo de Android")

### <a name="universal-windows-platform-application-projects"></a>Proyectos de aplicación de la Plataforma universal de Windows

La Plataforma universal de Windows posee una infraestructura de recursos que simplifica la localización de las imágenes y el nombre de la aplicación. Para obtener más información sobre la localización de la plataforma universal de Windows, consulte [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/).

#### <a name="images"></a>Imágenes

Las imágenes se pueden localizar colocándolas en una carpeta específica de recursos, como se muestra en la captura de pantalla siguiente:

![](text-images/uwp-image-folder-structure.png "Estructura de las carpetas de localización de imágenes en UWP")

En tiempo de ejecución, la infraestructura de recursos de Windows seleccionará la imagen adecuada según la configuración regional del usuario.

## <a name="summary"></a>Resumen

Las aplicaciones de Xamarin.Forms pueden localizarse con clases de globalización de .NET y archivos RESX. Además de requerirse una pequeña cantidad de código específico de plataforma para detectar el idioma que prefiere el usuario, la mayoría de los esfuerzos de localización se centran en el código común.

Por lo general, las imágenes se tratan específicamente para la plataforma, de cara a aprovechar la compatibilidad con varias resoluciones proporcionada en iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de localización de RESX](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Aplicación de ejemplo TodoLocalized](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalized)
- [Localización multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
- [Localización de Android](~/android/app-fundamentals/localization.md)
- [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Utilizar la clase CultureInfo (MSDN)](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [Buscar y utilizar recursos para una referencia cultural específica (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
