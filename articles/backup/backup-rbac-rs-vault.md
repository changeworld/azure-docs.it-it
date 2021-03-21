---
title: Gestire i backup con il controllo degli accessi in base al ruolo di Azure
description: Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle operazioni di gestione di backup nell'insieme di credenziali di servizi
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613443"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Usare il controllo di accesso in base al ruolo di Azure per gestire i punti di ripristino di backup di Azure

Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente una gestione degli accessi con granularità fine per Azure. Usando il controllo degli accessi in base al ruolo di Azure, è possibile separare le mansioni all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il loro lavoro.

> [!IMPORTANT]
> I ruoli forniti da backup di Azure sono limitati alle azioni che possono essere eseguite in portale di Azure o tramite l'API REST o i cmdlet di PowerShell per l'insieme di credenziali di servizi di ripristino. Le azioni eseguite nell'interfaccia utente del client dell'agente di backup di Azure o nell'interfaccia utente di System Center Data Protection Manager o nell'interfaccia utente server di Backup di Azure non sono in alcun controllo di questi ruoli.

Backup di Azure offre tre ruoli predefiniti per controllare le operazioni di gestione dei backup. Scopri di più sui [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md)

* [Collaboratore backup](../role-based-access-control/built-in-roles.md#backup-contributor) : questo ruolo dispone di tutte le autorizzazioni per creare e gestire il backup, eccetto l'eliminazione dell'insieme di credenziali dei servizi di ripristino e l'accesso ad altri. Si immagini questo ruolo come amministratore della gestione di backup autorizzato a eseguire ogni operazione in tale ambito.
* [Operatore di backup](../role-based-access-control/built-in-roles.md#backup-operator): questo ruolo dispone delle autorizzazioni per tutte le operazioni svolte da un collaboratore, tranne per la rimozione di backup e la gestione dei criteri di backup. Questo ruolo è equivalente al collaboratore, ma non può eseguire operazioni distruttive, ad esempio interrompere il backup con eliminazione dei dati o rimuovere la registrazione di risorse locali.
* [Lettore di backup](../role-based-access-control/built-in-roles.md#backup-reader): questo ruolo dispone delle autorizzazioni per visualizzare tutte le operazioni di gestione di backup. Si immagini questo ruolo come una persona addetta al monitoraggio.

Per definire ruoli personalizzati per un maggiore controllo, vedere How to [Build Custom Roles](../role-based-access-control/custom-roles.md) in Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapping dei ruoli predefiniti di Backup per azioni di gestione di backup

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Requisiti minimi del ruolo per il backup delle macchine virtuali di Azure

La tabella seguente acquisisce le azioni di gestione del backup e il ruolo di Azure minimo corrispondente necessario per eseguire tale operazione.

| Operazione di gestione | Ruolo minimo di Azure obbligatorio | Ambito necessario | Alternativa |
| --- | --- | --- | --- |
| Creare un insieme di credenziali di Servizi di ripristino | Collaboratore di backup | Gruppo di risorse contenente l'insieme di credenziali |   |
| Abilitare il backup di VM di Azure | Operatore di backup | Gruppo di risorse contenente l'insieme di credenziali |   |
| | Collaboratore macchine virtuali | Risorsa della VM |  In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| Backup su richiesta della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |   |
| Ripristino della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |   |
| | Autore di contributi | Gruppo di risorse in cui verrà distribuita la VM |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. resources/subscriptions/resourceGroups/Write Microsoft. DomainRegistration/Domains/Write, Microsoft. Compute/virtualMachines/Write Microsoft. Network/virtualNetworks/Read Microsoft. Network/virtualNetworks/Subnets/join/Action |
| | Collaboratore macchine virtuali | VM di origine di cui è stato eseguito il backup |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| Ripristinare dischi non gestiti dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | VM di origine di cui è stato eseguito il backup | In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| | Collaboratore account di archiviazione | Risorsa account di archiviazione in cui i dischi saranno ripristinati |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. storage/storageAccounts/Write |
| Ripristinare dischi gestiti dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | VM di origine di cui è stato eseguito il backup |    In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| | Collaboratore account di archiviazione | Account di archiviazione temporaneo selezionato come parte del ripristino per contenere i dati dall'insieme di credenziali prima di convertirli in dischi gestiti |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. storage/storageAccounts/Write |
| | Autore di contributi | Gruppo di risorse in cui verranno ripristinati i dischi gestiti | In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. resources/subscriptions/resourceGroups/Write|
| Ripristinare singoli file dal backup delle VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore macchine virtuali | VM di origine di cui è stato eseguito il backup | In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| Ripristino tra aree | Operatore di backup | Sottoscrizione dell'insieme di credenziali di servizi di ripristino | Questa operazione è aggiunta alle autorizzazioni di ripristino indicate in precedenza. In particolare per CRR, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: "Microsoft. RecoveryServices/locations/backupAadProperties/Read" "Microsoft. RecoveryServices/locations/backupCrrJobs/Action" "Microsoft. RecoveryServices/locations/backupCrrJob/Action" "Microsoft. RecoveryServices/locations/backupCrossRegionRestore/Action" "Microsoft. RecoveryServices/locations/backupCrrOperationResults/Read" "Microsoft. RecoveryServices/locations/backupCrrOperationsStatus/Read" |
| Creare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Modificare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Eliminare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Interrompere il backup (con o senza conservazione dei dati) in operazioni di backup di VM | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Registrare Windows Server/client/SCDPM locale o server di Backup di Azure | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Eliminare Windows Server/client/SCDPM locale o server di Backup di Azure registrato | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |

> [!IMPORTANT]
> Se si specifica collaboratore macchina virtuale in un ambito di risorse VM e si seleziona **backup** come parte delle impostazioni della macchina virtuale, verrà visualizzata la schermata **Abilita backup** , anche se è già stato eseguito il backup della macchina virtuale. Questo perché la chiamata per verificare lo stato del backup funziona solo a livello di sottoscrizione. Per evitare questo problema, passare all'insieme di credenziali e aprire la visualizzazione dell'elemento di backup della VM o specificare il ruolo Collaboratore macchina virtuale a livello di sottoscrizione.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Requisiti minimi del ruolo per i backup del carico di lavoro di Azure (backup del database SQL e HANA)

La tabella seguente acquisisce le azioni di gestione del backup e il ruolo di Azure minimo corrispondente necessario per eseguire tale operazione.

| Operazione di gestione | Ruolo minimo di Azure obbligatorio | Ambito necessario | Alternativa |
| --- | --- | --- | --- |
| Creare un insieme di credenziali di Servizi di ripristino | Collaboratore di backup | Gruppo di risorse contenente l'insieme di credenziali |   |
| Abilitare il backup dei database SQL e/o HANA | Operatore di backup | Gruppo di risorse contenente l'insieme di credenziali |   |
| | Collaboratore macchine virtuali | Risorsa macchina virtuale in cui è installato il database |  In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| Backup su richiesta del database | Operatore di backup | Insieme di credenziali dei servizi di ripristino |   |
| Ripristina database o Ripristina come file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |   |
| | Collaboratore macchine virtuali | VM di origine di cui è stato eseguito il backup |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| | Collaboratore macchine virtuali | VM di destinazione in cui verrà ripristinato il database o verranno creati i file |   In alternativa, invece di un ruolo predefinito, è possibile prendere in considerazione un ruolo personalizzato con le autorizzazioni seguenti: Microsoft. Compute/virtualMachines/Write |
| Creare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Modificare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Eliminare criteri di backup per il backup di VM di Azure | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Interrompere il backup (con o senza conservazione dei dati) in operazioni di backup di VM | Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisiti minimi del ruolo per il backup della condivisione file di Azure

La tabella seguente acquisisce le azioni di gestione del backup e il ruolo corrispondente necessari per eseguire l'operazione di condivisione file di Azure.

| Operazione di gestione | Ruolo obbligatorio | Risorse |
| --- | --- | --- |
| Abilitare il backup delle condivisioni file di Azure | Collaboratore di backup |Insieme di credenziali dei servizi di ripristino |
| |Account di archiviazione | Risorsa account di archiviazione collaboratore |
| Backup su richiesta della VM | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| Ripristino della condivisione file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| | Collaboratore account di archiviazione | Risorse dell'account di archiviazione in cui sono presenti condivisioni file di origine e di destinazione |
| Ripristinare singoli file | Operatore di backup | Insieme di credenziali dei servizi di ripristino |
| |Collaboratore account di archiviazione|Risorse dell'account di archiviazione in cui sono presenti condivisioni file di origine e di destinazione |
| Arresta protezione |Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| Annulla la registrazione dell'account di archiviazione dall'insieme di credenziali |Collaboratore di backup | Insieme di credenziali dei servizi di ripristino |
| |Collaboratore account di archiviazione | Risorsa dell'account di archiviazione|

## <a name="next-steps"></a>Passaggi successivi

* [Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md): Introduzione a RBAC di azure nella portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo di Azure](../role-based-access-control/troubleshooting.md): ottenere suggerimenti per la risoluzione di problemi comuni.
