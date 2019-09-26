---
title: Introducción a las bibliotecas de clases portables (PCL)
description: En este artículo se presentan proyectos de la biblioteca de clases portable (PCL) y se explica cómo crear y consumir proyectos PCL en Visual Studio para Mac y Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a4ee81f7d59c9fb680dfd371a7aaba7660fb3343
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "68681074"
---
# <a name="portable-class-libraries-pcl"></a>Bibliotecas de clases portables (PCL)

> [!TIP]
> Las bibliotecas de clases portables (PCL) se consideran en desuso en las versiones más recientes de Visual Studio.
> Aunque todavía puede abrir, editar y compilar PCL, para los proyectos nuevos se recomienda usar [bibliotecas de .net Standard](~/cross-platform/app-fundamentals/net-standard.md) para tener acceso a un área expuesta de API más grande.

Un componente clave de la creación de aplicaciones multiplataforma es poder compartir código entre varios proyectos específicos de la plataforma. Sin embargo, esto es complicado por el hecho de que las distintas plataformas usan a menudo un subconjunto diferente de la biblioteca de clases base (BCL) de .NET y, por tanto, se crean realmente en un perfil de biblioteca de .NET Core diferente. Esto significa que cada plataforma solo puede usar las bibliotecas de clases que están destinadas al mismo perfil, de modo que parezcan necesitar proyectos de biblioteca de clases independientes para cada plataforma.

Hay tres enfoques principales para el uso compartido de código que solucionan este problema: **proyectos de .net Standard**, **proyectos de recursos compartidos**y **proyectos de biblioteca de clases portable (PCL)** .

- **.Net Standard proyectos** son el método preferido para compartir código .net, lea más sobre los [proyectos de .net Standard y Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- Los **proyectos de recursos compartidos** usan un único conjunto de archivos y ofrecen una manera rápida y sencilla de compartir código dentro de una solución y, por lo general, emplean directivas de compilación condicional para especificar rutas de acceso de código para varias plataformas que lo usarán (para más información vea el [artículo sobre proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)).
- Los proyectos **PCL** tienen como destino perfiles específicos que admiten un conjunto conocido de clases o características de BCL. Sin embargo, el lado inferior a PCL es que a menudo requieren un esfuerzo de arquitectura adicional para separar el código específico del perfil en sus propias bibliotecas.

En esta página se explica cómo crear un proyecto de **PCL** que tenga como destino un perfil específico, al que pueden hacer referencia varios proyectos específicos de la plataforma.

## <a name="what-is-a-portable-class-library"></a>¿Qué es una biblioteca de clases portable?

Cuando se crea un proyecto de aplicación o un proyecto de biblioteca, el archivo DLL resultante se limita a trabajar en la plataforma específica para la que se crea. Esto le impide escribir un ensamblado para una aplicación de Windows y, a continuación, volver a usarlo en Xamarin. iOS y Xamarin. Android.

Sin embargo, cuando se crea una biblioteca de clases portable, puede elegir una combinación de plataformas en las que desea que se ejecute el código. Las opciones de compatibilidad que realiza al crear una biblioteca de clases portable se traducen en un identificador de "perfil", que describe qué plataformas admite la biblioteca.

En la tabla siguiente se muestran algunas de las características que varían en función de la plataforma .NET. Para escribir un ensamblado PCL que se ejecute en dispositivos o plataformas específicos, simplemente elija la compatibilidad que se requiere al crear el proyecto.

|Característica|.NET Framework|Aplicaciones para UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Principal|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7,5 +|Y|
|Serialización|Y|Y|Y|Y|Y|
|Anotaciones de datos|4.0.3 +|Y|Y||Y|

La columna Xamarin refleja el hecho de que Xamarin. iOS y Xamarin. Android admiten todos los perfiles incluidos con Visual Studio, y la disponibilidad de las características de las bibliotecas que cree solo estarán limitadas por las otras plataformas que elija admitir.

Esto incluye los perfiles que son combinaciones de:

- .NET 4 o .NET 4,5
- Silverlight 5
- Windows Phone 8
- Aplicaciones para UWP

Puede obtener más información acerca de las capacidades de los distintos perfiles en el [sitio web de Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) y ver el [Resumen del perfil de PCL](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) de otro miembro de la comunidad, que incluye información de marco de trabajo compatible y otras notas.

**Beneficios**

1. Uso compartido de código centralizado: escriba y pruebe el código en un único proyecto que puedan usar otras bibliotecas o aplicaciones.
2. Las operaciones de refactorización afectarán a todo el código cargado en la solución (la biblioteca de clases portable y los proyectos específicos de la plataforma).
3. Otros proyectos de una solución pueden hacer referencia fácilmente al proyecto PCL, o bien el ensamblado de salida puede compartirse para que otros hagan referencia en sus soluciones.

**Desventajas**

1. Dado que la misma biblioteca de clases portable se comparte entre varias aplicaciones, no se puede hacer referencia a las bibliotecas específicas de la plataforma (por ejemplo, Community. CsharpSqlite. WP7).
2. Es posible que el subconjunto de la biblioteca de clases portable no incluya clases que, de otro modo, podrían estar disponibles en MonoTouch y mono para Android (como DllImport o System. IO. File).

> [!NOTE]
> Las bibliotecas de clases portables han quedado en desuso en la versión más reciente de Visual Studio y, en su lugar, se recomiendan [.net Standard bibliotecas](net-standard.md) .

En cierta medida, ambos desventajas se pueden eludir mediante el patrón de proveedor o la inserción de dependencias para codificar la implementación real en los proyectos de plataforma con una interfaz o clase base que se define en la biblioteca de clases portable.

En este diagrama se muestra la arquitectura de una aplicación multiplataforma que usa una biblioteca de clases portable para compartir código, pero también el uso de la inserción de dependencias para pasar características dependientes de la plataforma:

[![](pcl-images/image1.png "Este diagrama muestra la arquitectura de una aplicación multiplataforma mediante una biblioteca de clases portable para compartir código, pero también mediante la inserción de dependencias para pasar características dependientes de la plataforma.")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio para Mac tutorial

En esta sección se explica cómo crear y usar una biblioteca de clases portable mediante Visual Studio para Mac. Consulte la sección ejemplo de PCL para obtener una implementación completa.

### <a name="creating-a-pcl"></a>Crear una PCL

Agregar una biblioteca de clases portable a la solución es muy similar a agregar un proyecto de biblioteca normal.

1. En el cuadro de diálogo **nuevo proyecto** , seleccione la opción biblioteca **multiplataforma > Biblioteca > portable Library** :

    ![Crear un nuevo proyecto de PCL](pcl-images/image2.png)

1. Cuando se crea una PCL en Visual Studio para Mac se configura automáticamente con un perfil que funciona para Xamarin. iOS y Xamarin. Android. El proyecto PCL aparecerá como se muestra en esta captura de pantalla:

    ![Proyecto PCL en el panel de solución](pcl-images/image3.png)

La PCL ahora está lista para agregar el código. También se puede hacer referencia a él desde otros proyectos (proyectos de aplicación, proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-pcl-settings"></a>Editar la configuración de PCL

Para ver y cambiar la configuración de PCL de este proyecto, haga clic con el botón derecho en el proyecto y elija **opciones > Compilar > general** para ver la pantalla que se muestra aquí:

[![Opciones de proyecto de PCL para establecer el perfil](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Haga clic en **cambiar...** para modificar el perfil de destino de esta biblioteca de clases portable.

Si se cambia el perfil después de que el código ya se haya agregado a la PCL, es posible que la biblioteca ya no se compile si el código hace referencia a características que no forman parte del perfil recién seleccionado.

## <a name="working-with-a-pcl"></a>Trabajar con una PCL

Cuando se escribe código en una biblioteca PCL, el editor de Visual Studio para Mac reconoce las limitaciones del perfil seleccionado y ajusta las opciones de autocompletar en consecuencia. Por ejemplo, en esta captura de pantalla se muestran las opciones de autocompletar de System.IO con el perfil predeterminado (Profile136) que se usa en Visual Studio para Mac: Observe la barra de desplazamiento que indica la mitad de las clases disponibles que se muestran (de hecho, solo hay 14 clases disponibles).

[![Lista de IntelliSense de 14 clases en la clase System.IO de una PCL](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Compárelo con la función de autocompletar System.IO en un proyecto de Xamarin. iOS o Xamarin. Android: hay 40 clases disponibles, incluidas las `File` clases `Directory` usadas comúnmente como y que no están en ningún perfil de PCL.

[![Lista de IntelliSense de las clases 40 en .NET Framework espacio de nombres System.IO](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Esto refleja el equilibrio subyacente del uso de PCL: la capacidad de compartir código sin problemas en muchas plataformas significa que algunas API no están disponibles porque no tienen implementaciones comparables en todas las plataformas posibles.

### <a name="using-pcl"></a>Usar PCL

Una vez creado un proyecto PCL, puede agregarle una referencia desde cualquier proyecto de biblioteca o aplicación compatible de la misma manera que normalmente agrega referencias. En Visual Studio para Mac, haga clic con el botón secundario en el nodo referencias y elija **Editar referencias...** a continuación, cambie a la pestaña **proyectos** como se muestra a continuación:

[![Agregar una referencia a una PCL a través de la opción Editar referencias](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

En la captura de pantalla siguiente se muestra el panel de solución de la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL en la parte inferior y una referencia a esa biblioteca PCL en el proyecto de Xamarin. iOS.

[![Solución de ejemplo de TaskyPortable que muestra el proyecto PCL](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

La salida de una PCL (es decir, la DLL del ensamblado resultante) también se puede agregar como referencia a la mayoría de los proyectos. Esto hace que PCL sea una manera ideal de enviar componentes y bibliotecas multiplataforma.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Tutorial de Visual Studio

En esta sección se explica cómo crear y usar una biblioteca de clases portable con Visual Studio. Consulte la sección ejemplo de PCL para obtener una implementación completa.

### <a name="creating-a-pcl"></a>Crear una PCL

Agregar una PCL a la solución en Visual Studio es ligeramente diferente para agregar un proyecto normal:

1. En la pantalla **Agregar nuevo proyecto** , seleccione la opción **biblioteca de clases (Legacy portable)** . Tenga en cuenta que la descripción de la derecha aconseja que este tipo de proyecto esté en desuso.

    [![Ventana nuevo proyecto para crear una biblioteca de clases portable](pcl-images/image10-sml.png "Biblioteca de clases portable")](pcl-images/image10.png#lightbox)

2. Visual Studio le preguntará inmediatamente con el siguiente cuadro de diálogo para que se pueda configurar el perfil.
 Marque las plataformas que necesite admitir y presione Aceptar.

    [![Seleccionar las plataformas de destino de la biblioteca](pcl-images/image11-sml.png "Marque las plataformas que necesite admitir y presione aceptar") .](pcl-images/image11.png#lightbox)

3. El proyecto PCL aparecerá como se muestra en la explorador de soluciones &ndash; el texto **(portátil)** aparece junto al nombre del proyecto para indicar que es una PCL:

    ![NET Framework definido por el perfil de PCL](pcl-images/image12.png "NET Framework definido por el perfil de PCL")

La PCL ahora está lista para agregar el código. También se puede hacer referencia a él desde otros proyectos (proyectos de aplicación, proyectos de biblioteca e incluso otros proyectos PCL).

### <a name="editing-pcl-settings"></a>Editar la configuración de PCL

Para ver y cambiar la configuración de PCL, haga clic con el botón derecho en el proyecto y elija **propiedades > biblioteca** , tal como se muestra en esta captura de pantalla:

[![Editar los destinos de la plataforma](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Si se cambia el perfil después de que el código ya se haya agregado a la PCL, es posible que la biblioteca ya no se compile si el código hace referencia a características que no forman parte del perfil recién seleccionado.

> [!TIP]
> También hay un mensaje que lo advierte **. NETStandard es el método recomendado para compartir código**. Esta es una indicación de que, aunque todavía se admiten PCL, se recomienda actualizar a .NET Standard.

### <a name="working-with-a-pcl"></a>Trabajar con una PCL

Cuando se escribe código en una biblioteca PCL, Visual Studio reconocerá las limitaciones del perfil seleccionado y ajustará las opciones de IntelliSense en consecuencia. Por ejemplo, en esta captura de pantalla se muestran las opciones de Autocompletar para System.IO con el perfil predeterminado (Profile136): Observe la barra de desplazamiento que indica la mitad de las clases disponibles que se muestran (de hecho, solo hay 14 clases disponibles).

[![Número reducido de clases de e/s disponibles en una PCL](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Compárelo con la función de autocompletar System.IO en un proyecto normal: hay 40 clases disponibles, incluidas las clases `File` de `Directory` uso frecuente como y que no están en ningún perfil de PCL.

[![Muchas más clases de e/s disponibles en el .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Esto refleja el equilibrio subyacente del uso de PCL: la capacidad de compartir código sin problemas en muchas plataformas significa que algunas API no están disponibles porque no tienen implementaciones comparables en todas las plataformas posibles.

> [!TIP]
> .NET Standard 2,0 representa un área expuesta de API mucho mayor que PCL, incluido el espacio de nombres System.IO. En el caso de los proyectos nuevos, se recomienda .NET Standard a través de PCL.

### <a name="using-pcl"></a>Usar PCL

Una vez creado un proyecto PCL, puede agregarle una referencia desde cualquier proyecto de biblioteca o aplicación compatible de la misma manera que normalmente agrega referencias. En Visual Studio, haga clic con el botón derecho en el nodo `Add Reference...` referencias y elija cambiar a la **solución >** la pestaña proyectos, como se muestra a continuación:

[![Agregar una referencia a una PCL mediante la pestaña agregar proyectos de referencia](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

En la captura de pantalla siguiente se muestra el panel de solución de la aplicación de ejemplo TaskyPortable, que muestra la biblioteca PCL en la parte inferior y una referencia a esa biblioteca PCL en el proyecto de Xamarin. iOS.

[![Solución de ejemplo TaskyPortable que muestra una biblioteca PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

La salida de una PCL (es decir, la DLL del ensamblado resultante) también se puede agregar como referencia a la mayoría de los proyectos.
Esto hace que PCL sea una manera ideal de enviar componentes y bibliotecas multiplataforma.

-----

## <a name="pcl-example"></a>Ejemplo de PCL

La aplicación de ejemplo [TaskyPortable](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/) muestra cómo se puede usar una biblioteca de clases portable con Xamarin.
Estas son algunas capturas de pantallas de las aplicaciones resultantes que se ejecutan en iOS y Android:

[![](pcl-images/image18.png "Estas son algunas capturas de pantallas de las aplicaciones resultantes que se ejecutan en iOS, Android y Windows Phone")](pcl-images/image18.png#lightbox)

Comparte una serie de clases de datos y lógicas que son solo código portable, y también muestra cómo incorporar requisitos específicos de la plataforma mediante la inserción de dependencias para la implementación de la base de datos SQLite.

La estructura de la solución se muestra a continuación (en Visual Studio para Mac y Visual Studio, respectivamente):

[![](pcl-images/image19.png "La estructura de la solución se muestra aquí en Visual Studio para Mac y Visual Studio, respectivamente.")](pcl-images/image19.png#lightbox)

Dado que el código SQLite-NET tiene partes específicas de la plataforma (para trabajar con las implementaciones de SQLite en cada sistema operativo diferente) con fines de demostración, se ha refactorizado en una clase abstracta que se puede compilar en una biblioteca de clases portable. el código real implementado como subclases en los proyectos de iOS y Android.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

La biblioteca de clases portable está limitada en las características de .NET que puede admitir. Dado que se compila para ejecutarse en varias plataformas, no puede usar `[DllImport]` la funcionalidad que se usa en SQLite-net. En su lugar, SQLite-NET se implementa como una clase abstracta y después se hace referencia a él a través del resto del código compartido. A continuación se muestra un extracto de la API abstracta:

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

El resto del código compartido utiliza la clase abstracta para "almacenar" y "recuperar" objetos de la base de datos. En cualquier aplicación que use esta clase abstracta, debemos pasar una implementación completa que proporcione la funcionalidad de base de datos real.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid y TaskyiOS

Los proyectos de aplicación de iOS y Android contienen la interfaz de usuario y otro código específico de la plataforma que se usa para conectar el código compartido en la PCL.

Estos proyectos también contienen una implementación de la API de base de datos abstracta que funciona en esa plataforma. En iOS y Android, el motor de base de datos de SQLite está integrado en el sistema operativo, por lo `[DllImport]` que la implementación puede usar tal y como se muestra para proporcionar la implementación concreta de la conectividad de base de datos. Aquí se muestra un extracto del código de implementación específico de la plataforma:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

La implementación completa puede verse en el código de ejemplo.

## <a name="summary"></a>Resumen

En este artículo se han explicado brevemente las ventajas e inconvenientes de las bibliotecas de clases portables, en la que se muestra cómo crear y usar PCL desde dentro Visual Studio para Mac y Visual Studio. y, por último, presenta una aplicación de ejemplo completa: TaskyPortable, que muestra una PCL en acción.

## <a name="related-links"></a>Vínculos relacionados

- [TaskyPortable (ejemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [Creación de aplicaciones multiplataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Visual Basic portátil](~/cross-platform/platform/visual-basic/index.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opciones de código compartido](~/cross-platform/app-fundamentals/code-sharing.md)
- [Desarrollo multiplataforma con el .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
