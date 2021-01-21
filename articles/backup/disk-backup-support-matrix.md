---
title: Matrice di supporto per il backup di dischi di Azure
description: Fornisce un riepilogo delle impostazioni di supporto e delle limitazioni di backup su disco di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 099e83d8a2fb109da862657265dad8be8143f608
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624935"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Matrice di supporto di backup su disco di Azure (in anteprima)

>[!IMPORTANT]
>Il backup su disco di Azure è in anteprima senza un contratto di servizio e non è consigliato per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Compilare questo modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) per iscriversi all'anteprima.

È possibile usare [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) per proteggere i dischi di Azure. Questo articolo riepiloga la disponibilità delle aree, gli scenari supportati e le limitazioni.

## <a name="supported-regions"></a>Aree supportate

Il backup su disco di Azure è disponibile in anteprima nelle aree seguenti: Stati Uniti centro-occidentali. 

Quando diventano disponibili, verranno annunciate altre aree.

## <a name="limitations"></a>Limitazioni

- Il backup su disco di Azure è supportato per Managed Disks di Azure, inclusi i dischi condivisi (unità SSD Premium condivise). I dischi non gestiti non sono supportati. Attualmente questa soluzione non supporta l'ultra-disks, inclusi i dischi rigidi condivisi, a causa della mancanza di funzionalità di snapshot.

- Il backup su disco di Azure supporta il backup di acceleratore di scrittura disco. Tuttavia, durante il ripristino il disco verrebbe ripristinato come disco normale. La cache con accelerazione scrittura può essere abilitata sul disco dopo il montaggio in una macchina virtuale.

- Backup di Azure fornisce il backup a livello operativo (snapshot) di Azure Managed disks con supporto per più backup al giorno. I backup non vengono copiati nell'insieme di credenziali per il backup.

- Attualmente, l'opzione di ripristino Original-Location (OLR) da ripristinare sostituendo i dischi di origine esistenti in cui sono stati eseguiti i backup non è supportata. È possibile eseguire il ripristino dal punto di ripristino per creare un nuovo disco nello stesso gruppo di risorse del disco di origine da cui sono stati eseguiti i backup o in qualsiasi altro gruppo di risorse. Questa operazione è nota come Alternate-Location Recovery (ALR).

- Backup di Azure per Managed Disks USA snapshot incrementali, che sono limitati a 200 snapshot per disco. Per consentire il backup su richiesta da parte di backup pianificati, i criteri di backup limitano il totale dei backup a 180. Altre informazioni sullo [snapshot incrementale](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) per Managed Disks.

- La [sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-disk-limits) di Azure e i limiti del servizio si applicano al numero totale di snapshot del disco per area per sottoscrizione.

- Gli snapshot temporizzati di più dischi collegati a una macchina virtuale non sono supportati.

- L'insieme di credenziali per il backup e i dischi di cui eseguire il backup possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse. Tuttavia, l'insieme di credenziali per il backup e il disco di cui eseguire il backup devono trovarsi nella stessa area.

- I dischi di cui eseguire il backup e il gruppo di risorse snapshot in cui gli snapshot vengono archiviati localmente devono trovarsi nella stessa sottoscrizione.

- Il ripristino di un disco dal backup allo stesso o a una sottoscrizione diversa è supportato. Il disco ripristinato verrà tuttavia creato nella stessa area di quella dello snapshot.

- I dischi crittografati ADE non possono essere protetti.

- Sono supportati i dischi crittografati con chiavi gestite dalla piattaforma o chiavi gestite dal cliente. Tuttavia, il ripristino avrà esito negativo per i punti di ripristino di un disco crittografato con le chiavi gestite dal cliente (CMK) se la chiave di crittografia del disco impostata è disabilitata o eliminata.

- Attualmente, non è possibile modificare i criteri di backup e il gruppo di risorse snapshot assegnato a un'istanza di backup quando si configura il backup di un disco non può essere modificato.

- Attualmente, l'esperienza portale di Azure per configurare il backup dei dischi è limitata a un massimo di 20 dischi dalla stessa sottoscrizione.

- Attualmente (durante l'anteprima), l'uso di PowerShell e dell'interfaccia della riga di comando di Azure per configurare il backup e il ripristino dei dischi non è supportato.

- Quando si configura il backup, il disco selezionato per il backup e il gruppo di risorse snapshot in cui devono essere archiviati gli snapshot devono far parte della stessa sottoscrizione. Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco. Altre informazioni sugli [snapshot incrementali](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) per il disco gestito. Per altre informazioni su come scegliere un gruppo di risorse snapshot, vedere  [configurare il backup](backup-managed-disks.md#configure-backup).

- Per operazioni di backup e ripristino riuscite, le assegnazioni di ruolo sono richieste dall'identità gestita dell'insieme di credenziali di backup. Utilizzare solo le definizioni di ruolo fornite nella documentazione. L'uso di altri ruoli, come proprietario, collaboratore e così via, non è supportato. È possibile che si verifichino problemi di autorizzazione, se si inizia a configurare le operazioni di backup o ripristino subito dopo l'assegnazione di ruoli. Ciò è dovuto al fatto che le assegnazioni di ruolo hanno effetto solo alcuni minuti.

- I dischi gestiti consentono di modificare il livello di prestazioni in fase di distribuzione o successivamente senza modificare le dimensioni del disco. La soluzione di backup su disco di Azure supporta le modifiche al livello di prestazioni del disco di origine di cui è in corso il backup. Durante il ripristino, il livello di prestazioni del disco ripristinato sarà uguale a quello del disco di origine al momento del backup. Seguire la documentazione [qui](https://docs.microsoft.com/azure/virtual-machines/disks-performance-tiers-portal) per modificare il livello di prestazioni del disco dopo l'operazione di ripristino.

- Il supporto dei [collegamenti privati](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal) per Managed disks consente di limitare l'esportazione e l'importazione di dischi gestiti in modo che si verifichino solo all'interno della rete virtuale di Azure. Il backup su disco di Azure supporta il backup di dischi con endpoint privati abilitati. Questa operazione non include i dati o gli snapshot di backup accessibili tramite l'endpoint privato.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup di Azure Managed Disks](backup-managed-disks.md)
