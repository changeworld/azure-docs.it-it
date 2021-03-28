---
title: Esercitazione per la configurazione di Azure Active Directory B2C con Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Esercitazione per configurare Azure Active Directory B2C con Microsoft Dynamics 365 Fraud Protection per identificare un account rischioso e fraudolento
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9483b0a8829c042a7cf8d516c6007cbbf14a97ac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639752"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Esercitazione: configurare Microsoft Dynamics 365 Fraud Protection con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) con la Azure Active Directory (ad) B2C.

Microsoft DFP fornisce ai client la possibilità di valutare se il rischio di tentativi di creazione di nuovi account e tentativi di accesso all'ecosistema del client sono fraudolenti. Microsoft DFP assessment può essere usato dal cliente per bloccare o sfidare tentativi sospetti di creare nuovi account falsi o compromettere gli account esistenti. La protezione degli account include l'intelligenza artificiale basata sull'impronta digitale dei dispositivi, le API per la valutazione dei rischi in tempo reale, l'esperienza di regole e elenchi per ottimizzare la strategia di rischio in base alle esigenze aziendali del cliente e una scorecard per monitorare l'efficacia e le tendenze della protezione da frodi nell'ecosistema del client.

In questo esempio verranno integrate le funzionalità di protezione dell'account di Microsoft DFP con un flusso utente Azure AD B2C. Il servizio eliminerà l'impronta digitale di ogni tentativo di accesso o di iscrizione e osserverà eventuali comportamenti sospetti passati o presenti. Azure AD B2C richiama un endpoint decisionale da Microsoft DFP, che restituisce un risultato in base a tutti i comportamenti passati e presenti dall'utente identificato e anche alle regole personalizzate specificate nel servizio Microsoft DFP. Azure AD B2C prende una decisione di approvazione basata su questo risultato e passa lo stesso a Microsoft DFP.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](./tutorial-create-tenant.md). Il tenant è collegato alla sottoscrizione di Azure.

- Ottenere una [sottoscrizione](https://dynamics.microsoft.com/pricing/#Sales)di Microsoft DFP. È anche possibile configurare una [versione del client di valutazione](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) .

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Microsoft DFP include i componenti seguenti:

- **Tenant Azure ad B2C**: autentica l'utente e funge da client di Microsoft DFP. Ospita uno script di impronta digitale che raccoglie i dati di identificazione e diagnostica di ogni utente che esegue un criterio di destinazione. Blocchi successivi o problemi durante l'accesso o l'iscrizione se Microsoft DFP li trova sospetti.

- **Servizio app personalizzato**: un'applicazione Web che svolge due finalità.

  - Serve le pagine HTML da usare come interfaccia utente del Framework dell'esperienza di identità. Responsabile dell'incorporamento dello script di impronta digitale di Microsoft Dynamics 365.

  - Un controller API con endpoint RESTful che connette Microsoft DFP a Azure AD B2C. L'elaborazione, la struttura e l'elaborazione dei dati dell'handle sono conformi ai requisiti di sicurezza di entrambi.

- **Servizio di impronta digitale di Microsoft DFP**: script incorporato in modo dinamico, che registra i dati di telemetria del dispositivo e gli utenti autocertificati per creare un'impronta digitale univocamente identificabile per l'utente da usare in un secondo momento nel processo decisionale.

- **Endpoint dell'API Microsoft DFP**: fornisce il risultato della decisione e accetta uno stato finale che riflette l'operazione eseguita dall'applicazione client. Azure AD B2C non comunica direttamente con gli endpoint a causa dei requisiti di sicurezza e payload API variabili, USA invece il servizio app come intermedio.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Immagine mostra il diagramma dell'architettura di Microsoft Dynamics 365 Fraud Protection](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'iscrizione per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato che può essere utilizzato da Microsoft DFP API. Quindi, dopo l'invio a Microsoft DFP API.
| 4. | Quando l'API di Microsoft DFP usa le informazioni e le elabora, restituisce il risultato all'API del livello intermedio.
| 5. | L'API del livello intermedio elabora le informazioni e invia le informazioni rilevanti a Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e scritto nella directory.

## <a name="set-up-the-solution"></a>Configurare la soluzione

1. [Creare un'applicazione Facebook](./identity-provider-facebook.md#create-a-facebook-application) configurata per consentire la Azure ad B2C della Federazione.
2. [Aggiungere il segreto di Facebook](./custom-policy-get-started.md#create-the-facebook-key) creato come chiave dei criteri del Framework dell'esperienza di identità.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configurare l'applicazione in Microsoft DFP

[Configurare il tenant di Azure ad per l'](/dynamics365/fraud-protection/integrate-real-time-api) uso di Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Eseguire la distribuzione nell'applicazione Web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementare l'impronta digitale del servizio Microsoft DFP

L' [impronta digitale dei dispositivi Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) è un requisito per la protezione degli account Microsoft DFP.

>[!NOTE]
>Oltre a Azure AD B2C pagine dell'interfaccia utente, il cliente può implementare anche il servizio di impronta digitale all'interno del codice dell'app per la profilatura dei dispositivi più completa. Il servizio di impronta digitale nel codice dell'app non è incluso in questo esempio.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Distribuire il codice dell'API Azure AD B2C

Distribuire il [codice API fornito](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) a un servizio di Azure. Il codice può essere [pubblicato da Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Configurare CORS, aggiungere l' **origine consentita**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>In seguito sarà necessario l'URL del servizio distribuito per configurare Azure AD con le impostazioni necessarie.

Per altre informazioni, vedere la [documentazione del servizio app](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Aggiungere impostazioni di configurazione dipendenti dal contesto

Configurare le impostazioni dell'applicazione nel [servizio app in Azure](../app-service/configure-common.md#configure-app-settings). Questo consente di configurare in modo sicuro le impostazioni senza archiviarle in un repository. Per l'API REST sono necessarie le impostazioni seguenti:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Configurazione di Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | ID cliente per le impronte digitali dei dispositivi Microsoft |     |
| FraudProtectionSettings:ApiBaseUrl |  URL di base del portale di Microsoft DFP   | Rimuovere '-int ' per chiamare invece l'API di produzione|
|  TokenProviderConfig: risorsa  | URL di base- https://api.dfp.dynamics-int.com     | Rimuovere '-int ' per chiamare invece l'API di produzione|
|   TokenProviderConfig: ClientID       |Il commerciante della protezione illecito Azure AD ID app client      |       |
| TokenProviderConfig: Authority | https://login.microsoftonline.com/<directory_ID> | Il commerciante della protezione illecito Azure AD autorità tenant |
| TokenProviderConfig: CertificateThumbprint * | Identificazione personale del certificato da usare per l'autenticazione nell'app client Merchant Azure AD |
| TokenProviderConfig: ClientSecret * | Il segreto per l'app client Merchant Azure AD | È consigliabile usare un gestore di segreti |

* Impostare solo 1 dei 2 parametri contrassegnati a seconda che si esegua l'autenticazione con un certificato o un segreto, ad esempio una password.

## <a name="azure-ad-b2c-configuration"></a>Configurazione di Azure AD B2C

### <a name="replace-the-configuration-values"></a>Sostituire i valori di configurazione

Nei [criteri personalizzati](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)forniti individuare i segnaposti seguenti e sostituirli con i valori corrispondenti dell'istanza.

| Segnaposto | Sostituire con | Note |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Nome breve del tenant |  "tenantinuso" da yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID tenant del tenant di Azure AD B2C |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID app dell'app IdentityExperienceFramework configurata nel tenant di Azure AD B2C    |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID app dell'app ProxyIdentityExperienceFramework configurata nel tenant di Azure AD B2C      |   01234567-89ab-cdef-0123-456789ABCDEF     |
|  {your_tenant_extensions_appid}   |  ID app dell'applicazione di archiviazione del tenant   |  01234567-89ab-cdef-0123-456789ABCDEF  |
|   {your_tenant_extensions_app_objectid}  | ID oggetto dell'applicazione di archiviazione del tenant    | 01234567-89ab-cdef-0123-456789ABCDEF   |
|   {your_app_insights_instrumentation_key}  |   Chiave di strumentazione dell'istanza di Application Insights *  |   01234567-89ab-cdef-0123-456789ABCDEF |
|  {your_ui_base_url}   | Endpoint nel servizio app da cui vengono serviti i file dell'interfaccia utente    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | URL del servizio app    |  https://yourapp.azurewebsites.net  |
|   {Your-Facebook-App-ID}  |  ID app dell'app Facebook configurata per la Federazione con Azure AD B2C   | 000000000000000   |
|  {Your-Facebook-App-Secret}   |  Nome della chiave del criterio in cui è stato salvato il segreto dell'app di Facebook   | B2C_1A_FacebookAppSecret   |

* App Insights può trovarsi in un tenant diverso. Questo passaggio è facoltativo. Se non è necessario, rimuovere i elementi technicalprofile e OrechestrationSteps corrispondenti.

### <a name="call-microsoft-dfp-label-api"></a>Chiama API Microsoft DFP label

I clienti devono [implementare l'API delle etichette](/dynamics365/fraud-protection/integrate-ap-api). Per altre informazioni, vedere l' [API di Microsoft DFP](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) .

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Il valore dell'ID utente deve corrispondere a quello del valore di configurazione Azure AD B2C corrispondente (ObjectID).

>[!NOTE]
>Aggiungere la notifica di consenso alla pagina della raccolta di attributi. Notificare che la telemetria degli utenti e le informazioni sull'identità utente verranno registrate a scopo di protezione dell'account.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurare i criteri di Azure AD B2C

1. Passare al [criterio Azure ad B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) nella cartella criteri.

2. Segui questo [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) per scaricare lo [Starter Pack le LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurare i criteri per il tenant Azure AD B2C.

>[!NOTE]
>Aggiornare i criteri forniti per la correlazione con il tenant specifico.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **Framework esperienza di identità**.

2. Selezionare il **SignUpSignIn** creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Il servizio Microsoft DFP verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

>[!NOTE]
>Aggiornare le regole direttamente nel portale di Microsoft DFP se si usa il [motore regole di Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Esempi di Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
