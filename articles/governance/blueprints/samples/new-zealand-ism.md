---
title: Esempio di progetto con restrizioni per ISM New Zealand
description: Panoramica dell'esempio di progetto con restrizioni ISM di New Zealand. Questo esempio di progetto consente ai clienti di valutare specifici controlli.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803931"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Esempio di progetto con restrizioni per ISM New Zealand

L'esempio New Zealand ISM Restricted Blueprint fornisce Guard di governance tramite [criteri di Azure](../../policy/overview.md) che consentono di valutare i controlli manuali specifici per la [sicurezza delle informazioni di New Zealand](https://www.nzism.gcsb.govt.nz/) . Questo progetto consente ai clienti di distribuire un set di criteri di base per qualsiasi architettura distribuita in Azure che deve implementare i controlli per la nuova Zelanda ISM con restrizioni.

## <a name="control-mapping"></a>Mapping dei controlli

Il [mapping del controllo criteri di Azure](../../policy/samples/new-zealand-ism.md) fornisce informazioni dettagliate sulle definizioni dei criteri incluse in questo progetto e sul modo in cui queste definizioni dei criteri vengono mappate ai **controlli** nel manuale della sicurezza delle informazioni di New Zealand. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire il progetto di Azure Blueprints New Zealand stricted Blueprint Sample, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare il nuovo esempio di progetto con **restrizioni ISM di Zealand** in _altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio New Zealand ISM Restricted Blueprint.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti inclusi nell'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica può essere spostata dall'allineamento ai controlli con restrizioni ISM di New Zealand.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. Fornire le **Note sulle modifiche** , ad esempio "prima versione pubblicata dall'esempio di progetto con restrizioni ISM di New Zealand". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

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
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Elenco di utenti che devono essere inclusi nel gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con gli utenti da includere nel gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Elenco di utenti che devono essere esclusi dal gruppo amministratori VM Windows|Elenco delimitato da punto e virgola con gli utenti da escludere dal gruppo locale Administrators, ad esempio: Administrator; utente1; utente2|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Elenco dei soli utenti che devono essere inclusi nel gruppo Administrators delle macchine virtuali Windows|Elenco delimitato da punti e virgola di tutti i membri previsti del gruppo locale Administrators. Ad esempio: Administrator; User1; myUser2|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|ID dell'area di lavoro Log Analytics per la segnalazione degli agenti di macchine virtuali|ID (GUID) dell'area di lavoro Log Analytics a cui gli agenti delle macchine virtuali devono inviare le segnalazioni|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve essere abilitato per il servizio front door di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: le impostazioni di valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: le raccomandazioni per la protezione avanzata della rete adattiva devono essere applicate alle macchine virtuali con connessione Internet|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Alla sottoscrizione deve essere assegnato più di un proprietario|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: La crittografia del disco deve essere applicata nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve usare la modalità specificata per il gateway applicazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Requisito della modalità WAF per il gateway applicazione|È necessario abilitare la modalità di prevenzione o rilevamento nel servizio del gateway applicazione|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Transparent Data Encryption deve essere abilitata nei database SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: la valutazione della vulnerabilità deve essere abilitata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate che hanno supportato il sistema operativo Windows per aggiungere l'ambito alle immagini nella raccolta per i criteri: Distribuisci-configura Dependency Agent per l'abilitazione in macchine virtuali Windows|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare solo connessioni sicure alla cache di Azure per Redis|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Windows che mancano i membri specificati nel gruppo Administrators|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate che hanno supportato il sistema operativo Windows da aggiungere all'ambito di altre immagini nella raccolta per i criteri: [Anteprima]: Log Analytics agente deve essere abilitato per le immagini di macchina virtuale elencate|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate con sistema operativo Linux supportato da aggiungere all'ambito di altre immagini nella raccolta per i criteri: [Anteprima]: Log Analytics agente deve essere abilitato per le immagini di macchina virtuale elencate|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono limitare l'accesso alla rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate che hanno supportato il sistema operativo Windows per aggiungere l'ambito alle immagini nella raccolta per i criteri: Distribuisci-configura Dependency Agent per l'abilitazione nei set di scalabilità di macchine virtuali Windows|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Windows con account aggiuntivi nel gruppo Administrators|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve usare la modalità specificata per il servizio front door di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Requisito della modalità WAF per il servizio front door di Azure|È necessario abilitare la modalità di prevenzione o rilevamento nel servizio front-end di Azure|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: i controlli applicazione adattivi per la definizione di applicazioni sicure devono essere abilitati nei computer|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Per la sottoscrizione devono essere designati al massimo 3 proprietari|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: [Anteprima]: l'accesso pubblico dell'account di archiviazione non deve essere consentito|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i server Web Windows che non usano protocolli di comunicazione protetti|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Versione minima di TLS per i server Web Windows|I server Web Windows con versioni di TLS inferiori verranno valutati come non conformi|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate con sistema operativo Linux supportato da aggiungere all'ambito di altre immagini nella raccolta per i criteri: Log Analytics Agent deve essere abilitato nei set di scalabilità di macchine virtuali per le immagini di macchina virtuale elencate|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Facoltativo: elenco di immagini di VM personalizzate che hanno supportato il sistema operativo Windows da aggiungere all'ambito di altre immagini nella raccolta per i criteri: Log Analytics Agent deve essere abilitato nei set di scalabilità di macchine virtuali per le immagini di macchine virtuali elencate|Per ulteriori informazioni sulla configurazione Guest, visitare [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Windows con i membri specificati nel gruppo Administrators|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'app per le funzioni deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: le sottoscrizioni di Azure devono avere un profilo di log per il log attività|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica||
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema devono essere installati nelle macchine|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le API è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: È consigliabile distribuire l'estensione Microsoft IaaSAntimalware nei server Windows|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'applicazione Web deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la protezione DDoS di Azure standard|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Sicurezza dei dati avanzata deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la sicurezza dei dati avanzata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: il log attività deve essere conservato per almeno un anno|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: le porte di gestione delle macchine virtuali devono essere protette con il controllo di accesso di rete just-in-Time|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'app per le API deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Windows in cui Windows Defender exploit Guard non è abilitato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Windows in cui Windows Defender exploit Guard non è abilitato|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Stato di conformità da segnalare per i computer Windows in cui Windows Defender exploit Guard non è disponibile|Windows Defender exploit Guard è disponibile solo a partire da Windows 10/Windows Server con l'aggiornamento 1709. L'impostazione di questo valore su' non conforme ' Mostra i computer con versioni precedenti in cui Windows Defender exploit Guard non è disponibile (ad esempio Windows Server 2012 R2) come non conforme. Impostando questo valore su' compliant ', i computer vengono visualizzati come conformi.|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le applicazioni Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: È consigliabile correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Il debug remoto deve essere disattivato per le app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Linux che consentono le connessioni remote dagli account senza password|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Linux che consentono le connessioni remote dagli account senza password|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Nell'app Web è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: i computer Windows devono soddisfare i requisiti per ' impostazioni di sicurezza-Criteri account '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Applicare la cronologia delle password per gli account locali delle macchine virtuali Windows|Specifica i limiti per il riutilizzo delle password: numero di volte per cui è necessario creare una nuova password per un account utente prima che la password possa essere ripetuta|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: i computer Windows devono soddisfare i requisiti per ' impostazioni di sicurezza-Criteri account '|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Validità massima password per gli account locali delle macchine virtuali Windows|Specifica il numero massimo di giorni che possono trascorrere prima che sia necessario modificare la password di un account utente. il formato del valore è costituito da due numeri interi separati da una virgola, che indica un intervallo inclusivo|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Validità minima password per gli account locali delle macchine virtuali Windows|Specifica il numero minimo di giorni che devono trascorrere prima che sia possibile modificare la password di un account utente|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Lunghezza minima password per gli account locali della VM Windows|Specifica il numero minimo di caratteri che la password di un account utente può contenere|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|La password deve soddisfare i requisiti di complessità per gli account locali delle macchine virtuali Windows|Specifica se la password di un account utente deve essere complessa. Se necessario, una password complessa non deve contenere parte del nome dell'account utente o del nome completo; avere una lunghezza di almeno 6 caratteri; contenere una combinazione di caratteri maiuscoli, minuscoli, numerici e non alfabetici|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: controlla i computer Linux con account senza password|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Includi server connessi ad arco durante la valutazione dei criteri: controlla i computer Linux con account senza password|Selezionando "true", l'utente accetta di essere addebitato mensilmente per computer connesso ad arco|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le funzioni è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per i criteri: [Anteprima]: tutto il traffico Internet deve essere instradato tramite il firewall di Azure distribuito|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|New Zealand ISM con restrizioni|Assegnazione dei criteri|Effetto per il criterio: Le vulnerabilità dei database SQL devono essere risolte|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).