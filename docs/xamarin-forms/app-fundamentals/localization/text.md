---
title: Localización de cadenas e imágenes en Xamarin.Forms
description: Las aplicaciones de Xamarin.Forms se pueden localizar con archivos de recursos de .NET.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 727bac49c5bff522125a2f21a9742e428b81c924
ms.sourcegitcommit: bb18e6b3b5d592374b17932efa1e85c3b5fbad57
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950747"
---
# <a name="no-locxamarinforms-string-and-image-localization"></a>Localización de cadenas e imágenes de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)

La localización es el proceso de adaptación de una aplicación para cumplir los requisitos lingüísticos o culturales específicos de un mercado objetivo. Para llevar a cabo la localización, es posible que haya que traducir el texto y las imágenes de una aplicación a varios idiomas. Una aplicación localizada muestra automáticamente el texto traducido en función de la configuración de la referencia cultural del dispositivo móvil:

![Capturas de pantallas de la aplicación de localización en iOS y Android](text-images/localizationdemo-screenshots.png)

.NET Framework incluye un mecanismo integrado para localizar aplicaciones con [archivos de recursos resx](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps). Un archivo de recursos almacena texto y otro contenido como pares de nombre/valor que permiten a la aplicación recuperar el contenido de una clave proporcionada. Los archivos de recursos permiten separar el contenido localizado del código de la aplicación.

El uso de archivos de recursos para localizar aplicaciones de Xamarin.Forms requiere que se realicen los pasos siguientes:

1. [Creación de archivos resx](#create-resx-files) que contengan texto traducido.
1. [Especificación de la referencia cultural predeterminada](#specify-the-default-culture) en el proyecto compartido.
1. [Localización de texto en Xamarin.Forms](#localize-text-in-xamarinforms).
1. [Localización de imágenes](#localize-images) en función de la configuración de la referencia cultural para cada plataforma.
1. [Localización del nombre de la aplicación](#localize-the-application-name) en cada plataforma.
1. [Prueba de la localización](#test-localization) en cada plataforma.

## <a name="create-resx-files"></a>Creación de archivos resx

Los archivos de recursos son archivos XML con una extensión **.resx** que se compilan en archivos de recursos binarios (.resources) durante el proceso de compilación. Visual Studio 2019 genera una clase que proporciona una API que permite recuperar recursos. Normalmente, una aplicación localizada contiene un archivo de recursos predeterminado con todas las cadenas usadas en la aplicación, así como archivos de recursos para cada idioma admitido. La aplicación de ejemplo tiene una carpeta **Resx** en el proyecto compartido que contiene los archivos de recursos y su archivo de recursos predeterminado se denomina **AppResources.resx**.

Los archivos de recursos contienen la siguiente información para cada elemento:

- **Nombre** especifica la clave que se usa para acceder al texto en el código.
- **Valor** especifica el texto traducido.
- **Comentario** es un campo opcional que contiene información adicional.

::: zone pivot="windows"

Para agregar un archivo de recursos se debe usar el cuadro de diálogo **Agregar nuevo elemento** en Visual Studio 2019:

![Adición de un nuevo recurso en Visual Studio 2019](text-images/pc-add-resource-file.png)

Una vez agregado el archivo, se pueden agregar filas para cada recurso de texto:

![Especificación de los recursos de texto predeterminados en un archivo .resx](text-images/pc-default-strings.png)

La opción desplegable **Modificador de acceso** determina el modo en que Visual Studio genera la clase utilizada para acceder a los recursos. Al establecer el modificador de acceso en **Público** o **Interno**, se genera una clase generada con el nivel de accesibilidad especificado. Si el modificador de acceso se establece en **Sin generación de código**, no se genera un archivo de clase. El archivo de recursos predeterminado debe configurarse para generar un archivo de clase, lo que se traduce en que se agrega un archivo con la extensión **.designer.cs** al proyecto.

Una vez creado el archivo de recursos predeterminado, se pueden crear archivos adicionales para cada referencia cultural que admita la aplicación. Cada archivo de recursos adicional debe incluir la referencia cultural de la traducción en el nombre del archivo y debe tener el **Modificador de acceso** establecido en **Sin generación de código**.

Al iniciarse, la aplicación intenta resolver una solicitud de recursos por orden de especificidad. Por ejemplo, si la referencia cultural del dispositivo es **en-US** la aplicación busca archivos de recursos por este orden:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (predeterminado)

En la captura de pantalla siguiente se muestra un archivo de traducción al español denominado **AppResources.es.cs**:

![Especificación de los recursos de texto en español predeterminados en un archivo .resx](text-images/pc-spanish-strings.png)

El archivo de traducción utiliza los mismos valores de **Nombre** especificados en el archivo predeterminado, pero contiene cadenas de idioma español en la columna **Value**. Además, el **Modificador de acceso** se establece en **Sin generación de código**.

::: zone-end
::: zone pivot="macos"

Se agrega un archivo de recursos con el cuadro de diálogo **Agregar nuevo archivo** en Visual Studio 2019 para Mac:

![Adición de un nuevo recurso en Visual Studio 2019 para Mac](text-images/mac-add-resource-file.png)

Una vez creado un archivo de recursos predeterminado, se puede agregar texto creando elementos `data` dentro del elemento `root` en el archivo de recursos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="AddButton" xml:space="preserve">
        <value>Add Note</value>
    </data>
    <data name="NotesLabel" xml:space="preserve">
        <value>Notes:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>e.g. Get Milk</value>
    </data>
</root>
```

Se puede crear un archivo de clase **.designer.cs** estableciendo una propiedad **Herramienta personalizada** en las opciones del archivo de recursos:

![Especificación de Herramienta personalizada en las propiedades de un archivo de recursos](text-images/mac-resx-properties.png)

Al establecer **Herramienta personalizada** en **PublicResXFileCodeGenerator** se producirá una clase generada con acceso `public`. Al establecer **Herramienta personalizada** en **InternalResXFileCodeGenerator** se producirá una clase generada con acceso `internal`. Un valor vacío de **Herramienta personalizada** no generará ninguna clase. El nombre de la clase generada coincidirá con el nombre del archivo de recursos. Por ejemplo, el archivo **AppResources.resx** se traducirá en la creación de una clase `AppResources` en un archivo denominado **AppResources.designer.cs**.

Se pueden crear archivos de recursos adicionales para cada referencia cultural admitida. Cada archivo de idioma debe incluir la referencia cultural de traducción en el nombre de archivo, por lo que un archivo destinado a **es-MX** debe denominarse **AppResources.es-MX.resx**.

Al iniciarse, la aplicación intenta resolver una solicitud de recursos por orden de especificidad. Por ejemplo, si la referencia cultural del dispositivo es **en-US** la aplicación busca archivos de recursos por este orden:

1. AppResources.en-US.resx
1. AppResources.en.resx
1. AppResources.resx (predeterminado)

Los archivos de traducción de idioma deben tener los mismos valores de **Nombre** especificados como archivo predeterminado. En el XML siguiente se muestra un archivo de traducción al español denominado **AppResources.es.resx**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<root>
    ...
    <data name="NotesLabel" xml:space="preserve">
        <value>Notas:</value>
    </data>
    <data name="NotesPlaceholder" xml:space="preserve">
        <value>por ejemplo . comprar leche</value>
    </data>
    <data name="AddButton" xml:space="preserve">
        <value>Agregar nuevo elemento</value>
    </data>
</root>
```

::: zone-end

## <a name="specify-the-default-culture"></a>Especificación de la referencia cultural predeterminada

Para que los archivos de recursos funcionen correctamente, la aplicación debe tener un valor de `NeutralResourcesLanguage` especificado. En el proyecto compartido, hay que personalizar el archivo **AssemblyInfo.cs** para especificar la referencia cultural predeterminada. En el código siguiente se muestra cómo establecer el valor de `NeutralResourcesLanguage` en **en-US** en el archivo **AssemblyInfo.cs**:

```csharp
using System.Resources;

// The resources from the neutral language .resx file are stored directly
// within the library assembly. For that reason, changing en-US to a different
// language in this line will not by itself change the language shown in the
// app. See the discussion of UltimateResourceFallbackLocation in the
// documentation for additional information:
// https://docs.microsoft.com/dotnet/api/system.resources.neutralresourceslanguageattribute
[assembly: NeutralResourcesLanguage("en-US")]
```

> [!WARNING]
> Si no especifica el atributo `NeutralResourcesLanguage`, la clase `ResourceManager` devuelve valores `null` para las referencias culturales que no incluyan un archivo de recursos específico. Cuando se especifica la referencia cultural predeterminada, la clase `ResourceManager` devuelve los resultados del archivo resx predeterminado para las referencias culturales no admitidas. Por lo tanto, se recomienda especificar siempre el valor de `NeutralResourcesLanguage` para que se muestre el texto de las referencias culturales no admitidas.

Una vez que se ha creado un archivo de recursos predeterminado y se ha especificado la referencia cultural predeterminada en el archivo **AssemblyInfo.cs**, la aplicación puede recuperar cadenas localizadas durante el inicio.

Para obtener más información sobre los archivos de recursos, vea [Creación de archivos de recursos para aplicaciones .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps).

## <a name="specify-supported-languages-on-ios"></a>Especificación de los idiomas admitidos en iOS

En iOS, debe declarar todos los idiomas admitidos en el archivo **Info.plist** del proyecto. En el archivo **Info.plist**, use la vista **Origen** para establecer una matriz para la clave `CFBundleLocalizations` y proporcione valores que se correspondan a los archivos Resx. Además, asegúrese de establecer un idioma esperado a través de la clave `CFBundleDevelopmentRegion`:

![Captura de pantalla del editor de Info.plist en la que se muestra la sección Localizaciones](text-images/info-plist.png)

Como alternativa, abra el archivo **Info.plist** en un editor XML y agregue lo siguiente:

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

> [!NOTE]
> Apple trata el idioma portugués de un modo ligeramente distinto de lo que cabría esperar. Para obtener más información, vea [Adición de idiomas](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2) en developer.apple.com.

Para obtener más información, vea [Especificación de los idiomas predeterminados y admitidos en Info.plist](~/ios/app-fundamentals/localization/index.md#specifying-default-and-supported-languages-in-infoplist).

## <a name="localize-text-in-no-locxamarinforms"></a>Localización de texto en Xamarin.Forms

El texto se localiza en Xamarin.Forms mediante la clase `AppResources` generada. Esta clase se denomina según el nombre del archivo de recursos predeterminado. Dado que el archivo de recursos del proyecto de ejemplo se denomina **AppResources.cs**, Visual Studio genera una clase coincidente denominada `AppResources`. Las propiedades estáticas se generan en la clase `AppResources` para cada fila del archivo de recursos. Las siguientes propiedades estáticas se generan en la clase `AppResources` de la aplicación de ejemplo:

- AddButton
- NotesLabel
- NotesPlaceholder

El acceso a estos valores como propiedades [x:Static](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md#the-xstatic-markup-extension) permite mostrar texto localizado en XAML:

```xaml
<ContentPage ...
             xmlns:resources="clr-namespace:LocalizationDemo.Resx">
    <Label Text="{x:Static resources:AppResources.NotesLabel}" />
    <Entry Placeholder="{x:Static resources:AppResources.NotesPlaceholder}" />
    <Button Text="{x:Static resources:AppResources.AddButton}" />
</ContentPage>
```

El texto localizado también se puede recuperar con código:

```csharp
public LocalizedCodePage()
{
    Label notesLabel = new Label
    {
        Text = AppResources.NotesLabel,
        // ...
    };

    Entry notesEntry = new Entry
    {
        Placeholder = AppResources.NotesPlaceholder,
        //...
    };

    Button addButton = new Button
    {
        Text = AppResources.AddButton,
        // ...
    };

    Content = new StackLayout
    {
        Children = {
            notesLabel,
            notesEntry,
            addButton
        }
    };
}
```

Las propiedades de la clase `AppResources` usan el valor actual de `System.Globalization.CultureInfo.CurrentUICulture` para determinar de qué archivo de recursos de referencia cultural se deben recuperar los valores.

## <a name="localize-images"></a>Localización de imágenes

Además de almacenar texto, los archivos resx pueden almacenar algo más que texto, también pueden almacenar imágenes y datos binarios. Ahora bien, los dispositivos móviles tienen una gama de tamaños y densidades de pantalla y cada plataforma móvil tiene funcionalidad para mostrar imágenes dependientes de la densidad. Por lo tanto, se debe usar la funcionalidad de localización de imágenes de plataforma en lugar de almacenar imágenes en archivos de recursos.

### <a name="localize-images-on-android"></a>Localización de imágenes en Android

En Android, los recursos Drawable localizados (imágenes) se almacenan con una convención de nomenclatura para carpetas en el directorio **Resources**. Las carpetas se denominan **Drawable** con un sufijo para el idioma de destino. Por ejemplo, la carpeta de idioma español se denomina **drawable-es**.

Cuando se requiere un código de configuración regional de cuatro letras, Android requiere un carácter **r** adicional después del guión. Por ejemplo, la carpeta de configuración regional de México (es-MX) debe denominarse **drawable-es-rMX**. Los nombres de archivo de imagen de cada carpeta de configuración regional deben ser idénticos:

![Imágenes localizadas en el proyecto de Android](text-images/pc-android-images.png)

Para obtener más información, vea [Localización de Android](~/android/app-fundamentals/localization.md).

### <a name="localize-images-on-ios"></a>Localización de imágenes en iOS

En iOS, las imágenes localizadas se almacenan con una convención de nomenclatura para carpetas en el directorio **Resources**. La carpeta predeterminada se denomina **Base.lproj**. Las carpetas específicas del idioma se denominan con el nombre de idioma o de configuración regional, seguido de **.lproj**. Por ejemplo, la carpeta de idioma español se denomina **es.lproj**.

Los códigos locales de cuatro letras funcionan igual que los códigos de idioma de dos letras. Por ejemplo, la carpeta de configuración regional de México (es-MX) debe denominarse **es-MX.lproj**. Los nombres de archivo de imagen de cada carpeta de configuración regional deben ser idénticos:

![Imágenes localizadas en el proyecto de iOS](text-images/pc-ios-images.png)

> [!NOTE]
> iOS admite la creación de un catálogo de recursos localizado en lugar de usar la estructura de carpetas. lproj. Aunque se debe crear y administrar en Xcode.

Para obtener más información, vea [Localización de iOS](~/ios/app-fundamentals/localization/index.md).

### <a name="localize-images-on-uwp"></a>Localización de imágenes en UWP

En UWP, las imágenes localizadas se almacenan con una convención de nomenclatura para carpetas en el directorio **Assets/Images**. Las carpetas se denominan con el idioma o la configuración regional. Por ejemplo, la carpeta de idioma español se denomina **es** y la carpeta de configuración regional de México debe tener el nombre **es-MX**. Los nombres de archivo de imagen de cada carpeta de configuración regional deben ser idénticos:

![Imágenes localizadas en el proyecto de UWP](text-images/pc-uwp-images.png)

Para obtener más información, consulte [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/).

### <a name="consume-localized-images"></a>Consumo de imágenes localizadas

Dado que cada plataforma almacena las imágenes con una estructura de archivos única, el código XAML usa la clase `OnPlatform` para establecer la propiedad `ImageSource` según la plataforma actual:

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

> [!NOTE]
> La extensión de marcado `OnPlatform` ofrece una forma más concisa de especificar valores específicos de la plataforma. Para obtener más información, vea [Extensión de marcado OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

El origen de la imagen se puede establecer en función de la propiedad `Device.RuntimePlatform` en el código:

```csharp
string imgSrc = Device.RuntimePlatform == Device.UWP ? "Assets/Images/flag.png" : "flag.png";
Image flag = new Image
{
    Source = ImageSource.FromFile(imgSrc),
    WidthRequest = 100
};
```

## <a name="localize-the-application-name"></a>Localización del nombre de la aplicación

El nombre de la aplicación se especifica por plataforma y no utiliza archivos de recursos resx. Para localizar el nombre de la aplicación en Android, vea [Localización del nombre de la aplicación en Android](~/android/app-fundamentals/localization.md#stringsxml-file-format). Para localizar el nombre de la aplicación en iOS, vea [Localización del nombre de la aplicación en iOS](~/ios/app-fundamentals/localization/index.md#app-name). Para localizar el nombre de la aplicación en UWP, vea [Localización de cadenas en el manifiesto de paquete de UWP](https://docs.microsoft.com/windows/uwp/app-resources/localize-strings-ui-manifest).

## <a name="test-localization"></a>Prueba de la localización

La mejor manera de probar la localización consiste en cambiar el idioma del dispositivo. Es posible establecer el valor de `System.Globalization.CultureInfo.CurrentUICulture` en el código, pero el comportamiento es incoherente entre las plataformas, por lo que no se recomienda para las pruebas.

En iOS, en la aplicación de configuración, puede establecer el idioma de cada aplicación específicamente sin cambiar el idioma del dispositivo.

En Android, la configuración de idioma se detecta y se almacena en caché cuando se inicia la aplicación. Si cambia de idioma, es posible que haya que salir de la aplicación y reiniciarla para ver los cambios aplicados.

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de ejemplo de localización](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingresxlocalization)
- [Creación de archivos de recursos para aplicaciones .NET](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps)
- [Localización multiplataforma](~/cross-platform/app-fundamentals/localization.md)
- [Utilizar la clase CultureInfo (MSDN)](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)
- [Localización de Android](~/android/app-fundamentals/localization.md)
- [Localización de iOS](~/ios/app-fundamentals/localization/index.md)
- [Localización de UWP](/windows/uwp/design/globalizing/globalizing-portal/)
- [Buscar y utilizar recursos para una referencia cultural específica (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
