---
title: Personalizzare le valutazioni per Azure Migrate | Microsoft Docs
description: Viene descritto come personalizzare le valutazioni create con Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785207"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

Questo articolo descrive come personalizzare le valutazioni create da Azure Migrate strumento di individuazione e valutazione.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

È possibile usare lo strumento di individuazione e valutazione Azure Migrate per creare valutazioni per le macchine virtuali VMware locali e le VM Hyper-V, in preparazione per la migrazione ad Azure. Lo strumento di individuazione e valutazione valuta i server locali per la migrazione alle macchine virtuali IaaS di Azure e alla soluzione Azure VMware (AVS). 

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Le valutazioni create con lo strumento di individuazione e valutazione sono uno snapshot temporizzato dei dati. Esistono due tipi di valutazione che è possibile creare usando Azure Migrate: individuazione e valutazione.

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione.(concepts-assessment-calculation.md)
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

Opzioni relative ai criteri di ridimensionamento nelle valutazioni Azure Migrate:

**Criteri di dimensionamento** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni in base ai dati sulle prestazioni raccolti | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sui dati relativi all'utilizzo di CPU e memoria.<br/><br/> La raccomandazione sul tipo di disco (HDD/SSD standard o dischi gestiti Premium) è basata sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.<br/><br/>**Azure SQL Assessment**: la configurazione di SQL Azure è basata sui dati sulle prestazioni delle istanze e dei database SQL, che includono: utilizzo della CPU, utilizzo della memoria, IOPS (file di dati e di log), velocità effettiva e latenza delle operazioni di i/o<br/><br/>**Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sui dati relativi all'utilizzo di CPU e memoria.
**Come in locale** | Valutazioni che non usano i dati sulle prestazioni per fare raccomandazioni. | **Valutazione delle macchine virtuali di Azure**: la raccomandazione sulle dimensioni della VM è basata sulle dimensioni delle VM locali<br/><br> Il tipo di disco consigliato è basato sull'opzione selezionata nell'impostazione del tipo di archiviazione per la valutazione.<br/><br/> **Valutazione della soluzione Azure VMware**: la raccomandazione sui nodi della soluzione Azure VMware è basata sulle dimensioni delle VM locali.

## <a name="how-is-an-assessment-done"></a>Come viene eseguita una valutazione?

Una valutazione effettuata in Azure Migrate individuazione e valutazione prevede tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati. [Altre informazioni.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Che cosa è in una valutazione di VM di Azure?

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Azure VM Assessment supporta attualmente le aree di destinazione seguenti: Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, Stati Uniti orientali, Germania centrale, Germania nordorientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati uniti centro-meridionali, Asia sudorientale, India meridionale Regno Unito occidentale Regno Unito meridionale , US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
**Tipo di archiviazione** | È possibile usare questa proprietà per specificare il tipo di dischi che si vuole spostare in Azure.<br/><br/> Per il dimensionamento locale è possibile specificare il tipo di archiviazione di destinazione come dischi gestiti da Premium, dischi gestiti da SDD Standard o dischi gestiti da HDD Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di destinazione come dischi automatici, gestiti con gestione Premium, dischi gestiti da HDD Standard o dischi gestiti da SDD Standard.<br/><br/> Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se si specifica il tipo di archiviazione Premium o standard, la valutazione indicherà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola pari al 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti Premium. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Azure
**Istanze riservate (RI)** | Questa proprietà consente di specificare se sono presenti [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. le stime dei costi nella valutazione vengono quindi effettuate in sconti per il ri. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate.
**Criterio di dimensionamento** | Il criterio da usare per dimensionare correttamente le VM per Azure. È possibile eseguire il ridimensionamento in *base alle prestazioni* o ridimensionare le macchine virtuali *in locale*, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.
**Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il ridimensionamento è *basato sulle prestazioni*.
**Serie VM** |     È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | La valutazione delle VM di Azure considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specificare se si dispone di Software Assurance e si è idonei per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. Il valore predefinito è Yes.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Cosa c'è in una valutazione della soluzione VMware di Azure (AVS)?

Ecco cosa è incluso in una valutazione AVS:


| **Proprietà** | **Dettagli** |
| - | - |
| **Posizione di destinazione** | Specifica la posizione del cloud privato AVS in cui si vuole eseguire la migrazione.<br/><br/> AVS Assessment supporta attualmente le aree di destinazione: Stati Uniti orientali, Europa occidentale, Stati Uniti occidentali. |
| **Tipo di archiviazione** | Specifica il motore di archiviazione da usare in AVS.<br/><br/> Si noti che AVS Assessment supporta solo rete VSAN come tipo di archiviazione predefinito. |
**Istanze riservate (RIs)** | Questa proprietà consente di specificare istanze riservate in AVS. I servizi di installazione remota non sono attualmente supportati per i nodi AVS. |
**Tipo di nodo** | Specifica il [tipo di nodo AVS](../azure-vmware/concepts-private-clouds-clusters.md) usato per eseguire il mapping delle macchine virtuali locali. Si noti che il tipo di nodo predefinito è AV36. <br/><br/> Azure Migrate consiglierà un numero di nodi necessario per la migrazione delle macchine virtuali in AVS. |
**Impostazione di ITF, livello RAID** | Specifica l'errore applicabile da tollerare e le combinazioni RAID. L'opzione di ITF selezionata combinata con il requisito del disco della macchina virtuale locale determina lo spazio di archiviazione rete VSAN totale richiesto in AVS. |
**Criterio di dimensionamento** | Imposta i criteri da usare per _dimensionare correttamente_ le VM per AVS. È possibile optare per il dimensionamento in _base alle prestazioni_ o _come locale_ senza considerare la cronologia delle prestazioni. |
**Cronologia delle prestazioni** | Imposta la durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando i criteri di ridimensionamento sono _basati sulle prestazioni_. |
**Utilizzo percentile** | Specifica il valore percentile del set di campionamento delle prestazioni da considerare per il corretto dimensionamento. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.|
**Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. |
**Offerta** | Visualizza l' [offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Azure Migrate stima il costo di conseguenza.|
**Valuta** | Mostra la valuta di fatturazione per l'account. |
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%. |
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Sebbene non abbia alcun effetto sui prezzi delle soluzioni VMware di Azure a causa del prezzo basato sui nodi, i clienti possono comunque applicare le proprie licenze del sistema operativo locali (basate su Microsoft) in AVS usando i vantaggi di Azure Hybrid. Altri fornitori di sistemi operativi software dovranno fornire le proprie condizioni di licenza, ad esempio RHEL. |
**oversubscription vCPU** | Specifica il rapporto tra il numero di core virtuali associato a 1 core fisico nel nodo AVS. Il valore predefinito nei calcoli è 4 vCPU: 1 core fisico in AVS. <br/><br/> Gli utenti dell'API possono impostare questo valore come intero. Si noti che vCPU oversubscription > 4:1 può iniziare a causare un peggioramento delle prestazioni, ma può essere usato per i carichi di lavoro di tipo server Web. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Quali proprietà vengono usate per creare e personalizzare una valutazione SQL di Azure?

Ecco cosa è incluso nelle proprietà di valutazione di SQL Azure:

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione. Le raccomandazioni per la configurazione e i costi di Azure SQL si basano sul percorso specificato.
**Tipo di distribuzione di destinazione** | Tipo di distribuzione di destinazione in cui si vuole eseguire la valutazione: <br/><br/> Selezionare **consigliato**, se si vuole Azure migrate valutare la conformità dei server SQL per la migrazione al database SQL di Azure mi e di Azure e consigliare l'opzione di distribuzione di destinazione più adatta, il livello di destinazione, la configurazione di SQL di Azure e le stime mensili.<br/><br/>Selezionare il database **SQL di Azure**, se si vuole valutare i server SQL per la migrazione solo ai database SQL di Azure ed esaminare il livello di destinazione, la configurazione del database SQL di Azure e le stime mensili.<br/><br/>Selezionare **Azure SQL mi**, se si vuole valutare i server SQL per la migrazione solo ai database SQL di Azure ed esaminare il livello di destinazione, la configurazione di Azure SQL mi e le stime mensili.
**Capacità riservata** | Specifica la capacità riservata in modo da tenere conto delle stime dei costi nella valutazione.<br/><br/> Se si seleziona un'opzione di capacità riservata, non è possibile specificare "discount (%)".
**Criteri di dimensionamento** | Questa proprietà viene usata per dimensionare correttamente la configurazione di SQL Azure. <br/><br/> Il valore predefinito è **basato sulle prestazioni** , ovvero la valutazione raccoglierà le metriche delle prestazioni SQL Server istanze e database per consigliare una istanza gestita SQL di Azure e/o una raccomandazione per il livello/configurazione del database SQL di Azure di dimensioni ottimali.
**Cronologia delle prestazioni** | Cronologia prestazioni specifica la durata utilizzata per la valutazione dei dati sulle prestazioni.
**Utilizzo percentile** | L'utilizzo percentile specifica il valore percentile dell'esempio di prestazioni utilizzato per rightsizing.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. In questo modo vengono rilevati problemi come l'utilizzo stagionale, la cronologia delle prestazioni brevi e probabilmente aumenti nell'utilizzo futuro.<br/><br/> Ad esempio, un'istanza di 10 core con utilizzo del 20% normalmente produce un'istanza a due core. Con un fattore di comfort di 2,0, il risultato è invece un'istanza a quattro core.
**Programma offerta/licenza** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Attualmente è possibile scegliere solo da sviluppo/test con pagamento in base al consumo e con pagamento in base al consumo. Si noti che è possibile usufruire di uno sconto aggiuntivo applicando la capacità riservata e Vantaggio Azure Hybrid sull'offerta con pagamento in base al consumo.
**Livello di servizio** | L'opzione del livello di servizio più appropriata per soddisfare le esigenze aziendali per la migrazione al database SQL di Azure e/o Istanza gestita SQL di Azure:<br/><br/>**Consigliato** se si desidera che Azure migrate consigli il livello di servizio più adatto per i server. Può trattarsi di un utilizzo generico o di un business critical. <br/><br/> **Per utilizzo generico** Se si vuole una configurazione SQL di Azure progettata per carichi di lavoro orientati al budget. [Altre informazioni](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Business critical** Se si vuole una configurazione SQL di Azure progettata per carichi di lavoro a bassa latenza con resilienza elevata a errori e failover rapidi. [Altre informazioni](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Valuta** | Valuta di fatturazione per l'account.
**Sconto (%)** | Tutti gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%.
**Vantaggio Azure Hybrid** | Specifica se si dispone già di una licenza di SQL Server. <br/><br/> In tal caso, è possibile applicare la Vantaggio Azure Hybrid quando si impostano le licenze in Azure con il software SQL Server Assurance attivo.

## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

Per modificare le proprietà della valutazione dopo la creazione di una valutazione, procedere come segue:

1. Nel progetto Azure Migrate fare clic su **Server**.
2. In **Azure migrate: individuazione e valutazione** fare clic sul conteggio valutazioni.
3. In **valutazione** fare clic sulla valutazione pertinente > **modificare le proprietà**.
5. Personalizzare le proprietà di valutazione in base alle tabelle precedenti.
6. Fare clic su **Salva** per aggiornare la valutazione.


Quando si crea una valutazione, è anche possibile modificare le proprietà di valutazione.


## <a name="next-steps"></a>Passaggi successivi

- [Altre](concepts-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni delle VM di Azure.
- [Altre](concepts-azure-sql-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni SQL di Azure.
- [Altre](concepts-azure-vmware-solution-assessment-calculation.md) informazioni su come vengono calcolate le valutazioni AVS.

