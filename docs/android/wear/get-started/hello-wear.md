---
title: Hola, Wear
description: Cree su primera aplicación de desgaste de Android y ejecútela en un dispositivo o emulador de desgaste. En este tutorial se proporcionan instrucciones paso a paso para crear un pequeño proyecto de desgaste de Android que controla los clics de los botones y muestra un contador de clics en el dispositivo de desgaste. Explica cómo depurar la aplicación mediante un emulador de desgaste o un dispositivo de desgaste que está conectado a través de Bluetooth a un teléfono Android. También proporciona un conjunto de sugerencias de depuración para el desgaste de Android.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/10/2018
ms.openlocfilehash: 0fe08856c70d8a23fb768a5b94b933addd42714b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028665"
---
# <a name="hello-wear"></a>Hola, Wear

_Cree su primera aplicación de desgaste de Android y ejecútela en un dispositivo o emulador de desgaste. En este tutorial se proporcionan instrucciones paso a paso para crear un pequeño proyecto de desgaste de Android que controla los clics de los botones y muestra un contador de clics en el dispositivo de desgaste. Explica cómo depurar la aplicación mediante un emulador de desgaste o un dispositivo de desgaste que está conectado a través de Bluetooth a un teléfono Android. También proporciona un conjunto de sugerencias de depuración para el desgaste de Android._

![Captura de pantalla de la aplicación de desgaste que se va a completar en este tutorial](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Su primera aplicación de desgaste

Siga estos pasos para crear su primera aplicación de desgaste de Xamarin. Android:

### <a name="1-create-a-new-android-project"></a>1. crear un nuevo proyecto de Android

Cree una nueva **aplicación de desgaste de Android**:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![creación de una nueva aplicación de desgaste de Android en el cuadro de diálogo nuevo proyecto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![creación de una nueva aplicación de desgaste de Android en el cuadro de diálogo Nueva solución](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----

Esta plantilla incluye automáticamente el NuGet (y las dependencias) de **Xamarin Android portátil Library** , por lo que tendrá acceso a widgets específicos de desgaste. Si no ve la plantilla de desgaste, revise la guía de [instalación y configuración](~/android/wear/get-started/installation.md) para comprobar que ha instalado un Android SDK compatible. 

### <a name="2-choose-the-correct-target-framework"></a>2. Elija la versión de **.NET Framework de destino** correcta

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Asegúrese de que **Android to target mínimo** esté establecido en **Android 5,0 (Lollipop)** o posterior: 

[![establecer la plataforma de destino en Android 5,0 en Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Asegúrese de que el marco de trabajo de destino esté establecido en **Android 5,0 (Lollipop)** o posterior:

[![establecer la versión de .NET Framework de destino en Android 5,0 en Visual Studio para Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Para obtener más información sobre cómo establecer la plataforma de destino, consulte Descripción de los niveles de la [API de Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="3-edit-the-mainaxml-layout"></a>3. editar el diseño **Main. axml**

Configure el diseño para que contenga un `TextView` y un `Button` para el ejemplo: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. editar el origen de **MainActivity.CS**

Agregue el código para incrementar un contador y mostrarlo cada vez que se haga clic en el botón: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. configurar un emulador o un dispositivo

El siguiente paso consiste en configurar un emulador o un dispositivo para implementar y ejecutar la aplicación. Si aún no está familiarizado con el proceso de implementación y ejecución de aplicaciones de Xamarin. Android en general, consulte la guía de [Inicio rápido de Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

Si no tiene un dispositivo de desgaste de Android, como un desgaste de SmartWatch de Android, puede ejecutar la aplicación en un emulador. Para obtener información sobre cómo depurar aplicaciones de desgaste en un emulador, consulte [depurar el desgaste de Android en un emulador](~/android/wear/deploy-test/debug-on-emulator.md).

Si tiene un dispositivo de desgaste de Android, como un desgaste de SmartWatch de Android, puede ejecutar la aplicación en el dispositivo en lugar de usar un emulador. Para obtener más información sobre la depuración en un dispositivo de desgaste, consulte [depurar en un dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).

### <a name="6-run-the-android-wear-app"></a>6. ejecutar la aplicación de desgaste de Android

El dispositivo de desgaste de Android debe aparecer en el menú desplegable del dispositivo. Asegúrese de elegir el dispositivo de desgaste de Android correcto o AVD antes de iniciar la depuración. Después de seleccionar el dispositivo, haga clic en el botón reproducir para implementar la aplicación en el emulador o el dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![elección de un menú de uso de AVD en Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![elegir un uso de AVD en Visual Studio para Mac menú del dispositivo](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Es posible que vea un mensaje **solo un minuto...** (o alguna otra pantalla intersticial) al principio: 

![Ver emulador muestra solo un minuto...](hello-wear-images/please-wait.png)

Si usa un emulador de inspección, puede tardar un rato en iniciar la aplicación. Cuando se usa Bluetooth, se tarda más tiempo en implementar la aplicación que en USB. (Por ejemplo, tarda unos 5 minutos en implementar esta aplicación en una inspección de LG G que está conectada a un teléfono de Nexus 5).

Una vez que la aplicación se implemente correctamente, la pantalla del dispositivo de desgaste debería mostrar una pantalla similar a la siguiente:

[![pantalla inicial de la aplicación de desgaste](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Pulse **en** la situado en la parte frontal del dispositivo de desgaste y ver el incremento de recuento con cada TAP:

[![captura de pantalla de la aplicación de desgaste tras 3 clics](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

Consulte los [ejemplos de desgaste](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) , incluidas las aplicaciones de desgaste de Android con aplicaciones de teléfono complementarias.

Cuando esté listo para distribuir la aplicación, consulte [trabajar con empaquetado](~/android/wear/deploy-test/packaging.md).

## <a name="related-links"></a>Vínculos relacionados

- [Haga clic en mi aplicación (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-weartest)
