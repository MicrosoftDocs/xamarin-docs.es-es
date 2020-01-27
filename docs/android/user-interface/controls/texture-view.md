---
title: TextureView de Xamarin. Android
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724981"
---
# <a name="xamarinandroid-textureview"></a>TextureView de Xamarin. Android

La clase `TextureView` es una vista que usa la representación 2D con aceleración de hardware para habilitar la presentación de un flujo de contenido de vídeo o OpenGL. Por ejemplo, en la siguiente captura de pantalla se muestra el `TextureView` mostrar una fuente dinámica desde la cámara del dispositivo:

[![captura de pantalla de ejemplo de una imagen dinámica de la cámara del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A diferencia de la clase `SurfaceView`, que también se puede usar para mostrar contenido de OpenGL o vídeo, TextureView no se representa en una ventana independiente.
Por lo tanto, `TextureView` es capaz de admitir transformaciones de vista como cualquier otra vista. Por ejemplo, la rotación de un `TextureView` se puede realizar simplemente estableciendo su propiedad `Rotation`, su transparencia estableciendo su propiedad `Alpha`, etc.

Por lo tanto, con la `TextureView` podemos hacer cosas como mostrar una secuencia en directo desde la cámara y transformarla, tal y como se muestra en el código siguiente:

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

En el código anterior se crea una instancia de `TextureView` en el método de `OnCreate` de la actividad y se establece la actividad como `SurfaceTextureListener`del `TextureView`. Para ser el `SurfaceTextureListener`, la actividad implementa la interfaz `TextureView.ISurfaceTextureListener`. El sistema llamará al método `OnSurfaceTextAvailable` cuando el `SurfaceTexture` esté listo para su uso. En este método, tomamos el `SurfaceTexture` que se pasa y lo establece en la textura de vista previa de la cámara. A continuación, podemos realizar operaciones normales basadas en vistas, como establecer el `Rotation` y `Alpha`, como en el ejemplo anterior. La aplicación resultante, que se ejecuta en un dispositivo, se muestra a continuación:

[![ejemplo de la aplicación que se ejecuta en un dispositivo y que muestra una imagen](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar el `TextureView`, la aceleración de hardware debe estar habilitada, que será de forma predeterminada a partir del nivel de API 14. Además, dado que en este ejemplo se usa la cámara, el permiso `android.permission.CAMERA` y la característica `android.hardware.camera` deben establecerse en **archivo AndroidManifest. XML**.

## <a name="related-links"></a>Vínculos relacionados

- [TextureViewDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/)
