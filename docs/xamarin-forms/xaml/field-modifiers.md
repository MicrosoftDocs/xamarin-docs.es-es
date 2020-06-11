---
title: "los modificadores de campo XAML en Xamarin.Forms " Description: "el atributo de espacio de nombres x:FieldModifier (especifica el nivel de acceso para los campos generados para los elementos XAML con nombre".
MS. Prod: Xamarin ms. AssetID: 12357CE0-3C11-4B62-947F-72DB6DFC23A2 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/02/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML enXamarin.Forms

El `x:FieldModifier` atributo namespace especifica el nivel de acceso para los campos generados para los elementos XAML con nombre. Los valores válidos del atributo son:

- `private`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro del cuerpo de la clase en la que se declara.
- `public`: especifica que el campo generado para el elemento XAML no tiene restricciones de acceso.
- `protected`: especifica que el campo generado para el elemento XAML es accesible dentro de su clase y por instancias de la clase derivada.
- `internal`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro de los tipos del mismo ensamblado.
- `notpublic`: especifica que solo se puede tener acceso al campo generado para el elemento XAML dentro de los tipos del mismo ensamblado.

De forma predeterminada, si no se establece el valor del atributo, el campo generado para el elemento será `private` .

> [!NOTE]
> El valor del atributo puede usar cualquier distinción de mayúsculas y minúsculas, ya que se convertirá a minúsculas Xamarin.Forms .

Se deben cumplir las condiciones siguientes para `x:FieldModifier` que se procese un atributo:

- El elemento XAML de nivel superior debe ser un válido `x:Class` .
- El elemento XAML actual tiene un `x:Name` especificado.

En el código XAML siguiente se muestran ejemplos del establecimiento del atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> El `x:FieldModifier` atributo no se puede usar para especificar el nivel de acceso de una clase XAML.
