---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Python | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'app Web Python può concedere l'accesso agli utenti, ottenere un token di accesso da Microsoft Identity Platform e chiamare l'API Microsoft Graph.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3c3eaddf1767a3fa4a2ba73ae7a27f1f7df13990
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178204"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione Web Python che consente agli utenti di accedere e ottenere un token di accesso per chiamare l'API Microsoft Graph. Possono accedere all'applicazione gli utenti con account Microsoft o un account di qualsiasi organizzazione Azure Active Directory (Azure AD).

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) o [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [requests](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
>
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (Opzione 1) e manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">Portale di Azure - Registrazioni app<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. In **Nome** immettere un nome per l'applicazione, ad esempio `python-webapp`. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
> 1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
> 1. Selezionare **Registra**.
> 1. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. In **Gestisci** selezionare **Autenticazione**.
> 1. Selezionare **Aggiungi una piattaforma** > **Web**.
> 1. Aggiungere `http://localhost:5000/getAToken` come valore di **URI di reindirizzamento**.
> 1. Selezionare **Configura**.
> 1. In **Gestisci** selezionare **Certificati e segreti** e nella sezione **Segreti client** selezionare **Nuovo segreto client**.
> 1. Digitare una descrizione della chiave (ad esempio segreto app), lasciare la scadenza predefinita e selezionare **Aggiungi**.
> 1. Prendere nota del **valore** di **Segreto client** per usarlo successivamente.
> 1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**.
>1.  Verificare che la scheda **API Microsoft** sia selezionata.
> 1. Nella sezione *API Microsoft più usate* selezionare **Microsoft Graph**.
> 1. Nella sezione **Autorizzazioni delegate** verificare che siano selezionate le autorizzazioni corrette: **User.ReadBasic.All**. Se necessario, usare la casella di ricerca.
> 1. Selezionare il pulsante **Aggiungi autorizzazioni**.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il corretto funzionamento del codice di esempio di questo argomento di avvio rapido, è necessario:
>
> 1. Aggiungere `http://localhost:5000/getAToken` come URL di risposta.
> 1. Creare un segreto client.
> 1. Aggiungere l'autorizzazione delegata User.ReadBasic.All dell'API Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questo attributo

#### <a name="step-2-download-your-project"></a>Passaggio 2: Scaricare il progetto
> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Scaricare il progetto ed estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Passaggio 3: Configurare l'applicazione
>
> 1. Estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
> 1. Se si usa un IDE (Integrated Development Environment), aprire l'esempio al suo interno.
> 1. Aprire il file **app_config.py**, presente nella cartella radice e sostituirne il contenuto con il frammento di codice seguente:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Dove:
>
> - `Enter_the_Application_Id_here` è l'ID applicazione dell'applicazione registrata.
> - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.
> - `Enter_the_Tenant_Name_Here` - è il valore dell'**ID directory (tenant)** dell'applicazione registrata.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passaggio 3: Eseguire il codice di esempio

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

1. È necessario installare la libreria MSAL Python, il framework Flask, Flask-Sessions per la gestione delle sessioni sul lato server e requests con pip come descritto di seguito:

    ```Shell
    pip install -r requirements.txt
    ```

2. Eseguire app.py dalla shell o dalla riga di comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni sull'uso di un certificato, vedere [queste istruzioni](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Recupero di MSAL
MSAL è la libreria usata per l'accesso degli utenti e per richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform.
È possibile aggiungere MSAL Python all'applicazione tramite PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL
È possibile inserire il riferimento a MSAL Python aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle app Web che concedono l'accesso agli utenti in una serie di scenari in più parti.

> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
