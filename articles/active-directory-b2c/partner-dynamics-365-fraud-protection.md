---
title: Esercitazione per configurare Azure Active Directory B2C con Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Esercitazione per configurare Azure Active Directory B2C con Microsoft Dynamics 365 Fraud Protection per identificare account rischiosi e fraudolenti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587817"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Esercitazione: Configurare Microsoft Dynamics 365 Fraud Protection con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite indicazioni su come integrare [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) con Azure Active Directory (AD) B2C.

Microsoft DFP offre ai client la possibilità di valutare se il rischio di tentativi di creare nuovi account e tentativi di accesso all'ecosistema del client è fraudolento. La valutazione di Microsoft DFP può essere usata dal cliente per bloccare o contestare tentativi sospetti di creare nuovi account falsi o di compromettere gli account esistenti. La protezione degli account include l'impronta digitale dei dispositivi ottimizzata per l'intelligenza artificiale, le API per la valutazione dei rischi in tempo reale, l'esperienza di regole ed elenchi per ottimizzare la strategia di rischio in base alle esigenze aziendali del cliente e una scorecard per monitorare l'efficacia e le tendenze della protezione delle frodi nell'ecosistema del cliente.

In questo esempio verranno integrazione le funzionalità di protezione degli account di Microsoft DFP con un flusso Azure AD B2C utente. Il servizio effettua esternamente l'impronta digitale di ogni tentativo di accesso o iscrizione e verifica eventuali comportamenti sospetti passati o presenti. Azure AD B2C richiama un endpoint decisionale da Microsoft DFP, che restituisce un risultato in base a tutti i comportamenti passati e presenti dell'utente identificato, oltre alle regole personalizzate specificate all'interno del servizio Microsoft DFP. Azure AD B2C prende una decisione di approvazione in base a questo risultato e lo passa nuovamente a Microsoft DFP.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- Un [tenant Azure AD B2C .](./tutorial-create-tenant.md) Il tenant è collegato alla sottoscrizione di Azure.

- Ottenere una sottoscrizione [](https://dynamics.microsoft.com/pricing/#Sales)di Microsoft DFP. È anche possibile configurare una versione [di valutazione del client.](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Microsoft DFP include i componenti seguenti:

- **Azure AD B2C tenant:** autentica l'utente e funge da client di Microsoft DFP. Ospita uno script di rilevamento delle impronte digitali che raccoglie i dati di identificazione e diagnostica di ogni utente che esegue un criterio di destinazione. Blocchi o problemi successivi di accesso o tentativi di iscrizione se Microsoft DFP li rileva sospetti.

- **Servizio app personalizzato:** un'applicazione Web che svolge due scopi.

  - Serve pagine HTML da usare come interfaccia Identity Experience Framework'interfaccia utente di . Responsabile dell'incorporamento dello script di rilevamento delle impronte digitali di Microsoft Dynamics 365.

  - Un controller API con endpoint RESTful che connette Microsoft DFP a Azure AD B2C. Gestire l'elaborazione dei dati, la struttura e rispetta i requisiti di sicurezza di entrambi.

- Servizio di impronta digitale **Microsoft DFP:** script incorporato dinamicamente, che registra i dati di telemetria del dispositivo e i dettagli dell'utente autocertificati per creare un'impronta digitale identificabile in modo univoco per l'utente da usare più avanti nel processo decisionale.

- **Endpoint dell'API Microsoft DFP:** fornisce il risultato della decisione e accetta uno stato finale che riflette l'operazione eseguita dall'applicazione client. Azure AD B2C comunica direttamente con gli endpoint a causa dei diversi requisiti di sicurezza e payload dell'API, usa invece il servizio app come intermedio.

Il diagramma dell'architettura seguente illustra l'implementazione.

![Immagine che mostra il diagramma dell'architettura di microsoft dynamics365 fraud protection](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'iscrizione per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API di livello intermedio e passa gli attributi utente.
| 3. | L'API di livello intermedio raccoglie gli attributi utente e lo trasforma in un formato che potrebbe essere utilizzato dall'API Microsoft DFP. Quindi, dopo l'invio all'API Microsoft DFP.
| 4. | Dopo che l'API Microsoft DFP usa le informazioni e le elabora, restituisce il risultato all'API di livello intermedio.
| 5. | L'API di livello intermedio elabora le informazioni e restituisce le informazioni rilevanti Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API di livello intermedio. Se viene visualizzata una risposta di errore, all'utente viene visualizzato un messaggio di errore. Se viene visualizzata una risposta Operazione riuscita, l'utente viene autenticato e scritto nella directory.

## <a name="set-up-the-solution"></a>Configurare la soluzione

1. [Creare un'applicazione Facebook configurata](./identity-provider-facebook.md#create-a-facebook-application) per consentire la federazione Azure AD B2C.
2. [Aggiungere il segreto di Facebook](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) creato come chiave Identity Experience Framework criteri.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configurare l'applicazione in Microsoft DFP

[Configurare il tenant Azure AD per](/dynamics365/fraud-protection/integrate-real-time-api) l'uso di Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Eseguire la distribuzione nell'applicazione Web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementare l'impronta digitale del servizio Microsoft DFP

[L'impronta digitale del dispositivo Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) è un requisito per la protezione dell'account microsoft DFP.

>[!NOTE]
>Oltre a Azure AD B2C dell'interfaccia utente, il cliente può implementare anche il servizio di impronta digitale all'interno del codice dell'app per una profilatura del dispositivo più completa. Il servizio di impronta digitale nel codice dell'app non è incluso in questo esempio.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Distribuire il codice Azure AD B2C'API

Distribuire il [codice API fornito](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) in un servizio di Azure. Il codice può essere [pubblicato da Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Configurare CORS, aggiungere **l'origine consentita**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>In seguito sarà necessario l'URL del servizio distribuito per configurare Azure AD con le impostazioni necessarie.

Per [altre informazioni, vedere la](../app-service/app-service-web-tutorial-rest-api.md) documentazione del servizio app.

### <a name="add-context-dependent-configuration-settings"></a>Aggiungere impostazioni di configurazione dipendenti dal contesto

Configurare le impostazioni dell'applicazione nel [servizio app in Azure](../app-service/configure-common.md#configure-app-settings). In questo modo le impostazioni possono essere configurate in modo sicuro senza archiviarle in un repository. L'API Rest richiede le impostazioni seguenti:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Configurazione di Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | ID cliente per l'impronta digitale del dispositivo Microsoft |     |
| FraudProtectionSettings:ApiBaseUrl |  URL di base dal portale di Microsoft DFP   | Rimuovere '-int' per chiamare invece l'API di produzione|
|  TokenProviderConfig: risorsa  | URL di base: https://api.dfp.dynamics-int.com     | Rimuovere '-int' per chiamare invece l'API di produzione|
|   TokenProviderConfig:ClientId       |ID dell'app client Azure AD'esercente di Fraud Protection      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | L'esercente di Fraud Protection Azure AD'autorità tenant |
| TokenProviderConfig:CertificateThumbprint* | Identificazione personale del certificato da usare per l'autenticazione nell'app client Azure AD esercente |
| TokenProviderConfig:ClientSecret* | Il segreto per l'app client Azure AD esercente | È consigliabile usare un gestore dei segreti |

*Impostare solo 1 dei 2 parametri contrassegnati a seconda se si esegue l'autenticazione con un certificato o un segreto, ad esempio una password.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C configurazione

### <a name="replace-the-configuration-values"></a>Sostituire i valori di configurazione

Nei criteri [personalizzati forniti](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)trovare i segnaposto seguenti e sostituirli con i valori corrispondenti dell'istanza.

| Segnaposto | Sostituire con | Note |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Nome breve del tenant |  "yourtenant" da yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID tenant del tenant Azure AD B2C |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID app dell'app IdentityExperienceFramework configurata nel tenant Azure AD B2C servizio    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID app dell'app ProxyIdentityExperienceFramework configurata nel tenant Azure AD B2C      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  ID app dell'applicazione di archiviazione del tenant   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | ID oggetto dell'applicazione di archiviazione del tenant    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Chiave di strumentazione dell'istanza di App Insights*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Endpoint nel servizio app da cui vengono serviti i file dell'interfaccia utente    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | URL del servizio app    |  `https://yourapp.azurewebsites.net`  |
|   {your-facebook-app-id}  |  ID app dell'app facebook configurata per la federazione con Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Nome della chiave dei criteri salvata come segreto dell'app di Facebook   | B2C_1A_FacebookAppSecret   |

*Le informazioni dettagliate sulle app possono essere in un tenant diverso. Questo passaggio è facoltativo. Rimuovere i corrispondenti TechnicalProfiles e OrechestrationSteps, se non necessario.

### <a name="call-microsoft-dfp-label-api"></a>Chiamare l'API etichetta Microsoft DFP

I clienti devono [implementare l'API etichetta](/dynamics365/fraud-protection/integrate-ap-api). Per [altre informazioni, vedere API Microsoft DFP.](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Il valore di userID deve essere uguale a quello nel valore di configurazione Azure AD B2C corrispondente (ObjectID).

>[!NOTE]
>Aggiungere la notifica di consenso alla pagina di raccolta degli attributi. Notificare che i dati di telemetria degli utenti e le informazioni sull'identità dell'utente verranno registrati ai fini della protezione dell'account.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurare i criteri Azure AD B2C

1. Passare al criterio [Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) nella cartella Criteri.

2. Seguire questo [documento per](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) scaricare lo starter [pack LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurare i criteri per il tenant Azure AD B2C servizio.

>[!NOTE]
>Aggiornare i criteri forniti per la relazione con il tenant specifico.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant Azure AD B2C e in Criteri selezionare **Identity Experience Framework**.

2. Selezionare l'account **SignUpSignIn creato in precedenza.**

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione:** selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta:** selezionare l'URL **di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Eseguire il flusso di iscrizione e creare un account

5. Il servizio Microsoft DFP verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, verificare che l'utente non sia salvato nella directory.

>[!NOTE]
>Aggiornare le regole direttamente nel portale di Microsoft DFP se si usa il motore regole [di Microsoft DFP.](/dynamics365/fraud-protection/rules)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [Esempi di Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
