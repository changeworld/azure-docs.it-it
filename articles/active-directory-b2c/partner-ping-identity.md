---
title: Esercitazione per la configurazione di Azure Active Directory B2C con l'identità ping
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con l'identità ping
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 430629f94695f0689422434c8d80fe4e1876e5dd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900179"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Esercitazione: configurare l'identità ping con Azure Active Directory B2C per l'accesso ibrido sicuro

In questa esercitazione di esempio viene illustrato come estendere Azure Active Directory (AD) B2C con  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) per abilitare l'accesso ibrido sicuro.

Molte proprietà Web esistenti, ad esempio siti di e-commerce e applicazioni Web esposte a Internet, vengono distribuite dietro un sistema proxy, a volte definito sistema di proxy inverso. Questi sistemi proxy forniscono varie funzioni, tra cui la preautenticazione, l'applicazione dei criteri e il routing del traffico. I casi d'uso di esempio includono la protezione dell'applicazione Web dal traffico Web in ingresso e la gestione uniforme delle sessioni tra distribuzioni distribuite di server.

Nella maggior parte dei casi, questa configurazione include un livello di conversione dell'autenticazione che esteriorizza l'autenticazione dall'applicazione Web. I proxy inversi a loro volta forniscono il contesto degli utenti autenticati alle applicazioni Web, in una forma più semplice, ad esempio un valore di intestazione in formato chiaro o digest. In tale configurazione, le applicazioni non usano token standard del settore, ad esempio Security Assertion Markup Language (SAML), OAuth o Open ID Connect (OIDC), bensì dipendono dal proxy per fornire il contesto di autenticazione e gestire la sessione con l'agente dell'utente finale, ad esempio il browser o l'applicazione nativa. Poiché un servizio è in esecuzione in un "Man-in-the-Middle", i proxy possono fornire il controllo della sessione finale. Questo significa anche che il servizio proxy deve essere altamente efficiente e scalabile, non per diventare un collo di bottiglia o un singolo punto di guasto per le applicazioni dietro il servizio proxy. Il diagramma è una rappresentazione di una tipica implementazione del proxy inverso e di un flusso di comunicazioni.

![immagine che mostra l'implementazione del proxy inverso](./media/partner-ping/reverse-proxy.png)

Se ci si trova in una situazione in cui si desidera modernizzare la piattaforma di identità in tali configurazioni, vengono generate le seguenti considerazioni.

- In che modo è possibile separare la modernizzazione delle applicazioni dalla modernizzazione della piattaforma di identità?

- In che modo è possibile stabilire un ambiente di coesistenza con l'autenticazione moderna e legacy, usando il provider di servizi di identità modernizzato?

  a. Come gestire la coerenza dell'esperienza utente finale?

  b. Come fornire un'esperienza Single Sign-on tra le applicazioni coesistenti?

Viene illustrato un approccio per risolvere tali problemi integrando Azure AD B2C con le tecnologie [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) .

## <a name="coexistence-environment"></a>Ambiente di coesistenza

Una soluzione semplice tecnicamente praticabile che è anche conveniente è la configurazione del sistema di proxy inverso per l'uso del sistema di identità modernizzato, delegando l'autenticazione.  
In questo caso, i proxy supporteranno i protocolli di autenticazione moderni e useranno l'autenticazione basata su Reindirizzamento (passiva) che invierà l'utente al nuovo provider di identità (IdP).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C come provider di identità

Azure AD B2C è in grado di definire **criteri** che consentono di gestire esperienze utente e comportamenti diversi, denominati anche **percorsi utente** , come orchestrati dall'estremità del server. Ognuno di questi criteri espone un endpoint di protocollo che può eseguire l'autenticazione come se fosse un IdP. Non è necessaria alcuna gestione speciale sul lato applicazione per criteri specifici. L'applicazione esegue semplicemente una richiesta di autenticazione standard di settore per l'endpoint di autenticazione specifico del protocollo esposto dal criterio di interesse.  
Azure AD B2C possono essere configurati in modo da condividere lo stesso emittente in più criteri o in un'autorità emittente univoca per ogni criterio. Ogni applicazione può puntare a uno o più di questi criteri effettuando una richiesta di autenticazione nativa del protocollo e indirizzare i comportamenti degli utenti desiderati, ad esempio l'accesso, l'iscrizione e le modifiche del profilo. Il diagramma mostra i flussi di lavoro delle applicazioni OIDC e SAML.

![Image Mostra l'implementazione di OIDC e SAML](./media/partner-ping/azure-ad-identity-provider.png)

Sebbene lo scenario indicato funzioni bene per le applicazioni modernizzate, può essere difficile per le applicazioni legacy reindirizzare l'utente in modo appropriato perché la richiesta di accesso alle applicazioni potrebbe non includere il contesto per l'esperienza utente. Nella maggior parte dei casi, il livello proxy o un agente integrato nell'applicazione Web intercetta la richiesta di accesso.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess come proxy inverso

Molti clienti hanno distribuito PingAccess come proxy inverso per riprodurre uno o più ruoli come indicato in precedenza in questo articolo. PingAccess è in grado di intercettare una richiesta diretta in base al tipo man-in-the-Middle o come reindirizzamento proveniente da un agente in esecuzione sul server applicazioni Web.

PingAccess può essere configurato con OIDC, OAuth2 o SAML per eseguire l'autenticazione con un provider di autenticazione upstream. Per questo scopo, è possibile configurare un singolo IdP upstream sul server PingAccess. Il diagramma seguente mostra questa configurazione.

![Image Mostra PingAccess con implementazione di OIDC](./media/partner-ping/authorization-flow.png)

In una tipica distribuzione di Azure AD B2C in cui più criteri espongono più **IDP**, costituisce una sfida. Poiché PingAccess può essere configurato solo con un singolo IdP upstream.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate come proxy di Federazione

PingFederate è un Bridge di identità aziendale che può essere completamente configurato come provider di autenticazione o proxy per altri IDP upstream se necessario. Il diagramma seguente illustra questa funzionalità.

![immagine che mostra l'implementazione di PingFederate](./media/partner-ping/pingfederate.png)

Questa funzionalità può essere usata per passare in modo contestuale/dinamico o dichiarativo una richiesta in ingresso a un criterio di Azure AD B2C specifico. Di seguito è riportata una descrizione del flusso di sequenza del protocollo per questa configurazione.

![immagine che mostra il flusso di lavoro PingAccess e PingFederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha un account, ottenere un [account gratuito](https://azure.microsoft.com/free/).

- Un [tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- PingAccess e PingFederate distribuiti in contenitori Docker o direttamente in macchine virtuali di Azure.

## <a name="connectivity"></a>Connettività

Verificare che sia connesso quanto segue.

- **Server PingAccess** : in grado di comunicare con il server PingFederate, il browser client, OIDC, l'individuazione di chiavi e note OAuth pubblicata dal servizio Azure ad B2C e dal server PingFederate.

- **Server PingFederate** : in grado di comunicare con il server PingAccess, il browser client, OIDC, l'individuazione di chiavi e note OAuth pubblicata dal servizio Azure ad B2C.

- **Applicazione AuthN basata su intestazione o legacy** : in grado di comunicare con il server PingAccess.

- **SAML relying party application** : in grado di raggiungere il traffico del browser dal client. In grado di accedere ai metadati di Federazione SAML pubblicati dal servizio Azure AD B2C.

- **Applicazione moderna** : è in grado di raggiungere il traffico del browser dal client. In grado di accedere a OIDC, OAuth well-known e chiavi Discovery pubblicate dal servizio Azure AD B2C.

- **API REST** : in grado di raggiungere il traffico da un client Web o nativo. In grado di accedere a OIDC, OAuth well-known e chiavi Discovery pubblicate dal servizio Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Configurare Azure AD B2C

A questo scopo, è possibile usare i flussi utente Basic o i criteri Advanced Identity Enterprise Framework (Framework dell'esperienza). PingAccess genera l'endpoint di metadati in base al valore dell' **autorità emittente** usando la convenzione di individuazione basata su [webfinger](https://tools.ietf.org/html/rfc7033) .
Per seguire questa convenzione, aggiornare l'aggiornamento dell'autorità di certificazione Azure AD B2C usando le proprietà dei criteri nei flussi utente.

![immagine che mostra le impostazioni del token](./media/partner-ping/token-setting.png)

Nei criteri avanzati, questo può essere configurato usando l'elemento dei metadati **IssuanceClaimPattern** per **AuthorityWithTfp** valore nel [profilo tecnico dell'emittente del token JWT](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile).

## <a name="configure-pingaccesspingfederate"></a>Configurare PingAccess/PingFederate

Nella sezione seguente viene illustrata la configurazione richiesta.
Il diagramma illustra il flusso globale dell'utente per l'integrazione.

![Image Mostra l'integrazione di PingAccess e PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Configurare PingFederate come provider di token

Per configurare PingFederate come provider di token per PingAccess, verificare che la connettività da PingFederate a PingAccess sia stabilita, seguita dalla connettività da PingAccess a PingFederate.  
Per la procedura di configurazione, vedere [questo articolo](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) .

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Configurare un'applicazione PingAccess per l'autenticazione basata su intestazione

Per l'autenticazione basata su intestazioni è necessario creare un'applicazione PingAccess per l'applicazione Web di destinazione. Attenersi a questa procedura.

#### <a name="step-1--create-a-virtual-host"></a>Passaggio 1: creare un host virtuale

>[!IMPORTANT]
>Per configurare per questa soluzione, è necessario creare un host virtuale per ogni applicazione. Per ulteriori informazioni sulle considerazioni relative alla configurazione e sul relativo effetto, vedere [considerazioni principali](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Per creare un host virtuale, attenersi alla procedura seguente:

1. Passare a **Impostazioni**  >  **accesso** a  >  **host virtuali**

2. Selezionare **Aggiungi host virtuale**

3. Nel campo host immettere la parte FQDN dell'URL dell'applicazione

4. Nel campo porta immettere **443**

5. Selezionare **Salva**

#### <a name="step-2--create-a-web-session"></a>Passaggio 2: creare una sessione Web

Per creare una sessione Web, attenersi alla procedura seguente:

1. Passare a **Impostazioni**  >  **accesso alle**  >  **sessioni Web**

2. Selezionare **Aggiungi sessione Web**

3. Consente di specificare un **nome** per la sessione Web.

4. Selezionare il **tipo di cookie**, **firmato JWT** o **Encrypted JWT**

5. Fornire un valore univoco per i **destinatari**

6. Nel campo **ID client** immettere l'ID dell' **applicazione Azure ad**

7. Nel campo **client Secret (segreto client** ) immettere la **chiave** generata per l'applicazione in Azure ad.

8. Facoltativo: è possibile creare e usare attestazioni personalizzate con l'API Microsoft Graph. Se si sceglie di eseguire questa operazione, selezionare **Avanzate** e deselezionare le opzioni **profilo richiesta** e **Aggiorna attributi utente** . Per altre informazioni sull'uso di attestazioni personalizzate, vedere [usare un'attestazione personalizzata](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#optional---use-a-custom-claim).

9. Selezionare **Salva**

#### <a name="step-3--create-identity-mapping"></a>Passaggio 3: creare il mapping delle identità

>[!NOTE]
>Il mapping di identità può essere usato con più di un'applicazione se più di un'applicazione prevede gli stessi dati nell'intestazione.

Per creare il mapping di identità, attenersi alla procedura seguente:

1. Passare a **Impostazioni**  >  **Accedi** a  >  **mapping identità**

2. Selezionare **Aggiungi mapping di identità**

3. Specificare un **nome**

4. Selezionare il **tipo di mapping delle identità del mapping di identità intestazione**

5. Nella tabella **mapping attributi** specificare i mapping richiesti. Ad esempio,

   Nome attributo | Nome intestazione |
   |-------|--------|
   |upn | x-userPrincipleName |
   |email   |    x-posta elettronica  |
   |oid   | x-OID  |
   |scp   |     ambito x |
   |amr    |    x-AMR    |

6. Selezionare **Salva**

#### <a name="step-4--create-a-site"></a>Passaggio 4: creare un sito

>[!NOTE]
>In alcune configurazioni, è possibile che un sito possa contenere più di un'applicazione. Un sito può essere usato con più di un'applicazione, laddove appropriato.

Per creare un sito, attenersi alla procedura seguente:

1. Vai ai   >  **siti** principali

2. Selezionare **Aggiungi sito**

3. Specificare un **nome** per il sito

4. Immettere la **destinazione** del sito. La destinazione è la coppia nome host: porta per il server che ospita l'applicazione. Non immettere il percorso dell'applicazione in questo campo. Ad esempio, un'applicazione in https://mysite:9999/AppName avrà un valore di destinazione di sito: 9999

5. Indica se la destinazione prevede connessioni protette.

6. Se la destinazione prevede connessioni sicure, impostare il gruppo di certificati attendibili su **Considera attendibile**.

7. Selezionare **Salva**

#### <a name="step-5--create-an-application"></a>Passaggio 5: creare un'applicazione

Seguire questa procedura per creare un'applicazione in PingAccess per ogni applicazione in Azure che si vuole proteggere.

1. Vai alle   >  **applicazioni** principali

2. Selezionare **Aggiungi applicazione**

3. Specificare un **nome** per l'applicazione

4. Facoltativamente, immettere una **Descrizione** per l'applicazione

5. Specificare la **radice del contesto** per l'applicazione. Ad esempio, un'applicazione in https://mysite:9999/AppName avrà una radice del contesto di/AppName. La radice del contesto deve iniziare con una barra (/), non deve terminare con una barra (/) e può essere più grande di un livello, ad esempio/Apps/MyApp.

6. Selezionare l' **host virtuale** creato

   >[!NOTE]
   >La combinazione di host virtuale e radice del contesto deve essere univoca in PingAccess.

7. Selezionare la **sessione Web** creata

8. Selezionare il **sito** creato che contiene l'applicazione

9. Selezionare il **mapping di identità** creato

10. Selezionare **abilitato** per abilitare il sito quando si salva

11. Selezionare **Salva**

### <a name="configure-the-pingfederate-authentication-policy"></a>Configurare i criteri di autenticazione PingFederate

Configurare i criteri di autenticazione PingFederate per la Federazione ai più IDP forniti dai tenant di Azure AD B2C

1. Creare un contratto per colmare gli attributi tra gli IDP e SP. Per altre informazioni, vedere [l'hub federativo e i contratti di criteri di autenticazione](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). È probabile che sia necessario un solo contratto a meno che SP non richieda un set di attributi diverso da ogni IdP.

2. Per ogni IdP, creare una connessione IdP tra IdP e PingFederate, l'hub federativo come SP.

3. Nella finestra **mapping sessione di destinazione** aggiungere i contratti di criteri di autenticazione applicabili alla connessione IDP.

4. Nella finestra **selezionatori** configurare un selettore di autenticazione. Vedere ad esempio un'istanza della **prima scheda identificatore** per eseguire il mapping di ogni IDP alla connessione IDP corrispondente in un criterio di autenticazione.

5. Creare una connessione SP tra PingFederate, l'hub federativo come IdP e SP.

6. Aggiungere il contratto di criteri di autenticazione corrispondente alla connessione SP nella finestra **Mapping origine autenticazione** .

7. Usare ogni IdP per connettersi a PingFederate, l'hub federativo come SP.

8. Usare SP per connettersi a PingFederate, l'hub federativo come IdP.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti.

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
