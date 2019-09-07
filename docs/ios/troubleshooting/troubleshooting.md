---
title: Sugerencias para la solución de problemas de Xamarin. iOS
description: En este documento se proporcionan varias sugerencias útiles para la solución de problemas durante el desarrollo de aplicaciones de Xamarin. iOS. Describe los mensajes de error específicos, así como otros posibles problemas.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: e6a1b6f4d35a6b8774901ed5a505b5333511c848
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769705"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Sugerencias para la solución de problemas de Xamarin. iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin. iOS no puede resolver System. ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15,1 o anterior) solo es compatible con el **sistema. ValueTuple NuGet 4.3.0** (o anterior).

- **Visual Studio 2017 Update 2** (la versión 15,2 o posterior) solo es compatible con el **sistema. ValueTuple NuGet 4.3.1** o una versión más reciente.

Elija el NuGet System. ValueTuple correcto que se corresponda con la instalación de Visual Studio 2017.

## <a name="receiving-error-retrieving-update-information-error-message"></a>Recibe el mensaje de error "error al recuperar la información de actualización"

Al intentar actualizar el software y aparece este mensaje de error, pruebe a reiniciar el IDE y cierre la sesión y vuelva a iniciarla en su cuenta en el IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Cómo crear tomas o acciones con Interface Builder?

Con la introducción del Xamarin Designer para iOS en Visual Studio para Mac y Visual Studio, los desarrolladores de Xamarin. iOS ahora pueden aprovechar la creación de una interfaz de usuario a través de guiones gráficos y. Xib. Consulte las guías [Hello, iOS](~/ios/get-started/hello-ios/index.md) para obtener más información sobre el uso del diseñador.

También puede consultar las guías de [tomas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) y [acciones](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) de Apple para obtener más información sobre el uso de salidas y acciones en IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System. Text. Encoding. GetEncoding produce NotSupportedException

Es posible que esté usando una codificación que no se agrega de forma predeterminada. Consulte la página [internacionalización](~/ios/app-fundamentals/localization/index.md) para obtener información sobre cómo agregar compatibilidad para más codificación.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (todo lo demás)

Es probable que el enlazador haya quitado el miembro y, por tanto, no exista en el ensamblado en tiempo de ejecución.  Hay varias soluciones para ello:

- Agregue el [`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) atributo al miembro.  Esto impedirá que el vinculador lo quite.
- Al invocar a [**Mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), use las opciones **-nolink** o **-linksdkonly** :
  - **-nolink** deshabilita todos los vínculos.
  - **-linksdkonly** solo vinculará los ensamblados proporcionados por Xamarin. iOS, como **Xamarin. iOS. dll**, a la vez que conserva todos los tipos en los ensamblados creados por el usuario (es decir, los proyectos de la aplicación).

Tenga en cuenta que los ensamblados están vinculados para que el ejecutable resultante sea más pequeño; por lo tanto, la deshabilitación de la vinculación puede dar lugar a un ejecutable mayor que el deseado.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Está obteniendo un ModelNotImplementedException

Si recibe esta excepción, significa que está llamando a base. Método () en una clase que invalida un modelo. No es necesario llamar al método base en una clase para los modelos (son clases marcadas con el atributo [Model]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Esta clase no es compatible con la codificación de valores de clave para la clave XXXX

Si recibe este error al cargar un archivo NIB, significa que no se encontró el valor XXXX en la clase administrada. Esto significa que falta una declaración como la siguiente:

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

La definición anterior se genera automáticamente mediante Visual Studio para Mac para los archivos Xib que se agreguen a Visual Studio para Mac en `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` el archivo.

Además, los tipos que contienen el código anterior deben ser una subclase de [NSObject](xref:Foundation.NSObject).  Si el tipo contenedor se encuentra dentro de un espacio de nombres, también debe tener un atributo [[Register]](xref:Foundation.RegisterAttribute) que proporcione un nombre de tipo sin un espacio de nombres (como Interface Builder no admite espacios de nombres en tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Clase no conocida XXXX en Interface Builder archivo

Este error se genera si se define una clase en los archivos de Interface Builder pero no se proporciona la implementación real en el C# código.

Debe agregar código similar al siguiente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException: No se encontró ningún constructor para foo. bar:: ctor (System. IntPtr)

Este error se genera en tiempo de ejecución cuando el código intenta crear una instancia de las clases a las que se hace referencia desde el archivo de Interface Builder. Esto significa que olvidó agregar un constructor que toma un solo IntPtr como parámetro.

El constructor con un identificador IntPtr se utiliza para enlazar objetos administrados con sus representaciones no administradas.

Para corregir esto, agregue la siguiente línea de código a la clase foo. bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>El tipo {foo} no contiene una definición para `GetNativeField` y no se encontró `GetNativeField` ningún método de extensión de tipo {foo}

Si obtiene este error en los archivos generados por el diseñador (*. xib.designer.cs), significa una de estas dos cosas:

 **1) falta una clase parcial o una clase base**

Las clases parciales generadas por el diseñador deben tener las clases parciales correspondientes en el código de usuario que heredan de `UIViewController`alguna subclase de `NSObject`, a menudo. Asegúrese de que tiene una clase de este tipo para el tipo que está ofreciendo el error.

 **2) los espacios de nombres predeterminados han cambiado**

Los archivos del diseñador se generan utilizando la configuración predeterminada del espacio de nombres del proyecto. Si ha cambiado esta configuración o ha cambiado el nombre del proyecto, es posible que las clases parciales generadas ya no estén en el mismo espacio de nombres que sus homólogos de código de usuario.

La configuración del espacio de nombres se puede encontrar en el cuadro de diálogo Opciones del proyecto. El espacio de nombres predeterminado se encuentra en la sección **configuración principal general de >** . Si está en blanco, el nombre del proyecto se utiliza como valor predeterminado. Puede encontrar más opciones de configuración de espacio de nombres en la sección **código fuente > directivas de nomenclatura de .net** .

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>ADVERTENCIA para las acciones: El método privado ' foo ' nunca se usa. (CS0169)

Las acciones de los archivos de Interface Builder están conectadas a los widgets mediante reflexión en tiempo de ejecución, por lo que se espera esta advertencia.

Puede usar "#pragma ADVERTENCIA deshabilitar 0169" "#pragma ADVERTENCIA habilitar 0169" en torno a sus acciones si desea suprimir esta advertencia solo para estos métodos, o agregar 0169 al campo "omitir advertencias" en las opciones del compilador si quiere deshabilitarlo para todo el proyecto (no recomendado).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>error de Mtouch con el siguiente mensaje: No se puede abrir el ensamblado '/Path/to/yourproject.exe '

Si ve este mensaje de error, generalmente el problema es que la ruta de acceso absoluta al proyecto contiene un espacio. Esto se corregirá en una versión futura de Xamarin. iOS, pero puede solucionar el problema moviendo el proyecto a una carpeta sin espacios.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versión de sqlite3 es antigua. actualice al menos v 3.5.0.

Esto sucede cuando se realizan todas las acciones siguientes:

1. Usar mono. Data. SQLite
1. Usar Mac OS X Leopard (10,5)
1. Ejecute la aplicación en el simulador.

El problema es que mono selecciona el sistema operativo X `libsqlite3.dylib`, no el archivo de `libsqlite3.dylib` iPhoneSimulator. La aplicación funcionará en el dispositivo, pero no en el simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>No se puede implementar en el dispositivo con System. Exception: AMDeviceInstallApplication devolvió 3892346901

Este error significa que la configuración de firma de código para el identificador de paquete o certificado no coincide con el perfil de aprovisionamiento instalado en el dispositivo.  Confirme que tiene seleccionado el certificado adecuado en opciones de proyecto: > firma de paquete de iPhone y el ID. de paquete correcto especificado en opciones de proyecto-> aplicación de iPhone.

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>La finalización del código no funciona en Visual Studio para Mac

Asegúrese de que está usando la versión más reciente de Visual Studio para Mac y Xamarin. iOS

Si el problema sigue presente, envíe [un error](http://monodevelop.com/Developers#Reporting_Bugs), adjunte los archivos de **registro ~/Library/Logs/XamarinStudio-{version}/IDE-{timestamp}.log**, **AndroidTools-{timestamp}. log**y **Components-{timestamp}. log** .

Si se produce un error en todo lo demás, puede intentar quitar la memoria caché de finalización de código para que se vuelva a generar:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Tenga cuidado de escribir este comando correctamente o podría quitar accidentalmente archivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio para Mac se bloquea cuando se copia texto

Las populares utilidades de Mac QuickSilver, la barra de herramientas de Google y la barra de herramientas tienen características del portapapeles que dañan la memoria de Visual Studio para Mac. En sus opciones, puede enumerar Visual Studio para Mac como un proceso en el que no deberían interferir.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio para Mac se requiere mono 2,4

Si ha actualizado Visual Studio para Mac debido a una actualización reciente y, al intentar iniciarlo de nuevo, se queja de que no está presente mono 2,4, lo único que debe hacer es [actualizar la instalación de mono 2,4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3 _6 corrige algunos problemas importantes que impidieron que Visual Studio para Mac se ejecutara de forma confiable, a veces se bloqueó Visual Studio para Mac en el inicio o impidió que se generara la base de datos de finalización de código.

Una vez que instale el nuevo mono, Visual Studio para Mac se iniciará según lo previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Aserción en.. /.. /.. /.. /mono/Metadata/Generic-Sharing.c: 704, no se cumple la condición ' OTI '

Si recibe el siguiente seguimiento de la pila:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Significa que está vinculando una biblioteca estática compilada con código Thumb en el proyecto. A partir de la versión 3,1 de iPhone SDK (o superior en el momento de redactar este documento), Apple presentó un error en el enlazador al vincular código no Thumb (Xamarin. iOS) con código Thumb (su biblioteca estática). Tendrá que vincular con una versión no Thumb de la biblioteca estática para mitigar este problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>System.ExecutionEngineException: Intentando compilar de forma JIT (contenedor administrado a administrado) foo []: System. Collections. Generic. ICollection'1. get_Count ()

El sufijo [] indica que usted o la biblioteca de clases están llamando a un método en una matriz a través de una colección genérica, como IEnumerable < >, ICollection < > o IList < >. Como solución alternativa, puede obligar explícitamente al compilador de AOT a incluir este método llamando al método usted mismo y asegurándose de que este código se ejecuta antes de la llamada que desencadenó la excepción. En este caso, puede escribir:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que obligará al compilador de AOT a incluir el método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio para Mac editor de origen es muy lento

A veces, el editor de origen de Visual Studio para Mac se vuelve muy lento y parece que se bloquea durante varios segundos entre caracteres de escritura.

Este problema es muy poco frecuente y extremadamente difícil de reproducir; normalmente no se puede reproducir en el mismo equipo después de reiniciar Visual Studio para Mac. Por este motivo, nos agradecería que pudiera realizar varios pasos de depuración antes de reiniciar Visual Studio para Mac y enviarnos los resultados.

1. Intente cerrar la pestaña Editor y vuelva a abrirla. ¿Se tarda un poco de edición o se mueve el símbolo de intercalación hasta que se produce de nuevo la ralentización?
1. Deshabilite "sincronización de la viga" mediante la herramienta de desarrollo de "Quartz debug" (que puede encontrar con Spotlight) y compruebe si el rendimiento del editor de origen se ha restaurado a normal.
1. Intente repetir el paso (1) con la sincronización de la luz deshabilitada.
1. Si el editor se bloquea durante más de unos segundos, intente ejecutar "killall-QUIT [Visual Studio para Mac]" en un terminal mientras esté bloqueado. Es posible que sea difícil que el comando kill se produzca mientras el editor está bloqueado, pero es esencial hacerlo, porque el comando fuerza a mono a escribir los seguimientos de la pila de todos los subprocesos en el registro MD, que podemos usar para detectar el estado en el que se encuentran los subprocesos mientras el XS está bloqueado.

Adjunte los registros XS, **~/Library/Logs/XamarinStudio-{version}/IDE-{timestamp}.log**, **ANDROIDTOOLS-{timestamp}. log**y **Components-{timestamp}. log** (en versiones anteriores de XS/MonoDevelop, simplemente envíe **~/Library/Logs /MonoDevelop-(3.0 | 2.8 | 2.6)/MonoDevelop.log**).

> [!NOTE]
> El problema anterior se corrigió en XS 2,2 final * *

## <a name="compiled-application-is-very-large"></a>La aplicación compilada es muy grande

Para admitir la depuración, las compilaciones de depuración contienen código adicional. Los proyectos generados en modo de versión son una fracción del tamaño.

A partir de Xamarin. iOS 1,3, las compilaciones de depuración incluían la compatibilidad con la depuración para cada componente único de mono (todos los métodos en cada clase de los marcos de trabajo).  

Con Xamarin. iOS 1,4 se introducirá un método más detallado para la depuración. el valor predeterminado será proporcionar únicamente instrumental de depuración para el código y las bibliotecas, y no hacer esto para todos los [ensamblados mono](~/cross-platform/internals/available-assemblies.md) (esto seguirá siendo posible, pero usted tendrá que optar por depurar esos ensamblados).

## <a name="installation-hangs"></a>La instalación se bloquea

Los instaladores mono y Xamarin. iOS se bloquean si se ejecuta el simulador de iPhone. Este problema no se limita a mono o Xamarin. iOS, es un problema coherente en todo el software que intenta instalar software en el Leopard de nieve de MacOS si el simulador de iPhone se ejecuta durante la instalación.

Asegúrese de salir del simulador de iPhone y volver a intentar la instalación.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Se agotó el trampolines del tipo 0

Si recibe este mensaje mientras se ejecuta el dispositivo, puede crear más tipos de trampolines de tipo 0 (específico del tipo) modificando la sección "creación de iPhone" de las opciones del proyecto.  Quiere agregar argumentos adicionales para los destinos de compilación del dispositivo:

 `-aot "ntrampolines=2048"`

El número predeterminado de trampolines es 1024. Intente aumentar este número hasta que tenga suficiente para la aplicación.

## <a name="ran-out-of-trampolines-of-type-1"></a>Se agotó el trampolines del tipo 1

Si hace un uso intensivo de los genéricos recursivos, puede obtener este mensaje en el dispositivo.  Puede crear más tipos de trampolines de tipo 1 (tipo RGCTX) modificando la sección "creación de iPhone" de las opciones del proyecto.  Quiere agregar argumentos adicionales para los destinos de compilación del dispositivo:

 `-aot "nrgctx-trampolines=2048"`

El número predeterminado de trampolines es 1024. Intente aumentar este número hasta que tenga suficiente para el uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Se agotó el trampolines del tipo 2

Si realiza interfaces de uso intensivo, puede obtener este mensaje en el dispositivo.
Puede crear más tipos de trampolines de tipo 2 (tipo IMT thunks) modificando la sección "creación de iPhone" de las opciones del proyecto.  Quiere agregar argumentos adicionales para los destinos de compilación del dispositivo:

 `-aot "nimt-trampolines=512"`

El número predeterminado de IMT de código thunk trampolines es 128. Intente aumentar este número hasta que tenga suficiente para el uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>El depurador no puede conectar con el dispositivo

Al iniciar la depuración de una configuración de dispositivo, verá que el depurador muestra un cuadro de diálogo que indica que está intentando conectarse a la aplicación. Hay varios motivos por los que es posible que el depurador no pueda conectarse a la aplicación, en función del modo que use para conectarse (USB o Wi-Fi).

 **Si el dispositivo y el host del depurador están en redes diferentes**, es posible que un firewall o una red privada impidan que la aplicación se conecte al host del depurador en modo WiFi.

 **Es posible que Visual Studio para Mac no pueda consultar la dirección IP correcta del host**. En el modo WiFi Visual Studio para Mac proporciona a la aplicación todas las direcciones IP que puede encontrar en el host, y la aplicación las prueba todos para ver si puede utilizar cualquiera de ellas para conectarse a Visual Studio para Mac.

 **Otro dispositivo está conectado a un puerto USB en el host.** En algunos casos, se sabe que otros dispositivos conectados a los puertos USB del host interfieren de alguna manera con la depuración en modo USB.

Si el modo de Wi-Fi o USB no funciona, puede probar con facilidad el otro: en Visual Studio para Mac, abra las preferencias, vaya a la página de depurador de preferencias/depurador/iPhone y active la casilla "depurar dispositivos iOS a través de WiFi en lugar de USB".   Si no funciona, puede ver más información sobre el error en la consola del dispositivo en modo detallado (que se habilita agregando "-v-v-v" a los argumentos Mtouch adicionales en las opciones del proyecto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Error 134: error de Mtouch con el siguiente mensaje:

Este error puede producirse si está intentando compilar con el vínculo-nolink en el estilo de versiones de Xamarin. iOS 1,4. Puede solucionar este error especificando argumentos adicionales en la configuración del proyecto de MonoDevelop.

Agregar el argumento

 `-nosymbolstrip`

y se debe resolver el problema.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>La identidad de distribución no se muestra en Visual Studio para Mac opciones de firma de proyecto

Visual Studio para Mac 2,2 tiene un error que hace que no detecte certificados de distribución que contengan una coma. Actualice a Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Error "AFCFileRefWrite devuelto: 1 "durante la carga

Al cargar una aplicación en el dispositivo, puede recibir el error "AFCFileRefWrite devuelto: 1 ". Esto puede ocurrir si tiene un archivo de longitud cero.

## <a name="error-mtouch-failed-with-no-output"></a>Error "error de Mtouch sin salida"

Se producirá un error en la versión actual de Xamarin. iOS y Visual Studio para Mac cuando el nombre del proyecto o el directorio donde se almacena la solución o el proyecto contengan espacios.
Para corregir este problema:

- Asegúrese de que ni su proyecto o el directorio donde está almacenado contiene un espacio.
- En el proyecto "configuración principal", asegúrese de que el nombre del proyecto no contenga espacios.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Error "el archivo binario cargado no era válido. Se usó una versión beta preliminar del SDK para compilar la aplicación "

Este error se debe normalmente a un proyecto que se inició en el desarrollo de iPad antes de que se publicara Xamarin. iOS 2.0.0, probablemente tenga algunas claves en el archivo info. plist como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Este par de KEYPAIR debe quitarse como Visual Studio para Mac lo controla automáticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Error "se usó una versión beta preliminar del SDK para compilar la aplicación"

(Aportada por Ed Anuff)

Siga estos pasos:

- Cambie la versión del SDK en iPhone Build a 3,2 o iTunes Connect lo rechazará en la carga porque está viendo una aplicación compatible con iPad compilada con una versión de SDK inferior a 3,2
- Cree un archivo info. plist personalizado para el proyecto y establezca explícitamente MinimumOSVersion en 3,0.   Esto invalidará el valor de MinimumOSVersion 3,2 establecido por Xamarin. iOS.   Si no lo hace, la aplicación no se podrá ejecutar en un iPhone.
- Vuelva a compilar, comprimir y cargar en iTunes Connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Excepción no controlada: System.Exception: No se pudo encontrar el selector someSelector: en {Type}

Esta excepción se debe a una de estas tres cosas:

1. Ha proporcionado un selector al tiempo de ejecución de Objective-C sin aplicar el atributo [Export] correspondiente a un método.
1. Ha habilitado la vinculación completa y no ha aplicado el atributo [preserve] al método [Export] Ed.
1. Ha aplicado el atributo [Export] a un método privado en un tipo heredado.

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>El archivo MainWindow.xib.designer.cs no está actualizado

Se produjo un error en Xamarin Studio 2,4 que hacía que no se agrupara el archivo MainWindow. Xib con el archivo MainWindow. Xib. Designer en proyectos nuevos. Esto significaba que no actualizaría el código del diseñador para ese archivo en particular.

Este problema se ha corregido en la versión de Visual Studio para Mac que está disponible en su actualizador integrado, por lo que debe asegurarse de usar la versión más reciente.

Puede corregir los proyectos existentes quitando (no eliminando) el Xib y su archivo de diseñador y, a continuación, agregándolo de nuevo. Esto debe volver a agrupar los archivos correctamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView o UIActionSheet desaparecen después de crearse

Si tiene código similar al siguiente:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

el objeto "actionSheet" reside como una variable temporal en la función y tan pronto como finaliza la función, el objeto es válido para la recolección de elementos no utilizados, por lo que termina en la recolección de elementos no utilizados.

Para corregir este problema, debe mantener una referencia a "actionSheet" fuera del método, en algún lugar que vaya más allá de su método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Project siempre se ejecuta en el simulador de iPad

El instalador de iPhone SDK 4,0 instala 2 SDK: el SDK de 3,2, para compilar aplicaciones solo para iPad y el SDK de 4,0, que se usa para compilar aplicaciones de iPhone y universal. También instala un simulador de 3,2, que simula solo un iPad y un simulador 4,0 que simula iPhone o iPhone 4. Se quitan todos los SDK y simuladores anteriores.

Visual Studio para Mac opciones de compilación del proyecto de iPhone incluyen un valor para la versión del SDK que se usará para compilar la aplicación. Esta configuración se puede encontrar en **Opciones de proyecto-> compilación-> iPhone Build**.

Los nuevos proyectos de Visual Studio para Mac usar el SDK instalado más antiguo como su configuración predeterminada del SDK y, si el SDK especificado no existe, Visual Studio para Mac usará lo más cercano puede encontrar para compilar la aplicación. Esto se hizo para que los proyectos no siempre requieran el SDK más reciente. Sin embargo, en este momento se usa el SDK de 3,2, lo que hace que se use el simulador de iPad.

Para solucionar este paso mediante el SDK de 4,0, vaya a **Project Options-> Build-> iPhone build**> y cambie el valor del SDK a "4,0" mediante el cuadro desplegable. Debe hacerlo para cada combinación de configuración y plataforma, a la que se tiene acceso mediante las listas desplegables en la parte superior del panel.

La versión del SDK no debe confundirse con la configuración de "versión mínima del sistema operativo".
Este valor no tiene que coincidir con el valor de la versión del SDK; esto afecta a la versión mínima del sistema operativo en el que se instalará la aplicación, lo que puede ser más antiguo que el SDK, siempre que solo se usen API que existan en el sistema operativo anterior o se proteja el uso de las características más recientes mediante el uso del sistema operativo en tiempo de ejecución. KS. Debe establecerla en la versión de sistema operativo más antigua en la que se prueba la aplicación.

Tenga en cuenta también que se puede usar el menú **de > de destino del simulador de iPhone del > del proyecto**para elegir el simulador que se utiliza de forma predeterminada al ejecutar o depurar un proyecto. Además, se puede usar el menú ejecutar **> ejecutar con**> para elegir un simulador específico con el que ejecutar

## <a name="ibtool-returns-error-133"></a>ibtool devuelve el error 133

Esto significa que tiene XCode 4 instalado.   En XCode 4, se quitó la herramienta ibtool, ya no es posible editar los archivos de XIB con una herramienta independiente.

Si desea usar Interface Builder, instale [Xcode series 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponible en el sitio web de Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>"No se puede crear el enlace de presentación para el tipo MIME: Application/vnd. Apple-Interface-Builder"

Este error se produce si intenta crear una interfaz de usuario de iPhone desde un proyecto que no es de iPhone. Asegúrese de empezar con una solución de iPhone o iPad, no es posible agregar elementos de interfaz de usuario de iPhone a un proyecto que no sea de iPhone o iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Bloqueo de inicio cuando se ejecuta en el simulador de iOS

Si obtiene un bloqueo en tiempo de ejecución (SIGSEGV) dentro del simulador junto con un seguimiento de la pila que tiene este aspecto:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

... es probable que tenga uno (o más) ensamblado obsoleto en el directorio de la aplicación del simulador. Estos ensamblados pueden existir ya que el simulador de Apple iOS agrega y actualiza archivos, pero nunca Los elimina. Si esto sucede, la solución más sencilla consiste en seleccionar "restablecer y el contenido y la configuración..." en el menú del simulador.   

> [!WARNING]
> Se quitarán todos los archivos, aplicaciones y datos del simulador.   La próxima vez que ejecute la aplicación, Visual Studio para Mac se implementará en el simulador y no habrá ningún ensamblado antiguo y obsoleto para provocar el bloqueo.

## <a name="simulator-hangs-during-application-installation"></a>El simulador deja de responder durante la instalación de la aplicación

Esto puede ocurrir cuando los nombres de aplicación incluyen un '. ' (punto) en su nombre.
Esto se prohíbe como el nombre del archivo ejecutable en CFBundleExecutable, incluso si puede funcionar en muchos otros casos (como los dispositivos).

 \* "El valor no debe incluir ninguna extensión en el nombre". -[https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Error: "No se admite el tipo de atributo personalizado 0X43" al hacer doble clic en archivos. Xib

Esto se debe a un intento de abrir archivos. Xib cuando las variables de entorno se establecen incorrectamente. Esto no debería ocurrir con el uso normal de Visual Studio para Mac/Xamarin. iOS, y volver a abrir Visual Studio para Mac desde/Applications debería solucionar el problema.

Al intentar actualizar el software y aparece este mensaje de error, envíe un correo electrónico *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>La aplicación se ejecuta en el simulador pero se produce un error en el dispositivo

Este problema puede manifestarse en varios formatos y no siempre genera un error coherente. Si la aplicación contiene un. Xib, asegúrese de que la **acción de compilación** en el. Xib se establece en **InterfaceDefinition**. Esta es la acción de compilación predeterminada para. Xib.

Para comprobar la acción de compilación, haga clic con el botón derecho en el archivo. Xib y elija **acción de compilación**.

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: No hay datos disponibles para la codificación 437

Al incluir bibliotecas de terceros en la aplicación de Xamarin. iOS, puede obtener un error con el formato "System. NotSupportedException: No hay datos disponibles para la codificación 437 "al intentar compilar y ejecutar la aplicación. Por ejemplo, las bibliotecas, `Ionic.Zip.ZipFile`como, pueden producir esta excepción durante la operación.

Esto puede solucionarse abriendo las opciones del proyecto de Xamarin. iOS, pasando a la**internacionalización** de la **compilación** > de iOS y comprobando la internacionalización **occidental** .
