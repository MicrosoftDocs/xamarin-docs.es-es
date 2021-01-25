---
ms.openlocfilehash: ff37327e1fd942382c3eb81b8c6bcc0041347b0e
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689908"
---
Un elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos mediante la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource), que es de tipo `IEnumerable`. El paso anterior rellenó `CollectionView` en XAML con una matriz de cadenas. Sin embargo, `CollectionView` normalmente se rellena con datos de una colección, definida en código, que implementa `IEnumerable`.

En este ejercicio, modificará el proyecto **CollectionViewTutorial** para rellenar [`CollectionView`](xref:Xamarin.Forms.CollectionView) con datos de una colección de objetos almacenados en un elemento `List`.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. En el **Explorador de soluciones**, en el proyecto **CollectionViewTutorial**, agregue una clase denominada `Monkey` que contenga el siguiente código:

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }        
    }
    ```

    Este código define un objeto `Monkey` que almacena un nombre, una ubicación y una dirección URL de una imagen que representa el objeto monkey. Además, la clase invalida el método `ToString` para devolver la propiedad `Name`.

1. En el **Explorador de soluciones**, en el proyecto **CollectionViewTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace CollectionViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    Este código define una propiedad `Monkeys` de tipo `IList<Monkey>` y la inicializa en una lista vacía en el constructor de clase. Después, se agregan los objetos `Monkey` a la colección `Monkeys` y el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en el objeto `MainPage`. Para obtener más información sobre la propiedad `BindingContext`, vea [Enlaces con un contexto de enlace](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) en la guía de [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) se hereda a través del árbol visual. Por lo tanto, como ya se ha establecido en el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), los objetos secundarios del elemento `ContentPage` heredan su valor, incluido el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView).

1. En **MainPage.xaml**, modifique la declaración de [`CollectionView`](xref:Xamarin.Forms.CollectionView) para establecer la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) para la colección `Monkeys`:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}" />
    ```

    Este código enlaza datos de la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) a la colección `Monkeys`. En tiempo de ejecución, el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) examinará su [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para la colección `Monkeys` y se rellenará con datos de esta colección. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView rellenado con datos de una colección, en iOS y Android](../images/populate-data.png "CollectionView que muestra datos de una colección")](../images/populate-data-large.png#lightbox "CollectionView que muestra datos de una colección")

    El elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra la propiedad `Name` de cada `Monkey` de la colección `Monkeys`. Esto es porque, de forma predeterminada, el elemento `CollectionView` llama al método `ToString` al mostrar los objetos de una colección (que se ha invalidado en la clase `Monkey` para devolver el valor de propiedad `Name`).

    En Visual Studio, detenga la aplicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **Panel de solución**, en el proyecto **CollectionViewTutorial**, agregue una clase denominada `Monkey` que contenga el siguiente código:

    ```csharp
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
        public string ImageUrl { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
    ```

    Este código define un objeto `Monkey` que almacena un nombre, una ubicación y una dirección URL de una imagen que representa el objeto monkey. Además, la clase invalida el método `ToString` para devolver la propiedad `Name`.

1. En el **Panel de solución**, en el proyecto **CollectionViewTutorial**, expanda **MainPage.xaml** y haga doble clic en **MainPage.xaml.cs** para abrirlo. Después, en **MainPage.xaml.cs**, quite todo el código de plantilla y sustitúyalo por el código siguiente:

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace CollectionViewTutorial
    {
        public partial class MainPage : ContentPage
        {
            public IList<Monkey> Monkeys { get; private set; }

            public MainPage()
            {
                InitializeComponent();

                Monkeys = new List<Monkey>();
                Monkeys.Add(new Monkey
                {
                    Name = "Baboon",
                    Location = "Africa & Asia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Capuchin Monkey",
                    Location = "Central & South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Blue Monkey",
                    Location = "Central and East Africa",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Squirrel Monkey",
                    Location = "Central & South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/Saimiri_sciureus-1_Luc_Viatour.jpg/220px-Saimiri_sciureus-1_Luc_Viatour.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Lion Tamarin",
                    Location = "Brazil",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Golden_lion_tamarin_portrait3.jpg/220px-Golden_lion_tamarin_portrait3.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Howler Monkey",
                    Location = "South America",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/Alouatta_guariba.jpg/200px-Alouatta_guariba.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Japanese Macaque",
                    Location = "Japan",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Macaca_fuscata_fuscata1.jpg/220px-Macaca_fuscata_fuscata1.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Mandrill",
                    Location = "Southern Cameroon, Gabon, Equatorial Guinea, and Congo",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Mandrill_at_san_francisco_zoo.jpg/220px-Mandrill_at_san_francisco_zoo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Proboscis Monkey",
                    Location = "Borneo",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Proboscis_Monkey_in_Borneo.jpg/250px-Proboscis_Monkey_in_Borneo.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Red-shanked Douc",
                    Location = "Vietnam, Laos",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Portrait_of_a_Douc.jpg/159px-Portrait_of_a_Douc.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gray-shanked Douc",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Cuc.Phuong.Primate.Rehab.center.jpg/320px-Cuc.Phuong.Primate.Rehab.center.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Golden Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c8/Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg/165px-Golden_Snub-nosed_Monkeys%2C_Qinling_Mountains_-_China.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Black Snub-nosed Monkey",
                    Location = "China",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/RhinopitecusBieti.jpg/320px-RhinopitecusBieti.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Tonkin Snub-nosed Monkey",
                    Location = "Vietnam",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg/320px-Tonkin_snub-nosed_monkeys_%28Rhinopithecus_avunculus%29.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Thomas's Langur",
                    Location = "Indonesia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Thomas%27s_langur_Presbytis_thomasi.jpg/142px-Thomas%27s_langur_Presbytis_thomasi.jpg"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Purple-faced Langur",
                    Location = "Sri Lanka",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Semnopithèque_blanchâtre_mâle.JPG/192px-Semnopithèque_blanchâtre_mâle.JPG"
                });

                Monkeys.Add(new Monkey
                {
                    Name = "Gelada",
                    Location = "Ethiopia",
                    ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/13/Gelada-Pavian.jpg/320px-Gelada-Pavian.jpg"
                });

                BindingContext = this;
            }
        }
    }
    ```

    Este código define una propiedad `Monkeys` de tipo `IList<Monkey>` y la inicializa en una lista vacía en el constructor de clase. Después, se agregan los objetos `Monkey` a la colección `Monkeys` y el elemento [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la página se establece en el objeto `MainPage`. Para obtener más información sobre la propiedad `BindingContext`, vea [Enlaces con un contexto de enlace](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context) en la guía de [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

    > [!IMPORTANT]
    > La propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) se hereda a través del árbol visual. Por lo tanto, como ya se ha establecido en el objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), los objetos secundarios del elemento `ContentPage` heredan su valor, incluido el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView).

1. En **MainPage.xaml**, modifique la declaración de [`CollectionView`](xref:Xamarin.Forms.CollectionView) para establecer la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) para la colección `Monkeys`:

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}" />
    ```

    Este código enlaza datos de la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) a la colección `Monkeys`. En tiempo de ejecución, el elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) examinará su [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para la colección `Monkeys` y se rellenará con datos de esta colección. Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de un elemento CollectionView rellenado con datos de una colección, en iOS y Android](../images/populate-data.png "CollectionView que muestra datos de una colección")](../images/populate-data-large.png#lightbox "CollectionView que muestra datos de una colección")

    El elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra la propiedad `Name` de cada `Monkey` de la colección `Monkeys`. Esto es porque, de forma predeterminada, el elemento `CollectionView` llama al método `ToString` al mostrar los objetos de una colección (que se ha invalidado en la clase `Monkey` para devolver el valor de propiedad `Name`).

    En Visual Studio para Mac, detenga la aplicación.
