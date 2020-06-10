---
Título: "solución de problemas" Descripción: "condiciones de error comunes y cómo resolverlos" MS. topic: solución de problemas de MS. Prod: Xamarin ms. AssetID: 63291951-7375-4CBF-BCC3-2E4AD157A2C8 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/25/2017 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="troubleshooting"></a>Solución de problemas

_Condiciones de error comunes y cómo resolverlas_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Error: "no se puede encontrar una versión de Xamarin.Forms compatible con..."

Los errores siguientes pueden aparecer en la ventana de la **consola del paquete** al actualizar todos los paquetes de NuGet en una Xamarin.Forms solución o en un Xamarin.Forms proyecto de aplicación de Android:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>¿Qué causa este error?

Visual Studio para Mac (o Visual Studio) pueden indicar que las actualizaciones están disponibles para el Xamarin.Forms paquete de NuGet *y todas sus dependencias*. En Xamarin Studio, el nodo **paquetes** de la solución podría ser similar al siguiente (los números de versión pueden ser diferentes):

![](images/updates-available.png "Android Project Packages Folder")

Este error puede producirse si intenta actualizar _todos_ los paquetes.

Esto se debe a que con los proyectos de Android establecidos en una versión de destino o compilación de Android 6,0 (API 23) o inferior, Xamarin.Forms tiene una dependencia fuerte en versiones *específicas* de los paquetes de soporte de Android. Aunque las versiones actualizadas de esos paquetes pueden estar disponibles, Xamarin.Forms no es necesariamente compatible con ellas.

En este caso, _solo_ debe actualizar el **Xamarin.Forms** paquete, ya que se asegurará de que las dependencias permanecen en versiones compatibles. Otros paquetes que ha agregado al proyecto también se pueden actualizar de forma individual siempre que no hagan que los paquetes de soporte de Android se actualicen.

> [!NOTE]
> Si usa Xamarin.Forms 2.3.4 o posterior **y** la versión de destino o compilación del proyecto de Android está establecida en Android 7,0 (API 24) o superior, las dependencias fuertes mencionadas anteriormente ya no se aplican y puede actualizar los paquetes de soporte independientemente del Xamarin.Forms paquete.

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Corregir: quitar todos los paquetes y volver a agregarXamarin.Forms

Si los paquetes de **Xamarin. Android. support** se han actualizado a versiones incompatibles, la solución más sencilla consiste en:

1. Elimine manualmente todos los paquetes NuGet en el proyecto de Android y, a continuación,
2. Vuelva a agregar el **Xamarin.Forms** paquete.

Se descargarán automáticamente las versiones *correctas* de los demás paquetes.

Para ver un vídeo de este proceso, consulte esta [publicación de foros](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
