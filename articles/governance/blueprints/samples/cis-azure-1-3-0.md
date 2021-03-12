---
title: Esempio di modello di benchmark v 1.3.0 di CIS Microsoft Azure Foundation
description: Panoramica dell'esempio di progetto di benchmark v Microsoft Azure Foundations di CIS. Questo esempio di progetto consente ai clienti di valutare specifici controlli.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: d6b3a84ab426c5003233958b77f5c480f28cd704
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202663"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Esempio di modello di benchmark v 1.3.0 di CIS Microsoft Azure Foundation

L'esempio SID Microsoft Azure Foundations benchmark v 1.3.0 Blueprint fornisce Guard Guard di governance usando [criteri di Azure](../../policy/overview.md) che consentono di valutare specifiche CIS Microsoft Azure Foundation benchmark v 1.3.0 raccomandazioni. Questo progetto consente ai clienti di distribuire un set di criteri di base per qualsiasi architettura distribuita in Azure che deve Microsoft Azure implementare i consigli di 1.3.0 per le fondamenta di benchmark v.

## <a name="recommendation-mapping"></a>Mapping di raccomandazioni

Il [mapping delle raccomandazioni sui criteri di Azure](../../policy/samples/cis-azure-1-3-0.md) fornisce informazioni dettagliate sulle definizioni dei criteri incluse in questo progetto e sul modo in cui queste definizioni di criteri sono mappate ai **consigli** in CIS Microsoft Azure Foundations benchmark v 1.3.0. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire l'esempio Azure Blueprints CIS Microsoft Azure Foundations benchmark v 1.3.0 Blueprint, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Individuare l'esempio **CIS Microsoft Azure Foundations benchmark v 1.3.0** Blueprint in _altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: Specificare un nome per la copia dell'esempio di progetto CIS Microsoft Azure Foundations Benchmark.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti inclusi nell'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dall'allineamento con le raccomandazioni di 1.3.0 Microsoft Azure di base di benchmark v

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dell'esempio di progetto CIS Microsoft Azure Foundations Benchmark". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

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
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Elenco di estensioni macchina virtuale approvate per l'uso|Elenco delimitato da punti e virgola delle estensioni delle macchine virtuali. per visualizzare un elenco completo delle estensioni, usare il comando Azure PowerShell Get-AzVMExtensionImage|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le istanze gestite di SQL devono usare chiavi gestite dal cliente per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le impostazioni di valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica in Azure Data Lake Store devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: La crittografia del disco deve essere applicata nelle macchine virtuali|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'insieme di credenziali delle chiavi deve essere abilitato per l'eliminazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che l'app per le API abbia ' certificati client (certificati client in ingresso)' impostati su' on '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: SQL Server deve usare chiavi gestite dal cliente per crittografare i dati inattivi|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario usare l'identità gestita nel app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per Key Vault|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i ruoli proprietario sottoscrizione personalizzata non devono esistere|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le chiavi devono avere date di scadenza impostate|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Transparent Data Encryption deve essere abilitata nei database SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: la valutazione della vulnerabilità deve essere abilitata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione di PHP" sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per il servizio app|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono limitare l'accesso alla rete usando le regole della rete virtuale|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'identità gestita deve essere usata nell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'accesso SSH da Internet deve essere bloccato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i dischi non collegati devono essere crittografati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per l'archiviazione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono limitare l'accesso alla rete|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nelle app per la logica devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nell'hub degli eventi devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che nel app per le funzioni sia necessario solo FTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni di sicurezza specifiche (Microsoft. Security/securitySolutions/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni di sicurezza specifiche (Microsoft. Security/securitySolutions/Write)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica negli account batch devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: il provisioning automatico dell'agente di Log Analytics deve essere abilitato nella sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che FTPS sia necessario nell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le sottoscrizioni devono avere un indirizzo di posta elettronica di contatto per i problemi di sicurezza|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'accesso pubblico dell'account di archiviazione non deve essere consentito|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per Kubernetes|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la limitazione della connessione per i server di database PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che l'app WEB abbia ' certificati client (certificati client in ingresso)' impostati su' on '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per SQL Server nei computer|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la notifica tramite posta elettronica per gli avvisi con gravità elevata|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'account di archiviazione deve usare la chiave gestita dal cliente per la crittografia|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione Python" sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nei set di scalabilità di macchine virtuali devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i server del database SQL di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nell'hub eventi devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Gli aggiornamenti di sistema devono essere installati nelle macchine|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che ' Java version ' sia il più recente, se usato come parte dell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: SQL Server deve essere configurato con una conservazione del controllo di 90 giorni o superiore.|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le API è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare l'autenticazione nell'app Web|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le date di scadenza dei segreti devono essere impostate|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che "versione HTTP" sia il più recente, se usato per eseguire l'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: solo FTPS dovrebbe essere obbligatorio nell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: L'applicazione Web deve essere accessibile solo tramite HTTPS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: È consigliabile abilitare il controllo in SQL Server|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Sicurezza dei dati avanzata deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare la sicurezza dei dati avanzata in SQL Istanza gestita|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario usare il controllo di accesso Role-Based (RBAC) sui servizi Kubernetes|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nei servizi di ricerca devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nei servizi app devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/securityRules/Write)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. Network/networkSecurityGroups/Write)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. SQL/Servers/firewallRules/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni amministrative specifiche (Microsoft. SQL/Servers/firewallRules/Write)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario installare solo le estensioni di macchina virtuale approvate|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare Azure Defender per i registri contenitori|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'identità gestita deve essere usata nell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare l'autenticazione nell'app per le API|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni specifiche del criterio (Microsoft. Authorization/policyAssignments/Delete)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario che esista un avviso del log attività per operazioni specifiche del criterio (Microsoft. Authorization/policyAssignments/Write)|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario abilitare l'autenticazione nell'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica in Data Lake Analytics devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica in Key Vault devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario attivare la connessione SSL per i server di database PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'accesso RDP da Internet deve essere bloccato|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: è necessario attivare la connessione SSL per i server di database MySQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: assicurarsi che l'app per le funzioni abbia ' certificati client (certificati client in ingresso)' impostati su' on '|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i checkpoint dei log devono essere abilitati per i server di database PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le connessioni log devono essere abilitate per i server di database PostgreSQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: le disconnessioni devono essere registrate per i server di database PostgreSQL.|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Nell'app Web è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica nel bus di servizio devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: i log di diagnostica in analisi di flusso di Azure devono essere abilitati|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per il criterio: Nell'app per le funzioni è necessario usare la versione più recente di TLS|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Effetto per i criteri: l'account di archiviazione contenente il contenitore con i log attività deve essere crittografato con BYOK|Per ulteriori informazioni sugli effetti, visitare [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Includere i cluster AKS quando si controlla se i log di diagnostica del set di scalabilità di macchine virtuali sono abilitati||
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Versione più recente di Java per i servizi app|Versione di Java più recente supportata per Servizi app|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Versione più recente di Python per Linux per i servizi app|Versione di Python più recente supportata per Servizi app|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Elenco di aree in cui deve essere abilitato Network Watcher|Per visualizzare un elenco completo delle aree, eseguire il comando di PowerShell Get-AzLocation|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Versione più recente di PHP per i servizi app|Versione di PHP più recente supportata per Servizi app|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Periodo di conservazione richiesto (giorni) per i log delle risorse|Per ulteriori informazioni sui log delle risorse, visitare [https://aka.ms/resourcelogs](https://aka.ms/resourcelogs) |
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Nome del gruppo di risorse per Network Watcher|Nome del gruppo di risorse in cui si trovano i controlli di rete|
|1.3.0 di base di Microsoft Azure CIS per le fondamenta v|Assegnazione dei criteri|Impostazione di controllo obbligatoria per SQL Server||

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).