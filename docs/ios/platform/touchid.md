---
title: Touch ID en Xamarin. iOS
description: En este documento se describe cómo usar Touch ID, la tecnología de autenticación de huellas digitales biométricas de Apple, en aplicaciones de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 112a2a038be9f749f37d2d3260d08f2e58b0c597
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031425"
---
# <a name="touch-id-in-xamarinios"></a>Touch ID en Xamarin. iOS

Touch ID se incorporó en iOS 7 como medio para autenticar al usuario, similar a un código de acceso. Sin embargo, se limitó a desbloquear el dispositivo, mediante el App Store, con iTunes y autenticando solo la cadena de claves de iCloud.

Ahora hay dos maneras de usar Touch ID como mecanismo de autenticación en una aplicación de iOS 8 mediante la API de autenticación local. Actualmente no es posible usar la autenticación local para autenticarse de forma remota.

Para comprender perfectamente el ID. de Touch y su patrimonio, deberíamos explorar los servicios de cadena de claves y lo que significan estos nuevos cambios en los datos del usuario. El acceso a llaves también se ha ampliado en iOS 8 mediante el uso de la nueva característica de listas de Access Control (ACL).

## <a name="keychain--secure-enclave"></a>Cadena de claves & Secure enclave

La cadena de claves es una base de datos de gran tamaño que proporciona almacenamiento seguro para contraseñas, claves, certificados y notas para un ID. de Apple individual. En iOS 8, una aplicación siempre tiene acceso a sus propios elementos de cadena de claves únicos y no puede tener acceso a los elementos de cadena de claves de otras aplicaciones. Esto difiere de OS X donde la cadena de claves se desbloquea con una sola contraseña, lo que permite que cualquier aplicación compatible con los servicios de cadena de claves use la cadena de claves. En este artículo, nos centraremos en cómo funciona la cadena de claves en iOS 8.

La cadena de claves es una base de datos especializada, donde cada fila se conoce como un _elemento de cadena de claves_. Cada elemento se describe mediante atributos de cadena de claves y se compone de valores cifrados. Para permitir un uso eficaz de la cadena de claves, está optimizada para elementos pequeños o _secretos_.
Cada elemento de cadena de claves está protegido por el código de acceso de los usuarios y un secreto de dispositivo único. Los elementos de cadena de claves deben estar protegidos incluso cuando los usuarios no usen sus dispositivos. Esto se implementa en iOS solo permitiendo que los elementos estén disponibles cuando el dispositivo esté desbloqueado: cuando el dispositivo está bloqueado, dejan de estar disponibles. También se pueden almacenar en una copia de seguridad cifrada. Una de las características clave de la cadena de claves es aplicar el control de acceso; una aplicación tiene acceso a su parte de la cadena de claves y se impedirán todas las demás aplicaciones. En el diagrama siguiente se muestra cómo interactúa una aplicación con la cadena de claves:

[![](touchid-images/image1.png "This diagram illustrates how an application interacts with the keychain")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Protección de enclave

La cadena de claves no puede descifrar el elemento de cadena de claves. en su lugar, se realiza en el *enclave seguro*. El enclave seguro es un co-procesador dentro del chip A7 que es responsable de determinar una coincidencia correcta de los datos de huella digital del sensor de Touch ID con una impresión registrada. Después descifrará el elemento de cadena de claves y devolverá el secreto descifrado a la cadena de claves.

### <a name="working-with-keychain"></a>Trabajar con llaves

En primer lugar, la aplicación debe consultar la cadena de claves para ver si existe una contraseña. Si no existe, es posible que tenga que solicitar una contraseña para que no se le pregunte al usuario. Si es necesario actualizar la contraseña, pida al usuario una nueva contraseña y pase el valor actualizado a la cadena de claves.

> [!NOTE]
> Después de usar un secreto recuperado de la cadena de claves, todas las referencias a los datos se deben purgar de la memoria. No lo asigne nunca a una variable global.

## <a name="keychain-acl-and-touch-id"></a>ACL e ID. táctil de cadena de claves

Access Control lista es un nuevo atributo de elemento de cadena de claves en iOS 8 que describe la información relativa a lo que debe ocurrir para permitir que se produzca una operación determinada. Puede tratarse de una forma de mostrar un cuadro de diálogo de alerta o de solicitar un código de acceso. ACL permite establecer la accesibilidad y la autenticación para un elemento de cadena de claves. En el diagrama siguiente se muestra cómo se vincula este nuevo atributo con el resto del elemento de cadena de claves:

[![](touchid-images/image2.png "This diagram shows how this new attribute ties in with the rest of the keychain item")](touchid-images/image2.png#lightbox)

A partir de iOS 8, ahora hay una nueva Directiva de presencia de usuario, `SecAccessControl`, que se aplica mediante el enclave seguro en un iPhone 5 s y versiones posteriores. En la tabla siguiente se puede ver el modo en que la configuración del dispositivo puede influir en la evaluación de la Directiva:

|Configuración del dispositivo|Evaluación de directivas|Mecanismo de copia de seguridad|
|--- |--- |--- |
|Dispositivo sin código de acceso|Sin acceso|Ninguno|
|Dispositivo con código de acceso|Requiere código de acceso|Ninguno|
|Dispositivo con Touch ID|Prefiere el ID. táctil|Permite código de acceso|

Todas las operaciones dentro del enclave seguro pueden confiar entre sí. Esto significa que se puede usar el resultado de autenticación Touch ID para autorizar el descifrado del elemento de cadena de claves. El enclave seguro también mantiene un contador de coincidencias de ID. de Touch con errores, en cuyo caso un usuario tendrá que volver a usar el código de acceso.
Un nuevo marco de trabajo de iOS 8, denominado _autenticación local_, es compatible con este proceso de autenticación dentro del dispositivo. Lo exploraremos en la sección siguiente.

## <a name="local-authentication"></a>Autenticación local

Como hemos establecido en la sección anterior, las aplicaciones pueden usar la autenticación local para autenticar al usuario en cumplimiento con la Directiva de seguridad que se ha configurado en el dispositivo.

Actualmente, la API solo proporciona dos funciones: en primer lugar, ayuda a los servicios de cadena de claves existentes mediante el uso de nuevas listas de claves Access Control (ACL). Los datos de cadena de claves se pueden desbloquear con la autenticación correcta de la huella digital de los usuarios.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar la aplicación localmente. Los desarrolladores deben usar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de aceptar el ID. de Touch y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Aunque ambas capacidades ofrecen autenticación local, no proporcionan un mecanismo para que la aplicación o el usuario se autentiquen en un servidor remoto.
La autenticación local proporciona una nueva interfaz de usuario estándar para la autenticación. En el caso de Touch ID, se trata de una vista de alertas con dos botones, como se muestra a continuación. Un botón para cancelar y otro para usar el medio de reserva de autenticación: el código de acceso. También hay un mensaje personalizado que debe establecerse. Es recomendable usarlo para explicar al usuario por qué se requiere la autenticación Touch ID.

[![](touchid-images/image12.png "The Touch ID authentication alert")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Con los servicios de cadena de claves

Hemos visto un poco más arriba en cómo se descifra un elemento de cadena de claves, mediante el uso de enclave seguro para comprobar el código de acceso. En iOS 8, podemos usar la autenticación local para solicitar la comprobación del identificador de Touch junto con la característica listas de Access Control, que proporciona la implementación del mecanismo de reserva o la contraseña.
Para usar ACL, deberíamos usar la Directiva de `SecAccessControl` y, a continuación, comprobar el estado del dispositivo con `SecAccessible.WhenPasscodeSetThisDeviceOnly` o `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Consideraciones con ACL

Hay muchas cosas que debemos tener en cuenta al usar ACL con la cadena de claves y algunas de ellas se enumeran a continuación:

- Usar solo con la aplicación de primer plano: Si llama a cualquier operación de cadena de claves en un subproceso en segundo plano, se producirá un error en la llamada.
- La adición y actualización de elementos de cadena de claves puede requerir autenticación.
- Si una solicitud devuelve varios elementos coincidentes en la cadena de claves, es posible que sea necesaria la autenticación.
- Los elementos protegidos con ACL son solo de dispositivo y, por tanto, no se sincronizan ni se realiza una copia de seguridad.

### <a name="using-local-authentication-without-keychain-services"></a>Usar la autenticación local sin los servicios de cadena de claves

La autenticación local se creó como una manera de recopilar credenciales, como el código de acceso o el ID. táctil, y para trabajar con el enclave seguro para finalizar la autenticación del usuario. Imagínese como un puente entre la aplicación y el enclave seguro, que nunca se puede comunicar directamente entre sí. También se puede usar para la evaluación de directivas de la aplicación.

Para ello, una aplicación llama a la evaluación de la Directiva dentro de la autenticación local, que inicia la operación dentro de enclave seguro. Puede aprovechar esto para proporcionar autenticación a la aplicación, sin consultar directamente el enclave seguro ni acceder a él.

[![](touchid-images/image13a.png "Using Local Authentication without Keychain Services")](touchid-images/image13a.png#lightbox)

El uso de la autenticación local en la aplicación proporciona una manera sencilla de implementar la comprobación del usuario, por ejemplo, para desbloquear una característica únicamente para los ojos del propietario del dispositivo, como las aplicaciones bancarias, o para que se le ayude a controlar los controles parentales. aplicación. También puede usarlo como una manera de ampliar la autenticación que ya existe: los usuarios como su información para ser seguros, pero también les gustaría tener opciones.

La seguridad de la autenticación local difiere de la de la cadena de claves. Por ejemplo, al usar la cadena de claves, la confianza se encuentra entre el sistema operativo y el enclave seguro. Con la autenticación local, se encuentra entre la aplicación y el sistema operativo, lo que significa que solo tiene acceso a los resultados del enclave seguro, no al propio enclave seguro.

En lo que se refiere a la seguridad, también es muy importante saber que no hay **acceso** a los dedos registrados ni a las imágenes de huella digital. El enclave seguro es el propietario de esta información, por lo que ningún otro componente del sistema tiene acceso a ella.

Para usar Touch ID sin cadena de claves aprovechando la API de autenticación local, hay algunas funciones que se pueden usar. Estos se detallan a continuación:

- `CanEvaluatePolicy`: esto simplemente comprueba si el dispositivo es capaz de aceptar el Touch ID.
- `EvaluatePolicy`: inicia la operación de autenticación y muestra la interfaz de usuario, y devuelve un `true` o `false` respuesta.
- `DeviceOwnerAuthenticationWithBiometrics`: se trata de la Directiva que se puede usar para mostrar la pantalla de Touch ID. Merece la pena mencionar que, en su lugar, no hay ningún mecanismo de reserva de código de acceso, debe implementar esta reserva en la aplicación para permitir a los usuarios omitir la autenticación de Touch ID.

Hay algunas advertencias con respecto al uso de la autenticación local, que se enumeran a continuación:

- Como con la cadena de claves, solo se puede ejecutar en primer plano. Si se llama en un subproceso en segundo plano, se producirá un error.
- Tenga en cuenta que se puede producir un error en la evaluación de la Directiva. Debe implementarse un botón de código de acceso como revertir.
- Debe proporcionar un `localizedReason` para explicar por qué se necesita autenticación. Esto ayuda a compilar la confianza con el usuario.

A continuación, en la sección siguiente, veremos cómo implementar la API teniendo en cuenta estas advertencias.

## <a name="adding-touch-id-to-your-application"></a>Agregar Touch ID a la aplicación

En las secciones anteriores, analizamos la teoría detrás del acceso y la autenticación mediante la cadena de claves y la autenticación local. Ahora echaremos un vistazo a cómo puede integrar Touch ID en en su aplicación.

### <a name="walkthrough"></a>Tutorial

Echemos un vistazo a la adición de una autenticación Touch ID a nuestra aplicación. En este tutorial vamos a actualizar el ejemplo de [tabla de guion gráfico](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/) , que agrega la autenticación local para que funcione como el ejemplo de [autenticación de tabla de guion gráfico](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication) , que solo permite a los usuarios autenticados agregar tareas a la lista.

1. Descargue el ejemplo y ejecútelo en Visual Studio para Mac.
2. Haga doble clic en `MainStoryboard.Storyboard` para abrir el ejemplo en el diseñador de iOS. Con este ejemplo, queremos agregar una nueva pantalla a la aplicación, que controlará la autenticación. Irá delante del `MasterViewController`actual.
3. Arrastre un nuevo **controlador de vista** desde el **cuadro de herramientas** hasta el **superficie de diseño**. Establézcalo como el **controlador de vista raíz** mediante **Ctrl + arrastre** desde el **controlador de navegación**:

    [![](touchid-images/image4.png "Set the Root View Controller")](touchid-images/image4.png#lightbox)
4. Asigne al nuevo controlador de vista el nombre `AuthenticationViewController`.
5. A continuación, arrastre un botón y colóquelo en el `AuthenticationViewController`. Llame a este `AuthenticateButton`y asígnele el `Add a Chore`de texto.
6. Cree un evento en el `AuthenticateButton` denominado `AuthenticateMe`.
7. Cree un segue manual desde `AuthenticationViewController`; para ello, haga clic en la barra negra situada en la parte inferior y **presione Ctrl + arrastre** desde la barra hasta el `MasterViewController` y elija la tecla de **comando de presionar** (o **Mostrar** si se usan las clases de tamaño):

    [![](touchid-images/image5.png "Drag from the bar to the MasterViewController and choosing push or show")](touchid-images/image6.png#lightbox)
8. Haga clic en el segue recién creado y asígnele el identificador `AuthenticationSegue`, como se muestra a continuación:

    [![](touchid-images/image7.png "Set the segue identifier to AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. Agrega el código siguiente a `AuthenticationViewController`:

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

Este es todo el código que necesita para implementar la autenticación de Touch ID mediante la autenticación local. Las líneas resaltadas en la imagen siguiente muestran el uso de la autenticación local:

[![](touchid-images/image8.png "The highlighted lines show the use of Local Authentication")](touchid-images/image8.png#lightbox)

En primer lugar, es necesario establecer si el dispositivo es capaz de aceptar la entrada de Touch ID, mediante el `CanEvaluatePolicy` y pasando el `DeviceOwnerAuthenticationWithBiometrics`de la Directiva. Si esto es así, podemos mostrar la interfaz de usuario de Touch ID mediante `EvaluatePolicy`. Hay tres fragmentos de información que tenemos que pasar en `EvaluatePolicy`, la propia Directiva, una cadena que explica por qué es necesaria la autenticación y un controlador de respuesta. El controlador de respuesta indica a la aplicación lo que debe hacer en caso de que se realice una autenticación correcta o incorrectamente. Echemos un vistazo más cerca al controlador de respuesta:

[![](touchid-images/image9.png "The reply handler")](touchid-images/image9.png#lightbox)

El controlador de respuesta se especifica de tipo `LAContextReplyHandler`, que toma los parámetros correctos: un valor `bool` y una `NSError` denominada `error`. Si se realiza correctamente, aquí es donde haremos todo lo que quiera autenticar; en este caso, se muestra la pantalla que nos permitirá agregar una nueva tarea. Recuerde que una de las advertencias de la autenticación local es que debe ejecutarse en primer plano, así que asegúrese de usar `InvokeOnMainThread`:

[![](touchid-images/image10.png "Use InvokeOnMainThread for Local Authentication")](touchid-images/image10.png#lightbox)

Por último, cuando la autenticación se ha realizado correctamente, queremos pasar a la `MasterViewController`. El método `PerformSegue` se puede usar para ello:

[![](touchid-images/image11.png "Call PerformSegue method to transition to the MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Resumen

En esta guía se ha examinado la cadena de claves y cómo funciona en iOS. También se ha explorado la ACL de la cadena de claves y se han realizado cambios en iOS. A continuación, echamos un vistazo al marco de autenticación local, que es nuevo en iOS 8 y, a continuación, examinamos la implementación de la autenticación Touch ID en nuestra aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Tabla de guiones gráficos: autenticación local](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [Ejemplo WWDC de cadena de claves](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [Cadena de claves (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)
