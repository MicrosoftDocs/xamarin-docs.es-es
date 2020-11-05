---
title: Esquemas de espacios de nombres personalizados XAML en Xamarin.Forms
description: Un esquema de espacio de nombres personalizado XAML se puede definir con la clase XmlnsDefinitionAttribute, que especifica una asignación entre una dirección URL personalizada y uno o varios espacios de nombres CLR. El esquema de espacio de nombres personalizado se puede usar en las declaraciones de espacio de nombres XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f950db0694b21239b742867d519e893d9a62384c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374074"
---
# <a name="xaml-custom-namespace-schemas-in-no-locxamarinforms"></a>Esquemas de espacios de nombres personalizados XAML en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

Se puede hacer referencia a los tipos de una biblioteca en XAML declarando un espacio de nombres XAML para la biblioteca, con la declaración de espacio de nombres que especifica el nombre del espacio de nombres de Common Language Runtime (CLR) y un nombre de ensamblado:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

Sin embargo, si se especifica un nombre de ensamblado y un espacio de nombres CLR en una `xmlns` definición, puede resultar difícil y propenso a errores. Además, es posible que se requieran varias declaraciones de espacios de nombres XAML si la biblioteca contiene tipos en varios espacios de nombres.

Un enfoque alternativo es definir un esquema de espacio de nombres personalizado, como `http://mycompany.com/schemas/controls` , que se asigna a uno o varios espacios de nombres CLR. Esto permite que una única declaración de espacio de nombres XAML haga referencia a todos los tipos de un ensamblado, aunque estén en diferentes espacios de nombres. También habilita una única declaración de espacio de nombres XAML para hacer referencia a los tipos de varios ensamblados.

Para obtener más información sobre los espacios de nombres XAML, vea [espacios Xamarin.Forms de nombres XAML en ](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definir un esquema de espacio de nombres personalizado

La aplicación de ejemplo contiene una biblioteca que expone algunos controles simples, como `CircleButton` :

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

Todos los controles de la biblioteca residen en el `MyCompany.Controls` espacio de nombres. Estos controles se pueden exponer a un ensamblado de llamada a través de un esquema de espacio de nombres personalizado.

Un esquema de espacio de nombres personalizado se define con la `XmlnsDefinitionAttribute` clase, que especifica la asignación entre un espacio de nombres XAML y uno o varios espacios de nombres CLR. `XmlnsDefinitionAttribute`Toma dos argumentos: el nombre del espacio de nombres XAML y el nombre del espacio de nombres CLR. El nombre del espacio de nombres XAML se almacena en la `XmlnsDefinitionAttribute.XmlNamespace` propiedad y el nombre del espacio de nombres CLR se almacena en la `XmlnsDefinitionAttribute.ClrNamespace` propiedad.

> [!NOTE]
> La `XmlnsDefinitionAttribute` clase también tiene una propiedad denominada `AssemblyName` , que se puede establecer opcionalmente en el nombre del ensamblado. Esto solo es necesario cuando un espacio de nombres CLR al que se hace referencia desde un `XmlnsDefinitionAttribute` se encuentra en un ensamblado externo.

`XmlnsDefinitionAttribute`Debe definirse en el nivel de ensamblado del proyecto que contiene los espacios de nombres CLR que se asignarán en el esquema de espacio de nombres personalizado. En el ejemplo siguiente se muestra el archivo **AssemblyInfo.CS** de la aplicación de ejemplo:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Este código crea un esquema de espacio de nombres personalizado que asigna la `http://mycompany.com/schemas/controls` dirección URL al `MyCompany.Controls` espacio de nombres CLR. Además, el `Preserve` atributo se especifica en el ensamblado para asegurarse de que el vinculador conserva todos los tipos del ensamblado.

> [!IMPORTANT]
> El `Preserve` atributo se debe aplicar a las clases del ensamblado que se asignan a través del esquema del espacio de nombres personalizado o se aplican a todo el ensamblado.

El esquema de espacio de nombres personalizado se puede usar para la resolución de tipos en los archivos XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Consumir un esquema de espacio de nombres personalizado

Para consumir tipos del esquema de espacio de nombres personalizado, el compilador XAML requiere que haya una referencia de código del ensamblado que consuma los tipos, al ensamblado que define los tipos. Esto puede realizarse agregando una clase que contiene un `Init` método al ensamblado que define los tipos que se consumirán a través de XAML:

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

`Init`A continuación, se puede llamar al método desde el ensamblado que consume tipos del esquema de espacio de nombres personalizado:

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

Para consumir el `CircleButton` control, se declara un espacio de nombres XAML, con la declaración del espacio de nombres que especifica la dirección URL del esquema del espacio de nombres personalizado:

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

`CircleButton` después, las instancias se pueden agregar al [`ContentPage`](xref:Xamarin.Forms.ContentPage) declararlas con el `controls` Prefijo de espacio de nombres.

Para buscar los tipos de esquema de espacio de nombres personalizados, Xamarin.Forms buscará en los ensamblados a los que se hace referencia `XmlnsDefinitionAttribute` . Si el `xmlns` atributo de un elemento de un archivo XAML coincide con el `XmlNamespace` valor de propiedad en `XmlnsDefinitionAttribute` , Xamarin.Forms intentará usar el `XmlnsDefinitionAttribute.ClrNamespace` valor de propiedad para la resolución del tipo. Si se produce un error en la resolución de tipos, Xamarin.Forms seguirá intentando la resolución de tipos basándose en las instancias coincidentes adicionales `XmlnsDefinitionAttribute` .

El resultado es que `CircleButton` se muestran dos instancias:

![Botones de círculo](custom-namespace-schemas-images/circle-buttons.png "Botones de círculo")

## <a name="related-links"></a>Vínculos relacionados

- [Esquemas de espacios de nombres personalizados (ejemplo)](/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [Prefijos recomendados de espacio de nombres de XAML](custom-prefix.md)
- [Espacios de nombres XAML en Xamarin.Forms](namespaces.md)