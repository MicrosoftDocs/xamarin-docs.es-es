---
title: 'Parte 4: Trabajo con varias plataformas'
description: En este documento se describe cómo controlar la divergencia de las aplicaciones en función de la plataforma o la capacidad. Describe el tamaño de la pantalla, las metáforas de navegación, la entrada táctil y los gestos, las notificaciones de envío y los paradigmas de interfaz como listas y pestañas.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: fb01d0ca56365fa95aa563ca99394dea39dc7d31
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288882"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4: Trabajo con varias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Control de &amp; las características de divergencia de plataforma

La divergencia no es solo un problema de ' multiplataforma '; los dispositivos de la plataforma "mismo" tienen capacidades diferentes (especialmente la gran variedad de dispositivos Android que están disponibles). Lo más obvio y básico es el tamaño de la pantalla, pero otros atributos de dispositivo pueden variar y requieren que una aplicación Compruebe ciertas funcionalidades y se comporten de manera diferente en función de su presencia (o ausencia).

Esto significa que todas las aplicaciones deben tratar con una degradación correcta de la funcionalidad, o bien presentar un conjunto de características poco atractivas, más bajas y comunes. La integración profunda de Xamarin con los SDK nativos de cada plataforma permite a las aplicaciones aprovechar la funcionalidad específica de la plataforma, por lo que tiene sentido diseñar aplicaciones para que usen esas características.

Consulte la documentación sobre las capacidades de la plataforma para obtener información general sobre cómo las plataformas difieren en la funcionalidad.

## <a name="examples-of-platform-divergence"></a>Ejemplos de divergencia de plataforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentales que existen entre plataformas

Hay algunas características de las aplicaciones móviles que son universales.
Se trata de conceptos de nivel superior que, por lo general, son verdaderos de todos los dispositivos y, por lo tanto, constituyen la base del diseño de la aplicación:

- Selección de características a través de pestañas o menús
- Listas de datos y desplazamiento
- Vistas únicas de los datos
- Editar vistas únicas de datos
- Desplazarse hacia atrás

Al diseñar el flujo de pantalla de alto nivel, puede basar una experiencia de usuario común en estos conceptos.

### <a name="platform-specific-attributes"></a>Atributos específicos de la plataforma

Además de los elementos básicos que existen en todas las plataformas, debe abordar las principales diferencias de la plataforma en el diseño. Es posible que tenga que tener en cuenta (y escribir código específicamente para controlar) estas diferencias:

- **Tamaños de pantalla** : algunas plataformas (como iOS y versiones anteriores Windows Phone) tienen tamaños de pantalla normalizados que son relativamente fáciles de usar. Los dispositivos Android tienen una gran variedad de dimensiones de pantalla, lo que requiere más esfuerzo por la compatibilidad en la aplicación.
- **Metáforas de navegación** : difiere entre plataformas (por ejemplo, botón "atrás" del hardware, control de la interfaz de usuario de panorama y dentro de las plataformas (Android 2 y 4, iPhone vs iPad).
- **Teclados** : algunos dispositivos Android tienen teclados físicos mientras que otros solo tienen un teclado de software. El código que detecta cuándo un teclado blando oculta parte de la pantalla debe ser sensible a estas diferencias.
- **Táctil y gestos** : la compatibilidad del sistema operativo con el reconocimiento de gestos varía, especialmente en versiones anteriores de cada sistema operativo. Las versiones anteriores de Android tienen compatibilidad muy limitada con las operaciones táctiles, lo que significa que la compatibilidad con dispositivos más antiguos puede requerir código independiente
- **Notificaciones de envío** : hay diferentes funcionalidades e implementaciones en cada plataforma (por ejemplo, Iconos dinámicos en Windows).

### <a name="device-specific-features"></a>Características específicas del dispositivo

Determinar las características mínimas necesarias para la aplicación. o bien, cuando decida qué características adicionales aprovechar en cada plataforma. El código será necesario para detectar características y deshabilitar la funcionalidad u ofrecer alternativas (por ejemplo, una alternativa a la ubicación geográfica podría ser permitir que el usuario escriba una ubicación o elija un mapa):

- **Cámara** : la funcionalidad es diferente en los dispositivos: algunos dispositivos no tienen una cámara y otros tienen cámaras delante y detrás. Algunas cámaras pueden grabar vídeo.
- **Mapas de & de ubicación geográfica** : la compatibilidad con la ubicación de GPS o Wi-Fi no está presente en todos los dispositivos. Las aplicaciones también deben satisfacer los distintos niveles de precisión que admite cada método.
- **Acelerómetro, giroscopio y brújula** : estas características suelen encontrarse en una selección de dispositivos en cada plataforma, por lo que las aplicaciones casi siempre necesitan proporcionar una reserva cuando no se admite el hardware.
- **Twitter y Facebook** : solo ' integrado ' en IOS5 y iOS6, respectivamente. En versiones anteriores y otras plataformas, deberá proporcionar sus propias funciones de autenticación e interactuar directamente con la API de cada servicio.
- **Comunicaciones de campo en proximidad (NFC)** : solo en (algunos) teléfonos Android (en el momento de escribir).

## <a name="dealing-with-platform-divergence"></a>Abordar la divergencia de la plataforma

Existen dos enfoques diferentes para admitir varias plataformas de la misma base de código, cada una con su propio conjunto de ventajas y desventajas.

- **Abstracción de plataforma** : patrón de fachada empresarial, proporciona un acceso unificado entre plataformas y abstrae las implementaciones de plataforma concretas en una única API unificada.
- **Implementación divergente** : invocación de características específicas de la plataforma a través de implementaciones divergentes a través de herramientas de arquitectura como interfaces y herencia o compilación condicional.

## <a name="platform-abstraction"></a>Abstracción de plataforma

### <a name="class-abstraction"></a>Abstracción de clases

Usar interfaces o clases base definidas en el código compartido e implementadas o extendidas en proyectos específicos de la plataforma. Escribir y extender el código compartido con abstracciones de clase es especialmente adecuado para las bibliotecas de clases portables porque tienen un subconjunto limitado del marco de trabajo a su disposición y no pueden contener directivas de compilador para admitir ramas de código específicas de la plataforma.

#### <a name="interfaces"></a>Interfaces

El uso de interfaces permite implementar clases específicas de la plataforma que todavía se pueden pasar a las bibliotecas compartidas para aprovechar el código común.

La interfaz se define en el código compartido y se pasa a la biblioteca compartida como un parámetro o una propiedad.

Las aplicaciones específicas de la plataforma pueden entonces implementar la interfaz y seguir aprovechando el código compartido para "procesarlo".

 **Ventajas**

La implementación puede contener código específico de la plataforma e incluso hacer referencia a bibliotecas externas específicas de la plataforma.

 **Desventajas**

Tener que crear y pasar implementaciones en el código compartido. Si la interfaz se usa en profundidad dentro del código compartido, terminará pasando a través de varios parámetros de método o, de lo contrario, se insertará a través de la cadena de llamadas. Si el código compartido utiliza muchas interfaces diferentes, se deben crear y establecer en el código compartido en alguna parte.

#### <a name="inheritance"></a>Herencia

El código compartido podría implementar clases abstractas o virtuales que podrían extenderse en uno o varios proyectos específicos de la plataforma. Esto es similar al uso de interfaces pero con algún comportamiento ya implementado. Hay diferentes puntos de vista sobre si las interfaces o la herencia son una mejor opción de diseño: C# en concreto, dado que solo permite la herencia única, puede determinar la forma en que se pueden diseñar las API en el futuro. Utilice la herencia con precaución.

Las ventajas y desventajas de las interfaces se aplican igualmente a la herencia, con la ventaja adicional de que la clase base puede contener algún código de implementación (quizás una implementación independiente de la plataforma que se pueda extender opcionalmente).

## <a name="xamarinforms"></a>Xamarin.Forms

Consulte la documentación de [Xamarin. Forms](~/get-started/index.yml) .

### <a name="other-cross-platform-libraries"></a>Otras bibliotecas multiplataforma

Estas bibliotecas también ofrecen funcionalidad multiplataforma para C# desarrolladores:

- [**Xamarin. Essentials**](~/essentials/index.md) : API multiplataforma para características comunes.
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) : gráficos 2D multiplataforma.

## <a name="conditional-compilation"></a>Compilación condicional

Hay algunas situaciones en las que el código compartido todavía necesitará trabajar de forma diferente en cada plataforma, lo que podría tener acceso a clases o características que se comporten de manera diferente. La compilación condicional funciona mejor con proyectos de recursos compartidos, donde se hace referencia al mismo archivo de código fuente en varios proyectos que tienen distintos símbolos definidos.

Los proyectos de Xamarin `__MOBILE__` siempre definen lo que es cierto para los proyectos de aplicación iOS y Android (tenga en cuenta el doble subrayado anterior y posterior a la corrección en estos símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

Xamarin. iOS define `__IOS__` que puede usar para detectar dispositivos iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

También hay símbolos de inspección y específicos de TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

El código que solo se debe compilar en aplicaciones de Xamarin. Android puede utilizar lo siguiente

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versión de la API también define una nueva Directiva de compilador, por lo que código como este le permitirá agregar características si las API más recientes son de destino. Cada nivel de API incluye todos los símbolos de nivel "inferiores". Esta característica no es realmente útil para admitir varias plataformas; Normalmente, `__ANDROID__` el símbolo será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Actualmente no hay ningún símbolo integrado para Xamarin. Mac, pero puede Agregar el suyo propio en las opciones del proyecto de aplicación Mac **> Compilar > compilador** en el cuadro **definir símbolos** , o editar el archivo **. csproj** y agregarlo `__MAC__`(por ejemplo,).

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Use `WINDOWS_UWP`. No hay ningún carácter de subrayado alrededor de la cadena como los símbolos de la plataforma Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Usar la compilación condicional

Un ejemplo sencillo de caso práctico de compilación condicional es establecer la ubicación del archivo para el archivo de base de datos de SQLite. Las tres plataformas tienen requisitos ligeramente diferentes para especificar la ubicación del archivo:

- **iOS** : Apple prefiere que los datos que no son de usuario se coloquen en una ubicación específica (el directorio de la biblioteca), pero no hay ninguna constante del sistema para este directorio. Se requiere código específico de la plataforma para compilar la ruta de acceso correcta.
- **Android** : la ruta de acceso del `Environment.SpecialFolder.Personal` sistema devuelta por es una ubicación aceptable para almacenar el archivo de base de datos.
- **Windows Phone** : el mecanismo de almacenamiento aislado no permite especificar una ruta de acceso completa, solo una ruta de acceso relativa y un nombre de archivo.
- **Plataforma universal de Windows** : usa `Windows.Storage` las API de.

En el código siguiente `DatabaseFilePath` se usa la compilación condicional para asegurarse de que es correcta para cada plataforma:

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

El resultado es una clase que se puede compilar y usar en todas las plataformas, colocando el archivo de base de datos SQLite en una ubicación diferente en cada plataforma.
