---
title: ¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026925"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Pasos de ejemplo para Xamarin.Android.support.v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Descargue el paquete NuGet de Xamarin.Android.Support que desee (por ejemplo, mediante su instalación con el administrador de paquetes NuGet).

Use `ildasm` para comprobar la versión de **android_m2repository.zip** que el paquete NuGet necesita:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Salida del ejemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Descargue **android\_m2repository.zip** de Google con la dirección URL devuelta desde **ildasm**. Como alternativa, puede comprobar qué versión de _Android Support Repository_ tiene instalada actualmente en Administrador de Android SDK:

!["Administrador de Android SDK que muestra Android Support Repository versión 32 instalado"](install-android-support-library-images/sdk-extras.png)

Si la versión coincide con la que necesita para el paquete NuGet, no tendrá que descargar nada. En su lugar, puede volver a comprimir el directorio **m2repository** existente que se encuentra en **extras\\android** en _SDK Path_ (Ruta de acceso de SDK) (como se muestra en la parte superior de la ventana Administrador de Android SDK).

Calcule el hash MD5 de la dirección URL devuelta desde **ildasm**. Formatee la cadena resultante para que todas las letras estén en mayúsculas y no haya ningún espacio. Por ejemplo, ajuste la variable `$url` según sea necesario y, a continuación, ejecute las 2 líneas siguientes (basadas en [el código original C# de Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) en PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Salida del ejemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **android\_m2repository.zip** en la carpeta **%LOCALAPPDATA%\\Xamarin\\zips\\** . Cambie el nombre del archivo para que use el hash MD5 del paso anterior de cálculo del hash MD5. Por ejemplo:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descomprima el archivo en **%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\content\\** (creación de un subdirectorio **content\\m2repository**). Si omite este paso, la primera compilación que use la biblioteca tardará un poco más, ya que deberá completar este paso.
El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente la misma que la versión del paquete NuGet. Puede utilizar `ildasm` para buscar el número correcto de versión:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Salida del ejemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Descargue el paquete NuGet de Xamarin.Android.Support que desee (por ejemplo, mediante su instalación con el administrador de paquetes NuGet).

Haga doble clic en el ensamblado _Xamarin.Android.Support.v4_ en la sección _Referencias_ del proyecto de Android en Visual Studio para Mac para abrir el ensamblado en el Inspector de ensamblados. Asegúrese de que la lista desplegable _Lenguaje_ esté establecida en _C#_ y seleccione el ensamblado de nivel superior _Xamarin.Android.Support.v4_ en el árbol de navegación del Inspector de ensamblados. Busque la propiedad `SourceUrl` en uno de los atributos `IncludeAndroidResourcesFrom` o `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Descargue **android\_m2repository.zip** de Google con la dirección `SourceUrl` devuelta desde **ildasm**. Como alternativa, puede comprobar qué versión de _Android Support Repository_ tiene instalada actualmente en Administrador de Android SDK:

!["Administrador de Android SDK que muestra Android Support Repository versión 32 instalado"](install-android-support-library-images/sdk-extras.png)

Si la versión coincide con la que necesita para el paquete NuGet, no tendrá que descargar nada. En su lugar, puede volver a comprimir el directorio **m2repository** existente que se encuentra en **extras/android** en _SDK Path_ (Ruta de acceso de SDK) (como se muestra en la parte superior de la ventana Administrador de Android SDK).

Calcule el hash MD5 de la dirección URL devuelta desde **ildasm**. Formatee la cadena resultante para que todas las letras estén en mayúsculas y no haya ningún espacio. Por ejemplo, ajuste la cadena de dirección URL según sea necesario y, a continuación, ejecute el siguiente comando en un símbolo del sistema de **Terminal.app**:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Otra opción consiste en usar el intérprete de `csharp` para ejecutar [el mismo código C# que usa Xamarin.Android en sí](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para hacerlo, ajuste la variable `url` según sea necesario y, a continuación, ejecute el siguiente comando en un símbolo del sistema de **Terminal.app**:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Salida del ejemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **android\_m2repository.zip** en la carpeta **$HOME/.local/share/Xamarin/zips/** . Cambie el nombre del archivo para que use el hash MD5 del paso anterior de cálculo del hash MD5. Por ejemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Opcional) Descomprima el archivo en: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creando un subdirectorio **content/m2repository**). Si omite este paso, la primera compilación que use la biblioteca tardará un poco más, ya que deberá completar este paso.

El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente la misma que la versión del paquete NuGet. Como en el paso **ildasm** anterior, puede utilizar el Inspector de ensamblados en Visual Studio para Mac para buscar el número correcto de versión. Busque la propiedad `Version` en uno de los atributos `IncludeAndroidResourcesFrom` o `JavaLibraryReference`:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Referencias adicionales

- [Error 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245): mensajes de error "Error de descarga. Descargue {0} y colóquelo en el directorio {1}" e "Instale el paquete: "{0}" disponible en el instalador del SDK" incorrectos relacionados con paquetes de Xamarin.Android.Support

### <a name="next-steps"></a>Pasos siguientes

En este documento se describe el comportamiento actual a partir de agosto de 2016. La técnica descrita en este documento no forma parte del conjunto de pruebas estable para Xamarin, por lo que podría interrumpirse en el futuro.

Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema persiste incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo presentar un nuevo error si es necesario.
