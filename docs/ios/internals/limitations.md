---
title: Limitaciones de Xamarin. iOS
description: En este documento se describen las limitaciones de Xamarin. iOS, la explicación de los genéricos, las subclases genéricas de NSObjects, P/Invoke en objetos genéricos y mucho más.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/09/2018
ms.openlocfilehash: 91513936a0223af0e4220154d0fe65ee0a599a4f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305868"
---
# <a name="limitations-of-xamarinios"></a>Limitaciones de Xamarin. iOS

Puesto que las aplicaciones que usan Xamarin. iOS se compilan en código estático, no es posible usar ninguna funcionalidad que requiera la generación de código en tiempo de ejecución.

Estas son las limitaciones de Xamarin. iOS en comparación con el escritorio mono:

 <a name="Limited_Generics_Support" />

## <a name="limited-generics-support"></a>Compatibilidad con genéricos limitados

A diferencia de mono/. NET tradicional, el código en el iPhone se compila estáticamente de antemano en lugar de compilarse a petición mediante un compilador JIT.

La tecnología de [AOT completa](https://www.mono-project.com/docs/advanced/aot/#full-aot) de mono tiene algunas limitaciones con respecto a los genéricos, ya que no todas las creaciones de instancias genéricas posibles se pueden determinar por adelantado en tiempo de compilación. Esto no es un problema para los tiempos de ejecución de .NET o mono normales, ya que el código siempre se compila en tiempo de ejecución mediante el compilador Just-in-Time. Pero esto supone un reto para un compilador estático como Xamarin. iOS.

Algunos de los problemas comunes en los que se ejecutan los desarrolladores son:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />

### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Las subclases genéricas de NSObjects están limitadas

Xamarin. iOS tiene actualmente compatibilidad limitada para la creación de subclases genéricas de la clase NSObject, como no se admiten métodos genéricos. A partir de 7.2.1, es posible usar subclases genéricas de NSObjects, como esta:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Aunque son posibles las subclases genéricas de NSObjects, hay algunas limitaciones. Lea las [subclases genéricas del documento NSObject](~/ios/internals/api-design/nsobject-generics.md) para obtener más información.

 <a name="No_Dynamic_Code_Generation" />

## <a name="no-dynamic-code-generation"></a>No hay generación de código dinámico

Dado que el kernel de iOS impide que una aplicación genere código de forma dinámica, Xamarin. iOS no admite ningún tipo de generación de código dinámico. Entre ellas se incluyen las siguientes:

- System. Reflection. Emit no está disponible.
- No se admite System. Runtime. Remoting.
- No se admite la creación dinámica de tipos (sin Type. GetType ("mi tipo ' 1")), aunque la búsqueda de tipos existentes (Type. GetType ("System. String"), por ejemplo, funciona correctamente).
- Las devoluciones de llamada inversas se deben registrar con el Runtime en tiempo de compilación.

 <a name="System.Reflection.Emit" />

### <a name="systemreflectionemit"></a>System.Reflection.Emit

Falta de System. Reflection. **Emit** significa que no funcionará ningún código que dependa de la generación de código en tiempo de ejecución. Esto incluye elementos como:

- Dynamic Language Runtime.
- Cualquier lenguaje basado en Dynamic Language Runtime.
- TransparentProxy de la comunicación remota o cualquier otro elemento que haría que el Runtime generara código dinámicamente.

  > [!IMPORTANT]
  > No confunda **Reflection. Emit** con **reflexión**. Reflection. Emit consiste en generar código dinámicamente y hacer que ese código se JITed y compile en código nativo. Debido a las limitaciones de iOS (sin compilación JIT) esto no se admite.

Sin embargo, toda la API de reflexión, incluido Type. GetType ("someClass"), los métodos de enumeración, las propiedades de lista, la captura de atributos y valores funciona bien.

### <a name="using-delegates-to-call-native-functions"></a>Usar delegados para llamar a funciones nativas

Para llamar a una función nativa a C# través de un delegado, la declaración del delegado se debe decorar con uno de los siguientes atributos:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (preferido, ya que es multiplataforma y compatible con .net Standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Si no se proporciona uno de estos atributos, se producirá un error de tiempo de ejecución como:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```

 <a name="Reverse_Callbacks" />

### <a name="reverse-callbacks"></a>Devoluciones de llamada inversas

En mono estándar es posible pasar C# instancias de delegado a código no administrado en lugar de un puntero a función. Normalmente, el tiempo de ejecución transformaría esos punteros de función en un código thunk pequeño que permite que el código no administrado vuelva a llamar a código administrado.

En mono, estos puentes se implementan mediante el compilador Just-in-Time. Al usar el compilador de antemano que requiere el iPhone, hay dos limitaciones importantes en este momento:

- Debe marcar todos los métodos de devolución de llamada con [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
- Los métodos deben ser métodos estáticos, no se admiten los métodos de instancia.

<a name="No_Remoting" />

## <a name="no-remoting"></a>Sin comunicación remota

La pila de comunicación remota no está disponible en Xamarin. iOS.

 <a name="Runtime_Disabled_Features" />

## <a name="runtime-disabled-features"></a>Características deshabilitadas en tiempo de ejecución

Se han deshabilitado las siguientes características en el tiempo de ejecución de iOS de mono:

- Generador de perfiles
- Reflection. Emit
- Funcionalidad de Reflection. Emit. Save
- Enlaces COM
- Motor JIT
- Comprobador de metadatos (ya que no hay JIT)

 <a name=".NET_API_Limitations" />

## <a name="net-api-limitations"></a>Limitaciones de la API de .NET

La API de .NET expuesta es un subconjunto del marco completo, ya que no todo está disponible en iOS. Vea las preguntas más frecuentes para obtener una [lista de los ensamblados admitidos actualmente](~/cross-platform/internals/available-assemblies.md).

En concreto, el perfil de API que usa Xamarin. iOS no incluye System. Configuration, por lo que no es posible usar archivos XML externos para configurar el comportamiento del tiempo de ejecución.
