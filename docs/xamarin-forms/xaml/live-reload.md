---
title: Recarga en vivo de Xamarin (versión preliminar)
description: Vea los cambios en el código XAML reflejado en directo, sin necesidad de otro compilar e implementar.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b67594e2675c774512f3bf64f2e91ef10dbff444
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84134217"
---
# <a name="xamarin-live-reload-preview"></a>Recarga en vivo de Xamarin (versión preliminar)

> [!NOTE]
> La vista previa de la recarga en vivo de Xamarin ha finalizado y queremos agradecer a todos sus comentarios y comentarios. 
>
> Para editar el código XAML mientras se ejecuta la aplicación, use la [recarga activa de Xamarin.Forms XAML para ](~/xamarin-forms/xaml/hot-reload.md).
>

La recarga en vivo de Xamarin permite **realizar cambios en el código XAML y verlos reflejados en directo, sin necesidad de volver a compilar e implementar**. Cualquier cambio realizado en el código XAML se volverá a implementar al guardarlo y se reflejará en el destino de implementación.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 versión 15,7 o superior](https://visualstudio.microsoft.com/vs/) con la carga de trabajo **desarrollo para dispositivos móviles con .net** .
* [ Xamarin.Forms 3.0.0 o superior](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Introducción
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instale la recarga en vivo de Xamarin desde el Visual Studio Marketplace

La recarga en vivo de Xamarin se distribuye a través de la Visual Studio Marketplace. Para instalar la extensión, visite la [Página de recarga de Xamarin Live en el sitio web de Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) y haga clic en **Descargar**.

Abra el. vsix que se descarga y haga clic en **instalar**.

![Confirmación de la recarga en directo de Xamarin de Visual Studio Installer](images/LiveReloadVSIXInstall.png)

Como alternativa, puede buscarlo en la pestaña en **línea** del cuadro de diálogo **extensiones y actualizaciones** dentro de Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. configurar la aplicación para que use la recarga en vivo

La adición de recarga en vivo a las aplicaciones móviles existentes se puede realizar en tres pasos:

1. Asegúrese de que todos los proyectos se han actualizado para usar [ Xamarin.Forms 3.0.0 o superior](https://www.nuget.org/packages/Xamarin.Forms/) o superior.

2. Agregue el paquete NuGet **Xamarin. LiveReload** :

    a. **.Net Standard** : Instale el NuGet **Xamarin. LiveReload** en la biblioteca .net Standard 2,0. No es necesario instalarlo en los proyectos de la plataforma. Asegúrese de que el **origen del paquete** está establecido en **todos**.
    
    b. **Proyectos compartidos** : Instale el NuGet **Xamarin. LiveReload** en todos los proyectos de la plataforma (como Android, iOS, UWP, etc.). Asegúrese de que el **origen del paquete** está establecido en **todos**.

    [![Agregar la recarga en vivo de Xamarin con el administrador de paquetes NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Agregue `LiveReload.Init();` al constructor en la `Application` clase, como se muestra en el siguiente fragmento de código:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. iniciar la recarga en vivo

Compile e implemente la aplicación. Una vez implementada la aplicación, abra un archivo XAML, realice algunos cambios y guarde el archivo. Los cambios se reimplementan en el destino de implementación.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

La recarga en vivo funciona con cambios en cualquier archivo XAML. Los cambios en C# o la adición o eliminación de paquetes NuGet requieren una nueva compilación e implementación para que surtan efecto.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>¿Está disponible la recarga en vivo de Xamarin en Visual Studio para Mac? 

No, la versión preliminar de la recarga en vivo de Xamarin solo está disponible para Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>¿Funciona con todas las bibliotecas, como Prism? 

Dado que la aplicación está compilada, la recarga en vivo funciona con todas las bibliotecas, como Prism, y con bibliotecas de control de terceros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity y otros proveedores de control.

### <a name="what-changes-does-live-reload-redeploy"></a>¿Qué cambios vuelve a implementar la recarga en directo? 

La recarga en vivo solo aplica los cambios realizados en XAML o CSS. Si realiza cambios en un archivo de C#, será necesario volver a compilar. 

### <a name="what-platforms-are-supported"></a>Plataformas compatibles 

La recarga en vivo funciona en cualquier plataforma compatible con Xamarin.Forms , incluidos Android, iOS y UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>¿Funciona en emuladores, simuladores y dispositivos físicos? 

Sí, la recarga en vivo funciona con todos los destinos de implementación válidos, incluidos los emuladores de Android, los simuladores de iOS y los dispositivos físicos. La implementación en un dispositivo requiere que el dispositivo y el equipo estén en la misma red Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>¿Funciona con redes corporativas?

Si está depurando en un emulador de Android o en un simulador de iOS, la recarga en vivo usa localhost para comunicarse. Si desea realizar la implementación en un dispositivo, el dispositivo y el equipo deben estar en la misma red Wi-Fi. En escenarios en los que esto no sea posible, puede [configurar su propio servidor de recarga en vivo](#live-reload-server), lo que le permitirá realizar una recarga en vivo, independientemente de la configuración de conectividad de la red.

### <a name="does-it-require-debugging-the-app"></a>¿Necesita depurar la aplicación? 

No. De hecho, puede incluso iniciar todos los destinos de aplicación admitidos (Android, iOS y UWP) en cualquier número de dispositivos o simuladores/emuladores y verlos todas las actualizaciones a la vez. 

## <a name="limitations"></a>Limitaciones

* Solo se admite la recarga de XAML.
* No se puede mantener el estado de la interfaz de usuario entre reimplementaciones, a menos que se use MVVM.

## <a name="known-issues"></a>Problemas conocidos

* Solo se admite en Visual Studio.
* La vinculación se debe establecer para **no vincular** o **vincular SDK de Framework únicamente** 
* Volver a cargar los recursos de toda la aplicación (es decir, **app. Xaml** o diccionarios de recursos compartidos), se restablece la navegación de la aplicación. 
* La recarga de ContentView requiere actualmente la recarga de la página contenedora.
* Los elementos que contienen AutomationId pueden producir un error de recarga.
* La edición de XAML durante la depuración de UWP puede producir un bloqueo en tiempo de ejecución. Solución alternativa: use **iniciar sin depurar (Ctrl + F5)** en lugar de **iniciar depuración (F5)**.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="error-codes"></a>Códigos de error

* **XLR001**: *el proyecto actual hace referencia a la versión del paquete NuGet ' Xamarin. LiveReload ', pero la extensión de la recarga activa de Xamarin requiere la versión ' [Version] '.*

  Con el fin de permitir la iteración rápida y la evolución de la característica de recarga en directo, el paquete NuGet y la extensión de Visual Studio deben coincidir exactamente. Actualice el paquete NuGet a la misma versión de la extensión que ha instalado.

* **XLR002**: *la recarga en vivo requiere al menos la propiedad ' MqttHostname ' al compilar desde la línea de comandos. También puede establecer ' EnableLiveReload ' en ' false ' para deshabilitar la característica.*

  Las propiedades requeridas por la recarga en directo no están disponibles al compilar desde la línea de comandos (o en la integración continua) y, por tanto, se deben proporcionar explícitamente. 

* **XLR003**: *el paquete NuGet de recarga en vivo requiere la instalación de la extensión de Visual Studio de Xamarin Live reload.*

  Se intentó compilar un proyecto que hace referencia al paquete NuGet de recarga en directo, pero la extensión visual no está instalada.  

* *Excepción al cargar ensamblados: System. IO. FileNotFoundException: no se pudo cargar el ensamblado ' Xamarin. Live. Reload, version = 0.3.27.0, Culture = neutral, PublicKeyToken = '.*

  El proyecto host debe usar `PackageReference` en lugar de`packages.config`

### <a name="app-doesnt-connect"></a>La aplicación no se conecta

Cuando se compila la aplicación, la información de **herramientas > opciones > Xamarin > la recarga en vivo** (nombre de host, puerto y claves de cifrado) se insertan en la aplicación, de modo que `LiveReload.Init()` , cuando se ejecuta, no es necesario ningún emparejamiento ni configuración para que la conexión se realice correctamente.

Aparte de los problemas de red normales (firewall, dispositivo en una red diferente), el motivo principal por el que la aplicación puede no conectarse correctamente al IDE es porque su configuración difiere de la de Visual Studio. Esto puede ocurrir si:

* La aplicación se compiló en otro equipo.
* La aplicación se compiló e implementó en una sesión de Visual Studio diferente y se comprueba la **generación automática de claves de cifrado** (valor predeterminado) en **herramientas > opciones > Xamarin > la recarga en vivo**.
* Se cambió la configuración de Visual Studio (es decir, el nombre de host, el puerto o las claves de cifrado), pero la aplicación no se compiló y se implementó de nuevo.

Estos casos se solucionan al compilar e implementar la aplicación de nuevo.

### <a name="uninstalling-preview-1"></a>Desinstalación de Preview 1

Si tiene una versión preliminar anterior y tiene problemas para desinstalarla, siga estos pasos:

1. Elimine la carpeta C:\Archivos de programa **(x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: reemplace "Enterprise" por la edición instalada y "Preview" por "2017" Si instaló en una versión estable frente a)
2. Abra un **símbolo del sistema para desarrolladores** de Visual Studio y ejecute `devenv /updateconfiguration` . 

## <a name="tips--tricks"></a>Sugerencias & trucos

* Siempre y cuando la configuración de recarga en directo no cambie (incluidas las claves de cifrado, por ejemplo, si desactiva la **generación automática de claves de cifrado**) y se compila desde el mismo equipo, no es necesario compilar e implementar la aplicación después de la implementación inicial, a menos que cambies el código o las dependencias. Simplemente puede volver a iniciar una aplicación implementada previamente y se conectará al último host usado.

* No hay ninguna limitación en cuanto al número de dispositivos que se pueden conectar a la misma sesión de Visual Studio. Puede implementar e iniciar la aplicación en tantos dispositivos/simuladores como sea necesario para ver la recarga en directo que funciona en todos ellos al mismo tiempo.

* La recarga en vivo solo volverá a cargar la parte de la interfaz de usuario de la aplicación, pero *no* volverá a crear las páginas ni reemplazará el modelo de vista (o el contexto de enlace). Esto significa que *todo* el estado de la aplicación siempre se conserva entre las recargas, incluidas las dependencias insertadas.

## <a name="live-reload-server"></a>Servidor de recarga en vivo

En escenarios en los que no es posible establecer una conexión desde la aplicación en ejecución a su equipo (como se indica mediante el uso `localhost` `127.0.0.1` de o en **herramientas > opciones > Xamarin > la recarga en vivo**) (es decir, firewalls, redes diferentes), puede configurar un servidor remoto en su lugar, al que el IDE y la aplicación se conectarán.

La recarga en vivo usa el [Protocolo MQTT](https://mqtt.org/) estándar para intercambiar mensajes y, por tanto, se puede comunicar con [servidores de terceros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Hay incluso [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (también conocidos como *agentes*) disponibles que puede usar. La recarga en vivo se ha probado `broker.hivemq.com` con `iot.eclipse.org` los nombres de host y y, así como con los servicios proporcionados por [www.cloudmqtt.com](https://www.cloudmqtt.com) y [www.cloudamqp.com](https://www.cloudamqp.com). También puede implementar su propio servidor de MQTT en la nube, como [HiveMQ en Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Puede configurar cualquier puerto, pero es habitual usar el puerto predeterminado 1883 para los servidores remotos. Los mensajes de recarga en vivo usan el cifrado simétrico AES completo de un extremo a otro, por lo que es seguro conectarse a los servidores remotos. De forma predeterminada, tanto la clave de cifrado como el vector de inicialización (IV) se vuelven a generar en cada sesión de Visual Studio.

Probablemente, la manera más fácil es instalar el servidor de [mosquitto](https://mosquitto.org) en una máquina virtual Ubuntu en blanco en Azure:

1. Creación de una nueva máquina virtual de Ubuntu Server en Azure portal
2. Agregar una nueva regla de puerto de entrada para 1883 (puerto MQTT predeterminado) en la pestaña redes
3. Abrir el [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modo Bash)
4. Escriba `ssh [USERNAME]@[PUBLIC_IP]` con el nombre de usuario que eligió en 1) y la dirección IP pública que se muestra en la página de información general de la máquina virtual.
5. Ejecute `sudo apt-get install mosquitto` y escriba la contraseña que eligió en 1)

Ahora puede usar esa dirección IP para conectarse a su propio servidor de MQTT.
