---
title: Devoluciones de llamada en Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 982eecea12a4e967bc0c05480ae9099cea10b4cb
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120046"
---
# <a name="callbacks-on-android"></a>Devoluciones de llamada en Android

Llamar a Java desde C# es algo una *empresa arriesgada*. Es decir, hay un *patrón* para las devoluciones de C# llamada de a Java; sin embargo, es más complicado de lo que nos gustaría.

Trataremos las tres opciones para realizar devoluciones de llamada que son más adecuadas para Java:

- Clases abstractas
- Interfaces
- Métodos virtuales

## <a name="abstract-classes"></a>Clases abstractas

Esta es la ruta más sencilla para las devoluciones de llamada, `abstract` por lo que recomiendo usar si solo está intentando obtener una devolución de llamada que funciona en la forma más sencilla.

Comencemos con una C# clase que nos gustaría implementar con Java:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Estos son los detalles para hacer este trabajo:

- `[Register]`genera un nombre de paquete agradable en Java; obtendrá un nombre de paquete generado automáticamente sin él.
- Subclase `Java.Lang.Object` de señales para la inserción de .net para ejecutar la clase mediante el generador de Java de Xamarin. Android.
- Constructor vacío: es lo que se desea usar desde el código de Java.
- `(IntPtr, JniHandleOwnership)`constructor: es lo que utilizará Xamarin. Android para crear C#el equivalente de objetos de Java.
- `[Export]`indica a Xamarin. Android que exponga el método a Java. También podemos cambiar el nombre del método, ya que el mundo de Java me gusta usar los métodos en minúsculas.

A continuación, vamos a C# crear un método para probar el escenario:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```

`JavaCallbacks`podría ser cualquier clase para probar esto, siempre y cuando sea un `Java.Lang.Object`.

Ahora, ejecute la inserción de .NET en el ensamblado .NET para generar un AAR. Consulte la [Guía de introducción](~/tools/dotnet-embedding/get-started/java/android.md) para obtener más información.

Después de importar el archivo AAR en Android Studio, vamos a escribir una prueba unitaria:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```

Así:

- `AbstractClass` Implementación de en Java con un tipo anónimo
- Se ha asegurado de que `"Java"` la instancia se devuelve de Java
- Se ha asegurado de que `"Java"` la instancia se devuelve deC#
- Agregado `throws Throwable`, puesto C# que los constructores están marcados actualmente con`throws`

Si ejecutamos esta prueba unitaria tal cual, se produciría un error como el siguiente:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Lo que falta aquí es un `Invoker` tipo. Se trata de una subclase `AbstractClass` de que C# reenvía llamadas a Java. Si un objeto Java entra en C# el mundo y el C# tipo equivalente es abstracto, Xamarin. Android busca automáticamente un C# tipo con el sufijo `Invoker` para usarlo en C# el código.

Xamarin. Android usa este `Invoker` patrón para los proyectos de enlace de Java entre otras cosas.

Esta es nuestra implementación de `AbstractClassInvoker`:

```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Aquí hay un poco que va aquí:

- Se ha agregado una clase con `Invoker` el sufijo que subclases`AbstractClass`
- Se `class_ref` agregó para contener la referencia de JNI a la clase de Java que subclase la C# clase.
- Agregado `id_gettext` para contener la referencia de JNI al método `getText` Java
- Incluye un `(IntPtr, JniHandleOwnership)` constructor
- `ThresholdType` Implementado `ThresholdClass` y como requisito de Xamarin. Android para conocer los detalles del`Invoker`
- `GetText`necesario para buscar el método `getText` Java con la firma de JNI adecuada y llamarlo
- `Dispose`solo se necesita para borrar la referencia a`class_ref`

Después de agregar esta clase y generar un nuevo AAR, se supera nuestra prueba unitaria. Como puede ver, este patrón para las devoluciones de llamada no es lo *ideal*, pero factible.

Para más información sobre la interoperabilidad de Java, consulte la impresionante [documentación de Xamarin. Android](~/android/platform/java-integration/working-with-jni.md) sobre este tema.

## <a name="interfaces"></a>Interfaces

Las interfaces son prácticamente las mismas que las clases abstractas, a excepción de un detalle: Xamarin. Android no genera Java para ellos. Esto se debe a que antes de la inserción de .NET, no hay muchos escenarios en C# los que Java implementaría una interfaz.

Supongamos que tenemos la siguiente C# interfaz:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject`indica a la inserción de .net que se trata de una interfaz de Xamarin. Android, pero en caso contrario, `abstract` es exactamente igual que una clase.

Como Xamarin. Android no generará actualmente el código Java para esta interfaz, agregue el siguiente Java al C# proyecto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Puede colocar el archivo en cualquier parte, pero asegúrese de establecer su acción de compilación `AndroidJavaSource`en. Esto indicará la incrustación de .NET para copiarlo en el directorio adecuado para compilarlo en el archivo AAR.

A continuación, `Invoker` la implementación será bastante similar:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Después de generar un archivo AAR, en Android Studio podríamos escribir la siguiente prueba unitaria de paso:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Métodos virtuales

La `virtual` invalidación de en Java es posible, pero no es una excelente experiencia.

Supongamos que tiene la siguiente C# clase:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Si siguió el ejemplo `abstract` anterior de la clase, funcionaría excepto por un detalle: _Xamarin. Android no buscará `Invoker`el_ .

Para corregir esto, modifique la C# clase para que `abstract`sea:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Esto no es lo ideal, pero obtiene este escenario en funcionamiento. Xamarin. Android recogerá `VirtualClassInvoker` y Java podrá usar `@Override` en el método.

## <a name="callbacks-in-the-future"></a>Devoluciones de llamada en el futuro

Hay un par de cosas que podríamos mejorar estos escenarios:

1. `throws Throwable`en C# los constructores se fija en este [PR](https://github.com/xamarin/java.interop/pull/170).
1. Cree el generador de Java en las interfaces de soporte técnico de Xamarin. Android.
    - Esto elimina la necesidad de agregar el archivo de código fuente de Java con `AndroidJavaSource`una acción de compilación de.
1. Haga que Xamarin. Android cargue un `Invoker` para las clases virtuales.
    - Esto elimina la necesidad de marcar la clase en nuestro `virtual` ejemplo `abstract`.
1. Generar `Invoker` clases para la inserción automática de .net
    - Esto va a ser complicado, pero factible. Xamarin. Android ya está haciendo algo similar a esto para los proyectos de enlace de Java.

Hay mucho trabajo que realizar aquí, pero estas mejoras en la inserción de .NET son posibles.

## <a name="further-reading"></a>Información adicional

- [Introducción en Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Investigación preliminar de Android](~/tools/dotnet-embedding/android/index.md)
- [Limitaciones de la inserción de .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuir al proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
