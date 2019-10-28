---
title: Visual Basic en Xamarin. Android y Xamarin. iOS
description: En este tutorial se muestra cómo compilar aplicaciones nativas de Xamarin. iOS y Xamarin. Android que usan la lógica de negocios escrita en Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: ea4dc91b262c2ae153088f6e1a8416cc01cb0fa9
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959121"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Visual Basic en Xamarin Android e iOS

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

La aplicación de ejemplo [TaskyVB](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/) muestra cómo Visual Basic código compilado en una biblioteca de .net Standard se puede usar con Xamarin. Estas son algunas capturas de pantallas de las aplicaciones resultantes que se ejecutan en Android e iOS:

 [![Android e iOS que ejecutan una aplicación compilada con Visual Basic](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

En el ejemplo, los proyectos de iOS y Android están escritos C#en. La interfaz de usuario de cada aplicación se crea con tecnologías nativas, mientras que la administración de `TodoItem` se proporciona mediante la biblioteca de .NET Standard de Visual Basic con un archivo XML (con fines de demostración, no una base de datos completa).

## <a name="sample-walkthrough"></a>Ejemplo de tutorial

En esta guía se describe cómo se ha implementado Visual Basic en el ejemplo [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) de Xamarin para iOS y Android.

> [!NOTE]
> Revise las instrucciones de [Visual Basic y .net Standard](index.md) antes de continuar con esta guía.
>
> Consulte [Xamarin. Forms mediante Visual Basic](xamarin-forms.md) instrucciones para ver cómo compilar una aplicación con el código Visual Basic de la interfaz de usuario compartida.

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic bibliotecas de .NET Standard solo se pueden crear en Visual Studio en Windows.
La biblioteca de ejemplo contiene los aspectos básicos de nuestra aplicación en estos archivos Visual Basic:

- TodoItem. VB
- TodoItemManager. VB
- TodoItemRepositoryXML. VB
- XmlStorage. VB

### <a name="todoitemvb"></a>TodoItem. VB

Esta clase contiene el objeto comercial que se va a utilizar en toda la aplicación. Se definirá en Visual Basic y se compartirá con los proyectos de iOS y Android escritos en C#.

Aquí se muestra la definición de clase:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

En el ejemplo se usa la serialización y la deserialización XML para cargar y guardar los objetos TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager. VB

La clase de administrador presenta la ' API ' para el código portable. Proporciona operaciones CRUD básicas para la clase `TodoItem`, pero no implementaciones de esas operaciones.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

El constructor toma una instancia de IXmlStorage como parámetro. Esto permite a cada plataforma proporcionar su propia implementación de trabajo y, al mismo tiempo, permitir que el código portable describa otras funciones que se pueden compartir.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository. VB

La clase Repository contiene la lógica para administrar la lista de objetos TodoItem. A continuación se muestra el código completo: la lógica existe principalmente para administrar un valor de identificador único en el TodoItems a medida que se agregan y se quitan de la colección.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Este código es un ejemplo de un mecanismo de almacenamiento de datos muy básico.
> Se proporciona para mostrar cómo una biblioteca de .NET Standard puede codificar en una interfaz para tener acceso a la funcionalidad específica de la plataforma (en este caso, cargar y guardar un archivo XML). No pretende ser una alternativa de base de datos de producción.

## <a name="android-and-ios-application-projects"></a>Proyectos de aplicaciones de iOS y Android

### <a name="ios"></a>iOS

En la aplicación de iOS, el `TodoItemManager` y el `XmlStorageImplementation` se crean en el archivo **AppDelegate.CS** , tal como se muestra en este fragmento de código. Las primeras cuatro líneas son simplemente compilando la ruta de acceso al archivo en el que se almacenarán los datos. las dos líneas finales muestran las dos clases de las que se crean instancias.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

En la aplicación de Android, el `TodoItemManager` y el `XmlStorageImplementation` se crean en el archivo **Application.CS** , tal como se muestra en este fragmento de código. Las tres primeras líneas solo están compilando la ruta de acceso al archivo en el que se almacenarán los datos. las dos líneas finales muestran las dos clases de las que se crean instancias.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

El resto del código de la aplicación está principalmente relacionado con la interfaz de usuario y con la clase `TaskMgr` para cargar y guardar `TodoItem` clases.

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 para Mac

> [!WARNING]
> Visual Studio para Mac no admite la edición del lenguaje de Visual Basic, no hay elementos de menú para crear Visual Basic proyectos o archivos. Si abre un **. VB** , no hay ningún resaltado de sintaxis de lenguaje, Autocompletar o IntelliSense.

Visual Studio 2019 para Mac _puede_ compilar proyectos de .net Standard de Visual Studio creados en Windows, por lo que las aplicaciones de iOS pueden hacer referencia a esos proyectos.

Visual Studio 2017 _no puede_ compilar proyectos de Visual Basic.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo consumir código Visual Basic en aplicaciones de Xamarin con Visual Studio y bibliotecas de .NET Standard. Aunque Xamarin no admite Visual Basic directamente, compilar Visual Basic en una biblioteca de .NET Standard permite incluir código escrito con Visual Basic en aplicaciones iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [TaskyVB (ejemplo .NET Standard)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [Novedades de .NET Standard](https://docs.microsoft.com/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)
