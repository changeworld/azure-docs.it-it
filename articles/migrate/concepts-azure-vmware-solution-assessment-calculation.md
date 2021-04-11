---
title: Calcoli di AVS assessment in Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dei calcoli di AVS assessment nel servizio Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: b55f0562ae08a6e5a6b38bd0b6fc9f781b94ab64
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078690"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Panoramica della valutazione (migrazione alla soluzione VMware di Azure)

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali. Consente anche di tenere traccia delle istanze di cloud privato e pubblico in Azure. L'hub offre strumenti Azure Migrate per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti.

Lo strumento di individuazione e valutazione in Azure Migrate valuta i server locali per la migrazione a macchine virtuali di Azure e alla soluzione VMware di Azure (AVS). Questo articolo fornisce informazioni sul modo in cui vengono calcolate le valutazioni di Azure VMware Solution (AVS).

> [!NOTE]
> La valutazione della soluzione VMware di Azure (AVS) può essere creata solo per le macchine virtuali VMware.

## <a name="types-of-assessments"></a>Tipi di valutazioni

Le valutazioni create con Azure Migrate sono uno snapshot temporizzato dei dati. È possibile creare due tipi di valutazioni con Azure Migrate:

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. È possibile valutare i server locali nell'ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione alle macchine virtuali di Azure con questo tipo di valutazione.
**SQL di Azure** | Valutazioni per la migrazione dei server SQL locali dall'ambiente VMware al database SQL di Azure o al Istanza gestita SQL di Azure.
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Se il numero di macchine virtuali di Azure o di valutazione AVS non è corretto nello strumento di individuazione e valutazione, fare clic sul numero totale di valutazioni per passare a tutte le valutazioni e ricalcolare la VM di Azure o le valutazioni AVS. Lo strumento di individuazione e valutazione indicherà quindi il numero corretto per tale tipo di valutazione. 

La valutazione della soluzione VMware di Azure (AVS) fornisce due opzioni per i criteri di ridimensionamento:

| **Valutazione** | **Dettagli** | **Dati** |
| - | - | - |
| **Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti delle macchine virtuali locali. | **Dimensioni consigliate del nodo**: basate sui dati di utilizzo della CPU e della memoria con il tipo di nodo, il tipo di archiviazione e l'impostazione di ITF selezionati per la valutazione. |
| **Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni consigliate del nodo**: in base alle dimensioni della macchina virtuale locale con il tipo di nodo, il tipo di archiviazione e l'impostazione di ITF selezionati per la valutazione. |

## <a name="how-do-i-run-an-assessment"></a>Ricerca per categorie eseguire una valutazione?

Esistono due modi per eseguire una valutazione.

- Valutare i server usando i metadati del server raccolti da un'appliance Azure Migrate leggera. L'appliance individua i server locali. Invia quindi i dati sulle prestazioni e i metadati del server a Azure Migrate. Questo consente una maggiore precisione.
- Valutare i server usando i metadati del server importati in un formato con valori delimitati da virgole (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Ricerca per categorie valutare con l'appliance?

Se si sta distribuendo un'appliance Azure Migrate per individuare i server locali, seguire questa procedura:

1. Configurare Azure e l'ambiente locale per lavorare con Azure Migrate.
2. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento di individuazione e valutazione.
3. Distribuire un'appliance Azure Migrate leggera. L'appliance individua continuamente i server locali e invia i metadati del server e i dati sulle prestazioni a Azure Migrate. Distribuire l'appliance come macchina virtuale. Non è necessario installare alcun elemento sui server che si desidera valutare.

Quando l'appliance inizia l'individuazione del server, è possibile raccogliere i server che si vuole valutare in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **soluzione VMware di Azure (AVS)**.

Per creare la prima valutazione della soluzione VMware di Azure (AVS [), seguire questa procedura.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Ricerca per categorie valutare con i dati importati?

Se si sta valutando i server usando un file CSV, non è necessario un dispositivo. Eseguire invece i passaggi seguenti:

1. Configurare Azure per l'uso con Azure Migrate.
2. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento di individuazione e valutazione.
3. Scaricare un modello CSV e aggiungervi i dati del server.
4. Importare il modello in Azure Migrate.
5. Individuare i server aggiunti con l'importazione, raccoglierli in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **soluzione VMware di Azure (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Se si usa Azure Migrate Appliance per la valutazione, informazioni sui metadati e i dati sulle prestazioni raccolti per [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>In che modo l'appliance calcola i dati sulle prestazioni?

Se si usa il dispositivo per l'individuazione, raccoglie i dati sulle prestazioni per le impostazioni di calcolo con i passaggi seguenti:

1. L'appliance raccoglie un punto di esempio in tempo reale.

   - **VM VMware**: un punto di esempio viene raccolto ogni 20 secondi.
2. L'Appliance combina i punti di esempio per creare un singolo punto dati ogni 10 minuti. Per creare il punto dati, l'appliance seleziona i valori di picco di tutti gli esempi. Invia quindi il punto dati ad Azure.
3. Azure Migrate archivia tutti i punti dati di 10 minuti per l'ultimo mese.
4. Quando si crea una valutazione, la valutazione identifica il punto dati appropriato da usare per rightsizing. L'identificazione si basa sui valori percentile per la *cronologia delle prestazioni* e l' *utilizzo percentile*.

   - Se, ad esempio, la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è il 95 ° percentile, la valutazione Ordina i punti di campionamento di 10 minuti per l'ultima settimana. Li ordina in ordine crescente e sceglie il valore del 95 ° percentile per rightsizing.
   - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se è stato selezionato il 99 ° percentile.
   - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, selezionare il 99 ° percentile per l'utilizzo percentile.
5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per le metriche raccolte dal dispositivo:

   - Uso della CPU
   - Utilizzo RAM

I dati sulle prestazioni seguenti vengono raccolti ma non usati nei consigli per il dimensionamento per le valutazioni AVS:

- I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
- I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

## <a name="how-are-avs-assessments-calculated"></a>Come vengono calcolate le valutazioni AVS?

AVS Assessment usa i metadati e i dati sulle prestazioni dei server locali per calcolare le valutazioni. Se si distribuisce il dispositivo Azure Migrate, la valutazione usa i dati raccolti dal dispositivo. Tuttavia, se si esegue una valutazione importata utilizzando un file CSV, è necessario fornire i metadati per il calcolo.

I calcoli si verificano in queste tre fasi:

1. **Calcolo della conformità della soluzione VMware di Azure (AVS)**: se le VM locali sono adatte per la migrazione alla soluzione VMware di Azure (AVS).
2. **Calcolare il numero di nodi AVS e l'utilizzo tra i nodi**: numero stimato di nodi AVS necessari per eseguire le VM VMware e proiezione della CPU, della memoria e dell'utilizzo dello spazio di archiviazione in tutti i nodi.
3. **Stima dei costi mensili**: i costi mensili stimati per tutti i nodi della soluzione VMware di Azure (AVS) che eseguono le macchine virtuali locali.

I calcoli sono nell'ordine precedente. Un server passa a una fase successiva solo se passa quello precedente. Ad esempio, se un server ha esito negativo, la fase di idoneità AVS viene contrassegnata come non adatta per Azure. Il dimensionamento e i calcoli dei costi non vengono eseguiti per quel server

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Cosa c'è in una valutazione della soluzione VMware di Azure (AVS)?

Ecco cosa è incluso in una valutazione AVS:

| **Proprietà** | **Dettagli** |
| - | - |
| **Posizione di destinazione** | Specifica la posizione del cloud privato AVS in cui si vuole eseguire la migrazione. |
| **Tipo di archiviazione** | Specifica il motore di archiviazione da usare in AVS. AVS supporta attualmente solo rete VSAN come tipo di archiviazione predefinito, ma saranno disponibili altre opzioni di archiviazione in base alla roadmap. |
| **Istanze riservate (RIs)** | Questa proprietà consente di specificare istanze riservate in AVS se acquistate e il termine dell'istanza riservata. Utilizzato per calcolare i costi. |
| **Tipo di nodo** | Specifica il [tipo di nodo AVS](../azure-vmware/concepts-private-clouds-clusters.md) usato per l'uso in Azure. Il tipo di nodo predefinito è AV36. Potrebbero essere disponibili altri tipi di nodo in futuro.  Azure Migrate consiglierà un numero di nodi necessario per la migrazione delle macchine virtuali in AVS. |
| **Impostazione di ITF, livello RAID** | Specifica la combinazione valida di errori da tollerare e le combinazioni RAID. L'opzione di ITF selezionata combinata con il livello RAID e il requisito del disco della macchina virtuale locale determineranno il totale di spazio di archiviazione rete VSAN necessario in AVS. Lo spazio di archiviazione totale disponibile dopo i calcoli include anche uno spazio riservato per gli oggetti di gestione come vCenter e b) il 25% di Slack di archiviazione necessario per le operazioni rete VSAN. |
| **Criterio di dimensionamento** | Imposta i criteri da usare per determinare i requisiti di memoria, CPU e archiviazione per i nodi AVS. È possibile optare per il dimensionamento in *base alle prestazioni* o *come locale* senza considerare la cronologia delle prestazioni. Per eseguire semplicemente il lift-and-Shift, scegliere come in locale. Per ottenere il dimensionamento basato sull'utilizzo, scegliere basato sulle prestazioni. |
| **Cronologia delle prestazioni** | Imposta la durata da considerare per la valutazione dei dati sulle prestazioni dei server. Questa proprietà è applicabile solo quando i criteri di ridimensionamento sono *basati sulle prestazioni*. |
| **Utilizzo percentile** | Specifica il valore percentile del set di campionamento delle prestazioni da considerare per il corretto dimensionamento. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni. |
| **Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), Questo buffer viene applicato ai dati di utilizzo del server per le macchine virtuali (CPU, memoria e disco). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro. Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. |
| **Offerta** | Visualizza l' [offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Azure Migrate stima il costo di conseguenza. |
| **Valuta** | Mostra la valuta di fatturazione per l'account. |
| **Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%. |
| **Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Anche se non ha alcun effetto sui prezzi delle soluzioni VMware di Azure a causa del prezzo basato sui nodi, i clienti possono comunque applicare le licenze SQL o il sistema operativo locale (basato su Microsoft) in AVS usando i vantaggi di Azure Hybrid. Altri fornitori di sistemi operativi software dovranno fornire le proprie condizioni di licenza, ad esempio RHEL. |
| **oversubscription vCPU** | Specifica il rapporto tra il numero di core virtuali legati a un core fisico nel nodo AVS. Il valore predefinito nei calcoli è 4 vCPU: 1 core fisico in AVS. Gli utenti dell'API possono impostare questo valore come intero. Si noti che vCPU oversubscription > 4:1 può influito sui carichi di lavoro a seconda dell'utilizzo della CPU. Quando si esegue il ridimensionamento si presuppone sempre il 100% di utilizzo dei core scelti. |
| **Fattore di overcommit della memoria** | Specifica il rapporto di memoria rispetto al commit sul cluster. Il valore 1 rappresenta il 100% di utilizzo di memoria, 0,5 ad esempio è 50% e 2 utilizza il 200% della memoria disponibile. È possibile aggiungere valori compresi tra 0,5 e 10 fino a una posizione decimale. |
| **Fattore di compressione e deduplicato** | Specifica i deduplicati previsti e il fattore di compressione per i carichi di lavoro. Il valore effettivo può essere ottenuto dalla configurazione locale di rete VSAN o dell'archiviazione. Questi variano in base al carico di lavoro. Il valore 3 significherebbe 3x, quindi per il disco 300GB verrebbero usati solo 100 GB di spazio di archiviazione. Il valore 1 non significa alcun deduplicato o compressione. È possibile aggiungere solo valori da 1 a 10 fino a una posizione decimale. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analisi di idoneità per la soluzione VMware di Azure (AVS)

Le valutazioni AVS valutano ogni macchina virtuale locale per la relativa idoneità per AVS esaminando le proprietà del server. Assegna anche ogni server valutato a una delle categorie di idoneità seguenti:

- **Pronto per AVS**: è possibile eseguire la migrazione del server così com'è ad Azure (AVS) senza alcuna modifica. Verrà avviato in AVS con supporto AVS completo.
- **Pronto con le condizioni**: la macchina virtuale potrebbe avere problemi di compatibilità con la versione corrente di vSphere, nonché richiedere gli strumenti VMware e altre impostazioni prima che sia possibile ottenere le funzionalità complete dalla macchina virtuale in AVS.
- **Non pronto per AVS**: la macchina virtuale non viene avviata in AVS. Ad esempio, se la macchina virtuale VMware locale dispone di un dispositivo esterno collegato, ad esempio un CD-ROM, l'operazione VMware VMotion avrà esito negativo (se si usa VMware VMotion).
- **Conformità sconosciuta**: Azure migrate non è stato in grado di determinare la conformità del server a causa di metadati insufficienti raccolti dall'ambiente locale.

La valutazione esamina le proprietà del server per determinare la conformità di Azure del server locale.

### <a name="server-properties"></a>Proprietà server

La valutazione esamina la seguente proprietà della macchina virtuale locale per determinare se può essere eseguita in una soluzione VMware di Azure (AVS).

| **Proprietà** | **Dettagli** | **Stato di conformità con AVS** |
| - | - | - |
| **Protocollo Internet** | Azure attualmente non supporta l'indirizzamento Internet IPv6 end-to-end. Contattare il team locale MSFT AVS GBB per informazioni aggiuntive sulle linee guida per la correzione se il server viene rilevato con IPv6. | Protocollo Internet predisposto in modo condizionale |

### <a name="guest-operating-system"></a>Sistema operativo guest

Attualmente, le valutazioni AVS non esaminano il sistema operativo come parte dell'analisi di idoneità. È probabile che tutti i sistemi operativi in esecuzione su macchine virtuali locali vengano eseguiti in una soluzione VMware di Azure (AVS).

Insieme alle proprietà delle macchine virtuali, la valutazione esamina il sistema operativo guest dei server per determinare se può essere eseguito in Azure.

## <a name="sizing"></a>Ridimensionamento

Dopo che un server è stato contrassegnato come pronto per AVS, AVS Assessment apporta consigli sul dimensionamento dei nodi, che implicano l'identificazione dei requisiti di VM locali appropriati e la ricerca del numero totale di nodi AVS richiesti. Queste raccomandazioni variano a seconda delle proprietà di valutazione specificate.

- Se la valutazione usa il *dimensionamento in base alle prestazioni*, Azure migrate considera la cronologia delle prestazioni del server per apportare le indicazioni di dimensionamento appropriate per AVS. Questo metodo è particolarmente utile se la macchina virtuale locale è stata eccessivamente allocata, ma l'utilizzo è basso e si vuole dimensionare correttamente la VM in AVS per ridurre i costi. Questo metodo consente di ottimizzare le dimensioni durante la migrazione.
- Se non si vogliono considerare i dati sulle prestazioni per il dimensionamento delle macchine virtuali e si vuole portare i server locali così come sono in AVS, è possibile impostare i criteri di ridimensionamento su * come in locale *. Quindi, la valutazione ridimensiona le macchine virtuali in base alla configurazione locale senza considerare i dati di utilizzo.

### <a name="ftt-sizing-parameters"></a>Parametri di ridimensionamento dell'ITF

Il motore di archiviazione usato in AVS è rete VSAN. i criteri di archiviazione rete VSAN definiscono i requisiti di archiviazione per i server. Questi criteri garantiscono il livello di servizio richiesto per le macchine virtuali, perché determinano la modalità di allocazione dell'archiviazione alla macchina virtuale. Le combinazioni di FTT-Raid disponibili sono:

| **Tolleranza errori** | **Configurazione RAID** | **Numero minimo di host richiesti** | **Considerazioni sul dimensionamento** |
| - | - | - | - |
| 1 | RAID-1 (Mirroring) | 3 | Una macchina virtuale da 100 GB utilizza 200 GB. |
| 1 | RAID-5 (Erasure Coding) | 4 | Una macchina virtuale da 100 GB utilizza 133,33 GB |
| 2 | RAID-1 (Mirroring) | 5 | Una macchina virtuale da 100 GB utilizza 300 GB. |
| 2 | RAID-6 (Erasure Coding) | 6 | Una macchina virtuale da 100 GB utilizza 150 GB. |
| 3 | RAID-1 (Mirroring) | 7 | Una macchina virtuale da 100 GB utilizza 400 GB. |

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per il dimensionamento basato sulle prestazioni, Azure Migrate Appliance profila l'ambiente locale per raccogliere i dati sulle prestazioni per CPU, memoria e disco. Pertanto, il dimensionamento basato sulle prestazioni per AVS prenderà in considerazione lo spazio su disco allocato e utilizzerà l'utilizzo percentile scelto della memoria e della CPU. Ad esempio, se una macchina virtuale dispone di 4vCores allocato ma solo con il 25%, AVS verrà ridimensionato per 1vCore per tale macchina virtuale.

**Passaggi per la raccolta dei dati sulle prestazioni:**

1. Per le macchine virtuali VMware, l'appliance Azure Migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi.
2. Il dispositivo esegue il rollup dei punti di esempio raccolti ogni 10 minuti e invia al Azure Migrate il valore massimo per gli ultimi 10 minuti.
3. Azure Migrate archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese. Quindi, a seconda delle proprietà di valutazione specificate per la *cronologia delle prestazioni* e l'*utilizzo percentile*, identifica il punto dati appropriato da usare per il dimensionamento corretto. Se ad esempio la cronologia delle prestazioni è impostata su 1 giorno e l'utilizzo percentile è il 95 ° percentile, Azure Migrate usa i punti di campionamento di 10 minuti per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore del 95 ° percentile per il corretto dimensionamento.
4. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU e utilizzo della memoria) raccolta dal dispositivo.

Una volta determinato il valore di utilizzo effettivo, le dimensioni di archiviazione, rete e calcolo vengono gestite come indicato di seguito.

**Ridimensionamento dell'archiviazione**: Azure migrate usa lo spazio su disco della macchina virtuale locale totale come parametro di calcolo per determinare i requisiti di archiviazione AVS rete VSAN, oltre all'impostazione di ITF selezionata dal cliente. ITF: gli errori da tollerare, oltre a richiedere un numero minimo di nodi per ogni opzione di ITF, determineranno l'archiviazione rete VSAN totale necessaria combinata con il requisito del disco della macchina virtuale. Attualmente l'utilizzo dello spazio di archiviazione non viene considerato e la logica esamina solo lo spazio di archiviazione allocato per ogni macchina virtuale.

**Ridimensionamento di rete**: le valutazioni AVS attualmente non prendono in considerazione le impostazioni di rete.

**Dimensionamento del calcolo**: dopo aver calcolato i requisiti di archiviazione, AVS Assessment considera i requisiti di CPU e memoria per determinare il numero di nodi necessari per AVS in base al tipo di nodo.

- In base ai criteri di ridimensionamento, AVS Assessment esamina i dati delle VM basate sulle prestazioni o la configurazione della macchina virtuale locale. L'impostazione del fattore di comfort consente di specificare il fattore di crescita del cluster. Attualmente è abilitato per impostazione predefinita l'hyperthreading, quindi un nodo a 36 Core avrà 72 vCore. Vengono usati 4 vCore per computer fisico per determinare le soglie della CPU per cluster usando lo standard VMware che prevede di non superare un utilizzo dell'80% per consentire la manutenzione o la gestione degli errori senza compromettere la disponibilità del cluster. Attualmente non sono disponibili sostituzioni per modificare i valori di oversubscription, che potrebbero essere presenti nelle versioni future.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale

Se si usa *come dimensionamento locale*, AVS Assessment non considera la cronologia delle prestazioni delle VM e dei dischi. Al contrario, alloca i nodi AVS in base alla dimensione allocata in locale. Il tipo di archiviazione predefinito è rete VSAN in AVS.

[Altre](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) informazioni su come esaminare una valutazione della soluzione VMware di Azure.

### <a name="cpu-utilization-on-avs-nodes"></a>Utilizzo della CPU nei nodi AVS

L'utilizzo della CPU presuppone il 100% di utilizzo dei core disponibili. Per ridurre il numero di nodi necessari, è possibile aumentare l'oversubscription da 4:1, a indicare 6:1 in base alle caratteristiche del carico di lavoro e all'esperienza locale. Diversamente da quanto avviene per il disco, AVS non applica alcun limite all'utilizzo della CPU, ma spetta ai clienti garantire la corretta esecuzione del cluster, in modo che se è necessario eseguire la regolazione "Hot". Per consentire più spazio per la crescita, ridurre l'oversubscription o aumentare il valore per il fattore di crescita.

Anche l'utilizzo della CPU è già associato a un sovraccarico di gestione da vCenter, gestione NSX e altre risorse più piccole.

### <a name="memory-utilization-on-avs-nodes"></a>Utilizzo della memoria nei nodi AVS

L'utilizzo della memoria Mostra la memoria totale di tutti i nodi rispetto ai requisiti del server o dei carichi di lavoro. La memoria può essere sovrascritta e l'AVS non prevede alcun limite ed è il cliente a eseguire prestazioni ottimali del cluster per i carichi di lavoro.

L'utilizzo della memoria è già presente anche per il sovraccarico di gestione da vCenter, NSX Manager e altre risorse più piccole.

### <a name="storage-utilization-on-avs-nodes"></a>Utilizzo dello spazio di archiviazione nei nodi AVS

L'utilizzo dello spazio di archiviazione viene calcolato in base alla sequenza seguente:

1. Dimensioni richieste per le macchine virtuali (allocate come sono o spazio usato basato sulle prestazioni)
2. Applica fattore di crescita se presente
3. Aggiungere un sovraccarico di gestione e applicare il rapporto di ITF
4. Applicare la deduplicazione e il fattore di compressione
5. Applicare il 25% di Slack richiesto per rete VSAN
6. Risultato dello spazio di archiviazione disponibile per le macchine virtuali fuori dall'archiviazione totale, incluso il sovraccarico di gestione.

Lo spazio di archiviazione disponibile in un cluster a 3 nodi sarà basato sui criteri di archiviazione predefiniti, ovvero RAID-1, e usa il provisioning di spessore. Quando si calcola per la cancellazione di codice o RAID-5, ad esempio, è necessario un minimo di 4 nodi. Si noti che in AVS i criteri di archiviazione devono essere modificati dall'amministratore per consentire una maggiore quantità di spazio.

## <a name="confidence-ratings"></a>Classificazioni di confidenza

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità che varia da uno (più basso) a cinque stelle (più alto).

- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- Le classificazioni di attendibilità non sono applicabili per *come valutazioni locali* .
- Per il dimensionamento in base alle prestazioni, le valutazioni AVS richiedono i dati di utilizzo per la CPU e la memoria della macchina virtuale. I dati seguenti vengono raccolti ma non usati nei consigli per il dimensionamento per AVS:

  - I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
  - I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

  Se uno di questi numeri di utilizzo non è disponibile in server vCenter, la raccomandazione di dimensioni potrebbe non essere affidabile.

A seconda della percentuale di punti dati disponibili, la classificazione di attendibilità per la valutazione viene eseguita come segue.

| **Disponibilità dei punti dati** | **Classificazione di attendibilità** |
| - | - |
| 0-20% | 1 stella |
| 21-40% | 2 stelle |
| 41-60% | 3 stelle |
| 61-80% | 4 stelle |
| 81-100% | 5 stelle |

### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:

- L'ambiente non è stato profilato per il periodo di tempo per cui si sta creando la valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- La valutazione non è in grado di raccogliere i dati sulle prestazioni per alcune o tutte le macchine virtuali nel periodo di valutazione. Per una classificazione di attendibilità elevata, verificare che:

  - Le macchine virtuali sono accese per la durata della valutazione
  - Sono consentite le connessioni in uscita sulle porte 443
  - Per le VM Hyper-V è abilitata la memoria dinamica

  Ricalcolare la valutazione in modo da riflettere le ultime modifiche apportate alla classificazione di attendibilità.
- Alcune macchine virtuali sono state create durante il periodo in cui è stata calcolata la valutazione. Si supponga, ad esempio, che sia stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma che alcune macchine virtuali siano state create solo una settimana fa. In questo caso, i dati sulle prestazioni per le nuove macchine virtuali non saranno disponibili per l'intera durata e la classificazione di attendibilità sarà limitata.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare la valutazione al dimensionamento locale.

## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Una volta completate le indicazioni sul dimensionamento, Azure Migrate calcola il costo totale di esecuzione dei carichi di lavoro locali in AVS moltiplicando il numero di nodi AVS richiesti dal prezzo del nodo. Il costo per macchina virtuale viene calcolato dividendo il costo totale per il numero di macchine virtuali nella valutazione.

- Il calcolo prende in considerazione il numero di nodi necessari, il tipo di nodo e il percorso.
- Aggrega il costo in tutti i nodi per calcolare il costo mensile totale.
- I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.

Dato che i prezzi per la soluzione VMware di Azure (AVS) sono per nodo, il costo totale non prevede costi di calcolo e distribuzione dei costi di archiviazione. [Altre informazioni](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Linee guida per lo strumento di migrazione

Nel report di idoneità per Azure per la valutazione della soluzione Azure VMware è possibile visualizzare gli strumenti consigliati seguenti:

- **VMware HCX o Enterprise**: per i server VMware, la soluzione VMware Hybrid Cloud Extension (HCx) è lo strumento di migrazione suggerito per eseguire la migrazione del carico di lavoro locale al cloud privato della soluzione VMware di Azure (AVS). [Altre informazioni](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Sconosciuto**: per i server importati tramite un file CSV, lo strumento di migrazione predefinito è sconosciuto. Per i server VMware, tuttavia, è consigliabile usare la soluzione VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Passaggi successivi

Creare una valutazione per le [VM AVS VMware](how-to-create-azure-vmware-solution-assessment.md).
