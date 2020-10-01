---
title: Solucionar problemas de conexión del host de compilación de Xamarin.iOS
description: En esta guía se proporcionan los pasos necesarios para solucionar los problemas que se pueden producir al usar el nuevo administrador de conexiones, incluidos los problemas de conectividad y SSH.
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 56c4e9c5b0ca44500ca3930a63b300b6bd3fb33f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436599"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Solucionar problemas de conexión del host de compilación de Xamarin.iOS

_En esta guía se proporcionan los pasos necesarios para solucionar los problemas que se pueden producir al usar el nuevo administrador de conexiones, incluidos los problemas de conectividad y SSH._

## <a name="log-file-location"></a>Ubicación del archivo de registro

- **Mac**:`~/Library/Logs/Xamarin-[MAJOR.MINOR]`
- **Windows**:`%LOCALAPPDATA%\Xamarin\Logs`

Para encontrar los archivos de registro, vaya a **Ayuda &gt; Xamarin &gt; Comprimir registros** en Visual Studio.

## <a name="wheres-the-xamarin-build-host-app"></a>¿Dónde está la aplicación host de compilación de Xamarin?

El host de compilación de Xamarin de versiones anteriores de Xamarin.iOS ya no es necesario. Visual Studio ahora implementa automáticamente el agente en el inicio de sesión remoto y lo ejecuta en segundo plano. No se ejecutará ninguna aplicación adicional en la máquina Mac o Windows.

## <a name="troubleshooting-remote-login"></a>Solucionar problemas del inicio de sesión remoto

> [!IMPORTANT]
> Estos pasos están pensados principalmente para solucionar los problemas que se producen durante la instalación inicial en un sistema nuevo.  Si ha estado utilizando anteriormente la conexión sin problemas en un entorno concreto y la conexión dejó de funcionar de repente o de vez en cuando, puede (en la mayoría de los casos) pasar directamente a comprobar si ayuda algo de lo siguiente: 
>
> - Elimine los procesos sobrantes tal y como se describe a continuación en [Errores debidos a los procesos de host de compilación existentes en el equipo Mac](#errors). 
> - Desactive los agentes tal y como se describe en [Desactivar los agentes de Broker, IDB, compilación y diseñador en el equipo Mac](#clearing) y, a continuación, use una conexión a Internet por cable y conéctese directamente a través de la dirección IP, tal y como se describe en [Couldn't connect to MacBuildHost.local. Please try again.](#tryagain) (No se pudo conectar a MacBuildHost.local. Inténtelo de nuevo).  
> Si ninguno de estos pasos soluciona el problema, siga las instrucciones incluidas en el [paso 9](#stepnine) para registrar un nuevo informe de errores.

1. Compruebe que tiene instaladas versiones de Xamarin.iOS compatibles en el equipo Mac. Para hacer esto con Visual Studio 2017, asegúrese de que se encuentra en el canal de distribución **estable** en Visual Studio para Mac. En Visual Studio 2015 y anterior, asegúrese de que se encuentra en el mismo canal de distribución en ambos IDE.
    - En Visual Studio para Mac, vaya a **Visual Studio para Mac > Buscar actualizaciones...**  para ver o cambiar el **canal de actualización**.
    - En Visual Studio 2015 y anterior, compruebe el canal de distribución en **Herramientas > Opciones > Xamarin > Otros**.

2. Asegúrese de que esté habilitada la opción **Inicio de sesión remoto** en el equipo Mac. Establezca el acceso para **Solo estos usuarios** y asegúrese de que su usuario de Mac esté incluido en la lista o grupo:

    [![Establecimiento del acceso para solo estos usuarios](troubleshooting-images/troubleshooting-image1.png)](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. Compruebe que el firewall permita las conexiones entrantes a través del puerto 22, el predeterminado para SSH:

    [![Comprobación de que el firewall permita las conexiones entrantes a través del puerto 22](troubleshooting-images/troubleshooting-image2.png)](troubleshooting-images/troubleshooting-image2.png#lightbox)

    Si ha desactivado **Automatically allow signed software to receive incoming connections** (Permitir automáticamente que el software firmado reciba conexiones entrantes), OS X mostrará un cuadro de diálogo durante el proceso de emparejamiento en el que se le preguntará si se permite que `mono-sgen` o `mono-sgen32` reciban conexiones entrantes. Haga clic en **Permitir** en este cuadro de diálogo:

    [![Clic en Permitir en este cuadro de diálogo](troubleshooting-images/troubleshooting-image4a.png)](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. Confirme que ha iniciado sesión en la cuenta de usuario en ese equipo Mac y que tiene una sesión activa en la GUI.

5. Asegúrese de que se está conectando al equipo Mac con el _nombre de usuario_, en lugar de con el _nombre completo_. De este modo se evita una limitación conocida que se aplica a los nombres completos que incluyen caracteres acentuados.

    Para encontrar su _nombre de usuario_, ejecute el comando `whoami` en **Terminal.app**.

    Por ejemplo, en la siguiente captura de pantalla, el nombre de cuenta será **amyb** y no **Amy Burns**:

    [![Obtención del nombre de cuenta de la aplicación Terminal](troubleshooting-images/troubleshooting-image5a.png)](troubleshooting-images/troubleshooting-image5a.png#lightbox)

6. Compruebe que la dirección IP que usa para el equipo Mac es correcta. Encontrará la dirección IP en **Preferencias del sistema > Compartir > Inicio de sesión remoto** en el equipo Mac.

    [![Dirección IP en la aplicación Preferencias del sistema](troubleshooting-images/troubleshooting-image17.png)](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. Una vez que haya confirmado la dirección IP del equipo Mac, intente hacer `ping` a esa dirección en `cmd.exe` en Windows:

    ```
    ping 10.1.8.95
    ```
    
    Si se produce un error al hacer ping, significa que el equipo Mac no es _enrutable_ desde el equipo Windows. Deberá resolver ese problema en la configuración de red de área local entre los dos equipos. Asegúrese de que ambas máquinas estén en la misma red local.

8. Después, pruebe si el cliente `ssh` OpenSSH puede conectarse correctamente al equipo Mac desde Windows. Una manera de instalar este programa consiste en instalar [Git para Windows](https://git-for-windows.github.io/). Luego, puede iniciar un símbolo del sistema **Git Bash** e intentar conectarlo mediante `ssh` al equipo Mac con su nombre de usuario y dirección IP:

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine"></a>

9. Si **el paso 8 se realiza correctamente**, puede intentar ejecutar un comando simple como `ls` en la conexión:

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    Esto debería mostrar el contenido de su directorio particular en el equipo Mac. Si el comando `ls` funciona correctamente pero la conexión de Visual Studio sigue sin funcionar, puede comprobar la sección titulada [Problemas y limitaciones conocidos](#knownissues) sobre problemas específicos de Xamarin. Si ninguno de estos coincide con su problema, registre un nuevo informe de errores en la Comunidad de desarrolladores. Para ello, vaya a **Ayuda > Enviar comentarios > Notificar un problema** en Visual Studio y adjunte los registros que se describen en [Archivos de registro detallados](#verboselogs).

10. Si **se produce un error en el paso 8**, puede ejecutar el comando siguiente en Terminal en el equipo Mac para ver si el servidor SSH acepta _alguna_ conexión:

    ```bash
    ssh localhost
    ```
    
11. Si se produce un error en el paso 8 pero **el paso 10 se realiza correctamente**, lo más probable es que el problema se deba a que el puerto 22 del host de compilación de Mac no es accesible desde Windows debido a la configuración de red. Entre los posibles problemas de configuración se incluyen los siguientes:

    - La configuración del firewall de OS X no permite la conexión. Asegúrese de comprobar de nuevo el paso 3.

        En ocasiones, la configuración por aplicación del firewall de OS X también puede derivar en un estado no válido, en el que la configuración que se muestra en las preferencias del sistema no refleja el comportamiento real. Si elimina el archivo de configuración ( **/Library/Preferences/com.apple.alf.plist**) y reinicia el equipo, puede ayudar a restaurar el comportamiento predeterminado. Una manera de eliminar el archivo consiste en escribir **/Library/Preferences** en **Ir &gt; Ir a la carpeta** en el Finder y, después, mover el archivo **com.apple.alf.plist** a la papelera.

    - La configuración del firewall de uno de los enrutadores entre el equipo Mac y el equipo Windows está bloqueando la conexión.

    - Windows no permite las conexiones salientes al puerto remoto 22. Esto no es habitual. Es posible configurar el Firewall de Windows de modo que no permita las conexiones salientes, pero la configuración predeterminada permite todas las conexiones salientes.

    - El host de compilación de Mac no permite el acceso al puerto 22 desde ningún host externo mediante una regla `pfctl`. Esto es poco probable, a menos que se haya configurado `pfctl` en el pasado.

12. Si se produce un error en el paso 8 y también **se produce un error en el paso 10**, es probable que el problema se deba a que el proceso del servidor SSH del equipo Mac no se está ejecutando o no está configurado para permitir que el usuario actual inicie sesión. En este caso, asegúrese de comprobar de nuevo la configuración del inicio de sesión remoto del paso 2 antes de investigar posibilidades más complicadas.

<a name="knownissues"></a>

### <a name="known-issues-and-limitations"></a>Problemas y limitaciones conocidos

> [!NOTE]
> Esta sección solo se aplica si ya se ha conectado correctamente al host de compilación de Mac con su nombre de usuario de Mac y su contraseña mediante el cliente SSH OpenSSH, tal como se describió en los pasos 8 y 9 anteriores.

#### <a name="invalid-credentials-please-try-again"></a>"Invalid credentials. Please try again." (Credenciales no válidas. Vuelva a intentarlo.)

Causas conocidas:

- **Limitación**: este error puede aparecer al intentar iniciar sesión en el host de compilación con el _nombre completo_ de la cuenta si dicho nombre incluye un carácter acentuado. Se trata de una limitación de la [biblioteca SSH.NET](https://sshnet.codeplex.com/) que Xamarin usa para la conexión SSH. **Solución alternativa**: Consulte el paso 5 anterior.

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>"Unable to authenticate with SSH keys. Please try to log in with credentials first" (No se pudo autenticar con claves SSH. Intente primero iniciar sesión con las credenciales.)

Causa conocida:

- **Restricción de seguridad de SSH**: la mayoría de las veces, este mensaje significa que uno de los archivos o directorios en la ruta de acceso completa **$HOME/.ssh/authorized\_keys** del equipo Mac tiene permisos de escritura habilitados para _otros_ miembros o miembros de _grupo_. **Corrección habitual**: ejecute `chmod og-w "$HOME"` en un símbolo del sistema de Terminal en el equipo Mac. Para obtener más información sobre qué archivo o directorio en concreto está causando el problema, ejecute `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` en Terminal, abra el archivo **sshd.log** desde el escritorio y busque "Authentication refused: bad ownership or modes" (Autenticación rechazada: propiedad o modos incorrectos).

#### <a name="trying-to-connect-never-completes"></a>"Intentando conectar..." nunca finaliza

- **Error [52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** : este problema puede darse en Xamarin 4.1 si **Shell de inicio de sesión**, en el menú contextual **Opciones avanzadas** del usuario de Mac, en **Preferencias del sistema&gt; Usuarios &amp; Grupos** , está establecido en otro valor que no sea **/bin/bash**. A partir de Xamarin 4.2, este caso corresponde al mensaje de error "No se pudo conectar". **Solución alternativa**: revierta el valor del **shell de inicio de sesión** al original predeterminado de **/bin/bash**.

<a name="tryagain"></a>

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>"Couldn't connect to MacBuildHost.local. Please try again." (Credenciales no válidas. Vuelva a intentarlo.)

Causas notificadas:

- **Error**: algunos usuarios han visto el mensaje de error "An unexpected error occurred while configuring SSH for the user… Session operation has timed out" (Se produjo un error inesperado durante la configuración de SSH para el usuario… Se agotó el tiempo de operación de sesión.) junto con un error más detallado en los archivos de registro al intentar iniciar sesión en el host de compilación mediante Active Directory u otra cuenta de usuario de dominio de servicio de directorios. **Solución:** inicie sesión en el host de compilación con una cuenta de usuario local.

- **Error**: algunos usuarios han visto este error al hacer doble clic en el nombre del equipo Mac en el cuadro de diálogo de conexión para intentar conectarse con el host de compilación. **Posible solución alternativa**: [agregue manualmente el equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac) mediante la dirección IP.

- **Error [35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)** : algunos usuarios se han encontrado con este error al usar una conexión de red inalámbrica entre el host de compilación de Mac y Windows. **Posible solución alternativa**: mueva ambos equipos a una conexión de red con cable.

- **Error [36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)** : en Xamarin 4.0, este mensaje aparecerá cada vez que el archivo **$HOME/.bashrc** del equipo Mac contenga un error. (A partir de Xamarin 4.1, los errores del archivo **.bashrc** no afectarán al proceso de conexión). **Solución alternativa**: mueva el archivo **.bashrc** a una ubicación de copia de seguridad (o elimínelo si sabe que no lo necesita).

- **Error [52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** : este error puede darse si **Shell de inicio de sesión**, en el menú contextual **Opciones avanzadas** del usuario de Mac en **Preferencias del sistema > Usuarios y Grupos** , está establecido en otro valor que no sea **/bin/bash**. **Solución alternativa**: revierta el valor del **shell de inicio de sesión** al original predeterminado de **/bin/bash**.

- **Limitación**: este error puede aparecer si el host de compilación de Mac está conectado a un enrutador que no tiene acceso a Internet (o si el equipo Mac usa un servidor DNS que agota el tiempo de espera cuando se pide la búsqueda de DNS inversa del equipo de Windows). Visual Studio tardará cerca de 30 segundos en recuperar la huella digital de SSH y finalmente producirá un error de conexión.

    **Posible solución alternativa**: agregue "UseDNS no" al archivo **sshd\_config**. Infórmese sobre esta configuración de SSH antes de cambiarla. Por ejemplo, consulte [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](https://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option).

    En los pasos siguientes se describe una manera de cambiar la configuración. Deberá haber iniciado sesión con una cuenta de administrador en el equipo Mac para completar los pasos.

    1. Confirme la ubicación del archivo **sshd\_config**. Para ello, ejecute `ls /etc/ssh/sshd_config` y `ls /etc/sshd_config` en un símbolo del sistema de Terminal. En los demás pasos, asegúrese de usar la ubicación que _no_ devuelva "No existe el archivo o directorio".

        [![Ejecución de "ls /etc/ssh/sshd_config" y "ls /etc/sshd_config" en Terminal](troubleshooting-images/troubleshooting-image18.png)](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. Ejecute `cp /etc/ssh/sshd_config "$HOME/Desktop/"` en Terminal para copiar el archivo en el escritorio.

    3. Abra el archivo desde el escritorio en un editor de texto. Por ejemplo, puede ejecutar `open -a TextEdit "$HOME/Desktop/sshd_config"` en Terminal.

    4. Agregue la línea siguiente en la parte inferior del archivo:

        ```
        UseDNS no
        ```

    5. Quite las líneas que contengan `UseDNS yes` para asegurarse de que la nueva configuración surta efecto.

    6. Guarde el archivo.

    7. Ejecute `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` en Terminal para copiar el archivo editado de nuevo en su lugar. Si se le solicita, escriba su contraseña.

    8. Deshabilite y vuelva a habilitar el **inicio de sesión remoto** en **Preferencias del sistema &gt; Compartir &gt; Inicio de sesión remoto** para reiniciar el servidor SSH.

<a name="clearing"></a>

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>Desactivar los agentes de Broker, IDB, compilación y diseñador en el equipo Mac

Si los archivos de registro muestran un problema durante los pasos de instalación, carga o inicio para cualquiera de los agentes de Mac, pruebe a eliminar la carpeta de caché **XMA** para obligar a Visual Studio a volver a cargarlos.

1. En el equipo Mac, ejecute el comando siguiente en Terminal:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. Pulse la tecla Control y haga clic en la carpeta **XMA** y, después, seleccione **Mover a la papelera**:

    [![Envío de la carpeta XMA a la Papelera](troubleshooting-images/troubleshooting-image8.png)](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. También puede resultar útil borrar una caché que hay en Windows. Abra un símbolo del sistema como administrador en Windows:

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>Mensajes de advertencia

En esta sección se describen algunos de los mensajes que pueden aparecer en las ventanas de salida y los registros y que normalmente se pueden pasar por alto.

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>"There is a mismatch between the installed Xamarin.iOS… and the local Xamarin.iOS" (Hay un error de coincidencia entre la versión de Xamarin.iOS instalada… y la versión local de Xamarin.iOS)

Siempre y cuando haya confirmado que el equipo Mac y el equipo Windows están actualizados con el mismo canal de distribución de Xamarin, puede pasar por alto esta advertencia.

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>"Failed to execute 'ls /usr/bin/mono': ExitStatus = 1" (No se pudo ejecutar "ls /usr/bin/mono": ExitStatus = 1)

Este mensaje se puede pasar por alto siempre y cuando el equipo Mac ejecute OS X 10.11 (El Capitan) o una versión posterior. Este mensaje no es un problema en OS X 10.11, ya que Xamarin también comprueba **/usr/local/bin/mono**, que es la ubicación correcta que se espera para `mono` en OS X 10.11.

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>"Bonjour service 'MacBuildHost' did not respond with its IP address." ("MacBuildHost" del servicio Bonjour no ha respondido con su dirección IP.)

Puede pasar por alto este mensaje a menos que vea que el cuadro de diálogo de conexión no muestra la dirección IP del host de la compilación de Mac. Si _falta_ la dirección IP en ese cuadro de diálogo, puede [agregar manualmente el equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac).

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>"Invalid user a from 10.1.8.95" (Usuario no válido a desde 10.1.8.95) y "input\_userauth\_request: invalid user a [preauth]" (input_userauth_request: usuario no válido a [autenticación previa])

Podría ver este mensaje si consulta el archivo **sshd.log**. Estos mensajes forman parte del proceso de conexión normal y aparecen porque Xamarin usa el nombre de usuario **a** temporalmente al recuperar la _huella digital de SSH_.

### <a name="output-window-and-log-files"></a>Ventana de salida y archivos de registro

Si Visual Studio produce un error al conectar con el host de compilación, hay dos ubicaciones en las que puede buscar mensajes adicionales: la ventana de salida y los archivos de registro.

#### <a name="output-window"></a>Resultados (Ventana)

La ventana de salida es el mejor lugar para comenzar. En ella se muestran mensajes sobre los principales pasos de conexión y errores. Para ver los mensajes de Xamarin en la ventana de salida:

1. Seleccione **Ver > Salida** en el menú o haga clic en la pestaña **Salida**.
2. En el menú de lista desplegable, haga clic en **Mostrar salida de**.
3. Seleccione **Xamarin**.

[![Selección de Xamarin en la pestaña Salida](troubleshooting-images/troubleshooting-image11.png)](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>Archivos de registro

Si la ventana de salida no incluye suficiente información para diagnosticar el problema, los archivos de registro son el siguiente lugar que debe consultar. Los archivos de registro contienen mensajes de diagnóstico adicionales que no aparecen en la ventana de salida. Para ver los archivos de registro:

1. Inicie Visual Studio.

    > [!IMPORTANT]
    > Tenga en cuenta que **.svclogs** no está habilitado de forma predeterminada. Para tener acceso, necesitará iniciar Visual Studio con registros detallados, como se explica en la guía [Registros de versión](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs). Para obtener más información, vea el blog [Troubleshooting Extensions with the Activity Log](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/) (Solución de problemas de las extensiones con el registro de actividad).

2. Intente conectarse al host de compilación.

3. Una vez que Visual Studio haya producido el error de conexión, recopile los registros en **Ayuda > Xamarin > Comprimir registros**:

    [![Recopilación de los registros en Ayuda > Xamarin > Registros de archivos ZIP](troubleshooting-images/troubleshooting-image12.png)](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. Cuando abra el archivo .zip, verá una lista de archivos similar al ejemplo siguiente. En el caso de los errores de conexión, los archivos más importantes son **\*Ide.log** y **\*Ide.svclog**. Estos archivos contienen los mismos mensajes en dos formatos ligeramente diferentes. **.svclog** es un archivo XML y resulta útil para examinar los mensajes. **.log** es un archivo de texto sin formato y es útil si quiere filtrar los mensajes mediante herramientas de línea de comandos.

    Para examinar todos los mensajes, seleccione y abra el archivo **.svclog**:

    [![Selección del archivo svclog](troubleshooting-images/troubleshooting-image13.png)](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. El archivo **.svclog** se abrirá en **Microsoft Service Trace Viewer**. Puede examinar los mensajes por subproceso para ver grupos de mensajes relacionados. Para examinar por subproceso, seleccione la pestaña **Gráfico**, haga clic en el menú desplegable **Modo de diseño** y seleccione **Subproceso**:

    [![Clic en el menú desplegable Modo de diseño y selección de Subproceso](troubleshooting-images/troubleshooting-image14.png)](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs"></a>

#### <a name="verbose-log-files"></a>Archivos de registro detallados

Si los archivos de registro normales todavía no proporcionan suficiente información para diagnosticar el problema, puede probar como último recurso habilitar el registro detallado. Los registros detallados también se prefieren para los informes de errores.

1. Salga de Visual Studio.

2. Inicie un [**símbolo del sistema para desarrolladores**](/dotnet/framework/tools/developer-command-prompt-for-vs).

3. Ejecute el comando siguiente en el símbolo del sistema para iniciar Visual Studio con el registro detallado:

    ```bash
    devenv /log
    ```

4. Intente conectarse al host de compilación desde Visual Studio.

5. Una vez que Visual Studio haya producido el error de conexión, recopile los registros en **Ayuda > Xamarin > Comprimir registros**.

6. Ejecute el comando siguiente en Terminal en el equipo Mac para copiar los mensajes de registro recientes del servidor SSH en un archivo en el escritorio:

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

Si estos archivos de registro detallado no proporcionan suficiente información para solucionar el problema directamente, [registre un nuevo informe de errores](https://bugzilla.xamarin.com/newbug) y adjunte el archivo .zip del paso 5 y el archivo .log del paso 6.

## <a name="troubleshooting-automatic-mac-provisioning"></a>Solución de problemas de aprovisionamiento automático del equipo Mac

### <a name="ide-log-files"></a>Archivos de registro del IDE

Si se produce algún problema con el [aprovisionamiento automático del equipo Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning), eche un vistazo a los registros del IDE de Visual Studio 2017, que se almacenan en **%LOCALAPPDATA%\Xamarin\Logs\15.0**.

## <a name="troubleshooting-build-and-deployment-errors"></a>Solución de problemas de errores de compilación e implementación

En esta sección se tratan algunos de los problemas que se pueden producir después de que Visual Studio se haya conectado correctamente al host de compilación.

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>"No se puede conectar a la dirección = '192.168.1.2:22 ' con el usuario = 'macuser'"

Causas conocidas:

- **Característica de seguridad de Xamarin 4.1**: este error _se producirá_ si cambia a la versión Xamarin 4.0 después de usar Xamarin 4.1 o una versión superior. En este caso, el error irá acompañado de la advertencia "Private key is encrypted but passphrase is empty" (La clave privada está cifrada, pero la frase de contraseña está vacía). Se trata de un cambio _intencional_ debido a una nueva característica de seguridad de Xamarin 4.1. **Corrección recomendada**: elimine **id\_rsa** e **id\_rsa.pub** de **%LOCALAPPDATA%\Xamarin\MonoTouch** y, después, vuelva a conectar con el host de compilación de Mac.

- **Restricción de seguridad de SSH**: cuando este mensaje va acompañado de la advertencia "Could not authenticate the user using the existing ssh keys" (No se pudo autenticar al usuario con las claves SSH existentes), la mayoría de las veces, este mensaje significa que uno de los archivos o directorios en la ruta de acceso completa **$HOME/.ssh/authorized\_keys** del equipo Mac tiene permisos de escritura habilitados para _otros_ miembros o miembros de _grupo_. **Corrección habitual**: ejecute `chmod og-w "$HOME"` en un símbolo del sistema de Terminal en el equipo Mac. Para obtener más información sobre qué archivo o directorio en concreto está causando el problema, ejecute `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` en Terminal, abra el archivo **sshd.log** desde el escritorio y busque "Authentication refused: bad ownership or modes" (Autenticación rechazada: propiedad o modos incorrectos).

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>No se puede cargar soluciones desde un recurso compartido de red

Las soluciones solo se compilarán si están en el sistema de archivos local de Windows o en una unidad asignada.

Las soluciones que estén guardadas en un recurso compartido de red podrían producir errores o directamente no compilarse. Los archivos **.sln** que se usen en Visual Studio se deben guardar en el sistema de archivos local de Windows.

Debido a este problema, se produce el siguiente error:

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

Error relacionado: [36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>Faltan perfiles de aprovisionamiento o error "Failed to create the a fat library" (No se pudo crear la biblioteca fat)

Inicie Xcode en el equipo Mac y asegúrese de que ha iniciado sesión con su cuenta de desarrollador de Apple y que se ha descargado su perfil de desarrollo de iOS:

[![Comprobación de que la cuenta de desarrollador de Apple se haya registrado y de que el perfil de desarrollo de iOS se haya descargado](troubleshooting-images/troubleshooting-image7.png)](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>"Se intentó realizar una operación de socket en una red inaccesible"

Causas notificadas:

- **Mejora [36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)** : este error puede impedir que las compilaciones se lleven a cabo correctamente cuando Visual Studio usa una dirección IPv6 para conectarse con el host de compilación. (La conexión del host de compilación todavía no admite direcciones IPv6).

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>El complemento de Visual Studio de Xamarin.iOS no se puede cargar después de la reinstalación del canal beta/alfa

Corrección pertinente: [40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781).

Este problema puede ocurrir si se produce un error cuando Visual Studio actualiza la caché de componentes de MEF. Si es así, instalar esta extensión de Visual Studio puede ser de ayuda: [https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)

De este modo, se borrará la caché de componentes de MEF de Visual Studio para solucionar los problemas relacionados con daños en la caché.

<a name="errors"></a>

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>Errores debidos a los procesos de host de compilación existentes en el equipo Mac

En ocasiones, los procesos de conexiones anteriores de host de compilación pueden interferir en el comportamiento de la conexión activa actual. Para comprobar los procesos existentes, cierre Visual Studio y, después, ejecute los comandos siguientes en Terminal en el equipo Mac:

```bash
ps -A | grep mono
```

[![Ejecución de comandos en Terminal en el equipo Mac](troubleshooting-images/troubleshooting-image10.png)](troubleshooting-images/troubleshooting-image10.png#lightbox)

Para terminar los procesos existentes, use el siguiente comando:

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>Borrar la caché de compilación de Mac

Si está solucionando un problema de compilación y quiere asegurarse de que el comportamiento no esté relacionado con ningún archivo de compilación temporal almacenado en el equipo Mac, puede eliminar la carpeta de caché de compilación.

1. En el equipo Mac, ejecute el comando siguiente en Terminal:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. Pulse la tecla Control y haga clic en la carpeta **mtbs** y, después, seleccione **Mover a la papelera**:

    [![Envío de la carpeta mtbs a la Papelera](troubleshooting-images/troubleshooting-image9.png)](troubleshooting-images/troubleshooting-image9.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Vídeo del agente de compilación de Mac de Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)