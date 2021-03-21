---
title: Backup continuo con funzionalità di ripristino temporizzato in Azure Cosmos DB
description: La funzionalità di ripristino temporizzato di Azure Cosmos DB consente di ripristinare i dati da un'operazione di scrittura, eliminazione accidentale o di ripristinare i dati in qualsiasi area. Informazioni sui prezzi e sulle limitazioni correnti.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393140"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Backup continuo con funzionalità di ripristino temporizzato (anteprima) in Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità di ripristino temporizzato di Azure Cosmos DB (anteprima) è utile in diversi scenari, ad esempio:

* Per eseguire il ripristino da un'operazione di scrittura o eliminazione accidentale all'interno di un contenitore.
* Per ripristinare un account eliminato, un database o un contenitore.
* Per eseguire il ripristino in qualsiasi area (in cui sono stati eseguiti i backup) al momento del ripristino.

Azure Cosmos DB esegue il backup dei dati in background senza richiedere la velocità effettiva (UR) con provisioning supplementare o influire sulle prestazioni e sulla disponibilità del database. I backup continui vengono eseguiti in ogni area in cui è presente l'account. La figura seguente mostra come un contenitore con area di scrittura negli Stati Uniti occidentali, le aree di lettura in est e negli Stati Uniti orientali 2 viene sottoposto a backup in un account di archiviazione BLOB di Azure remoto nelle rispettive aree. Per impostazione predefinita, ogni area archivia il backup negli account di archiviazione con ridondanza locale. Se per l'area sono abilitate le [zone di disponibilità](high-availability.md#availability-zone-support) , il backup viene archiviato in Zone-Redundant account di archiviazione.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB il backup dei dati nell'archivio BLOB di Azure." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

L'intervallo di tempo disponibile per il ripristino (noto anche come periodo di memorizzazione) è il valore inferiore dei due: *30 giorni dopo il ritorno da adesso* o *fino al momento della creazione delle risorse*. Il punto nel tempo per il ripristino può essere qualsiasi timestamp entro il periodo di memorizzazione.

Nella versione di anteprima pubblica è possibile ripristinare l'account Azure Cosmos DB per l'API SQL o i contenuti MongoDB temporizzati in un altro account usando [portale di Azure](continuous-backup-restore-portal.md), l' [interfaccia della riga di comando di Azure](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)o il [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Che cosa viene ripristinato?

In uno stato stabile, tutte le mutazioni eseguite sull'account di origine (che include database, contenitori ed elementi) vengono sottoposte a backup in modo asincrono entro 100 secondi. Se il supporto di backup, ovvero archiviazione di Azure, non è attivo o non è disponibile, le mutazioni vengono rese disponibili localmente fino a quando il supporto non è disponibile e quindi vengono scaricate per evitare la perdita di fedeltà delle operazioni che possono essere ripristinate.

È possibile scegliere di ripristinare qualsiasi combinazione di contenitori di velocità effettiva con provisioning, database di velocità effettiva condivisi o l'intero account. Questa azione ripristina tutti i dati e le relative proprietà di indice in un nuovo account. Il processo di ripristino garantisce che tutti i dati ripristinati in un account, un database o un contenitore siano coerenti fino all'ora di ripristino specificata. La durata del ripristino dipende dalla quantità di dati da ripristinare.

> [!NOTE]
> Con la modalità di backup continuo, i backup vengono eseguiti in ogni area in cui è disponibile l'account Azure Cosmos DB. I backup eseguiti per ogni account di area sono ridondanti localmente per impostazione predefinita e con ridondanza della zona se per l'account è abilitata la funzionalità della [zona di disponibilità](high-availability.md#availability-zone-support) . Con l'azione di ripristino i dati vengono sempre ripristinati in un nuovo account.

## <a name="what-is-not-restored"></a>Cosa non viene ripristinato?

Le seguenti configurazioni non vengono ripristinate dopo il recupero temporizzato:

* Impostazioni del firewall, del VNET, dell'endpoint privato.
* Impostazioni di coerenza. Per impostazione predefinita, l'account viene ripristinato con la coerenza di sessione.  
* Regioni.
* Stored procedure, trigger e funzioni definite dall'utente.

È possibile aggiungere queste configurazioni all'account ripristinato dopo il completamento del ripristino.

## <a name="restore-scenarios"></a>Scenari di ripristino

Di seguito sono riportati alcuni scenari chiave che vengono risolti dalla funzionalità di ripristino temporizzato. Gli scenari [a] tramite [c] dimostrano come attivare un ripristino se il timestamp di ripristino è noto in anticipo.
Tuttavia, potrebbero verificarsi scenari in cui non si conosce l'ora esatta dell'eliminazione accidentale o del danneggiamento. Scenari [d] e [e] illustrano come _individuare_ il timestamp di ripristino usando le nuove API del feed di eventi nel database o nei contenitori ripristinabili.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Eventi del ciclo di vita con timestamp per un account ripristinabile." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Ripristina account eliminato** : tutti gli account eliminati che è possibile ripristinare sono visibili dal riquadro **ripristino** . Ad esempio, se *l'account a* viene eliminato al timestamp T3. In questo caso il timestamp appena prima della T3, della località, del nome dell'account di destinazione, del gruppo di risorse e del nome dell'account di destinazione è sufficiente per il ripristino da [portale di Azure](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)o l' [interfaccia](continuous-backup-restore-command-line.md#trigger-restore)della riga di comando.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Eventi del ciclo di vita con timestamp per un database e un contenitore ripristinabili." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Ripristinare i dati di un account in una determinata area** , ad esempio se *l'account a* è presente in due aree *Stati Uniti orientali* e *Stati Uniti occidentali* al timestamp T3. Se è necessaria una copia dell'account A negli *Stati Uniti occidentali*, è possibile eseguire un ripristino temporizzato dall' [portale di Azure](continuous-backup-restore-portal.md), da [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)o dall' [interfaccia](continuous-backup-restore-command-line.md#trigger-restore) della riga di comando con Stati Uniti occidentali come percorso di destinazione.

c. Eseguire il **ripristino da un'operazione di scrittura o eliminazione accidentale all'interno di un contenitore con un timestamp di ripristino noto** , ad esempio se si è **certi** che il contenuto del *contenitore 1* nel *database 1* è stato modificato accidentalmente al timestamp T3. È possibile eseguire un ripristino temporizzato dall' [portale di Azure](continuous-backup-restore-portal.md#restore-live-account), da [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)o dall' [interfaccia](continuous-backup-restore-command-line.md#trigger-restore) della riga di comando a un altro account in timestamp T3 per ripristinare lo stato desiderato del contenitore.

d. **Ripristinare un account a un momento precedente prima dell'eliminazione accidentale del database** : nella [portale di Azure](continuous-backup-restore-portal.md#restore-live-account)è possibile utilizzare il riquadro feed eventi per determinare quando un database è stato eliminato e individuare l'ora di ripristino. Analogamente, con l'interfaccia della riga di comando di [Azure](continuous-backup-restore-command-line.md#trigger-restore) e [PowerShell](continuous-backup-restore-powershell.md#trigger-restore), è possibile individuare l'evento di eliminazione del database enumerando il feed degli eventi di database e quindi attivare il comando Restore con i parametri necessari.

e. **Ripristinare un account a un momento precedente prima dell'eliminazione accidentale o della modifica delle proprietà del contenitore.** -In [portale di Azure](continuous-backup-restore-portal.md#restore-live-account), è possibile usare il riquadro feed eventi per determinare quando un contenitore è stato creato, modificato o eliminato per trovare l'ora di ripristino. Analogamente, con l'interfaccia della riga di comando di [Azure](continuous-backup-restore-command-line.md#trigger-restore) e [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)è possibile individuare tutti gli eventi del contenitore enumerando il feed degli eventi del contenitore e quindi attivare il comando Restore con i parametri necessari.

## <a name="permissions"></a>Autorizzazioni

Azure Cosmos DB consente di isolare e limitare le autorizzazioni di ripristino per l'account di backup continuo a un ruolo specifico o a un'entità. Il proprietario dell'account può attivare un ripristino e assegnare un ruolo ad altre entità per eseguire l'operazione di ripristino. Per altre informazioni, vedere l'articolo [autorizzazioni](continuous-backup-restore-permissions.md) .

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Prezzi

Azure Cosmos DB gli account con backup continuo abilitato comporteranno un addebito mensile aggiuntivo per *archiviare il backup* e *ripristinare i dati*. Il costo di ripristino viene aggiunto ogni volta che viene avviata l'operazione di ripristino. Se si configura un account con backup continuo ma non si ripristinano i dati, nella fattura verranno inclusi solo i costi di archiviazione dei backup.

L'esempio seguente si basa sul prezzo di un account Azure Cosmos distribuito in un'area non governativa negli Stati Uniti. I prezzi e i calcoli possono variare a seconda dell'area in uso. per informazioni più aggiornate sui prezzi, vedere la pagina relativa ai [prezzi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

* Tutti gli account abilitati con criteri di backup continuo comportano un addebito mensile aggiuntivo per l'archiviazione di backup calcolato come segue:

  $0,20/GB * dimensioni dei dati in GB nell'account * numero di aree

* Ogni chiamata all'API di ripristino comporta un addebito di una sola volta. L'addebito è una funzione della quantità di ripristino dei dati e viene calcolato come segue:

  dimensioni dei dati di $0.15/GB * in GB.

Se, ad esempio, si dispone di 1 TB di dati in due aree:

* Il costo di archiviazione dei backup viene calcolato come (1000 * 0,20 * 2) = $400 al mese

* Il costo di ripristino viene calcolato come (1000 * 0,15) = $150 per ripristino

## <a name="current-limitations-public-preview"></a>Limitazioni correnti (anteprima pubblica)

Attualmente la funzionalità di ripristino temporizzato è disponibile in anteprima pubblica e presenta le limitazioni seguenti:

* Per il backup continuo sono supportate solo le API Azure Cosmos DB per SQL e MongoDB. Le API Cassandra, Tabella e Gremlin non sono ancora supportate.

* Non è possibile convertire un account esistente con criteri di backup periodici predefiniti per usare la modalità di backup continuo.

* Le aree del Cloud Azure Sovereign e Azure per enti pubblici non sono ancora supportate.

* Gli account con chiavi gestite dal cliente non sono supportati per l'utilizzo del backup continuo.

* Gli account di scrittura in più aree non sono supportati.

* Gli account con collegamento sinapsi abilitato non sono supportati.

* L'account ripristinato viene creato nella stessa area in cui risiede l'account di origine. Non è possibile ripristinare un account in un'area in cui l'account di origine non esisteva.

* La finestra di ripristino è costituita solo da 30 giorni e non può essere modificata.

* I backup non sono automaticamente resistenti al ripristino di emergenza geografico. È necessario aggiungere in modo esplicito un'altra area per ottenere la resilienza per l'account e il backup.

* Mentre è in corso un ripristino, non modificare o eliminare i criteri di gestione delle identità e degli accessi che concedono le autorizzazioni per l'account o modificare la configurazione del firewall VNET.

* Le API di Azure Cosmos DB per gli account SQL o MongoDB che creano un indice univoco dopo la creazione del contenitore non sono supportate per il backup continuo. Sono supportati solo i contenitori che creano un indice univoco nell'ambito della creazione del contenitore iniziale. Per gli account MongoDB, è possibile creare un indice univoco usando i [comandi di estensione](mongodb-custom-commands.md).

* La funzionalità di ripristino temporizzato esegue sempre il ripristino in un nuovo account Azure Cosmos. Il ripristino in un account esistente non è attualmente supportato. Se si è interessati a fornire commenti e suggerimenti sul ripristino sul posto, contattare il team di Azure Cosmos DB tramite il rappresentante dell'account o [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Tutte le nuove API esposte `RestorableDatabaseAccount` per `RestorableSqlDatabases` l'elenco,,, `RestorableSqlContainer` `RestorableMongodbDatabase` , `RestorableMongodbCollection` sono soggette a modifiche mentre la funzionalità è in anteprima.

* Dopo il ripristino, è possibile che per alcune raccolte l'indice coerente possa essere ricompilato. È possibile controllare lo stato dell'operazione di ricompilazione tramite la proprietà [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* Questo processo ripristina tutte le proprietà di un contenitore, inclusa la relativa configurazione TTL. Di conseguenza, è possibile che, se configurati in questo modo, i dati ripristinati vengano eliminati immediatamente. Per evitare questa situazione, il timestamp di ripristino deve essere precedente al momento in cui le proprietà TTL sono state aggiunte al contenitore.

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
