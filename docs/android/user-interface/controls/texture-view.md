---
title: TextureView de Xamarin. Android
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 3085131e251a787965c91216106becb6c954da85
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457710"
---
# <a name="xamarinandroid-textureview"></a>TextureView de Xamarin. Android

La `TextureView` clase es una vista que usa la representación 2D con aceleración de hardware para permitir que se muestre un flujo de contenido de vídeo o OpenGL. Por ejemplo, en la siguiente captura de pantalla se muestra la `TextureView` presentación de una fuente en directo desde la cámara del dispositivo:

[![Captura de pantalla de ejemplo de una imagen dinámica de la cámara del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A diferencia de la `SurfaceView` clase, que también se puede usar para mostrar contenido de OpenGL o vídeo, TextureView no se representa en una ventana independiente.
Por lo tanto, `TextureView` es capaz de admitir transformaciones de vista como cualquier otra vista. Por ejemplo, la rotación de un `TextureView` se puede realizar simplemente estableciendo su `Rotation` propiedad, su transparencia estableciendo su `Alpha` propiedad, etc.

Por lo tanto, con, `TextureView` ahora podemos hacer cosas como mostrar una secuencia en directo desde la cámara y transformarla, tal como se muestra en el código siguiente:

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

En el código anterior se crea una `TextureView` instancia en el método de la actividad `OnCreate` y se establece la actividad como `TextureView` `SurfaceTextureListener` . Para ser `SurfaceTextureListener` , la actividad implementa la `TextureView.ISurfaceTextureListener` interfaz. El sistema llamará al `OnSurfaceTextAvailable` método cuando el `SurfaceTexture` está listo para su uso. En este método, se toma el `SurfaceTexture` que se pasa y se establece en la textura de vista previa de la cámara. A continuación, podemos realizar operaciones normales basadas en vistas, como establecer `Rotation` y `Alpha` , como en el ejemplo anterior. La aplicación resultante, que se ejecuta en un dispositivo, se muestra a continuación:

[![Ejemplo de la aplicación que se ejecuta en un dispositivo y que muestra una imagen](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar `TextureView` , la aceleración de hardware debe estar habilitada, que será de forma predeterminada a partir del nivel de API 14. Además, dado que en este ejemplo se usa la cámara, el `android.permission.CAMERA` permiso y la `android.hardware.camera` característica deben establecerse en el **AndroidManifest.xml**.

## <a name="related-links"></a>Vínculos relacionados

- [TextureViewDemo (ejemplo)](/samples/xamarin/monodroid-samples/textureviewdemo)/)