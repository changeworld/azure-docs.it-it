---
title: Spostare macchine virtuali di Azure tra aree con Spostamento risorse di Azure
description: Informazioni su come spostare macchine virtuali di Azure in un'altra area con Spostamento risorse di Azure
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d1ac17c93bdf95e36f68af678d2ee38b896ef1e7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979743"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Esercitazione: Spostare macchine virtuali tra aree

Questo articolo illustra come spostare macchine virtuali di Azure e risorse di rete/archiviazione correlate in un'area di Azure diversa, usando [Spostamento risorse di Azure](overview.md).
.


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare prerequisiti e requisiti.
> * Selezionare le risorse da spostare.
> * Risolvere le dipendenze delle risorse.
> * Preparare e spostare il gruppo di risorse di origine. 
> * Preparare e spostare le altre risorse.
> * Decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 
> * Facoltativamente, rimuovere le risorse nell'area di origine dopo lo spostamento.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare. Accedere quindi al [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti
**Requisito** | **Descrizione**
--- | ---
**Autorizzazioni di sottoscrizione** | Verificare di disporre dell'accesso *proprietario* per la sottoscrizione contenente le risorse che si desidera spostare<br/><br/> **Perché è necessario l'accesso del proprietario?** La prima volta che si aggiunge una risorsa per una coppia di origine e destinazione specifica in una sottoscrizione di Azure, Spostamento risorse crea un'[identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (nota in precedenza come identità del servizio gestita) che viene considerata attendibile dalla sottoscrizione. Per creare l'identità e assegnarle il ruolo richiesto (Collaboratore o Amministratore Accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere le risorse deve avere le autorizzazioni di *Proprietario* nella sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
**Supporto per macchine virtuali** |  Controllare che le macchine virtuali da spostare siano supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le VM Windows supportate.<br/><br/> - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le VM Linux e le versioni del kernel supportate.<br/><br/> -Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
**Sottoscrizione di destinazione** | La sottoscrizione nell'area di destinazione richiede una quota sufficiente per creare le risorse che si stanno muovendo nell'area di destinazione. Se non è disponibile alcuna quota, [richiedere limiti aggiuntivi](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Addebiti per l'area di destinazione** | Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le macchine virtuali. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).
    

## <a name="prepare-vms"></a>Preparare le macchine virtuali

1. Dopo aver verificato che le macchine virtuali soddisfino i requisiti, assicurarsi che le macchine virtuali che si desidera spostare siano attivate. Tutti i dischi delle macchine virtuali che si desidera siano disponibili nell'area di destinazione devono essere collegati e inizializzati nella macchina virtuale.
1. Assicurarsi che per le macchine virtuali siano disponibili i certificati radice trusted più recenti e un elenco di revoche di certificati aggiornato. Per eseguire questa operazione:
    - Nelle macchine virtuali Windows installare gli ultimi aggiornamenti di Windows.
    - Nelle macchine virtuali Linux seguire le indicazioni fornite dal distributore in modo nelle macchine siano disponibili i certificati e l'elenco di revoche di certificati più recenti. 
1. Consentire la connettività in uscita dalle macchine virtuali:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi [URL](support-matrix-move-region-azure-vm.md#url-access)
    - Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, creare queste [regole del tag di servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Selezionare le risorse 

Selezionare le risorse da spostare.

- Vengono visualizzati tutti i tipi di risorse supportati presenti nei gruppi di risorse all'interno dell'area di origine selezionata.
- Le risorse che sono già state aggiunte per lo spostamento tra aree non vengono visualizzate.
- Le risorse vengono spostate in un'area di destinazione nella stessa sottoscrizione dell'area di origine. Se si vuole cambiare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

1. Nel portale di Azure cercare *spostamento risorse*. In **Servizi** selezionare quindi **Spostamento risorse di Azure**.

    ![Risultati della ricerca di spostamento risorse nel portale di Azure](./media/tutorial-move-region-virtual-machines/search.png)

2. In **Panoramica** fare clic su **Attività iniziali**.

    ![Pulsante per aggiungere le risorse da spostare in un'altra area](./media/tutorial-move-region-virtual-machines/get-started.png)

3. In **Sposta risorse** > **Origine e destinazione** selezionare l'area e la sottoscrizione di origine.
4. In **Destinazione** selezionare l'area in cui spostare le macchine virtuali. Quindi fare clic su **Next**.

    ![Pagina per la selezione dell'area di origine e di destinazione](./media/tutorial-move-region-virtual-machines/source-target.png)

6. In **Risorse da spostare** fare clic su **Selezionare le risorse**.
7. In **Selezionare le risorse** selezionare la macchina virtuale. È possibile aggiungere solo le [risorse supportate per lo spostamento](#prepare-vms). Fare quindi clic su **Done**.

    ![Pagina per selezionare le macchine virtuali da spostare](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  In **Risorse da spostare** fare clic su **Avanti**.
9. In verifica verificare le impostazioni di origine **e di destinazione**. 

    ![Pagina per rivedere le impostazioni e procedere allo spostamento](./media/tutorial-move-region-virtual-machines/review.png)
10. Per iniziare ad aggiungere le risorse, fare clic su **Continua**.
11. Dopo il completamento del processo di aggiunta fare clic su **Aggiunta di risorse per lo spostamento** nell'icona di notifica.
12. Dopo aver fatto clic sulla notifica, esaminare le risorse nella pagina **Tra aree**.

> [!NOTE]
> - Le risorse aggiunte si trovano nello stato *Preparazione in sospeso*.
> - Il gruppo di risorse per le VM viene aggiunto automaticamente.
> - Il metodo per rimuovere una risorsa da una raccolta di spostamento dipende da punto in cui ci si trova nel processo di spostamento. [Altre informazioni](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

1. Se per le risorse viene visualizzato un messaggio *Convalida dipendenze* nella colonna **Problemi**, fare clic sul pulsante **Convalida dipendenze**. Viene avviato il processo di convalida.
2. Se vengono trovate dipendenze, fare clic su **Aggiungi dipendenze**. 
3. In **Aggiungi dipendenze** lasciare l'opzione predefinita **Mostra tutte le dipendenze** .

    - Mostra tutte le dipendenze esegue l'iterazione in tutte le dipendenze dirette e indirette per una risorsa. Per una macchina virtuale, ad esempio, vengono visualizzati la scheda NIC, la rete virtuale, i gruppi di sicurezza di rete (gruppi) e così via.
    - Mostra dipendenze di primo livello Mostra solo le dipendenze dirette. Per una macchina virtuale, ad esempio, viene visualizzata la scheda di interfaccia di rete, ma non la rete virtuale.


4. Selezionare le risorse dipendenti che si desidera aggiungere > **Aggiungi dipendenze**. Monitorare lo stato di avanzamento nelle notifiche.

    ![Aggiungere le dipendenze](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Convalidare di nuovo le dipendenze. 
    ![Pagina per aggiungere altre dipendenze](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le macchine virtuali, il gruppo di risorse delle macchine virtuali deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Durante il processo di preparazione Spostamento risorse genera modelli di Azure Resource Manager usando le impostazioni del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non sono interessate.

Eseguire la preparazione nel modo seguente:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Prepara**.
2. In **Prepara risorse** fare clic su **Prepara**.

    ![Preparare il gruppo di risorse](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Dopo la preparazione il gruppo di risorse si trova nello stato *Avvio spostamento in sospeso*. 

 
### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Avviare lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**. Il gruppo di risorse passa allo stato *Avvio spostamento in corso*.
3. Dopo l'avvio dello spostamento, viene creato il gruppo di risorse di destinazione in base al modello di Resource Manager generato. Il gruppo di risorse di origine passa allo stato *Commit spostamento in sospeso*.

    ![Fare clic sul pulsante Avvia spostamento](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Per eseguire il commit e terminare il processo di spostamento:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Commit spostamento**.
2. In **Sposta risorse** fare clic su **Commit**.

> [!NOTE]
> Dopo il commit dello spostamento il gruppo di risorse di origine si trova nello stato *Eliminazione origine in sospeso*.

## <a name="prepare-resources-to-move"></a>Preparare le risorse da spostare

Ora che il gruppo di risorse di origine è stato spostato, è possibile prepararsi per spostare altre risorse che si trovano nello stato di *preparazione in sospeso* .

1. In **aree diverse** verificare che le risorse siano ora in stato di *preparazione in sospeso* , senza problemi. In caso contrario, eseguire di nuovo la convalida e risolvere eventuali problemi in sospeso.

    ![Pagina che mostra le risorse nello stato Preparazione in sospeso](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Se si vogliono modificare le impostazioni relative alla destinazione prima di avviare lo spostamento, selezionare il collegamento nella colonna **Configurazione della destinazione** per la risorsa e modificare le impostazioni. Se si modificano le impostazioni della macchina virtuale di destinazione, le dimensioni della macchina virtuale di destinazione non devono essere inferiori a quelle della macchina virtuale di origine.  

Dopo aver spostato il gruppo di risorse di origine è possibile preparare lo spostamento delle altre risorse.

3. Selezionare le risorse da preparare. 

    ![Pagina per selezionare le altre risorse da preparare](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Selezionare **Prepara**. 

> [!NOTE]
> - Durante il processo di preparazione nelle macchine virtuali viene installato l'agente del servizio di mobilità di Azure Site Recovery per consentirne la replica.
> - I dati delle macchine virtuali vengono replicati periodicamente nell'area di destinazione. Tale operazione non ha effetto sulla macchina virtuale di origine.
> - Spostamento risorse genera modelli di Resource Manager per le altre risorse di origine.
> - Dopo la preparazione le risorse si trovano nello stato *Avvio spostamento in sospeso*.

![Pagina che mostra le risorse nello stato Avvio spostamento in sospeso](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Avviare lo spostamento

Dopo aver preparato le risorse è ora possibile avviare lo spostamento. 

1. In **Tra aree** selezionare le risorse con lo stato *Avvio spostamento in sospeso*. Fare quindi clic su **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**.

    ![Fare clic sul pulsante Avvia spostamento](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.

> [!NOTE]
> - Nel caso delle macchine virtuali vengono create macchine virtuali di replica nell'area di destinazione. La macchina virtuale di origine viene arrestata e rimane inattiva per qualche minuto.
> - Spostamento risorse ricrea altre risorse usando i modelli di Resource Manager che sono stati preparati. Questa operazione non comporta in genere tempi di inattività.
> - Dopo lo spostamento le risorse si trovano in uno stato *Commit spostamento in sospeso*.

![Pagina che mostra le risorse nello stato *Eliminazione origine in sospeso*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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

    ![Pagina per eseguire il commit delle risorse e finalizzare lo spostamento](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.

> [!NOTE]
> - Dopo il commit dello spostamento la replica delle macchine virtuali viene arrestata. Il commit non influisce sulla macchina virtuale di origine.
> - Il commit non influisce sulle risorse di rete di origine.
> - Dopo il commit dello spostamento le risorse si trovano nello stato *Eliminazione origine in sospeso*.

![Pagina che mostra le risorse nello stato *Eliminazione origine in sospeso*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

- Il servizio di mobilità non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo installato se si prevede di spostare nuovamente il server.
- Modificare le regole di controllo degli accessi in base al ruolo di Azure dopo lo spostamento.


## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine. 

> [!NOTE]
> Alcune risorse, ad esempio insiemi di credenziali delle chiavi e server SQL Server, non possono essere eliminate dal portale e devono essere eliminate dalla pagina delle proprietà della risorsa.

1. In **aree diverse** fare clic sul nome della risorsa di origine che si desidera eliminare.
2. Selezionare **Elimina origine**.

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
> * Le macchine virtuali di Azure sono state spostate in un'altra area di Azure.
> * Le risorse associate alle macchine virtuali sono state spostate in un'altra area.

A questo punto provare a spostare pool elastici e database SQL di Azure in un'altra area.

> [!div class="nextstepaction"]
> [Spostare le risorse SQL di Azure](./tutorial-move-region-sql.md)
