---
title: Aree e zone di disponibilità in Azure
description: Informazioni sulle aree e zone di disponibilità in Azure per soddisfare i requisiti tecnici e normativi.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 01/26/2021
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 0b67f113fb8ab3835419a75697e60a732d4ee390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896054"
---
# <a name="regions-and-availability-zones-in-azure"></a>Aree e zone di disponibilità in Azure

I servizi Microsoft Azure sono disponibili a livello globale per guidare le operazioni cloud a un livello ottimale. È possibile scegliere l'area migliore per le proprie esigenze in base a considerazioni tecniche e normative: funzionalità del servizio, residenza dei dati, requisiti di conformità e latenza.

## <a name="terminology"></a>Terminologia

Per comprendere meglio le aree e zone di disponibilità in Azure, è utile comprendere i termini o i concetti chiave.

| Termine o concetto | Descrizione |
| --- | --- |
| region | Set di data center distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza regionale dedicata. |
| geography | Un'area del mondo che contiene almeno un'area di Azure. Le aree geografiche definiscono un mercato discreto che mantiene i limiti di residenza e di conformità dei dati. Le aree geografiche consentono ai clienti con esigenze specifiche a livello di residenza dei dati e conformità di mantenere vicini i propri dati e le proprie applicazioni. Le aree geografiche sono a tolleranza d'errore per resistere a un errore di area completa attraverso la connessione all'infrastruttura di rete a capacità elevata dedicata. |
| Zona di disponibilità | Percorsi fisici univoci all'interno di un'area. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. |
| area consigliata | Area che fornisce la più ampia gamma di funzionalità del servizio ed è progettata per supportare zone di disponibilità ora o in futuro. Queste sono indicate nel portale di Azure come **consigliato**. |
| area alternativa (altro) | Area che estende il footprint di Azure entro un limite di residenza dei dati in cui esiste anche un'area consigliata. Le aree alternative consentono di ottimizzare la latenza e forniscono una seconda area per le esigenze di ripristino di emergenza. Non sono progettati per supportare la zone di disponibilità (anche se Azure esegue una valutazione regolare di queste aree per determinare se devono diventare aree consigliate). Queste sono indicate nel portale di Azure come **altro**. |
| servizio Foundation | Un servizio di base di Azure disponibile in tutte le aree in cui l'area è disponibile a livello generale. |
| servizio mainstream | Un servizio di Azure disponibile in tutte le aree consigliate entro 12 mesi dalla disponibilità generale a livello di area/servizio o alla disponibilità basata su richiesta in aree alternative. |
| servizio specializzato | Un servizio di Azure che è una disponibilità basata su richiesta tra le aree supportate da hardware personalizzato o specializzato. |
| servizio a livello di area | Un servizio di Azure distribuito a livello di area e consente al cliente di specificare l'area in cui verrà distribuito il servizio. Per un elenco completo, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| servizio non a livello di area | Un servizio di Azure per il quale non esiste alcuna dipendenza da una specifica area di Azure. I servizi non a livello di area vengono distribuiti in due o più aree e se si verifica un errore a livello di area, l'istanza del servizio in un'altra area continua a servire i clienti. Per un elenco completo, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regioni

Un'area è un set di centri dati distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza regionale dedicata. Azure offre la flessibilità necessaria per distribuire le applicazioni in cui è necessario, tra cui più aree, per offrire resilienza tra aree diverse. Per altre informazioni, vedere [Panoramica del pilastro di resilienza](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zone di disponibilità

Una zona di disponibilità è un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma Azure riconosce questa distribuzione tra i domini di aggiornamento per assicurarsi che le macchine virtuali in zone diverse non siano pianificate per l'aggiornamento nello stesso momento.

È possibile configurare la disponibilità elevata nell'architettura delle applicazioni includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:

- **Servizi di zona** : una risorsa è bloccata a una zona specifica, ad esempio macchine virtuali, dischi gestiti, indirizzi IP standard o
- **Servizi con ridondanza della zona** : quando la piattaforma Azure viene replicata automaticamente tra le zone, ad esempio l'archiviazione con ridondanza della zona, il database SQL.

Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza.
 
![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Gli identificatori delle zone di disponibilità (i numeri 1, 2 e 3 nell'immagine precedente) vengono mappati logicamente alle zone fisiche effettive per ogni sottoscrizione in modo indipendente. Ciò significa che Zona 1 di disponibilità in una determinata sottoscrizione possono fare riferimento a una zona fisica diversa rispetto alla disponibilità Zona 1 in una sottoscrizione diversa. Di conseguenza, è consigliabile non fare affidamento sugli ID della zona di disponibilità tra sottoscrizioni diverse per la selezione host per macchina virtuale.

## <a name="region-and-service-categories"></a>Aree e categorie di servizi

L'approccio di Azure sulla disponibilità dei servizi di Azure tra le aree è più adatto per la descrizione tramite l'espressione dei servizi resi disponibili nelle aree consigliate e nelle aree alternative.

- **Area consigliata** : area che fornisce la più ampia gamma di funzionalità del servizio ed è progettata per supportare zone di disponibilità ora o in futuro. Queste sono indicate nel portale di Azure come **consigliato**.
- **Area alternativa (altra)** : area che estende il footprint di Azure entro un limite di residenza dei dati in cui esiste anche un'area consigliata. Le aree alternative consentono di ottimizzare la latenza e forniscono una seconda area per le esigenze di ripristino di emergenza. Non sono progettati per supportare la zone di disponibilità (anche se Azure esegue una valutazione regolare di queste aree per determinare se devono diventare aree consigliate). Queste sono indicate nel portale di Azure come **altro**.

### <a name="comparing-region-types"></a>Confronto tra tipi di area

I servizi di Azure sono raggruppati in tre categorie: fondamentale, principale e specializzato. I criteri generali di Azure per la distribuzione dei servizi in una determinata area sono principalmente basati sul tipo di area, sulle categorie di servizi e sulla domanda dei clienti:

- **Fondamento** : disponibile in tutte le aree consigliate e alternative quando l'area è disponibile a livello generale oppure entro 12 mesi da un nuovo servizio di base che diventa disponibile a livello generale.
- **Mainstream** : disponibile in tutte le aree consigliate entro 12 mesi dalla disponibilità generale a livello di area/servizio. basati su richiesta in aree alternative (molte sono già distribuite in un ampio subset di aree alternative).
- Offerte di servizi mirate **specializzate** , spesso incentrate sul settore o basate su hardware personalizzato o specializzato. Disponibilità basata su richiesta tra le aree (molte sono già distribuite in un ampio subset di aree consigliate).

Per visualizzare i servizi distribuiti in una determinata area, nonché la roadmap futura per l'anteprima o la disponibilità generale dei servizi in un'area, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Se un'offerta di servizio non è disponibile in un'area specifica, è possibile condividere il proprio interesse contattando il rappresentante Microsoft.

| Tipo di area | Non a livello di area | Fondamentale | Mainstream | Specializzata | Zone di disponibilità | Residenza dei dati |
| --- | --- | --- | --- | --- | --- | --- |
| Consigliato | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Basata su richiesta | :heavy_check_mark: | :heavy_check_mark: |
| Alternativo | :heavy_check_mark: | :heavy_check_mark: | Basata su richiesta | Basata su richiesta | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Servizi per categoria

Come indicato in precedenza, Azure classifica i servizi in tre categorie: fondamentale, mainstream e specializzato. Le categorie di servizio vengono assegnate a livello generale. Spesso i servizi avviano il ciclo di vita come servizio specializzato e gli aumenti di richiesta e utilizzo possono essere promossi al mainstream o alla base. La tabella seguente elenca la categoria per i servizi come base, mainstream o specializzata. Per la tabella è necessario tenere presente quanto segue:

- Alcuni servizi non sono a livello di area. Per informazioni e un elenco di servizi non regionali, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).
- Le macchine virtuali di generazione meno recenti non sono elencate. Per ulteriori informazioni, vedere la documentazione relativa alle [generazioni precedenti di dimensioni di macchine virtuali](../virtual-machines/sizes-previous-gen.md)
- . Ai servizi non viene assegnata una categoria fino alla disponibilità generale (GA). Per informazioni e per un elenco dei servizi in anteprima, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Fondamentale                          | Mainstream                                        | Specializzata                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Account di archiviazione                      | Gestione API                                    | API di Azure per FHIR                                   |
> | Gateway applicazione                   | Configurazione app                                 | Azure Analysis Services                              |
> | Backup di Azure                          | Servizio app                                       | Servizi cognitivi di Azure: rilevamento anomalie           |
> | Azure Cosmos DB                       | Automazione                                        | Servizi cognitivi di Azure: Visione personalizzata              |
> | Azure Data Lake Storage Gen2          | Servizi di dominio Azure Active Directory            | Servizi cognitivi di Azure: riconoscimento moduli            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Servizi cognitivi di Azure: personalizzatore               |
> | IP pubblico di Azure                       | Cache Redis di Azure                             | Servizi cognitivi di Azure: QnA Maker                  |
> | Database SQL di Azure                    | Ricerca cognitiva di Azure                            | Database di Azure per MariaDB                           |
> | Azure SQL: Istanza gestita          | Servizi cognitivi di Azure                          | Servizio Migrazione del database di Azure                     |
> | Servizi cloud                        | Servizi cognitivi di Azure: Visione artificiale         | Modulo di protezione hardware dedicato di Azure                                  |
> | Servizi cloud: Av2-Series            | Servizi cognitivi di Azure: Content Moderator       | Gemelli digitali di Azure                                  |
> | Servizi cloud: Dv2-Series            | Servizi cognitivi di Azure: viso                    | Bot per l'integrità di Azure                                     |
> | Servizi cloud: Dv3-Series            | Servizi cognitivi di Azure: Lettore immersivo        | Cache HPC di Azure                                      |
> | Servizi cloud: Ev3-Series            | Servizi cognitivi di Azure: Language Understanding  | Azure Lab Services                                   |
> | Servizi cloud: indirizzi IP a livello di istanza    | Servizi cognitivi di Azure: servizi vocali         | Azure NetApp Files                                   |
> | Servizi cloud: IP riservato           | Servizi cognitivi di Azure: Analisi del testo          | Servizio Azure SignalR                                |
> | Archiviazione su disco                          | Servizi cognitivi di Azure: Translator              | Servizio cloud Spring di Azure                           |
> | Hub eventi                            | Esplora dati di Azure                               | Azure Time Series Insights                           |
> | Key Vault                             | Condivisione dati di Azure                                  | Soluzione Azure VMware                                |
> | Bilanciamento del carico                         | Database di Azure per MySQL                          | Soluzione Azure VMware di CloudSimple                 |
> | Bus di servizio                           | Database di Azure per PostgreSQL                     | Servizi cloud: serie H                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Archiviazione: livelli di archiviazione BLOB ad accesso frequente/sporadico  | Protezione DDoS di Azure                             | Data Lake Analytics                                  |
> | Archiviazione: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (versione classica)              |
> | Set di scalabilità di macchine virtuali            | Firewall di Azure                                    | Ancoraggi nello spazio                                      |
> | Macchine virtuali                      | Gestione firewall di Azure                            | Archiviazione: Spazio di archiviazione                             |
> | Macchine virtuali: Av2-Series          | Funzioni di Azure                                   | StorSimple                                           |
> | Macchine virtuali: Bs-Series           | Hub IoT Azure                                     | archiviazione su disco Ultra                                   |
> | Macchine virtuali: DSv2-Series         | Servizio Azure Kubernetes                    | Video Indexer                                        |
> | Macchine virtuali: DSv3-Series         | Azure Machine Learning                            | Macchine virtuali: DASv4-Series                       |
> | Macchine virtuali: Dv2-Series          | Monitoraggio di Azure: Application Insights               | Macchine virtuali: DAv4-Series                        |
> | Macchine virtuali: Dv3-Series          | Monitoraggio di Azure: Log Analytics                      | Macchine virtuali: serie DCsv2                       |
> | Macchine virtuali: ESv3-Series         | Collegamento privato di Azure                                | Macchine virtuali: EASv4-Series                       |
> | Macchine virtuali: Ev3-Series          | Azure Red Hat OpenShift                           | Macchine virtuali: EAv4-Series                        |
> | Macchine virtuali: indirizzi IP a livello di istanza  | Azure Site Recovery                               | Macchine virtuali: HBv1-Series                        |
> | Macchine virtuali: IP riservato         | Analisi di flusso di Azure                            | Macchine virtuali: HBv2-Series                        |
> | Rete virtuale                       | Azure Synapse Analytics                           | Macchine virtuali: HCv1-Series                        |
> | Gateway VPN                           | Batch                                             | Macchine virtuali: serie H                           |
> |                                       | Servizi cloud: serie M                          | Macchine virtuali: LSv2-Series                        |
> |                                       | Istanze di contenitore                               | Macchine virtuali: Mv2-Series                         |
> |                                       | Registro contenitori                                | Macchine virtuali: NCv3-Series                        |
> |                                       | Data Factory                                      | Macchine virtuali: NDv2-Series                        |
> |                                       | Griglia di eventi                                        | Macchine virtuali: NVv3-Series                        |
> |                                       | HDInsight                                         | Macchine virtuali: NVv4-Series                        |> 
> |                                       | App per la logica                                        | Macchine virtuali: SAP HANA in istanze Large di Azure  |
> |                                       | Servizi multimediali                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Hub di notifica                                 |                                                      |
> |                                       | Archiviazione BLOB Premium                              |                                                      |
> |                                       | Archiviazione file Premium                             |                                                      |
> |                                       | Macchine virtuali: Ddsv4-Series                    |                                                      |
> |                                       | Macchine virtuali: Ddv4-Series                     |                                                      |
> |                                       | Macchine virtuali: Dsv4-Series                     |                                                      |
> |                                       | Macchine virtuali: Dv4-Series                      |                                                      |
> |                                       | Macchine virtuali: Edsv4-Series                    |                                                      |
> |                                       | Macchine virtuali: Edv4-Series                     |                                                      |
> |                                       | Macchine virtuali: Esv4-Series                     |                                                      |
> |                                       | Macchine virtuali: Ev4-Series                      |                                                      |
> |                                       | Macchine virtuali: Fsv2-Series                     |                                                      |
> |                                       | Macchine virtuali: serie M                        |                                                      |
> |                                       | Rete WAN virtuale                                       |                                                      |


## <a name="next-steps"></a>Passaggi successivi

- [Aree che supportano zone di disponibilità in Azure](az-region.md)
- [Modelli di avvio rapido](https://aka.ms/azqs)
