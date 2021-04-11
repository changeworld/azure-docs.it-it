---
title: Livelli di accesso per archiviazione BLOB di Azure-accesso frequente, ad accesso sporadico e archivio
description: Informazioni sui livelli di accesso ad accesso frequente, ad accesso sporadico e archivio per archiviazione BLOB di Azure. Esaminare gli account di archiviazione che supportano la suddivisione in livelli.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 957973cc4f53dba10ed9d635c8e3f69fd66ee33b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278423"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Livelli di accesso per archiviazione BLOB di Azure-accesso frequente, ad accesso sporadico e archivio

Archiviazione di Azure offre livelli di accesso diversi, consentendo di archiviare i dati degli oggetti BLOB in modo più conveniente. I livelli di accesso disponibili includono:

-  Ottimizzato per l'archiviazione dei dati a cui si accede di frequente.
- **Raffreddamento** ottimizzato per l'archiviazione dei dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni.
- **Archivio** : ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili, nell'ordine di ore.

Le considerazioni seguenti sono applicabili ai diversi livelli di accesso:

- Il livello di accesso può essere impostato su un BLOB durante o dopo il caricamento.
- Solo i livelli di accesso sporadico e frequente possono essere impostati a livello di account. Il livello di accesso dell'archivio può essere impostato solo a livello di BLOB.
- I dati nel livello ad accesso sporadico hanno una disponibilità leggermente inferiore, ma hanno comunque una durabilità elevata, una latenza di recupero e caratteristiche di velocità effettiva simili a quelle dei dati attivi. Per i dati ad accesso sporadico, la disponibilità leggermente inferiore e i costi di accesso più elevati sono compromessi accettabili per ridurre i costi di archiviazione complessivi rispetto ai dati sensibili. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- I dati nel livello di accesso dell'archivio vengono archiviati offline. Il livello archivio offre i costi di archiviazione più bassi, ma anche i costi di accesso e la latenza più elevati.
- I livelli ad accesso frequente e sporadico supportano tutte le opzioni di ridondanza. Il livello archivio supporta solo con ridondanza locale, GRS e RA-GRS.
- I limiti di archiviazione dei dati vengono impostati a livello di account e non per livello di accesso. È possibile scegliere di usare tutto il limite in un livello o in tutti e tre i livelli.

La quantità di dati archiviati nel cloud è in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi in base alle modalità di generazione, elaborazione e accesso per tutta la loro durata. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi nel cloud e dopo l'archiviazione l'accesso a tali dati viene eseguito raramente o mai.

Ognuno di questi scenari di accesso ai dati trae vantaggio da un livello di accesso diverso, ottimizzato per un particolare modello di accesso. Con i livelli di accesso ad accesso frequente, sporadico e archivio, l'archiviazione BLOB di Azure risponde a questa esigenza di livelli di accesso differenziati con modelli di prezzi distinti.

Gli strumenti e le librerie client seguenti supportano tutte la suddivisione in livelli e l'archiviazione di archiviazione a livello di BLOB.

- Portale di Azure
- PowerShell
- Strumenti dell'interfaccia della riga di comando Azure
- Libreria client .NET
- Libreria client Java
- Libreria client Python
- Libreria client di Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

La suddivisione in livelli dei dati di archiviazione degli oggetti tra accesso frequente, accesso sporadico e archivio è supportata negli account di archiviazione BLOB e per utilizzo generico V2 (GPv2). Gli account per utilizzo generico V1 (utilizzo generico V1) non supportano la suddivisione in livelli. È possibile convertire facilmente gli account di archiviazione BLOB o utilizzo generico V1 esistenti in account GPv2 tramite il portale di Azure. GPv2 offre nuovi prezzi e funzionalità per BLOB, file e code. Alcune funzionalità e i tagli dei prezzi sono offerti solo negli account GPv2. Alcuni carichi di lavoro possono essere più costosi in GPv2 rispetto a utilizzo generico V1. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Gli account di archiviazione BLOB e GPv2 espongono l'attributo **livello di accesso** a livello di account. Questo attributo consente di specificare il livello di accesso predefinito per qualsiasi BLOB che non è impostato in modo esplicito a livello di oggetto. Per gli oggetti con il livello impostato in modo esplicito, il livello account non verrà applicato. Il livello archivio può essere applicato solo a livello di oggetto. È possibile passare tra i livelli di accesso in qualsiasi momento.

Usare GPv2 anziché gli account di archiviazione BLOB per la suddivisione in livelli. GPv2 supporta tutte le funzionalità supportate dagli account di archiviazione BLOB e molto altro ancora. I prezzi tra l'archiviazione BLOB e GPv2 sono quasi identici, ma alcune nuove funzionalità e tagli dei prezzi sono disponibili solo negli account GPv2.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Livello di accesso frequente

Il livello di accesso frequente presenta costi di archiviazione più elevati rispetto ai livelli di accesso sporadico e archiviazione, ma i costi di accesso più bassi. Ecco alcuni scenari di esempio per il livello di accesso frequente:

- I dati in uso attivo o che devono essere letti e scritti frequentemente
- Dati gestiti temporaneamente per l'elaborazione e l'eventuale migrazione al livello di accesso sporadico

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di accesso sporadico presenta costi di archiviazione più bassi e costi di accesso più alti rispetto all'archiviazione ad accesso frequente. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Ecco alcuni scenari di esempio per il livello di accesso sporadico:

- Backup a breve termine e ripristino di emergenza
- I dati meno recenti non vengono usati di frequente ma dovrebbero essere disponibili immediatamente quando si accede
- Set di dati di grandi dimensioni che devono essere archiviati in modo conveniente, mentre più dati vengono raccolti per l'elaborazione futura

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di accesso all'archivio presenta il costo di archiviazione più basso, ma i costi di recupero dei dati più elevati rispetto ai livelli ad accesso frequente e sporadico. I dati devono rimanere nel livello di archiviazione per almeno 180 giorni oppure essere soggetti a un addebito per l'eliminazione anticipata. Il recupero dei dati nel livello archivio può richiedere diverse ore a seconda della priorità di reidratazione specificata. Per gli oggetti di piccole dimensioni, un reidratante con priorità alta può recuperare l'oggetto dall'archivio in meno di un'ora. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md) .

Mentre un BLOB si trova nell'archiviazione dell'archivio, i dati BLOB sono offline e non possono essere letti o modificati. Per leggere o scaricare un BLOB in archivio, è innanzitutto necessario riattivarlo in un livello online. Non è possibile creare snapshot di un BLOB nella risorsa di archiviazione dell'archivio. Tuttavia, i metadati del BLOB rimangono online e disponibili, consentendo di elencare il BLOB, le relative proprietà, i metadati e i tag dell'indice BLOB. L'impostazione o la modifica dei metadati del BLOB in archivio non è consentita. Tuttavia, è possibile impostare e modificare i tag dell'indice BLOB. Per i BLOB nell'archivio, le uniche operazioni valide sono [recuperare le proprietà del BLOB](/rest/api/storageservices/get-blob-properties), ottenere i [metadati dei BLOB](/rest/api/storageservices/get-blob-metadata), impostare i [tag BLOB](/rest/api/storageservices/set-blob-tags), [ottenere i tag BLOB](/rest/api/storageservices/get-blob-tags), [trovare i BLOB in base ai tag](/rest/api/storageservices/find-blobs-by-tags), [elencare](/rest/api/storageservices/list-blobs)i BLOB, [impostare il livello BLOB](/rest/api/storageservices/set-blob-tier), [copiare BLOB](/rest/api/storageservices/copy-blob)ed [eliminare BLOB](/rest/api/storageservices/delete-blob).

Gli scenari di utilizzo di esempio per il livello di accesso dell'archivio includono:

- Set di dati di archiviazione, backup secondario e backup a lungo termine
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale
- Dati di conformità e di archiviazione che devono essere archiviati da molto tempo e difficilmente accessibili

> [!NOTE]
> Il livello archivio non è supportato per gli account ZRS, GZRS o RA-GZRS. La migrazione da con ridondanza locale a GRS è supportata purché nessun BLOB sia stato spostato nel livello archivio mentre l'account è stato impostato su con ridondanza locale. Un account può essere spostato di nuovo in GRS se l'aggiornamento viene eseguito per meno di 30 giorni dal momento in cui l'account è diventato con ridondanza locale e nessun BLOB è stato spostato nel livello archivio mentre l'account è stato impostato su con ridondanza locale.

## <a name="account-level-tiering"></a>Suddivisione in livelli a livello di account

I BLOB in tutti e tre i livelli di accesso possono coesistere nello stesso account. Qualsiasi BLOB a cui non è assegnato un livello in modo esplicito deduce il livello dall'impostazione del livello di accesso dell'account. Se il livello di accesso deriva dall'account, viene visualizzata la proprietà del BLOB **dedotto del livello di accesso** impostato su "true" e la proprietà BLOB **livello di accesso** corrisponde al livello dell'account. Nella portale di Azure la proprietà del _livello di accesso dedotto_ viene visualizzata con il livello di accesso BLOB come **attivo (dedotto)** o sporadico **(dedotto)**.

La modifica del livello di accesso dell'account si applica a tutti gli oggetti _dedotti del livello di accesso_ archiviati nell'account che non dispongono di un set di livelli esplicito. Se si attiva o disattiva il livello account da accesso frequente a sporadico, verranno addebitate le operazioni di scrittura (per 10.000) per tutti i BLOB senza un livello di impostazione solo per gli account GPv2. Non sono previsti addebiti per questa modifica negli account di archiviazione BLOB. Verranno addebitate le operazioni di lettura (per 10.000) e il recupero dati (per GB) se si passa da accesso sporadico ad accesso frequente in account di archiviazione BLOB o GPv2.

È possibile impostare solo livelli di accesso frequente e sporadico come livello di accesso dell'account predefinito. L'archivio può essere impostato solo a livello di oggetto. Al caricamento di BLOB, è possibile specificare il livello di accesso desiderato, ad accesso frequente, ad accesso sporadico o archivio, indipendentemente dal livello di account predefinito. Questa funzionalità consente di scrivere dati direttamente nel livello Archivio per realizzare risparmi sui costi dal momento in cui si creano i dati nell'archivio BLOB.

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

La suddivisione in livelli a livello di BLOB consente di caricare i dati nel livello di accesso desiderato usando le operazioni [Put Blob](/rest/api/storageservices/put-blob) o [Put Block List](/rest/api/storageservices/put-block-list) e modificare il livello dei dati a livello di oggetto usando la funzionalità [set BLOB](/rest/api/storageservices/set-blob-tier) Operation o [Lifecycle Management](#blob-lifecycle-management) . È possibile caricare i dati nel livello di accesso richiesto, quindi modificare facilmente il livello di accesso BLOB tra i livelli ad accesso frequente, ad accesso sporadico o archivio, in quanto i modelli di utilizzo cambiano, senza dover spostare i dati tra gli account. Tutte le richieste di modifica del livello si verificano immediatamente e le modifiche al livello tra accesso frequente e accesso sporadico sono La riattivazione di un BLOB dal livello archivio può richiedere diverse ore.

L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). L' **ora di modifica del livello di accesso** è una proprietà a livello di BLOB e non viene aggiornata quando viene modificato il livello di account predefinito. Le proprietà dell'account e le proprietà del BLOB sono separate. Sarebbe troppo costoso aggiornare l' **ora di modifica del livello di accesso** in ogni BLOB in un account di archiviazione ogni volta che viene modificato il livello di accesso predefinito dell'account.

Quando si sovrascrive un BLOB nel livello ad accesso frequente o sporadico, il BLOB appena creato eredita il livello del BLOB che è stato sovrascritto, a meno che il nuovo livello di accesso al BLOB non venga impostato in modo esplicito al momento della creazione. Se un BLOB si trova nel livello archivio, non può essere sovrascritto, quindi il caricamento dello stesso BLOB non è consentito in questo scenario.

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi.

### <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole che è possibile usare per passare i dati al livello di accesso migliore e per scadere i dati alla fine del ciclo di vita. Vedere [ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure](storage-lifecycle-management-concepts.md) per altre informazioni.

> [!NOTE]
> I dati archiviati in un account di archiviazione BLOB in blocchi (prestazioni Premium) attualmente non possono essere suddivisi in livelli ad accesso frequente, sporadico o archivio usando l' [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) o la gestione del ciclo di vita dell'archiviazione BLOB di Azure
> Per spostare i dati, è necessario copiare in modo sincrono i BLOB dall'account di archiviazione BLOB in blocchi al livello di accesso frequente in un account diverso usando il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) o una versione di AzCopy che supporta questa API.
> L'API **Put Block From URL** copia in modo sincrono i dati nel server, vale a dire che la chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene caricato o spostato tra i livelli, viene addebitata la tariffa corrispondente immediatamente al momento del caricamento o della modifica del livello.

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione, dove vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB).

Quando un BLOB viene spostato in un livello più caldo (archivio->sporadico, >ad accesso frequente o ad accesso sporadico >frequente), l'operazione viene fatturata come lettura dal livello di origine, in cui si applicano le operazioni di lettura (per 10.000) e il recupero dati (per GB) del livello di origine. Potrebbero essere applicati anche gli addebiti per l' [eliminazione anticipata](#cool-and-archive-early-deletion) per qualsiasi BLOB spostato fuori dal livello di accesso sporadico o archivio. La riattivazione [dei dati dal livello archivio](storage-blob-rehydration.md) richiede tempo e i prezzi dell'archivio verranno addebitati fino a quando i dati non vengono ripristinati online e il livello BLOB diventa frequente o sporadico.

La tabella seguente riepiloga come vengono fatturati i vari livelli.

| | **Addebiti per scrittura (operazione + accesso)** | **Addebiti per la lettura (operazione + accesso)** |
| ---- | ----- | ----- |
| **Set Blob Tier** | > ad accesso frequente<br> Archivio a caldo ><br> Archivio > cool | Archivio-> cool<br> Archivio-> attivo<br> Cool-> Hot

### <a name="cool-and-archive-early-deletion"></a>Eliminazione anticipata per accesso sporadico o archivio

Qualsiasi BLOB spostato nel livello di accesso sporadico (solo account GPv2) è soggetto a un periodo di eliminazione anticipata di 30 giorni. Qualsiasi BLOB spostato nel livello archivio è soggetto a un periodo di eliminazione anticipata di archiviazione di 180 giorni. Questo addebito è ripartito proporzionalmente. Se, ad esempio, un BLOB viene spostato in archivio e quindi eliminato o spostato al livello di accesso frequente dopo 45 giorni, viene addebitata una tariffa per eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nell'archivio.

Sono disponibili alcuni dettagli quando si passa da un livello di archiviazione ad accesso sporadico all'altra:

1. Se un BLOB viene dedotto come sporadico in base al livello di accesso predefinito dell'account di archiviazione e il BLOB viene spostato in archivio, non è previsto alcun addebito per l'eliminazione anticipata.
1. Se un BLOB viene spostato in modo esplicito nel livello di accesso sporadico e quindi spostato nell'archivio, viene applicato l'addebito per l'eliminazione anticipata.

Calcolare l'ora di eliminazione anticipata usando la proprietà BLOB dell' **Ultima modifica** , se non sono state apportate modifiche al livello di accesso. In caso contrario, usare quando il livello di accesso è stato modificato l'ultima volta in un ambiente sporadico o archivio visualizzando la proprietà BLOB: **Access-Tier-Change-Time**. Per altre informazioni sulle proprietà di BLOB, vedere l'articolo relativo all'operazione [Get Blob Properties](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Confronto tra le opzioni di archiviazione BLOB in blocchi

La tabella seguente illustra un confronto tra l'archiviazione BLOB in blocchi di prestazioni Premium e i livelli di accesso ad accesso frequente, ad accesso sporadico e archivio.

|                                           | **Prestazioni Premium**   | **Livello critico** | **Livello ad accesso sporadico**       | **Livello archivio**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilità**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilità** <br> **(Letture RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Addebiti per l'utilizzo**                         | Costi di archiviazione più elevati, accesso inferiore e costo della transazione | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Durata archiviazione minima**              | N/D                       | N/D          | 30 giorni<sup>1</sup> | 180 giorni
| **Latency** <br> **(Tempo per il primo byte)** | Millisecondi a singola cifra | millisecondi | millisecondi        | ore<sup>2</sup> |

<sup>1</sup> gli oggetti nel livello di accesso sporadico per gli account GPv2 hanno una durata di conservazione minima di 30 giorni. Gli account di archiviazione BLOB non hanno una durata minima di conservazione per il livello di accesso sporadico.

<sup>2</sup> spazio di archiviazione supporta attualmente due priorità di riattivazione, alta e standard, che offrono latenze e costi diversi per il recupero. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md).

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione per utilizzo generico V2. Per altre informazioni, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](scalability-targets.md).

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti gli account di archiviazione usano un modello di determinazione dei prezzi per l'archiviazione BLOB in blocchi in base al livello di ogni BLOB. Tenere presenti le considerazioni seguenti sulla fatturazione:

- **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.
- **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nel livello di accesso ad accesso sporadico e archivio, viene addebitato un costo di accesso ai dati per Gigabyte per le letture.
- **Costi delle transazioni**: è previsto un addebito per transazione per tutti i livelli che aumenta man mano che il livello diventa più interessante.
- **Costi di trasferimento dati con replica geografica**: questo addebito si applica solo agli account con la replica geografica configurata, inclusi GRS e RA-GRS. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.
- **Modificando il** livello di accesso: la modifica del livello di accesso dell'account comporterà un addebito per tutti i BLOB che non dispongono di un set di livelli esplicito. Per informazioni sulla modifica del livello di accesso per un singolo BLOB, vedere [fatturazione a livelli a livello di BLOB](#blob-level-tiering-billing).

    Per modificare il livello di accesso per un BLOB quando è abilitato il controllo delle versioni o se il BLOB contiene snapshot, può comportare addebiti aggiuntivi. Per informazioni sui BLOB con il controllo delle versioni abilitato, vedere [prezzi e fatturazione](versioning-overview.md#pricing-and-billing) nella documentazione relativa al controllo delle versioni dei BLOB. Per informazioni sui BLOB con snapshot, vedere [prezzi e fatturazione](snapshots-overview.md#pricing-and-billing) nella documentazione snapshot BLOB.

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi, vedere [prezzi dei BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/). Per ulteriori informazioni sugli addebiti per il trasferimento di dati in uscita, vedere la pagina [Dettagli prezzi](https://azure.microsoft.com/pricing/details/bandwidth/) .

## <a name="availability"></a>Disponibilità

In aree selezionate sono disponibili diversi livelli di accesso, insieme a livelli a livello di BLOB. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire i BLOB e gli account tra i livelli di accesso.

- [Come gestire il livello di un BLOB in un account di archiviazione di Azure](manage-access-tier.md)
- [Come gestire il livello di accesso dell'account predefinito di un account di archiviazione di Azure](../common/manage-account-default-access-tier.md)
- [Ottimizzare i costi automatizzando i livelli di accesso all'archivio BLOB di Azure](storage-lifecycle-management-concepts.md)
