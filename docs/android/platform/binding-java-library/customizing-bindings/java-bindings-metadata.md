---
title: Metadatos de enlaces Java
description: C#el código de Xamarin. Android llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstrae los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin. Android proporciona una herramienta que genera estos enlaces. Estas herramientas permiten al desarrollador controlar cómo se crea un enlace mediante metadatos, lo que permite procedimientos como la modificación de espacios de nombres y el cambio de nombre de los miembros. En este documento se explica cómo funcionan los metadatos, se resumen los atributos que admiten los metadatos y se explica cómo resolver los problemas de enlace mediante la modificación de estos metadatos.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 046c392709f2c94664120e9fac3f4198e9f50dbf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756605"
---
# <a name="java-bindings-metadata"></a>Metadatos de enlaces Java

_C#el código de Xamarin. Android llama a las bibliotecas de Java a través de enlaces, que son un mecanismo que abstrae los detalles de bajo nivel que se especifican en Java Native Interface (JNI). Xamarin. Android proporciona una herramienta que genera estos enlaces. Estas herramientas permiten al desarrollador controlar cómo se crea un enlace mediante metadatos, lo que permite procedimientos como la modificación de espacios de nombres y el cambio de nombre de los miembros. En este documento se explica cómo funcionan los metadatos, se resumen los atributos que admiten los metadatos y se explica cómo resolver los problemas de enlace mediante la modificación de estos metadatos._

## <a name="overview"></a>Información general

Una **biblioteca de enlace de Java** de Xamarin. Android intenta automatizar gran parte del trabajo necesario para enlazar una biblioteca de Android existente con la ayuda de una herramienta que a veces se conoce como generador de _enlaces_. Al enlazar una biblioteca de Java, Xamarin. Android inspeccionará las clases de Java y generará una lista de todos los paquetes, tipos y miembros que se van a enlazar. Esta lista de API se almacena en un archivo XML que se puede encontrar en  **\{el directorio de proyecto} \obj\Release\api.XML** para una compilación de **versión** y en  **\{el directorio de proyecto} \obj\Debug\api.XML** para una compilación de **depuración** .

![Ubicación del archivo API. XML en la carpeta obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

El generador de enlaces usará el archivo **API. XML** como guía para generar las clases de contenedor C# necesarias. El contenido de este archivo XML es una variación del formato de _proyecto de código abierto de Android_ de Google.
El siguiente fragmento de código es un ejemplo del contenido de **API. XML**:

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

En este ejemplo, **API. XML** declara una clase en el `android` paquete denominado `Manifest` que extiende el `java.lang.Object`.

En muchos casos, la asistencia humana es necesaria para que la API de Java sienta más ".NET como" o para corregir los problemas que impiden la compilación del ensamblado de enlace. Por ejemplo, puede ser necesario cambiar los nombres de los paquetes Java a espacios de nombres .NET, cambiar el nombre de una clase o cambiar el tipo de valor devuelto de un método.

Estos cambios no se logran mediante la modificación de **API. XML** directamente.
En su lugar, los cambios se registran en archivos XML especiales proporcionados por la plantilla biblioteca de enlaces de Java. Al compilar el ensamblado de enlace de Xamarin. Android, estos archivos de asignación influirán en el generador de enlaces al crear el ensamblado de enlace.

Estos archivos de asignación XML se pueden encontrar en la carpeta **Transformations** del proyecto:

- **Metadata. XML** &ndash; permite realizar cambios en la API final, como cambiar el espacio de nombres del enlace generado. 

- **EnumFields. XML** &ndash; contiene la asignación entre las `int` constantes de Java C# `enums` y. 

- **Enummethods (. XML** &ndash; permite cambiar los parámetros de método y los tipos `int` de valor devueltos de las constantes de Java a C# `enums` . 

El archivo **Metadata. XML** es la importación más importante de estos archivos, ya que permite cambios generales en el enlace, como:

- Cambiar el nombre de los espacios de nombres, clases, métodos o campos para seguir las convenciones de .NET. 

- Quitar espacios de nombres, clases, métodos o campos que no son necesarios. 

- Mover clases a distintos espacios de nombres. 

- Agregar clases de soporte adicionales para hacer que el diseño del enlace siga los patrones de .NET Framework. 

Permite pasar a analizar **Metadata. XML** con más detalle.

## <a name="metadataxml-transform-file"></a>Metadata. XML (archivo de transformación)

Como ya hemos aprendido, el generador de enlaces usa el archivo **Metadata. XML** para influir en la creación del ensamblado de enlace.
El formato de metadatos usa la sintaxis de [XPath](https://www.w3.org/TR/xpath/) y es casi idéntico a los *metadatos de GAPI* descritos en la guía de [metadatos de GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) . Esta implementación es casi una implementación completa de XPath 1,0 y, por lo tanto, admite elementos del estándar 1,0. Este archivo es un eficaz mecanismo basado en XPath para cambiar, agregar, ocultar o quitar cualquier elemento o atributo en el archivo de la API. Todos los elementos de regla de la especificación de metadatos incluyen un atributo de ruta de acceso para identificar el nodo al que se aplicará la regla. Las reglas se aplican en el orden siguiente:

- **agregar nodo** &ndash; Anexa un nodo secundario al nodo especificado por el atributo de ruta de acceso.
- **ATTR** &ndash; Establece el valor de un atributo del elemento especificado por el atributo de ruta de acceso.
- **quitar nodo** &ndash; Quita los nodos que coinciden con un XPath especificado.

El siguiente es un ejemplo de un archivo **Metadata. XML** :

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

- `interface`&ndash; Se usa para buscar una interfaz de Java. por ejemplo,. `/interface[@name='AuthListener']`

- `class`&ndash; Se utiliza para buscar una clase. por ejemplo,. `/class[@name='MapView']`

- `method`&ndash; Se usa para buscar un método en una clase o interfaz de Java. por ejemplo,. `/class[@name='MapView']/method[@name='setTitleSource']`

- `parameter`&ndash; Identifique un parámetro para un método. ej.`/parameter[@name='p0']`

### <a name="adding-types"></a>Agregar tipos

El `add-node` elemento le indicará al proyecto de enlace de Xamarin. Android que agregue una nueva clase contenedora a **API. XML**. Por ejemplo, el siguiente fragmento de código dirigirá el generador de enlace para crear una clase con un constructor y un campo único:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Quitar tipos

Es posible indicar al generador de enlaces de Xamarin. Android que ignore un tipo de Java y que no lo enlace. Esto se hace agregando un `remove-node` elemento XML al archivo **Metadata. XML** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Cambiar el nombre de los miembros

No es posible cambiar el nombre de los miembros editando directamente el archivo **API. XML** porque Xamarin. Android requiere los nombres de la interfaz nativa de Java (JNI) original. Por lo tanto `//class/@name` , no se puede modificar el atributo; si es, el enlace no funcionará.

Considere el caso en el que queremos cambiar el nombre de un `android.Manifest`tipo.
Para lograrlo, es posible que intente editar directamente **API. XML** y cambiar el nombre de la clase de la manera siguiente:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Esto hará que el generador de enlaces cree el código siguiente C# para la clase contenedora:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Observe que se ha cambiado el nombre de la `NewName`clase contenedora a, mientras que el `Manifest`tipo de Java original sigue siendo. Ya no es posible que la clase de enlace de Xamarin. Android tenga acceso a cualquier `android.Manifest`método de; la clase contenedora se enlaza a un tipo de Java inexistente.

Para cambiar correctamente el nombre administrado de un tipo ajustado (o método), es necesario establecer el `managedName` atributo como se muestra en este ejemplo:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Cambiar el `EventArg` nombre de clases contenedoras

Cuando el generador de enlaces de Xamarin. Android `onXXX` identifica un método de establecedor para un tipo C# de agente `EventArgs` de _escucha_, se generará un evento y una subclase para admitir una API de .net aromatizada para el patrón de agente de escucha basado en Java. Como ejemplo, considere la clase y el método de Java siguientes:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin. Android quitará el prefijo `on` del método setter y, en su lugar, usará `2DSignNextManuever` como base para el nombre `EventArgs` de la subclase. La subclase se denominará algo similar a:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

No es un nombre de C# clase válido. Para corregir este problema, el autor del enlace debe usar `argsType` el atributo y proporcionar un C# nombre válido para `EventArgs` la subclase:

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Atributos admitidos

En las secciones siguientes se describen algunos de los atributos para transformar las API de Java.

### <a name="argstype"></a>argsType

Este atributo se coloca en métodos de establecedor para `EventArg` asignar un nombre a la subclase que se generará para admitir agentes de escucha de Java. Esto se describe con más detalle a continuación en la sección [cambiar el nombre de las clases de contenedor de EventArg](#Renaming_EventArg_Wrapper_Classes) más adelante en esta guía.

### <a name="eventname"></a>eventName

Especifica un nombre para un evento. Si está vacío, impide la generación de eventos.
Esto se describe con más detalle en el título sección [cambiar el nombre de las clases contenedoras de EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Se utiliza para cambiar el nombre de un paquete, una clase, un método o un parámetro. Por ejemplo, para cambiar el nombre de la clase `MyClass` Java `NewClassName`a:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

En el ejemplo siguiente se muestra una expresión XPath para cambiar el nombre `java.lang.object.toString` del `Java.Lang.Object.NewManagedName`método a:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType`se utiliza para cambiar el tipo de valor devuelto de un método. En algunas situaciones, el generador de enlaces infiere incorrectamente el tipo de valor devuelto de un método Java, lo que producirá un error en tiempo de compilación. Una posible solución en esta situación es cambiar el tipo de valor devuelto del método.

Por ejemplo, el generador de enlaces considera que el método `de.neom.neoreadersdk.resolution.compareTo()` Java debe `int`devolver, lo que genera el error CS0535 del **mensaje de error: Resguardo. Neom. Neoreadersdk. Resolution ' no implementa el miembro de interfaz ' Java. lang. IComparable. CompareTo (Java. lang.** Object) '. El siguiente fragmento de código muestra cómo cambiar el tipo de parámetro del C# método generado de `DE.Neom.Neoreadersdk.Resolution` un a `Java.Lang.Object`un: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Cambia el tipo de valor devuelto de un método. Esto no cambia el atributo Return (dado que los cambios en los atributos devueltos pueden dar lugar a cambios incompatibles en la firma JNI). En el ejemplo siguiente, el tipo de `append` valor devuelto del método se cambia de `SpannableStringBuilder` a `IAppendable` ( C# Recuerde que no admite los tipos de valor devuelto covariante):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>ofuscados

Las herramientas que ofuscan las bibliotecas de Java pueden interferir con el generador de enlaces de Xamarin. Android C# y su capacidad de generar clases contenedoras. Las características de las clases ofuscadas incluyen: 

- El nombre de clase incluye **$** una clase, es decir, **una $.**
- El nombre de clase está totalmente comprometido por caracteres en minúsculas, es decir, **a.**

Este fragmento de código es un ejemplo de cómo generar un tipo "no ofuscado C# ":

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Este atributo se puede utilizar para cambiar el nombre de una propiedad administrada.

Un caso especializado de uso `propertyName` implica la situación en la que una clase de Java solo tiene un método de captador para un campo. En esta situación, el generador de enlaces desearía crear una propiedad de solo escritura, algo que no se recomienda en .NET. En el fragmento de código siguiente se muestra cómo "quitar" las propiedades `propertyName` de .net estableciendo en una cadena vacía:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Tenga en cuenta que el generador de enlaces todavía creará los métodos Setter y getter.

### <a name="sender"></a>sender

Especifica qué parámetro de un método debe ser el `sender` parámetro cuando el método se asigna a un evento. El valor puede ser `true` o `false`. Por ejemplo:

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilidad

Este atributo se usa para cambiar la visibilidad de una clase, método o propiedad. Por ejemplo, puede que sea necesario promocionar un `protected` método de Java para que sea el C# contenedor `public`correspondiente:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields. XML y Enummethods (. XML

Hay casos en los que las bibliotecas de Android usan constantes de tipo entero para representar los Estados que se pasan a las propiedades o los métodos de las bibliotecas. En muchos casos, resulta útil enlazar estas constantes de tipo entero a las enumeraciones en C#. Para facilitar esta asignación, use los archivos **EnumFields. XML** y **enummethods (. XML** en el proyecto de enlace. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Definir una enumeración mediante EnumFields. XML

El archivo **EnumFields. XML** contiene la asignación entre las `int` constantes de Java C# `enums`y. Vamos a crear el ejemplo siguiente de una C# enumeración que se crea para un `int` conjunto de constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Aquí `SKRealReachSettings` hemos tomado la clase de Java y hemos definido una C# enumeración llamada `SKMeasurementUnit` en el espacio de nombres. `Skobbler.Ngx.Map.RealReach` Las `field` entradas definen el nombre de la constante de Java ( `UNIT_SECOND`ejemplo), el nombre de la entrada de enumeración (ejemplo `Second`) y el valor entero representado por ambas entidades ( `0`ejemplo). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Definir métodos de captador/establecedor mediante Enummethods (. XML

El archivo **enummethods (. XML** permite cambiar los parámetros de método y los tipos `int` de valor devueltos de las constantes de Java a. C# `enums` En otras palabras, asigna la lectura y escritura C# de enumeraciones (definidas en el archivo **EnumFields. XML** ) a los métodos `int` y `get` `set` constantes de Java.

Dada la `SKRealReachSettings` enumeración definida anteriormente, el siguiente archivo **enummethods (. XML** definiría el captador/establecedor para esta enumeración:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La primera `method` línea asigna el valor devuelto del método `getMeasurementUnit` Java a la `SKMeasurementUnit` enumeración. La segunda `method` línea asigna el primer parámetro `setMeasurementUnit` de a la misma enumeración.

Con todos estos cambios en su lugar, puede usar el código siguiente en Xamarin. Android para establecer `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Resumen

En este artículo se describe cómo Xamarin. Android usa metadatos para transformar una definición de API desde el formato de *Google* *AOSP*. Después de cubrir los cambios que son posibles mediante *Metadata. XML*, examinó las limitaciones que se producen al cambiar el nombre de los miembros y se presenta la lista de atributos XML admitidos, que describen Cuándo debe usarse cada atributo.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Metadatos de GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
