---
title: Configuración personalizada del enlazador
description: En este documento se describe un archivo XML que puede usarse para configurar el vinculador, de modo que se garantice explícitamente que el código necesario no se elimine de la aplicación vinculada.
ms.prod: xamarin
ms.assetid: F8A99E3F-2197-4399-AC81-F1DBAB5729C9
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: bf934d50c90efaba266cbfac00aa5140ea92009b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526679"
---
# <a name="custom-linker-configuration"></a>Configuración personalizada del enlazador

Si el conjunto predeterminado de opciones no es suficiente, puede controlar el proceso de vinculación con un archivo XML que describe qué desea del enlazador.

Puede proporcionar definiciones adicionales para que el enlazador se asegure de que el tipo, los métodos y los campos no se eliminen de la aplicación. En su propio código, el método preferido es usar el atributo personalizado `[Preserve]`, como bien se describe en las guías [Vincular en iOS](~/ios/deploy-test/linker.md) y [Vincular en Android](~/android/deploy-test/linker.md).
Sin embargo, si necesita algunas definiciones del SDK o de los ensamblados del producto, usar un archivo XML puede ser la mejor solución con respecto a la adición de código que garantice que el enlazador no eliminará lo que se necesita.

Para ello, defina un archivo XML con el elemento de nivel superior `<linker>` que contiene nodos de *ensamblado* que, a su vez, contienen nodos de *tipo*, que, a su vez, contienen nodos de *método* y *archivo*.

Cuando ya tenga este archivo de descripción del enlazador, agréguelo al proyecto y:

- **Para Android**: establezca **Acción de compilación** en **LinkDescription**.
- **Para iOS**: establezca **Acción de compilación** en **LinkDescription**.


En el ejemplo siguiente se muestra el aspecto del archivo XML:

```xml
<linker>
        <assembly fullname="mscorlib">
                <type fullname="System.Environment">
                        <field name="mono_corlib_version" />
                        <method name="get_StackTrace" />
                </type>
        </assembly>
        <assembly fullname="My.Own.Assembly">
                <type fullname="Foo" preserve="fields">
                        <method name=".ctor" />
                </type>
                <type fullname="Bar">
                        <method signature="System.Void .ctor(System.String)" />
                        <field signature="System.String _blah" />
                </type>
                <namespace fullname="My.Own.Namespace" />
                <type fullname="My.Other*" />
        </assembly>
</linker>
```

En el ejemplo anterior, el enlazador leerá y aplicará las instrucciones en los ensamblados `mscorlib.dll` (que incluyen Mono para Android) y `My.Own.Assembly` (código de usuario).

La primera sección, para `mscorlib.dll`, se asegurará de que el tipo `System.Environment` conserve su campo con nombre `mono_corlib_version` y su método `get_StackTrace`.
Tenga en cuenta que deben usarse los nombres de método de establecedor y captador, ya que el enlazador funciona en IL y no entiende las propiedades de C#.

La segunda sección, para `My.Own.Assembly.dll`, se asegurará de que el tipo `Foo` conservará todos sus campos (es decir, el atributo `preserve="fields"`) y todos sus constructores (es decir, todos los métodos denominados `.ctor` en IL). El tipo `Bar` conservará firmas específicas (no nombres) para un constructor (que acepta un único parámetro de cadena) y para un campo de cadena específico `_blah`.
El espacio de nombres `My.Own.Namespace` conservará todos los tipos que contiene.
Por último, cualquier tipo cuyo nombre completo (incluido el espacio de nombres) coincida con el patrón de carácter comodín "My.Other\*" conservará todos sus campos y métodos. El carácter comodín `*` puede incluirse varias veces en un patrón de "nombre completo de tipo".



## <a name="related-links"></a>Vínculos relacionados

- [Vincular en iOS](~/ios/deploy-test/linker.md)
- [Vincular en Android](~/android/deploy-test/linker.md)
