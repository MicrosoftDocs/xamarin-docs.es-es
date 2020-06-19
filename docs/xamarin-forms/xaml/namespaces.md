---
title: Espacios de nombres XAML enXamarin.Forms
description: XAML usa el atributo XML xmlns para las declaraciones de espacio de nombres. En este artículo se presenta la sintaxis del espacio de nombres XAML y se muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f35342134767ccdadfab086bfa14f6b610b325d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130382"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Espacios de nombres XAML enXamarin.Forms

_XAML usa el atributo XML xmlns para las declaraciones de espacio de nombres. En este artículo se presenta la sintaxis del espacio de nombres XAML y se muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo._

## <a name="overview"></a>Información general

Hay dos declaraciones de espacios de nombres XAML que siempre están dentro del elemento raíz de un archivo XAML. El primero define el espacio de nombres predeterminado, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
xmlns="http://xamarin.com/schemas/2014/forms"
```

El espacio de nombres predeterminado especifica que los elementos definidos dentro del archivo XAML sin prefijo hagan referencia a Xamarin.Forms clases como, por ejemplo, [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

La segunda declaración de espacio de nombres usa el `x` prefijo, como se muestra en el siguiente ejemplo de código XAML:

```xaml
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa prefijos para declarar espacios de nombres no predeterminados, con el prefijo que se usa al hacer referencia a los tipos del espacio de nombres. La `x` declaración de espacio de nombres especifica que los elementos definidos dentro de XAML con un prefijo de `x` se usan para los elementos y atributos que son intrínsecos a XAML (concretamente, la especificación XAML 2009).

En la tabla siguiente se describen los `x` atributos de espacio de nombres admitidos por Xamarin.Forms :

|Construcción|Descripción|
|--- |--- |
|`x:Arguments`|Especifica los argumentos de constructor para un constructor no predeterminado o para una declaración de objeto Factory Method.|
|`x:Class`|Especifica el espacio de nombres y el nombre de clase de una clase definida en XAML. El nombre de clase debe coincidir con el nombre de clase del archivo de código subyacente. Tenga en cuenta que esta construcción solo puede aparecer en el elemento raíz de un archivo XAML.|
|`x:DataType`|Especifica el tipo del objeto al que se enlazará el elemento XAML y sus elementos secundarios.|
|`x:FactoryMethod`|Especifica un Factory Method que se puede utilizar para inicializar un objeto.|
|`x:FieldModifier`|Especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.|
|`x:Key`|Especifica una clave única definida por el usuario para cada recurso de un `ResourceDictionary` . El valor de la clave se usa para recuperar el recurso XAML y se utiliza normalmente como argumento para la `StaticResource` extensión de marcado.|
|`x:Name`|Especifica un nombre de objeto en tiempo de ejecución para el elemento XAML. `x:Name`La configuración es similar a la declaración de una variable en el código.|
|`x:TypeArguments`|Especifica los argumentos de tipo genérico para el constructor de un tipo genérico.|

Para obtener más información sobre el `x:DataType` atributo, vea [enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Para obtener más información sobre el `x:FieldModifier` atributo, vea [modificadores de campo](~/xamarin-forms/xaml/field-modifiers.md). Para obtener más información sobre `x:Arguments` los `x:FactoryMethod` atributos y, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md). Para obtener más información sobre el `x:TypeArguments` atributo, vea [genéricos en XAML Xamarin.Forms con ](generics.md).

> [!NOTE]
> Además de los atributos de espacio de nombres enumerados anteriormente, Xamarin.Forms también incluye extensiones de marcado que se pueden consumir a través del `x` Prefijo de espacio de nombres. Para obtener más información, consulte [consumo de extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

En XAML, las declaraciones de espacio de nombres heredan del elemento primario al elemento secundario. Por lo tanto, al definir un espacio de nombres en el elemento raíz de un archivo XAML, todos los elementos de ese archivo heredan la declaración de espacio de nombres.

## <a name="declaring-namespaces-for-types"></a>Declarar espacios de nombres para tipos

Se puede hacer referencia a los tipos en XAML declarando un espacio de nombres XAML con un prefijo, con la declaración de espacio de nombres especificando el nombre del espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Esto se logra definiendo los valores de las siguientes palabras clave dentro de la declaración del espacio de nombres:

- **CLR-namespace:** o **Using:** : el espacio de nombres CLR declarado en el ensamblado que contiene los tipos que se van a exponer como elementos XAML. Esta palabra clave es obligatoria.
- **Assembly =** : el ensamblado que contiene el espacio de nombres CLR al que se hace referencia. Este valor es el nombre del ensamblado, sin la extensión de archivo. La ruta de acceso al ensamblado debe establecerse como una referencia en el archivo de proyecto que contiene el archivo XAML que hará referencia al ensamblado. Esta palabra clave se puede omitir si el valor del **espacio de nombres CLR** está dentro del mismo ensamblado que el código de aplicación que hace referencia a los tipos.

Tenga en cuenta que el carácter que separa el `clr-namespace` `using` token o de su valor es un signo de dos puntos, mientras que el carácter que separa el `assembly` token de su valor es un signo igual. El carácter que se va a usar entre los dos tokens es un punto y coma.

En el ejemplo de código siguiente se muestra una declaración de espacio de nombres XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

Como alternativa, puede escribirse como:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

El `local` prefijo es una Convención que se usa para indicar que los tipos del espacio de nombres son locales para la aplicación. Como alternativa, si los tipos están en un ensamblado diferente, el nombre de ensamblado también debe definirse en la declaración de espacio de nombres, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

A continuación, se especifica el prefijo de espacio de nombres al declarar una instancia de un tipo a partir de un espacio de nombres importado, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Para obtener información sobre cómo definir un esquema de espacio de nombres personalizado, vea [esquemas de espacios de nombres personalizados XAML](custom-namespace-schemas.md).

## <a name="summary"></a>Resumen

En este artículo se ha introducido la sintaxis del espacio de nombres XAML y se ha mostrado cómo declarar un espacio de nombres XAML para tener acceso a un tipo. XAML usa el `xmlns` atributo XML para las declaraciones de espacio de nombres y se puede hacer referencia a los tipos en XAML declarando un espacio de nombres XAML con un prefijo.

## <a name="related-links"></a>Vínculos relacionados

- [Paso de argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Genéricos en XAML conXamarin.Forms](generics.md)
