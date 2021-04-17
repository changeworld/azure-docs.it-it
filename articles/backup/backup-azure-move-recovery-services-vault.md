---
title: Come spostare gli insiemi Backup di Azure insiemi di credenziali di Servizi di ripristino
description: Istruzioni su come spostare un insieme di credenziali di Servizi di ripristino tra sottoscrizioni di Azure e gruppi di risorse.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 49d6782af5a9c946eaf92147dab22e4605195d89
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514768"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Spostare un insieme di credenziali di Servizi di ripristino tra sottoscrizioni di Azure e gruppi di risorse

Questo articolo illustra come spostare un insieme di credenziali di Servizi di ripristino configurato per Backup di Azure tra sottoscrizioni di Azure o in un altro gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale di Azure o PowerShell per spostare un insieme di credenziali di Servizi di ripristino.

## <a name="supported-regions"></a>Aree supportate

Tutte le aree pubbliche e sovrane sono supportate, ad eccezione di Francia centrale, Francia meridionale, Germania nord-orientale, Germania centrale, Cina settentrionale, Cina settentrionale2, Cina orientale e Cina orientale2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Prerequisiti per lo spostamento dell'insieme di credenziali di Servizi di ripristino

- Durante lo spostamento dell'insieme di credenziali tra gruppi di risorse, i gruppi di risorse di origine e di destinazione vengono bloccati impedendo le operazioni di scrittura ed eliminazione. Per altre informazioni, vedere questo [articolo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Solo la sottoscrizione amministratore ha le autorizzazioni per spostare un insieme di credenziali.
- Per lo spostamento di insiemi di credenziali tra sottoscrizioni, la sottoscrizione di destinazione deve risiedere nello stesso tenant della sottoscrizione di origine e il relativo stato deve essere abilitato. Per spostare un insieme di credenziali in una directory Azure AD, vedere Trasferire una sottoscrizione a una [directory](../role-based-access-control/transfer-subscription.md) diversa e Domande frequenti su Insieme di credenziali del servizio [di ripristino](/backup-azure-backup-faq.yml#recovery-services-vault).
- È necessario avere l'autorizzazione per eseguire operazioni di scrittura sul gruppo di risorse di destinazione.
- Lo spostamento dell'insieme di credenziali modifica solo il gruppo di risorse. L'insieme di credenziali di Servizi di ripristino si trova nello stesso percorso e non può essere modificato.
- È possibile spostare un solo insieme di credenziali di Servizi di ripristino, per area, alla volta.
- Se una macchina virtuale non viene spostata con l'insieme di credenziali di Servizi di ripristino tra sottoscrizioni o in un nuovo gruppo di risorse, i punti di ripristino della macchina virtuale correnti rimarranno invariati nell'insieme di credenziali fino alla scadenza.
- Sia che macchina virtuale venga spostata nell'insieme di credenziali o meno, è sempre possibile ripristinare la macchina virtuale dalla cronologia di backup conservata nell'insieme di credenziali.
- Il Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali risiedano nella stessa area e sottoscrizione di Azure.
- Per spostare una macchina virtuale con dischi gestiti, vedere questo [articolo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Le opzioni per lo spostamento delle risorse distribuite tramite il modello classico variano a seconda che le risorse siano state trasferite all'interno di una sottoscrizione o in una nuova sottoscrizione. Per altre informazioni, vedere questo [articolo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- I criteri di backup definiti per l'insieme di credenziali vengono conservati dopo che l'insieme di credenziali viene spostato tra le sottoscrizioni o in un nuovo gruppo di risorse.
- È possibile spostare solo un insieme di credenziali che contiene uno dei tipi di elementi di backup seguenti. Tutti gli elementi di backup di tipi non elencati di seguito dovranno essere arrestati e i dati verranno eliminati definitivamente prima di spostare l'insieme di credenziali.
  - Macchine virtuali di Azure
  - Microsoft Azure di Servizi di ripristino (MARS)
  - Backup di Microsoft Azure Server (MABS)
  - Data Protection Manager (DPM)
- Se si sposta un insieme di credenziali contenente i dati di backup delle macchine virtuali tra sottoscrizioni, è necessario spostare le macchine virtuali nella stessa sottoscrizione e usare lo stesso nome del gruppo di risorse della macchina virtuale di destinazione (come nella sottoscrizione precedente) per continuare i backup.

> [!NOTE]
> Lo spostamento di insiemi di credenziali di Servizi di Backup di Azure tra aree di Azure non è supportato.<br><br>
> Se sono stati configurati macchine virtuali (IaaS di Azure, Hyper-V, VMware) o computer fisici per il ripristino di emergenza con **Azure Site Recovery**, l'operazione di spostamento verrà bloccata. Se si vogliono spostare insiemi di credenziali [](../site-recovery/move-vaults-across-regions.md) per Azure Site Recovery, vedere questo articolo per informazioni sullo spostamento manuale degli insiemi di credenziali.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usare portale di Azure per spostare l'insieme di credenziali di Servizi di ripristino in un gruppo di risorse diverso

Per spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in un gruppo di risorse diverso:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'elenco **Insiemi di credenziali dei servizi di ripristino** e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

   ![Aprire l'insieme di credenziali di Servizi di ripristino](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se le informazioni di  base per l'insieme di credenziali non vengono visualizzate, selezionare l'icona a discesa. Le informazioni di base dovrebbero ora essere visualizzate.

   ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu di panoramica dell'insieme **di** credenziali selezionare Modifica accanto a Gruppo **di risorse** per aprire il **riquadro Sposta** risorse.

   ![Cambiare il gruppo di risorse](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Nel riquadro **Sposta risorse** per l'insieme di credenziali selezionato è consigliabile spostare le risorse correlate facoltative selezionando la casella di controllo come illustrato nell'immagine seguente.

   ![Spostare la sottoscrizione](./media/backup-azure-move-recovery-services/move-resource.png)

5. Per aggiungere il gruppo di  risorse di destinazione, nell'elenco a discesa Gruppo di risorse selezionare un gruppo di risorse esistente o selezionare l'opzione Crea **un nuovo** gruppo.

   ![Creare la risorsa](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Dopo aver aggiunto il gruppo di risorse, verificare di aver compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiornerò per usare i nuovi **ID** risorsa e quindi selezionare **OK** per completare lo spostamento dell'insieme di credenziali.

   ![Messaggio di conferma](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usare portale di Azure per spostare l'insieme di credenziali di Servizi di ripristino in una sottoscrizione diversa

È possibile spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in una sottoscrizione diversa

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'elenco Insiemi di credenziali dei servizi di ripristino e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

    ![Aprire l'insieme di credenziali di Servizi di ripristino](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se le informazioni di  base per l'insieme di credenziali non vengono visualizzate, selezionare l'icona a discesa. Le informazioni di base dovrebbero ora essere visualizzate.

    ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu di panoramica dell'insieme di credenziali selezionare **Cambia** accanto a **Sottoscrizione** per aprire il **riquadro Sposta** risorse.

   ![Cambiare la sottoscrizione](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selezionare le risorse da spostare. In questo caso, è consigliabile usare l'opzione **Seleziona tutto** per selezionare tutte le risorse facoltative elencate.

   ![Spostare la risorsa](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di destinazione in cui si vuole spostare l'insieme di credenziali.
6. Per aggiungere il gruppo di  risorse di destinazione, nell'elenco a discesa Gruppo di risorse selezionare un gruppo di risorse esistente o selezionare l'opzione Crea **un nuovo** gruppo.

   ![Aggiungere la sottoscrizione](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selezionare **Comprendo che gli strumenti** e gli script associati alle risorse spostate non funzioneranno fino a quando non vengono aggiornati in modo da usare i nuovi ID risorsa per confermare e quindi selezionare **OK.**

> [!NOTE]
> Il backup tra sottoscrizioni (l'insieme di credenziali RS e le macchine virtuali protette sono in sottoscrizioni diverse) non è uno scenario supportato. Inoltre, l'opzione di ridondanza dell'archiviazione dall'archiviazione con ridondanza locale (LRS) all'archiviazione con ridondanza globale (GRS) e viceversa non può essere modificata durante l'operazione di spostamento dell'insieme di credenziali.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usare PowerShell per spostare l'insieme di credenziali di Servizi di ripristino

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` richiede il nome e il tipo della risorsa. È possibile ottenerli entrambi dal cmdlet `Get-AzureRmRecoveryServicesVault`.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Per spostare le risorse in una diversa sottoscrizione, includere il parametro `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Dopo aver eseguito i cmdlet precedenti, verrà chiesto di confermare che si vogliono spostare le risorse specificate. Digitare **Y** per confermare. Dopo la convalida, la risorsa viene spostata.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usare l'interfaccia della riga di comando per spostare l'insieme di credenziali di Servizi di ripristino

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet seguente:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="post-migration"></a>Dopo la migrazione

1. Impostare/verificare i controlli di accesso per i gruppi di risorse.  
2. Al termine dello spostamento, è necessario configurare di nuovo la funzionalità Di creazione report e monitoraggio di backup per l'insieme di credenziali. La configurazione precedente andrà persa durante l'operazione di spostamento.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Spostare una macchina virtuale di Azure in un insieme di credenziali del servizio di ripristino diverso. 

Se si vuole spostare una macchina virtuale di Azure in cui è abilitato il backup di Azure, sono disponibili due opzioni. Dipendono dai requisiti aziendali:

- [Non è necessario mantenere i dati di backup precedenti](#dont-need-to-preserve-previous-backed-up-data)
- [Deve mantenere i dati di cui è stato eseguito il backup precedente](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Non è necessario mantenere i dati di backup precedenti

Per proteggere i carichi di lavoro in un nuovo insieme di credenziali, la protezione e i dati correnti dovranno essere eliminati nell'insieme di credenziali precedente e il backup verrà configurato nuovamente.

>[!WARNING]
>L'operazione seguente è distruttiva e non può essere annullata. Tutti i dati di backup e gli elementi di backup associati al server protetto verranno eliminati definitivamente. Procedere con cautela.

**Arrestare ed eliminare la protezione corrente nell'insieme di credenziali precedente:**

1. Disabilitare l'eliminazione temporaneamente nelle proprietà dell'insieme di credenziali. Seguire [questa procedura per](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) disabilitare l'eliminazione temporaneamente.

2. Arrestare la protezione ed eliminare i backup dall'insieme di credenziali corrente. Nel menu del dashboard dell'insieme di credenziali selezionare **Elementi di backup**. Gli elementi elencati di seguito che devono essere spostati nel nuovo insieme di credenziali devono essere rimossi insieme ai relativi dati di backup. Vedere come [eliminare elementi protetti nel cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) ed eliminare elementi protetti in [locale.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

3. Se si prevede di spostare AFS (condivisioni file di Azure), server SQL o SAP HANA server, è necessario annullare anche la registrazione. Nel menu del dashboard dell'insieme di credenziali selezionare **Infrastruttura di backup**. Informazioni su come [annullare la registrazione del server SQL,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)annullare la registrazione di un account di archiviazione associato alle condivisioni file di [Azure](manage-afs-backup.md#unregister-a-storage-account)e annullare la registrazione di un'SAP HANA istanza [di .](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

4. Dopo la rimozione dall'insieme di credenziali precedente, continuare a configurare i backup per il carico di lavoro nel nuovo insieme di credenziali.

### <a name="must-preserve-previous-backed-up-data"></a>Deve mantenere i dati di cui è stato eseguito il backup precedente

Se è necessario mantenere i dati protetti correnti nell'insieme di credenziali precedente e continuare la protezione in un nuovo insieme di credenziali, esistono opzioni limitate per alcuni dei carichi di lavoro:

- Per MARS, è possibile [arrestare la protezione conservando i dati](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registrare l'agente nel nuovo insieme di credenziali.

  - Backup di Azure servizio continuerà a mantenere tutti i punti di ripristino esistenti dell'insieme di credenziali precedente.
  - È necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali precedente.
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup solo per i punti di ripristino non scaduti nell'insieme di credenziali precedente.
  - Nel nuovo insieme di credenziali sarà necessario creare una nuova replica iniziale dei dati.

- Per una macchina virtuale [](backup-azure-manage-vms.md#stop-protecting-a-vm) di Azure, è possibile arrestare la protezione conservando i dati per la macchina virtuale nell'insieme di credenziali precedente, spostare la macchina virtuale in un altro gruppo di risorse e quindi proteggere la macchina virtuale nel nuovo insieme di credenziali. Vedere [indicazioni e limitazioni per lo](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) spostamento di una macchina virtuale in un altro gruppo di risorse.

  Una macchina virtuale può essere protetta in un solo insieme di credenziali alla volta. Tuttavia, la macchina virtuale nel nuovo gruppo di risorse può essere protetta nel nuovo insieme di credenziali perché è considerata una macchina virtuale diversa.

  - Backup di Azure servizio manterrà i punti di ripristino di cui è stato eseguito il backup nell'insieme di credenziali precedente.
  - È necessario pagare per mantenere i punti di ripristino nell'insieme di credenziali precedente (per informazioni dettagliate, vedere Backup di Azure [prezzi).](azure-backup-pricing.md)
  - Sarà possibile ripristinare la macchina virtuale, se necessario, dall'insieme di credenziali precedente.
  - Il primo backup nel nuovo insieme di credenziali della macchina virtuale nella nuova risorsa sarà una replica iniziale.

## <a name="next-steps"></a>Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/management/move-resource-group-and-subscription.md).