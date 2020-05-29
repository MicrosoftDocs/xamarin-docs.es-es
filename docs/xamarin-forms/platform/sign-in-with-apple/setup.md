---
title: "pasos de configuración: Inicio de sesión con Apple para Xamarin.Forms " Descripción: "el inicio de sesión con el programa de instalación de Apple difiere en función de las distintas plataformas a las que se destina la aplicación móvil".
MS. Prod: Xamarin ms. AssetID: 8F712802-395B-469B-B5BE-C927AD1A8391 ms. Technology: Xamarin-Forms Author: davidortinau ms. Author: daortin ms. Date: 09/10/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>Configurar inicio de sesión con Apple paraXamarin.Forms

En esta guía se describe la serie de pasos necesarios para configurar las aplicaciones multiplataforma con el fin de seguir el inicio de sesión con Apple. Aunque el programa de instalación de Apple es sencillo en el portal para desarrolladores de Apple, es necesario realizar pasos adicionales para crear una relación segura entre Android y Apple. 

## <a name="apple-developer-setup"></a>Configuración de Apple Developer

Antes de poder usar el inicio de sesión con Apple en sus aplicaciones, debe abordar algunos pasos de configuración de la sección [certificados, identificadores & perfiles](https://developer.apple.com/account/resources/) del portal para desarrolladores de Apple.

### <a name="apple-sign-in-domain"></a>Dominio de inicio de sesión de Apple

Registre el nombre de dominio y compruébelo con Apple en la sección [más](https://developer.apple.com/account/resources/services/list) de la sección *certificados, identificadores & perfiles* .

![Sección más](sign-in-images/readme-signin-domain-configure.png)

Agregue el dominio y haga clic en **registrar**.

![registro del formulario de dominio](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> Si ve un error que le informará de que el dominio no es compatible con SPF, deberá agregar un registro TXT de DNS SPF a su dominio y esperar a que se propague antes de continuar: el TXT SPF puede tener un aspecto similar al siguiente:`v=spf1 a a:myapp.com -all`

A continuación, tendrá que comprobar la propiedad del dominio haciendo clic en **Descargar** para recuperar el `apple-developer-domain-association.txt` archivo y cargarlo en la `.well-known` carpeta del sitio web del dominio.

Una vez `.well-known/apple-developer-domain-association.txt` cargado el archivo y accesible, puede hacer clic en **comprobar** para que Apple Compruebe la propiedad del dominio.

> [!NOTE]
> Apple comprobará la propiedad con `https://` . Asegúrese de que tiene la configuración de SSL y de que el archivo es accesible a través de una dirección URL segura.

Este proceso se completó correctamente antes de continuar.

## <a name="setup-your-app-id"></a>Configuración del identificador de la aplicación

En la sección [identificadores](https://developer.apple.com/account/resources/identifiers/list) , cree un nuevo identificador y elija ID. de **aplicación**. Si ya tiene un ID. de aplicación, elija editarlo en su lugar.

![Crear un nuevo identificador de aplicación](sign-in-images/readme-appid-create.png)

Habilite el **Inicio de sesión con Apple**. Lo más probable es que quiera usar la opción **Habilitar como ID. de aplicación principal** .

![Habilitación del inicio de sesión con Apple](sign-in-images/readme-appid-signin.png)

Guarde los cambios en el identificador de la aplicación.

## <a name="create-a-service-id"></a>Crear un identificador de servicio

En la sección [identificadores](https://developer.apple.com/account/resources/identifiers/list/serviceId) , cree un nuevo identificador y elija ID. de **servicio**.

![Crear un nuevo identificador de servicio](sign-in-images/readme-serviceid-create.png)

Asigne a su ID. de servicios una descripción y un identificador.  Este identificador será su `ServerId` .  Asegúrese de habilitar el **Inicio de sesión con Apple**.

Antes de continuar, haga clic en **configurar** junto a la opción _iniciar sesión con Apple_ habilitada.

En el panel Configuración, asegúrese de que está seleccionado el **identificador de aplicación principal** correcto.

A continuación, elija el **dominio web** que configuró anteriormente.

Por último, agregue una o más **direcciones URL de retorno**.  Cualquier `redirect_uri` usuario que use más adelante debe registrarse aquí exactamente como lo usa.  Asegúrese `http://` de incluir o `https://` en la dirección URL cuando la escriba.

> [!NOTE]
> Con fines de prueba, no puede usar `127.0.0.1` ni `localhost` , pero puede usar otros dominios como `local.test` .  Si decide hacerlo, puede editar el archivo del equipo `hosts` para resolver este dominio ficticio en la dirección IP local.

![Configuración del inicio de sesión de Apple](sign-in-images/readme-serviceid-configure.png)

Guarde los cambios cuando termine.

## <a name="create-a-key-for-your-services-id"></a>Creación de una clave para el identificador de servicios

En la sección [claves](https://developer.apple.com/account/resources/authkeys/list) , cree una nueva **clave**.

Asigne un nombre a la clave y habilite el **Inicio de sesión con Apple**.

![Crear una nueva clave](sign-in-images/readme-key-create.png)

Haga clic en **configurar** después _iniciar sesión con Apple_.

Asegúrese de que el **identificador de aplicación principal** correcto está seleccionado y haga clic en **Guardar**.

Haga clic en **continuar** y, a continuación, **Regístrese** para crear la nueva clave.

A continuación, solo tendrá una oportunidad de descargar la clave que acaba de generar.  Haga clic en **Descargar**.

![Descargar clave](sign-in-images/readme-key-download.png)

Además, tome nota del **identificador de clave** en este paso. Se usará para su `KeyId` posterior.

Habrá descargado un `.p8` archivo de clave.  Puede abrir este archivo en el Bloc de notas o VSCode para ver el contenido del texto.  Deben tener un aspecto similar al siguiente:

```
-----BEGIN PRIVATE KEY-----
MIGTAgEAMBMGBasGSM49AgGFCCqGSM49AwEHBHkwdwIBAQQg3MX8n6VnQ2WzgEy0
Skoz9uOvatLMKTUIPyPCAejzzUCgCgYIKoZIzj0DAQehRANCAARZ0DoM6QPqpJxP
JKSlWz0AohFhYre10EXPkjrih4jTm+b0AeG2BGuoIWd18i8FimGDgK6IzHHPsEqj
DHF5Svq0
-----END PRIVATE KEY-----
```

Asigne un nombre `P8FileContents` a esta clave y guárdela en un lugar seguro. Lo usará al integrar este servicio en su aplicación móvil.

## <a name="summary"></a>Resumen

En este artículo se describen los pasos necesarios para configurar el inicio de sesión con Apple para su uso en las Xamarin.Forms aplicaciones.

## <a name="related-links"></a>Vínculos relacionados

- [Inicio de sesión con las directrices de Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  