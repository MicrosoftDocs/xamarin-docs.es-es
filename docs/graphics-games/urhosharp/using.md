---
title: Uso de UrhoSharp para compilar un juego en 3D
description: En este documento se proporciona información general de UrhoSharp, donde se describen las escenas, los componentes, las formas, las cámaras, las acciones, los datos proporcionados por el usuario, el sonido, etc.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f9ea9c4f6f2c920d903bd6f136dd0b98ddc7bf57
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574332"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Uso de UrhoSharp para compilar un juego en 3D

Antes de escribir su primer juego, desea familiarizarse con los conceptos básicos: Cómo configurar la escena, cómo cargar recursos (esto contiene la ilustración) y cómo crear interacciones sencillas para su juego.

<a name="scenenodescomponentsandcameras"></a>

## <a name="scenes-nodes-components-and-cameras"></a>Escenas, nodos, componentes y cámaras

El modelo de escena puede describirse como un gráfico de escenas basado en componentes. La escena se compone de una jerarquía de nodos de la escena, empezando por el nodo raíz, que también representa toda la escena. Cada una `Node` tiene una transformación 3D (posición, rotación y escala), un nombre, un identificador, más un número arbitrario de componentes.  Los componentes ponen un nodo en vida, pueden hacer que agregue una representación visual ( `StaticModel` ), pueden emitir sonidos ( `SoundSource` ), pueden proporcionar un límite de colisión, etc.

Puede crear los nodos de instalación y de escenas mediante el [Editor de Urho](#urhoeditor), o bien puede hacer cosas desde el código de C#.  En este documento exploraremos la configuración de las cosas con el código, ya que ilustran los elementos necesarios para que se muestren en la pantalla.

Además de configurar la escena, debe configurar un `Camera` , que es lo que determina lo que se mostrará al usuario.

### <a name="setting-up-your-scene"></a>Configuración de la escena

Normalmente crearía este formulario en el método de Inicio:

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>Componentes

La representación de objetos 3D, la reproducción de sonido, las actualizaciones de lógica y de lógica con scripts se habilitan al crear componentes diferentes en los nodos mediante una llamada a `CreateComponent<T>()` .  Por ejemplo, configure el nodo y el componente de luz del modo siguiente:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Hemos creado sobre un nodo con el nombre " `DirectionalLight` " y hemos establecido una dirección para él, pero nada más.  Ahora, podemos convertir el nodo anterior en un nodo de emisión de luz, adjuntando un `Light` componente a él, con `CreateComponent` :

```csharp
var light = lightNode.CreateComponent<Light>();
```

Los componentes creados en el `Scene` propio tienen un rol especial: para implementar la funcionalidad de toda la escena. Deben crearse antes que todos los demás componentes e incluir lo siguiente:

 `Octree`: implementa el particionamiento espacial y las consultas de visibilidad acelerada. Sin estos objetos 3D no se pueden representar.
 `PhysicsWorld`: implementa la simulación física. Los componentes de física como `RigidBody` o `CollisionShape` no pueden funcionar correctamente sin este.
 `DebugRenderer`: implementa la representación de la geometría de depuración.

Componentes ordinarios como `Light` , `Camera` o`StaticModel`
no debe crearse directamente en el `Scene` , sino en los nodos secundarios.

La biblioteca incluye una amplia variedad de componentes que se pueden adjuntar a los nodos para que tengan vida: elementos visibles para el usuario (modelos), sonidos, cuerpos rígidos, formas de colisión, cámaras, fuentes luminosas, emisores de partículas y mucho más.

### <a name="shapes"></a>Formas

Por comodidad, hay varias formas disponibles como nodos simples en el espacio de nombres Urho. Shapes.  Estos incluyen cajas, esferas, conos, cilindros y aviones.

### <a name="camera-and-viewport"></a>Cámara y ventanilla

Al igual que la luz, las cámaras son componentes, por lo que tendrá que adjuntar el componente a un nodo, de la siguiente manera:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con esto, ha creado una cámara y ha colocado la cámara en el mundo 3D. el siguiente paso es informar de que se trata de `Application` la cámara que quiere usar; esto se hace con el código siguiente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

Y ahora debería poder ver los resultados de la creación.

### <a name="identification-and-scene-hierarchy"></a>Identificación y jerarquía de escenas

A diferencia de los nodos, los componentes no tienen nombres; los componentes que se encuentran dentro del mismo nodo solo se identifican por su tipo, y el índice de la lista de componentes del nodo, que se rellena en orden de creación, por ejemplo, puede recuperar el `Light` componente fuera del `lightNode` objeto anterior, como se indica a continuación:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

También puede obtener una lista de todos los componentes mediante la recuperación de la `Components` propiedad, que devuelve un `IList<Component>` que se puede usar.

Cuando se crean, los dos nodos y los componentes obtienen los identificadores enteros de la escena. Se pueden consultar desde la escena mediante las funciones `GetNode(uint id)` y `GetComponent(uint id)` . Esto es mucho más rápido que, por ejemplo, las consultas de nodo de escena basadas en nombres recursivos.

No hay ningún concepto integrado de una entidad o un objeto de juego; en su lugar, depende del programador decidir la jerarquía de nodos y en qué nodos colocar cualquier lógica de script. Normalmente, los objetos de movimiento libre en el mundo 3D se crearán como elementos secundarios del nodo raíz. Los nodos se pueden crear con o sin nombre mediante `CreateChild` . No se exige la unicidad de los nombres de nodo.

Siempre que hay una composición jerárquica, se recomienda (y, de hecho, es necesario que los componentes no tengan sus propias transformaciones 3D) para crear un nodo secundario.

Por ejemplo, si un carácter contiene un objeto en su mano, el objeto debe tener su propio nodo, que sería primario del hueso del carácter (también un `Node` ).  La excepción es la física `CollisionShape` , que puede ser offsetted y girada individualmente en relación con el nodo.

Tenga en cuenta que `Scene` la transformación propia se omite de forma intencionada como una optimización al calcular las transformaciones derivadas del mundo de los nodos secundarios, por lo que cambiar no tiene ningún efecto y debe dejarse tal como está (posición en el origen, sin giro, sin escala).

`Scene`los nodos pueden cambiarse de forma gratuita. En contraste, los componentes siempre pertenecen al nodo al que se adjuntan y no se pueden desplazar entre los nodos. Tanto los nodos como los componentes proporcionan una `Remove` función para hacerlo sin tener que pasar por el elemento primario. Una vez que se quita el nodo, no se pueden realizar operaciones en el nodo o componente en cuestión después de llamar a esa función.

También es posible crear un que no `Node` pertenezca a una escena. Esto resulta útil, por ejemplo, cuando una cámara se mueve en una escena que se puede cargar o guardar, ya que la cámara no se guardará junto con la escena real y no se destruirá cuando se cargue la escena. Sin embargo, tenga en cuenta que la creación de componentes de geometría, física o de script en un nodo no adjuntado y, después, moverlos a una escena más adelante hará que esos componentes no funcionen correctamente.

### <a name="scene-updates"></a>Actualizaciones de escenas

Una escena cuyas actualizaciones estén habilitadas (valor predeterminado) se actualizarán automáticamente en cada iteración del bucle principal.  Se invoca el controlador de eventos de la aplicación `SceneUpdate` .

Los nodos y componentes se pueden excluir de la actualización de la escena al deshabilitarlos, vea `Enabled` .  El comportamiento depende del componente específico, pero, por ejemplo, al deshabilitar un componente que se puede dibujar también hace que sea invisible, mientras que al deshabilitar un componente de origen de sonido se silencia. Si un nodo está deshabilitado, todos sus componentes se tratan como deshabilitados, independientemente de su propio estado de habilitación o deshabilitación.

## <a name="adding-behavior-to-your-components"></a>Agregar el comportamiento a los componentes

La mejor forma de estructurar el juego es crear su propio componente que encapsule un actor o un elemento en el juego.  Esto hace que la característica sea independiente, desde los recursos usados para mostrarla, hasta su comportamiento.

La manera más sencilla de agregar el comportamiento a un componente es usar acciones, que son instrucciones que se pueden poner en cola y combinar con la programación asincrónica de C#.  Esto permite que el comportamiento del componente sea muy alto y simplifica la comprensión de lo que está ocurriendo.

Como alternativa, puede controlar exactamente lo que le sucede a su componente actualizando las propiedades del componente en cada fotograma (descrito en la sección comportamiento basado en marcos).

### <a name="actions"></a>Acciones

Puede Agregar el comportamiento a los nodos fácilmente con las acciones.  Las acciones pueden modificar varias propiedades de nodo y ejecutarlas a lo largo de un período de tiempo, o bien repetirlas varias veces con una curva de animación determinada.

Por ejemplo, Imagine un nodo de "nube" en la escena, puede atenuarlo de la siguiente manera:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Las acciones son objetos inmutables, lo que permite volver a usar la acción para conducir distintos objetos.

Una expresión común consiste en crear una acción que realice la operación inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

En el ejemplo siguiente se atenua el objeto en un período de tres segundos.  También puede ejecutar una acción después de otra, por ejemplo, puede moverla primero y, a continuación, ocultarla:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Si desea que ambas acciones se realicen al mismo tiempo, puede usar la acción paralela y proporcionar todas las acciones que desea realizar en paralelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

En el ejemplo anterior, la nube se moverá y desaparecerá al mismo tiempo.

Observará que se usan C# `await` , lo que le permite pensar de forma lineal sobre el comportamiento que desea lograr.

### <a name="basic-actions"></a>Acciones básicas

Estas son las acciones que se admiten en UrhoSharp:

- Mover nodos: `MoveTo` , `MoveBy` , `Place` , `BezierTo` , `BezierBy` , `JumpTo` ,`JumpBy`
- Girar nodos: `RotateTo` ,`RotateBy`
- Escalar nodos: `ScaleTo` ,`ScaleBy`
- Nodos de difuminación: `FadeIn` , `FadeTo` , `FadeOut` , `Hide` ,`Blink`
- Maticing: `TintTo` ,`TintBy`
- Instantáneas: `Hide` , `Show` , `Place` , `RemoveSelf` ,`ToggleVisibility`
- Bucle: `Repeat` , `RepeatForever` ,`ReverseTime`

Otras características avanzadas incluyen la combinación de las `Spawn` `Sequence` acciones y.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Aceleración: control de la velocidad de las acciones

La aceleración es una manera que dirige la manera en que se desdoblará la animación y puede hacer que las animaciones sean mucho más agradables.  De forma predeterminada, las acciones tendrán un comportamiento lineal; por ejemplo, una `MoveTo` acción tendría un movimiento robótico.  Puede ajustar las acciones en una acción de entrada y salida lenta para cambiar el comportamiento, por ejemplo, uno que inicie lentamente el movimiento, acelere y llegue lentamente al final ( `EasyInOut` ).

Para ello, ajuste una acción existente en una acción de entradas y salidas lentas, por ejemplo:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Hay muchos modos de aceleración, el siguiente gráfico muestra los distintos tipos de aceleración y su comportamiento en el valor del objeto que controlan durante el período de tiempo, de principio a fin:

![Modos de aceleración](using-images/easing.png "Este gráfico muestra los distintos tipos de aceleración y su comportamiento en el valor del objeto que controlan durante el período de tiempo")

### <a name="using-actions-and-async-code"></a>Usar acciones y código asincrónico

En la `Component` subclase, debe introducir un método asincrónico que prepare el comportamiento del componente y la funcionalidad para él.
A continuación, debe invocar este método con la `await` palabra clave de C# desde otra parte del programa, ya sea el `Application.Start` método o como respuesta a un punto de usuario o caso de la aplicación.

Por ejemplo:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

En el `Launch` método anterior, se inician tres acciones: el robot entra en la escena, esta acción modificará la ubicación del nodo en un período de 0,6 segundos.  Dado que se trata de una opción asincrónica, esto ocurrirá al mismo tiempo que la siguiente instrucción que es la llamada a `MoveRandomly` .  Este método modificará la posición del robot en paralelo a una ubicación aleatoria.  Esto se logra mediante la realización de dos acciones compuestas, el movimiento a una nueva ubicación y la vuelta a la posición original y la repite siempre que el robot permanezca activo.  Y para hacer cosas más interesantes, el robot seguirá filmando simultáneamente.  La filmación solo se iniciará cada 0,1 segundos.

### <a name="frame-based-behavior-programming"></a>Programación del comportamiento basado en marcos

Si desea controlar el comportamiento del componente fotograma a fotograma en lugar de usar acciones, lo que haría es invalidar el `OnUpdate` método de la `Component` subclase.  Este método se invoca una vez por fotograma y solo se invoca si se establece la propiedad ReceiveSceneUpdates en true.

A continuación se muestra cómo crear un `Rotator` componente, que se adjunta a un nodo, que hace que el nodo rote:

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

Y esto es cómo asociaría este componente a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinar estilos

Puede usar el modelo basado en Async/Action para programar gran parte del comportamiento que es ideal para el estilo de programación de desencadenar y olvidar, pero también puede ajustar el comportamiento del componente para ejecutar también código de actualización en cada fotograma.

Por ejemplo, en la demostración de SamplyGame se usa en la `Enemy` clase codifica el comportamiento básico que usa las acciones, pero también garantiza que los componentes apunten hacia el usuario estableciendo la dirección del nodo con `Node.LookAt` :

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>Cargar y guardar escenas

Las escenas se pueden cargar y guardar en formato XML. Vea las funciones `LoadXml` y `SaveXML` . Cuando se carga una escena, primero se quita todo el contenido existente (los nodos y componentes secundarios). Los nodos y componentes marcados como temporales con la `Temporary` propiedad no se guardarán. El serializador controla todos los componentes y propiedades integrados, pero no es lo suficientemente inteligente como para controlar las propiedades y los campos personalizados definidos en las subclases de componentes. Sin embargo, proporciona dos métodos virtuales para ello:

 `OnSerialize`Dónde puede registrar los Estados personalizados para la serialización

 `OnDeserialized`Dónde puede obtener los Estados personalizados guardados.

Normalmente, un componente personalizado tendrá un aspecto similar al siguiente:

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>Objeto Prefabs

Simplemente cargar o guardar escenas enteras no es lo suficientemente flexible como para juegos en los que es necesario crear dinámicamente nuevos objetos. Por otro lado, la creación de objetos complejos y el establecimiento de sus propiedades en el código también serán tediosas. Por este motivo, también es posible guardar un nodo de escena que incluirá sus nodos secundarios, componentes y atributos. Estos se pueden cargar más adelante como un grupo.  Este tipo de objeto guardado a menudo se conoce como recurso prefabricado. Hay tres formas de hacerlo:

- En el código mediante una llamada a `Node.SaveXml` en el nodo
- En el editor, seleccione el nodo en la ventana jerarquía y elija "guardar nodo como" en el menú "archivo".
- Mediante el comando "node" de `AssetImporter` , que guardará la jerarquía de nodos de la escena y los modelos contenidos en el recurso de entrada (p. ej., un archivo Collada)

Para crear una instancia del nodo guardado en una escena, llame a `InstantiateXml` . El nodo se creará como un elemento secundario de la escena, pero puede cambiarse de forma gratuita después. Es necesario especificar la posición y la rotación para colocar el nodo. En el código siguiente se muestra cómo crear una instancia de recurso prefabricado `Ninja.xm` en una escena con la posición y la rotación deseadas:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Eventos

UrhoObjects provocan un número de eventos, que se muestran como eventos de C# en las diversas clases que los generan.  Además del modelo de eventos basado en C#, también es posible usar los `SubscribeToXXX` métodos que le permitirán suscribirse y mantener un token de suscripción que podrá usar posteriormente para cancelar la suscripción.  La diferencia es que el primero permite que se suscriban muchos llamadores, mientras que el segundo solo lo permite, pero permite el uso del enfoque de estilo lambda más agradable y, sin embargo, permite quitar fácilmente la suscripción.  Son mutuamente excluyentes.

Al suscribirse a un evento, debe proporcionar un método que tome un argumento con los argumentos de evento adecuados.

Por ejemplo, este es el procedimiento para suscribirse a un evento de presionar el botón del mouse:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Con estilo lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

A veces, querrá dejar de recibir notificaciones para el evento, en esos casos, guardar el valor devuelto de la llamada al `SubscribeTo` método e invocar el método de cancelación de suscripción en él:

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

El parámetro recibido por el controlador de eventos es una clase de argumentos de evento fuertemente tipados que será específica de cada evento y contendrá la carga del evento.

## <a name="responding-to-user-input"></a>Responder a los datos proporcionados por el usuario

Puede suscribirse a varios eventos, como pulsaciones de teclas por debajo de la suscripción al evento y responder a la entrada que se está entregando:

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Pero en muchos escenarios, desea que los controladores de actualización de la escena comprueben el estado actual de las claves cuando se estén actualizando y actualice el código en consecuencia.  Por ejemplo, se puede utilizar lo siguiente para actualizar la ubicación de la cámara en función de la entrada del teclado:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Recursos (activos)

Los recursos incluyen la mayoría de los elementos de UrhoSharp que se cargan desde el almacenamiento masivo durante la inicialización o en tiempo de ejecución:

- `Animation`-se usa para animaciones esquemáticas
- `Image`: representa imágenes almacenadas en diversos formatos gráficos
- `Model`-Modelos 3D
- `Material`: materiales utilizados para representar modelos.
- `ParticleEffect`- [describe](https://urho3d.github.io/documentation/1.4/_particles.html) cómo funciona un emisor de partículas, vea "[partículas](#particles)" a continuación.
- `Shader`-sombreadores personalizados
- `Sound`-sonidos para la reproducción, consulte "[sonido](#sound)" a continuación.
- `Technique`-técnicas de representación de materiales
- `Texture2D`-textura 2D
- `Texture3D`-textura 3D
- `TextureCube`-Textura del cubo
- `XmlFile`

Los administra y carga el `ResourceCache` subsistema (disponible como `Application.ResourceCache` ).

Los propios recursos se identifican por sus rutas de acceso de archivo, con respecto a los archivos de paquete o directorios de recursos registrados. De forma predeterminada, el motor registra los directorios de recursos `Data` y `CoreData` , o los paquetes `Data.pak` y `CoreData.pak` si existen.

Si se produce un error al cargar un recurso, se registrará un error y se devolverá una referencia nula.

En el ejemplo siguiente se muestra una forma típica de capturar un recurso de la caché de recursos.  En este caso, una textura para un elemento de la interfaz de usuario, utiliza la `ResourceCache` propiedad de la `Application` clase.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Los recursos también se pueden crear manualmente y almacenarse en la memoria caché de recursos como si se hubieran cargado desde el disco.

Los presupuestos de memoria se pueden establecer por tipo de recurso: si los recursos consumen más memoria de la permitida, los recursos más antiguos se quitarán de la memoria caché si ya no están en uso. De forma predeterminada, los presupuestos de memoria se establecen en ilimitado.

### <a name="bringing-3d-models-and-images"></a>Incorporación de modelos 3D e imágenes

Urho3D intenta usar los formatos de archivo existentes siempre que sea posible, y define los formatos de archivo personalizados solo cuando sea absolutamente necesario, como en el caso de los modelos (. MDL) y para las animaciones (. ANI). Para estos tipos de activos, Urho proporciona un convertidor- [AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) que puede consumir muchos formatos 3D conocidos como FBX, DAE, 3DS y obj, etc.

También hay un complemento útil para [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) el mezclador que puede exportar los recursos del mezclador en el formato adecuado para Urho3D.

### <a name="background-loading-of-resources"></a>Carga en segundo plano de recursos

Normalmente, al solicitar recursos con uno de los `ResourceCache` métodos de `Get` , se cargan inmediatamente en el subproceso principal, que puede tardar varios milisegundos en realizar todos los pasos necesarios (Cargar archivo desde el disco, analizar datos, cargar en GPU si es necesario) y, por tanto, puede dar lugar a caídas de velocidad de fotogramas.

Si sabe de antemano qué recursos necesita, puede solicitar que se carguen en un subproceso en segundo plano mediante una llamada a `BackgroundLoadResource` . Puede suscribirse al evento cargado en segundo plano de recursos mediante el `SubscribeToResourceBackgroundLoaded` método. le indicará si la carga se ha realizado correctamente o si se ha producido un error. En función del recurso, solo una parte del proceso de carga puede moverse a un subproceso en segundo plano; por ejemplo, el paso finalizar carga de GPU siempre debe aparecer en el subproceso principal. Tenga en cuenta que si llama a uno de los métodos de carga de recursos para un recurso que está en cola para la carga en segundo plano, el subproceso principal se detendrá hasta que se complete la carga.

La funcionalidad asincrónica de carga de escenas `LoadAsync` y `LoadAsyncXML` tiene la opción de cargar en segundo plano los recursos antes de continuar para cargar el contenido de la escena. También se puede usar para cargar solo los recursos sin modificar la escena, especificando `LoadMode.ResourcesOnly` . Esto permite preparar un archivo de recurso prefabricado de escena u objeto para la creación de instancias rápida.

Por último, el tiempo máximo (en milisegundos) empleado en cada fotograma al finalizar los recursos cargados en segundo plano puede configurarse estableciendo la `FinishBackgroundResourcesMs` propiedad en `ResourceCache` .

<a name="sound"></a>

## <a name="sound"></a>Sonido

El sonido es una parte importante de la reproducción del juego y el marco de trabajo de UrhoSharp proporciona una forma de reproducir sonidos en el juego.  Para reproducir sonidos, adjunte un`SoundSource`
`Node`y, a continuación, reproduciendo un archivo con nombre a partir de los recursos.

Así es como se hace:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"></a>

## <a name="particles"></a>Partículas

Las partículas proporcionan una forma sencilla de agregar efectos simples y económicos a la aplicación.  Puede consumir partículas almacenadas en formato PEX con herramientas como [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/) .

Las partículas son componentes que se pueden agregar a un nodo.  Debe llamar al método del nodo `CreateComponent<ParticleEmitter2D>` para crear la partícula y, a continuación, configurar la partícula estableciendo la propiedad Effect en un efecto 2D que se carga desde la memoria caché de recursos.

Por ejemplo, puede invocar este método en el componente para mostrar algunas partículas que se representan como una explosión cuando llega a:

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

En el código anterior se creará un nodo de explosión que se adjunta al componente actual, dentro de este nodo de explosión se crea un emisor de partículas 2D y se configura estableciendo la propiedad efecto.  Ejecutamos dos acciones, una que escala el nodo para que sea menor y otra que lo deja en ese tamaño durante 0,5 segundos.  A continuación, se quita la explosión, que también quita el efecto de partículas de la pantalla.

La partícula anterior se representa como esta al utilizar una textura de esfera:

![Partículas con una textura de esfera](using-images/image-1.png "La partícula anterior se representa como esta al utilizar una textura de esfera")

Y esto es lo que parece si usa una textura de bloque:

![Partículas con una textura de cuadro](using-images/image-2.png "Y esto es lo que parece si se usa una textura de bloque")

## <a name="multi-threading-support"></a>Compatibilidad con varios subprocesos

UrhoSharp es una biblioteca de un solo subproceso.  Esto significa que no se debe intentar invocar métodos en UrhoSharp desde un subproceso en segundo plano, o se corre el riesgo de dañar el estado de la aplicación y es probable que se bloquee la aplicación.

Si desea ejecutar código en segundo plano y, a continuación, actualizar los componentes de Urho en la interfaz de usuario principal, puede usar el`Application.InvokeOnMain(Action)`
.  Además, puede usar las API Await de C# y .NET Task para asegurarse de que el código se ejecuta en el subproceso adecuado.

## <a name="urhoeditor"></a>UrhoEditor

Puede descargar el editor de Urho para su plataforma desde el [sitio web de Urho](http://urho3d.github.io/), vaya a downloads (descargas) y seleccione la versión más reciente.

## <a name="copyrights"></a>Derechos

Esta documentación contiene contenido original de Xamarin Inc, pero se basa en gran medida en la documentación de código abierto para el proyecto Urho3D y contiene capturas de pantallas del proyecto Cocos2D.
