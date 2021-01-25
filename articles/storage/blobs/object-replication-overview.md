---
title: Panoramica della replica degli oggetti
titleSuffix: Azure Storage
description: La replica di oggetti copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e un account di destinazione. Usare la replica di oggetti per ridurre al minimo la latenza sulle richieste di lettura, per aumentare l'efficienza per i carichi di lavoro di calcolo, per ottimizzare la distribuzione dei dati e per ridurre al minimo i costi.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7fa6b1ee7c92f82c3e15335991f5a240c7acc52
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762882"
---
# <a name="object-replication-for-block-blobs"></a>Replica di oggetti per BLOB in blocchi

La replica di oggetti copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e un account di destinazione. Alcuni scenari supportati dalla replica di oggetti includono:

- **Riduzione al minimo della latenza.** La replica di oggetti può ridurre la latenza per le richieste di lettura consentendo ai client di usare i dati di un'area con prossimità fisica più stretta.
- **Aumento dell'efficienza per i carichi di lavoro di calcolo.** Con la replica di oggetti, i carichi di lavoro di calcolo possono elaborare gli stessi set di BLOB in blocchi in aree diverse.
- **Ottimizzazione della distribuzione dei dati.** È possibile elaborare o analizzare i dati in un'unica posizione e quindi replicare solo i risultati in aree aggiuntive.
- **Ottimizzazione dei costi.** Una volta che i dati sono stati replicati, è possibile ridurre i costi spostandoli nel livello archivio usando i criteri di gestione del ciclo di vita.

Il diagramma seguente illustra in che modo la replica di oggetti replica i BLOB in blocchi da un account di archiviazione di origine in un'area ad account di destinazione in due aree diverse.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramma che illustra il funzionamento della replica di oggetti":::

Per informazioni su come configurare la replica degli oggetti, vedere [configurare la replica](object-replication-configure.md)degli oggetti.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Prerequisiti per la replica di oggetti

Per la replica di oggetti è necessario che siano abilitate anche le funzionalità di archiviazione di Azure seguenti:

- [Feed delle modifiche](storage-blob-change-feed.md): è necessario abilitare l'account di origine. Per informazioni su come abilitare il feed delle modifiche, vedere [abilitare e disabilitare il feed delle modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Controllo delle versioni dei BLOB](versioning-overview.md): deve essere abilitato sia per gli account di origine che per quelli di destinazione. Per informazioni su come abilitare il controllo delle versioni, vedere [abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md).

L'abilitazione del feed di modifiche e del controllo delle versioni dei BLOB può comportare costi aggiuntivi. Per informazioni più dettagliate, fare riferimento alla pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-object-replication-works"></a>Funzionamento della replica degli oggetti

La replica di oggetti copia in modo asincrono i BLOB in blocchi in un contenitore in base alle regole configurate. Il contenuto del BLOB, le eventuali versioni associate al BLOB e i metadati e le proprietà del BLOB vengono copiati dal contenitore di origine al contenitore di destinazione.

> [!IMPORTANT]
> Poiché i dati BLOB in blocchi vengono replicati in modo asincrono, l'account di origine e l'account di destinazione non sono immediatamente sincronizzati. Al momento non esiste alcun contratto di servizio per il tempo necessario per replicare i dati nell'account di destinazione. Per determinare se la replica è stata completata, è possibile controllare lo stato della replica nel BLOB di origine. Per altre informazioni, vedere [controllare lo stato di replica di un BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Controllo delle versioni dei BLOB

Per la replica di oggetti è necessario che il controllo delle versioni dei BLOB sia abilitato sia negli account di origine che in quelli di destinazione Quando viene modificato un BLOB replicato nell'account di origine, viene creata una nuova versione del BLOB nell'account di origine che riflette lo stato precedente del BLOB, prima della modifica. La versione corrente (o BLOB di base) nell'account di origine riflette gli aggiornamenti più recenti. Sia la versione aggiornata che la nuova versione precedente vengono replicate nell'account di destinazione. Per altre informazioni su come le operazioni di scrittura influiscono sulle versioni dei BLOB, vedere [controllo delle versioni nelle operazioni di scrittura](versioning-overview.md#versioning-on-write-operations).

Quando viene eliminato un BLOB nell'account di origine, la versione corrente del BLOB viene acquisita in una versione precedente e quindi eliminata. Tutte le versioni precedenti del BLOB vengono mantenute anche dopo l'eliminazione della versione corrente. Questo stato viene replicato nell'account di destinazione. Per altre informazioni su come le operazioni di eliminazione influiscono sulle versioni dei BLOB, vedere [controllo delle versioni in operazioni di eliminazione](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Snapshot

La replica di oggetti non supporta snapshot BLOB. Gli snapshot di un BLOB nell'account di origine non vengono replicati nell'account di destinazione.

### <a name="blob-tiering"></a>Suddivisione in livelli BLOB

La replica di oggetti è supportata quando gli account di origine e di destinazione si trovano nel livello ad accesso frequente o sporadico. Gli account di origine e di destinazione possono trovarsi in livelli diversi. Tuttavia, la replica di oggetti avrà esito negativo se un BLOB nell'account di origine o di destinazione è stato spostato nel livello archivio. Per altre informazioni sui livelli BLOB, vedere [livelli di accesso per l'archiviazione BLOB di Azure: frequente,](storage-blob-storage-tiers.md)accesso sporadico e archivio.

### <a name="immutable-blobs"></a>BLOB non modificabili

La replica di oggetti non supporta i BLOB non modificabili. Se un contenitore di origine o di destinazione ha un criterio di conservazione basato sul tempo o un periodo di attesa legale, la replica dell'oggetto ha esito negativo. Per altre informazioni sui BLOB non modificabili, vedere [archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md).

## <a name="object-replication-policies-and-rules"></a>Regole e criteri di replica di oggetti

Quando si configura la replica di oggetti, si crea un criterio di replica che specifica l'account di archiviazione di origine e l'account di destinazione. Un criterio di replica include una o più regole che specificano un contenitore di origine e un contenitore di destinazione e indicano i BLOB in blocchi del contenitore di origine che verranno replicati.

Dopo aver configurato la replica di oggetti, Archiviazione di Azure controlla periodicamente il feed di modifiche per l'account di origine e replica in modo asincrono le operazioni di scrittura o eliminazione nell'account di destinazione. La latenza di replica dipende dalla dimensione del BLOB in blocchi da replicare.

### <a name="replication-policies"></a>Criteri di replica

Quando si configura la replica di oggetti, viene creato un criterio di replica sia nell'account di origine che nell'account di destinazione tramite il provider di risorse di Archiviazione di Azure. Il criterio di replica è identificato da un ID criterio. Il criterio negli account di origine e di destinazione deve avere lo stesso ID criterio per poter eseguire la replica.

Un account di archiviazione può fungere da account di origine per un massimo di due account di destinazione. Gli account di origine e di destinazione possono trovarsi nella stessa area o in aree diverse. Possono inoltre trovarsi in sottoscrizioni diverse e in tenant di Azure Active Directory (Azure AD) diversi. È possibile creare un solo criterio di replica per ogni coppia account di origine/account di destinazione.

### <a name="replication-rules"></a>Regole di replica

Le regole di replica specificano in che modo Archiviazione di Azure replica i BLOB da un contenitore di origine a un contenitore di destinazione. È possibile specificare fino a 10 regole di replica per ogni criterio di replica. Ogni regola di replica definisce un singolo contenitore di origine e di destinazione e ogni contenitore di origine e di destinazione può essere utilizzato solo in una regola.

Quando si crea una regola di replica, per impostazione predefinita vengono copiati solo i nuovi BLOB in blocchi aggiunti successivamente al contenitore di origine. È possibile specificare che vengono copiati sia i BLOB in blocchi nuovi che quelli esistenti oppure è possibile definire un ambito di copia personalizzato che copia i BLOB in blocchi creati a partire dall'ora specificata.

È anche possibile specificare uno o più filtri come parte di una regola di replica per filtrare i BLOB in blocchi per prefisso. Quando si specifica un prefisso, solo i BLOB corrispondenti al prefisso nel contenitore di origine verranno copiati nel contenitore di destinazione.

Devono esistere sia i contenitori di origine che di destinazione prima di poterli specificare in una regola. Dopo aver creato il criterio di replica, non sono consentite operazioni di scrittura nel contenitore di destinazione. Qualsiasi tentativo di scrittura nel contenitore di destinazione non riesce e viene restituito il codice errore 409 (conflitto). Per scrivere in un contenitore di destinazione per cui è configurata una regola di replica, è necessario eliminare la regola configurata per tale contenitore oppure rimuovere il criterio di replica. Le operazioni di lettura ed eliminazione nel contenitore di destinazione sono consentite quando il criterio di replica è attivo.

È possibile chiamare l'operazione di [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) su un BLOB nel contenitore di destinazione per spostarla nel livello archivio. Per altre informazioni sul livello archivio, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="replication-status"></a>Stato della replica

È possibile controllare lo stato di replica di un BLOB nell'account di origine. Per altre informazioni, vedere [controllare lo stato di replica di un BLOB](object-replication-configure.md#check-the-replication-status-of-a-blob).

Se lo stato di replica di un BLOB nell'account di origine indica un errore, esaminare le possibili cause seguenti:

- Verificare che i criteri di replica degli oggetti siano configurati nell'account di destinazione.
- Verificare che il contenitore di destinazione esista ancora.
- Se il BLOB di origine è stato crittografato con una chiave fornita dal cliente come parte di un'operazione di scrittura, la replica degli oggetti avrà esito negativo. Per altre informazioni sulle chiavi fornite dal cliente, vedere [fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](encryption-customer-provided-keys.md).

## <a name="billing"></a>Fatturazione

La replica di oggetti comporta costi aggiuntivi per le transazioni di lettura e scrittura rispetto agli account di origine e di destinazione, nonché gli addebiti in uscita per la replica dei dati dall'account di origine all'account di destinazione e la lettura degli addebiti per l'elaborazione del feed delle modifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la replica di oggetti](object-replication-configure.md)
- [Supporto del feed delle modifiche nell'archiviazione BLOB di Azure](storage-blob-change-feed.md)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
