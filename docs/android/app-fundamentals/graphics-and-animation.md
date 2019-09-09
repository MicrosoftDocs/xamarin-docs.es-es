---
title: Gráficos y animación
description: Android proporciona un marco de trabajo muy rico y diverso para admitir animaciones y gráficos 2D. En este tema se presentan estos marcos de trabajo y se describe cómo crear animaciones y gráficos personalizados para su uso en una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: ea713b2b56f18c435f3ec676b42d0aa4802abc6a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755426"
---
# <a name="android-graphics-and-animation"></a>Gráficos y animación de Android

_Android proporciona un marco de trabajo muy rico y diverso para admitir animaciones y gráficos 2D. En este tema se presentan estos marcos de trabajo y se describe cómo crear animaciones y gráficos personalizados para su uso en una aplicación de Xamarin. Android._

## <a name="overview"></a>Información general

A pesar de que se ejecutan en dispositivos que tradicionalmente tienen una capacidad limitada, las aplicaciones móviles con mayor clasificación suelen tener una experiencia de usuario sofisticada, completada con gráficos de alta calidad y animaciones que proporcionan una sensación intuitiva, dinámica y receptiva. A medida que las aplicaciones móviles son más sofisticadas, los usuarios han empezado a esperar más y más de aplicaciones.

Afortunadamente para nosotros, las plataformas móviles modernas tienen marcos muy eficaces para crear animaciones sofisticadas y gráficos personalizados a la vez que conservan la facilidad de uso. Esto permite a los desarrolladores agregar interactividad enriquecida con muy poco esfuerzo.

Los marcos de API de interfaz de usuario en Android pueden dividirse en dos categorías aproximadamente: Gráficos y animación.

Los gráficos se dividen en enfoques diferentes para hacer gráficos 2D y 3D. los gráficos 3D están disponibles a través de una serie de marcos de trabajo integrados como OpenGL ES (una versión específica para móviles de OpenGL) y marcos de terceros como monogame (un kit de herramientas multiplataforma compatible con XNA Toolkit). Aunque los gráficos 3D no se encuentran dentro del ámbito de este artículo, examinaremos las técnicas de dibujo 2D integradas.

Android proporciona dos API diferentes para crear gráficos 2D. Uno es un enfoque declarativo de alto nivel y el otro mediante programación API de bajo nivel:

- **Recursos Dibujables** &ndash; Se usan para crear gráficos personalizados mediante programación o (más normalmente) incrustando instrucciones de dibujo en archivos XML. Los recursos que se pueden dibujar se suelen definir como archivos XML que contienen instrucciones o acciones para que Android represente un gráfico 2D. 

- **Lienzo** de &ndash; se trata de una API de bajo nivel que implica dibujar directamente en un mapa de bits subyacente. Proporciona un control muy exhaustivo sobre lo que se muestra. 

Además de estas técnicas de gráficos 2D, Android también proporciona varias maneras de crear animaciones:

- **Animaciones Dibujables** Android también admite animaciones de fotogramas fotogramas conocidas como *animación dibujable.* &ndash; Esta es la API de animación más sencilla. Android se carga secuencialmente y muestra los recursos que se dibujan en secuencia (muy parecido a un dibujo).

- **Ver animaciones** Las animaciones de vista son las API de animación originales en Android y están disponibles en todas las versiones de Android. &ndash; Esta API está limitada, ya que solo funcionará con objetos de vista y solo puede realizar transformaciones simples en esas vistas.
    Las animaciones de vista se definen normalmente en los archivos XML `/Resources/anim` que se encuentran en la carpeta.

- **Animaciones de propiedades** Android 3,0 presentó un nuevo conjunto de API de animación conocido como *animaciones de propiedades.* &ndash; Estas nuevas API incorporan un sistema extensible y flexible que se puede usar para animar las propiedades de cualquier objeto, no solo los objetos de vista. Esta flexibilidad permite encapsular animaciones en clases distintas que harán que el uso compartido del código sea más sencillo.

Las animaciones de vista son más adecuadas para las aplicaciones que deben ser compatibles con las API anteriores a Android 3,0 (nivel de API 11). De lo contrario, las aplicaciones deben usar las API de animación de propiedades más recientes por las razones mencionadas anteriormente.

Todos estos marcos son opciones viables; sin embargo, siempre que sea posible, se deben proporcionar preferencias a las animaciones de propiedades, ya que es una API más flexible con la que trabajar. Las animaciones de propiedades permiten encapsular la lógica de animación en clases distintas que facilita el uso compartido de código y simplifica el mantenimiento del código.

## <a name="accessibility"></a>Accesibilidad

Los gráficos y las animaciones ayudan a que las aplicaciones Android sean atractivas y divertidas de usar. sin embargo, es importante recordar que algunas interacciones se producen a través de screenreaders, dispositivos de entrada alternativos o con el zoom asistido.
Además, algunas interacciones pueden producirse sin capacidades de audio.

Las aplicaciones son más fáciles de usar en estas situaciones si se han diseñado pensando en la accesibilidad: proporcionar sugerencias y ayuda de navegación en la interfaz de usuario, y garantizar que hay contenido de texto o descripciones de los elementos gráficos de la interfaz de usuario.

Consulte [la guía de accesibilidad de Google](https://developer.android.com/guide/topics/ui/accessibility/) para obtener más información sobre cómo se usan las API de accesibilidad de Android.

## <a name="2d-graphics"></a>Gráficos 2D

Los recursos que se pueden dibujar son una técnica popular en aplicaciones Android. Como con otros recursos, los recursos que se pueden &ndash; dibujar son declarativos que se definen en archivos XML. Este enfoque permite una separación limpia del código de los recursos. Esto puede simplificar el desarrollo y el mantenimiento, ya que no es necesario cambiar el código para actualizar o cambiar los gráficos en una aplicación de Android. Sin embargo, aunque los recursos que se pueden dibujar son útiles para muchos requisitos gráficos sencillos y comunes, carecen de la eficacia y el control de la API de canvas.

La otra técnica, con el objeto [Canvas](xref:Android.Graphics.Canvas) , es muy similar a la de otros marcos de API tradicionales, como System. Drawing o el dibujo principal de iOS. El uso del objeto Canvas proporciona el máximo control sobre cómo se crean los gráficos 2D. Es adecuado para situaciones en las que un recurso que se puede dibujar no funcionará o será difícil de trabajar con él. Por ejemplo, puede ser necesario dibujar un control deslizante personalizado cuya apariencia cambiará en función de los cálculos relacionados con el valor del control deslizante.

Vamos a examinar los recursos que se van a dibujar en primer lugar. Son más sencillas y cubren los casos de dibujo personalizados más comunes.

### <a name="drawable-resources"></a>Recursos Dibujables

Los recursos que se pueden dibujar se definen en un archivo XML `/Resources/drawable`en el directorio. A diferencia de la incrustación de PNG o JPEG, no es necesario proporcionar versiones específicas de densidad de recursos que se pueda dibujar.
En tiempo de ejecución, una aplicación de Android cargará estos recursos y usará las instrucciones de estos archivos XML para crear gráficos 2D.
Android define varios tipos diferentes de recursos que se dibujan:

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; Se trata de un objeto dibujable que dibuja una forma geométrica primitiva y aplica un conjunto limitado de efectos gráficos en esa forma. Son muy útiles para cosas como la personalización de botones o la configuración del fondo de TextViews. Veremos un ejemplo de cómo usar un `ShapeDrawable` más adelante en este artículo.

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; Se trata de un recurso que se dibuja y que cambiará la apariencia según el estado de un widget/control. Por ejemplo, un botón puede cambiar su apariencia en función de si está presionado o no.

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; Este recurso dibujable que apilará varios drawables uno encima de otro. En la captura de pantalla siguiente se muestra un ejemplo de un *LayerDrawable* :

    ![Ejemplo de LayerDrawable](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) Se trata de una LayerDrawable, pero con una diferencia. &ndash; Un *TransitionDrawable* es capaz de animar una capa que se muestra encima de otra encima.

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) Esto es muy similar a un StateListDrawable en que mostrará una imagen basada en determinadas condiciones. &ndash; Sin embargo, a diferencia de *StateListDrawable*, *LevelListDrawable* muestra una imagen basada en un valor entero. Un ejemplo de una *LevelListDrawable* sería Mostrar la fuerza de una señal WiFi. A medida que cambia la intensidad de la señal Wi-Fi, el dibujo que se muestra cambiará en consecuencia.

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; Como su nombre implica, estos Drawables proporcionan la funcionalidad de escalado y recorte. El *ScaleDrawable* escalará otro dibujable, mientras que el *ClipDrawable* recortará otro dibujable.

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; Este dibujo se aplicará a los lados de otro recurso que se dibuja. Se utiliza cuando una vista necesita un fondo menor que los límites reales de la vista.

- XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; este archivo es un conjunto de instrucciones, en XML, que se realizarán en un mapa de bits real. Algunas acciones que Android puede realizar son el mosaico, la interpolación y el suavizado de contorno. Uno de los usos muy comunes de esto es poner en mosaico un mapa de bits a través del fondo de un diseño.

#### <a name="drawable-example"></a>Ejemplo dibujable

Echemos un vistazo a un ejemplo rápido de cómo crear un gráfico 2D con un `ShapeDrawable`. Un `ShapeDrawable` puede definir una de las cuatro formas básicas: rectángulo, óvalo, línea y anillo. También es posible aplicar efectos básicos, como el degradado, el color y el tamaño. El siguiente XML es un `ShapeDrawable` que se puede encontrar en el proyecto complementario *AnimationsDemo* (en el `Resources/drawable/shape_rounded_blue_rect.xml`archivo).
Define un rectángulo con un fondo de degradado púrpura y esquinas redondeadas:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

Podemos hacer referencia a este recurso que se puede dibujar mediante declaración en un diseño u otro dibujable, tal y como se muestra en el siguiente XML:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

Los recursos Dibujables también se pueden aplicar mediante programación. En el fragmento de código siguiente se muestra cómo establecer mediante programación el fondo de una TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver el aspecto que tendría esto, ejecute el proyecto *AnimationsDemo* y seleccione el elemento dibujado por la forma en el menú principal. Deberíamos ver algo parecido a la captura de pantalla siguiente:

![TextView con un fondo personalizado, dibujable con un degradado y con esquinas redondeadas](graphics-and-animation-images/image1.png)

Para obtener más información sobre los elementos XML y la sintaxis de los recursos que se van a dibujar, consulte la [documentación de Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

### <a name="using-the-canvas-drawing-api"></a>Usar la API de dibujo de Canvas

Los Drawables son eficaces pero tienen sus limitaciones. Algunas cosas no son posibles o muy complejas (por ejemplo: aplicar un filtro a una imagen tomada por una cámara en el dispositivo). Sería muy difícil aplicar la reducción de ojos rojos mediante un recurso que se puede dibujar.
En su lugar, la API de canvas permite que una aplicación tenga un control muy específico para cambiar los colores de una parte específica de la imagen.

Una clase que se usa normalmente con el lienzo es la clase [Paint](xref:Android.Graphics.Paint) . Esta clase contiene información de color y estilo sobre cómo dibujar. Se usa para proporcionar elementos como un color y una transparencia.

La API de canvas usa el *modelo de la pintor* para dibujar gráficos 2D.
Las operaciones se aplican en capas sucesivas unas de otras. Cada operación cubrirá parte del mapa de bits subyacente. Cuando el área se superpone a un área dibujada anteriormente, el nuevo dibujo ocultará el antiguo de forma parcial o totalmente. Esta es la misma manera que muchas otras API de dibujo como los gráficos principales de System. Drawing y iOS.

Hay dos maneras de obtener un `Canvas` objeto. La primera forma implica definir un objeto de [mapa de bits](xref:Android.Graphics.Bitmap) y, a `Canvas` continuación, crear una instancia de un objeto con él. Por ejemplo, el fragmento de código siguiente crea un nuevo lienzo con un mapa de bits subyacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

La otra manera de obtener un `Canvas` objeto es mediante el método de devolución de llamada [OnDraw](xref:Android.Views.View.OnDraw*) que se proporciona a la clase base de [vista](xref:Android.Views.View) . Android llama a este método cuando decide que una vista debe dibujarse y pasar un `Canvas` objeto para la vista con la que trabajar.

La clase Canvas expone métodos para proporcionar las instrucciones Draw mediante programación. Por ejemplo:

- [Canvas. DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*) &ndash; rellena el mapa de bits del lienzo completo con el dibujo especificado.

- [Canvas. DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash; dibuja la forma geométrica especificada utilizando la pintura especificada.

- [Canvas. DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash; dibuja el texto en el lienzo con el color especificado. El texto se dibuja en la `x,y` ubicación.

#### <a name="drawing-with-the-canvas-api"></a>Dibujo con la API de Canvas

Veamos un ejemplo de la API de canvas en acción. En el fragmento de código siguiente se muestra cómo dibujar una vista:

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Este código anterior crea primero una pintura roja y un objeto de pintura verde. Rellena el contenido del lienzo con rojo y, a continuación, indica al lienzo que dibuje un rectángulo verde que sea el 25% del ancho del lienzo. Un ejemplo de esto puede verse en `AnimationsDemo` el proyecto que se incluye con el código fuente de este artículo. Al iniciar la aplicación y seleccionar el elemento de dibujo en el menú principal, deberíamos tener una pantalla similar a la siguiente:

![Pantalla con objetos de pintura roja y de pintura verde](graphics-and-animation-images/image3.png)

## <a name="animation"></a>Animación

Usuarios como cosas que se mueven en sus aplicaciones. Las animaciones son una excelente manera de mejorar la experiencia del usuario de una aplicación y ayudar a deshacerla. Las mejores animaciones son las que los usuarios no tienen en cuenta porque se sienten naturales. Android proporciona las siguientes tres API para animaciones:

- **Ver animación** &ndash; Esta es la API original. Estas animaciones están asociadas a una vista específica y pueden realizar transformaciones simples en el contenido de la vista. Debido a su simplicidad, esta API sigue siendo útil para cosas como animaciones alfa, giros, etc.

- **Animación de propiedades** &ndash; Las animaciones de propiedades se introdujeron en Android 3,0. Permiten a una aplicación animar casi todo. Las animaciones de propiedad se pueden utilizar para cambiar cualquier propiedad de cualquier objeto, aunque ese objeto no esté visible en la pantalla.

- **Animación dibujable** &ndash; Este es un recurso dibujado especial que se usa para aplicar un efecto de animación muy simple a los diseños.

En general, la animación de propiedades es el sistema preferido que se va a usar, ya que es más flexible y ofrece más características.

### <a name="view-animations"></a>Ver animaciones

Las animaciones de vista se limitan a las vistas y solo pueden realizar animaciones en valores como los puntos de inicio y final, el tamaño, la rotación y la transparencia.
Estos tipos de animaciones se conocen normalmente como *animaciones de interpolación*. Las animaciones de vista se pueden definir &ndash; de dos maneras mediante programación en el código o mediante archivos XML. Los archivos XML son la manera preferida de declarar animaciones de vista, ya que son más legibles y fáciles de mantener.

Los archivos XML de animación se almacenarán en `/Resources/anim` el directorio de un proyecto de Xamarin. Android. Este archivo debe tener uno de los siguientes elementos como elemento raíz:

- `alpha`&ndash; Una animación de fundido de salida o de fundido.

- `rotate`&ndash; Animación de giro.

- `scale`&ndash; Animación de cambio de tamaño.

- `translate`&ndash; Movimiento horizontal y/o vertical.

- `set`&ndash; Contenedor que puede contener uno o varios elementos de animación.

De forma predeterminada, todas las animaciones de un archivo XML se aplicarán simultáneamente. Para que las animaciones se realicen de forma `android:startOffset` secuencial, establezca el atributo en uno de los elementos definidos anteriormente.

Es posible afectar a la tasa de cambio en una animación mediante el uso de un *interpolador*. Un interpolador permite acelerar, repetir o desacelerar los efectos de animación. El marco de trabajo de Android proporciona varios interpoladores listos para el cuadro, como (pero sin limitarse a):

- `AccelerateInterpolator`Estos interpoladores aumentan o reducen la velocidad de cambio de una animación. / `DecelerateInterpolator` &ndash;

- `BounceInterpolator`&ndash; el cambio se rebota al final.

- `LinearInterpolator`&ndash; la tasa de cambios es constante.

El siguiente código XML muestra un ejemplo de un archivo de animación que combina algunos de estos elementos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

Esta animación realizará todas las animaciones simultáneamente. La animación de la primera escala ampliará la imagen horizontalmente y la reducirá verticalmente y, a continuación, la imagen se girará de manera simultánea 45 grados en el sentido contrario a las agujas del reloj y se reducirá en la pantalla.

La animación se puede aplicar mediante programación a una vista si se infla la animación y, a continuación, se aplica a una vista. Android proporciona la clase `Android.Views.Animations.AnimationUtils` auxiliar que impondrá un recurso de animación y devolverá una instancia de. `Android.Views.Animations.Animation` Este objeto se aplica a una vista mediante una `StartAnimation` llamada a y `Animation` pasando el objeto. En el fragmento de código siguiente se muestra un ejemplo de esto:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ahora que tenemos un conocimiento fundamental de cómo funcionan las animaciones de vistas, permite moverse a animaciones de propiedades.

### <a name="property-animations"></a>Animaciones de propiedades

Los animadores de propiedades son una nueva API que se presentó en Android 3,0.
Proporcionan una API más extensible que se puede usar para animar cualquier propiedad en cualquier objeto.

Todas las animaciones de propiedades las crean las instancias de la subclase de [animación](xref:Android.Animation.Animator) . Las aplicaciones no usan directamente esta clase, sino que usan una de sus subclases:

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; Esta clase es la clase más importante en toda la API de animación de propiedades. Calcula los valores de las propiedades que se deben cambiar. `ViewAnimator` No actualiza directamente esos valores; en su lugar, genera eventos que se pueden usar para actualizar objetos animados.

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) Esta clase es una subclase de `ValueAnimator`. &ndash; Está diseñado para simplificar el proceso de animación de objetos aceptando un objeto de destino y una propiedad para actualizar.

- [AnimationSet](xref:Android.Animation.AnimatorSet) &ndash; Esta clase es responsable de orquestar cómo se ejecutan las animaciones en relación con otras. Las animaciones pueden ejecutarse simultáneamente, secuencialmente o con un retraso especificado entre ellas.

Los *evaluadores* son clases especiales que los animadores usan para calcular los nuevos valores durante una animación. En la caja, Android proporciona los siguientes evaluadores:

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; Calcula los valores de las propiedades de entero.

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; Calcula los valores de las propiedades float.

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; Calcula los valores de las propiedades de color.

Si la propiedad que se está animando no es `float`un `int` , o el color, las aplicaciones pueden crear su propio evaluador implementando la `ITypeEvaluator` interfaz. (La implementación de evaluadores personalizados queda fuera del ámbito de este tema).

#### <a name="using-the-valueanimator"></a>Usar ValueAnimator

Hay dos partes en cualquier animación: calcular valores animados y después establecer esos valores en las propiedades de algún objeto. 
[ValueAnimator](xref:Android.Animation.ValueAnimator) solo calculará los valores, pero no funcionará directamente en objetos. En su lugar, los objetos se actualizarán dentro de los controladores de eventos que se invocarán durante la duración de la animación. Este diseño permite actualizar varias propiedades de un valor animado.

Puede obtener una instancia de `ValueAnimator` mediante una llamada a uno de los siguientes métodos de fábrica:

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

Una vez hecho esto, la `ValueAnimator` instancia debe tener su conjunto de duración y, a continuación, se puede iniciar. En el ejemplo siguiente se muestra cómo animar un valor de 0 a 1 en el intervalo de 1000 milisegundos:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Pero, el fragmento de código anterior no es muy útil &ndash; . el animador se ejecutará, pero no hay ningún destino para el valor actualizado. La `Animator` clase generará el evento de actualización cuando decida que es necesario informar a los agentes de escucha de un nuevo valor. Las aplicaciones pueden proporcionar un controlador de eventos para responder a este evento, tal y como se muestra en el siguiente fragmento de código:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Ahora que tenemos una descripción de `ValueAnimator`, le permite obtener más información `ObjectAnimator`sobre.

#### <a name="using-the-objectanimator"></a>Usar ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) es una subclase de `ViewAnimator` que combina el motor de control de tiempo y el `ValueAnimator` cálculo de valores de con la lógica necesaria para conectar controladores de eventos. El `ValueAnimator` requiere que las aplicaciones conecten explícitamente un &ndash; controlador `ObjectAnimator` de eventos se encargará de este paso.

La API de `ObjectAnimator` es muy similar a la API de `ViewAnimator`, pero requiere que proporcione el objeto y el nombre de la propiedad que se va a actualizar. En el ejemplo siguiente se muestra un ejemplo `ObjectAnimator`del uso de:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como puede ver en el fragmento de código anterior, `ObjectAnimator` puede reducir y simplificar el código necesario para animar un objeto.

### <a name="drawable-animations"></a>Animaciones Dibujables

La API de animación final es la API de animación dibujable. Las animaciones Dibujables cargan una serie de recursos que se puedan dibujar uno tras otro y los muestran secuencialmente, de forma similar a un dibujo animado.

Los recursos Dibujables se definen en un archivo XML que tiene `<animation-list>` un elemento como el elemento raíz y una serie `<item>` de elementos que definen cada fotograma de la animación. Este archivo XML se almacena en la `/Resource/drawable` carpeta de la aplicación. El siguiente código XML es un ejemplo de una animación dibujable:

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

Esta animación se ejecutará a través de seis fotogramas. El `android:duration` atributo declara cuánto tiempo se mostrará cada fotograma. El siguiente fragmento de código muestra un ejemplo de cómo crear una animación dibujable e iniciarla cuando el usuario hace clic en un botón de la pantalla:

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

En este momento, hemos tratado las bases de las API de animación disponibles en una aplicación de Android.

## <a name="summary"></a>Resumen

En este artículo se presentaron muchos nuevos conceptos y API para ayudar a agregar algunos gráficos a una aplicación de Android. En primer lugar, se han descrito las distintas API de gráficos 2D y se ha mostrado cómo Android permite a las aplicaciones dibujar directamente en la pantalla mediante un objeto Canvas. También vimos algunas técnicas alternativas que permiten la creación declarativa de gráficos mediante archivos XML. En este caso, nos centramos en describir las API antiguas y nuevas para crear animaciones en Android.

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de animaciones (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/animationdemo)
- [Animación y gráficos](https://developer.android.com/guide/topics/graphics/index.html)
- [Uso de animaciones para que la Mobile Apps sea vida](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [Canvas](xref:Android.Graphics.Canvas)
- [Animador de objetos](xref:Android.Animation.ObjectAnimator)
- [Animación de valores](xref:Android.Animation.ValueAnimator)
