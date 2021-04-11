---
title: Risolvere i problemi relativi ai criteri personalizzati con Application Insights
titleSuffix: Azure AD B2C
description: Come configurare Application Insights per tracciare l'esecuzione dei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028744"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Raccogliere i log di Azure Active Directory B2C con Application Insights

Questo articolo illustra i passaggi per la raccolta di log da Active Directory B2C (Azure AD B2C) in modo da poter diagnosticare i problemi relativi ai criteri personalizzati. servizio che consente di diagnosticare le eccezioni e di visualizzare i problemi di prestazioni delle applicazioni. Azure AD B2C supporta una funzionalità per l'invio di dati ad Application Insights.

I log attività dettagliati descritti in questa sezione devono essere abilitati **solo** durante lo sviluppo dei criteri personalizzati.

> [!WARNING]
> Non impostare `DeploymentMode` su `Development` negli ambienti di produzione. I log raccolgono tutte le attestazioni inviate da e verso i provider di identità. Gli sviluppatori si assumono la responsabilità di tutti i dati personali raccolti nei log del Application Insights. Questi log dettagliati vengono raccolti solo quando i criteri vengono inseriti in **modalità sviluppatore**.

## <a name="set-up-application-insights"></a>Configurare Application Insights

Se non ne è già presente uno, creare un'istanza di Application Insights nella sottoscrizione.

> [!TIP]
> Per più tenant Azure AD B2C è possibile usare una singola istanza di Application Insights. Nella query è quindi possibile filtrare in base al tenant o al nome del criterio. Per ulteriori informazioni, [vedere i log in Application Insights](#see-the-logs-in-application-insights) esempi.

Per usare un'istanza di uscita di Application Insights nella sottoscrizione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure (non la directory di Azure ad B2C).
1. Aprire la risorsa Application Insights creata in precedenza.
1. Nella pagina **Overview (panoramica** ) e registrare la **chiave di strumentazione**

Per creare un'istanza di Application Insights nella sottoscrizione, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure (non la directory di Azure ad B2C).
1. Selezionare **Crea una risorsa** nel menu di spostamento a sinistra.
1. Cercare e selezionare **Application Insights**, quindi selezionare **Crea**.
1. Completare il modulo, selezionare **Verifica + crea**, quindi selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
1. In **Configura** in Application Insights menu selezionare **Proprietà**.
1. Registrare la **chiave di strumentazione** per l'uso in un passaggio successivo.

## <a name="configure-the-custom-policy"></a>Configurare i criteri personalizzati

1. Aprire il file relying party (RP), ad esempio *SignUpOrSignin.xml*.
1. Aggiungere gli attributi seguenti all'elemento `<TrustFrameworkPolicy>`:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se non esiste già, aggiungere un `<UserJourneyBehaviors>` nodo figlio al `<RelyingParty>` nodo. Deve trovarsi dopo `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`. Assicurarsi di sostituire `{Your Application Insights Key}` con la chiave di **strumentazione** Application Insights registrata in precedenza.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` indica a ApplicationInsights di accelerare i dati di telemetria attraverso la pipeline di elaborazione. Ideale per lo sviluppo, ma vincolato a volumi elevati. In produzione, impostare `DeveloperMode` su `false` .
    * `ClientEnabled="true"` Invia lo script del lato client di ApplicationInsights per tenere traccia degli errori sul lato client e sulla visualizzazione pagina. È possibile visualizzarli nella tabella **browserTimings** nel portale di Application Insights. Impostando `ClientEnabled= "true"` , si aggiungono Application Insights allo script di pagina e si ottengono i tempi di caricamento delle pagine e le chiamate AJAX, i conteggi, i dettagli delle eccezioni del browser e degli errori Ajax, nonché i conteggi degli utenti e delle sessioni. Questo campo è **facoltativo** e è impostato su per `false` impostazione predefinita.
    * `ServerEnabled="true"` invia l'elemento JSON UserJourneyRecorder esistente come evento personalizzato ad Application Insights.

    Ad esempio:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Caricare i criteri.

## <a name="see-the-logs-in-application-insights"></a>Visualizza i log in Application Insights

Si verifica un breve ritardo, in genere meno di cinque minuti, prima che sia possibile visualizzare nuovi log in Application Insights.

1. Aprire la risorsa di Application Insights creata nel [portale di Azure](https://portal.azure.com).
1. Nella pagina **Panoramica** selezionare **log**.
1. Aprire una nuova scheda in Application Insights.

Di seguito è riportato un elenco di query che è possibile usare per visualizzare i log:

| Query | Descrizione |
|---------------------|--------------------|
| `traces` | Ottenere tutti i log generati da Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Ottenere tutti i log generati da Azure AD B2C per l'ultimo giorno.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Ottenere tutti i log con errori delle ultime due ore.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Ottenere tutti i log generati dal tenant Azure AD B2C *contoso.onmicrosoft.com* e il percorso utente è *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Ottenere tutti i log generati da Azure AD B2C per un ID correlazione. Sostituire l'ID di correlazione con l'ID di correlazione. | 

Le voci possono essere lunghe. Per visualizzarle meglio è possibile esportarle in formato CSV.

Per altre informazioni sull'esecuzione di query, vedere [Panoramica delle query di log in monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>Vedere i log nell'estensione VS Code

Si consiglia di installare l' [estensione Azure ad B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) per [vs code](https://code.visualstudio.com/). Con l'estensione Azure AD B2C, i log sono organizzati in base al nome del criterio, all'ID di correlazione (Application Insights presenta la prima cifra dell'ID di correlazione) e al timestamp del log. Questa funzionalità consente di trovare il log pertinente in base al timestamp locale e di visualizzare il percorso utente come eseguito da Azure AD B2C.

> [!NOTE]
> La community ha sviluppato l'estensione di Visual Studio Code per Azure AD B2C per aiutare gli sviluppatori di identità. L'estensione non è supportata da Microsoft e viene resa disponibile esclusivamente così com'è.

### <a name="set-application-insights-api-access"></a>Impostare l'accesso all'API Application Insights

Dopo aver configurato il Application Insights e aver configurato i criteri personalizzati, è necessario ottenere l' **ID api** Application Insights e creare la **chiave API**. Sia l'ID API che la chiave API vengono usati dall'estensione Azure AD B2C per leggere gli eventi di Application Insights (telemetria). Le chiavi API devono essere gestite come le password. Mantenerla segreta.

> [!NOTE]
> Application Insights chiave di strumentazione che la creazione precedente viene usata da Azure AD B2C per inviare le telemetrie al Application Insights. Usare la chiave di strumentazione solo nei criteri di Azure AD B2C e non nell'estensione di Visual Studio Code.

Per ottenere Application Insights ID e la chiave:

1. In portale di Azure aprire la risorsa Application Insights per l'applicazione.
1. Selezionare **Impostazioni**, quindi selezionare **accesso all'API**.
1. Copiare l' **ID applicazione**
1. Selezionare **Crea chiave API**
1. Controllare la casella **Leggi telemetria** .
1. Copiare la **chiave** prima di chiudere il pannello Crea chiave API e salvarlo in un punto sicuro. Se si perde la chiave, è necessario crearne un'altra.

    ![Screenshot che illustra come creare una chiave di accesso all'API.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Configurare Azure AD B2C estensione VS Code

A questo punto, l'utente ha applicazione Azure ID e la chiave dell'API Insights, è possibile configurare l'estensione di Visual Studio Code per leggere i log. Azure AD B2C VS Code Extension fornisce due ambiti per le impostazioni:

- **Impostazioni globali utente** : le impostazioni che si applicano a livello globale a qualsiasi istanza di vs code si apre.
- **Impostazioni area di lavoro** : impostazioni archiviate nell'area di lavoro e applicabili solo quando l'area di lavoro viene aperta (usando vs Code **cartella aperta**).

1. Da Esplora **traccia Azure ad B2C** fare clic sull'icona **delle impostazioni** .

    ![Screenshot che illustra come selezionare le impostazioni di Application Insights.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Fornire l' **ID** e la **chiave** di applicazione Azure Insights.
1. Fare clic su **Save** (Salva).

Dopo aver salvato le impostazioni, i log di Application Insights vengono visualizzati nella finestra **traccia Azure ad B2C (App Insights)** .

![Screenshot dell'estensione Azure AD B2C per VSCODE, che presenta la traccia di applicazione Azure Insights.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Configurare Application Insights nell'ambiente di produzione

Per migliorare le prestazioni dell'ambiente di produzione e migliorare l'esperienza utente, è importante configurare i criteri per ignorare i messaggi che non sono importanti. Utilizzare la configurazione seguente per inviare solo messaggi di errore critici al Application Insights. 

1. Impostare l' `DeploymentMode` attributo di [TrustFrameworkPolicy](trustframeworkpolicy.md) su `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Impostare `DeveloperMode` di [JourneyInsights](relyingparty.md#journeyinsights) su `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Caricare e testare i criteri.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi Azure ad B2C criteri personalizzati](troubleshoot-custom-policies.md)
