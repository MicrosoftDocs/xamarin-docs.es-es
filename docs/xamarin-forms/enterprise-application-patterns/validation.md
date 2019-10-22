---
title: Validación en aplicaciones empresariales
description: En este capítulo se explica cómo la aplicación móvil eShopOnContainers realiza la validación de los datos proporcionados por el usuario. Esto incluye la especificación de reglas de validación, la activación de la validación y la visualización de errores de validación.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "70760162"
---
# <a name="validation-in-enterprise-apps"></a>Validación en aplicaciones empresariales

Cualquier aplicación que acepte la entrada de los usuarios debe asegurarse de que la entrada es válida. Una aplicación podría, por ejemplo, comprobar la entrada que solo contiene caracteres de un intervalo determinado, es de una longitud determinada o coincide con un formato determinado. Sin validación, un usuario puede proporcionar datos que provocan un error en la aplicación. La validación aplica las reglas de negocios y evita que un atacante Inserte datos malintencionados.

En el contexto del patrón Model-View-ViewModel (MVVM), a menudo se necesitará un modelo de vista o modelo para realizar la validación de datos y señalar los errores de validación en la vista para que el usuario pueda corregirlos. La aplicación móvil eShopOnContainers realiza la validación sincrónica en el lado cliente de las propiedades del modelo de vista y notifica al usuario de los errores de validación resaltando el control que contiene los datos no válidos y mostrando los mensajes de error que informan al usuario. de por qué los datos no son válidos. En la figura 6-1 se muestran las clases implicadas en la realización de la validación en la aplicación móvil eShopOnContainers.

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**Figura 6-1**: clases de validación en la aplicación móvil eShopOnContainers

Ver las propiedades del modelo que requieren validación son del tipo `ValidatableObject<T>` y cada instancia `ValidatableObject<T>` tiene reglas de validación agregadas a su propiedad `Validations`. La validación se invoca desde el modelo de vista llamando al método `Validate` de la instancia de `ValidatableObject<T>`, que recupera las reglas de validación y las ejecuta con la propiedad `ValidatableObject<T>` `Value`. Los errores de validación se colocan en la propiedad `Errors` de la instancia de `ValidatableObject<T>` y la propiedad `IsValid` de la instancia de `ValidatableObject<T>` se actualiza para indicar si la validación se realizó correctamente o no.

La clase `ExtendedBindableObject` proporciona la notificación de cambio de propiedad, por lo que un control [`Entry`](xref:Xamarin.Forms.Entry) puede enlazar con la propiedad `IsValid` de `ValidatableObject<T>` instancia de la clase de modelo de vista para recibir una notificación de si los datos especificados son válidos o no.

## <a name="specifying-validation-rules"></a>Especificar reglas de validación

Las reglas de validación se especifican mediante la creación de una clase que deriva de la interfaz `IValidationRule<T>`, que se muestra en el ejemplo de código siguiente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Esta interfaz especifica que una clase de regla de validación debe proporcionar un `boolean` `Check` método que se utiliza para realizar la validación necesaria, y una propiedad `ValidationMessage` cuyo valor es el mensaje de error de validación que se mostrará si se produce un error en la validación.

En el ejemplo de código siguiente se muestra la regla de validación de `IsNotNullOrEmptyRule<T>`, que se usa para realizar la validación del nombre de usuario y la contraseña especificados por el usuario en el `LoginView` al usar servicios ficticios en la aplicación móvil eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

El método `Check` devuelve un `boolean` que indica si el argumento de valor es `null`, está vacío o solo contiene caracteres de espacio en blanco.

Aunque no se usa en la aplicación móvil eShopOnContainers, en el ejemplo de código siguiente se muestra una regla de validación para validar las direcciones de correo electrónico:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

El método `Check` devuelve un `boolean` que indica si el argumento de valor es una dirección de correo electrónico válida. Esto se consigue buscando en el argumento de valor la primera aparición del patrón de expresión regular especificado en el constructor de `Regex`. Si se ha encontrado el patrón de expresión regular en la cadena de entrada puede determinarse comprobando el valor de la propiedad `Success` del objeto `Match`.

> [!NOTE]
> A veces, la validación de propiedades puede implicar propiedades dependientes. Un ejemplo de propiedades dependientes es cuando el conjunto de valores válidos para la propiedad A depende del valor determinado que se ha establecido en la propiedad B. Para comprobar que el valor de la propiedad A es uno de los valores permitidos, sería necesario recuperar el valor de la propiedad B. Además, cuando cambia el valor de la propiedad B, es necesario volver a validar la propiedad A.

## <a name="adding-validation-rules-to-a-property"></a>Agregar reglas de validación a una propiedad

En la aplicación móvil eShopOnContainers, las propiedades del modelo de vista que requieren validación se declaran como de tipo `ValidatableObject<T>`, donde `T` es el tipo de los datos que se van a validar. En el ejemplo de código siguiente se muestra un ejemplo de dos de estas propiedades:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para que se produzca la validación, se deben agregar reglas de validación a la colección de `Validations` de cada instancia de `ValidatableObject<T>`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Este método agrega la regla de validación `IsNotNullOrEmptyRule<T>` a la colección `Validations` de cada `ValidatableObject<T>` instancia, especificando los valores de la propiedad `ValidationMessage` de la regla de validación, que especifica el mensaje de error de validación que se mostrará si se produce un error en la validación.

## <a name="triggering-validation"></a>Desencadenar la validación

El enfoque de validación usado en la aplicación móvil eShopOnContainers puede desencadenar manualmente la validación de una propiedad y desencadenar automáticamente la validación cuando cambia una propiedad.

### <a name="triggering-validation-manually"></a>Desencadenar la validación manualmente

La validación se puede desencadenar manualmente para una propiedad del modelo de vista. Por ejemplo, esto ocurre en la aplicación móvil eShopOnContainers cuando el usuario pulsa el botón de **Inicio de sesión** en el `LoginView`, al usar los servicios ficticios. El delegado de comando llama al método `MockSignInAsync` en el `LoginViewModel`, que invoca la validación ejecutando el método `Validate`, que se muestra en el ejemplo de código siguiente:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

El método `Validate` realiza la validación del nombre de usuario y la contraseña especificados por el usuario en el `LoginView`, invocando el método Validate en cada instancia de `ValidatableObject<T>`. En el ejemplo de código siguiente se muestra el método Validate de la clase `ValidatableObject<T>`:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Este método borra la colección de `Errors` y, a continuación, recupera todas las reglas de validación que se agregaron a la colección de `Validations` del objeto. Se ejecuta el método `Check` para cada regla de validación recuperada y el valor de la propiedad `ValidationMessage` para cualquier regla de validación que no pueda validar los datos se agrega a la colección de `Errors` de la instancia de `ValidatableObject<T>`. Por último, se establece la propiedad `IsValid` y su valor se devuelve al método de llamada, que indica si la validación se realizó correctamente o no.

### <a name="triggering-validation-when-properties-change"></a>Desencadenar la validación cuando cambian las propiedades

También se puede desencadenar la validación cada vez que cambia una propiedad enlazada. Por ejemplo, cuando un enlace bidireccional en el `LoginView` establece la propiedad `UserName` o `Password`, se desencadena la validación. En el ejemplo de código siguiente se muestra cómo se produce esto:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

El control [`Entry`](xref:Xamarin.Forms.Entry) se enlaza a la propiedad `UserName.Value` de la instancia de `ValidatableObject<T>` y la colección `Behaviors` del control tiene una instancia `EventToCommandBehavior` agregada a él. Este comportamiento ejecuta el `ValidateUserNameCommand` como respuesta a la activación del evento [`TextChanged`] en el `Entry`, que se genera cuando cambia el texto del `Entry`. A su vez, el delegado de `ValidateUserNameCommand` ejecuta el método `ValidateUserName`, que ejecuta el método `Validate` en la instancia de `ValidatableObject<T>`. Por lo tanto, cada vez que el usuario escribe un carácter en el control `Entry` para el nombre de usuario, se realiza la validación de los datos especificados.

Para obtener más información sobre los comportamientos, vea [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Mostrar errores de validación

La aplicación móvil eShopOnContainers notifica al usuario de los errores de validación resaltando el control que contiene los datos no válidos con una línea roja y mostrando un mensaje de error que informa al usuario de por qué los datos no son válidos por debajo del control que contiene el datos no válidos. Cuando se corrigen los datos no válidos, la línea cambia a negro y se quita el mensaje de error. En la figura 6-2 se muestra la LoginView en la aplicación móvil de eShopOnContainers cuando hay errores de validación.

![](validation-images/validation-login.png "Displaying validation errors during login")

**Figura 6-2:** Mostrar errores de validación durante el inicio de sesión

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Resaltado de un control que contiene datos no válidos

El comportamiento adjunto `LineColorBehavior` se usa para resaltar [`Entry`](xref:Xamarin.Forms.Entry) controles en los que se han producido errores de validación. En el ejemplo de código siguiente se muestra cómo el `LineColorBehavior` el comportamiento adjunto se adjunta a un control `Entry`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

El control [`Entry`](xref:Xamarin.Forms.Entry) consume un estilo explícito, que se muestra en el ejemplo de código siguiente:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Este estilo establece las propiedades adjuntas `ApplyLineColor` y `LineColor` del comportamiento asociado `LineColorBehavior` en el control [`Entry`](xref:Xamarin.Forms.Entry) . Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

Cuando el valor de la propiedad adjunta `ApplyLineColor` está establecido, o cambia, el comportamiento adjunto `LineColorBehavior` ejecuta el método `OnApplyLineColorChanged`, que se muestra en el ejemplo de código siguiente:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Los parámetros de este método proporcionan la instancia del control al que está asociado el comportamiento, así como los valores antiguos y nuevos de la propiedad adjunta `ApplyLineColor`. La clase `EntryLineColorEffect` se agrega a la colección de [`Effects`](xref:Xamarin.Forms.Element.Effects) del control si el `ApplyLineColor` propiedad adjunta es `true`, de lo contrario se quita de la colección `Effects` del control. Para obtener más información sobre los comportamientos, vea [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

El `EntryLineColorEffect` subclases de la clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) y se muestra en el ejemplo de código siguiente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

La clase [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) representa un efecto independiente de la plataforma que ajusta un efecto interno que es específico de la plataforma. Esto simplifica el proceso de eliminación del efecto, ya que no hay ningún acceso en tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El `EntryLineColorEffect` llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se especifica en cada clase de efecto específica de la plataforma.

En el ejemplo de código siguiente se muestra la implementación de `eShopOnContainers.EntryLineColorEffect` para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

El método `OnAttached` recupera el control nativo del control de Xamarin. Forms [`Entry`](xref:Xamarin.Forms.Entry) y actualiza el color de línea llamando al método `UpdateLineColor`. La invalidación de `OnElementPropertyChanged` responde a los cambios de propiedades enlazables en el control de `Entry` actualizando el color de línea si la propiedad `LineColor` adjunta cambia o la propiedad [`Height`](xref:Xamarin.Forms.VisualElement.Height) del `Entry` cambia. Para obtener más información sobre los efectos, vea [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

Cuando se escriben datos válidos en el control [`Entry`](xref:Xamarin.Forms.Entry) , se aplicará una línea negra en la parte inferior del control para indicar que no hay ningún error de validación. En la figura 6-3 se muestra un ejemplo de esto.

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**Figura 6-3**: línea negra que indica que no hay ningún error de validación

El control [`Entry`](xref:Xamarin.Forms.Entry) también tiene un [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) agregado a su colección [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) . En el ejemplo de código siguiente se muestra el `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Esta [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) supervisa la propiedad `UserName.IsValid` y, si su valor se convierte en `false`, ejecuta el [`Setter`](xref:Xamarin.Forms.Setter), que cambia la propiedad adjunta `LineColor` de la `LineColorBehavior` comportamiento adjunto a rojo. En la figura 6-4 se muestra un ejemplo de esto.

![](validation-images/validation-redline.png "Red line indicating validation error")

**Figura 6-4**: línea roja que indica un error de validación

La línea del control [`Entry`](xref:Xamarin.Forms.Entry) permanecerá en rojo mientras que los datos especificados no son válidos; de lo contrario, cambiará a negro para indicar que los datos introducidos son válidos.

Para obtener más información acerca de los desencadenadores, vea [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Mostrar mensajes de error

La interfaz de usuario muestra los mensajes de error de validación en los controles de etiqueta debajo de cada control cuyos datos no hayan superado la validación. En el ejemplo de código siguiente se muestra el [`Label`](xref:Xamarin.Forms.Label) que muestra un mensaje de error de validación si el usuario no ha especificado un nombre de usuario válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [`Label`](xref:Xamarin.Forms.Label) enlaza a la propiedad `Errors` del objeto de modelo de vista que se está validando. La clase `ValidatableObject<T>` proporciona la propiedad `Errors` y es de tipo `List<string>`. Dado que la propiedad `Errors` puede contener varios errores de validación, la instancia de `FirstValidationErrorConverter` se utiliza para recuperar el primer error de la colección para su presentación.

## <a name="summary"></a>Resumen

La aplicación móvil eShopOnContainers realiza la validación sincrónica en el lado cliente de las propiedades del modelo de vista y notifica al usuario de los errores de validación resaltando el control que contiene los datos no válidos y mostrando los mensajes de error que informan al usuario. por qué los datos no son válidos.

Ver las propiedades del modelo que requieren validación son del tipo `ValidatableObject<T>` y cada instancia `ValidatableObject<T>` tiene reglas de validación agregadas a su propiedad `Validations`. La validación se invoca desde el modelo de vista llamando al método `Validate` de la instancia de `ValidatableObject<T>`, que recupera las reglas de validación y las ejecuta con la propiedad `ValidatableObject<T>` `Value`. Los errores de validación se colocan en la propiedad `Errors` del `ValidatableObject<T>`instance y la propiedad `IsValid` de la instancia de `ValidatableObject<T>` se actualiza para indicar si la validación se realizó correctamente o no.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
