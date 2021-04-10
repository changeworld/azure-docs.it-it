---
title: Guida alla migrazione di archiviazione di Azure
description: Guida introduttiva alla migrazione archiviazione descrive le linee guida di base per la migrazione dell'archiviazione
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231593"
---
# <a name="azure-storage-migration-overview"></a>Panoramica della migrazione di archiviazione di Azure

Questo articolo è incentrato sulle migrazioni di archiviazione in Azure e fornisce indicazioni sui seguenti scenari di migrazione dell'archiviazione:

- Migrazione di dati non strutturati, ad esempio file e oggetti
- Migrazione di dispositivi basati su blocchi, ad esempio dischi e reti di archiviazione (San)

## <a name="migration-of-unstructured-data"></a>Migrazione di dati non strutturati

La migrazione di dati non strutturati include gli scenari seguenti:

- Migrazione di file da un dispositivo NAS (Network Attached Storage) a una delle offerte di file di Azure:
  - [File di Azure](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [soluzioni ISV (Independent Software Vendor)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Migrazione di oggetti da soluzioni di archiviazione di oggetti alla piattaforma di archiviazione di oggetti di Azure:
  - [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Fasi di migrazione

Una migrazione completa è costituita da diverse fasi: individuazione, valutazione e migrazione.

![Diagramma che illustra le fasi di individuazione, valutazione e migrazione della migrazione](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Fase di individuazione

Nella fase di individuazione, è possibile determinare tutte le origini di cui è necessario eseguire la migrazione, ad esempio condivisioni SMB, esportazioni NFS o spazi dei nomi degli oggetti. È possibile eseguire questa fase manualmente oppure usare strumenti automatici.

#### <a name="assessment-phase"></a>Fase di valutazione

La fase di valutazione è fondamentale per comprendere le opzioni disponibili per la migrazione. Per ridurre il rischio durante la migrazione ed evitare problemi comuni, attenersi ai tre passaggi seguenti:

| Passaggi della fase di valutazione                     | Opzioni                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Scegliere un servizio di archiviazione di destinazione**            | -Archiviazione BLOB di Azure e Data Lake Storage<br>-File di Azure<br>-Azure NetApp Files<br>-Soluzioni ISV |
| **Selezionare un metodo di migrazione**                  | -Online<br>-Offline<br> -Combinazione di entrambi                                  |
| **Scegliere lo strumento di migrazione migliore per il processo** | -Strumenti commerciali (Azure e ISV)<br> -Open Source                             


Sono disponibili diversi strumenti commerciali (ISV) che possono risultare utili per la fase di valutazione. Vedere la [matrice di confronto](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Scegliere un servizio di archiviazione di destinazione

La scelta di un servizio di archiviazione di destinazione dipende dall'applicazione o dagli utenti che accedono ai dati. La scelta corretta dipende dagli aspetti tecnici e finanziari. Per prima cosa, eseguire una valutazione tecnica per valutare le possibili destinazioni e determinare quali servizi soddisfano i requisiti. Eseguire quindi una valutazione finanziaria per determinare la scelta migliore.

Per facilitare la selezione del servizio di archiviazione di destinazione per la migrazione, valutare gli aspetti seguenti di ogni servizio:

- Supporto dei protocolli
- Caratteristiche delle prestazioni
- Limiti del servizio di archiviazione di destinazione

Il diagramma seguente è un albero delle decisioni semplificato che consente di guidare il servizio file di Azure consigliato. Se i servizi nativi di Azure non soddisfano i requisiti, si otterranno diverse [soluzioni ISV (Independent Software Vendor)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) .

Una volta completata la valutazione tecnica e selezionato la destinazione corretta, eseguire una valutazione dei costi per determinare l'opzione più conveniente.

![Albero delle decisioni di base sulla scelta del servizio file corretto](./media/storage-migration-overview/files-decision-tree.png)

Per semplificare l'albero delle decisioni, i limiti del servizio di archiviazione di destinazione non sono incorporati nel diagramma. Per ulteriori informazioni sui limiti correnti e per determinare se è necessario modificare le scelte in base a tali limiti, vedere:

- [Limiti dell'account di archiviazione](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Limiti di archiviazione BLOB](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Obiettivi di scalabilità e prestazioni per File di Azure](/azure/storage/files/storage-files-scale-targets)
- [Limiti delle risorse Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Se uno dei limiti costituisce un blocco per l'uso di un servizio, Azure supporta diversi fornitori di archiviazione che offrono le proprie soluzioni in Azure Marketplace. Per informazioni sui partner ISV convalidati che forniscono servizi file, vedere [partner di archiviazione di Azure per l'archiviazione primaria e secondaria](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Selezionare il metodo di migrazione

Esistono due metodi di migrazione di base per le migrazioni di archiviazione.

- **Online**. Il metodo online usa la rete per la migrazione dei dati. È possibile usare la rete Internet pubblica o [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) . Se il servizio non dispone di un endpoint pubblico, è necessario usare una VPN con la rete Internet pubblica.
- **Offline.** Il metodo offline usa uno dei dispositivi [Azure Data Box](https://azure.microsoft.com/services/databox/) .

La decisione di usare un metodo online rispetto a un metodo offline dipende dalla larghezza di banda di rete disponibile. Il metodo online è preferibile nei casi in cui è disponibile una larghezza di banda di rete sufficiente per eseguire una migrazione entro la sequenza temporale necessaria.

È possibile usare una combinazione di entrambi i metodi, il metodo offline per la migrazione in blocco iniziale e un metodo online per la migrazione incrementale delle modifiche. L'utilizzo simultaneo di entrambi i metodi richiede un livello elevato di coordinamento e non è consigliato per questo motivo. Se si sceglie di utilizzare entrambi i metodi, isolare i set di dati di cui viene eseguita la migrazione online dai set di dati di cui viene eseguita la migrazione offline.

Per altre informazioni sui diversi metodi e linee guida per la migrazione, vedere [scegliere una soluzione di Azure per il trasferimento di dati](/azure/storage/common/storage-choose-data-transfer-solution) ed [eseguire la migrazione a condivisioni file di Azure](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Scegliere lo strumento di migrazione migliore per il processo

Per eseguire la migrazione, è possibile usare vari strumenti di migrazione. Alcune sono open source, ad esempio AzCopy, Robocopy, XCOPY e rsync, mentre altre sono commerciali. L'elenco degli strumenti commerciali disponibili e il confronto tra di essi sono disponibili nella [matrice di confronto](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

Gli strumenti open source sono particolarmente adatti per le migrazioni su scala ridotta. Per la migrazione da file server Windows a File di Azure, Microsoft consiglia di iniziare con File di Azure funzionalità nativa e con [sincronizzazione file di Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync). Per migrazioni più complesse costituite da origini diverse, capacità elevata o requisiti speciali come la limitazione o la creazione di report dettagliati con le funzionalità di controllo, gli strumenti commerciali sono la scelta migliore. Questi strumenti semplificano la migrazione e riducono significativamente il rischio. La maggior parte degli strumenti commerciali può anche eseguire l'individuazione, che fornisce un input prezioso per la valutazione.

#### <a name="migration-phase"></a>Fase di migrazione

La fase di migrazione è il passaggio finale della migrazione che esegue lo spostamento e la migrazione dei dati. In genere, la fase di migrazione viene eseguita più volte per ottenere un passaggio più semplice. La fase di migrazione prevede i passaggi seguenti:

1. **Migrazione iniziale.** Il passaggio iniziale della migrazione esegue la migrazione di tutti i dati dall'origine alla destinazione. Questo passaggio esegue la migrazione della maggior parte dei dati di cui è necessario eseguire la migrazione.
2. **Risincronizzazione.** Un'operazione di risincronizzazione esegue la migrazione di tutti i dati che sono stati modificati dopo il passaggio iniziale della migrazione. È possibile ripetere questo passaggio più volte se sono presenti numerose modifiche. L'obiettivo dell'esecuzione di più operazioni di risincronizzazione consiste nel ridurre il tempo necessario per il passaggio finale. Per i dati inattivi e per i dati senza modifiche, ad esempio i dati di backup o di archiviazione, è possibile ignorare questo passaggio.
3. **Switchover finale**. Il passaggio di switchover finale passa l'utilizzo attivo dei dati dall'origine alla destinazione e ritira l'origine.

La durata della migrazione dei dati non strutturati dipende da diversi aspetti. Al di fuori del metodo scelto, i fattori più importanti sono la dimensione totale della distribuzione dei dati e delle dimensioni del file. Maggiore è il set di dati totale, maggiore è il tempo di migrazione. Minore è la dimensione media del file, più lunga è la durata della migrazione. Se si dispone di un numero elevato di file di piccole dimensioni, è consigliabile archiviarli in file di dimensioni maggiori, ad esempio in un file con estensione tar o zip, per ridurre il tempo totale di migrazione.

## <a name="migration-of-block-based-devices"></a>Migrazione di dispositivi basati su blocchi

La migrazione dei dispositivi basati su blocchi viene in genere eseguita come parte della migrazione di macchine virtuali o host fisici. Si tratta di un malinteso comune per ritardare le decisioni di archiviazione a blocchi fino al termine della migrazione. Prendere queste decisioni in anticipo con considerazioni appropriate per i requisiti del carico di lavoro comporta una migrazione più semplice al cloud.

Per esplorare i carichi di lavoro per la migrazione e l'approccio da intraprendere, vedere la [documentazione di archiviazione su disco di Azure](/azure/virtual-machines/disks-types)e le risorse nella [pagina del prodotto archiviazione su disco](https://azure.microsoft.com/services/storage/disks/#resources). È possibile ottenere informazioni sui dischi che soddisfano i requisiti e sulle funzionalità più recenti, ad esempio il potenziamento del [disco](/azure/virtual-machines/disk-bursting). Per informazioni su come eseguire la migrazione delle macchine virtuali insieme ai dispositivi basati su blocchi sottostanti, vedere la documentazione [Azure migrate](/azure/migrate/) .

## <a name="see-also"></a>Vedi anche

- [Scegliere una soluzione di Azure per il trasferimento dei dati](/azure/storage/common/storage-choose-data-transfer-solution)
- [Confronto tra strumenti di migrazione commerciale](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Eseguire la migrazione a condivisioni file di Azure](/azure/storage/files/storage-files-migration-overview)
- [Eseguire la migrazione a Data Lake Storage con la piattaforma WANdisco LiveData per Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copiare o spostare dati in archiviazione di Azure con AzCopy](https://aka.ms/azcopy)
- [Eseguire la migrazione di set di impostazioni di grandi dimensioni nell'archiviazione BLOB di Azure con AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)