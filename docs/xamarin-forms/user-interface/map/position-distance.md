---
Título: ' Xamarin.Forms posición de asignación y distancia ' Descripción: ' Xamarin.Forms . El espacio de nombres Maps contiene una estructura position que se usa normalmente al colocar una asignación y sus clavijas, y una estructura de distancia que se puede usar opcionalmente al colocar un mapa.
MS. Prod: MS. AssetID: MS. Technology: Author: MS. Author: MS. Date: no-LOC:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-map-position-and-distance"></a>Xamarin.FormsPosición y distancia del mapa

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

El [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espacio de nombres contiene un [`Position`](xref:Xamarin.Forms.Maps.Position) struct que se usa normalmente al colocar una asignación y sus clavijas, y un [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que se puede usar opcionalmente al colocar un mapa.

## <a name="position"></a>Posición

La [`Position`](xref:Xamarin.Forms.Maps.Position) estructura encapsula una posición almacenada como valores de latitud y longitud. Este struct define dos propiedades de solo lectura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de tipo `double` , que representa la latitud de la posición en grados decimales.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de tipo `double` , que representa la longitud de la posición en grados decimales.

[`Position`](xref:Xamarin.Forms.Maps.Position)los objetos se crean con el `Position` constructor, que requiere que los argumentos de latitud y longitud se especifiquen como `double` valores:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Al crear un `Position` objeto, el valor de latitud se unirá entre-90,0 y 90,0, y el valor de longitud se fijará entre-180,0 y 180,0.

> [!NOTE]
> La `GeographyUtils` clase tiene un `ToRadians` método de extensión que convierte un `double` valor de grados a radianes y un `ToDegrees` método de extensión que convierte un `double` valor de radianes en grados.

## <a name="distance"></a>Distancia

El [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct encapsula una distancia almacenada como un `double` valor, que representa la distancia en metros. Este struct define tres propiedades de solo lectura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de tipo `double` , que representa la distancia en kilómetros que abarca `Distance` .
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de tipo `double` , que representa la distancia en metros que abarca `Distance` .
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de tipo `double` , que representa la distancia en millas que abarca `Distance` .

[`Distance`](xref:Xamarin.Forms.Maps.Distance)los objetos se pueden crear con el `Distance` constructor, que requiere un argumento de metros especificado como `double` :

```csharp
Distance distance = new Distance(1450.5);
```

Como alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) los objetos se pueden crear con [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) los [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) métodos de generador,, [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) y `BetweenPositions` :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
