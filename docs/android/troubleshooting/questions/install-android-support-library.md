---
title: ¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 107fcd39a64ef1d7758d211ab47a07e1ece52f4e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757237"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>¿Cómo instalar manualmente las bibliotecas de compatibilidad de Android que requieren los paquetes de Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Pasos de ejemplo para Xamarin. Android. support. V4 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Descargue el paquete de NuGet de Xamarin. Android. support que desee (por ejemplo, mediante su instalación con el administrador de paquetes NuGet).

Use `ildasm` para comprobar qué versión de **android_m2repository. zip** necesita el paquete NuGet:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Salida del ejemplo:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Descargue **Android\_m2repository. zip** de Google con la dirección URL devuelta por **Ildasm**. Como alternativa, puede comprobar qué versión del repositorio de _compatibilidad con Android_ está instalado actualmente en el administrador de Android SDK:

!["Administrador de Android SDK que muestra el repositorio de compatibilidad con Android versión 32 instalada"](install-android-support-library-images/sdk-extras.png)

Si la versión coincide con la que necesita para el paquete NuGet, no tendrá que descargar nada nuevo. En su lugar, puede volver a comprimir el directorio **m2repository** existente que se encuentra **en\\extras Android** en la _ruta de acceso del SDK_ (como se muestra en la parte superior de la ventana Administrador de Android SDK).

Calcule el hash MD5 de la dirección URL devuelta desde **Ildasm**. Dé formato a la cadena resultante para que use todas las letras mayúsculas y ningún espacio. Por ejemplo, ajuste la `$url` variable según sea necesario y, a continuación, ejecute las 2 líneas siguientes (según [el código original C# de Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) en PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Salida del ejemplo:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **Android\_m2repository. zip** en la carpeta **\\\\% LOCALAPPDATA% Xamarinmozip.\\** Cambie el nombre del archivo para que use el hash MD5 del paso anterior de cálculo del hash MD5. Por ejemplo:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

Opta Descomprima el archivo en **\\el contenido\\\\\\de\\ % LOCALAPPDATA% Xamarin Xamarin. Android. support. V4 23.4.0.0** (creación de un **m2repository de contenido\\** ) subdirectorio). Si omite este paso, la primera compilación que use la biblioteca tardará un poco más, ya que será necesario completar este paso.
El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente igual que la versión del paquete NuGet. Puede usar `ildasm` para buscar el número de versión correcto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Salida del ejemplo:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Descargue el paquete de NuGet de Xamarin. Android. support que desee (por ejemplo, mediante su instalación con el administrador de paquetes NuGet).

Haga doble clic en el ensamblado _Xamarin. Android. support. V4_ en la sección _referencias_ del proyecto de Android en Visual Studio para Mac para abrir el ensamblado en el explorador de ensamblados. Asegúrese de que la lista desplegable _idioma_ está establecida _C#_ en y seleccione el ensamblado de nivel superior _Xamarin. Android. support. V4_ en el árbol de navegación del explorador de ensamblados. Busque la `SourceUrl` propiedad en uno de los `IncludeAndroidResourcesFrom` atributos `JavaLibraryReference` o:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Descargue **Android\_m2repository. zip** de Google con el `SourceUrl` devuelto de **Ildasm**. Como alternativa, puede comprobar qué versión del repositorio de _compatibilidad con Android_ está instalado actualmente en el administrador de Android SDK:

!["Administrador de Android SDK que muestra el repositorio de compatibilidad con Android versión 32 instalada"](install-android-support-library-images/sdk-extras.png)

Si la versión coincide con la que necesita para el paquete NuGet, no tendrá que descargar nada nuevo. En su lugar, puede volver a comprimir el directorio **m2repository** existente que se encuentra en **extras/Android** en la ruta de acceso del _SDK_ (como se muestra en la parte superior de la ventana Administrador de Android SDK).

Calcule el hash MD5 de la dirección URL devuelta desde **Ildasm**. Dé formato a la cadena resultante para que use todas las letras mayúsculas y ningún espacio. Por ejemplo, ajuste la cadena de dirección URL según sea necesario y, a continuación, ejecute el siguiente comando en un símbolo del sistema de **terminal. app** :

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Otra opción consiste en usar el `csharp` intérprete para ejecutar [el mismo C# código que usa Xamarin. Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
Para ello, ajuste la `url` variable según sea necesario y, a continuación, ejecute el siguiente comando en un símbolo del sistema de **terminal. app** :

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Salida del ejemplo:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copie **Android\_m2repository. zip** en la carpeta **$Home/.local/share/Xamarin/Zips/** Cambie el nombre del archivo para que use el hash MD5 del paso anterior de cálculo del hash MD5. Por ejemplo:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

Opta Descomprima el archivo en: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(crear un subdirectorio **Content/m2repository** ). Si omite este paso, la primera compilación que use la biblioteca tardará un poco más, ya que será necesario completar este paso.

El número de versión del subdirectorio (**23.4.0.0** en este ejemplo) no es exactamente igual que la versión del paquete NuGet. Como en el paso **Ildasm** anterior, puede utilizar el explorador de ensamblados en Visual Studio para Mac para buscar el número de versión correcto. Busque la `Version` propiedad en uno de los `IncludeAndroidResourcesFrom` atributos o `JavaLibraryReference` :

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Referencias adicionales

- [Error 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) : no se pudo descargar "inexacto". Descargue {0} y colóquelo en el {1} directorio ". y "Instale el paquete: '{0}' disponible en el instalador del SDK" mensajes de error relacionados con Xamarin. Android. support packages

### <a name="next-steps"></a>Pasos siguientes

En este documento se describe el comportamiento actual a partir del 2016 de agosto. La técnica descrita en este documento no forma parte del conjunto de pruebas estable para Xamarin, por lo que podría interrumpirse en el futuro.

Para obtener más ayuda, para ponerse en contacto con nosotros o si este problema permanece incluso después de usar la información anterior, consulte [¿Qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias y cómo archivar un nuevo error si es necesario .
