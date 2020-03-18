---
title: Uso de bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "75489172"
---
# <a name="using-native-libraries"></a>Uso de bibliotecas nativas

Xamarin.Android admite el uso de bibliotecas nativas mediante el mecanismo estándar de PInvoke. También puede agrupar bibliotecas nativas adicionales que no forman parte del sistema operativo en su .apk.

Para implementar una biblioteca nativa con una aplicación de Xamarin.Android, agregue al proyecto el archivo binario de la biblioteca y establezca su **acción de compilación** en **AndroidNativeLibrary**.

Para implementar una biblioteca nativa con un proyecto de la biblioteca de Xamarin.Android, agregue al proyecto el archivo binario de la biblioteca y establezca su **acción de compilación** en **EmbeddedNativeLibrary**.

Observe que puesto que Android admite varias interfaces binarias de aplicación (ABI), Xamarin.Android debe saber para qué ABI está compilada la biblioteca nativa.
Hay dos maneras de hacerlo:

1. Examinar la ruta de acceso
1. Mediante un elemento `AndroidNativeLibrary/Abi` dentro del archivo de proyecto

Con el examen de la ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI a la que se dirige la biblioteca. Por lo tanto, si agrega `lib/armeabi/libfoo.so` al proyecto, la ABI se examina como `armeabi`.

Como alternativa, puede editar el archivo de proyecto para especificar explícitamente la ABI que se va a usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para más información sobre el uso de bibliotecas nativas, consulte [Interoperabilidad con bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depuración de código nativo con Visual Studio

Si usa *Visual Studio 2019* o *Visual Studio 2017*, no tiene que modificar los archivos de proyecto, como se ha descrito anteriormente.
Puede compilar y depurar C++ dentro de la solución de Xamarin.Android agregando una referencia de proyecto a un proyecto de **biblioteca dinámica compartida (Android)** de C++.

Para depurar código nativo de C++ en el proyecto, siga estos pasos:

1. Haga doble clic en la opción **Propiedades** del proyecto y seleccione la página **Android Options** (Opciones de Android).
2. Desplácese hacia abajo hasta **Opciones de depuración**.
3. En el menú desplegable del **Depurador**, seleccione **C++** (en lugar del predeterminado **.NET [Xamarin]** ).

Para más información, los desarrolladores de C++ para Visual Studio pueden ver el ejemplo [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) para intentar depurar C++ desde Visual Studio 2019 o Visual Studio 2017 con Xamarin y consultar nuestra [entrada de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/).

## <a name="related-links"></a>Vínculos relacionados

- [SanAngeles_NativeDebug (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Desarrollo de aplicaciones nativas de Xamarin Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
