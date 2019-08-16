---
title: Uso de bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: fa0a3a75a4cc2cfd04b607f17206faa822af0474
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523643"
---
# <a name="using-native-libraries"></a>Uso de bibliotecas nativas

Xamarin. Android admite el uso de bibliotecas nativas mediante el mecanismo estándar de PInvoke. También puede agrupar bibliotecas nativas adicionales que no forman parte del sistema operativo en su. apk.

Para implementar una biblioteca nativa con una aplicación de Xamarin. Android, agregue el archivo binario de la biblioteca al proyecto y establezca su **acción de compilación** en **AndroidNativeLibrary**.

Para implementar una biblioteca nativa con un proyecto de biblioteca de Xamarin. Android, agregue el archivo binario de biblioteca al proyecto y establezca su **acción de compilación** en **EmbeddedNativeLibrary**.

Tenga en cuenta que, como Android admite varias interfaces binarias de aplicación (ABI), Xamarin. Android debe saber para qué ABI se ha creado la biblioteca nativa.
Hay dos maneras de hacerlo:

1. Ruta de acceso "sniffing"
1. Mediante el uso `AndroidNativeLibrary/Abi` de un elemento dentro del archivo de proyecto


Con el examen de la ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI a la que se dirige la biblioteca. Por lo tanto, si `lib/armeabi/libfoo.so` agrega al proyecto, la ABI se "examinará" como. `armeabi`

Como alternativa, puede editar el archivo de proyecto para especificar explícitamente la ABI que se va a usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obtener más información sobre el uso de bibliotecas nativas, consulte interoperabilidad [con bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depurar código nativo con Visual Studio

Si usa *visual studio 2019* o *Visual Studio 2017*, no tiene que modificar los archivos de proyecto tal y como se ha descrito anteriormente.
Puede compilar y C++ depurar dentro de la solución de Xamarin. Android agregando C++ una referencia de proyecto a un proyecto de **biblioteca dinámica compartida (Android)** .

Para depurar código nativo C++ en el proyecto, siga estos pasos:

1. Haga doble clic en **propiedades** del proyecto y seleccione la página **Opciones de Android** .
2. Desplácese hacia abajo hasta **Opciones**de depuración.
3. En el menú desplegable del depurador, seleccione **C++** (en lugar de **.net predeterminado (Xamarin)** ).

Los desarrolladores C++ C++ de Visual Studio pueden ver el ejemplo [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) para probar la depuración desde visual Studio 2019 o Visual Studio 2017 con Xamarin. y consulte nuestra [entrada de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obtener más información.



## <a name="related-links"></a>Vínculos relacionados

- [SanAngeles_NativeDebug (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Desarrollo de aplicaciones nativas de Xamarin Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
