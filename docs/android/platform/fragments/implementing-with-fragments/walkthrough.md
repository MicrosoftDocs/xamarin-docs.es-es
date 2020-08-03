---
title: 'Tutorial sobre los fragmentos de Xamarin.Android: parte 1'
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 56c9fc307166ec9c72ab6cb1f7a726c4cd12cb3e
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997038"
---
# <a name="fragments-walkthrough-ndash-phone"></a>Tutorial sobre los fragmentos: teléfono

Esta es la primera parte de un tutorial que creará una aplicación de Xamarin.Android destinada a un dispositivo Android en orientación vertical. En este tutorial se explica cómo crear fragmentos en Xamarin. Android y cómo agregarlos a un ejemplo.

[![Captura de pantalla de la aplicación del tutorial](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Se crearán las siguientes clases para esta aplicación:

1. `PlayQuoteFragment` &nbsp; Este fragmento mostrará una cita de una obra de William Shakespeare. Se hospedará en `PlayQuoteActivity`.
1. `Shakespeare` &nbsp; Esta clase contendrá dos matrices codificadas como propiedades.
1. `TitlesFragment` &nbsp; Este fragmento mostrará una lista de los títulos de las obras que ha escrito William Shakespeare. Se hospedará en `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` iniciará `PlayQuoteActivity` como respuesta al usuario seleccionando una obra en `TitlesFragment`.

## <a name="1-create-the-android-project"></a>1. Creación del proyecto para Android

Cree un nuevo proyecto de Xamarin.Android llamado **FragmentSample**.
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Creación de un nuevo proyecto de Xamarin.Android](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Creación de un nuevo proyecto de Xamarin.Android](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

Se recomienda seleccionar **Desarrollo moderno** para este tutorial.

Después de crear el proyecto, cambie el nombre del archivo **layout/Main.axml** a **layout/activity_main.axml**.

-----

## <a name="2-add-the-data"></a>2. Incorporación de datos

Los datos de esta aplicación se almacenarán en dos matrices de cadenas codificadas de forma rígida que son propiedades de un nombre de clase `Shakespeare`:

* `Shakespeare.Titles` &nbsp; Esta matriz contendrá una lista de obras de William Shakespeare. Este es el origen de datos para `TitlesFragment`.
* `Shakespeare.Dialogue` &nbsp; Esta matriz contendrá una lista de citas de una de las obras contenidas en `Shakespeare.Titles`. Este es el origen de datos para `PlayQuoteFragment`.

Agregue una nueva clase de C# al proyecto **FragmentSample** y asígnele el nombre **Shakespeare.cs**. Dentro de este archivo, cree una nueva clase de C# llamada `Shakespeare` con el siguiente contenido:

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. Creación de PlayQuoteFragment

`PlayQuoteFragment` es un fragmento de Android que mostrará una cita para una obra de Shakespeare que el usuario seleccionó anteriormente en la aplicación. Este fragmento no usará un archivo de diseño de Android; en su lugar, creará dinámicamente su interfaz de usuario. Agregue una clase `Fragment` denominada `PlayQuoteFragment` al proyecto:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Incorporación de una nueva clase de C#](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Incorporación de una nueva clase de C#](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

Después, cambie el código del fragmento para que sea similar a este fragmento de código:

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

Se trata de un patrón común en las aplicaciones de Android para proporcionar una instancia de Factory Method que creará una instancia de un fragmento. Esto garantiza que el fragmento se creará con los parámetros necesarios para funcionar correctamente. En este tutorial, se espera que la aplicación use el método `PlayQuoteFragment.NewInstance` para crear un nuevo fragmento cada vez que se selecciona una cita. El método `NewInstance` tomará un parámetro único: el índice de la oferta que se va a mostrar.

Android invocará al método `OnCreateView` cuando sea el momento de representar el fragmento en la pantalla. Devolverá un objeto `View` de Android que es el fragmento. Este fragmento no utiliza un archivo de diseño para crear una vista. En su lugar, creará la vista mediante programación creando una instancia de **TextView** para hospedar la cita y mostrará el widget en un **ScrollView**.

> [!NOTE]
> Las subclases del fragmento deben tener un constructor público predeterminado que no tenga ningún parámetro.

## <a name="4-create-the-playquoteactivity"></a>4. Creación de PlayQuoteActivity

Los fragmentos se deben hospedar dentro de una actividad, por lo que esta aplicación requiere una actividad que hospedará `PlayQuoteFragment`. La actividad agregará dinámicamente el fragmento a su diseño en tiempo de ejecución. Agregue una nueva actividad a la aplicación y asígnele el nombre `PlayQuoteActivity`:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Incorporación de la actividad de Android al proyecto](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Incorporación de la actividad de Android al proyecto](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

Edición del código en `PlayQuoteActivity`:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

Cuando se crea `PlayQuoteActivity`, creará una instancia de `PlayQuoteFragment` nuevo y cargará ese fragmento en su vista raíz en el contexto de `FragmentTransaction`. Tenga en cuenta que esta actividad no carga un archivo de diseño de Android para su interfaz de usuario. En su lugar, se agrega un nuevo `PlayQuoteFragment` a la vista raíz de la aplicación. El identificador de recursos `Android.Resource.Id.Content` se usa para hacer referencia a la vista raíz de una actividad sin conocer su identificador específico.

## <a name="5-create-titlesfragment"></a>5. Creación de TitlesFragment

`TitlesFragment` creará una subclase de un fragmento especializado conocido como `ListFragment`, que encapsula la lógica para mostrar `ListView` en un fragmento. `ListFragment` expone una propiedad `ListAdapter` (que `ListView` utiliza para mostrar su contenido) y un controlador de eventos denominado `OnListItemClick` que permite al fragmento responder a los clics en una fila que se muestra mediante `ListView`.

Para empezar, agregue un nuevo fragmento al proyecto y asígnele el nombre **TitlesFragment**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Incorporación de un fragmento de Android al proyecto](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Incorporación de un fragmento de Android al proyecto](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

Edite el código dentro del fragmento:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Cuando se crea la actividad, Android invocará al método `OnActivityCreated` del fragmento. Aquí es donde se crea el adaptador de lista para `ListView`.  El método `ShowQuoteFromPlay` iniciará una instancia de `PlayQuoteActivity` para mostrar la cita de la obra seleccionada.

## <a name="display-titlesfragment-in-mainactivity"></a>Visualización de TitlesFragment en MainActivity

El paso final consiste en mostrar `TitlesFragment` dentro de `MainActivity`. La actividad no carga dinámicamente el fragmento. En cambio, el fragmento se cargará estáticamente al declararlo en el archivo de diseño de la actividad mediante un elemento `fragment`. El fragmento que se va a cargar se identifica estableciendo el atributo `android:name` en la clase del fragmento (incluido el espacio de nombres del tipo). Por ejemplo, para usar `TitlesFragment`, `android:name` se establecería en `FragmentSample.TitlesFragment`.

Edite el archivo de diseño **activity_main.axml** , reemplazando el archivo XML existente por lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> El atributo `class` es un sustituto válido de `android:name`. No hay una guía formal sobre qué forma se prefiere; hay muchos ejemplos de bases de código que utilizarán `class` indistintamente con `android:name`.

No se requieren cambios de código para MainActivity. El código de esa clase debería ser muy similar a este fragmento de código:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>Ejecutar la aplicación

Ahora que el código está completo, ejecute la aplicación en un dispositivo para verlo en acción.

[![Capturas de pantalla de la aplicación que se ejecuta en un teléfono.](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[La parte 2 de este tutorial](./walkthrough-landscape.md) optimizará esta aplicación para dispositivos que se ejecutan en modo horizontal.
