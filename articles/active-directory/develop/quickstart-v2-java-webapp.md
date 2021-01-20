---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Java | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido descrive come accedere l'accesso con Microsoft a un'applicazione Web Java usando OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 91aa6c96c714bff26ea7e0df5b2b6971c68edec0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178569"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Java

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione Web Java che consente agli utenti di accedere e chiamare l'API Microsoft Graph. Possono accedere all'applicazione gli utenti di qualsiasi organizzazione Azure Active Directory (Azure AD).

 Per una panoramica, vedere il [diagramma del funzionamento dell'esempio](#how-the-sample-works).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo esempio, sono necessari:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 o versione successiva. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (opzione 1) e manuale (opzione 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Immettere un nome per l'applicazione e quindi fare clic su **Registra**.
> 1. Seguire le istruzioni nell'esperienza di avvio rapido del portale per scaricare il codice dell'applicazione configurato automaticamente.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Se si ha accesso a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui registrare l'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app**.
> 1. Selezionare **Nuova registrazione**.
> 1. In **Nome** immettere un nome per l'applicazione, ad esempio **java-webapp**. Questo nome può essere visualizzato dagli utenti dell'app. È possibile cambiarlo in seguito.
> 1. Selezionare **Registra**.
> 1. Nella pagina **Panoramica** prendere nota dei valori di **ID applicazione (client)** e **ID della directory (tenant)** . Questi valori saranno necessari più avanti.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Selezionare **Aggiungi una piattaforma** > **Web**.
> 1. Nella sezione **URI di reindirizzamento** immettere `https://localhost:8443/msal4jsample/secure/aad`.
> 1. Selezionare **Configura**.
> 1. Nella sezione **Web**, sotto **URI di reindirizzamento**, immettere `https://localhost:8443/msal4jsample/graph/me` come secondo URI di reindirizzamento.
> 1. In **Gestisci**, selezionare **Certificati e segreti**. Nella sezione **Segreti client** selezionare **Nuovo segreto client**.
> 1. Digitare una descrizione della chiave (ad esempio *segreto app*), lasciare la scadenza predefinita e selezionare **Aggiungi**.
> 1. Prendere nota del **Valore** del segreto client. Sarà necessario più avanti.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per usare l'esempio di codice di questa guida di avvio rapido, è necessario:
>
> 1. Aggiungere gli URL di risposta `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me`.
> 1. Creare un segreto client.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-code-sample"></a>Passaggio 2: Scaricare il codice di esempio
> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Scaricare il progetto ed estrarre il file ZIP in una cartella vicina alla radice dell'unità. Ad esempio, *C:\Azure-Samples*.
>
> Per usare HTTPS con localhost, specificare le proprietà `server.ssl.key`. Per generare un certificato autofirmato, usare l'utilità keytool (inclusa in JRE).
>
> Ecco un esempio:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Inserire il file dell'archivio chiavi generato nella cartella *resources*.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Passaggio 3: Configurare il codice di esempio
> 1. Estrarre il file ZIP in una cartella locale.
> 1. *Facoltativo.* Se si usa un ambiente di sviluppo integrato, aprire l'esempio al suo interno.
> 1. Aprire il file *application.properties*. È disponibile nella cartella *src/main/resources/* . Sostituire i valori nei campi `aad.clientId`, `aad.authority` e `aad.secretKey` rispettivamente con i valori di ID applicazione, ID tenant e segreto client. Il risultato è analogo al seguente:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    Nel codice precedente:
>
>    - `Enter_the_Application_Id_here` è l'ID dell'applicazione registrata.
>    - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.
>    - `Enter_the_Tenant_Info_Here` è il valore dell'**ID directory (tenant)** dell'applicazione registrata.
> 1. Per usare HTTPS con localhost, specificare le proprietà `server.ssl.key`. Per generare un certificato autofirmato, usare l'utilità keytool (inclusa in JRE).
>
>    Ecco un esempio:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Inserire il file dell'archivio chiavi generato nella cartella *resources*.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passaggio 3: Eseguire il codice di esempio
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

Per eseguire il progetto, eseguire una delle operazioni seguenti:

- Eseguirlo direttamente dall'IDE usando il server Spring Boot incorporato. 
- Creare un pacchetto in un file WAR usando [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e quindi distribuirlo in una soluzione contenitore J2EE come [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Esecuzione del progetto da un IDE

Per eseguire l'applicazione Web da un ambiente IDE, selezionare Esegui, quindi passare alla home page del progetto. Per questo esempio, l'URL dell'home page standard è https://localhost:8443.

1. Nella pagina iniziale selezionare il pulsante **Accedi** per reindirizzare gli utenti ad Azure Active Directory e richiedere le credenziali.

1. Una volta autenticati, gli utenti vengono reindirizzati a `https://localhost:8443/msal4jsample/secure/aad`. Ora sono connessi e nella pagina vengono visualizzate le informazioni sull'account utente. L'interfaccia utente di esempio include i pulsanti seguenti:
    - **Disconnetti**: disconnette l'utente corrente dall'applicazione e lo reindirizza alla home page.
    - **Show User Info** (Mostra informazioni utente): acquisisce un token per Microsoft Graph e chiama Microsoft Graph con una richiesta contenente il token, che restituisce informazioni di base sull'utente che ha eseguito l'accesso.

##### <a name="running-the-project-from-tomcat"></a>Esecuzione del progetto da Tomcat

Se si vuole distribuire l'esempio Web in Tomcat, sarà necessario apportare alcune modifiche al codice sorgente.

1. Aprire *ms-identity-java-webapp/pom.xml*.
    - In `<name>msal-web-sample</name>` aggiungere `<packaging>war</packaging>`.

2. Aprire *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Eliminare tutto il codice sorgente e sostituirlo con il codice seguente:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   La porta HTTP predefinita di Tomcat è 8080, ma è necessaria una connessione HTTPS sulla porta 8443. Per configurare questa impostazione:
        - Passare a *tomcat/conf/server.xml*.
        - Cercare il tag `<connector>` e sostituire il connettore esistente con questo:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Aprire una finestra del prompt dei comandi. Passare alla cartella radice di questo esempio (in cui si trova il file pom.xml) ed eseguire `mvn package` per compilare il progetto.
    - Questo comando genera un file *msal-web-sample-0.1.0.war* nella directory */targets*.
    - Rinominare questo file in *msal4jsample.war*.
    - Distribuire il file WAR usando Tomcat o un'altra soluzione contenitore J2EE.
        - Per distribuire il file msal4jsample.war, copiarlo nella directory */webapps/* dell'installazione di Tomcat e quindi avviare il server Tomcat.

5. Dopo aver distribuito il file, passare a https://localhost:8443/msal4jsample usando un browser.


> [!IMPORTANT]
> L'applicazione di questa guida di avvio rapido usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di usare l'applicazione in un ambiente di produzione. Per altre informazioni sull'utilizzo di un certificato, vedere [Credenziali del certificato per l'autenticazione dell'applicazione](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Diagramma che mostra come funziona l'app di esempio generata da questa guida di avvio rapido.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Ottenere MSAL

MSAL per Java (MSAL4J) è la libreria Java usata per far accedere gli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform.

Aggiungere MSAL4J all'applicazione usando Maven o Gradle per gestire le dipendenze apportando le modifiche seguenti al file pom.xml (Maven) o build.gradle (Gradle) dell'applicazione.

In pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>Inizializzare MSAL

Aggiungere un riferimento a MSAL per Java aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione più dettagliata della creazione di app Web per l'accesso degli utenti in Microsoft Identity Platform, vedere la serie di scenari in più parti:

> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md?tabs=java)
