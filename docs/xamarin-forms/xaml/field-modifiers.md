---
title: Modificadores de campo XAML en Xamarin. Forms
description: El atributo de espacio de nombres X:FieldModifier (especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
ms.openlocfilehash: 0f6050de943ca9878cf41b448d44bf222689be56
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739452"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML en Xamarin. Forms

El `x:FieldModifier` atributo namespace especifica el nivel de acceso para los campos generados para los elementos XAML con nombre. Los valores válidos del atributo son:

- `private`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro del cuerpo de la clase en la que se declara.
- `public`: especifica que el campo generado para el elemento XAML no tiene restricciones de acceso.
- `protected`: especifica que el campo generado para el elemento XAML es accesible dentro de su clase y por instancias de la clase derivada.
- `internal`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro de los tipos del mismo ensamblado.
- `notpublic`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro de los tipos del mismo ensamblado.

De forma predeterminada, si no se establece el valor del atributo, el campo generado para el elemento `private`será.

> [!NOTE]
> El valor del atributo puede usar cualquier grafía, ya que Xamarin. Forms lo convertirá a minúsculas.

Se deben cumplir las condiciones siguientes para que `x:FieldModifier` se procese un atributo:

- El elemento XAML de nivel superior debe ser un válido `x:Class`.
- El elemento XAML actual tiene un `x:Name` especificado.

En el código XAML siguiente se muestran ejemplos del establecimiento del atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> El `x:FieldModifier` atributo no se puede usar para especificar el nivel de acceso de una clase XAML.
