---
title: Metadatos de enlaces Java
description: El código de C# de Xamarin.Android llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstrae los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin.Android proporciona una herramienta que genera estos enlaces. Esta herramienta permite al desarrollador controlar cómo se crea un enlace mediante metadatos, lo que permite usar procedimientos como la modificación de espacios de nombres y el cambio de nombre de miembros. En este documento se explica cómo funcionan los metadatos, se resumen los atributos que admiten los metadatos y se explica cómo resolver los problemas de enlaces modificando estos metadatos.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 2a88888b2306589930ad6386fb69bbd3b48924b7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571381"
---
# <a name="java-bindings-metadata"></a>Metadatos de enlaces Java

_El código de C# de Xamarin.Android llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstrae los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin.Android proporciona una herramienta que genera estos enlaces. Esta herramienta permite al desarrollador controlar cómo se crea un enlace mediante metadatos, lo que permite usar procedimientos como la modificación de espacios de nombres y el cambio de nombre de miembros. En este documento se explica cómo funcionan los metadatos, se resumen los atributos que admiten los metadatos y se explica cómo resolver los problemas de enlaces modificando estos metadatos._

## <a name="overview"></a>Información general

Una **biblioteca de enlace de Java de Xamarin.Android** intenta automatizar gran parte del trabajo necesario para enlazar una biblioteca de Android con la ayuda de una herramienta conocida habitualmente como _generador de enlaces_. Al enlazar una biblioteca de Java, Xamarin.Android inspeccionará las clases de Java y generará una lista de todos los paquetes, tipos y miembros que se van a enlazar. Esta lista de API se almacena en un archivo XML que se puede encontrar en **\{directorio del proyecto}\obj\Release\api.xml** para una compilación de **VERSIÓN** y en **\{directorio del proyecto}\obj\Debug\api.xml** para una compilación de **DEPURACIÓN**.

![Ubicación del archivo api.xml en la carpeta obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

El generador de enlaces usará el archivo **api.xml** como guía para generar las clases contenedoras de C# necesarias. El contenido de este archivo XML es una variación del formato del _proyecto de código abierto Android de Google_.
El siguiente fragmento de código muestra un ejemplo del contenido de **api.xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

En este ejemplo, **api.xml** declara una clase en el paquete `android` denominado `Manifest`, que extiende el objeto `java.lang.Object`.

En muchos casos, la asistencia humana es necesaria para que la API de Java tenga un comportamiento más similar a .NET o para corregir los problemas que impiden que se compile el ensamblado de enlace. Por ejemplo, puede que haya que cambiar los nombres de los paquetes de Java a espacios de nombres .NET, cambiar el nombre de una clase o modificar el tipo de valor devuelto de un método.

Estos cambios no se logran modificando el archivo **api.xml** directamente.
En su lugar, los cambios se registran en archivos XML especiales proporcionados por la plantilla de biblioteca de enlaces de Java. Al compilar el ensamblado de enlace de Xamarin.Android, estos archivos de asignación influirán en el generador de enlaces al crear el ensamblado de enlace.

Estos archivos de asignación XML se pueden encontrar en la carpeta **Transforms** del proyecto:

- **MetaData.xml**: permite realizar cambios en la API final, como modificar el espacio de nombres del enlace generado. 

- **EnumFields.xml**: contiene la asignación entre las constantes `int` de Java y las enumeraciones `enums` de C#. 

- **EnumMethods.xml**: permite cambiar los parámetros de método y los tipos de valor devueltos de las constantes `int` de Java a las enumeraciones `enums` de C#. 

El archivo **MetaData.xml** es la mayor importación de estos archivos, ya que permite cambios generales en el enlace. Por ejemplo:

- Cambiar el nombre de los espacios de nombres, clases, métodos o campos para seguir las convenciones de .NET. 

- Quitar espacios de nombres, clases, métodos o campos que no son necesarios. 

- Mover clases a distintos espacios de nombres. 

- Agregar clases de compatibilidad adicionales para que el diseño del enlace siga los patrones de .NET Framework. 

Pasemos a analizar **Metadata.xml** con más detalle.

## <a name="metadataxml-transform-file"></a>Archivo de transformación Metadata.xml

Como ya sabemos, el archivo **Metadata.xml** utiliza el generador de enlaces para influir en la creación del ensamblado de enlace.
El formato de metadatos usa la sintaxis [XPath](https://www.w3.org/TR/xpath/) y es casi idéntico al de los *metadatos de GAPI*, que se describe en la guía de [metadatos de GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata). Esta implementación es prácticamente una implementación completa de XPath 1.0 y, por lo tanto, admite elementos del estándar 1.0. Este archivo es un eficaz mecanismo basado en XPath para cambiar, agregar, ocultar o quitar cualquier elemento o atributo del archivo de la API. Todos los elementos de reglas de la especificación de metadatos incluyen un atributo de ruta de acceso para identificar el nodo al que se aplicará la regla. Las siguientes reglas se aplican en el siguiente orden:

- **add-node**: anexa un nodo secundario al nodo especificado por el atributo path.
- **attr**: establece el valor de un atributo del elemento especificado por el atributo path.
- **remove-node**: quita los nodos que coinciden con una sintaxis XPath especificada.

El siguiente ejemplo es de un archivo **Metadata.xml**:

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

A continuación se enumeran algunos de los elementos XPath más usados para las API de Java:

- `interface`: se usa para buscar una interfaz de Java. Por ejemplo: `/interface[@name='AuthListener']`.

- `class`: se usa para buscar una clase. Por ejemplo: `/class[@name='MapView']`.

- `method`: se usa para buscar un método en una clase o interfaz de Java. Por ejemplo: `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter`: se usa para identificar un parámetro para un método. p. ej. `/parameter[@name='p0']`

### <a name="adding-types"></a>Incorporación de tipos

El elemento `add-node` indicará al proyecto de enlace de Xamarin.Android que agregue una nueva clase contenedora a **api.xml**. Por ejemplo, el siguiente fragmento de código dirigirá el generador de enlaces para crear una clase con un constructor y un solo campo:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Eliminación de tipos

Es posible indicar al generador de enlaces de Xamarin.Android que omite un tipo de Java y que no lo enlace. Esto se hace agregando un elemento XML `remove-node` al archivo **metadata.xml**:

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Cambio del nombre de los miembros

No es posible cambiar el nombre de los miembros si se edita directamente el archivo **api.xml** porque Xamarin.Android requiere los nombres de la interfaz nativa de Java (JNI) original. Por lo tanto, no se puede modificar el atributo `//class/@name`; si se hace, el enlace no funcionará.

Tenga en cuenta el caso en el que deseamos cambiar el nombre de un tipo, `android.Manifest`.
Para ello, podemos intentar editar directamente **api.xml** y cambiar el nombre de la clase de la manera siguiente:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Esto hará que el generador de enlaces cree el código siguiente de C# para la clase contenedora:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que se ha cambiado el nombre de la clase contenedora a `NewName`, mientras que el tipo de Java original sigue siendo `Manifest`. Ya no es posible que la clase de enlace de Xamarin.Android tenga acceso a los métodos de `android.Manifest`; la clase contenedora se enlaza a un tipo de Java inexistente.

Para cambiar correctamente el nombre administrado de un tipo encapsulado (o método), es necesario establecer el atributo `managedName` como se muestra en este ejemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes"></a>

#### <a name="renaming-eventarg-wrapper-classes"></a>Cambio de nombre de las clases contenedoras `EventArg`

Cuando el generador de enlaces de Xamarin.Android identifica un método establecedor `onXXX` para un _tipo de agente de escucha_, se generará un evento de C# y una subclase `EventArgs` con el fin de admitir una API de .NET con tipo para el patrón del cliente de escucha basado en Java. A modo de ejemplo, tenga en cuenta el siguiente método y clase de Java:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android quitará el prefijo `on` del método establecedor y, en su lugar, usará `2DSignNextManuever` como base para el nombre de la subclase `EventArgs`. El nombre de la subclase será similar a esto:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

No es un nombre de clase de C# válido. Para corregir este problema, el autor del enlace debe usar el atributo `argsType` y proporcionar un nombre de C# válido para la subclase `EventArgs`:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Atributos compatibles

En las secciones siguientes se describen algunos de los atributos para transformar las API de Java.

### <a name="argstype"></a>argsType

Este atributo se coloca en métodos establecedores para asignar un nombre a la subclase `EventArg` que se generará para admitir clientes de escucha de Java. Esto se describe con más detalle a continuación en la sección sobre cómo [cambiar el nombre de las clases contenedoras de EventArg](#Renaming_EventArg_Wrapper_Classes), más adelante en esta guía.

### <a name="eventname"></a>eventName

Especifica un nombre para un evento. Si está vacío, impide la generación de eventos.
Esto se describe con más detalle a continuación en la sección sobre cómo [cambiar el nombre de las clases contenedoras de EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Se utiliza para cambiar el nombre de un paquete, clase, método o parámetro. Por ejemplo, para cambiar el nombre de la clase de Java de `MyClass` a `NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

En el ejemplo siguiente se muestra una expresión XPath para cambiar el nombre del método de `java.lang.object.toString` a `Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` se utiliza para cambiar el tipo de valor devuelto de un método. En algunas situaciones, el generador de enlaces infiere incorrectamente el tipo de valor devuelto de un método Java, lo que producirá un error en tiempo de compilación. Una posible solución en esta situación es cambiar el tipo de valor devuelto del método.

Por ejemplo, el generador de enlaces considera que el método de Java `de.neom.neoreadersdk.resolution.compareTo()` debe devolver `int` y tomar `Object` como parámetros, lo que genera el mensaje de error: **Error CS0535: 'DE.Neom.Neoreadersdk.Resolution' no implementa el miembro de interfaz 'Java.Lang.IComparable.CompareTo(Java.Lang.Object)'** . El siguiente fragmento de código muestra cómo cambiar el tipo del primer parámetro del método de C# generado de `DE.Neom.Neoreadersdk.Resolution` a `Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Cambia el tipo de valor devuelto de un método. Esto no cambia el atributo devuelto (dado que los cambios en los atributos devueltos pueden dar lugar a cambios incompatibles en la firma JNI). En el ejemplo siguiente, el tipo de valor devuelto del método `append` se cambia de `SpannableStringBuilder` a `IAppendable` (recuerde que C# no admite tipos de valor devuelto covariantes):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>obfuscated

Las herramientas que ofuscan las bibliotecas de Java pueden interferir en el generador de enlaces de Xamarin.Android y su capacidad para generar clases contenedoras de C#. Estas son algunas de las características de las clases ofuscadas: 

- El nombre de clase incluye un símbolo **$** , es decir, **a$.class**.
- El nombre de clase está formado totalmente por caracteres en minúsculas, es decir, **a.class**.

Este fragmento de código es un ejemplo de cómo generar un tipo "no ofuscado" de C#:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Este atributo se puede utilizar para cambiar el nombre de una propiedad administrada.

Un caso especializado de uso de `propertyName` implica la situación en la que una clase de Java solo tiene un método captador para un campo. En esta situación, el generador de enlaces querría crear una propiedad de solo escritura, algo que no se recomienda en .NET. En el fragmento de código siguiente se muestra cómo "quitar" las propiedades de .NET estableciendo `propertyName` en una cadena vacía:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Tenga en cuenta que el generador de enlaces todavía creará los métodos establecedores y captadores.

### <a name="sender"></a>sender

Especifica qué parámetro de un método debe ser el parámetro `sender` cuando el método se asigna a un evento. El valor puede ser `true` o `false`. Por ejemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidad

Este atributo se usa para cambiar la visibilidad de una clase, método o propiedad. Por ejemplo, puede que sea necesario promover un método de Java `protected` para que el contenedor correspondiente de C# sea `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml y EnumMethods.xml

Hay casos en los que las bibliotecas de Android usan constantes de tipo entero para representar los estados que se pasan a las propiedades o los métodos de las bibliotecas. En muchos casos, resulta útil enlazar estas constantes de tipo entero a las enumeraciones en C#. Para facilitar esta asignación, use los archivos **EnumFields.xml** y **EnumMethods.xml** del proyecto de enlace. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definición de una enumeración mediante EnumFields.xml

El archivo **EnumFields.xml** contiene la asignación entre las constantes `int` de Java y las enumeraciones `enums` de C#. Vamos a crear el ejemplo siguiente de una enumeración de C# que se crea para un conjunto de constantes `int`: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aquí hemos tomado la clase de Java `SKRealReachSettings` y hemos definido una enumeración de C# llamada `SKMeasurementUnit` en el espacio de nombres `Skobbler.Ngx.Map.RealReach`. Las entradas de `field` definen el nombre de la constante de Java (por ejemplo, `UNIT_SECOND`), el nombre de la entrada de enumeración (por ejemplo, `Second`) y el valor entero representado por ambas entidades (por ejemplo, `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definición de métodos captadores y establecedores mediante EnumMethods.xml

El archivo **EnumMethods.xml** permite cambiar los parámetros de método y los tipos de valor devueltos de las constantes `int` de Java a las enumeraciones `enums` de C#. En otras palabras, asigna la lectura y escritura de enumeraciones de C# (definidas en el archivo **EnumFields.xml**) a la constante `int` de Java y los métodos `get` y `set`.

Dado que la enumeración `SKRealReachSettings` definida anteriormente, el siguiente archivo **EnumMethods.xml** definiría el captador o establecedor de esta enumeración:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La primera línea `method` asigna el valor devuelto del método de `getMeasurementUnit` Java a la enumeración `SKMeasurementUnit`. La segunda línea `method` asigna el primer parámetro de `setMeasurementUnit` a la misma enumeración.

Con todos estos cambios aplicados, puede usar el código siguiente en Xamarin.Android para establecer `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo Xamarin.Android usa metadatos para transformar una definición de API desde el *formato AOSP* de *Google*. Después de explicar los cambios que se pueden realizar con *Metadata.xml*, examinó las limitaciones que se producen al cambiar el nombre de los miembros y presentamos la lista de atributos XML admitidos, que describe cuándo debe usarse cada atributo.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadatos de GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
