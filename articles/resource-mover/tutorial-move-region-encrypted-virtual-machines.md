---
title: Spostare macchine virtuali di Azure crittografate tra aree usando Spostamento risorse di Azure
description: Informazioni su come spostare macchine virtuali di Azure crittografate in un'altra area usando Spostamento risorse di Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600693"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Esercitazione: Spostare macchine virtuali di Azure crittografate tra aree

Questo articolo illustra come spostare macchine virtuali di Azure crittografate in un'area di Azure diversa usando [Spostamento risorse di Azure](overview.md). 

Le macchine virtuali crittografate possono essere descritte come:

- Macchine virtuali con dischi con Crittografia dischi di Azure abilitata. Per altre informazioni, vedere [Creare e crittografare una macchina virtuale Windows usando il portale di Azure](../virtual-machines/windows/disk-encryption-portal-quickstart.md).
- Macchine virtuali che usano chiavi gestite dal cliente (CMK) per la crittografia dei dati in pausa o la crittografia lato server. Per altre informazioni, vedere Usare il portale di Azure per abilitare la crittografia lato server con chiavi gestite dal cliente [per i dischi gestiti.](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare i prerequisiti. 
> * Per le macchine virtuali con Crittografia dischi di Azure abilitata, copiare le chiavi e i segreti dall'insieme di credenziali delle chiavi dell'area di origine all'insieme di credenziali delle chiavi dell'area di destinazione.
> * Preparare lo spostamento delle macchine virtuali e selezionare le risorse nell'area di origine da cui si vuole spostarle.
> * Risolvere le dipendenze delle risorse.
> * Per le macchine virtuali con l'Crittografia dischi di Azure abilitata, assegnare manualmente l'insieme di credenziali delle chiavi di destinazione. Per le macchine virtuali che usano la crittografia lato server con chiavi gestite dal cliente, assegnare manualmente un set di crittografia del disco nell'area di destinazione.
> * Spostare l'insieme di credenziali delle chiavi o il set di crittografia del disco.
> * Preparare e spostare il gruppo di risorse di origine. 
> * Preparare e spostare le altre risorse.
> * Decidere se annullare o eseguire il commit dello spostamento. 
> * Facoltativamente, rimuovere le risorse nell'area di origine dopo lo spostamento.

> [!NOTE]
> Questa esercitazione illustra il percorso più rapido per provare uno scenario. Usa solo le opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al portale di Azure [.](https://portal.azure.com)

## <a name="prerequisites"></a>Prerequisiti

Requisito |Dettagli
--- | ---
**Autorizzazioni per la sottoscrizione** | Verificare di avere accesso *come* proprietario alla sottoscrizione che contiene le risorse da spostare.<br/><br/> *Perché è necessario l'accesso come proprietario?* La prima volta che si aggiunge una risorsa per una coppia di origine e destinazione specifica in una sottoscrizione di Azure, Resource Mover crea un'identità gestita assegnata dal [sistema,](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)nota in precedenza come identità del servizio gestito. Questa identità è attendibile dalla sottoscrizione. Prima di poter creare l'identità e assegnarle i ruoli necessari *(Collaboratore* e Amministratore  accesso utenti nella sottoscrizione di origine), l'account utilizzato per aggiungere le risorse richiede le autorizzazioni di proprietario nella sottoscrizione.  Per altre informazioni, vedere [Ruoli di amministratore della sottoscrizione classica, Ruoli di Azure e](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)ruoli Azure AD.
**Supporto per macchine virtuali** | Verificare che le macchine virtuali da spostare siano supportate eseguendo le operazioni seguenti:<li>[Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le macchine virtuali Windows supportate.<li>[Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le macchine virtuali Linux e le versioni del kernel supportate.<li>Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
**Requisiti dell'insieme di credenziali delle chiavi (Crittografia dischi di Azure)** | Se sono state abilitate Crittografia dischi di Azure per le macchine virtuali, è necessario un insieme di credenziali delle chiavi sia nell'aree di origine che in quello di destinazione. Per altre informazioni, vedere [Creare un insieme di credenziali delle chiavi.](../key-vault/general/quick-create-portal.md)<br/><br/> Per gli insiemi di credenziali delle chiavi nelle aree di origine e di destinazione, sono necessarie le autorizzazioni seguenti:<li>Autorizzazioni chiave: operazioni di gestione delle chiavi (Get, List) e operazioni di crittografia (decrittografia e crittografia)<li>Autorizzazioni segrete: Operazioni di gestione dei segreti (Get, List e Set)<li>Certificato (elenco e get)
**Set di crittografia del disco (crittografia lato server con CMK)** | Se si usano macchine virtuali con crittografia lato server che usa una chiave CMK, è necessario un set di crittografia del disco sia nell'aree di origine che in quella di destinazione. Per altre informazioni, vedere [Creare un set di crittografia del disco.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> Lo spostamento tra aree non è supportato se si usa il modulo di sicurezza hardware (chiavi HSM) per le chiavi gestite dal cliente.
**Quota dell'area di destinazione** | La quota disponibile nella sottoscrizione deve essere sufficiente per creare le risorse che si intende spostare nell'area di destinazione. Se non è disponibile alcuna quota, [richiedere limiti aggiuntivi](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Addebiti per l'area di destinazione** | Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si stanno spostando le macchine virtuali. Usare il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Verificare le autorizzazioni nell'insieme di credenziali delle chiavi

Se si spostano macchine virtuali con Crittografia dischi di Azure abilitata, è necessario eseguire uno script come indicato nella sezione Copiare le chiavi nell'insieme di credenziali delle [chiavi di](#copy-the-keys-to-the-destination-key-vault) destinazione. Gli utenti che eseguono lo script devono disporre delle autorizzazioni appropriate a tale scopo. Per informazioni sulle autorizzazioni necessarie, vedere la tabella seguente. Le opzioni per la modifica delle autorizzazioni sono disponibili nell'insieme di credenziali delle chiavi nel portale di Azure. In **Impostazioni** selezionare **Criteri di accesso.**

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Screenshot del collegamento &quot;Criteri di accesso&quot; nel riquadro Impostazioni dell'insieme di credenziali delle chiavi." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Se le autorizzazioni utente non sono disponibili, selezionare Aggiungi criteri **di accesso** e quindi specificare le autorizzazioni. Se l'account utente dispone già di un criterio, in **Utente** impostare le autorizzazioni in base alle istruzioni riportate nella tabella seguente.

Le macchine virtuali di Azure Crittografia dischi di Azure possono avere le varianti seguenti ed è necessario impostare le autorizzazioni in base ai componenti pertinenti. Le macchine virtuali potrebbero avere:
- Opzione predefinita in cui il disco è crittografato solo con segreti.
- Aggiunta della sicurezza che usa [una chiave di crittografia della chiave (KEK).](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-key-vault"></a>Insieme di credenziali delle chiavi dell'area di origine

Per gli utenti che eseguono lo script, impostare le autorizzazioni per i componenti seguenti: 

Componente | Autorizzazioni necessarie
--- | ---
Segreti |  *Ottieni* <br></br> Selezionare **Autorizzazioni segrete** Operazioni di gestione  >  **dei** segreti e quindi selezionare **Ottieni**. 
Chiavi <br></br> Se si usa una kek, sono necessarie queste autorizzazioni oltre alle autorizzazioni per i segreti. | *Ottenere e* decrittografare  <br></br> Selezionare **Key Permissions** Key Management Operations (Operazioni di gestione delle chiavi per le autorizzazioni  >  chiave) e quindi selezionare **Get (Ottieni).** In **Operazioni di crittografia** selezionare Decrittografa . 

### <a name="destination-region-key-vault"></a>Insieme di credenziali delle chiavi dell'area di destinazione

In **Criteri di accesso** assicurarsi che l'Crittografia dischi di Azure per la crittografia dei **volumi** sia abilitata. 

Per gli utenti che eseguono lo script, impostare le autorizzazioni per i componenti seguenti: 

Componente | Autorizzazioni necessarie
--- | ---
Segreti |  *Set* <br></br> Selezionare **Autorizzazioni segrete Operazioni** di gestione  >  **dei** segreti e quindi selezionare **Imposta**. 
Chiavi <br></br> Se si usa una kek, sono necessarie queste autorizzazioni oltre alle autorizzazioni per i segreti. | *Ottenere*, *creare* e *crittografare* <br></br> Selezionare **Key Permissions** Key Management Operations (Operazioni di gestione delle chiavi per le autorizzazioni  >  chiave) e quindi selezionare Get and Create **(Ottieni** e **crea).** In **Operazioni di crittografia** selezionare **Crittografa**.

<br>

Oltre alle autorizzazioni precedenti, nell'insieme di credenziali delle chiavi [](./common-questions.md#how-is-managed-identity-used-in-resource-mover) di destinazione è necessario aggiungere le autorizzazioni per l'identità di sistema gestita utilizzata da Resource Mover per accedere alle risorse di Azure per conto dell'utente. 

1. In **Impostazioni** selezionare **Aggiungi criteri di accesso**. 
1. In **Selezionare l'entità** cercare il file MSI. Il nome dell'msi è ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Per l'msi, aggiungere le autorizzazioni seguenti:

    Componente | Autorizzazioni necessarie
    --- | ---
    Segreti|  *Ottenere* ed *elencare* <br></br> Selezionare **Autorizzazioni segrete** Operazioni di gestione  >  **dei** segreti e quindi selezionare **Ottieni** ed **elenca**. 
    Chiavi <br></br> Se si usa una kek, sono necessarie queste autorizzazioni oltre alle autorizzazioni per i segreti. | *Get* ed *List* <br></br> Selezionare **Key Permissions** Key Management Operations (Operazioni di gestione delle chiavi con autorizzazioni  >  chiave) e quindi selezionare Get and List **(Ottieni** **ed elenca).**

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copiare le chiavi nell'insieme di credenziali delle chiavi di destinazione

Copiare i segreti e le chiavi di crittografia dall'insieme di credenziali delle chiavi di origine all'insieme di credenziali delle chiavi di destinazione usando [uno script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) fornito.

- Eseguire lo script in PowerShell. È consigliabile usare la versione più recente di PowerShell.
- In particolare, lo script richiede questi moduli:
    - Az.Compute
    - Az.KeyVault (versione 3.0.0)
    - Az.Accounts (versione 2.2.3)

Per eseguire lo script, eseguire queste operazioni:

1. Aprire lo [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) in GitHub.
1. Copiare il contenuto dello script in un file locale e assegnare al file il *nomeCopy-keys.ps1*.
1. Eseguire lo script.
1. Accedere al portale di Azure.
1. Negli elenchi a discesa nella finestra **Input** utente selezionare la sottoscrizione di origine, il gruppo di risorse e la macchina virtuale di origine, quindi selezionare il percorso di destinazione e gli insiemi di credenziali di destinazione per la crittografia di dischi e chiavi.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Screenshot della finestra &quot;Input utente&quot; per l'immissione dei valori dello script." :::

1. Fare clic sul pulsante **Seleziona**. 
   
   Al termine dell'esecuzione dello script, viene visualizzato un messaggio che informa che CopyKeys ha avuto esito positivo.

## <a name="prepare-vms"></a>Preparare le macchine virtuali

1. Dopo aver verificato che le macchine virtuali soddisfino i [prerequisiti,](#prerequisites)assicurarsi che le macchine virtuali da spostare siano attivate. Tutti i dischi delle macchine virtuali che si desidera siano disponibili nell'area di destinazione devono essere collegati e inizializzati nella macchina virtuale.
1. Per assicurarsi che le macchine virtuali siano in grado di disporre dei certificati radice attendibili più recenti e di un elenco di revoche di certificati (CRL) aggiornato, eseguire le operazioni seguenti:
    - Nelle macchine virtuali Windows installare gli ultimi aggiornamenti di Windows.
    - Nelle macchine virtuali Linux seguire le indicazioni del distributore in modo che i computer diseguano i certificati e l'elenco di revoche di certificati più recenti. 
1. Per consentire la connettività in uscita dalle macchine virtuali, eseguire una delle operazioni seguenti:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire [l'accesso agli URL](support-matrix-move-region-azure-vm.md#url-access).
    - Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, creare queste [regole del tag di servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>Selezionare le risorse da spostare

- È possibile selezionare qualsiasi tipo di risorsa supportato in uno dei gruppi di risorse nell'area di origine selezionata.  
- È possibile spostare le risorse in un'area di destinazione nella stessa sottoscrizione dell'area di origine. Se si vuole modificare la sottoscrizione, è possibile farlo dopo lo spostamento delle risorse.

Per selezionare le risorse, eseguire le operazioni seguenti:

1. Nel portale di Azure cercare **spostamento risorse**. In **Servizi** selezionare quindi **Spostamento risorse di Azure**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Screenshot dei risultati della ricerca per Spostamento risorse di Azure nel portale di Azure." :::

1. Nel riquadro Spostamento risorse di Azure **panoramica** selezionare **Sposta tra aree.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Screenshot del pulsante &quot;Sposta tra aree&quot; per aggiungere risorse da spostare in un'altra area." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. Nel riquadro **Sposta risorse** selezionare la scheda **Origine e** destinazione. Negli elenchi a discesa selezionare quindi la sottoscrizione e l'area di origine.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Pagina per selezionare l'area di origine e di destinazione." :::

1. In **Destinazione** selezionare l'area in cui si vogliono spostare le macchine virtuali e quindi selezionare **Avanti.**

1. Selezionare la **scheda Risorse da spostare** e quindi selezionare Seleziona **risorse**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Screenshot del riquadro &quot;Sposta risorse&quot; e del pulsante &quot;Seleziona risorse&quot;." :::

1. Nel riquadro **Seleziona risorse** selezionare le macchine virtuali da spostare. Come accennato [nella sezione Selezionare](#select-the-resources-to-move) le risorse da spostare, è possibile aggiungere solo le risorse supportate per uno spostamento.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Screenshot del riquadro &quot;Seleziona risorse&quot; per la selezione delle macchine virtuali da spostare." :::

    > [!NOTE]
    >  In questa esercitazione si seleziona una macchina virtuale che usa la crittografia lato server (rayne-vm) con una chiave gestita dal cliente e una macchina virtuale con la crittografia del disco abilitata (rayne-vm-ade).

1. Selezionare **Fine**.
1. Selezionare la **scheda Risorse da spostare** e quindi selezionare **Avanti.**
1. Selezionare la **scheda Rivedi** e quindi controllare le impostazioni di origine e destinazione. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Screenshot del riquadro per esaminare le impostazioni di origine e destinazione." :::

1. Selezionare **Proceed** (Continua) per iniziare ad aggiungere le risorse.
1. Selezionare l'icona delle notifiche per tenere traccia dello stato di avanzamento. Al termine del processo, nel riquadro **Notifiche** selezionare **Risorse aggiunte per lo spostamento.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Screenshot del riquadro &quot;Notifiche&quot; per confermare che le risorse sono state aggiunte correttamente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Dopo aver selezionato la notifica, esaminare le risorse nella **pagina Tra** aree.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Screenshot delle risorse aggiunte con stato &quot;Prepara in sospeso&quot;." :::

> [!NOTE]
> - Le risorse che si aggiungono vengono inserite in uno *stato Prepara in* sospeso.
> - Il gruppo di risorse per le macchine virtuali viene aggiunto automaticamente.
> - Se si  modificano le voci di configurazione destinazione per usare una risorsa già esistente nell'area di destinazione, lo stato della risorsa viene impostato su *Commit* in sospeso, perché non è necessario avviare uno spostamento per tale risorsa.
> - Se si vuole rimuovere una risorsa aggiunta, il metodo da usare dipende dalla posizione in cui ci si trova nel processo di spostamento. Per altre informazioni, vedere Gestire [le raccolte di spostamento e i gruppi di risorse.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

1. Se le risorse visualizzano *un messaggio Convalida dipendenze* nella **colonna Problemi,** selezionare il **pulsante Convalida dipendenze.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Screenshot che mostra il pulsante &quot;Convalida dipendenze&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Viene avviato il processo di convalida.
1. Se vengono trovate dipendenze, selezionare **Aggiungi dipendenze.**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Screenshot del pulsante &quot;Aggiungi dipendenze&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. Nel riquadro **Aggiungi dipendenze** lasciare l'opzione **mostra tutte le dipendenze** predefinita.

    - **Mostra tutte le dipendenze** scorre tutte le dipendenze dirette e indirette per una risorsa. Ad esempio, per una macchina virtuale, mostra la scheda di interfaccia di rete, la rete virtuale, i gruppi di sicurezza di rete (NSG) e così via.
    - **Mostra dipendenze di primo livello mostra solo** le dipendenze dirette. Ad esempio, per una macchina virtuale mostra la scheda di interfaccia di rete, ma non la rete virtuale.
 
1. Selezionare le risorse dipendenti da aggiungere e quindi **selezionare Aggiungi dipendenze**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Screenshot dell'elenco delle dipendenze e del pulsante &quot;Aggiungi dipendenze&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Convalidare di nuovo le dipendenze. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Screenshot del riquadro per la riconvalida delle dipendenze." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Assegnare le risorse di destinazione

È necessario assegnare manualmente le risorse di destinazione associate alla crittografia.

- Se si sposta una macchina virtuale con Crittografia dischi di Azure abilitata, l'insieme di credenziali delle chiavi nell'area di destinazione viene visualizzato come dipendenza.
- Se si sposta una macchina virtuale con crittografia lato server che usa LEK, il set di crittografia del disco nell'area di destinazione viene visualizzato come dipendenza. 
- Poiché questa esercitazione illustra lo spostamento di una macchina virtuale Crittografia dischi di Azure abilitata e che usa un CMK, sia l'insieme di credenziali delle chiavi di destinazione che il set di crittografia del disco vengono visualizzati come dipendenze.

Per assegnare manualmente le risorse di destinazione, eseguire le operazioni seguenti:

1. Nella voce del set di crittografia del disco selezionare **Risorsa non assegnata** nella colonna Configurazione **di** destinazione.
1. In **Impostazioni di configurazione** selezionare il set di crittografia del disco di destinazione e quindi selezionare Salva **modifiche**.
1. È possibile salvare e convalidare le dipendenze per la risorsa che si sta modificando oppure è possibile salvare solo le modifiche e quindi convalidare tutte le modifiche apportate contemporaneamente.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Screenshot del riquadro &quot;Configurazione destinazione&quot; per salvare le modifiche nell'area di destinazione." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Dopo aver aggiunto la risorsa di destinazione, lo stato del set di crittografia del disco viene modificato in *Commit move pending*.

1. Nella voce Dell'insieme di credenziali delle chiavi **selezionare Risorsa non assegnata** nella colonna Configurazione **di** destinazione. In **Impostazioni di configurazione** selezionare l'insieme di credenziali delle chiavi di destinazione e quindi salvare le modifiche. 

In questa fase, gli stati del set di crittografia del disco e dell'insieme di credenziali delle chiavi vengono modificati in *Commit move pending*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Screenshot del riquadro per la preparazione di altre risorse." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Per eseguire il commit e completare il processo di spostamento per le risorse di crittografia, eseguire le operazioni seguenti:

1. In **Tra aree selezionare** la risorsa (set di crittografia del disco o insieme di credenziali delle chiavi) e quindi selezionare Commit move **(Esegui commit spostamento).**
1. In **Sposta risorse** selezionare **Commit.**

> [!NOTE]
> Dopo aver eseguito il commit dello spostamento, lo stato della risorsa cambia in *Elimina origine in sospeso.*


## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le macchine virtuali, il gruppo di risorse delle macchine virtuali deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Durante il processo di preparazione, Spostamento risorse genera modelli Azure Resource Manager (ARM) dalle impostazioni del gruppo di risorse. Le risorse all'interno del gruppo di risorse non sono interessate.

Per preparare lo spostamento del gruppo di risorse di origine, eseguire le operazioni seguenti:

1. In **Tra aree** selezionare il gruppo di risorse di origine e quindi selezionare **Prepara.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Screenshot del pulsante &quot;Prepara&quot; nel riquadro &quot;Prepara risorse&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. In **Preparare le risorse** selezionare **Prepara**.

> [!NOTE]
> Dopo aver preparato lo spostamento, lo stato del gruppo di risorse cambia in *Avvia spostamento in sospeso.* 

 
### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Iniziare a spostare il gruppo di risorse di origine seguendo questa procedura:

1. Nel riquadro **Tra aree** selezionare il gruppo di risorse e quindi selezionare **Avvia spostamento.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Screenshot del pulsante &quot;Avvia spostamento&quot; nel riquadro &quot;Tra aree&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. Nel riquadro **Sposta risorse** selezionare Avvia **spostamento.** Lo stato del gruppo di risorse cambia *in Avvio spostamento in corso.*   
1. Dopo aver avviato lo spostamento, viene creato il gruppo di risorse di destinazione, in base al modello DI RESOURCE generato. Lo stato del gruppo di risorse di origine cambia in *Commit spostamento in sospeso.*

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Screenshot del riquadro &quot;Sposta risorse&quot; che mostra lo stato del gruppo di risorse modificato in &quot;Commit spostamento in sospeso&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Per eseguire il commit dello spostamento e completare il processo, eseguire le operazioni seguenti:

1. Nel riquadro **Tra aree** selezionare il gruppo di risorse e quindi selezionare **Commit spostamento**.
1. Nel riquadro **Sposta risorse** selezionare **Commit**.

> [!NOTE]
> Dopo aver eseguito il commit dello spostamento, lo stato del gruppo di risorse di origine cambia in *Elimina origine in sospeso.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Screenshot del gruppo di risorse di origine che mostra lo stato modificato in &quot;Elimina origine in sospeso&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Preparare le risorse da spostare

Ora che le risorse di crittografia e il gruppo di risorse di origine vengono spostati, è possibile prepararsi a spostare altre risorse il cui stato corrente *è Prepara* in sospeso .


1. Nel riquadro **Tra aree** convalidare nuovamente lo spostamento e risolvere eventuali problemi.
1. Se si desidera modificare le impostazioni di destinazione prima di iniziare lo spostamento, selezionare il collegamento nella colonna **Configurazione** di destinazione per la risorsa e quindi modificare le impostazioni. Se si modificano le impostazioni della macchina virtuale di destinazione, le dimensioni della macchina virtuale di destinazione non devono essere inferiori a quelle della macchina virtuale di origine.
1. Per le risorse con *stato Prepara* in sospeso da spostare, selezionare **Prepara**.
1. Nel riquadro **Prepara risorse** selezionare **Prepara**.

    - Durante la preparazione, l Azure Site Recovery di mobilità viene installato nelle macchine virtuali per replicarle.
    - I dati della macchina virtuale vengono replicati periodicamente nell'area di destinazione. Tale operazione non ha effetto sulla macchina virtuale di origine.
    - Spostamento risorse genera modelli di Resource Manager per le altre risorse di origine.

> [!NOTE]
> Dopo aver preparato le risorse, lo stato cambia in *Avvia spostamento in sospeso.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Screenshot del riquadro &quot;Prepara risorse&quot;, che mostra le risorse nello stato &quot;Avvia spostamento in sospeso&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Avviare lo spostamento

Dopo aver preparato le risorse preparate, è possibile avviare lo spostamento. 

1. Nel riquadro **Tra aree** selezionare le risorse il cui stato è Avvia spostamento *in* sospeso e quindi selezionare **Avvia spostamento**.
1. Nel riquadro **Sposta risorse** selezionare Avvia **spostamento**.
1. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.

    - Nel caso delle macchine virtuali vengono create macchine virtuali di replica nell'area di destinazione. La macchina virtuale di origine viene arrestata e rimane inattiva per qualche minuto.
    - Resource Mover ri-crea altre risorse usando i modelli arm preparati. Questa operazione non comporta in genere tempi di inattività.
    - Dopo aver spostato le risorse, lo stato cambia in *Commit move pending (Commit spostamento in sospeso).*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Screenshot di un elenco di risorse con stato &quot;Commit spostamento in sospeso&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale, è possibile decidere se eseguire il commit dello spostamento o eliminarlo. 

- **Ignora:** è possibile rimuovere uno spostamento se lo si sta testando e non si vuole effettivamente spostare la risorsa di origine. Se si elimina lo spostamento, la risorsa viene impostata su Initiate move pending status *(Avvia spostamento in* sospeso).
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo aver eseguito il commit di una risorsa di origine, lo stato cambia in Elimina origine *in* sospeso ed è possibile decidere se eliminarla.


## <a name="discard-the-move"></a>Rimuovere lo spostamento 

Per annullare lo spostamento, eseguire le operazioni seguenti:

1. Nel riquadro **Tra aree** selezionare le risorse il cui stato è Commit spostamento *in sospeso* e quindi selezionare Ignora **spostamento**.
1. Nel riquadro **Rimuovi spostamento** selezionare **Ignora**.
1. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.


> [!NOTE]
> Dopo aver eliminato le risorse, lo stato della macchina virtuale cambia in *Avvio spostamento in sospeso.*

## <a name="commit-the-move"></a>Eseguire il commit dello spostamento

Per completare il processo di spostamento, eseguire il commit dello spostamento eseguendo le operazioni seguenti: 

1. Nel riquadro **Tra aree** selezionare le risorse il cui stato è Commit spostamento *in sospeso* e quindi selezionare Commit **spostamento**.
1. Nel riquadro **Commit resources (Esegui** commit risorse) selezionare **Commit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Screenshot di un elenco di risorse per il commit delle risorse per finalizzare lo spostamento." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.

> [!NOTE]
> - Dopo aver eseguito il commit dello spostamento, la replica delle macchine virtuali viene interrotta. La macchina virtuale di origine non è influenzata dal commit.
> - Il processo di commit non influisce sulle risorse di rete di origine.
> - Dopo aver eseguito il commit dello spostamento, lo stato della risorsa cambia in *Elimina origine in sospeso.*



## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

- Il servizio Mobility non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo installato se si prevede di spostare nuovamente il server.
- Modificare le regole di controllo degli accessi in base al ruolo di Azure dopo lo spostamento.

## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine. 

1. Nel riquadro **Tra aree** selezionare ogni risorsa di origine che si vuole eliminare e quindi selezionare **Elimina origine.**
1. In **Elimina origine** esaminare gli elementi che si intende eliminare e in Conferma **eliminazione** digitare **yes**. L'azione è irreversibile, quindi controllare attentamente.
1. Dopo aver digitato **sì,** selezionare **Elimina origine**.

> [!NOTE]
>  Nel portale di spostamento risorse non è possibile eliminare gruppi di risorse, insiemi di credenziali delle chiavi o istanze SQL Server risorse. È necessario eliminare ognuno singolarmente dalla pagina delle proprietà per ogni risorsa.


## <a name="delete-resources-that-you-created-for-the-move"></a>Eliminare le risorse create per lo spostamento

Dopo lo spostamento, è possibile eliminare manualmente la raccolta di spostamento e Site Recovery risorse create durante questo processo.

- La raccolta di spostamento è nascosta per impostazione predefinita. Per visualizzarla, è necessario attivare le risorse nascoste.
- Prima di eliminare l'archiviazione cache è necessario eliminare il relativo blocco.

Per eliminare le risorse, eseguire le operazioni seguenti: 
1. Individuare le risorse nel gruppo di risorse ```RegionMoveRG-<sourceregion>-<target-region>```.
1. Verificare che tutte le macchine virtuali e le altre risorse di origine nell'area di origine siano state spostate o eliminate. Questo passaggio garantisce che non siano presenti risorse in sospeso che le usano.
1. Eliminare le risorse seguenti:

    - Spostare il nome della raccolta: ```movecollection-<sourceregion>-<target-region>```
    - Nome dell'account di archiviazione della cache: ```resmovecache<guid>```
    - Nome dell'insieme di credenziali: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Le macchine virtuali di Azure crittografate e le relative risorse dipendenti sono stati spostati in un'altra area di Azure.


Come passaggio successivo, provare a spostare Azure SQL database e pool elastici in un'altra area.

> [!div class="nextstepaction"]
> [Spostare le risorse SQL di Azure](./tutorial-move-region-sql.md)
