---
title: Eseguire la migrazione Monitoraggio connessione da Monitoraggio connessione
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a Monitoraggio connessione da Monitoraggio connessione.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: fc5bcc7f0cd11160b33bb6501526fce9f29d710b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366386"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Eseguire la migrazione Monitoraggio connessione da Monitoraggio connessione (versione classica)

> [!IMPORTANT]
> A partire dal 1° luglio 2021 non sarà possibile aggiungere nuovi monitoraggi di connessione in Monitoraggio connessione (versione classica), ma è possibile continuare a usare i monitoraggi connessioni esistenti creati prima del 1° luglio 2021. Per ridurre al minimo l'interruzione del servizio per i carichi di lavoro correnti, eseguire la migrazione da [Monitoraggio connessione (versione classica)](migrate-to-connection-monitor-from-connection-monitor-classic.md)  al nuovo Monitoraggio connessione in Azure Network Watcher prima del 29 febbraio 2024.

È possibile eseguire la migrazione dei monitoraggi delle connessioni esistenti a nuovi monitoraggi Monitoraggio connessione con pochi clic e senza tempi di inattività. Per altre informazioni sui vantaggi, vedere [Monitoraggio connessione](./connection-monitor-overview.md).

## <a name="key-points-to-note"></a>Punti chiave da notare

La migrazione consente di produrre i risultati seguenti:

* Gli agenti e le impostazioni del firewall funzionano così come sono. Non sono necessarie modifiche. 
* I monitoraggi delle connessioni esistenti vengono mappati Monitoraggio connessione > gruppo di test > test. Selezionando Modifica **è** possibile visualizzare e modificare le proprietà del nuovo Monitoraggio connessione, scaricare un modello per apportare modifiche Monitoraggio connessione e inviarlo tramite Azure Resource Manager. 
* Le macchine virtuali di Azure con l'estensione Network Watcher inviano dati sia all'area di lavoro che alle metriche. Monitoraggio connessione rende disponibili i dati tramite le nuove metriche (ChecksFailedPercent e RoundTripTimeMs) anziché le metriche precedenti (ProbesFailedPercent e AverageRoundtripMs). Le metriche precedenti verranno migrate alle nuove metriche come ProbesFailedPercent -> ChecksFailedPercent e AverageRoundtripMs -> RoundTripTimeMs.
* Monitoraggio dei dati:
   * **Avvisi**: migrazione automatica alle nuove metriche.
   * **Dashboard e integrazioni:** richiedere la modifica manuale del set di metriche. 
    
## <a name="prerequisites"></a>Prerequisiti

1. Se si usa un'area di lavoro personalizzata, assicurarsi che Network Watcher sia abilitato nella sottoscrizione e nell'area dell'area di lavoro Log Analytics. In caso contrario, verrà visualizzato l'errore "Prima di provare a eseguire la migrazione, abilitare l'estensione Network Watcher nella sottoscrizione di selezione e nella posizione dell'area di lavoro di La selezione".
1. Se per le macchine virtuali usate come origini in Monitoraggio connessione (versione classica) l'estensione Network Watcher non è più abilitata, verrà visualizzato un messaggio di errore che indica che non è possibile importare i monitoraggi delle connessioni con i test seguenti perché in una o più macchine virtuali di Azure non è installata l'estensione Network Watcher. Installare l'estensione network watcher e fare clic su Aggiorna per importarle."



## <a name="migrate-the-connection-monitors"></a>Eseguire la migrazione dei monitoraggi delle connessioni

1. Per eseguire la migrazione dei monitoraggi di connessione meno recenti a quello più recente, **selezionare** Monitoraggio connessione e quindi selezionare Esegui la migrazione **di Monitoraggi connessione**.

    ![Screenshot che mostra la migrazione dei monitoraggi di connessione Monitoraggio connessione.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selezionare la sottoscrizione e i monitoraggi di connessione di cui si vuole eseguire la migrazione, quindi selezionare **Esegui la migrazione selezionata.** 

Con pochi clic, è stata eseguita la migrazione dei monitoraggi di connessione esistenti Monitoraggio connessione. Dopo la migrazione da CM (classico) a CM, non sarà possibile visualizzare il monitoraggio in CM (classico)

È ora possibile personalizzare le Monitoraggio connessione, modificare l'area di lavoro predefinita, scaricare i modelli e controllare lo stato della migrazione. 

Dopo l'inizio della migrazione, vengono apportate le modifiche seguenti: 
* La Azure Resource Manager viene modificata nel monitoraggio della connessione più recente.
    * Il nome, l'area e la sottoscrizione del monitoraggio della connessione rimangono invariati. L'ID risorsa non è interessato.
    * A meno che il monitoraggio della connessione non sia personalizzato, viene creata un'area di lavoro Log Analytics predefinita nella sottoscrizione e nell'area del monitoraggio connessione. Questa area di lavoro è la posizione in cui vengono archiviati i dati di monitoraggio. Anche i dati dei risultati del test vengono archiviati nelle metriche.
    * Viene eseguita la migrazione di ogni test a un gruppo di test *denominato defaultTestGroup*.
    * Gli endpoint di origine e di destinazione vengono creati e usati nel nuovo gruppo di test. I nomi predefiniti sono *defaultSourceEndpoint* e *defaultDestinationEndpoint.*
    * La porta di destinazione e l'intervallo di probe vengono spostati in una configurazione di test denominata *defaultTestConfiguration*. Il protocollo viene impostato in base ai valori della porta. Le soglie di esito positivo e altre proprietà facoltative vengono lasciati vuoti.
* Viene eseguita la migrazione degli avvisi delle metriche Monitoraggio connessione avvisi delle metriche. Le metriche sono diverse, quindi la modifica. Per altre informazioni, vedere [Monitoraggio della connettività di rete con Monitoraggio connessione](./connection-monitor-overview.md#metrics-in-azure-monitor).
* I monitoraggi delle connessioni di cui è stata eseguita la migrazione non vengono più visualizzati come soluzione di monitoraggio delle connessioni precedente. Sono ora disponibili per l'uso solo in Monitoraggio connessione.
* È necessario eseguire manualmente la migrazione di eventuali integrazioni esterne, ad esempio dashboard in Power BI e Grafana, e integrazioni con sistemi Security Information and Event Management (SIEM). Questo è l'unico passaggio manuale che è necessario eseguire per eseguire la migrazione della configurazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle Monitoraggio connessione, vedere:
* [Eseguire la migrazione da Monitoraggio prestazioni rete a Monitoraggio connessione](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Creare Monitoraggio connessione usando il portale di Azure](./connection-monitor-create-using-portal.md)
