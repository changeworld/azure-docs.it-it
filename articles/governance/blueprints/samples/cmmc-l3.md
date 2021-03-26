---
title: Esempio di progetto CMMC Level 3
description: Panoramica dell'esempio di progetto CMMC Level 3. Questo esempio di progetto consente ai clienti di valutare specifici controlli.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572658"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Esempio di progetto CMMC Level 3

L'esempio CMMC Level 3 Blueprint fornisce Guard di governance-Rails usando [criteri di Azure](../../policy/overview.md) che consentono di valutare specifici controlli del [Framework CMMC (Cybersecurity Maturity Model Certification)](https://www.acq.osd.mil/cmmc/index.html) . Questo progetto consente ai clienti di distribuire un set di criteri di base per qualsiasi architettura distribuita in Azure che deve implementare i controlli per il livello CMMC 3.

## <a name="control-mapping"></a>Mapping dei controlli

Il [mapping del controllo criteri di Azure](../../policy/samples/cmmc-l3.md) fornisce informazioni dettagliate sulle definizioni dei criteri incluse in questo progetto e sul mapping tra queste definizioni dei criteri e i **controlli** in CMMC Framework. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire l'esempio Azure Blueprints CMMC Level 3, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **CMMC Level 3** in _altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto CMMC Level 3.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti inclusi nell'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dall'allineamento con i controlli CMMC di livello 3.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio di progetto CMMC Level 3". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

### <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

### <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Livello CMMC 3|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione di criteri di configurazione Guest|Selezionando "true", l'utente accetta di essere addebitato mensilmente per ogni computer connesso ad arco; Per ulteriori informazioni, visitare il https://aka.ms/policy-pricing|
|Livello CMMC 3|Assegnazione dei criteri|Elenco di utenti che devono essere esclusi dal gruppo amministratori VM Windows|Elenco delimitato da punto e virgola con gli utenti da escludere dal gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|Livello CMMC 3|Assegnazione dei criteri|Elenco di utenti che devono essere inclusi nel gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con gli utenti da includere nel gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|Livello CMMC 3|Assegnazione dei criteri|ID dell'area di lavoro Log Analytics per la segnalazione degli agenti di macchine virtuali|ID (GUID) dell'area di lavoro Log Analytics a cui gli agenti delle macchine virtuali devono inviare le segnalazioni|
|Livello CMMC 3|Assegnazione dei criteri|Nomi di curva ellittica consentiti|Elenco di nomi di curva consentiti per i certificati di crittografia a curva ellittica.|
|Livello CMMC 3|Assegnazione dei criteri|Tipi di chiave consentiti|Elenco dei tipi di chiave consentiti|
|Livello CMMC 3|Assegnazione dei criteri|Consenti l'utilizzo della rete host per i pod del cluster Kubernetes|Impostare questo valore su true se pod è autorizzato a utilizzare la rete host in caso contrario false.|
|Livello CMMC 3|Assegnazione dei criteri|Controlla Modifica criteri di autenticazione|Specifica se gli eventi di controllo vengono generati quando vengono apportate modifiche ai criteri di autenticazione. Questa impostazione è utile per tenere traccia delle modifiche del trust a livello di dominio e di foresta e dei privilegi concessi agli account utente o ai gruppi.|
|Livello CMMC 3|Assegnazione dei criteri|Controlla Modifica criteri di autorizzazione|Specifica se gli eventi di controllo vengono generati per l'assegnazione e la rimozione dei diritti utente nei criteri utente corretti, le modifiche all'autorizzazione per gli oggetti token di sicurezza, le modifiche agli attributi delle risorse e le modifiche ai criteri di accesso centrale per file system oggetti.|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: il backup di Azure deve essere abilitato per le macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di servizi cognitivi devono limitare l'accesso alla rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le istanze gestite di SQL devono usare chiavi gestite dal cliente per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'API di Azure per FHIR deve usare una chiave gestita dal cliente (CMK) per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve essere abilitato per il servizio front door di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le impostazioni di valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per gli account di servizi cognitivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le raccomandazioni per la protezione avanzata della rete adattiva devono essere applicate alle macchine virtuali con connessione Internet|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Alla sottoscrizione deve essere assegnato più di un proprietario|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: La crittografia del disco deve essere applicata nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile abilitare le notifiche di posta elettronica al proprietario della sottoscrizione per gli avvisi con gravità alta|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'insieme di credenziali delle chiavi deve essere abilitato per l'eliminazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: SQL Server deve usare chiavi gestite dal cliente per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per Key Vault|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: il backup con ridondanza geografica deve essere abilitato per il database di Azure per MariaDB|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: CORS non deve consentire a ogni dominio di accedere all'API per FHIR|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per ' opzioni di sicurezza-sicurezza di rete '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario aggiornare le regole consentite nei criteri di controllo delle applicazioni adattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve usare la modalità specificata per il gateway applicazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le chiavi devono avere date di scadenza impostate|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Transparent Data Encryption deve essere abilitata nei database SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: il profilo di log di monitoraggio di Azure deve raccogliere i log per le categorie ' Write,' Delete,' è Action '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: la valutazione della vulnerabilità deve essere abilitata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione di PHP" sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'insieme di credenziali delle chiavi deve essere abilitato per l'eliminazione temporanea|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare solo connessioni sicure alla cache di Azure per Redis|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: la crittografia dell'infrastruttura deve essere abilitata per i server di database di Azure per PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per il servizio app|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per i criteri di controllo del sistema-modifica dei criteri|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di servizi cognitivi devono abilitare la crittografia dei dati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso SSH da Internet deve essere bloccato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i dischi non collegati devono essere crittografati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per l'archiviazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono limitare l'accesso alla rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: CORS non deve consentire a tutte le risorse di accedere all'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: distribuire Advanced Threat Protection negli account di archiviazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le variabili dell'account di automazione devono essere crittografate|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nell'hub degli eventi devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: la crittografia dell'infrastruttura deve essere abilitata per i server di database di Azure per MySQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni di sicurezza specifiche (Microsoft. Security/securitySolutions/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per ' opzioni di sicurezza-accesso alla rete '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: monitoraggio di Azure deve raccogliere i log attività da tutte le aree|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve usare la modalità specificata per il servizio front door di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono avere la crittografia dell'infrastruttura|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i controlli applicazione adattivi per la definizione di applicazioni sicure devono essere abilitati nei computer|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per "opzioni di sicurezza-controllo account utente"|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Per la sottoscrizione devono essere designati al massimo 3 proprietari|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le sottoscrizioni devono avere un indirizzo di posta elettronica di contatto per i problemi di sicurezza|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso pubblico dell'account di archiviazione non deve essere consentito|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per Kubernetes|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: il firewall deve essere abilitato in Key Vault|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Windows che consentono il riutilizzo delle 24 password precedenti|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i registri contenitori devono essere crittografati con una chiave gestita dal cliente (CMK)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per i server flessibili PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario correggere le vulnerabilità in Azure Container Registry immagini|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Service Fabric cluster deve avere la proprietà ClusterProtectionLevel impostata su EncryptAndSign|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per SQL Server nei computer|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di servizi cognitivi devono abilitare la crittografia dei dati con la chiave gestita dal cliente|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'app per le funzioni deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la notifica tramite posta elettronica per gli avvisi con gravità elevata|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'account di archiviazione deve usare la chiave gestita dal cliente per la crittografia|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione Python" sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le chiavi devono essere il tipo crittografico specificato RSA o EC|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le sottoscrizioni di Azure devono avere un profilo di log per il log attività|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i sistemi operativi e i dischi dati nei cluster di servizi di Azure Kubernetes devono essere crittografati dalle chiavi gestite dal cliente|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i server del database SQL di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: la crittografia del Esplora dati di Azure deve usare una chiave gestita dal cliente|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le chiavi che usano la crittografia RSA devono avere una dimensione minima della chiave specificata|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i pod del cluster Kubernetes devono usare solo la rete host e l'intervallo di porte approvati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema devono essere installati nelle macchine|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per i criteri di controllo del sistema-uso dei privilegi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i processi di analisi di flusso di Azure devono usare chiavi gestite dal cliente per crittografare i dati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che ' Java version ' sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le API è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione HTTP" sia il più recente, se usato per eseguire l'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: tutte le porte di rete devono essere limitate nei gruppi di sicurezza di rete associati alla macchina virtuale|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile selezionare il piano tariffario Standard del Centro sicurezza|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Windows che non limitano la lunghezza minima della password a 14 caratteri|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'applicazione Web deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile abilitare il controllo in SQL Server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'agente di Log Analytics deve essere installato nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Sicurezza dei dati avanzata deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la sicurezza dei dati avanzata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario usare il controllo di accesso Role-Based (RBAC) sui servizi Kubernetes|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le macchine virtuali devono avere l'estensione di configurazione Guest|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: il log attività deve essere conservato per almeno un anno|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le porte di gestione delle macchine virtuali devono essere protette con il controllo di accesso di rete just-in-Time|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per i server PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: distribuire Advanced Threat Protection per gli account Cosmos DB|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nei servizi app devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'app per le API deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. ClassicNetwork/networkSecurityGroups/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. SQL/Servers/firewallRules/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le macchine virtuali con connessione Internet non devono essere protette con i gruppi di sicurezza di rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Windows per cui non è abilitata l'impostazione di complessità password|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i registri contenitori|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Azure Data Box i processi devono abilitare la crittografia doppia per i dati inattivi nel dispositivo|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: Microsoft antimalware per Azure deve essere configurato per aggiornare automaticamente le firme di protezione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni specifiche del criterio (Microsoft. Authorization/policyAssignments/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per i server MySQL flessibili|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le applicazioni Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i certificati che usano la crittografia RSA devono avere la dimensione minima della chiave specificata|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i registri contenitori non devono consentire l'accesso alla rete senza restrizioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario attivare la connessione SSL per i server di database PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'estensione di configurazione Guest deve essere distribuita in macchine virtuali di Azure con identità gestita assegnata dal sistema|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per i server MySQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Windows che non archiviano le password con la crittografia reversibile|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per l'assegnazione dei diritti utente|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso RDP da Internet deve essere bloccato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Linux per i quali non sono impostate le autorizzazioni file passwd su 0644|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le subnet devono essere associate a un gruppo di sicurezza di rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario attivare la connessione SSL per i server di database MySQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Linux che consentono le connessioni remote dagli account senza password|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la crittografia doppia in Azure Esplora dati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: L'agente di Log Analytics deve essere installato nei set di scalabilità di macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Nell'app Web è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: la crittografia del disco deve essere abilitata in Azure Esplora dati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Linux con account senza password|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le aree di lavoro di Azure sinapsi devono usare chiavi gestite dal cliente per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le funzioni è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: tutto il traffico Internet deve essere instradato tramite il firewall di Azure distribuito|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: i computer Linux devono soddisfare i requisiti per la linea di base di sicurezza di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: l'accesso alla rete pubblica deve essere disabilitato per i server MariaDB|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità dei database SQL devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Effetto per i criteri: le chiavi che usano la crittografia a curva ellittica devono avere i nomi di curva specificati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Livello CMMC 3|Assegnazione dei criteri|Spazi dei nomi esclusi dalla valutazione dei criteri: i pod del cluster Kubernetes devono usare solo la rete host e l'intervallo di porte approvati|Elenco di spazi dei nomi Kubernetes da escludere dalla valutazione dei criteri.|
|Livello CMMC 3|Assegnazione dei criteri|Versione più recente di Java per i servizi app|Versione di Java più recente supportata per Servizi app|
|Livello CMMC 3|Assegnazione dei criteri|Versione più recente di Python per Linux per i servizi app|Versione di Python più recente supportata per Servizi app|
|Livello CMMC 3|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM con sistema operativo Linux supportato da aggiungere all'ambito durante il controllo della distribuzione di Log Analytics Agent|Valore di esempio:'/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.Compute/images/ContosoStdImage '|
|Livello CMMC 3|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM che hanno supportato il sistema operativo Windows da aggiungere all'ambito durante il controllo della distribuzione di Log Analytics Agent|Valore di esempio:'/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.Compute/images/ContosoStdImage '|
|Livello CMMC 3|Assegnazione dei criteri|Elenco di aree in cui deve essere abilitato Network Watcher|Controllare se Network Watcher non è abilitato per le aree.|
|Livello CMMC 3|Assegnazione dei criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica||
|Livello CMMC 3|Assegnazione dei criteri|Valore massimo nell'intervallo di porte host consentito che i pod possono usare nello spazio dei nomi della rete host|Valore massimo nell'intervallo di porte host consentito che i pod possono utilizzare nello spazio dei nomi della rete host.|
|Livello CMMC 3|Assegnazione dei criteri|Dimensioni minime della chiave RSA per le chiavi|Dimensioni minime della chiave per le chiavi RSA.|
|Livello CMMC 3|Assegnazione dei criteri|Certificati di dimensioni minime della chiave RSA|Dimensioni minime della chiave per i certificati RSA.|
|Livello CMMC 3|Assegnazione dei criteri|Versione minima di TLS per i server Web Windows|I server Web Windows con versioni di TLS inferiori verranno valutati come non conformi|
|Livello CMMC 3|Assegnazione dei criteri|Valore minimo nell'intervallo di porte host consentito che i pod possono usare nello spazio dei nomi della rete host|Valore minimo nell'intervallo di porte host consentito che i pod possono utilizzare nello spazio dei nomi della rete host.|
|Livello CMMC 3|Assegnazione dei criteri|Requisito della modalità|Modalità necessaria per tutti i criteri di WAF|
|Livello CMMC 3|Assegnazione dei criteri|Requisito della modalità|Modalità necessaria per tutti i criteri di WAF|
|Livello CMMC 3|Assegnazione dei criteri|Percorsi host consentiti per i volumi hostPath di pod da usare|Percorsi host consentiti per i volumi hostPath pod da usare. Consente di specificare un elenco di percorsi vuoti per bloccare tutti i percorsi host.|
|Livello CMMC 3|Assegnazione dei criteri|Accesso di rete: percorsi del Registro di sistema ai quali è possibile accedere in modo remoto|Specifica i percorsi del Registro di sistema che saranno accessibili tramite la rete, indipendentemente dagli utenti o i gruppi elencati nell'elenco di controllo di accesso (ACL) della chiave del Registro di sistema `winreg`.|
|Livello CMMC 3|Assegnazione dei criteri|Accesso alla rete: percorsi e sottopercorsi del Registro di sistema ai quali è possibile accedere in modalità remota|Specifica i percorsi e i sottopercorsi del Registro di sistema che saranno accessibili tramite la rete, indipendentemente dagli utenti o i gruppi elencati nell'elenco di controllo di accesso (ACL) della chiave del Registro di sistema `winreg`.|
|Livello CMMC 3|Assegnazione dei criteri|Accesso di rete: condivisioni alle quali è possibile accedere in modo anonimo|Specifica le condivisioni di rete a cui possono accedere gli utenti anonimi. La configurazione predefinita per questa impostazione dei criteri ha un effetto ridotto perché tutti gli utenti devono essere autenticati prima di poter accedere alle risorse condivise nel server.|
|Livello CMMC 3|Assegnazione dei criteri|Sicurezza di rete: configurare i tipi di crittografia consentiti per Kerberos|Specifica i tipi di crittografia che possono essere utilizzati da Kerberos.|
|Livello CMMC 3|Assegnazione dei criteri|Sicurezza di rete: livello di autenticazione di LAN Manager|Consente di specificare il protocollo di autenticazione di richiesta-risposta per gli accessi di rete. Questa scelta influiscono sul livello di protocollo di autenticazione utilizzato dai client, sul livello di sicurezza della sessione negoziato e sul livello di autenticazione accettato dai server.|
|Livello CMMC 3|Assegnazione dei criteri|Sicurezza di rete: requisiti per la firma client LDAP|Specificare il livello di firma dei dati richiesto per conto dei client che inviano richieste di binding LDAP.|
|Livello CMMC 3|Assegnazione dei criteri|Sicurezza di rete: sicurezza sessione minima per client basati su NTLM SSP (incluso l'RPC sicuro)|Specifica i comportamenti consentiti dai client per le applicazioni che utilizzano il provider di supporto per la sicurezza (SSP) NTLM. L'interfaccia SSP (SSPI) viene utilizzata dalle applicazioni che necessitano di servizi di autenticazione. [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)Per ulteriori informazioni, vedere.|
|Livello CMMC 3|Assegnazione dei criteri|Sicurezza di rete: sicurezza sessione minima per server basati su NTLM SSP (incluso l'RPC sicuro)|Specifica i comportamenti consentiti dai server per le applicazioni che utilizzano il provider di supporto per la sicurezza (SSP) NTLM. L'interfaccia SSP (SSPI) viene utilizzata dalle applicazioni che necessitano di servizi di autenticazione.|
|Livello CMMC 3|Assegnazione dei criteri|Versione più recente di PHP per i servizi app|Versione di PHP più recente supportata per Servizi app|
|Livello CMMC 3|Assegnazione dei criteri|Periodo di conservazione richiesto (giorni) per i log di diagnostica dell'hub Internet||
|Livello CMMC 3|Assegnazione dei criteri|Nome del gruppo di risorse per Network Watcher|Nome del gruppo di risorse di Network Watcher, ad esempio NetworkWatcherRG. Questo è il gruppo di risorse in cui si trovano i controlli di rete.|
|Livello CMMC 3|Assegnazione dei criteri|Impostazione di controllo obbligatoria per SQL Server||
|Livello CMMC 3|Assegnazione dei criteri|Azure Data Box SKU che supportano la crittografia doppia basata su software|Elenco di SKU di Azure Data Box che supportano la crittografia doppia basata su software|
|Livello CMMC 3|Assegnazione dei criteri|UAC: modalità Approvazione amministratore per l'account amministratore predefinito|Specifica il comportamento della modalità Approvazione amministratore per l'account amministratore predefinito.|
|Livello CMMC 3|Assegnazione dei criteri|Controllo dell'account utente: comportamento della richiesta di elevazione dei privilegi per gli amministratori in modalità Approvazione amministratore|Specifica il comportamento della richiesta di elevazione dei privilegi per gli amministratori.|
|Livello CMMC 3|Assegnazione dei criteri|Controllo dell'account utente: rilevare le installazioni dell'applicazione e richiedere l'elevazione|Specifica il comportamento del rilevamento dell'installazione dell'applicazione per il computer.|
|Livello CMMC 3|Assegnazione dei criteri|Controllo dell'account utente: Esegui tutti gli amministratori in modalità Approvazione amministratore|Specifica il comportamento di tutte le impostazioni dei criteri di controllo dell'account utente (UAC) per il computer.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi che potrebbero forzare l'arresto da un sistema remoto|Specifica gli utenti e i gruppi autorizzati ad arrestare il computer da una posizione remota sulla rete.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi a cui viene negato l'accesso al computer dalla rete|Specifica gli utenti o i gruppi a cui non è esplicitamente consentito la connessione al computer attraverso la rete.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi a cui è stato negato l'accesso locale|Specifica quali utenti e gruppi non sono autorizzati in modo esplicito ad accedere al computer.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi a cui viene negato l'accesso come processo batch|Specifica quali utenti e gruppi non sono autorizzati in modo esplicito ad accedere al computer come processo batch, ad esempio un'attività pianificata.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi a cui viene negato l'accesso come servizio|Specifica gli account di servizio non autorizzati in modo esplicito per la registrazione di un processo come servizio.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi a cui viene negato l'accesso tramite Servizi Desktop remoto|Specifica quali utenti e gruppi non sono autorizzati in modo esplicito ad accedere al computer tramite Servizi terminal/Desktop remoto client.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi che possono ripristinare file e directory|Specifica gli utenti e i gruppi autorizzati a ignorare file, directory, registro di sistema e altre autorizzazioni per oggetti persistenti durante il ripristino di file e directory di cui è stato eseguito il backup.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti e gruppi che potrebbero arrestare il sistema|Specifica gli utenti e i gruppi che sono connessi localmente ai computer nell'ambiente in uso possono arrestare il sistema operativo con il comando Arresta.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono accedere localmente|Specifica gli utenti remoti in rete autorizzati a connettersi al computer. Questa operazione non include Connessione Desktop remoto.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono eseguire il backup di file e directory|Consente di specificare gli utenti e i gruppi autorizzati a aggirare le autorizzazioni per file e directory per eseguire il backup del sistema.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono modificare l'ora di sistema|Specifica gli utenti e i gruppi autorizzati a modificare l'ora e la data del clock interno del computer.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono modificare il fuso orario|Specifica gli utenti e i gruppi autorizzati a modificare il fuso orario del computer.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono creare un oggetto token|Specifica gli utenti e i gruppi autorizzati a creare un token di accesso, che può fornire diritti elevati per accedere ai dati sensibili.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono accedere localmente|Specifica gli utenti o i gruppi che possono accedere in modo interattivo al computer. Anche gli utenti che tentano di accedere tramite Connessione Desktop remoto o IIS richiedono questo diritto utente.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti desktop remoto|Utenti o gruppi che possono accedere tramite Servizi Desktop remoto|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono gestire il registro di controllo e di protezione|Specifica gli utenti e i gruppi autorizzati a modificare le opzioni di controllo per file e directory e cancellare il registro di sicurezza.|
|Livello CMMC 3|Assegnazione dei criteri|Utenti o gruppi che possono assumere la proprietà di file o di altri oggetti|Specifica gli utenti e i gruppi autorizzati ad assumere la proprietà di file, cartelle, chiavi del registro di sistema, processi o thread. Il diritto utente ignora tutte le autorizzazioni disponibili per proteggere gli oggetti in modo da assegnare la proprietà all'utente specificato.|

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).