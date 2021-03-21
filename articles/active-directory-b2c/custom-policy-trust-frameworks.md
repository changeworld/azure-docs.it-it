---
title: Panoramica dei criteri personalizzati Azure AD B2C | Microsoft Docs
description: Informazioni sui criteri personalizzati e sul framework dell'esperienza di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592841"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Panoramica dei criteri personalizzati Azure AD B2C

I criteri personalizzati sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C (Azure AD B2C). Sebbene i [flussi utente](user-flow-overview.md) siano predefiniti nel portale Azure ad B2C per le attività di identità più comuni, i criteri personalizzati possono essere modificati completamente da uno sviluppatore di identità per completare diverse attività.

Un criterio personalizzato è completamente configurabile e basato sui criteri. Un criterio personalizzato gestisce la relazione di trust tra le entità nei formati di protocollo standard, ad esempio OpenID Connect, OAuth, SAML e alcuni modelli non standard, ad esempio scambi di attestazioni da sistema a sistema basato su API REST. Il Framework crea esperienze intuitive e con etichetta bianca.

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono i blocchi predefiniti, l'interazione con l'utente e altre parti e la logica di business. 

## <a name="custom-policy-starter-pack"></a>Pacchetto Starter per i criteri personalizzati

Azure AD B2C [Starter Pack](custom-policy-get-started.md#get-the-starter-pack) per criteri personalizzati è dotato di diversi criteri predefiniti che consentono di iniziare rapidamente. Ogni pacchetto Starter contiene il numero minimo di profili tecnici e percorsi utente necessario per conseguire gli scenari descritti:

- **LocalAccounts** - Consente l'uso solo di account locali.
- **SocialAccounts** - Consente l'uso solo di account di social networking (o federati).
- **SocialAndLocalAccounts** - Consente sia l'uso di account locali che di account di social networking. La maggior parte degli esempi si riferiscono a questi criteri.
- **SocialAndLocalAccountsWithMFA** - Consente l'uso di opzioni di social networking, locali e di autenticazione a più fattori.

## <a name="understanding-the-basics"></a>Informazioni sulle nozioni di base 

### <a name="claims"></a>Attestazioni

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione dei criteri di Azure AD B2C. Può archiviare informazioni relative all'utente, ad esempio nome, cognome o qualsiasi altra attestazione ottenuta dall'utente o da altri sistemi (scambi di attestazioni). Lo [schema di attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni. 

Quando il criterio viene eseguito, Azure AD B2C Invia e riceve attestazioni da e verso entità interne ed esterne e quindi Invia un subset di tali attestazioni all'applicazione relying party come parte del token. Le attestazioni vengono utilizzate nei modi seguenti: 

- Un'attestazione viene salvata, letta o aggiornata in base all'oggetto utente della directory.
- Un'attestazione viene ricevuta da un provider di identità esterno.
- Le attestazioni vengono inviate o ricevute usando un servizio API REST personalizzato.
- I dati vengono raccolti come attestazioni dall'utente durante i flussi di iscrizione o di modifica del profilo.

### <a name="manipulating-your-claims"></a>Modifica delle attestazioni

Le [trasformazioni delle attestazioni](claimstransformations.md) sono funzioni predefinite che possono essere usate per convertire un'attestazione specificata in un'altra, valutare un'attestazione o impostare un valore di attestazione. Ad esempio l'aggiunta di un elemento a una raccolta di stringhe, la modifica del case di una stringa o la valutazione di un'attestazione di data e ora. Una trasformazione delle attestazioni specifica un metodo Transform. 

### <a name="customize-and-localize-your-ui"></a>Personalizzare e localizzare l'interfaccia utente

Quando si desidera raccogliere informazioni dagli utenti presentando una pagina nel browser Web, usare il [profilo tecnico autocertificato](self-asserted-technical-profile.md). È possibile modificare il profilo tecnico autocertificato per [aggiungere attestazioni e personalizzare l'input dell'utente](./configure-user-input.md).

Per [personalizzare l'interfaccia utente](customize-ui-with-html.md) per il profilo tecnico autocertificato, è necessario specificare un URL nell'elemento di [definizione del contenuto](contentdefinitions.md) con contenuto HTML personalizzato. Nel profilo tecnico autocertificato puntare a questo ID definizione del contenuto.

Per personalizzare le stringhe specifiche della lingua, usare l'elemento [Localization](localization.md) . Una definizione del contenuto può contenere un riferimento alla [localizzazione](localization.md) che specifica un elenco di risorse localizzate da caricare. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi mostra la pagina all'utente. 

## <a name="relying-party-policy-overview"></a>Panoramica dei criteri della relying party

Un'applicazione relying party, che nel protocollo SAML è nota come provider di servizi, chiama il [criterio relying party](relyingparty.md) per eseguire un percorso utente specifico. I criteri di relying party specificano il percorso utente da eseguire e l'elenco delle attestazioni incluse nel token. 

![Diagramma che mostra il flusso di esecuzione dei criteri](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Tutte le applicazioni di relying party che usano lo stesso criterio riceveranno le stesse attestazioni del token e l'utente procede nello stesso percorso utente.

### <a name="user-journeys"></a>Percorsi utente

I [percorsi utente](userjourneys.md) consentono di definire la logica di business con il percorso attraverso il quale l'utente seguirà per accedere all'applicazione. L'utente viene sottoposto al percorso utente per recuperare le attestazioni da presentare all'applicazione. Un percorso utente è costituito da una sequenza di [passaggi dell'orchestrazione](userjourneys.md#orchestrationsteps). Un utente deve raggiungere l'ultimo passaggio per acquisire un token. 

Le istruzioni seguenti descrivono come è possibile aggiungere passaggi di orchestrazione ai criteri di [Starter Pack per gli account di social networking e locali](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Di seguito è riportato un esempio di una chiamata API REST che è stata aggiunta.

![percorso utente personalizzato](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Passaggi dell'orchestrazione

Il passaggio dell'orchestrazione fa riferimento a un metodo che implementa la funzionalità o lo scopo designato. Questo metodo è denominato [profilo tecnico](technicalprofiles.md). Quando il percorso utente richiede una diramazione per rappresentare meglio la logica di business, il passaggio dell'orchestrazione fa riferimento a [subjourney](subjourneys.md). Un sub-Journey contiene un set di passaggi di orchestrazione.

Per acquisire un token, un utente deve raggiungere l'ultimo passaggio dell'orchestrazione nel percorso utente. Tuttavia, gli utenti potrebbero non dover attraversare tutti i passaggi dell'orchestrazione. I passaggi di orchestrazione possono essere eseguiti in modo condizionale in base alle [precondizioni](userjourneys.md#preconditions) definite nel passaggio dell'orchestrazione. 

Al termine di un passaggio di orchestrazione, Azure AD B2C archivia le attestazioni in output nell' **elenco delle attestazioni**. Le attestazioni nel contenitore delle attestazioni possono essere utilizzate da altri passaggi di orchestrazione nel percorso utente.

Nel diagramma seguente viene illustrato il modo in cui i passaggi dell'orchestrazione del percorso utente possono accedere all'elenco di attestazioni.

![Percorso utente Azure AD B2C](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Profilo tecnico

Un profilo tecnico fornisce un'interfaccia per comunicare con diversi tipi di entità. Un percorso utente combina la chiamata ai profili tecnici tramite i passaggi dell'orchestrazione per definire la logica di business.

Tutti i tipi di profili tecnici condividono lo stesso concetto. È possibile inviare attestazioni di input, eseguire la trasformazione delle attestazioni e comunicare con l'entità configurata. Al termine del processo, il profilo tecnico restituisce le attestazioni di output per il contenitore delle attestazioni. Per altre informazioni, vedere [Cenni preliminari sui profili tecnici](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Profilo tecnico di convalida

Quando un utente interagisce con l'interfaccia utente, può essere opportuno convalidare i dati raccolti. Per interagire con l'utente, è necessario usare un [profilo tecnico autocertificato](self-asserted-technical-profile.md) .

Per convalidare l'input dell'utente, viene chiamato un [profilo tecnico di convalida](validation-technical-profile.md) dal profilo tecnico autocertificato. Un profilo tecnico di convalida è un metodo per chiamare qualsiasi profilo tecnico non interattivo. In questo caso, il profilo tecnico può restituire attestazioni di output o un messaggio di errore. Viene eseguito il rendering del messaggio di errore all'utente sullo schermo, consentendo all'utente di riprovare.

Nel diagramma seguente viene illustrato il modo in cui Azure AD B2C utilizza un profilo tecnico di convalida per convalidare le credenziali utente.

![Diagramma profilo tecnico di convalida](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Modello di ereditarietà

Ogni Starter Pack include i file seguenti:

- un file di **base** che contiene la maggior parte delle definizioni. Per semplificare la risoluzione dei problemi e la manutenzione a lungo termine dei criteri, provare a ridurre al minimo il numero di modifiche apportate a questo file.
- Un file di **estensioni** che include le modifiche di configurazione univoche per il tenant. Questo file dei criteri è derivato dal file di base. Usare questo file per aggiungere nuove funzionalità o eseguire l'override delle funzionalità esistenti. Ad esempio, usare questo file per la federazione con nuovi provider di identità.
- Un file **Relying Party (RP)** è l'unico file incentrato sulle attività che viene chiamato direttamente dell'applicazione relying party, come applicazioni Web, mobili o desktop. Ogni attività univoca, ad esempio iscrizione, accesso, reimpostazione della password o modifica del profilo, richiede un proprio file di criteri relying party. Il file dei criteri è derivato dal file delle estensioni.

Il modello di ereditarietà è come segue:

- Il criterio figlio a qualsiasi livello può ereditare dal criterio padre ed estenderlo aggiungendo nuovi elementi.
- Per scenari più complessi, è possibile aggiungere più livelli di ereditarietà, fino a 10 in totale.
- È possibile aggiungere altri criteri di relying party. Ad esempio, eliminare il mio account, modificare un numero di telefono, SAML relying party Policy e altro ancora.

Il diagramma seguente mostra la relazione tra i file di criteri e le applicazioni relying party.

![Diagramma che mostra il modello di ereditarietà dei criteri di Framework attendibilità](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Materiale sussidiario e procedure consigliate

### <a name="best-practices"></a>Procedure consigliate

All'interno di un Azure AD B2C criteri personalizzati, è possibile integrare la logica di business per creare le esperienze utente necessarie ed estendere le funzionalità del servizio. Per iniziare, abbiamo un set di procedure consigliate e consigli.

- Creare la logica entro i **criteri di estensione** o **relying party criterio**. È possibile aggiungere nuovi elementi, che sostituiranno i criteri di base facendo riferimento allo stesso ID. In questo modo sarà possibile scalare verticalmente il progetto, rendendo più semplice l'aggiornamento dei criteri di base in un secondo momento se Microsoft rilascia nuovi Starter Pack.
- All'interno dei **criteri di base**, è consigliabile evitare di apportare modifiche. Quando necessario, creare commenti in cui vengono apportate le modifiche.
- Quando si esegue l'override di un elemento, ad esempio i metadati del profilo tecnico, evitare di copiare l'intero profilo tecnico dai criteri di base. Copiare invece solo la sezione obbligatoria dell'elemento. Vedere [disabilitare la verifica della posta elettronica](./disable-email-verification.md) per un esempio di come apportare la modifica.
- Per ridurre la duplicazione dei profili tecnici, in cui è condivisa la funzionalità di base, usare l' [inclusione del profilo tecnico](technicalprofiles.md#include-technical-profile).
- Evitare di scrivere nella directory Azure AD durante l'accesso, il che potrebbe causare problemi di limitazione delle richieste.
- Se i criteri hanno dipendenze esterne, ad esempio le API REST, verificano che siano a disponibilità elevata.
- Per migliorare l'esperienza utente, assicurarsi che i modelli HTML personalizzati siano distribuiti a livello globale tramite la [distribuzione di contenuti online](../cdn/index.yml). La rete per la distribuzione di contenuti (CDN) di Azure consente di ridurre i tempi di caricamento, risparmiare larghezza di banda e migliorare la velocità di risposta.
- Se si desidera apportare una modifica al percorso utente, copiare l'intero percorso utente dal criterio di base ai criteri di estensione. Fornire un ID del percorso utente univoco al percorso utente copiato. Quindi, nel [criterio di relying party](relyingparty.md)modificare l'elemento [percorso utente predefinito](relyingparty.md#defaultuserjourney) in modo che punti al nuovo percorso utente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando si sviluppa con criteri di Azure AD B2C, è possibile che si verifichino errori o eccezioni durante l'esecuzione del percorso utente. L'oggetto può essere analizzato utilizzando Application Insights.

- Integrare Application Insights con Azure AD B2C per [diagnosticare le eccezioni](troubleshoot-with-application-insights.md).
- L' [estensione Azure ad B2C per Visual Studio Code consente di](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) accedere ai [log e visualizzarli](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) in base al nome e all'ora dei criteri.
- L'errore più comune nell'impostazione dei criteri è la formattazione XML non corretta. Utilizzare [XML schema convalida](troubleshoot-custom-policies.md) per identificare gli errori prima di caricare il file XML.

## <a name="continuous-integration"></a>Integrazione continua

Usando una pipeline di integrazione e recapito continua configurata in Azure Pipelines, è possibile [includere i Azure ad B2C criteri personalizzati nell'](deploy-custom-policies-devops.md) automazione del controllo del codice e della distribuzione del software. Quando si esegue la distribuzione in ambienti Azure AD B2C diversi, ad esempio sviluppo, test e produzione, è consigliabile rimuovere i processi manuali ed eseguire test automatizzati usando Azure Pipelines.

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Si inizia con Azure AD B2C criteri personalizzati:

1. [Creare un tenant di Azure AD B2C](tutorial-create-tenant.md)
1. [Registrare un'applicazione Web](tutorial-register-applications.md) usando il portale di Azure per poter testare i criteri.
1. Aggiungere le [chiavi dei criteri](custom-policy-get-started.md#add-signing-and-encryption-keys) necessarie e [registrare le applicazioni del Framework dell'esperienza di identità](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Ottenere lo Starter Pack per i criteri di Azure ad B2C](custom-policy-get-started.md#get-the-starter-pack) e caricarlo nel tenant. 
1. Dopo aver caricato il pacchetto Starter, [testare i criteri di iscrizione o di accesso](custom-policy-get-started.md#test-the-custom-policy).
1. Si consiglia di scaricare e installare [Visual Studio Code](https://code.visualstudio.com/) (vs code). Visual Studio Code è un editor di codice sorgente leggero ma potente, che viene eseguito sul desktop ed è disponibile per Windows, macOS e Linux. Con VS Code, è possibile spostarsi rapidamente e modificare i Azure AD B2C file XML dei criteri personalizzati installando l' [estensione Azure ad B2C per vs code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato e testato i criteri di Azure AD B2C, è possibile iniziare a personalizzare i criteri. Vedere gli articoli seguenti per informazioni su come:

- [Aggiungere attestazioni e personalizzare l'input utente](./configure-user-input.md) usando criteri personalizzati. Informazioni su come definire un'attestazione e aggiungere un'attestazione all'interfaccia utente personalizzando alcuni dei profili tecnici degli Starter Pack.
- [Personalizzare l'interfaccia utente](customize-ui-with-html.md) dell'applicazione usando un criterio personalizzato. Informazioni su come creare contenuto HTML personalizzato e personalizzare la definizione del contenuto.
- [Localizzare l'interfaccia utente](./language-customization.md) dell'applicazione usando un criterio personalizzato. Informazioni su come configurare l'elenco delle lingue supportate e fornire etichette specifiche della lingua, aggiungendo l'elemento resources localizzato.
- Durante lo sviluppo e il test dei criteri, è possibile [disabilitare la verifica tramite posta elettronica](./disable-email-verification.md). Informazioni su come sovrascrivere i metadati di un profilo tecnico.
- [Configurare l'accesso con un account Google](./identity-provider-google.md) usando criteri personalizzati. Informazioni su come creare un nuovo provider di attestazioni con il profilo tecnico OAuth2. Personalizzare quindi il percorso utente per includere l'opzione di accesso a Google.
- Per diagnosticare i problemi relativi ai criteri personalizzati, è possibile [raccogliere Azure Active Directory B2C log con Application Insights](troubleshoot-with-application-insights.md). Informazioni su come aggiungere nuovi profili tecnici e configurare i criteri di relying party.
