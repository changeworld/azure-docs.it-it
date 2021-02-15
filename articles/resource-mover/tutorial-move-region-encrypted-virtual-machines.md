---
title: Spostare le macchine virtuali di Azure crittografate tra le aree con Azure Resource Mover
description: Informazioni su come spostare le macchine virtuali di Azure crittografate in un'altra area con Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361010"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Esercitazione: spostare macchine virtuali di Azure crittografate tra le aree

Questo articolo illustra come spostare le macchine virtuali di Azure crittografate in un'area di Azure diversa usando il [motore di risorse di Azure](overview.md). Ecco cosa si intende per crittografia:

- Macchine virtuali con dischi con crittografia dischi di Azure abilitata. [Scopri di più](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- In alternativa, le macchine virtuali che usano chiavi gestite dal cliente (CMK) per la crittografia inattiva (crittografia lato server). [Scopri di più](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare i prerequisiti. 
> * Per le macchine virtuali con crittografia dischi di Azure abilitata, copiare le chiavi e i segreti dall'insieme di credenziali delle chiavi nell'area di destinazione.
> * Preparare le macchine virtuali per spostarle e selezionare le risorse nell'area di origine che si desidera spostare.
> * Risolvere le dipendenze delle risorse.
> * Per le macchine virtuali con crittografia dischi di Azure abilitata, assegnare manualmente l'insieme di credenziali delle chiavi di destinazione. Per le macchine virtuali che usano la crittografia lato server con chiavi gestite dal cliente, assegnare manualmente un set di crittografia del disco nell'area di destinazione.
> * Spostare l'insieme di credenziali delle chiavi e/o il set di crittografia del disco.
> * Preparare e spostare il gruppo di risorse di origine. 
> * Preparare e spostare le altre risorse.
> * Decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 
> * Facoltativamente, rimuovere le risorse nell'area di origine dopo lo spostamento.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

**Requisito** |**Dettagli**
--- | ---
**Autorizzazioni di sottoscrizione** | Verificare di avere accesso come *Proprietario* nella sottoscrizione che contiene le risorse da spostare.<br/><br/> **Perché è necessario l'accesso del proprietario?** La prima volta che si aggiunge una risorsa per una coppia di origine e destinazione specifica in una sottoscrizione di Azure, Spostamento risorse crea un'[identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (nota in precedenza come identità del servizio gestita) che viene considerata attendibile dalla sottoscrizione. Per creare l'identità e assegnarle il ruolo richiesto (collaboratore e amministratore accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere risorse richiede le autorizzazioni di *proprietario* per la sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
**Supporto per macchine virtuali** | Controllare che le macchine virtuali da spostare siano supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le VM Windows supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le VM Linux e le versioni del kernel supportate.<br/><br/> -Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
**Requisiti di Key Vault (crittografia dischi di Azure)** | Se crittografia dischi di Azure è abilitata per le macchine virtuali, oltre all'insieme di credenziali delle chiavi nell'area di origine, è necessario un insieme di credenziali delle chiavi nell'area di destinazione. [Creare un insieme di](../key-vault/general/quick-create-portal.md)credenziali delle chiavi.<br/><br/> Per gli insiemi di credenziali delle chiavi nell'area di origine e di destinazione, sono necessarie le autorizzazioni seguenti:<br/><br/> -Autorizzazioni per chiavi: operazioni di gestione delle chiavi (Get, List); Operazioni di crittografia (decrittografia e crittografia).<br/><br/> -Autorizzazioni segrete: operazioni di gestione dei segreti (Get, List e set)<br/><br/> -Certificate (List e Get).
**Crittografia del disco impostata (crittografia lato server con CMK)** | Se si usano macchine virtuali con la crittografia lato server usando un CMK, oltre alla crittografia del disco impostata nell'area di origine, è necessario un set di crittografia del disco nell'area di destinazione. [Creare un set di crittografia del disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Il passaggio tra le aree non è supportato se si usano chiavi HSM per le chiavi gestite dal cliente.
**Quota area di destinazione** | La quota disponibile nella sottoscrizione deve essere sufficiente per creare le risorse che si intende spostare nell'area di destinazione. Se non è disponibile alcuna quota, [richiedere limiti aggiuntivi](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Addebiti per l'area di destinazione** | Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le macchine virtuali. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Verificare le autorizzazioni utente nell'insieme di credenziali delle chiavi per le macchine virtuali con crittografia dischi di Azure (ADE)

Se si stanno migrando macchine virtuali con crittografia dischi di Azure abilitata, è necessario eseguire uno script come indicato di [seguito](#copy-the-keys-to-the-destination-key-vault) per il quale l'utente che esegue lo script deve disporre delle autorizzazioni appropriate. Per conoscere le autorizzazioni necessarie, fare riferimento alla tabella seguente. Le opzioni per modificare le autorizzazioni sono disponibili passando all'insieme di credenziali delle chiavi nella portale di Azure, in **Impostazioni**, selezionare criteri di **accesso**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Pulsante per aprire i criteri di accesso dell'insieme di credenziali delle chiavi." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Se non sono disponibili autorizzazioni utente, selezionare **Aggiungi criteri di accesso** e specificare le autorizzazioni. Se l'account utente dispone già di un criterio, in **utente** impostare le autorizzazioni in base alla tabella seguente.

Le macchine virtuali di Azure che usano ADE possono avere le seguenti varianti e le autorizzazioni devono essere impostate di conseguenza per i componenti pertinenti.
- Opzione predefinita in cui il disco è crittografato usando solo segreti
- Sicurezza aggiunta tramite [chiave di crittografia della chiave](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Insieme di credenziali delle credenziali dell'area di origine

È necessario impostare le autorizzazioni seguenti per l'utente che esegue lo script 

**Componente** | **Autorizzazione necessaria**
--- | ---
Segreti|  Ottenere l'autorizzazione <br> </br> In **Secret Permissions** >   **Secret Operation Management** selezionare **Get** 
Chiavi <br> </br> Se si usa la chiave di crittografia della chiave (KEK), è necessaria questa autorizzazione oltre ai segreti| Autorizzazione Get e Decrypt <br> </br> Nelle operazioni di gestione delle chiavi di **autorizzazioni chiave**  >  selezionare **Get**. In **operazioni crittografiche** selezionare **Decrypt**.

### <a name="destination-region-keyvault"></a>Insieme di credenziali delle credenziali dell'area di destinazione

In **criteri di accesso**, assicurarsi che **crittografia dischi di Azure per la crittografia del volume** sia abilitata. 

È necessario impostare le autorizzazioni seguenti per l'utente che esegue lo script 

**Componente** | **Autorizzazione necessaria**
--- | ---
Segreti|  Imposta autorizzazione <br> </br> In **Secret Permissions** >   **Secret Operation Management**, Select **set** 
Chiavi <br> </br> Se si usa la chiave di crittografia della chiave (KEK), è necessaria questa autorizzazione oltre ai segreti| Ottenere, creare e crittografare l'autorizzazione <br> </br> Nelle operazioni di gestione delle chiavi di **autorizzazioni chiave**  >  selezionare **Get** e **create** . In **operazioni di crittografia** selezionare **Crittografa**.

Oltre alle autorizzazioni sopra riportate, nell'insieme di credenziali delle chiavi di destinazione è necessario aggiungere le autorizzazioni per l' [identità del sistema gestito](./common-questions.md#how-is-managed-identity-used-in-resource-mover) usato da Mover per accedere alle risorse di Azure per conto dell'utente. 

1. In **Impostazioni** selezionare **Aggiungi criteri di accesso**. 
2. In **Seleziona entità** cercare il file MSI. Il nome MSI è ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. Aggiungere le autorizzazioni seguenti per il file MSI

**Componente** | **Autorizzazione necessaria**
--- | ---
Segreti|  Autorizzazione Get ed List <br> </br> In **Secret Permissions** >   **Secret Operation Management** selezionare **Get** ed **List** 
Chiavi <br> </br> Se si usa la chiave di crittografia della chiave (KEK), è necessaria questa autorizzazione oltre ai segreti| Get, elenco delle autorizzazioni <br> </br> In operazioni di gestione delle chiavi di **autorizzazioni chiave**  >  selezionare **Get** ed **elenco**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copiare le chiavi nell'insieme di credenziali delle chiavi di destinazione

È necessario copiare i segreti e le chiavi di crittografia dall'insieme di credenziali delle chiavi di origine all'insieme di credenziali delle chiavi di destinazione usando uno script fornito.

- Eseguire lo script in PowerShell. È consigliabile eseguire la versione più recente di PowerShell.
- In particolare, lo script richiede i moduli seguenti:
    - Az.Compute
    - AZ. tovault (versione 3.0.0
    - AZ. Accounts (versione 2.2.3)

Eseguire come indicato di seguito:

1. Passare allo [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) in GitHub.
2. Copiare il contenuto dello script in un file locale e denominarlo *Copy-keys.ps1*.
3. Eseguire lo script.
4. Accedere ad Azure.
5. Nella finestra popup **input utente** selezionare la sottoscrizione di origine, il gruppo di risorse e la macchina virtuale di origine. Selezionare quindi il percorso di destinazione e gli insiemi di credenziali di destinazione per la crittografia del disco e della chiave.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Popup nei valori di script di input." :::


6. Al termine dello script, l'output dello schermo indica che CopyKeys è riuscito.

## <a name="prepare-vms"></a>Preparare le macchine virtuali

1. Dopo aver [verificato che le macchine virtuali soddisfino i requisiti](#prerequisites), assicurarsi che le macchine virtuali che si desidera spostare siano attivate. Tutti i dischi delle macchine virtuali che si desidera siano disponibili nell'area di destinazione devono essere collegati e inizializzati nella macchina virtuale.
3. Verificare che le macchine virtuali dispongano dei certificati radice attendibili più recenti e un elenco di revoche di certificati (CRL) aggiornato. Per eseguire questa operazione:
    - Nelle macchine virtuali Windows installare gli ultimi aggiornamenti di Windows.
    - Nelle macchine virtuali Linux seguire le indicazioni fornite dal distributore in modo nelle macchine siano disponibili i certificati e l'elenco di revoche di certificati più recenti. 
4. Consenti connettività in uscita dalle macchine virtuali come indicato di seguito:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi [URL](support-matrix-move-region-azure-vm.md#url-access)
    - Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, creare queste [regole del tag di servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selezionare le risorse da spostare


- È possibile selezionare qualsiasi tipo di risorsa supportato in qualsiasi gruppo di risorse nell'area di origine selezionata.  
- Si spostano le risorse in un'area di destinazione che si trova nella stessa sottoscrizione dell'area di origine. Se si vuole cambiare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

Selezionare le risorse come segue:

1. Nel portale di Azure cercare *spostamento risorse*. In **Servizi** selezionare quindi **Spostamento risorse di Azure**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Risultati della ricerca per il motore di risorse nell'portale di Azure." :::

2. In **Panoramica** fare clic su **sposta tra le aree**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Per aggiungere risorse per lo spostamento in un'altra area." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. In **Sposta risorse** > **Origine e destinazione** selezionare l'area e la sottoscrizione di origine.
4. In **Destinazione** selezionare l'area in cui spostare le macchine virtuali. Quindi fare clic su **Next**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Per selezionare l'area di origine e di destinazione." :::

5. In **Risorse da spostare** fare clic su **Selezionare le risorse**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Pulsante per selezionare la risorsa da spostare.]." :::

6. In **Seleziona risorse** selezionare le macchine virtuali. È possibile aggiungere solo le risorse [supportate per lo spostamento](#prepare-vms). Fare quindi clic su **Done**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Per selezionare le macchine virtuali da spostare." :::

    > [!NOTE]
    >  In questa esercitazione viene selezionata una macchina virtuale che usa la crittografia lato server (Rayne-VM) con una chiave gestita dal cliente e una macchina virtuale con crittografia del disco abilitata (Rayne-VM-ADE).

7.  In **Risorse da spostare** fare clic su **Avanti**.
8. In verifica verificare le impostazioni di origine **e di destinazione**. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Per rivedere le impostazioni e procedere con lo spostamento." :::

9. Per iniziare ad aggiungere le risorse, fare clic su **Continua**.
10. Selezionare l'icona notifiche per tenere traccia dello stato di avanzamento. Una volta completato il processo di aggiunta, selezionare **risorse aggiunte per lo spostamento** nelle notifiche.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="La notifica per confermare le risorse è stata aggiunta." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Dopo aver fatto clic sulla notifica, esaminare le risorse nella pagina **Tra aree**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Pagine che mostrano le risorse aggiunte con la preparazione in sospeso." :::

> [!NOTE]
> - Le risorse aggiunte vengono inserite in uno stato di *preparazione in sospeso* .
> - Il gruppo di risorse per le VM viene aggiunto automaticamente.
> - Se si modificano le voci di **configurazione di destinazione** in modo da usare una risorsa già esistente nell'area di destinazione, lo stato della risorsa viene impostato su *commit in sospeso*, perché non è necessario avviare uno spostamento per esso.
> - Se si vuole rimuovere una risorsa che è stata aggiunta, il metodo per eseguire questa operazione dipende dalla posizione in cui si trova il processo di spostamento. [Altre informazioni](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

1. Se una risorsa Mostra un messaggio di *convalida delle dipendenze* nella colonna **problemi** , selezionare il pulsante **convalida dipendenze** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Npulsante per verificare le dipendenze." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Viene avviato il processo di convalida.
2. Se vengono trovate dipendenze, fare clic su **Aggiungi dipendenze**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Pulsante per l'aggiunta di dipendenze." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. In **Aggiungi dipendenze** lasciare l'opzione predefinita **Mostra tutte le dipendenze** .

    - **Mostra tutte le dipendenze** esegue l'iterazione in tutte le dipendenze dirette e indirette per una risorsa. Per una macchina virtuale, ad esempio, vengono visualizzati la scheda NIC, la rete virtuale, i gruppi di sicurezza di rete (gruppi) e così via.
    - **Mostra dipendenze di primo livello** Mostra solo le dipendenze dirette. Per una macchina virtuale, ad esempio, viene visualizzata la scheda di interfaccia di rete, ma non la rete virtuale.
 
4. Selezionare le risorse dipendenti che si desidera aggiungere > **Aggiungi dipendenze**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Selezionare dipendenze dall'elenco dipendenze." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Convalidare di nuovo le dipendenze. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Per eseguire di nuovo la convalida." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Assegnare le risorse di destinazione

Per le risorse di destinazione associate alla crittografia è necessaria l'assegnazione manuale.

- Se si sta migrando una VM con crittografia dischi di Azure, l'insieme di credenziali delle chiavi nell'area di destinazione verrà visualizzato come dipendenza.
- Se si sta migrando una macchina virtuale con crittografia lato server che usa chiavi gestite personalizzate (CMK), la crittografia del disco impostata nell'area di destinazione viene visualizzata come dipendenza. 
- Poiché questa esercitazione sta migrando una VM con ADE abilitato e una macchina virtuale che usa un CMK, l'insieme di credenziali delle chiavi di destinazione e il set di crittografia del disco vengono visualizzati come dipendenze.

Assegnare manualmente come segue:

1. Nella voce del set di crittografia del disco selezionare **risorsa non assegnata** nella colonna **configurazione di destinazione** .
2. In **impostazioni di configurazione** selezionare il set di crittografia del disco di destinazione. Quindi selezionare **Salva modifiche**.
3. È possibile scegliere di salvare e convalidare le dipendenze per la risorsa che si sta modificando. in alternativa, è possibile salvare le modifiche e convalidare tutti gli elementi modificati in un'unica operazione.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Per selezionare la crittografia del disco impostata nell'area di destinazione." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Dopo aver aggiunto la risorsa di destinazione, lo stato del set di crittografia del disco si *Sposta su commit in sospeso*.
3. Nella voce dell'insieme di credenziali delle chiavi selezionare **risorsa non assegnata** nella colonna **configurazione di destinazione** . **Impostazioni di configurazione** selezionare l'insieme di credenziali delle chiavi di destinazione. Salvare le modifiche. 

In questa fase sia la crittografia del disco che lo stato dell'insieme di credenziali delle chiavi si *spostano su commit in sospeso*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Per selezionare prepara per altre risorse." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Per eseguire il commit e terminare il processo di spostamento per le risorse di crittografia.

1. In **aree diverse** selezionare la risorsa (set di crittografia del disco o Key vault) > **Sposta commit**.
2. In **Sposta risorse** fare clic su **Commit**.

> [!NOTE]
> Dopo aver eseguito il commit dello spostamento, la risorsa si trova in uno stato di *eliminazione origine in sospeso* .


## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le macchine virtuali, il gruppo di risorse delle macchine virtuali deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Durante il processo di preparazione Spostamento risorse genera modelli di Azure Resource Manager usando le impostazioni del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non sono interessate.

Eseguire la preparazione nel modo seguente:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Prepara**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Preparare il gruppo di risorse." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. In **Prepara risorse** fare clic su **Prepara**.

> [!NOTE]
> Dopo la preparazione il gruppo di risorse si trova nello stato *Avvio spostamento in sospeso*. 

 
### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Avviare lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Avvia spostamento**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Pulsante per avviare lo spostamento." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. In **Sposta risorse** fare clic su **Avvia spostamento**. Il gruppo di risorse passa allo stato *Avvio spostamento in corso*.   
3. Dopo l'avvio dello spostamento, viene creato il gruppo di risorse di destinazione in base al modello di Resource Manager generato. Il gruppo di risorse di origine passa allo stato *Commit spostamento in sospeso*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Esaminare lo stato di spostamento commit in sospeso." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Per eseguire il commit e terminare il processo di spostamento:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Commit spostamento**.
2. In **Sposta risorse** fare clic su **Commit**.

> [!NOTE]
> Dopo il commit dello spostamento il gruppo di risorse di origine si trova nello stato *Eliminazione origine in sospeso*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Esaminare lo stato di eliminazione dello spostamento in sospeso." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Preparare le risorse da spostare

Ora che le risorse di crittografia e il gruppo di risorse di origine vengono spostati, è possibile preparare lo spostamento di altre risorse nello stato di *preparazione in sospeso* .


1. In **aree diverse**, eseguire di nuovo la convalida e risolvere eventuali problemi.
2. Se si vogliono modificare le impostazioni relative alla destinazione prima di avviare lo spostamento, selezionare il collegamento nella colonna **Configurazione della destinazione** per la risorsa e modificare le impostazioni. Se si modificano le impostazioni della macchina virtuale di destinazione, le dimensioni della macchina virtuale di destinazione non devono essere inferiori a quelle della macchina virtuale di origine.
3. Selezionare **prepara** per le risorse nello stato di *preparazione in sospeso* che si desidera spostare.
3. In **preparare le risorse** selezionare **prepara**

    - Durante il processo di preparazione nelle macchine virtuali viene installato l'agente del servizio di mobilità di Azure Site Recovery per consentirne la replica.
    - I dati delle macchine virtuali vengono replicati periodicamente nell'area di destinazione. Tale operazione non ha effetto sulla macchina virtuale di origine.
    - Spostamento risorse genera modelli di Resource Manager per le altre risorse di origine.

Dopo la preparazione le risorse si trovano nello stato *Avvio spostamento in sospeso*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Pagina che mostra le risorse nello stato di inizio spostamento in sospeso." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Avviare lo spostamento

Dopo aver preparato le risorse è ora possibile avviare lo spostamento. 

1. In **Tra aree** selezionare le risorse con lo stato *Avvio spostamento in sospeso*. Fare quindi clic su **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**.
3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.

    - Nel caso delle macchine virtuali vengono create macchine virtuali di replica nell'area di destinazione. La macchina virtuale di origine viene arrestata e rimane inattiva per qualche minuto.
    - Spostamento risorse ricrea altre risorse usando i modelli di Resource Manager che sono stati preparati. Questa operazione non comporta in genere tempi di inattività.
    - Dopo lo spostamento le risorse si trovano in uno stato *Commit spostamento in sospeso*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Pagina che mostra le risorse in uno stato di spostamento in sospeso di commit." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato *Avvio spostamento in sospeso*.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato *Eliminazione origine in sospeso* e sarà possibile decidere se eliminarla.


## <a name="discard-the-move"></a>Rimuovere lo spostamento 

È possibile rimuovere lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare le risorse con lo stato *Commit spostamento in sospeso* e fare clic su **Rimuovi spostamento**.
2. In **Rimuovi spostamento** fare clic su **Rimuovi**.
3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.


> [!NOTE]
> Dopo la rimozione delle risorse, le macchine virtuali si trovano nello stato *Avvio spostamento in sospeso*.

## <a name="commit-the-move"></a>Eseguire il commit dello spostamento

Se si vuole completare il processo di spostamento, eseguire il commit dello spostamento. 

1. In **Tra aree** selezionare le risorse con lo stato *Commit spostamento in sospeso* e fare clic su **Commit spostamento**.
2. In **Commit delle risorse** fare clic su **Commit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Pagina in cui eseguire il commit delle risorse per finalizzare lo spostamento." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.

> [!NOTE]
> - Dopo il commit dello spostamento la replica delle macchine virtuali viene arrestata. Il commit non influisce sulla macchina virtuale di origine.
> - Il commit non influisce sulle risorse di rete di origine.
> - Dopo il commit dello spostamento le risorse si trovano nello stato *Eliminazione origine in sospeso*.



## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

- Il servizio di mobilità non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo installato se si prevede di spostare nuovamente il server.
- Modificare le regole di controllo degli accessi in base al ruolo di Azure dopo lo spostamento.

## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine. 

1. In **aree diverse** selezionare ogni risorsa di origine che si desidera eliminare. Selezionare quindi **Elimina origine**.
2. In **Elimina origine** esaminare gli elementi che si intende eliminare e, in **Conferma eliminazione**, digitare **Sì**. L'azione è irreversibile, quindi controllare con attenzione.
3. Dopo aver digitato **Sì**, selezionare **Elimina origine**.

> [!NOTE]
>  Nel portale di spostamento delle risorse non è possibile eliminare gruppi di risorse, insiemi di credenziali delle chiavi o server SQL Server. È necessario eliminarli singolarmente dalla pagina delle proprietà per ogni risorsa.


## <a name="delete-additional-resources-created-for-move"></a>Eliminare le risorse aggiuntive create per lo spostamento

Dopo lo spostamento è possibile eliminare manualmente la raccolta di spostamento e le risorse di Site Recovery create.

- La raccolta di spostamento è nascosta per impostazione predefinita. Per visualizzarla, è necessario attivare le risorse nascoste.
- Prima di eliminare l'archiviazione cache è necessario eliminare il relativo blocco.

La procedura di eliminazione è la seguente: 
1. Individuare le risorse nel gruppo di risorse ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Controllare che tutte le macchine virtuali e le altre risorse di origine nell'area di origine siano state spostate o eliminate, per assicurarsi che non vi siano risorse in sospeso che le usano.
2. Eliminare le risorse seguenti:

    - Il nome della raccolta di spostamento è ```movecollection-<sourceregion>-<target-region>```.
    - Il nome dell'account di archiviazione cache è ```resmovecache<guid>```.
    - Il nome dell'insieme di credenziali è ```ResourceMove-<sourceregion>-<target-region>-GUID```.
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state spostate le VM di Azure crittografate e le risorse dipendenti in un'altra area di Azure.


A questo punto provare a spostare pool elastici e database SQL di Azure in un'altra area.

> [!div class="nextstepaction"]
> [Spostare le risorse SQL di Azure](./tutorial-move-region-sql.md)
