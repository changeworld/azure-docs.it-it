---
title: Panoramica della protezione dei dati
titleSuffix: Azure Storage
description: Le opzioni di protezione dei dati disponibili per l'archiviazione BLOB e i dati Azure Data Lake Storage Gen2 consentono di proteggere i dati dall'eliminazione o dalla sovrascrittura. Se è necessario ripristinare i dati che sono stati eliminati o sovrascritti, questa guida può essere utile per scegliere l'opzione di ripristino più adatta al proprio scenario.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304257"
---
# <a name="data-protection-overview"></a>Panoramica della protezione dei dati

Archiviazione di Azure offre protezione dei dati per l'archiviazione BLOB e Azure Data Lake Storage Gen2 per prepararsi agli scenari in cui è necessario ripristinare i dati eliminati o sovrascritti. È importante pensare al modo migliore per proteggere i dati prima che si verifichi un evento imprevisto che può comprometterlo. Questa guida consente di decidere in anticipo quali sono le funzionalità di protezione dei dati richieste dallo scenario e come implementarle. Se è necessario ripristinare i dati che sono stati eliminati o sovrascritti, questa panoramica fornisce anche indicazioni su come procedere, in base allo scenario.

Nella documentazione di archiviazione di Azure, la *protezione dei dati* si riferisce alle strategie per la protezione dell'account di archiviazione e dei dati all'interno da eliminare o modificare o per il ripristino dei dati dopo l'eliminazione o la modifica. Archiviazione di Azure offre anche opzioni per il *ripristino di emergenza*, inclusi più livelli di ridondanza per proteggere i dati dalle interruzioni del servizio a causa di problemi hardware o calamità naturali e failover gestito dal cliente nel caso in cui il Data Center nell'area primaria diventi non disponibile. Per ulteriori informazioni sul modo in cui i dati vengono protetti da interruzioni del servizio, vedere [ripristino di emergenza](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Suggerimenti per la protezione dei dati di base

Se si sta cercando la copertura per la protezione dei dati di base per l'account di archiviazione e i dati in esso contenuti, Microsoft consiglia di seguire questa procedura per iniziare:

- Configurare un blocco Azure Resource Manager sull'account di archiviazione per proteggere l'account dall'eliminazione o dalle modifiche di configurazione. [Ulteriori informazioni...](../common/lock-account-resource.md)
- Abilitare l'eliminazione temporanea del contenitore per l'account di archiviazione per ripristinare un contenitore eliminato e il relativo contenuto. [Ulteriori informazioni...](soft-delete-container-enable.md)
- Salvare lo stato di un BLOB a intervalli regolari:
  - Per i carichi di lavoro di archiviazione BLOB, abilitare il controllo delle versioni BLOB per salvare automaticamente lo stato dei dati ogni volta che un BLOB viene sovrascritto. [Ulteriori informazioni...](versioning-enable.md)
  - Per carichi di lavoro Azure Data Lake Storage, è possibile eseguire snapshot manuali per salvare lo stato dei dati in un determinato momento. [Ulteriori informazioni...](snapshots-overview.md)

Queste opzioni, oltre ad altre opzioni di protezione dei dati per altri scenari, sono descritte più dettagliatamente nella sezione seguente.

Per una panoramica dei costi associati a queste funzionalità, vedere [Riepilogo delle considerazioni sui costi](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Panoramica delle opzioni di protezione dati

La tabella seguente riepiloga le opzioni disponibili in archiviazione di Azure per gli scenari comuni di protezione dei dati. Per ulteriori informazioni sulle opzioni disponibili, scegliere gli scenari applicabili alla propria situazione. Si noti che non tutte le funzionalità sono disponibili in questo momento per gli account di archiviazione con uno spazio dei nomi gerarchico abilitato.

| Scenario | Opzione di protezione dati | Consigli | Vantaggio di protezione | Disponibile per Data Lake Storage |
|--|--|--|--|--|
| Impedire l'eliminazione o la modifica di un account di archiviazione. | Blocco Azure Resource Manager<br />[Ulteriori informazioni...](../common/lock-account-resource.md) | Bloccare tutti gli account di archiviazione con un blocco Azure Resource Manager per impedire l'eliminazione dell'account di archiviazione. | Protegge l'account di archiviazione da eliminazioni o modifiche di configurazione.<br /><br />Non protegge i contenitori o i BLOB nell'account da essere eliminati o sovrascritti. | Sì |
| Impedire che un contenitore e i relativi BLOB vengano eliminati o modificati per un intervallo che si controlla. | Criteri di immutabilità in un contenitore<br />[Ulteriori informazioni...](storage-blob-immutable-storage.md) | Impostare i criteri di immutabilità in un contenitore per proteggere i documenti aziendali critici, ad esempio per soddisfare i requisiti di conformità legali o normativi. | Protegge un contenitore e i relativi BLOB da tutte le eliminazioni e sovrascritture.<br /><br />Quando è attivo un criterio di conservazione basato sul tempo e bloccato, l'account di archiviazione viene protetto anche dall'eliminazione. I contenitori per i quali non sono stati impostati criteri di immutabilità non sono protetti dall'eliminazione. | Sì, in anteprima |
| Ripristinare un contenitore eliminato entro un intervallo specificato. | Eliminazione temporanea del contenitore (anteprima)<br />[Ulteriori informazioni...](soft-delete-container-overview.md) | Abilitare l'eliminazione temporanea del contenitore per tutti gli account di archiviazione, con un intervallo di conservazione minimo di 7 giorni.<br /><br />Abilitare il controllo delle versioni dei BLOB e l'eliminazione temporanea dei BLOB insieme all'eliminazione temporanea del contenitore per proteggere i singoli BLOB in un contenitore.<br /><br />Archiviare i contenitori che richiedono periodi di conservazione diversi in account di archiviazione separati. | Un contenitore eliminato e il relativo contenuto possono essere ripristinati entro il periodo di memorizzazione.<br /><br />È possibile ripristinare solo le operazioni a livello di contenitore (ad esempio, [Delete Container](/rest/api/storageservices/delete-container)). L'eliminazione temporanea del contenitore non consente di ripristinare un singolo BLOB nel contenitore se il BLOB viene eliminato. | Sì, in anteprima |
| Salva automaticamente lo stato di un BLOB in una versione precedente quando viene sovrascritto. | Controllo delle versioni dei BLOB<br />[Ulteriori informazioni...](versioning-overview.md) | Abilitare il controllo delle versioni dei BLOB, insieme all'eliminazione temporanea del contenitore e all'eliminazione temporanea dei BLOB, per gli account di archiviazione in cui è necessaria una protezione ottimale per i dati BLOB.<br /><br />Archiviare i dati BLOB che non richiedono il controllo delle versioni in un account separato per limitare i costi. | Ogni operazione di scrittura BLOB crea una nuova versione. Se la versione corrente è stata eliminata o sovrascritta, è possibile ripristinare la versione corrente di un BLOB da una versione precedente. | No |
| Ripristinare una versione BLOB o BLOB eliminata entro un intervallo specificato. | Eliminazione temporanea BLOB<br />[Ulteriori informazioni...](soft-delete-blob-overview.md) | Abilitare l'eliminazione temporanea BLOB per tutti gli account di archiviazione, con un intervallo di conservazione minimo di 7 giorni.<br /><br />Abilitare il controllo delle versioni BLOB e l'eliminazione temporanea del contenitore insieme all'eliminazione temporanea BLOB per la protezione ottimale dei dati BLOB.<br /><br />Archiviare BLOB che richiedono periodi di conservazione diversi in account di archiviazione separati. | È possibile ripristinare una versione BLOB o BLOB eliminata entro il periodo di memorizzazione. | No |
| Ripristinare un set di BLOB in blocchi a un momento precedente. | Ripristino temporizzato<br />[Ulteriori informazioni...](point-in-time-restore-overview.md) | Per eseguire il ripristino temporizzato per ripristinare uno stato precedente, progettare l'applicazione in modo da eliminare singoli BLOB in blocchi anziché eliminare i contenitori. | Un set di BLOB in blocchi può essere ripristinato allo stato in un momento specifico nel passato.<br /><br />Vengono ripristinate solo le operazioni eseguite sui BLOB in blocchi. Qualsiasi operazione eseguita su contenitori, BLOB di pagine o BLOB di Accodamento non viene ripristinata. | No |
| Salvare manualmente lo stato di un BLOB in un determinato momento. | Snapshot BLOB<br />[Ulteriori informazioni...](snapshots-overview.md) | Consigliato come alternativa al controllo delle versioni dei BLOB quando il controllo delle versioni non è appropriato per lo scenario, a causa di costi o altre considerazioni oppure quando per l'account di archiviazione è abilitato uno spazio dei nomi gerarchico. | Un BLOB può essere ripristinato da uno snapshot se il BLOB viene sovrascritto. Se il BLOB viene eliminato, vengono eliminati anche gli snapshot. | Sì, in anteprima |
| Un BLOB può essere eliminato o sovrascritto, ma i dati vengono copiati regolarmente in un secondo account di archiviazione. | Soluzione roll-your-own per la copia dei dati in un secondo account tramite la replica di oggetti di archiviazione di Azure o uno strumento come AzCopy o Azure Data Factory. | Consigliato per la protezione della sicurezza da azioni intenzionali impreviste o scenari imprevedibili.<br /><br />Creare il secondo account di archiviazione nella stessa area dell'account primario per evitare di incorrere in costi di uscita. | I dati possono essere ripristinati dal secondo account di archiviazione, se l'account primario viene compromesso in alcun modo. | AzCopy e Azure Data Factory sono supportati.<br /><br />La replica di oggetti non è supportata. |

## <a name="data-protection-by-resource-type"></a>Protezione dei dati per tipo di risorsa

La tabella seguente riepiloga le opzioni di protezione dei dati di archiviazione di Azure in base alle risorse che proteggono.

| Opzione di protezione dati | Protegge un account dall'eliminazione | Protegge un contenitore dall'eliminazione | Protegge un BLOB dall'eliminazione | Protegge un BLOB da sovrascritture |
|--|--|--|--|--|
| Blocco Azure Resource Manager | Sì | Nessun<sup>1</sup> | No | No |
| Criteri di immutabilità in un contenitore | Sì<sup>2</sup> | Sì | Sì | Sì |
| Eliminazione temporanea del contenitore | No | Sì | No | No |
| Controllo delle versioni dei BLOB<sup>3</sup> | No | No | Sì | Sì |
| Eliminazione temporanea BLOB<sup>3</sup> | No | No | Sì | Sì |
| Ripristino temporizzato<sup>3</sup> | No | No | Sì | Sì |
| Snapshot BLOB | No | No | No | Sì |
| Soluzione roll-your-own per la copia dei dati in un secondo account<sup>4</sup> | No | Sì | Sì | Sì |

<sup>1</sup> un blocco Azure Resource Manager non protegge un contenitore dall'eliminazione.<br />
<sup>2</sup> sebbene sia attivo un criterio di conservazione basato sul tempo o bloccato per un contenitore, anche l'account di archiviazione viene protetto dall'eliminazione.<br />
<sup>3</sup> attualmente non supportata per i carichi di lavoro data Lake storage.<br />
<sup>4</sup> AzCopy e Azure Data Factory sono opzioni supportate sia per l'archiviazione BLOB che per i carichi di lavoro di data Lake storage. La replica degli oggetti è supportata solo per i carichi di lavoro di archiviazione BLOB.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Ripristinare i dati eliminati o sovrascritti

Se è necessario ripristinare i dati sovrascritti o eliminati, il modo in cui procedere dipende dalle opzioni di protezione dati abilitate e dalla risorsa interessata. Nella tabella seguente vengono descritte le azioni che è possibile eseguire per ripristinare i dati.

| Risorsa eliminata o sovrascritta | Possibili azioni di ripristino | Requisiti per il ripristino |
|--|--|--|
| Account di archiviazione | Tentativo di recupero dell'account di archiviazione eliminato<br />[Ulteriori informazioni...](../common/storage-account-recover.md) | L'account di archiviazione è stato originariamente creato con il modello di distribuzione Azure Resource Manager ed è stato eliminato negli ultimi 14 giorni. Non è stato creato un nuovo account di archiviazione con lo stesso nome dopo l'eliminazione dell'account originale. |
| Contenitore | Ripristinare il contenitore eliminato temporaneamente e il relativo contenuto<br />[Ulteriori informazioni...](soft-delete-container-enable.md) | L'eliminazione temporanea del contenitore è abilitata e il periodo di memorizzazione dell'eliminazione temporanea del contenitore non è ancora scaduto. |
| Contenitori e BLOB | Ripristinare i dati da un secondo account di archiviazione | Tutte le operazioni del contenitore e del BLOB sono state replicate in modo efficace in un secondo account di archiviazione. |
| BLOB (qualsiasi tipo) | Ripristinare un BLOB da una versione precedente<sup>1</sup><br />[Ulteriori informazioni...](versioning-enable.md) | Il controllo delle versioni dei BLOB è abilitato e il BLOB contiene una o più versioni precedenti. |
| BLOB (qualsiasi tipo) | Ripristinare un BLOB eliminato temporaneamente<sup>1</sup><br />[Ulteriori informazioni...](soft-delete-blob-enable.md) | L'eliminazione temporanea del BLOB è abilitata e l'intervallo di conservazione dell'eliminazione temporanea non è scaduto. |
| BLOB (qualsiasi tipo) | Ripristinare un BLOB da uno snapshot<br />[Ulteriori informazioni...](snapshots-manage-dotnet.md) | Il BLOB contiene uno o più snapshot. |
| Set di BLOB in blocchi | Ripristinare un set di BLOB in blocchi al relativo stato in un punto precedente nel tempo<sup>1</sup><br />[Ulteriori informazioni...](point-in-time-restore-manage.md) | Il ripristino temporizzato è abilitato e il punto di ripristino rientra nell'intervallo di conservazione. L'account di archiviazione non è stato compromesso o è danneggiato. |
| Versione BLOB | Ripristinare una versione eliminata temporaneamente<sup>1</sup><br />[Ulteriori informazioni...](soft-delete-blob-enable.md) | L'eliminazione temporanea del BLOB è abilitata e l'intervallo di conservazione dell'eliminazione temporanea non è scaduto. |

<sup>1</sup> attualmente non supportata per i carichi di lavoro data Lake storage.

## <a name="summary-of-cost-considerations"></a>Riepilogo delle considerazioni sui costi

Nella tabella seguente sono riepilogate le considerazioni sui costi per le varie opzioni di protezione dei dati descritte in questa guida.

| Opzione di protezione dati | Considerazioni sul costo |
|-|-|
| Blocco Azure Resource Manager per un account di archiviazione | Nessun addebito per configurare un blocco su un account di archiviazione. |
| Criteri di immutabilità in un contenitore | Nessun addebito per configurare i criteri di immutabilità in un contenitore. |
| Eliminazione temporanea del contenitore | Nessun addebito per abilitare l'eliminazione temporanea del contenitore per un account di archiviazione. I dati in un contenitore eliminato temporaneamente vengono fatturati alla stessa frequenza dei dati attivi finché il contenitore eliminato temporaneamente non viene eliminato definitivamente. |
| Controllo delle versioni dei BLOB | Nessun addebito per abilitare il controllo delle versioni dei BLOB per un account di archiviazione. Dopo l'abilitazione del controllo delle versioni dei BLOB, ogni operazione di scrittura o eliminazione in un BLOB nell'account crea una nuova versione, che può causare un aumento dei costi di capacità.<br /><br />Una versione BLOB viene fatturata in base a blocchi o pagine univoche. I costi aumentano pertanto con la divergenza tra il BLOB di base e una particolare versione. La modifica del livello di un BLOB o di una versione BLOB può avere un effetto sulla fatturazione. Per ulteriori informazioni, vedere [prezzi e fatturazione](versioning-overview.md#pricing-and-billing).<br /><br />Usare la gestione del ciclo di vita per eliminare le versioni precedenti in base alle esigenze per controllare i costi. Per altre informazioni, vedere [ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure](storage-lifecycle-management-concepts.md). |
| Eliminazione temporanea BLOB | Nessun addebito per abilitare l'eliminazione temporanea del BLOB per un account di archiviazione. I dati in un BLOB eliminato temporaneamente vengono fatturati alla stessa frequenza dei dati attivi fino a quando il BLOB eliminato temporaneamente non viene eliminato definitivamente. |
| Ripristino temporizzato | Nessun addebito per abilitare il ripristino temporizzato per un account di archiviazione; Tuttavia, l'abilitazione del ripristino temporizzato Abilita anche il controllo delle versioni dei BLOB, l'eliminazione temporanea e il feed delle modifiche, ciascuno dei quali può comportare addebiti aggiuntivi.<br /><br />Quando si esegue un'operazione di ripristino viene addebitato il ripristino temporizzato. Il costo di un'operazione di ripristino dipende dalla quantità di dati da ripristinare. Per ulteriori informazioni, vedere [prezzi e fatturazione](point-in-time-restore-overview.md#pricing-and-billing). |
| Snapshot BLOB | I dati in uno snapshot vengono fatturati in base a blocchi o pagine univoche. I costi aumentano pertanto con la divergenza tra il BLOB di base e lo snapshot. La modifica del livello di un BLOB o di uno snapshot può avere un effetto sulla fatturazione. Per ulteriori informazioni, vedere [prezzi e fatturazione](snapshots-overview.md#pricing-and-billing).<br /><br />Usare la gestione del ciclo di vita per eliminare gli snapshot meno recenti necessari per controllare i costi. Per altre informazioni, vedere [ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure](storage-lifecycle-management-concepts.md). |
| Copiare i dati in un secondo account di archiviazione | La gestione dei dati in un secondo account di archiviazione comporterà costi di capacità e di transazione. Se il secondo account di archiviazione si trova in un'area diversa da quella dell'account di origine, la copia dei dati nel secondo account comporterà inoltre costi in uscita. |

## <a name="disaster-recovery"></a>Ripristino di emergenza

Archiviazione di Azure mantiene sempre più copie dei dati in modo che siano protette da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e calamità naturali di grandi dimensioni. La ridondanza garantisce che l'account di archiviazione soddisfi gli obiettivi di disponibilità e durabilità anche in caso di errori. Per altre informazioni su come configurare l'account di archiviazione per la disponibilità elevata, vedere [ridondanza di archiviazione di Azure](../common/storage-redundancy.md).

Nel caso in cui si verifichi un errore in un data center, se l'account di archiviazione è ridondante tra due aree geografiche (con ridondanza geografica), è possibile eseguire il failover dell'account dall'area primaria all'area secondaria. Per altre informazioni, vedere [Ripristino di emergenza e failover dell'account di archiviazione](../common/storage-disaster-recovery-guidance.md).

Il failover gestito dal cliente non è attualmente supportato per gli account di archiviazione con uno spazio dei nomi gerarchico abilitato. Per altre informazioni, vedere [funzionalità di archiviazione BLOB disponibili in Azure Data Lake storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Passaggi successivi

- [Azure Storage redundancy](../common/storage-redundancy.md) (Ridondanza di Archiviazione di Azure)
- [Ripristino di emergenza e failover dell'account di archiviazione](../common/storage-disaster-recovery-guidance.md)
