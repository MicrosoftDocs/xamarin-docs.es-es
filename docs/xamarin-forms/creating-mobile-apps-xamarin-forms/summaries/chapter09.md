---
title: "Resumen del capítulo 9. Llamadas de API específicas de la plataforma" description: "Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 9. Llamadas de API específicas de la plataforma" ms.prod: xamarin ms.technology: xamarin-forms ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D author: davidbritch ms.author: dabritch ms.date: 07/19/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumen del capítulo 9. Llamadas de API específicas de la plataforma

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> En las notas de esta página se indican las áreas en las que Xamarin.Forms difiere del material presentado en el libro.

A veces es necesario ejecutar código que varíe según la plataforma. En este capítulo se exploran las técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Preprocesamiento en el proyecto de recursos compartidos

Un proyecto de recurso compartido de Xamarin.Forms puede ejecutar código diferente para cada plataforma mediante las directivas de preprocesador de C# `#if`, `#elif` y `endif`. Esto se muestra en [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de pantalla triple de un párrafo con formato variable](images/ch09fg01-small.png "Modelo de dispositivo y sistema operativo")](images/ch09fg01-large.png#lightbox "Modelo de dispositivo y sistema operativo")

Sin embargo, el código resultante puede ser feo y difícil de leer.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Clases paralelas en el proyecto de recursos compartidos

En el ejemplo [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) se muestra un enfoque más estructurado para ejecutar código específico de la plataforma en SAP. Cada uno de los proyectos de la plataforma contiene una clase y métodos con nombres idénticos, pero implementados para esa plataforma en particular. A continuación, SAP simplemente crea instancias de la clase y llama al método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService y la biblioteca de clases portable

> [!NOTE] 
> Las bibliotecas de clases portables se han sustituido por las bibliotecas .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas .NET Standard.

Normalmente, una biblioteca no puede tener acceso a las clases de los proyectos de aplicación. Esta restricción parece evitar que la técnica que se muestra en **PlatInfoSap2** se use en una biblioteca. Aun así, Xamarin.Forms contiene una clase denominada [`DependencyService`](xref:Xamarin.Forms.DependencyService) que usa la reflexión de .NET para acceder a las clases públicas del proyecto de aplicación desde la biblioteca.

La biblioteca debe definir un elemento `interface` con los miembros que necesita usar en cada plataforma. Después, cada una de las plataformas contiene una implementación de esa interfaz. La clase que implementa la interfaz debe identificarse con un elemento [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) en el nivel de ensamblado.

A continuación, la biblioteca usa el método genérico [`Get`](xref:Xamarin.Forms.DependencyService.Get*) de `DependencyService` para obtener una instancia de la clase de plataforma que implementa la interfaz.

Esto se muestra en el ejemplo [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo).

## <a name="platform-specific-sound-generation"></a>Generación de sonido específica de la plataforma

El ejemplo [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) agrega pitidos al programa **MonkeyTap** mediante el acceso a las instalaciones de generación de sonido en cada plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Ejemplos del capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
