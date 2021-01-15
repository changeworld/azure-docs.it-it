---
title: Creare un piano di sicurezza per l'accesso esterno a Azure Active Directory
description: Pianificare la sicurezza per l'accesso esterno alle risorse dell'organizzazione.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40738f8fcb14c48ccfe3bc7869e5176c4ab63165
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222331"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. creare un piano di sicurezza per l'accesso esterno 

Ora che è stata [determinata la postura di sicurezza desiderata per l'accesso esterno](1-secure-access-posture.md) ed è [stato individuato lo stato di collaborazione corrente](2-secure-access-current-state.md), è possibile creare un piano di governance e sicurezza utente esterno. 

Questo piano deve documentare quanto segue:

* Le applicazioni e altre risorse che devono essere raggruppate per l'accesso.

* Condizioni di accesso appropriate per gli utenti esterni. Questi possono includere lo stato del dispositivo, la posizione di accesso, i requisiti dell'applicazione client e i rischi dell'utente.

* Criteri di business su quando esaminare e rimuovere l'accesso. 

* Popolamenti di utenti da raggruppare e trattare in modo analogo.

Una volta documentate queste aree, è possibile usare i criteri di gestione delle identità e degli accessi di Microsoft o di qualsiasi altro provider di identità (IdP) per implementare questo piano.

## <a name="document-resources-to-be-grouped-for-access"></a>Risorse documento da raggruppare per l'accesso

Sono disponibili diversi modi per raggruppare le risorse per l'accesso. 

* Microsoft teams raggruppa i file, i thread di conversazione e altre risorse in un'unica posizione. È necessario formulare una strategia di accesso esterno per Microsoft teams. Vedere [accesso sicuro a teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md).

* I pacchetti di accesso alla gestione dei diritti consentono di creare un singolo pacchetto di applicazioni e altre risorse a cui è possibile concedere l'accesso. 

* I criteri di accesso condizionale possono essere applicati a un massimo di 250 applicazioni con gli stessi requisiti di accesso.

Tuttavia, si gestirà l'accesso, sarà necessario documentare le applicazioni da raggruppare. Di seguito sono riportate alcune considerazioni:

* **Profilo di rischio**. Qual è il rischio per l'azienda se un attore malintenzionato ha ottenuto l'accesso a un'applicazione? Si consiglia di codificare ogni applicazione come alto, medio o basso rischio. Prestare attenzione al raggruppamento di applicazioni ad alto rischio con quelle a basso rischio. 

   * Documentare le applicazioni che non devono mai essere condivise con utenti esterni.

* **Framework di conformità**. Cosa accade se è necessario che un'applicazione soddisfi i Framework di conformità? Quali sono i requisiti di accesso e Revisione?

* **Applicazioni per ruoli di lavoro o reparti specifici**. Sono presenti applicazioni che devono essere raggruppate poiché tutti gli utenti in un ruolo o reparto specifico dovranno accedere?

* **Applicazioni** con stato attivo per la collaborazione. Quali applicazioni mirate alla collaborazione dovrebbero essere in grado di accedere agli utenti esterni? Potrebbe essere necessario accedere a Microsoft teams e SharePoint. Per le applicazioni di produttività all'interno di Office 365, come Word ed Excel, gli utenti esterni porteranno le proprie licenze o sarà necessario concederle in licenza e fornire l'accesso?

Per ogni raggruppamento di applicazioni e risorse che si desidera rendere accessibili agli utenti esterni, documentare quanto segue:

* Nome descrittivo per il gruppo, ad esempio *High_Risk_External_Access_Finance*. 

* Elenco completo di tutte le applicazioni e risorse nel gruppo.

* Proprietari di applicazioni e risorse e informazioni di contatto.

* Indica se l'accesso è controllato dall'IT o delegato al titolare dell'azienda.

* Eventuali prerequisiti, ad esempio il completamento di un controllo in background o di un training, per l'accesso.

* Eventuali requisiti di conformità per l'accesso alle risorse.

* Eventuali ulteriori problemi, ad esempio la richiesta di autenticazione a più fattori per risorse specifiche.

* Frequenza con cui l'accesso verrà esaminato, da chi e da dove verrà documentato.

Questo tipo di piano di governance può essere anche completato anche per l'accesso interno.

## <a name="document-sign-in-conditions-for-external-users"></a>Condizioni di accesso dei documenti per gli utenti esterni.

Come parte del piano è necessario determinare i requisiti di accesso per gli utenti esterni durante l'accesso alle risorse. I requisiti di accesso sono spesso basati sul profilo di rischio delle risorse e sulla valutazione dei rischi per l'accesso degli utenti.

Le condizioni di accesso vengono configurate in [Azure ad l'accesso condizionale](../conditional-access/overview.md) e sono costituite da una condizione e un risultato. Ad esempio, quando richiedere l'autenticazione a più fattori

**Condizioni di accesso basate sul rischio di risorse.**

| Profilo di rischio dell'applicazione| Considerare questi criteri per attivare l'autenticazione a più fattori |
| - |-|
| Rischio basso| Richiedi autenticazione a più fattori per set di applicazioni specifici |
| Rischio Med| Richiedi autenticazione a più fattori quando sono presenti altri rischi |
| Rischio elevato| Richiedi sempre l'autenticazione a più fattori per gli utenti esterni |


Attualmente, è possibile [applicare multi-factor authentication per gli utenti B2B nel tenant](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-tutorial-require-mfa). 

**Condizioni di accesso basate su utenti e dispositivi**.

| Rischio di accesso o utente| Considerare questi criteri |
| - | - |
| Dispositivo| Richiede dispositivi conformi |
| App per dispositivi mobili| Richiedi app approvate |
| Identity Protection Mostra un elevato rischio| Richiedi all'utente di modificare la password |
| Percorso di rete| Richiedere l'accesso da un intervallo di indirizzi IP specifico a progetti altamente riservati |

Attualmente, per usare lo stato del dispositivo come input per un criterio, il dispositivo deve essere registrato o aggiunto al tenant. 

È possibile usare i [criteri basati sul rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk) . Tuttavia, è necessario mitigare i problemi nel tenant principale dell'utente.

Per i [percorsi di rete](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location), è possibile limitare l'accesso a qualsiasi intervallo di indirizzi IP di cui si è proprietari. Questa operazione può essere utilizzata se si desidera che i partner esterni accedano a un'applicazione mentre si trovano nel sito dell'organizzazione.

[Altre informazioni sui criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

## <a name="document-access-review-policies"></a>Criteri di verifica dell'accesso ai documenti

Documentare i criteri di business quando è necessario verificare l'accesso alle risorse e quando è necessario rimuovere l'accesso all'account per gli utenti esterni. Gli input per queste decisioni possono includere:

* Requisiti descritti in dettaglio in tutti i Framework di conformità.

* Processi e criteri aziendali interni

* Comportamento utente

Sebbene i criteri vengano personalizzati in base alle proprie esigenze, tenere presente quanto segue:

* Verifiche di accesso per la **gestione dei diritti**. Usare la funzionalità di gestione dei diritti per

   * [Scade automaticamente i pacchetti di accesso](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-lifecycle-policy)e, di conseguenza, l'accesso degli utenti esterni alle risorse incluse.

   * Impostare una [frequenza di revisione obbligatoria](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-reviews-create) per le verifiche di accesso.

   * Se si usano [organizzazioni connesse](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-organization) per raggruppare tutti gli utenti da un singolo partner, pianificare le verifiche regolari con il titolare dell'azienda e il rappresentante del partner.

* **Gruppi di Microsoft 365**. Impostare [criteri di scadenza](https://docs.microsoft.com/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) dei gruppi per Microsoft 365 gruppi a cui gli utenti esterni vengono invitati. 

* **Altre opzioni**. Se gli utenti esterni possono accedere al di fuori dei pacchetti di accesso alla gestione dei diritti o dei gruppi di Microsoft 365, configurare il processo di business da verificare quando gli account devono essere resi inattivi o eliminati. Ad esempio:

   * Rimuovere la funzionalità di accesso per qualsiasi account non connesso a per 90 giorni.

   * Valutare le esigenze di accesso e intervenire alla fine di ogni progetto con utenti esterni.

 

## <a name="determine-your-access-control-methods"></a>Determinare i metodi di controllo di accesso

Ora che si conoscono gli elementi a cui si vuole controllare l'accesso, il modo in cui tali asset devono essere raggruppati per l'accesso comune e i criteri di verifica dell'accesso e di accesso necessari, è possibile decidere come eseguire il piano. 

Alcune funzionalità, ad esempio la [gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview), sono disponibili solo con una licenza di Azure ad Premium 2 (P2). Le licenze Microsoft 365 E5 e Office 365 E5 includono Azure AD P2. 

Altre combinazioni di Microsoft 365, Office 365 e Azure AD abilitano anche alcune funzionalità per la gestione degli utenti esterni. Per ulteriori informazioni, vedere [Information Protection](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) .

> [!NOTE]
> Le licenze sono per utente. È pertanto possibile avere utenti specifici, tra cui amministratori e proprietari aziendali, al livello Azure AD P2 o Microsoft 365 E5 senza abilitare tali licenze per tutti gli utenti. I primi 50.000 utenti esterni sono gratuiti. Se non si abilitano le licenze P2 per gli altri utenti interni, non saranno in grado di utilizzare la funzionalità di gestione dei diritti, come i pacchetti di accesso. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Governare l'accesso con Azure AD P2 e Microsoft/Office 365 E5
Azure AD P2 e Microsoft 365 E5 hanno la suite completa di strumenti di sicurezza e governance.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Provisioning, accesso, revisione dell'accesso e deprovisioning. Le voci in grassetto sono metodi preferiti

| Funzionalità| Provisioning di utenti esterni| Applicare Rich di accesso.| Verificare l'accesso| Deprovisioning dell'accesso |
| - | - | - | - | - |
| Collaborazione B2B Azure AD| Invito tramite posta elettronica, OTP, self-service| | **Revisione periodica per partner**| Rimozione di un account<br>Limita accesso |
| Gestione dei diritti| **Aggiungi utente tramite assegnazione o accesso self-service**| | Verifiche di accesso|**Scadenza o rimozione dal pacchetto di accesso**|
| Gruppi di Office 365| | | Verifica appartenenza a gruppi| Scadenza o eliminazione del gruppo<br> Gruppo modulo di rimozione |
| Gruppi di sicurezza di Azure AD| | **Criteri di accesso condizionale** (aggiungere utenti esterni ai gruppi di sicurezza in caso di necessità)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Accesso alle risorse. Le voci in grassetto sono metodi preferiti

|Funzionalità | Accesso alle risorse & APP| Accesso OneDrive di SharePoint &| Accesso ai team| Sicurezza del documento & posta elettronica |
| - |-|-|-|-|
| Gestione dei diritti| **Aggiungi utente tramite assegnazione o accesso self-service**| **Pacchetti di accesso**| **Pacchetti di accesso**|  |
| Gruppo Office 365| | Accesso ai siti (e al contenuto associato) inclusi nel gruppo| Accesso ai team (e al contenuto associato) inclusi nel gruppo|  |
| Etichette di riservatezza| | **Classificare e limitare manualmente e automaticamente l'accesso**| **Classificare e limitare manualmente e automaticamente l'accesso**| **Classificare e limitare manualmente e automaticamente l'accesso** |
| Gruppi di sicurezza di Azure AD| **Criteri di accesso condizionale per l'accesso non incluso nei pacchetti di accesso**| | |  |


### <a name="entitlement-management"></a>Gestione dei diritti 

I [pacchetti di accesso alla gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-create) consentono il provisioning e il deprovisioning dell'accesso a gruppi, team, applicazioni e siti di SharePoint. È possibile definire le organizzazioni connesse a cui è consentito l'accesso, se le richieste self-service sono consentite e quali flussi di lavoro di approvazione sono necessari (se disponibili) per concedere l'accesso. Per assicurarsi che l'accesso non sia più lungo del necessario, è possibile definire i criteri di scadenza e le verifiche di accesso per ogni pacchetto di accesso. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Governare l'accesso con Azure AD P1 e Microsoft/Office 365 E3
È possibile ottenere una governance affidabile con Azure AD P1 e Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Provisioning, accesso, revisione dell'accesso e deprovisioning


|Funzionalità | Provisioning di utenti esterni| Applicare i requisiti di accesso| Verificare l'accesso| Deprovisioning dell'accesso |
| - |-|-|-|-|
| Collaborazione B2B Azure AD| **Invito tramite posta elettronica, OTP, self-service**| Federazione B2B diretta| **Revisione periodica per partner**| Rimozione di un account<br>Limita accesso |
| Gruppi Microsoft o Office 365| | | | Scadenza o eliminazione del gruppo.<br>Rimozione dal gruppo. |
| Gruppi di sicurezza| | **Aggiungere utenti esterni ai gruppi di sicurezza (organizzazione, team, progetto e così via)**| |  |
| Criteri di accesso condizionale| | **Criteri di accesso condizionale per gli utenti esterni**| |  |


 ### <a name="access-to-resources"></a>Accesso alle risorse.

|Funzionalità | Accesso alle risorse & APP| Accesso OneDrive di SharePoint &| Accesso ai team| Sicurezza del documento & posta elettronica |
| - |-|-|-|-|
| Gruppi Microsoft o Office 365| | **Accesso ai siti inclusi nel gruppo (e contenuto associato)**|**Accesso ai team inclusi nel gruppo Microsoft 365 (e contenuto associato)**|  |
| Etichette di riservatezza| | Classificare e limitare manualmente l'accesso| Classificare e limitare manualmente l'accesso.| Classificazione manuale per limitare e crittografare |
| Criteri di accesso condizionale| Criteri di accesso condizionale per il controllo di accesso| | |  |
| Metodi aggiuntivi| | Limitare l'accesso al sito di SharePoint in dettaglio con i gruppi di sicurezza.<br>Non consentire la condivisione diretta.| **Limitare gli inviti esterni ai team**|  |


### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creare un piano di governance](3-secure-access-plan.md) .

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)