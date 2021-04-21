---
title: Ridimensionamento orizzontale di Azure Analysis Services | Microsoft Docs
description: Replicare Azure Analysis Services server con scalabilità orizzontale. Le query client possono quindi essere distribuite tra più repliche di query in un pool di query con scalabilità orizzontale.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769266"
---
# <a name="azure-analysis-services-scale-out"></a>Ridimensionamento orizzontale di Azure Analysis Services

Con la scalabilità orizzontale, le query client possono essere distribuite tra più repliche di *query* in un pool di *query,* riducendo i tempi di risposta durante carichi di lavoro di query elevati. È anche possibile separare l'elaborazione dal pool di query, assicurando così che le prestazioni delle query dei client non vengano influenzate negativamente dalle operazioni di elaborazione. Il ridimensionamento orizzontale può essere configurato nel portale di Azure o tramite l'API REST di Analysis Services.

Il ridimensionamento orizzontale è disponibile per i server del piano tariffario Standard. Ogni replica di query viene fatturata alla stessa tariffa del server. Tutte le repliche di query vengono create nella stessa area del server. Il numero di repliche di query che è possibile configurare dipende dall'area in cui risiede il server. Per altre informazioni, vedere [Disponibilità per area geografica](analysis-services-overview.md#availability-by-region). Il ridimensionamento orizzontale non aumenta la quantità di memoria disponibile per il server. Per aumentare la memoria, è necessario aggiornare il piano. 

## <a name="why-scale-out"></a>Perché aumentare le dimensioni?

In una distribuzione di server tipica, un server funge sia da server di elaborazione che da server di query. Se il numero di query client verso i modelli presenti nel server supera il numero di unità di elaborazione query (QPU, Query Processing Unit) del piano del server o se l'elaborazione dei modelli avviene contemporaneamente a carichi di lavoro di query elevati, le prestazioni possono peggiorare. 

Con la scalabilità orizzontale, è possibile creare un pool di query con fino a sette risorse di replica di query aggiuntive (otto in totale, incluso *il* server primario). È possibile ridimensionare il numero di repliche nel pool di query per soddisfare le richieste QPU in momenti critici ed è possibile separare un server di elaborazione dal pool di query in qualsiasi momento. 

Indipendentemente dal numero di repliche di query presenti in un pool di query, i carichi di lavoro di elaborazione non sono distribuiti tra le repliche di query. Il server primario funge da server di elaborazione. Le repliche di query vengono eseguite solo su database modello sincronizzati tra il server primario e ogni replica nel pool di query. 

Quando si esegue la scalabilità orizzontale, l'aggiunta incrementale di nuove repliche di query al pool di query può richiedere fino a cinque minuti. Quando tutte le nuove repliche di query sono attive e in esecuzione, il carico delle nuove connessioni client viene bilanciato tra le risorse nel pool di query. Le connessioni client esistenti non vengono modificate dalla risorsa alla quale sono attualmente connesse. Durante il ridimensionamento verticale, tutte le connessioni client esistenti a una risorsa del pool di query che viene rimossa dal pool di query vengono terminate. I client possono riconnettersi a una risorsa pool di query rimanente.

## <a name="how-it-works"></a>Funzionamento

Quando si configura la scalabilità orizzontale la prima  volta, i database modello nel server primario vengono automaticamente sincronizzati con le nuove repliche in un nuovo pool di query. La sincronizzazione automatica viene eseguita una sola volta. Durante la sincronizzazione automatica, i file di dati del server primario (crittografati in stato di inquieto nell'archiviazione BLOB) vengono copiati in una seconda posizione, crittografati anche in caso di inquieto nell'archiviazione BLOB. Le repliche nel pool di query vengono quindi *indotta* con i dati del secondo set di file. 

Mentre una sincronizzazione automatica viene eseguita solo quando si scala orizzontalmente un server per la prima volta, è anche possibile eseguire una sincronizzazione manuale. La sincronizzazione garantisce che i dati nelle repliche nel pool di query corrispondano a quello del server primario. Quando si elaborano i modelli (aggiornamento) nel server primario, è necessario eseguire una sincronizzazione *dopo il* completamento delle operazioni di elaborazione. Questa sincronizzazione copia i dati aggiornati dai file del server primario nell'archivio BLOB al secondo set di file. Le repliche nel pool di query vengono quindi idratate con i dati aggiornati del secondo set di file nell'archivio BLOB. 

Quando si esegue un'operazione di scalabilità orizzontale successiva, ad esempio aumentando il numero di repliche nel pool di query da due a cinque, le nuove repliche vengono idratate con i dati del secondo set di file nell'archivio BLOB. Non è presente alcuna sincronizzazione. Se quindi si esegue una sincronizzazione dopo la scalabilità orizzontale, le nuove repliche nel pool di query verranno idratate due volte, un'idratazione ridondante. Quando si esegue un'operazione di scalabilità orizzontale successiva, è importante tenere presente quanto segue:

* Eseguire una *sincronizzazione prima dell'operazione di scalabilità orizzontale* per evitare l'idratazione ridondante delle repliche aggiunte. Le operazioni simultanee di sincronizzazione e scalabilità orizzontale in esecuzione contemporaneamente non sono consentite.

* Quando si automatizzano le operazioni di elaborazione e scalabilità orizzontale, è importante prima elaborare i dati nel server primario, quindi eseguire una sincronizzazione e quindi eseguire l'operazione di scalabilità orizzontale.  Questa sequenza assicura un impatto minimo sulle risorse di memoria e QPU.

* Durante le operazioni di scalabilità orizzontale, tutti i server nel pool di query, incluso il server primario, sono temporaneamente offline.

* La sincronizzazione è consentita anche quando non sono presenti repliche nel pool di query. Se si esegue la scalabilità orizzontale da zero a una o più repliche con nuovi dati di un'operazione di elaborazione nel server primario, eseguire prima la sincronizzazione senza repliche nel pool di query e quindi aumentare il numero di repliche. La sincronizzazione prima della scalabilità orizzontale evita l'idratazione ridondante delle repliche appena aggiunte.

* Quando si elimina un database modello dal server primario, non viene eliminato automaticamente dalle repliche nel pool di query. È necessario eseguire un'operazione di sincronizzazione usando il comando [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) di PowerShell che rimuove i file per il database dal percorso di archiviazione BLOB condiviso della replica e quindi elimina il database modello nelle repliche nel pool di query. Per determinare se un database modello esiste nelle repliche nel pool di query ma non nel server primario, assicurarsi che l'impostazione Separa il server di elaborazione dall'esecuzione di query nel **pool** sia **impostata** su Sì . Usare quindi SSMS per connettersi al server primario usando il `:rw` qualificatore per verificare se il database esiste. Connettersi quindi alle repliche nel pool di query connettendosi senza il qualificatore per verificare se `:rw` esiste anche lo stesso database. Se il database esiste nelle repliche nel pool di query ma non nel server primario, eseguire un'operazione di sincronizzazione.   

* Quando si rinomina un database nel server primario, è necessario un passaggio aggiuntivo per assicurarsi che il database sia sincronizzato correttamente con qualsiasi replica. Dopo la ridenominazione, eseguire una sincronizzazione usando il comando [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) specificando il parametro `-Database` con il nome del database precedente. Questa sincronizzazione rimuove il database e i file con il nome precedente da tutte le repliche. Eseguire quindi un'altra sincronizzazione specificando il `-Database` parametro con il nuovo nome del database. La seconda sincronizzazione copia il database appena denominato nel secondo set di file e iuteni tutte le repliche. Queste sincronizzazioni non possono essere eseguite usando il comando Sincronizza modello nel portale.

### <a name="synchronization-mode"></a>Modalità di sincronizzazione

Per impostazione predefinita, le repliche di query vengono riidratate completamente, non in modo incrementale. La riidratazione avviene in fasi. Vengono scollegati e collegati due alla volta (presupponendo che siano presenti almeno tre repliche) per garantire che almeno una replica sia mantenuta online per le query in un determinato momento. In alcuni casi, potrebbe essere necessario riconnettere i client a una delle repliche online durante questo processo. Usando **l'impostazione ReplicaSyncMode** è ora possibile specificare che la sincronizzazione della replica di query avviene in parallelo. La sincronizzazione parallela offre i vantaggi seguenti: 

- Riduzione significativa del tempo di sincronizzazione. 
- È più probabile che i dati tra le repliche siano coerenti durante il processo di sincronizzazione. 
- Poiché i database vengono mantenuti online in tutte le repliche durante il processo di sincronizzazione, i client non devono riconnettersi. 
- La cache in memoria viene aggiornata in modo incrementale con solo i dati modificati, che possono essere più veloci rispetto alla riidratazione completa del modello. 

#### <a name="setting-replicasyncmode"></a>Impostazione di ReplicaSyncMode

Usare SSMS per impostare ReplicaSyncMode in Proprietà avanzate. I valori possibili sono: 

- `1` (impostazione predefinita): riidratazione completa del database di replica in fasi (incrementale). 
- `2`: sincronizzazione ottimizzata in parallelo. 

![Impostazione RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Quando si **imposta ReplicaSyncMode=2,** a seconda della quantità di cache che deve essere aggiornata, le repliche di query possono utilizzare memoria aggiuntiva. Per mantenere il database online e disponibile per le query, a seconda della  quantità di dati modificati, l'operazione può richiedere fino al doppio della memoria nella replica perché sia i segmenti vecchi che i nuovi vengono mantenuti in memoria contemporaneamente. I nodi di replica hanno la stessa allocazione di memoria del nodo primario ed è in genere presente memoria aggiuntiva nel nodo primario per le operazioni di aggiornamento, pertanto potrebbe essere improbabile che le repliche eseere esere esere insufficiente. Inoltre, lo scenario comune è che il database viene aggiornato in modo incrementale nel nodo primario e pertanto il requisito per il doppio della memoria deve essere insolito. Se l'operazione di sincronizzazione rileva un errore di memoria insufficiente, verrà eseguito un nuovo tentativo usando la tecnica predefinita (collega/scollega due alla volta). 

### <a name="separate-processing-from-query-pool"></a>Separare l'elaborazione dal pool di query

Per ottenere prestazioni ottimali sia delle operazioni di elaborazione che delle operazioni di query, è possibile scegliere di separare il server di elaborazione dal pool di query. Se separate, le nuove connessioni client vengono assegnate solo alle repliche di query nel pool di query. Se le operazioni di elaborazione richiedono pochi minuti, è possibile scegliere di separare il server di elaborazione dal pool di query solo per il tempo necessario per eseguire le operazioni di elaborazione e sincronizzazione e quindi includerlo nuovamente nel pool di query. Quando si separa il server di elaborazione dal pool di query o lo si aggiunge nuovamente al pool di query, il completamento dell'operazione può richiedere fino a cinque minuti.

## <a name="monitor-qpu-usage"></a>Monitorare l'utilizzo di QPU

Per determinare se la scalabilità orizzontale per il server è necessaria, monitorare il [server](analysis-services-monitor.md) in portale di Azure usando le metriche. Se le QPU si esauriscono regolarmente, significa che il numero di query verso i modelli supera il limite di QPU per il piano. La metrica relativa alla lunghezza della coda dei processi del pool di query aumenta anche quando il numero di query nella coda del pool di thread di query supera le QPU disponibili. 

Un'altra buona metrica da controllare è la media di QPU per ServerResourceType. Questa metrica confronta la media di QPU per il server primario con il pool di query. 

![Metriche di scalabilità orizzontale delle query](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Per configurare QPU in base a ServerResourceType**

1. In un grafico a linee Metriche fare clic **su Aggiungi metrica.** 
2. In **RISORSA** selezionare il server, quindi in SPAZIO DEI NOMI DELLA METRICA selezionare Analysis Services **metriche standard, quindi in** **METRICA** selezionare **QPU** e quindi in **AGGREGAZIONE** selezionare **Media**.  
3. Fare **clic su Applica suddivisione.** 
4. In **VALORI** selezionare **ServerResourceType.**  

### <a name="detailed-diagnostic-logging"></a>Registrazione diagnostica dettagliata

Usare Monitoraggio di Azure log per una diagnostica più dettagliata delle risorse del server con scalabilità orizzontale. Con i log è possibile usare le query di Log Analytics per suddividere QPU e memoria in base al server e alla replica. Per altre informazioni, vedere query di esempio in [Analysis Services diagnostica.](analysis-services-logging.md#example-queries)


## <a name="configure-scale-out"></a>Configurare il ridimensionamento orizzontale

### <a name="in-azure-portal"></a>Nel portale di Azure

1. Nel portale fare clic su **Scale-out**. Usare il dispositivo di scorrimento per selezionare il numero di server di replica di query. Il numero di repliche scelto viene aggiunto al server esistente.  

2. In **Separare il server di elaborazione dal pool di query** selezionare Sì per escludere il server di elaborazione dal server di query. Le [connessioni](#connections) client che usano la stringa di connessione predefinita (senza ) vengono `:rw` reindirizzate alle repliche nel pool di query. 

   ![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Fare clic su **Salva** per effettuare il provisioning dei nuovi server di replica di query. 

Quando si configura la scalabilità orizzontale per un server la prima volta, i modelli nel server primario vengono automaticamente sincronizzati con le repliche nel pool di query. La sincronizzazione automatica viene eseguita una sola volta, quando si configura per la prima volta la scalabilità orizzontale in una o più repliche. Le successive modifiche al numero di repliche nello stesso server non *attiveranno un'altra sincronizzazione automatica.* La sincronizzazione automatica non verrà eseguita di nuovo anche se si imposta il server su zero repliche e quindi si riparte il numero di repliche. 

## <a name="synchronize"></a>Sincronizza 

Le operazioni di sincronizzazione devono essere eseguite manualmente o tramite l'API REST.

### <a name="in-azure-portal"></a>Nel portale di Azure

In **Panoramica** > modello > **Sincronizza modello**.

![Icona Sincronizza](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Usare l'operazione **sync**.

#### <a name="synchronize-a-model"></a>Sincronizzare un modello   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Ottenere lo stato di sincronizzazione  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Codici di stato restituiti:


|Codice  |Descrizione  |
|---------|---------|
|-1     |  Non valido       |
|0     | Replicating        |
|1     |  Reidratazione       |
|2     |   Completato       |
|3     |   Non riuscito      |
|4     |    Finalizzazione     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di usare PowerShell, [installare o aggiornare la versione](/powershell/azure/install-az-ps)più Azure PowerShell modulo . 

Per eseguire la sincronizzazione, [usare Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Per impostare il numero di repliche di query, [usare Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Specificare il parametro facoltativo `-ReadonlyReplicaCount`.

Per separare il server di elaborazione dal pool di query, [usare Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Specificare il parametro `-DefaultConnectionMode` facoltativo per usare `Readonly` .

Per altre informazioni, vedere [Uso di un'entità servizio con il modulo Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Connessioni

Nella pagina Panoramica del server sono presenti due nomi di server. Se il ridimensionamento orizzontale non è stato ancora configurato per un server, entrambi i nomi di server funzionano allo stesso modo. Dopo che per un server è stato configurato il ridimensionamento orizzontale, è necessario specificare il nome del server appropriato a seconda del tipo di connessione. 

Per le connessioni client dell'utente finale Power BI Desktop, Excel e app personalizzate, usare **Nome server**. 

Per SSMS, Visual Studio e le stringhe di connessione in PowerShell, app per le funzioni di Azure e AMO, usare il nome **del server di gestione**. Il nome del server di gestione include un qualificatore (lettura e scrittura) `:rw` speciale. Tutte le operazioni di elaborazione vengono eseguite nel server di gestione (primario).

![Nomi dei server](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Scalabilità verticale, scalabilità orizzontale e scalabilità orizzontale

È possibile modificare il piano tariffario in un server con più repliche. Lo stesso piano tariffario si applica a tutte le repliche. Un'operazione di scalabilità in primo luogo porterà tutte le repliche tutte contemporaneamente e quindi tutte le repliche nel nuovo piano tariffario.

## <a name="troubleshoot"></a>Risolvere problemi

**Problema:** Impossibile trovare il **server \<Name of the server> 'istanza ' in modalità di connessione 'ReadOnly'.**

**Soluzione:** Quando si seleziona **l'opzione** Separa il server di elaborazione dal pool di query, le connessioni client che usano la stringa di connessione predefinita (senza ) vengono reindirizzate alle repliche `:rw` del pool di query. Se le repliche nel pool di query non sono ancora online perché la sincronizzazione non è stata ancora completata, le connessioni client reindirizzate possono avere esito negativo. Quando si esegue una sincronizzazione, per evitare errori di connessione, nel pool di query devono essere presenti almeno due server. Ogni server viene sincronizzato singolarmente, mentre gli altri rimangono online. Se si sceglie di non tenere il server di elaborazione all'interno del pool di query durante l'elaborazione, è possibile rimuoverlo dal pool per l'elaborazione e quindi riaggiungerlo al termine di questa, ma prima della sincronizzazione. Usare le metriche di memoria e di QPU per monitorare lo stato della sincronizzazione.



## <a name="related-information"></a>Informazioni correlate

[Monitorare le metriche del server](analysis-services-monitor.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md)
