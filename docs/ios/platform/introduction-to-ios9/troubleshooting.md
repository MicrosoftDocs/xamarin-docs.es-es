---
title: 'Xamarin. iOS 9: solución de problemas'
description: En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con iOS 9 en Xamarin. iOS. Las sugerencias cubren el análisis de XML, los simuladores, las restricciones de diseño, los problemas de red y muchos otros temas.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031753"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. iOS 9: solución de problemas

_En este artículo se proporcionan varias sugerencias para la solución de problemas para trabajar con iOS 9 en aplicaciones de Xamarin. iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Se produjo un problema al analizar el código XML

Xamarin iOS Designer todavía no admite características de Xcode 7. Los guiones gráficos no se cargarán en _el diseñador con "se produjo un problema al analizar el XML"_ al intentar usar nuevos elementos del diseñador de iOS 9 (Xcode 7) como StackView.

la compatibilidad del diseñador de iOS con las características de Xcode 7 está destinada a la próxima versión de la característica ciclo 6. La versión preliminar del ciclo 6 está disponible actualmente en el canal alfa y tiene compatibilidad limitada con las nuevas características de Xcode 7.

Solución alternativa parcial para Visual Studio para Mac: haga clic con el botón derecho en el guión gráfico y elija **abrir con** > **Xcode Interface Builder**.

## <a name="where-are-the-ios-8-simulators"></a>¿Dónde están los simuladores de iOS 8?

Si ha instalado Xcode 7 (o superior), reemplazará automáticamente todos los simuladores de iOS 8 con los simuladores de iOS 9 de forma predeterminada. Si todavía necesita probar en iOS 8, puede iniciar Xcode y luego descargar e instalar los simuladores de iOS 8.

En Xcode, seleccione el menú de **Xcode** , a continuación, **preferencias...**  > **descargas**:

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

Haga clic en el botón **comprobar e instalar ahora** para volver a instalar los simuladores de iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restricción de diseño con errores de atributo izquierdo/derecho

En iOS 8 (y versiones anteriores), los elementos de la interfaz de usuario de los guiones gráficos pueden usar una combinación **de los atributos & ** **izquierda** (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) y los atributos **finales** de & **iniciales** (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) en el mismo diseño.

Si el mismo guion gráfico se ejecuta en iOS 9, se producirá una excepción con el formato siguiente:

> Finalizando la aplicación debido a la excepción no detectada ' NSInvalidArgumentException ', motivo: ' * * * + [NSLayoutConstraint constraintWithItem: Attribute: relatedBy: toItem: Attribute: multiplicador: Constant:]: no se puede establecer una restricción entre un principio o un final Attribute y un atributo Right/left. Use iniciales/finales para ambos o ninguno.

iOS 9 aplica los diseños para usar los atributos finales ** & ** **izquierda** _o_ ** & ,** pero *no* ambos. Para corregir este problema, cambie todas las restricciones de diseño para usar el mismo conjunto de atributos en el archivo de guion gráfico.

Para obtener más información, consulte el [error de restricción de iOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) Stack Overflow discusión.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERROR ITMS-90535: clave CFBundleExecutable inesperada

Después de cambiar a iOS 9, desde una aplicación usa componentes de terceros (específicamente nuestro componente de Google Maps existente) que se compilaron y ejecutaron en iOS 8 (o versiones anteriores), al intentar enviar la nueva compilación a iTunes Connect, puede obtener un error con el formato:

> ERROR ITMS-90535: clave CFBundleExecutable inesperada. La agrupación en ' payload/App-Name. app/Component. bundle ' no contiene un ejecutable de paquete...

Normalmente, estos problemas se pueden resolver buscando la agrupación con nombre en el proyecto y, de este modo, el mensaje de error sugiere-editó el `Info.plist` que se encuentra en la agrupación quitando la clave de `CFBundleExecutable`. También se debe establecer la clave `CFBundlePackageType` en `BNDL`.

Después de realizar estos cambios, limpie y Recompile todo el proyecto. Debe ser capaz de enviar a iTunes Connect sin ningún problema después de realizar estos cambios.

Para obtener más información, consulte este [Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) debate.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Error de CFNetwork SSLHandshake (-9824)

Al intentar conectarse a Internet, ya sea directamente o desde una vista Web en iOS 9, puede obtener un error con el formato:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O en el formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

En iOS9, la seguridad de transporte de aplicaciones (ATS) exige conexiones seguras entre los recursos de Internet (como el servidor back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación mediante el protocolo de `HTTPS` y la comunicación de API de alto nivel que se va a cifrar con la versión 1,2 de TLS con confidencialidad directa.

Dado que ATS está habilitado de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10,11 (el Capitan), todas las conexiones que usen `NSURLConnection`, `CFURL` o `NSURLSession` estarán sujetas a los requisitos de seguridad de ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Consulte la sección [de aceptación de ATS](~/ios/app-fundamentals/ats.md) de nuestra guía de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) para obtener información sobre cómo resolver este problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Mis aplicaciones existentes no se ejecutan en iOS 9

Consulte la [información de compatibilidad de iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obtener instrucciones sobre cómo volver a generar y volver a implementar las aplicaciones existentes para que se ejecuten en iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView es null en los constructores

**Motivo:** En iOS 9, el constructor de `initWithFrame:` es necesario ahora, debido a los cambios de comportamiento en iOS 9 como los Estados de la [documentación de UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si registró una clase para el identificador especificado y se debe crear una nueva celda, la celda se inicializará ahora llamando a su método `initWithFrame:`.

**Corrección:** Agregue el `initWithFrame:` constructor de la siguiente manera:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplos relacionados: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView no se puede inicializar con el codificador al cargar una vista desde Xib/NIB

**Motivo:** El constructor `initWithCoder:` es el que se llama al cargar una vista desde un archivo Interface Builder Xib. Si este constructor no se exporta, el código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (por ejemplo, en iOS 8), se invocó el constructor `IntPtr` para inicializar la vista.

**Corrección:** Cree y exporte el `initWithCoder:` constructor de la siguiente manera:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Mensaje de dyld: no hay ninguna imagen de caché con el nombre...

Podría experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Se trata de un error en el enlazador nativo de Apple, que tiene lugar cuando hace que un marco privado sea público (JavaScriptCore se hizo público en iOS 7, antes de que fuera un marco de trabajo privado) y el destino de implementación de la aplicación sea para una versión de iOS cuando el marco de trabajo era privado. En este caso, el enlazador de Apple se vinculará con la versión privada de Framework en lugar de con la versión pública.

**Corrección:** Esto se solucionará para iOS 9, pero existe una solución sencilla que puede aplicarse a la vez: solo tiene que elegir como destino una versión posterior de iOS en el proyecto (puede probar iOS 7 en este caso). Otros marcos de trabajo pueden mostrar problemas similares, por ejemplo, el marco de la versión de .NET Framework se hizo público en iOS 8 (y, por tanto, el destino de iOS 7 producirá este error; debe tener como destino iOS 8 para usar WebKit en la aplicación).

## <a name="untrusted-enterprise-developer"></a>Desarrollador empresarial que no es de confianza

Al intentar ejecutar la versión de iOS 9 de la aplicación de Xamarin. iOS en un hardware de iOS real, es posible que aparezca un mensaje que indica que la cuenta de desarrollador no ha sido de confianza en el dispositivo. Por ejemplo:

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

Para solucionar este problema, haga lo siguiente:

1. Inicie Xcode (la versión beta más reciente) en el equipo Mac de desarrollo.
2. Seleccione **dispositivos** en el menú **ventana** para abrir la ventana dispositivos: 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. En el panel lateral **dispositivos** , seleccione el dispositivo, haga clic con el botón derecho y seleccione **Mostrar perfiles de aprovisionamiento..** .: 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. Seleccione cada perfil de aprovisionamiento actualmente en el dispositivo y haga clic en el botón **-** para eliminarlo: 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. En el menú de **Xcode** , seleccione **preferencias...** y **cuentas**: 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. Haga clic en el botón **Ver detalles..** . y, a continuación, haga clic en el botón **descargar todo** : 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. Una vez finalizada la actualización de la lista, haga clic en el botón **listo** y cierre la ventana Preferencias.
8. Quite la versión existente de la aplicación de Xamarin. iOS que estaba intentando probar desde el dispositivo iOS.
9. Vuelva a Visual Studio para Mac, realice una compilación limpia e intente volver a ejecutar la aplicación en el dispositivo.

Es posible que tenga que detener y reiniciar Visual Studio para Mac antes de que se vean los nuevos perfiles de aprovisionamiento cargados por Xcode. También puede que tenga que ajustar las opciones de **firma del lote de iOS** para la aplicación de Xamarin. iOS para seleccionar los nuevos perfiles de aprovisionamiento.

## <a name="launch-screen-issues"></a>Iniciar problemas de pantalla

iOS 9 aplica ahora los requisitos de la pantalla de inicio para que ya no se pueda volver a usar la misma imagen de inicio para admitir distintas orientaciones de interfaz. Consulte la [referencia de UILanchImage](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) de Apple para obtener más información.

Opcionalmente, puede usar un archivo de guion gráfico para mostrar la pantalla de inicio de la aplicación en lugar de usar un conjunto de archivos de imagen **. png** . Esta es ahora la mejor forma de presentar pantallas de inicio. Consulte la guía de [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información.

Por último, la aplicación debe usar un archivo de guion gráfico para su pantalla de inicio y admitir las cuatro orientaciones de interfaz (vertical, vertical, vertical, horizontalmente a la izquierda y horizontal derecha) que se deben tener en cuenta para que se ejecuten en un panel deslizante o en el modo de vista en dos paneles. Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte la guía [de multitarea para iPad](~/ios/platform/multitasking.md) .

## <a name="nsinternalinconsistencyexception-exception"></a>Excepción NSInternalInconsistencyException

Al compilar y ejecutar una aplicación de Xamarin. iOS existente para iOS 9, podría obtener un error con el formato:

> Se produjo una excepción de Objective-C.  Nombre: NSInternalInconsistencyException motivo: se espera que las ventanas de la aplicación tengan un controlador de vista raíz al finalizar el inicio de la aplicación

Este error se produce porque se espera que las ventanas de la aplicación tengan un controlador de vista raíz al finalizar el inicio de la aplicación y la aplicación existente no.

Existen al menos dos posibles soluciones para este problema:

1. Actualice la aplicación para que use un archivo de guion gráfico en lugar de `xib` archivos para definir su interfaz de usuario. Esto requiere bastante tiempo según el tamaño de la aplicación y el conocimiento de cómo usar el diseñador de iOS (o la Interface Builder de Xcode) para diseñar guiones gráficos. Para obtener más información, consulte la documentación [Introducción a los guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
2. Configure `RootViewController` propiedad de la ventana de la aplicación en `FinishedLaunching` método en `AppDelegate` clase para apuntar a un controlador de vista en la interfaz de usuario de la aplicación.

## <a name="when-to-initialize-views-and-view-controllers"></a>Cuándo inicializar vistas y controladores de vista

Con Xamarin. iOS es posible realizar la inicialización del controlador de vista o vista dentro de los constructores a los que se llama cuando se expone algo en el código administrado, pero interrumpe el diseño de iOS.

En general, no debe inicializar nada que pueda llamar a código de Objective-C desde el constructor, ya que no puede estar seguro de Cuándo se llamará. Eso también significa que hay mejores lugares (otros. ctor) o llamadas a override (ya que Objective-C no tiene eventos) donde se debe realizar esta inicialización.

## <a name="related-links"></a>Vínculos relacionados

- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualización de las aplicaciones de Xamarin. iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
