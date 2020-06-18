---
title: 'title: "Desencadenadores de doble pantalla de Xamarin.Forms" description: "En este artículo se explica cómo usar desencadenadores de doble pantalla de Xamarin.Forms para responder a cambios de la interfaz de usuario con XAML."'
description: 'ms.prod: xamarin ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59 ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 02/28/2020 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df10327b2ac12d2b119f1ab558d7f27e8c319507
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84131201"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Desencadenadores de doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

El espacio de nombres de [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) incluye dos desencadenadores de estado:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) desencadena un cambio en [`VisualState`](xref:Xamarin.Forms.VisualState) cuando se modifica el modo de vista del diseño adjunto.
- `WindowSpanModeStateTrigger` desencadena un cambio en [`VisualState`](xref:Xamarin.Forms.VisualState) cuando se modifica el modo de vista de la ventana.

Para obtener más información sobre desencadenadores de estado, vea [Desencadenadores de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Desencadenador de estado de modo de intervalo

Un elemento [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) desencadena un cambio en [`VisualState`](xref:Xamarin.Forms.VisualState) cuando se modifica el modo de intervalo del diseño adjunto. Este desencadenador tiene una única propiedad enlazable:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), que indica el modo de intervalo al que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Grid`](xref:Xamarin.Forms.Grid) que incluye objetos `SpanModeStateTrigger`:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

En este ejemplo, los estados visuales se establecen en un objeto [`Grid`](xref:Xamarin.Forms.Grid). El color de fondo del elemento `Grid` es verde cuando solo se muestra un panel, rojo cuando se muestran los paneles en paralelo y púrpura cuando los paneles se muestran de arriba a abajo.

## <a name="window-span-mode-state-trigger"></a>Desencadenador de estado de modo de intervalo de ventana

Un elemento `WindowSpanModeStateTrigger` desencadena un cambio en [`VisualState`](xref:Xamarin.Forms.VisualState) cuando se modifica el modo de intervalo de la ventana. Este desencadenador tiene una única propiedad enlazable:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), que indica el modo de intervalo al que debe aplicarse [`VisualState`](xref:Xamarin.Forms.VisualState).

> [!NOTE]
> El elemento `WindowSpanModeStateTrigger` deriva de la clase [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) y, por tanto, puede adjuntar un controlador de eventos al evento [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged).

En el siguiente ejemplo de XAML, se muestra una clase [`Grid`](xref:Xamarin.Forms.Grid) que incluye objetos `WindowSpanModeStateTrigger`:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

En este ejemplo, los estados visuales se establecen en un objeto [`Grid`](xref:Xamarin.Forms.Grid). El color de fondo del elemento `Grid` es rojo cuando solo se muestra un panel, verde cuando se muestran los paneles en paralelo y amarillo cuando los paneles se muestran de arriba a abajo.

## <a name="related-links"></a>Vínculos relacionados

- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
