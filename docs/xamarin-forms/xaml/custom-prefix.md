---
title: "prefijos recomendados del espacio de nombres XAML en Xamarin.Forms " Description: "los autores de controles pueden usar la clase XmlnsPrefixAttribute para especificar un prefijo recomendado para asociarlo a un espacio de nombres XAML, para el uso de XAML".
MS. Prod: Xamarin ms. AssetID: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/28/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>Prefijos recomendados del espacio de nombres XAML enXamarin.Forms

Los `XmlnsPrefixAttribute` autores de controles pueden usar la clase para especificar un prefijo recomendado para asociarlo a un espacio de nombres XAML, para el uso de XAML. El prefijo es útil al admitir la serialización del árbol de objetos en XAML o al interactuar con un entorno de diseño que tiene características de edición de XAML. Por ejemplo:

- Los editores de texto XAML podrían usar `XmlnsPrefixAttribute` como sugerencia una asignación inicial de espacio de nombres XAML `xmlns` .
- Los entornos de diseño XAML podrían usar `XmlnsPrefixAttribute` para agregar asignaciones al XAML al arrastrar objetos fuera de un cuadro de herramientas y a una superficie de diseño visual.

Los prefijos de espacio de nombres recomendados deben definirse en el nivel de ensamblado con el `XmlnsPrefixAttribute` constructor, que toma dos argumentos: una cadena que especifica el identificador de un espacio de nombres XAML y una cadena que especifica un prefijo recomendado:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

Los prefijos deben usar cadenas cortas, porque el prefijo se aplica normalmente a todos los elementos serializados que proceden del espacio de nombres XAML. Por lo tanto, la longitud de la cadena de prefijo puede tener un efecto perceptible en el tamaño de la salida XAML serializada.

> [!NOTE]
> `XmlnsPrefixAttribute`Se puede aplicar más de una a un ensamblado. Por ejemplo, si tiene un ensamblado que define los tipos de más de un espacio de nombres XAML, puede definir diferentes valores de prefijo para cada espacio de nombres XAML.

## <a name="related-links"></a>Vínculos relacionados

- [Esquemas de espacio de nombres personalizado XAML](custom-namespace-schemas.md)
- [Espacios de nombres XAML enXamarin.Forms](namespaces.md)
