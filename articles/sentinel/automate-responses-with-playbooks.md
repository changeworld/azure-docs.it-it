---
title: Automatizzare la risposta alle minacce con PlayBook in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra l'automazione in Sentinel di Azure e illustra come usare i PlayBook per automatizzare la prevenzione delle minacce e la risposta.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609809"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatizzare la risposta alle minacce con PlayBook in Sentinel di Azure

Questo articolo illustra i PlayBook di Azure Sentinel e il modo in cui usarli per implementare le operazioni di orchestrazione e risposta (SOAR) di sicurezza, ottenendo risultati migliori risparmiando tempo e risorse.

## <a name="what-is-a-playbook"></a>Che cos'è un PlayBook?

I team SIEM/SOC sono in genere inondati da avvisi e incidenti di sicurezza a intervalli regolari, a volumi così grandi da sovraccaricare il personale disponibile. Si tratta di un risultato troppo spesso nelle situazioni in cui molti avvisi vengono ignorati e molti eventi imprevisti non vengono analizzati, lasciando l'organizzazione vulnerabile ad attacchi che non sono stati osservati.

Molti, se non la maggior parte, di questi avvisi ed eventi imprevisti sono conformi a modelli ricorrenti che possono essere risolti da set specifici e definiti di azioni correttive.

Un PlayBook è una raccolta di queste azioni correttive che possono essere eseguite da Azure Sentinel come routine. Un PlayBook può aiutare ad automatizzare e orchestrare la risposta alle minacce; può essere eseguita manualmente o impostata per l'esecuzione automatica in risposta a avvisi o eventi imprevisti specifici, quando vengono attivati rispettivamente da una regola di analisi o da una regola di automazione.

I PlayBook vengono creati e applicati a livello di sottoscrizione, ma la scheda **PlayBook** (nel pannello nuovo **automazione** ) Visualizza tutti i PlayBook disponibili in tutte le sottoscrizioni selezionate.

### <a name="azure-logic-apps-basic-concepts"></a>Concetti di base di app per la logica di Azure

I PlayBook in Sentinel di Azure si basano sui flussi di lavoro compilati in app per la [logica di Azure](../logic-apps/logic-apps-overview.md), un servizio cloud che consente di pianificare, automatizzare e orchestrare attività e flussi di lavoro in tutti i sistemi aziendali. Ciò significa che i PlayBook possono sfruttare tutte le potenzialità e la personalizzazione dei modelli predefiniti delle app per la logica.

> [!NOTE]
> Poiché le app per la logica di Azure sono una risorsa separata, potrebbero essere applicati addebiti aggiuntivi. Per altri dettagli, visitare la pagina dei prezzi di app per la [logica di Azure](https://azure.microsoft.com/pricing/details/logic-apps/) .

App per la logica di Azure comunica con altri sistemi e servizi usando i connettori. Di seguito è riportata una breve spiegazione dei connettori e alcuni degli attributi importanti:

- **Connettore gestito:** Set di azioni e trigger che esegue il wrapping delle chiamate API a un particolare prodotto o servizio. App per la logica di Azure offre centinaia di connettori per comunicare con i servizi Microsoft e non Microsoft.
  - [Elenco di tutti i connettori delle app per la logica e della relativa documentazione](/connectors/connector-reference/)

- **Connettore personalizzato:** Potrebbe essere necessario comunicare con i servizi che non sono disponibili come connettori predefiniti. I connettori personalizzati risolvono questa necessità consentendo di creare (e persino condividere) un connettore e definire trigger e azioni personalizzati.
  - [Creare connettori personalizzati per le app per la logica](/connectors/custom-connectors/create-logic-apps-connector)

- **Connettore Sentinel di Azure:** Per creare PlayBook che interagiscono con Azure Sentinel, usare il connettore Azure Sentinel.
  - [Documentazione del connettore Sentinel di Azure](/connectors/azuresentinel/)

- **Trigger:** Componente connettore che avvia uno PlayBook. Definisce lo schema che il PlayBook prevede di ricevere quando viene attivato. Il connettore Azure Sentinel dispone attualmente di due trigger:
  - [Trigger di avviso](/connectors/azuresentinel/#triggers): il PlayBook riceve l'avviso come input.
  - [Trigger evento](/connectors/azuresentinel/#triggers)imprevisto: il PlayBook riceve l'evento imprevisto come input, insieme a tutti gli avvisi e le entità inclusi.

    > [!IMPORTANT]
    >
    > - La funzionalità di **attivazione degli eventi imprevisti** per i PlayBook è attualmente disponibile in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

- **Azioni:** Le azioni sono tutti i passaggi che si verificano dopo il trigger. Possono essere disposti in sequenza, in parallelo o in una matrice di condizioni complesse.

- **Campi dinamici:** Campi temporanei, determinati dallo schema di output dei trigger e delle azioni e popolati dall'output effettivo, che possono essere usati nelle azioni successive.

### <a name="permissions-required"></a>Autorizzazioni necessarie

 Per consentire al team di secops di usare le app per la logica per le operazioni di orchestrazione, automazione e risposta di sicurezza (SOAR), ovvero per creare ed eseguire PlayBook, in Azure Sentinel è possibile assegnare i ruoli di Azure, a membri specifici del team delle operazioni di sicurezza o all'intero team. Di seguito vengono descritti i diversi ruoli disponibili e le attività per le quali devono essere assegnati:

#### <a name="azure-roles-for-logic-apps"></a>Ruoli di Azure per app per la logica

- Il **collaboratore app** per la logica consente di gestire app per la logica ed eseguire PlayBook, ma non è possibile modificarne l'accesso (per cui è necessario il ruolo **proprietario** ).
- L' **operatore app** per la logica consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

#### <a name="azure-roles-for-sentinel"></a>Ruoli di Azure per Sentinel

- Il ruolo **collaboratore sentinella di Azure** consente di aggiungere un PlayBook a una regola di analisi.
- Il ruolo **risponditore sentinella di Azure** consente di eseguire manualmente un PlayBook.
- Il **collaboratore di automazione di Sentinel di Azure** consente alle regole di automazione di eseguire PlayBook. Non viene usato per altri scopi.

#### <a name="learn-more"></a>Altre informazioni

- [Altre informazioni sui ruoli di Azure in app per la logica di Azure](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Altre informazioni sui ruoli di Azure in Sentinel di Azure](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Passaggi per la creazione di un PlayBook

- [Definire lo scenario di automazione](#use-cases-for-playbooks).

- [Compilare l'app per la logica di Azure](tutorial-respond-threats-playbook.md).

- [Testare l'app per la logica](#run-a-playbook-manually-on-an-alert).

- Alleghi il PlayBook a una regola di [automazione](#incident-creation-automated-response) o a una [regola di analisi](#alert-creation-automated-response)oppure [Esegui manualmente, quando necessario](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Casi d'uso per i PlayBook

La piattaforma app per la logica di Azure offre centinaia di azioni e trigger, quindi è possibile creare quasi tutti gli scenari di automazione. Azure Sentinel consiglia di iniziare con gli scenari SOC seguenti:

#### <a name="enrichment"></a>Arricchimento

**Raccogliere i dati e collegarli all'evento imprevisto per prendere decisioni più intelligenti.**

Ad esempio:

Un evento imprevisto di Azure Sentinel è stato creato da un avviso da una regola di analisi che genera entità di indirizzi IP.

L'evento imprevisto attiva una regola di automazione che esegue un PlayBook con i passaggi seguenti:

- Inizia quando [viene creato un nuovo evento imprevisto di Azure Sentinel](/connectors/azuresentinel/#triggers). Le entità rappresentate nell'evento imprevisto sono archiviate nei campi dinamici del trigger di evento imprevisto.

- Per ogni indirizzo IP, eseguire una query su un provider di intelligence per le minacce esterno, ad esempio il [totale del virus](https://www.virustotal.com/), per recuperare più dati.

- Aggiungere i dati restituiti e le informazioni dettagliate come commenti dell'evento imprevisto.

#### <a name="bi-directional-sync"></a>Sincronizzazione bidirezionale

**È possibile usare i PlayBook per sincronizzare gli eventi imprevisti di Sentinel di Azure con altri sistemi di ticket.**

Ad esempio:

Creare una regola di automazione per la creazione degli eventi imprevisti e aggiungere un PlayBook che apre un ticket in ServiceNow:

- Inizia quando [viene creato un nuovo evento imprevisto di Azure Sentinel](/connectors/azuresentinel/#triggers).

- Creare un nuovo ticket in [ServiceNow](/connectors/service-now/).

- Includere nel Ticket il nome dell'evento imprevisto, i campi importanti e un URL per l'evento imprevisto di Sentinel di Azure per semplificare la trasformazione.

#### <a name="orchestration"></a>Orchestrazione

**Utilizzare la piattaforma SOC chat per controllare meglio la coda degli eventi imprevisti.**

Ad esempio:

Un evento imprevisto di Azure Sentinel è stato creato da un avviso da una regola di analisi che genera il nome utente e le entità di indirizzi IP.

L'evento imprevisto attiva una regola di automazione che esegue un PlayBook con i passaggi seguenti:

- Inizia quando [viene creato un nuovo evento imprevisto di Azure Sentinel](/connectors/azuresentinel/#triggers).

- Inviare un messaggio al canale delle operazioni di sicurezza in [Microsoft teams](/connectors/teams/) o [Slack](/connectors/slack/) per assicurarsi che gli analisti della sicurezza siano consapevoli dell'evento imprevisto.

- Inviare tutte le informazioni presenti nell'avviso tramite posta elettronica all'amministratore di rete e amministratore della sicurezza senior. Il messaggio di posta elettronica includerà i pulsanti di opzione **blocca** e **Ignora** utente.

- Attendere la ricezione di una risposta da parte degli amministratori, quindi continuare l'esecuzione.

- Se gli amministratori hanno scelto il **blocco**, inviare un comando al firewall per bloccare l'indirizzo IP nell'avviso e un altro per Azure ad per disabilitare l'utente.

#### <a name="response"></a>Risposta

**Rispondere immediatamente alle minacce, con dipendenze umane minime.**

Due esempi:

**Esempio 1:** Rispondere a una regola di analisi che indica un utente compromesso, come rilevato da [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Inizia quando [viene creato un nuovo evento imprevisto di Azure Sentinel](/connectors/azuresentinel/#triggers).

   - Per ogni entità utente nell'evento imprevisto sospettato come compromesso:

     - Inviare un messaggio di team all'utente, richiedendo la conferma che l'utente ha intrapreso l'azione sospetta.

     - Verificare con Azure AD Identity Protection per [verificare che lo stato dell'utente sia compromesso](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection etichetta l'utente come **rischioso** e applica i criteri di imposizione già configurati, ad esempio per richiedere all'utente di usare l'autenticazione a più fattori al successivo accesso.

        > [!NOTE]
        > Il PlayBook non avvia alcuna azione di imposizione per l'utente, né avvia alcuna configurazione dei criteri di applicazione. Indica solo Azure AD Identity Protection di applicare tutti i criteri già definiti nel modo appropriato. Qualsiasi imposizione dipende interamente dai criteri appropriati definiti in Azure AD Identity Protection.

**Esempio 2:** Rispondere a una regola di analisi che indica una macchina compromessa, come rilevato da [Microsoft Defender per l'endpoint](/windows/security/threat-protection/):

   - Inizia quando [viene creato un nuovo evento imprevisto di Azure Sentinel](/connectors/azuresentinel/#triggers).

   - Usare l'azione **entità-Ottieni host** in Sentinel di Azure per analizzare i computer sospetti inclusi nelle entità dell'evento imprevisto.

   - Inviare un comando a Microsoft Defender per l'endpoint per [isolare i computer](/connectors/wdatp/#actions---isolate-machine) nell'avviso.

## <a name="how-to-run-a-playbook"></a>Come eseguire un PlayBook

I PlayBook possono essere eseguiti **manualmente** o **automaticamente**.

Se vengono eseguiti manualmente, quando si riceve un avviso è possibile scegliere di eseguire un playbook su richiesta come risposta all'avviso selezionato. Attualmente questa funzionalità è supportata solo per gli avvisi, non per gli eventi imprevisti.

Eseguendoli automaticamente si intende impostarli come risposta automatica in una regola di analisi (per gli avvisi) o come azione in una regola di automazione (per gli eventi imprevisti). [Altre informazioni sulle regole di automazione](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Impostare una risposta automatica

I team delle operazioni di sicurezza possono ridurre significativamente il carico di lavoro automatizzando le risposte di routine ai tipi ricorrenti di eventi imprevisti e avvisi, consentendo di concentrarsi maggiormente su eventi imprevisti e avvisi univoci, analizzando i modelli, la ricerca di minacce e altro ancora.

L'impostazione della risposta automatica significa che ogni volta che viene attivata una regola di analisi, oltre a creare un avviso, la regola eseguirà un PlayBook, che riceverà come input l'avviso creato dalla regola.

Se l'avviso crea un evento imprevisto, l'evento imprevisto attiverà una regola di automazione che può a sua volta eseguire un PlayBook, che riceverà come input l'evento imprevisto creato dall'avviso.

#### <a name="alert-creation-automated-response"></a>Risposta automatica per la creazione di avvisi

Per i PlayBook attivati dalla creazione di avvisi e la ricezione di avvisi come input (il primo passaggio è "quando viene attivato un avviso di Azure Sentinel"), alleghi il PlayBook a una regola di analisi:

1. Modificare la [regola di analisi](tutorial-detect-threats-custom.md) che genera l'avviso per cui si desidera definire una risposta automatica.

1. In **automazione avvisi** nella scheda **risposta automatica** Selezionare il PlayBook o i PlayBook che questa regola di analisi attiverà quando viene creato un avviso.

#### <a name="incident-creation-automated-response"></a>Risposta automatica creazione evento imprevisto

Per i PlayBook attivati dalla creazione degli eventi imprevisti e la ricezione di eventi imprevisti come input (il primo passaggio è "quando viene attivato un evento imprevisto di Sentinel di Azure"), creare una regola di automazione e definire un'azione **Esegui PlayBook** al suo interno. Questa operazione può essere eseguita in due modi:

- Modificare la regola di analisi che genera l'evento imprevisto per il quale si vuole definire una risposta automatica. In **automazione eventi imprevisti** nella scheda **risposta automatica** creare una regola di automazione. Verrà creata una risposta automatica solo per questa regola di analisi.

- Nella scheda **regole di automazione** del pannello **automazione** creare una nuova regola di automazione e specificare le condizioni appropriate e le azioni desiderate. Questa regola di automazione verrà applicata a qualsiasi regola di analisi che soddisfi le condizioni specificate.

    > [!NOTE]
    > Per eseguire un PlayBook da una regola di automazione, Azure Sentinel usa un account del servizio appositamente autorizzato a tale scopo. L'uso di questo account (in contrapposizione all'account utente) aumenta il livello di sicurezza del servizio e consente all'API delle regole di automazione di supportare i casi d'uso CI/CD.
    >
    > È necessario concedere a questo account le autorizzazioni esplicite per il gruppo di risorse in cui risiede il PlayBook. A questo punto, tutte le regole di automazione saranno in grado di eseguire qualsiasi PlayBook in tale gruppo di risorse.
    >
    > Quando si aggiunge l'azione **Esegui PlayBook** a una regola di automazione, verrà visualizzato un elenco a discesa di PlayBook. I PlayBook a cui Azure Sentinel non ha le autorizzazioni vengono visualizzati come non disponibili ("disabilitato"). È possibile concedere l'autorizzazione ad Azure Sentinel in loco selezionando il collegamento **Gestisci autorizzazioni PlayBook** .
    >
    > In uno scenario multi-tenant ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)), è necessario definire le autorizzazioni per il tenant in cui si trova il PlayBook, anche se la regola di automazione che chiama il PlayBook si trova in un tenant diverso. A tale scopo, è necessario disporre delle autorizzazioni di **proprietario** per il gruppo di risorse del PlayBook.

Vedere le [istruzioni complete per la creazione di regole di automazione](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Eseguire manualmente un PlayBook su un avviso

L'attivazione manuale è disponibile dal portale di Azure Sentinel nei pannelli seguenti:

- Nella vista **eventi imprevisti** scegliere un evento imprevisto specifico, aprire la scheda **avvisi** e scegliere un avviso.

- In **analisi** scegliere un avviso specifico.

1. Fare clic su **Visualizza PlayBook** per l'avviso selezionato. Si otterrà un elenco di tutti i PlayBook che iniziano con un **quando viene attivato un avviso di Azure Sentinel** e a cui si ha accesso.

1. Fare clic su **Esegui** sulla riga di un PlayBook specifico per attivarlo.

1. Selezionare la scheda **esecuzioni** per visualizzare un elenco di tutte le volte in cui è stato eseguito un PlayBook nell'avviso. Potrebbero essere necessari alcuni secondi per l'esecuzione di tutte le esecuzioni appena completate in questo elenco.

1. Se si fa clic su un'esecuzione specifica, il log di esecuzione completa viene aperto in app per la logica.

### <a name="run-a-playbook-manually-on-an-incident"></a>Eseguire manualmente un PlayBook su un evento imprevisto

Non ancora supportata. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Gestisci i PlayBook

Nella scheda **Playbooks (PlayBook** ) viene visualizzato un elenco di tutti i PlayBook a cui si ha accesso, filtrato in base alle sottoscrizioni attualmente visualizzate in Azure. Il filtro sottoscrizioni è disponibile dal menu **directory + sottoscrizione** nell'intestazione di pagina globale.

Facendo clic sul nome di un PlayBook viene indirizzata la pagina principale del PlayBook in app per la logica. La colonna **stato** indica se è abilitata o disabilitata.

Il **tipo di trigger** rappresenta il trigger di app per la logica che avvia questo PlayBook.

| Tipo di trigger | Indica i tipi di componente in PlayBook |
|-|-|
| **Evento/avviso sentinella di Azure** | Il PlayBook viene avviato con uno dei trigger Sentinel (Alert, Incident) |
| **Uso dell'azione sentinella di Azure** | Il PlayBook viene avviato con un trigger non Sentinel ma usa un'azione sentinella di Azure |
| **Altri** | Il PlayBook non include alcun componente Sentinel |
| **Non inizializzato** | Il PlayBook è stato creato, ma non contiene componenti (trigger o azioni). |
|

Nella pagina dell'app per la logica del PlayBook è possibile visualizzare altre informazioni sul PlayBook, incluso un log di tutte le volte in cui è stato eseguito e il risultato (esito positivo o negativo e altri dettagli). È anche possibile immettere la finestra di progettazione delle app per la logica e modificare direttamente il PlayBook, se si dispone delle autorizzazioni appropriate.

### <a name="api-connections"></a>Connessioni API

Le connessioni API vengono usate per connettere app per la logica ad altri servizi. Ogni volta che viene eseguita una nuova autenticazione per un connettore di app per la logica, viene creata una nuova risorsa di tipo **connessione API** che contiene le informazioni fornite durante la configurazione dell'accesso al servizio.

Per visualizzare tutte le connessioni API, immettere le *connessioni API* nella casella di ricerca intestazione della portale di Azure. Si notino le colonne di interesse:

- Nome visualizzato: nome "descrittivo" che viene fornito alla connessione ogni volta che ne viene creato uno.
- Stato: indica lo stato della connessione. errore, connesso.
- Gruppo di risorse: le connessioni API vengono create nel gruppo di risorse della risorsa PlayBook (app per la logica).

Un altro modo per visualizzare le connessioni API consiste nel passare al pannello **tutte le risorse** e filtrarlo per tipo di *connessione API*. In questo modo è possibile selezionare, contrassegnare ed eliminare contemporaneamente più connessioni.

Per modificare l'autorizzazione di una connessione esistente, immettere la risorsa di connessione e selezionare **Modifica connessione API**.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: usare PlayBook per automatizzare le risposte alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md)
