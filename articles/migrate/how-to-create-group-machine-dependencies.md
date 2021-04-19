---
title: Configurare l'analisi delle dipendenze basata su agente in Azure Migrate
description: Questo articolo descrive come configurare l'analisi delle dipendenze basata su agente in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714777"
---
# <a name="set-up-dependency-visualization"></a>Configurare la visualizzazione delle dipendenze

Questo articolo descrive come configurare l'analisi delle dipendenze basata su agente in Azure Migrate: Individuazione e valutazione. [L'analisi delle](concepts-dependency-visualization.md) dipendenze consente di identificare e comprendere le dipendenze tra i server di cui si vuole valutare ed eseguire la migrazione ad Azure.

## <a name="before-you-start"></a>Prima di iniziare

- Esaminare i requisiti di supporto e distribuzione per l'analisi delle dipendenze basate su agente per:
    - [Server nell'ambiente VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Server fisici](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Server nell'ambiente Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Assicurarsi di:
    - Disporre di un progetto di Azure Migrate. In caso contrario, [crearne](./create-manage-projects.md) uno.
    - Verificare di aver aggiunto [lo](how-to-assess.md) strumento Azure Migrate: Individuazione e valutazione al progetto.
    - Configurare [un'appliance Azure Migrate](migrate-appliance.md) per individuare i server locali. L'appliance individua i server locali e invia metadati e dati sulle prestazioni Azure Migrate: individuazione e valutazione. Configurare un'appliance per:
        - [Server nell'ambiente VMware](how-to-set-up-appliance-vmware.md)
        - [Server nell'ambiente Hyper-V](how-to-set-up-appliance-hyper-v.md)
        - [Server fisici](how-to-set-up-appliance-physical.md)
- Per usare la visualizzazione delle dipendenze, associare [un'area di lavoro Log Analytics](../azure-monitor/logs/manage-access.md) a un Azure Migrate progetto:
    - È possibile collegare un'area di lavoro solo dopo la configurazione dell'appliance Azure Migrate e l'individuazione dei server nel Azure Migrate progetto.
    - Assicurarsi di avere un'area di lavoro nella sottoscrizione che contiene il Azure Migrate progetto.
    - L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.
    - L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - È possibile associare un'area di lavoro Log Analytics nuova o esistente a un Azure Migrate progetto.
    - L'area di lavoro viene collegata la prima volta che si configura la visualizzazione delle dipendenze per un server. Non è possibile modificare l'area di lavoro per un progetto Azure Migrate dopo che è stata aggiunta.
    - In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.

## <a name="associate-a-workspace"></a>Associare un'area di lavoro

1. Dopo aver individuato i server per la valutazione, in **Server**  >  **Azure Migrate: Individuazione** e valutazione fare clic su **Panoramica**.  
2. In **Azure Migrate: Individuazione e valutazione** fare clic su Informazioni di **base**.
3. **Nell'area di lavoro di OMS** fare clic su Richiede **configurazione**.

     ![Configurare un'area di lavoro Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. In **Configura area di lavoro OMS** specificare se si vuole creare una nuova area di lavoro o usarne una esistente.
    - È possibile selezionare un'area di lavoro esistente da tutte le aree di lavoro nella sottoscrizione del progetto.
    - Per associarla, è necessario l'accesso in lettura all'area di lavoro.
5. Se si crea una nuova area di lavoro, selezionare una posizione.

    ![Aggiungere una nuova area di lavoro](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Informazioni su come configurare](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) l'area di lavoro OMS per la connettività degli endpoint privati.  

## <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

Installare gli agenti in ogni server da analizzare.

> [!NOTE]
> Per i server monitorati da System Center Operations Manager 2012 R2 o versioni successive, non è necessario installare l'agente MMA. Mapping dei servizi si integra con Operations Manager. [Seguire le](../azure-monitor/vm/service-map-scom.md#prerequisites) linee guida per l'integrazione.

1. In **Azure Migrate: Individuazione e valutazione fare** clic su Server **individuati**.
2. Per ogni server da analizzare con la visualizzazione delle dipendenze, nella colonna **Dipendenze** fare clic su **Richiede l'installazione dell'agente.**
3. Nella pagina **Dipendenze** scaricare MMA e Dependency Agent per Windows o Linux.
4. In **Configura agente MMA copiare** l'ID e la chiave dell'area di lavoro. Sono necessari quando si installa l'agente MMA.

    ![Installare gli agenti](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installare MMA

Installare MMA in ogni server Windows o Linux che si vuole analizzare.

### <a name="install-mma-on-a-windows-server"></a>Installare MMA in un server Windows

Per installare l'agente in un server Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio Gestione configurazione [o Intigua.](https://www.intigua.com/intigua-for-azure-migration)
- [Altre informazioni](../azure-monitor/agents/log-analytics-agent.md#installation-options) sull'uso di questi metodi per installare l'agente MMA.
- L'agente MMA può essere installato anche usando questo [script](https://github.com/brianbar-MSFT/Install-MMA).
- [Altre informazioni](../azure-monitor/agents/agents-overview.md#supported-operating-systems) sui sistemi operativi Windows supportati da MMA.

### <a name="install-mma-on-a-linux-server"></a>Installare MMA in un server Linux

Per installare MMA in un server Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](../azure-monitor/agents/agents-overview.md#supported-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA. 

## <a name="install-the-dependency-agent"></a>Installare Dependency Agent

1. Per installare Dependency Agent in un server Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un server Linux, eseguire l'installazione come radice usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Altre informazioni](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.
- [Altre informazioni](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) sui sistemi operativi supportati da Dependency Agent.


## <a name="create-a-group-using-dependency-visualization"></a>Creare un gruppo usando la visualizzazione delle dipendenze

Creare ora un gruppo per la valutazione. 


> [!NOTE]
> I gruppi per cui si desidera visualizzare le dipendenze non devono contenere più di 10 server. Se sono disponibili più di 10 server, suddividerli in gruppi più piccoli.

1. In **Azure Migrate: Individuazione e valutazione** fare clic su Server **individuati**.
2. Nella colonna **Dipendenze** fare clic **su Visualizza dipendenze** per ogni server da esaminare.
3. Nella mappa delle dipendenze è possibile visualizzare quanto segue:
    - Connessioni TCP in ingresso (client) e in uscita (server), da e verso il server.
    - I server dipendenti in cui non sono installati gli agenti di dipendenza vengono raggruppati in base ai numeri di porta.
    - I server dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno del server. Espandere ogni casella del server per visualizzare i processi.
    - Proprietà del server (inclusi FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella del server per visualizzare i dettagli.

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo.
    - Per impostazione predefinita, l'intervallo è un'ora. 
    - È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
    - L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare Monitoraggio di Azure per eseguire query sui dati dipendenti per un periodo più lungo.

5. Dopo aver identificato i server dipendenti da raggruppare, usare CTRL+clic per selezionare più server sulla mappa e fare clic su **Raggruppa computer**.
6. Specificare un nome di gruppo.
7. Verificare che i server dipendenti siano individuati da Azure Migrate.

    - Se un server dipendente non viene individuato da Azure Migrate: individuazione e valutazione, non è possibile aggiungerlo al gruppo.
    - Per aggiungere un server, eseguire di nuovo l'individuazione e verificare che il server sia stato individuato.

8. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Dopo aver creato il gruppo, è consigliabile installare gli agenti in tutti i server del gruppo e quindi visualizzare le dipendenze per l'intero gruppo.

## <a name="query-dependency-data-in-azure-monitor"></a>Eseguire query sui dati delle dipendenze Monitoraggio di Azure

È possibile eseguire query sui dati sulle dipendenze acquisiti Mapping dei servizi nell'area di lavoro Log Analytics associata al Azure Migrate progetto. Log Analytics viene usato per scrivere ed eseguire query Monitoraggio di Azure log.

- [Informazioni su come](../azure-monitor/vm/service-map.md#log-analytics-records) cercare Mapping dei servizi dati in Log Analytics.
- [Panoramica della scrittura](../azure-monitor/logs/get-started-queries.md) di query di log in [Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

Eseguire una query per i dati delle dipendenze come segue:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Azure Migrate: Individuazione e valutazione fare** clic su **Panoramica.** Fare clic sulla freccia giù per espandere **Informazioni di base.**
3. **Nell'area di lavoro di OMS** fare clic sul nome dell'area di lavoro.
3. Nella pagina Dell'area di lavoro Log Analytics > **Generale** fare clic su **Log**.
4. Scrivere la query e fare clic su **Esegui**.

### <a name="sample-queries"></a>Query di esempio

Ecco alcune query di esempio che è possibile usare per estrarre i dati sulle dipendenze.

- È possibile modificare le query per estrarre i punti dati preferiti.
- [Esaminare](../azure-monitor/vm/service-map.md#log-analytics-records) un elenco completo dei record dei dati sulle dipendenze.
- [Esaminare query](../azure-monitor/vm/service-map.md#sample-log-searches) di esempio aggiuntive.

#### <a name="sample-review-inbound-connections"></a>Esempio: Esaminare le connessioni in ingresso

Esaminare le connessioni in ingresso per un set di server.

- I record nella tabella per le metriche di connessione (VMConnection) non rappresentano singole connessioni di rete fisiche.
- Più connessioni di rete fisiche vengono raggruppate in una connessione logica.
- [Altre informazioni su](../azure-monitor/vm/service-map.md#connections) come vengono aggregati i dati di connessione di rete fisica in VMConnection.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Esempio: Riepilogare i dati inviati e ricevuti

Questo esempio riepiloga il volume di dati inviati e ricevuti sulle connessioni in ingresso tra un set di server.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Passaggi successivi

[Creare una valutazione](how-to-create-assessment.md) per un gruppo.
