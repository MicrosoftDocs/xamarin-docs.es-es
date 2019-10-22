---
title: Esquemas de espacios de nombres personalizados XAML en Xamarin. Forms
description: Un esquema de espacio de nombres personalizado XAML se puede definir con la clase XmlnsDefinitionAttribute, que especifica una asignación entre una dirección URL personalizada y uno o varios espacios de nombres CLR. El esquema de espacio de nombres personalizado se puede usar en las declaraciones de espacio de nombres XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: d76b5eefcaf0edeb12f128c60e9b8fffff8bcf3b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68644705"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Esquemas de espacios de nombres personalizados XAML en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

Se puede hacer referencia a los tipos de una biblioteca en XAML declarando un espacio de nombres XAML para la biblioteca, con la declaración de espacio de nombres que especifica el nombre del espacio de nombres de Common Language Runtime (CLR) y un nombre de ensamblado:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

Sin embargo, si se especifica un espacio de nombres CLR y un nombre de ensamblado en una definición de `xmlns` puede resultar difícil y propenso a errores. Además, es posible que se requieran varias declaraciones de espacios de nombres XAML si la biblioteca contiene tipos en varios espacios de nombres.

Un enfoque alternativo es definir un esquema de espacio de nombres personalizado, como `http://mycompany.com/schemas/controls`, que se asigna a uno o varios espacios de nombres CLR. Esto permite que una única declaración de espacio de nombres XAML haga referencia a todos los tipos de un ensamblado, aunque estén en diferentes espacios de nombres. También habilita una única declaración de espacio de nombres XAML para hacer referencia a los tipos de varios ensamblados.

Para obtener más información sobre los espacios de nombres XAML, vea [espacios de nombres XAML en Xamarin. Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definir un esquema de espacio de nombres personalizado

La aplicación de ejemplo contiene una biblioteca que expone algunos controles simples, como `CircleButton`:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Todos los controles de la biblioteca residen en el espacio de nombres `MyCompany.Controls`. Estos controles se pueden exponer a un ensamblado de llamada a través de un esquema de espacio de nombres personalizado.

Un esquema de espacio de nombres personalizado se define con la clase `XmlnsDefinitionAttribute`, que especifica la asignación entre un espacio de nombres XAML y uno o varios espacios de nombres CLR. El `XmlnsDefinitionAttribute` toma dos argumentos: el nombre del espacio de nombres XAML y el nombre del espacio de nombres CLR. El nombre del espacio de nombres XAML se almacena en la propiedad `XmlnsDefinitionAttribute.XmlNamespace` y el nombre del espacio de nombres CLR se almacena en la propiedad `XmlnsDefinitionAttribute.ClrNamespace`.

> [!NOTE]
> La clase `XmlnsDefinitionAttribute` también tiene una propiedad denominada `AssemblyName`, que se puede establecer opcionalmente en el nombre del ensamblado. Esto solo es necesario cuando un espacio de nombres CLR al que se hace referencia desde un `XmlnsDefinitionAttribute` está en un ensamblado externo.

El `XmlnsDefinitionAttribute` debe definirse en el nivel de ensamblado del proyecto que contiene los espacios de nombres CLR que se asignarán en el esquema de espacio de nombres personalizado. En el ejemplo siguiente se muestra el archivo **AssemblyInfo.CS** de la aplicación de ejemplo:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Este código crea un esquema de espacio de nombres personalizado que asigna el `http://mycompany.com/schemas/controls` dirección URL al espacio de nombres CLR `MyCompany.Controls`. Además, el atributo `Preserve` se especifica en el ensamblado para asegurarse de que el vinculador conserva todos los tipos del ensamblado.

> [!IMPORTANT]
> El atributo `Preserve` se debe aplicar a las clases del ensamblado que se asignan a través del esquema del espacio de nombres personalizado o se aplican a todo el ensamblado.

El esquema de espacio de nombres personalizado se puede usar para la resolución de tipos en los archivos XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Consumir un esquema de espacio de nombres personalizado

Para consumir tipos del esquema de espacio de nombres personalizado, el compilador XAML requiere que haya una referencia de código del ensamblado que consuma los tipos, al ensamblado que define los tipos. Esto puede lograrse agregando una clase que contenga un método `Init` al ensamblado que define los tipos que se consumirán a través de XAML:

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

A continuación, se puede llamar al método `Init` desde el ensamblado que consume tipos del esquema de espacio de nombres personalizado:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Si no se incluye este tipo de referencia de código, el compilador XAML no podrá encontrar el ensamblado que contiene los tipos de esquema del espacio de nombres personalizado.

Para consumir el control `CircleButton`, se declara un espacio de nombres XAML, con la declaración del espacio de nombres que especifica la dirección URL del esquema del espacio de nombres personalizado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

a continuación, se pueden agregar instancias de `CircleButton` al [`ContentPage`](xref:Xamarin.Forms.ContentPage) declararlas con el prefijo de espacio de nombres `controls`.

Para buscar los tipos de esquema de espacio de nombres personalizados, Xamarin. Forms buscará en los ensamblados a los que se hace referencia `XmlnsDefinitionAttribute` instancias. Si el `xmlns` atributo de un elemento de un archivo XAML coincide con el valor de la propiedad `XmlNamespace` en un `XmlnsDefinitionAttribute`, Xamarin. Forms intentará usar el valor de la propiedad `XmlnsDefinitionAttribute.ClrNamespace` para la resolución del tipo. Si se produce un error en la resolución de tipos, Xamarin. Forms seguirá intentando la resolución de tipos basándose en las instancias de `XmlnsDefinitionAttribute` coincidentes adicionales.

El resultado es que se muestran dos instancias de `CircleButton`:

![Botones de círculo](custom-namespace-schemas-images/circle-buttons.png "Botones de círculo")

## <a name="related-links"></a>Vínculos relacionados

- [Esquemas de espacios de nombres personalizados (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [Prefijos recomendados de espacio de nombres de XAML](custom-prefix.md)
- [Espacios de nombres XAML en Xamarin. Forms](namespaces.md)
