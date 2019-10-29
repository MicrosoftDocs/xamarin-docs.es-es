---
title: Matriz de errores de Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: dd05acdf58fbae85d9d8a82e899ebedc4c945281
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019606"
---
# <a name="xamarinandroid-errors-matrix"></a>Matriz de errores de Xamarin. Android

## <a name="errors-reference"></a>Referencia de errores

En este documento se proporciona información sobre los distintos códigos de error de Xamarin.

|Categoría|Descripción|
|--- |--- |
|XA0xxx|Errores de mandroid|
|XA1xxx|Errores de copia de archivos/vínculos simbólicos (relacionados con proyectos)|
|XA2xxx|Errores del vinculador|
|XA3xxx|Errores de AOT|
|XA4xxx|Errores de generación de código|
|XA5xxx|Errores GCC y cadena de herramientas|
|XA6xxx|mandroid errores de herramientas internas|
|XA7xxx|Reservada|
|XA8xxx|Reservada|
|XA9xxx|Errores de licencia|

## <a name="error-codes"></a>Códigos de error

### <a name="xa0xxx-errors"></a>Errores de XA0xxx

|Código de error|Descripción|
|--- |--- |
|XA0000|Error inesperado: rellene un [Informe de errores](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|'-devname se proporcionó sin ninguna acción específica del dispositivo.|
|XA0002|No se pudo analizar la variable de entorno '{0}'.|
|XA0003|El nombre de aplicación '{0}. exe ' está en conflicto con un nombre de ensamblado de SDK o de producto (. dll).|
|XA0004|La nueva lógica de refcounting requiere que el SGen esté habilitado.|
|XA0005|El directorio de salida '{0}' no existe.|
|XA0006|No hay ninguna plataforma desarrollo en '{0}', use--Platform = CHAPAs para especificar el SDK|
|XA0007|El ensamblado raíz '{0}' no existe.|
|XA0008|Solo debe proporcionar un ensamblado raíz.|
|XA0009|Error al cargar los ensamblados: {0}.|
|XA0010|No se pudieron analizar los argumentos de la línea de comandos: {0}.|
|XA0011|{0} se creó con un tiempo de ejecución más reciente ({1}) de lo que admite MonoTouch.|
|XA0012|Se proporcionan datos incompletos para completar '{0}'.|
|XA0013|La compatibilidad con la generación de perfiles requiere que se habilite también Sgen.|
|XA0014|iOS {0} no admite la creación de aplicaciones dirigidas a ARMv6.|
|XA0020|No se pudo determinar la ruta de acceso de mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' no es válido en el proyecto de aplicación de Android. En su lugar, use AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Errores de XA1xxx

|Código de error|Descripción|
|--- |--- |
|XA1001|No se encontró ninguna aplicación en el directorio especificado.|
|XA1002|No se pudieron crear los vínculos simbólicos, se copiaron los archivos.|
|XA1003|No se pudo eliminar la aplicación '{0}'. Es posible que tenga que eliminar la aplicación manualmente.|
|XA1004|No se pudo obtener la lista de aplicaciones instaladas.|
|XA1005|No se pudo eliminar la aplicación '{0}' en el dispositivo '{1}': {2}. Es posible que tenga que eliminar la aplicación manualmente.|
|XA1006|No se pudo instalar la aplicación '{0}' en el dispositivo '{1}': {2}.|
|XA1007|No se pudo iniciar la aplicación '{0}' en el dispositivo '{1}': {2}. Puede seguir iniciando la aplicación manualmente punteando en ella.|
|XA1008|No se pudo iniciar el simulador: {0}.|
|XA1009|No se pudo copiar el ensamblado '{0}' en '{1}': {2}.|
|XA1010|No se pudo cargar el ensamblado '{0}': {1}.|
|XA1011|No se pudo agregar el archivo de recursos que faltaba: '{0}'.|
|XA1101|No se pudo iniciar la aplicación.|
|XA1102|No se pudo adjuntar a la aplicación (para eliminarla): {0}.|
|XA1103|No se pudo desasociar.|
|XA1104|No se pudo enviar el paquete: {0}.|
|XA1105|Tipo de respuesta inesperado.|
|XA1106|No se pudo obtener la lista de aplicaciones en el dispositivo: se agotó el tiempo de espera de la solicitud.|
|XA1107|No se pudo iniciar la aplicación.|
|XA1201|No se pudo cargar el simulador: {0}.|
|XA1301|Se omitió la biblioteca nativa '{0}' ({1}) porque no coincide con las arquitecturas de compilación actuales ({2}).|

### <a name="xa2xxx-errors"></a>Errores de XA2xxx

|Código de error|Descripción|
|--- |--- |
|XA2001|No se pudieron vincular los ensamblados.|
|XA2002|No se puede resolver la referencia: {0}.|
|XA2003|Se omitirá la opción '{0}' porque la vinculación está deshabilitada.|
|XA2004|No se pudo encontrar el archivo de definiciones del vinculador adicional '{0}'.|
|XA2005|No se pudieron analizar las definiciones de '{0}'.|
|XA2006|No se pudo resolver la referencia al elemento de metadatos '{0}' (definido en '{1}') desde '{2}'.|

### <a name="xa3xxx-errors"></a>Errores de XA3xxx

Estos son errores de AOT.

|Código de error|Descripción|
|--- |--- |
|XA3001|No se pudo el AOT en el ensamblado '{0}'.|
|XA3002|Restricción de AOT: el método '{0}' debe ser estático, ya que se decora con [MonoPInvokeCallback].|
|XA3003|En conflicto: opciones debug y--LLVM. La depuración de software está deshabilitada.|

### <a name="xa4xxx-errors"></a>Errores de XA4xxx

Se trata de errores de generación de código.

|Código de error|Descripción|
|--- |--- |
|XA4001|La plantilla principal no se pudo exrotar a '{0}'.|
|XA4101|El registrador no puede crear una firma para el tipo '{0}'.|
|XA4102|El registrador encontró un tipo no válido '{0}' en la firma para el método '{2}'. En su lugar, use '{1}'.|
|XA4103|El registrador encontró un tipo no válido '{0}' en la firma para el método '{2}': el tipo implementa INativeObject, pero no tiene un constructor que toma dos argumentos (IntPtr, bool).|
|XA4104|El registrador no puede calcular las referencias del valor devuelto para el tipo '{0}' en la Signatura del método '{1}'.|
|XA4105|El registrador no puede serializar el parámetro de tipo '{0}' en la Signatura del método '{1}'.|
|XA4106|El registrador no puede calcular las referencias del valor devuelto para la estructura '{0}' en la Signatura del método '{1}'.|
|XA4107|El registrador no puede serializar el parámetro de tipo '{0}' en la Signatura del método '{1}'.|
|XA4108|El registrador no puede obtener el tipo ObjectiveC para el tipo administrado '{0}'.|
|XA4109|No se pudo compilar el código de registrador generado. Registre un [Informe de errores](https://bugzilla.xamarin.com).|
|XA4110|El registrador no puede calcular las referencias del parámetro de salida de tipo '{0}' en la Signatura del método '{1}'.|
|XA4111|El registrador no puede crear una signatura para el tipo '{0}' en el método '{1}'.|
|XA4200|Solo puede generar ACW para los tipos ' Claas '.|
|XA4201|No se puede determinar el nombre de JNI para el tipo {0}.|
|XA4203|El nombre de tipo especificado debe ser completo.|
|XA4204|No se puede resolver el tipo de interfaz '{0}'. ¿Falta una referencia de ensamblado?|
|XA4205|[ExportField] solo se puede usar en métodos con 0 parámetros.|
|XA4206|[Export] no se puede usar en un tipo genérico.|
|XA4207|[ExportField] no se puede usar en un tipo genérico.|
|XA4208|[Java. Interop. ExportFieldAttribute] no se puede usar en un método que devuelva void.|
|XA4209|No se pudo crear JavaTypeInfo para la clase: {0} debido a {1}.|
|XA4210|Debe agregar una referencia a mono. Android. Export. dll cuando use ExportAttribute o ExportFieldAttribute.|
|XA4211|Archivo AndroidManifest. XML //uses-sdk/@android:targetSdkVersion '{0}' es menor que $ (TargetFrameworkVersion) '{1}'. Usar API-{1} para la compilación de ACW.|

### <a name="xa5xxx-errors"></a>Errores de XA5xxx

|Código de error|Descripción|
|--- |--- |
|XA5101|Falta el compilador '{0}'. Instale el NDK de Android.|
|XA5102|Error de conversión del ensamblado al código nativo. Registre un [Informe de errores](https://bugzilla.xamarin.com).|
|XA5103|No se pudo compilar el archivo '{0}'. Registre un [Informe de errores](https://bugzilla.xamarin.com).|
|XA5201|Error de vinculación nativa. Revise las marcas de usuario proporcionadas a GCC: {0}|
|XA5202|Error de vinculación nativa. Revise el registro de compilación.|
|XA5303|ADVERTENCIA de vinculación nativa: {0}.|
|XA5300|Android SDK no se encuentra o no está completamente instalado.|
|XA5301|Falta la herramienta ' Strip '. Instale el componente de las herramientas de línea de comandos de Xcode.|
|XA5302|Falta la herramienta ' dsymutil '. Instale el componente de las herramientas de línea de comandos de Xcode.|
|XA5203|No se pudieron generar los símbolos de depuración (directorio dSYM). Revise el registro de compilación.|
|XA5204|No se pudo quitar el archivo binario final. Revise el registro de compilación.|
|XA5205|Falta la herramienta ' AAPT '. Instale el paquete de herramientas de compilación de Android SDK.|
|XA5206|{0}Operador No existe el directorio de recursos de Android {1}.|
|XA5207|{0}Operador {1} de archivo de biblioteca Java no existe.|
|XA5208|Error en la descarga. Descargue {0} y colóquelo en el directorio {1}.|
|XA5209|Error al descomprimir. Descargue {0} y extráigalo en el directorio {1}.|
|XA5210|{0}Operador No existe el archivo de biblioteca nativo {1}.|

### <a name="xa6xxx-errors"></a>Errores de XA6xxx

|Código de error|Descripción|
|--- |--- |
|XA6001|La versión de ejecución de Cecil no admite la eliminación de ensamblados.|
|XA6002|No se puede quitar el ensamblado '{0}'.|
|XA6003|Mensaje UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Errores de XA9xxx

Estos códigos de error son los errores de activación y licencia.

#### <a name="xa9000"></a>XA9000

 **Causa:** Licencia expirada

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9001"></a>XA9001

 **Causa:** Evaluación expirada

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Causa:** AndroidJavaLibrary

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

 **Si un ensamblado de enlace tiene incrustado. jar, se detecta en tiempo de paquete, no en tiempo de compilación.**

 **Causa:** AndroidNativeLibrary

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

#### <a name="xa9003"></a>XA9003

 **Causa:** System. Runtime. Serialization

 **Comprobada durante:** Configura

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Causa:** System. ServiceModel. Web

 **Comprobada durante:** Configura

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Causa:** Mono. Data. TDS

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

 **Se hace referencia a esto mediante System. Data. dll, que se permite**

 **Causa:** Mono. Android. Export

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|Aceptar|Aceptar|Aceptar|

#### <a name="xa9004"></a>XA9004

 **Causa:** --generación de perfiles

 **Comprobada durante:** Configura

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

#### <a name="xa9005"></a>XA9005

 **Causa:** Límite de tamaño (32 KB).

 **Comprobada durante:** Configura

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|Aceptar|-|-|-|

#### <a name="xa9006"></a>XA9006

 **Causa:** Espacio de nombres System. Data. SqlClient.

 **Comprobada durante:** Configura

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

#### <a name="xa9008"></a>XA9008

 **Causa:** Compilar desde la línea de comandos.

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|Aceptar|Aceptar|Aceptar|

#### <a name="xa9009"></a>XA9009

 **Causa:** Falta el número de serie.

 **Comprobada durante:** No se pudo probar

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId no válido.

 **Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.

#### <a name="xa9011"></a>XA9011

 **Causa:** No se pudo actualizar el archivo de licencia (al nuevo formato de archivo).

 **Comprobada durante:** Activación

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** Sin Internet

 **Comprobada durante:** Activación

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9013"></a>XA9013

 **Causa:** Error desconocido

 **Comprobada durante:** Activación

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9014"></a>XA9014

 **Causa:** Código de activación no válido

 **Comprobada durante:** Activación

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9017"></a>XA9017

 **Causa:** El servidor de activación no devuelve una licencia válida.

 **Comprobada durante:** Activación

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9018"></a>XA9018

**Causa:** Licencia no válida

**Comprobada durante:** Versión

|Inicio|Indie|Empresa (evaluación)|Trabajo|Empresa|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|
