---
title: Conmutación por recuperación de enlaces de Xamarin.Forms
description: En este artículo se explica cómo hacer que los enlaces sean más sólidos mediante la definición de los valores de reserva que se usarán si se produce un error de enlace.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2c5ce10aa0a1137ad4e4832d5dc931c902b82810
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946213"
---
# <a name="xamarinforms-binding-fallbacks"></a>Conmutación por recuperación de enlaces de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

En ocasiones, los enlaces de datos producen errores, porque no se puede resolver el origen de enlace o porque el enlace se realiza correctamente pero devuelve un valor `null`. Si bien estos escenarios se pueden controlar con los convertidores de valor, u otro código adicional, los enlaces de datos pueden hacerse más sólidos mediante la definición de valores de reserva para su uso si se produce un error en el proceso de enlace. Esto puede realizarse mediante la definición de las propiedades [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) y [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) en una expresión de enlace. Dado que estas propiedades residen en la clase [`BindingBase`](xref:Xamarin.Forms.BindingBase), pueden usarse con enlaces, enlaces múltiples, enlaces compilados y la extensión de marcado `Binding`.

> [!NOTE]
> El uso de las propiedades [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) y [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) en una expresión de enlace es opcional.

## <a name="defining-a-fallback-value"></a>Definición de un valor de reserva

La propiedad [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) permite que se pueda definir un valor de reserva que se usará cuando el *origen* del enlace no se pueda resolver. Un escenario común para establecer esta propiedad es cuando se realiza el enlace con propiedades de origen que podrían no existir en todos los objetos en una colección enlazada de tipos heterogéneos.

La página **MonkeyDetail** ilustra el establecimiento de la propiedad [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue):

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

El enlace en [`Label`](xref:Xamarin.Forms.Label) define un valor de [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) que se establecerá en el destino si no se puede resolver el origen del enlace. Por lo tanto, el valor definido por la propiedad `FallbackValue` se mostrará si la propiedad `Population` no existe en el objeto enlazado. Tenga en cuenta que aquí, el valor de la propiedad `FallbackValue` está delimitado por caracteres de comillas simples (apóstrofo).

En lugar de definir los valores de la propiedad [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) en línea, se recomienda definirlos como recursos en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). La ventaja de este enfoque es que estos valores se definen una vez en una sola ubicación y son localizables más fácilmente. Posteriormente, se pueden recuperar los recursos mediante la extensión de marcado `StaticResource`:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> No es posible establecer la propiedad `FallbackValue` con una expresión de enlace.

Esta es la ejecución del programa:

![Enlace FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Enlace FallbackValue")

Cuando el `FallbackValue` propiedad no está establecida en una expresión de enlace y la ruta de acceso del enlace o parte de la ruta de acceso no se resuelve, [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) se establece en el destino. Sin embargo, cuando la propiedad `FallbackValue` está establecida y la ruta de acceso del enlace o parte de la ruta de acceso no se resuelve, el valor de la propiedad del valor `FallbackValue` se establece en el destino. Por lo tanto, en la página **MonkeyDetail**, [`Label`](xref:Xamarin.Forms.Label) muestra el mensaje "Population size unknown" ("Tamaño de la población desconocido"), porque al elemento enlazado le falta una propiedad `Population`.

> [!IMPORTANT]
> Un convertidor de valores definido no se ejecuta en una expresión de enlace cuando la propiedad [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) está establecida.

## <a name="defining-a-null-replacement-value"></a>Definición de un valor de reemplazo nulo

La propiedad [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) permite que se pueda definir un valor de reemplazo que se usará cuando el *origen* del enlace se resuelva pero el valor sea `null`. Un escenario común para establecer esta propiedad es cuando se realiza el enlace con propiedades de origen que podrían ser `null` en una colección enlazada.

La página **Monkeys** ilustra el establecimiento de la propiedad [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue):

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Los enlaces en [`Image`](xref:Xamarin.Forms.Image) y [`Label`](xref:Xamarin.Forms.Label) definen valores [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) que se aplicará si la ruta de acceso de enlace devuelve `null`. Por lo tanto, los valores definidos por las propiedades `TargetNullValue` se mostrarán para los objetos de la colección donde las propiedades `ImageUrl` y `Location` no estén definidas. Tenga en cuenta que aquí, los valores de la propiedad `TargetNullValue` están delimitados por caracteres de comillas simples (apóstrofo).

En lugar de definir los valores de la propiedad [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) en línea, se recomienda definirlos como recursos en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). La ventaja de este enfoque es que estos valores se definen una vez en una sola ubicación y son localizables más fácilmente. Posteriormente, se pueden recuperar los recursos mediante la extensión de marcado `StaticResource`:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> No es posible establecer la propiedad `TargetNullValue` con una expresión de enlace.

Esta es la ejecución del programa:

[![Enlace TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "Enlace TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Enlace TargetNullValue")

Cuando la propiedad `TargetNullValue` no está establecida en una expresión de enlace, un valor de origen de `null` se convertirá si se define un convertidor de valores, se le aplicará formato si se define `StringFormat` y, a continuación, se establecerá el resultado en el destino. Sin embargo, cuando la propiedad `TargetNullValue` está establecida, un valor de origen de `null` se convertirá si se ha definido un convertidor de valores, y si sigue siendo `null` después de la conversión, el valor de la propiedad `TargetNullValue` está establecido en el destino.

> [!IMPORTANT]
> No se aplicará formato a la cadena en una expresión de enlace cuando la propiedad `TargetNullValue` esté establecida.

## <a name="related-links"></a>Vínculos relacionados

- [Data Binding Demos (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demos de enlace de datos [ejemplo])
