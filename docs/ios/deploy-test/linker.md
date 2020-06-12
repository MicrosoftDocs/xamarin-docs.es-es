---
title: Vincular aplicaciones Xamarin.iOS
description: En este documento se describe el vinculador de Xamarin.iOS, que se usa para eliminar código no usado de una aplicación Xamarin.iOS a fin de reducir su tamaño.
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: 263c48c5006ba0060756e1050497c38dfb7c8eae
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567780"
---
# <a name="linking-xamarinios-apps"></a>Vincular aplicaciones Xamarin.iOS

Al compilar la aplicación, Visual Studio para Mac o Visual Studio llaman a una herramienta denominada **mtouch** que incluye un enlazador para código administrado. Se utiliza para quitar de las bibliotecas de clases las características que la aplicación no usa. El objetivo es reducir el tamaño de la aplicación, que se proporciona solo con los bits necesarios.

El enlazador usa análisis estáticos para determinar las diferentes rutas de código que la aplicación es susceptible de seguir. Es algo pesado porque tiene que pasar por todos los detalles de cada ensamblado, para asegurarse de que no se haya quitado nada reconocible. No está habilitado de forma predeterminada en las compilaciones del simulador para acelerar el tiempo de compilación durante la depuración. Sin embargo, debido a que genera aplicaciones más pequeñas, puede acelerar la compilación AOT y la carga en el dispositivo; además, todas las *compilaciones de dispositivos (versión)* usan el enlazador de forma predeterminada.

Como el enlazador es una herramienta estática, no puede marcar los tipos de inclusión y los métodos llamados mediante reflexión ni crear instancias de forma dinámica. Existen varias opciones para encontrar una solución alternativa a esta limitación.

<a name="Linker_Behavior"></a>

## <a name="linker-behavior"></a>Comportamiento del enlazador

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Se puede personalizar el proceso de vinculación mediante la lista desplegable del comportamiento del enlazador en **Opciones del proyecto**. Para acceder a este menú, haga doble clic en el proyecto de iOS y vaya a **Compilación de iOS > Opciones del enlazador**, como se ilustra a continuación:

[![](linker-images/image1.png "Linker Options")](linker-images/image1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Se puede personalizar el proceso de vinculación mediante la lista desplegable del comportamiento del enlazador en **Propiedades del proyecto** en Visual Studio.

Haga lo siguiente:

1. Haga clic con el botón derecho en el **nombre del proyecto** en el **Explorador de soluciones** y seleccione **Propiedades**:

    ![](linker-images/linking01w.png "Right-click on the Project Name in the Solution Explorer and select Properties")
2. En **Propiedades del proyecto**, seleccione **Compilación de iOS**:

    ![](linker-images/linking02w.png "Select IOS Build")
3. Siga las instrucciones siguientes para cambiar las opciones de vinculación.

-----

Se ofrecen las tres opciones principales descritas a continuación:

### <a name="dont-link"></a>No vincular

Deshabilitar la vinculación garantizará que no se realice ninguna modificación de los ensamblados. Por motivos de rendimiento, esta es la configuración predeterminada si tiene como destino el IDE para el simulador de iOS. Para las compilaciones de dispositivos, esto solo debe usarse como una solución alternativa a este problema siempre que el enlazador presente un error que impida que la aplicación se ejecute. Si la aplicación solo funciona con *-nolink*, envíe un [informe de errores](https://github.com/xamarin/xamarin-macios/issues/new).

Esto se corresponde con la opción *-nolink* cuando se usa la herramienta de línea de comandos mtouch.

<a name="Link_SDK_assemblies_only"></a>

### <a name="link-sdk-assemblies-only"></a>Vincular solo ensamblados de SDK

En este modo, el enlazador dejará intactos los ensamblados y reducirá el tamaño de los ensamblados del SDK (es decir, los que se incluyen con Xamarin.iOS) mediante la eliminación de todo lo que la aplicación no usa. Se trata de la configuración predeterminada cuando el IDE tiene como destino dispositivos iOS.

Esta es la opción más sencilla, ya que no requiere ningún cambio en el código. La diferencia con la vinculación de todo es que el enlazador no puede realizar algunas optimizaciones en este modo, por lo que es un equilibrio entre el trabajo necesario para vincular todo el contenido y el tamaño de la aplicación final.

Esto se corresponde con la opción *-linksdk* cuando se usa la herramienta de línea de comandos mtouch.

<a name="Link_all_assemblies"></a>

### <a name="link-all-assemblies"></a>Vincular todos los ensamblados

Al vincular todo el contenido, el enlazador puede usar el conjunto completo de sus optimizaciones para hacer que la aplicación sea lo más pequeña posible. Modificará el código de usuario, que se puede interrumpir cada vez que el código utiliza las características de una manera que el análisis estático del enlazador no puede detectar. En tales casos, por ejemplo, con los servicios web, la reflexión o la serialización, puede que sea necesario realizar algunos ajustes en la aplicación para vincular todo.

Esto se corresponde con la opción *-linkall* cuando se usa la herramienta de línea de comandos **mtouch**.

<a name="Controlling_the_Linker"></a>

## <a name="controlling-the-linker"></a>Controlar la enlazador

Cuando se usa el enlazador, en ocasiones, se eliminará código al que puede haber llamado de forma dinámica, incluso indirectamente. Para esos casos, el enlazador ofrece algunas características y opciones que permiten tener un mayor control de sus acciones.

<a name="Preserving_Code"></a>

### <a name="preserving-code"></a>Conservar el código

Cuando usa el enlazador, en ocasiones, se puede eliminar código al que puede haber llamado de forma dinámica mediante System.Reflection.MemberInfo.Invoke o con la exportación de los métodos a Objective-C mediante el atributo `[Export]` y después invocar el selector manualmente.

En esos casos, puede indicar al enlazador que considere la posibilidad de usar las clases completas o de conservar miembros individuales con la aplicación del atributo `[Xamarin.iOS.Foundation.Preserve]` a nivel de clase o de miembro. Todos los miembros no vinculados de forma estática por la aplicación podrían eliminarse. Por tanto, este atributo se usa para marcar los miembros a los que no se hace referencia de forma estática, pero que siguen siendo necesarios para la aplicación.

Por ejemplo, si crea instancias de tipos de forma dinámica, puede que desee conservar el constructor predeterminado de sus tipos. Si usa la serialización XML, puede que desee conservar las propiedades de los tipos.

Este atributo se puede aplicar en todos los miembros de un tipo o en el propio tipo. Si desea conservar todo el tipo, puede usar la sintaxis `[Preserve
(AllMembers = true)]` en dicho tipo.

A veces se desean conservar determinados tipos, pero solo si se mantiene el tipo contenedor. En esos casos, use `[Preserve (Conditional=true)]`

Si no desea recurrir a una dependencia en las bibliotecas de Xamarin, por ejemplo, como en el supuesto caso de que compile una biblioteca de clases portátil multiplataforma, también puede usar este atributo.

Para ello, solo debe declarar una clase PreserveAttribute y usarla en el código, como se indica a continuación:

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

No importa realmente en qué espacio de nombres se defina, ya que el enlazador busca este atributo por el nombre de tipo.

 <a name="Skipping_Assemblies"></a>

### <a name="skipping-assemblies"></a>Omitir ensamblados

Es posible especificar ensamblados que se deben excluir del proceso del enlazador, al mismo tiempo que se permite que otros ensamblados se vinculen de la forma habitual. Esto resulta útil si no es posible usar `[Preserve]` en algunos ensamblados (por ejemplo, código de terceros) o como una solución alternativa temporal para un error.

Esto se corresponde con la opción `--linkskip` cuando se usa la herramienta de línea de comandos mtouch.

Si usa la opción **Vincular todos los ensamblados** y desea indicar al enlazador que omita ensamblados completos, escriba lo siguiente en las opciones **Argumentos mtouch adicionales** del ensamblado de nivel superior:

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

Si desea que el enlazador omita varios ensamblados, incluya varios argumentos `linkskip`:

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

No hay interfaz de usuario para usar esta opción, pero se puede ofrecer en el cuadro de diálogo Opciones del proyecto de Visual Studio para Mac o en el panel Propiedades del proyecto de Visual Studio, en el campo de texto **Argumentos mtouch adicionales**. (P. ej., *--linkskip=mscorlib* no vincularía mscorlib.dll, pero sí los ensamblados de la solución).

<a name="Disabling_Link_Away"></a>

### <a name="disabling-link-away"></a>Deshabilitar "Link Away"

El enlazador quitará el código que es poco probable que se use en los dispositivos, como, por ejemplo, el que es incompatible o el que está deshabilitado. Son raras las ocasiones en que es posible que una aplicación o una biblioteca dependa de este código (independientemente de que funcione o no). A partir de Xamarin.iOS 5.0.1, se puede indicar al enlazador que omita esta optimización.

Esto se corresponde con la opción *-nolinkaway* cuando se usa la herramienta de línea de comandos mtouch.

No hay interfaz de usuario para usar esta opción, pero se puede ofrecer en el cuadro de diálogo Opciones del proyecto de Visual Studio para Mac o en el panel Propiedades del proyecto de Visual Studio, en el campo de texto **Argumentos mtouch adicionales**. (P. ej., *--nolinkaway* no eliminaría el código adicional, que son unos 100 kb).

### <a name="marking-your-assembly-as-linker-ready"></a>Marcar el ensamblado como listo para el enlazador

Los usuarios pueden seleccionar entre vincular solo los ensamblados del SDK y no realizar ninguna vinculación con el código.  Esto también significa que las bibliotecas de terceros que no forman parte del SDK principal de Xamarin tampoco se vincularán.

Esto suele ocurrir porque no desean agregar atributos `[Preserve]` manualmente a su código.  El inconveniente es que no se vincularán las bibliotecas de terceros y, en general, se trata de una buena opción predeterminada, ya que no es posible saber si una biblioteca de terceros es apta o no para el enlazador.

Si dispone de una biblioteca en el proyecto o es un desarrollador de bibliotecas reutilizables y desea que el enlazador trate el ensamblado como vinculable, lo único que tiene que hacer es agregar el atributo [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute) a nivel de ensamblado, como se indica a continuación:

```csharp
[assembly:LinkerSafe]
```

Para ello, no es necesario realmente que la biblioteca haga referencia a las bibliotecas de Xamarin.  Por ejemplo, si va a compilar una biblioteca de clases portátil que se ejecutará en otras plataformas, también puede usar el atributo `LinkerSafe`.
El enlazador de Xamarin busca el atributo `LinkerSafe` por el nombre, no por su tipo real.  Esto significa que puede escribir este código y también funcionará:

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>Configuración personalizada del enlazador

Siga las [instrucciones para crear un archivo de configuración del enlazador](~/cross-platform/deploy-test/linker.md).

## <a name="related-links"></a>Vínculos relacionados

- [Configuración personalizada del enlazador](~/cross-platform/deploy-test/linker.md)
- [Vincular en Mac](~/mac/deploy-test/linker.md)
- [Vincular en Android](~/android/deploy-test/linker.md)
