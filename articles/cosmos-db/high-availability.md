---
title: Disponibilità elevata in Azure Cosmos DB
description: In questo articolo viene descritto come Azure Cosmos DB garantisce disponibilità elevata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365366"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>In che modo Azure Cosmos DB disponibilità elevata
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB offre disponibilità elevata in due modi principali. In primo luogo, Azure Cosmos DB replica i dati tra aree configurate all'interno di un account Cosmos. In secondo Azure Cosmos DB gestisce 4 repliche di dati all'interno di un'area.

Azure Cosmos DB è un servizio di database distribuito a livello globale ed è un servizio di base disponibile in tutte le aree in cui [Azure è disponibile.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) È possibile associare un numero qualsiasi di aree di Azure al proprio account Azure Cosmos e i dati vengono replicati automaticamente e in modo trasparente. È possibile aggiungere o rimuovere un'area all'account Azure Cosmos in qualsiasi momento. Cosmos DB è disponibile in tutti i cinque ambienti cloud distinti di Azure disponibili per i clienti:

* Cloud **Azure pubblico**, disponibile a livello globale.

* **Azure China (21Vianet)** è disponibile tramite una partnership esclusiva tra Microsoft e 21Vianet, uno dei più grandi provider Internet del paese in Cina.

* **Azure Germania** fornisce servizi in base a un modello di trustee dei dati, che garantisce che i dati dei clienti rimangano in Germania sotto il controllo di T-Systems International GmbH, una filiale di Deutsche Telekom, che funge da trustee dei dati tedesco.

* **Azure per enti pubblici** è disponibile in quattro aree negli Stati Uniti per enti pubblici degli Stati Uniti e i rispettivi partner.

* **Azure per enti pubblici for Department of Defense (DoD)** è disponibile in due aree del Stati Uniti al Dipartimento della difesa degli Stati Uniti.

In un'area Azure Cosmos DB quattro copie dei dati come repliche all'interno di partizioni fisiche, come illustrato nell'immagine seguente:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partizionamento fisico" border="false":::

* I dati all'interno dei contenitori di Azure Cosmos sono [partizionati orizzontalmente.](partitioning-overview.md)

* Un set di partizioni è una raccolta di più set di repliche. All'interno di ogni area, ogni partizione è protetta da un set di repliche con tutte le scritture replicate e sottoposte a commit permanente dalla maggior parte delle repliche. Le repliche sono distribuite in 10-20 domini di errore.

* Ogni partizione in tutte le aree viene replicata. Ogni area contiene tutte le partizioni di dati di un contenitore Azure Cosmos e può servire le operazioni di lettura e le scritture quando sono abilitate le scritture in più aree.  

Se l'account Azure Cosmos viene distribuito tra *N* aree di Azure, saranno presenti almeno *N* x 4 copie di tutti i dati. La disponibilità di un account Azure Cosmos in più di 2 aree migliora la disponibilità dell'applicazione e offre bassa latenza tra le aree associate.

## <a name="slas-for-availability"></a>Contratti di servizio per la disponibilità

Azure Cosmos DB offre contratti di servizio completi che includono velocità effettiva, latenza al 99° percentile, coerenza e disponibilità elevata. La tabella seguente illustra le garanzie di disponibilità elevata fornite da Azure Cosmos DB per gli account singoli e in più aree. Per una maggiore disponibilità di scrittura, configurare l'account Azure Cosmos in modo che abbia più aree di scrittura.

|Tipo di operazione  | Area singola |Più aree (scritture a area singola)|Più aree (scrittura in più aree) |
|---------|---------|---------|-------|
|Scritture    | 99,99    |99,99   |99,999|
|Letture     | 99,99    |99,999  |99,999|

> [!NOTE]
> In pratica, la disponibilità effettiva di scrittura per delimitazione, sessione, prefisso coerente e modelli di coerenza finale è significativamente superiore rispetto ai contratti di servizio pubblicati. La disponibilità effettiva di lettura per tutti i livelli di coerenza è notevolmente superiore a quella prevista dai contratti di servizio pubblicati.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Disponibilità elevata con Azure Cosmos DB in caso di interruzioni a livello di regione

Per i rari casi di interruzione a livello di Azure Cosmos DB, assicurarsi che il database sia sempre a disponibilità elevata. I dettagli seguenti acquisiscono Azure Cosmos DB comportamento durante un'interruzione, a seconda della configurazione dell'account Azure Cosmos:

* Con Azure Cosmos DB, prima che un'operazione di scrittura venga riconosciuta al client, il commit dei dati viene eseguito in modo durevole da un quorum di repliche all'interno dell'area che accetta le operazioni di scrittura. Per altri dettagli, vedere [Livelli di coerenza e velocità effettiva](./consistency-levels.md#consistency-levels-and-throughput)

* Gli account in più aree configurati con aree a scrittura multipla rimarranno a disponibilità elevata sia per le operazioni di scrittura che per le operazioni di lettura. I failover a livello di regione vengono rilevati e gestiti nel client Azure Cosmos DB locale. Sono anche istantanei e non richiedono modifiche dall'applicazione.

* È possibile che gli account in area singola perdano disponibilità in caso di interruzione a livello di area. È sempre consigliabile configurare  almeno due aree (preferibilmente almeno due aree di scrittura) con l'account Azure Cosmos per garantire sempre disponibilità elevata.

> [!IMPORTANT]
> Quando si usano le API SQL, è necessario configurare Cosmos DB SDK per usare tutte le aree di lettura specificate per sfruttare la maggiore disponibilità. Per altre [informazioni, vedere](troubleshoot-sdk-availability.md) questo articolo.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Account in più aree con un'area di scrittura singola (interruzione dell'area di scrittura)

* Durante un'interruzione dell'area di scrittura, l'account Azure Cosmos alza automaticamente di livello un'area secondaria come nuova area di scrittura primaria quando viene configurato il **failover** automatico nell'account Azure Cosmos. Se abilitato, il failover si verificherà in un'altra area nell'ordine di priorità dell'area specificato.

* Si noti che il failover manuale non deve essere attivato e non avrà esito positivo in presenza di un'interruzione dell'area di origine o di destinazione. Ciò è dovuto a una verifica coerenza richiesta dalla procedura di failover che richiede la connettività tra le aree.

* Quando l'area tornati online in precedenza, tutti i dati di scrittura che non sono stati replicati quando l'area ha avuto esito negativo vengono resi disponibili tramite il [feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed). Le applicazioni possono leggere il feed dei conflitti, risolvere i conflitti in base alla logica specifica dell'applicazione e scrivere di nuovo i dati aggiornati nel contenitore Azure Cosmos in base alle esigenze.

* Una volta che viene ripristinata l'area di scrittura interessata in precedenza, diventa automaticamente disponibile come area di lettura. È possibile tornare all'area ripristinata come area di scrittura. È possibile cambiare le aree usando [PowerShell, l'interfaccia della riga di comando di Azure o portale di Azure](how-to-manage-database-account.md#manual-failover). Non si **verificano perdite di dati o disponibilità** prima, durante o dopo il passaggio all'area di scrittura e l'applicazione continua a essere a disponibilità elevata.

> [!IMPORTANT]
> È consigliabile configurare gli account Azure Cosmos usati per i carichi di lavoro di produzione per **abilitare il failover automatico.** Ciò consente Cosmos DB di eseguire automaticamente il failover dei database dell'account in aree disponibili. In assenza di questa configurazione, l'account perderà la disponibilità in scrittura per tutta la durata dell'interruzione dell'area di scrittura, perché il failover manuale non riuscirà a causa della mancanza di connettività dell'area.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Account in più aree con un'area di scrittura singola (interruzione dell'area di lettura)

* Durante un'interruzione dell'area di lettura, gli account Azure Cosmos che usano qualsiasi livello di coerenza o coerenza elevata con tre o più aree di lettura rimarranno a disponibilità elevata per le operazioni di lettura e scrittura.

* Gli account Azure Cosmos che usano la coerenza elevata con tre aree (una scrittura e due operazioni di lettura) manterranno la disponibilità di scrittura durante un'interruzione dell'area di lettura. Per gli account con due aree e failover automatico abilitato, l'account smetterà di accettare scritture finché l'area non viene contrassegnata come non riuscita e non si verifica il failover automatico.

* L'area nessa viene automaticamente disconnessa e contrassegnata come offline. Gli [AZURE COSMOS DB sdk reindirizzano](sql-api-sdk-dotnet.md) le chiamate di lettura alla successiva area disponibile nell'elenco delle aree preferite.

* Se nessuna delle aree presenti nell'elenco è disponibile, viene eseguito il fallback automatico delle chiamate all'area di scrittura corrente.

* Non sono necessarie modifiche nel codice dell'applicazione per gestire le interruzioni di lettura a livello di area. Quando l'area di lettura tornata online tornerà online, verrà automaticamente sincronizzata con l'area di scrittura corrente e sarà nuovamente disponibile per gestire le richieste di lettura.

* Le letture successive vengono reindirizzate all'area ripristinata senza richiedere alcuna modifica del codice dell'applicazione. Durante il failover e il ricongiunzione di un'area con errore in precedenza, le garanzie di coerenza di lettura continuano a essere rispettate Azure Cosmos DB.

* Anche in un evento raro e spiacevole quando l'area di Azure è permanentemente irrecuperabile,  non si verifica alcuna perdita di dati se l'account Azure Cosmos in più aree è configurato con coerenza forte. Nel caso di un'area di scrittura permanentemente irrecuperabile, un account Azure Cosmos in più aree configurato con coerenza delimitata, la finestra di potenziale perdita di dati è limitata alla finestra di incoerenza (*K* o *T*) in cui K=100.000 aggiornamenti o T=5 minuti, che si verifica per primo. Per i livelli di sessione, prefisso coerente e coerenza finale, la finestra di potenziale perdita di dati è limitata a un massimo di 15 minuti. Per altre informazioni sulle destinazioni RTO e RPO per Azure Cosmos DB, vedere Livelli di coerenza [e durabilità dei dati](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Supporto della zona di disponibilità

Oltre alla resilienza tra aree, Azure Cosmos DB  la ridondanza della zona nelle aree supportate quando si seleziona un'area da associare all'account Azure Cosmos.

Con il supporto della zona di disponibilità (AZ), Azure Cosmos DB le repliche verranno posizionate tra più zone all'interno di una determinata area per offrire disponibilità elevata e resilienza agli errori di zona. zone di disponibilità un contratto di servizio con disponibilità del 99,995% senza modifiche alla latenza. In caso di errore di una singola zona, la ridondanza della zona garantisce la durabilità completa dei dati con RPO=0 e la disponibilità con RTO=0. La ridondanza della zona è una funzionalità supplementare per la replica a livello di area. La ridondanza della zona da sola non può essere basata su per ottenere resilienza a livello di area.

La ridondanza della zona può essere configurata solo quando si aggiunge una nuova area a un account Azure Cosmos. Per le aree esistenti, la ridondanza della zona può essere abilitata rimuovendo l'area e quindi aggiungendola nuovamente con la ridondanza della zona abilitata. Per un account di una singola area, è necessario aggiungere un'altra area in cui eseguire temporaneamente il failover, quindi rimuovere e aggiungere l'area desiderata con la ridondanza della zona abilitata.

Quando si configurano scritture in più aree per l'account Azure Cosmos, è possibile acconsentire esplicitamente alla ridondanza della zona senza costi aggiuntivi. In caso contrario, vedere la tabella seguente relativa ai prezzi per il supporto della ridondanza della zona. Per un elenco delle aree in cui sono disponibili le zone di disponibilità, vedere Zone [di disponibilità](../availability-zones/az-region.md).

La tabella seguente riepiloga la funzionalità di disponibilità elevata delle varie configurazioni di account:

|Indicatore KPI|Singola area senza AZ|Singola area con AZ|Scritture in più aree e in una singola area con AZ|Scritture in più aree con AZ|
|---------|---------|---------|---------|---------|
|Contratto di servizio per la disponibilità in scrittura | 99,99% | 99.995% | 99.995% | 99,999% |
|Contratto di servizio per la disponibilità in lettura  | 99,99% | 99.995% | 99.995% | 99,999% |
|Errori di zona: perdita di dati | Perdita di dati | Senza perdita di dati | Senza perdita di dati | Senza perdita di dati |
|Errori di zona : disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità | Nessuna perdita di disponibilità | Nessuna perdita di disponibilità |
|Interruzione a livello di regione: perdita di dati | Perdita di dati |  Perdita di dati | Dipende dal livello di coerenza. Per [altre informazioni, vedere](./consistency-levels.md) Compromessi di coerenza, disponibilità e prestazioni. | Dipende dal livello di coerenza. Per [altre informazioni, vedere](./consistency-levels.md) Compromessi di coerenza, disponibilità e prestazioni.
|Interruzione a livello di regione - disponibilità | Perdita di disponibilità | Perdita di disponibilità | Nessuna perdita di disponibilità per l'errore dell'area di lettura, temporaneo per l'errore dell'area di scrittura | Nessuna perdita di disponibilità |
|Prezzo (***1** _) | N/D | Frequenza delle RU/s con provisioning x 1,25 | Frequenza di RU/s con provisioning x 1,25 (_*_2_**) | Velocità di scrittura in più aree |

***1*** Per le unità richiesta di account serverless (RU) vengono moltiplicate per un fattore di 1,25.

***2*** Frequenza 1,25 applicata solo alle aree in cui è abilitata la funzionalità AZ.

zone di disponibilità può essere abilitato tramite:

* [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Interfaccia della riga di comando di Azure](manage-with-cli.md#add-or-remove-regions)

* [Modelli di Gestione risorse di Azure](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Compilazione di applicazioni a disponibilità elevata

* Esaminare il comportamento [previsto degli SDK di Azure Cosmos](troubleshoot-sdk-availability.md) durante questi eventi e quali sono le configurazioni che lo interessano.

* Per garantire un'elevata disponibilità in scrittura e lettura, configurare l'account Azure Cosmos in modo che si eserciti almeno due aree con più aree di scrittura. Questa configurazione fornirà la disponibilità più elevata, la latenza più bassa e la migliore scalabilità sia per le operazioni di lettura che per le scritture supportate dai contratti di servizio. Per altre informazioni, vedere Come configurare [l'account Azure Cosmos con più aree di scrittura.](tutorial-global-distribution-sql-api.md)

* Per gli account Azure Cosmos in più aree configurati con un'area di scrittura singola, abilitare il failover automatico usando l'interfaccia della riga di comando [di Azure o portale di Azure](how-to-manage-database-account.md#automatic-failover). Dopo aver abilitato il failover automatico, quando si verifica un'emergenza a livello di aera Cosmos DB esegue automaticamente il failover dell'account.  

* Anche se l'account Azure Cosmos è a disponibilità elevata, l'applicazione potrebbe non essere progettata correttamente per rimanere a disponibilità elevata. Per testare la disponibilità elevata end-to-end dell'applicazione, come parte delle esercitazioni di test o ripristino di emergenza dell'applicazione, disabilitare temporaneamente il failover automatico per l'account, richiamare il failover manuale usando [PowerShell,](how-to-manage-database-account.md#manual-failover)l'interfaccia della riga di comando di Azure o portale di Azure , quindi monitorare il failover dell'applicazione. Al termine, è possibile eseguire il failover nell'area primaria e ripristinare il failover automatico per l'account.

> [!IMPORTANT]
> Non richiamare il failover manuale durante un'interruzione Cosmos DB nell'aree di origine o di destinazione, perché richiede la connettività delle aree per mantenere la coerenza dei dati e non avrà esito positivo.

* All'interno di un ambiente di database distribuito a livello globale esiste una relazione diretta tra il livello di coerenza e la durabilità dei dati in presenza di un'interruzione a livello di area. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo un evento di arresto improvviso. Il tempo necessario per il ripristino completo di un'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti che l'applicazione è in grado di tollerare durante il ripristino dopo un evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO). Per l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) per Azure Cosmos DB, vedere [Livelli di coerenza e durabilità dei dati](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Cosa aspettarsi durante un'interruzione Cosmos DB'area geografica

Per gli account in una singola area, i client perderanno la disponibilità in lettura e scrittura.

Gli account in più aree sperimenteranno comportamenti diversi a seconda della tabella seguente.

| Scrivere aree | Failover automatico | Risultati previsti | Operazioni da eseguire |
| -- | -- | -- | -- |
| Area di scrittura singola | Non abilitato | In caso di interruzione in un'area di lettura, tutti i client verranno reindirizzati ad altre aree. Nessuna perdita di disponibilità in lettura o scrittura. Nessuna perdita di dati. <p/> In caso di interruzione nell'area di scrittura, i client sperimenteranno una perdita di disponibilità in scrittura. Se il livello di coerenza elevato non è selezionato, è possibile che alcuni dati non siano stati replicati nelle aree attive rimanenti. Dipende dal livello di convività selezionato, come descritto in [questa sezione.](consistency-levels.md#rto) Se l'area interessata subisce una perdita permanente di dati, i dati nonplicati potrebbero essere persi. <p/> Cosmos DB la disponibilità di scrittura verrà ripristinata automaticamente al termine dell'interruzione. | Durante l'interruzione, assicurarsi che nelle aree rimanenti siano presenti unità di replica con provisioning sufficienti per supportare il traffico di lettura. <p/> Non *attivare* un failover manuale durante l'interruzione, perché non avrà esito positivo. <p/> Al termine dell'interruzione, modificare nuovamente le UNITÀ di provisioning in base alle esigenze. |
| Area di scrittura singola | Abilitato | In caso di interruzione in un'area di lettura, tutti i client verranno reindirizzati ad altre aree. Nessuna perdita di disponibilità in lettura o scrittura. Nessuna perdita di dati. <p/> In caso di interruzione nell'area di scrittura, i client avranno una perdita di disponibilità di scrittura fino a quando Cosmos DB non sceglie automaticamente una nuova area come nuova area di scrittura in base alle preferenze. Se il livello di coerenza elevato non è selezionato, è possibile che alcuni dati non siano stati replicati nelle aree attive rimanenti. Dipende dal livello di convività selezionato, come descritto in [questa sezione.](consistency-levels.md#rto) Se l'area interessata subisce una perdita permanente di dati, i dati nonplicati potrebbero essere persi. | Durante l'interruzione, assicurarsi che nelle aree rimanenti siano presenti unità di replica con provisioning sufficienti per supportare il traffico di lettura. <p/> Non *attivare* un failover manuale durante l'interruzione, perché non avrà esito positivo. <p/> Al termine dell'interruzione, è possibile spostare nuovamente l'area di scrittura nell'area originale e modificare di nuovo le UNITÀ DI cui è stato effettuato il provisioning in base alle esigenze. Gli account che usano LE API SQL possono anche ripristinare i dati non replicati nell'area non riuscita dal [feed dei conflitti.](how-to-manage-conflicts.md#read-from-conflict-feed) |
| Più aree di scrittura | Non applicabile | Nessuna perdita di disponibilità in lettura o scrittura. <p/> I dati aggiornati di recente nell'area in cui si è verificata l'errore potrebbero non essere raggiungibili nelle aree attive rimanenti. I livelli di coerenza finale, coerenza del prefisso e della sessione garantiscono un <di 15 minuti. Il delimitazione garantisce meno di K aggiornamenti o T secondi, a seconda della configurazione. Se l'area interessata subisce una perdita di dati permanente, i dati nonplicati potrebbero essere persi. | Durante l'interruzione, assicurarsi che siano disponibili unità richieste di cui è stato effettuato il provisioning sufficienti nelle aree rimanenti per supportare il traffico aggiuntivo. <p/> Al termine dell'interruzione, è possibile modificare di nuovo le UNITÀ richieste di cui è stato effettuato il provisioning in base alle esigenze. Se possibile, Cosmos DB recupera automaticamente i dati non replicati nell'area in cui si è verificata l'errore usando il metodo di risoluzione dei conflitti configurato per gli account API SQL e l'ultima scrittura wins per gli account che usano altre API. |

## <a name="next-steps"></a>Passaggi successivi

Successivamente si possono leggere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](./consistency-levels.md)

* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](./request-units.md)

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)

* [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md)

* [Come configurare l'account Cosmos con più aree di scrittura](how-to-multi-master.md)

* [Comportamento dell'SDK in ambienti multi-regione](troubleshoot-sdk-availability.md)