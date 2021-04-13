---
title: Eseguire la migrazione Monitoraggio connessione da Monitoraggio prestazioni rete
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a Monitoraggio connessione da Monitoraggio prestazioni rete.
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
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366352"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Eseguire la migrazione Monitoraggio connessione da Monitoraggio prestazioni rete

> [!IMPORTANT]
> A partire dal 1° luglio 2021 non sarà possibile aggiungere nuovi test in un'area di lavoro esistente o abilitare una nuova area di lavoro con Monitoraggio prestazioni rete. È possibile continuare a usare i test creati prima del 1° luglio 2021. Per ridurre al minimo l'interruzione del servizio per i carichi di lavoro correnti, eseguire la migrazione dei test da Monitoraggio prestazioni rete al nuovo Monitoraggio connessione in Azure Network Watcher prima del 29 febbraio 2024.

È possibile eseguire la migrazione dei test da Monitoraggio prestazioni rete (NPM) a nuovi test Monitoraggio connessione con un solo clic e senza tempi di inattività. Per altre informazioni sui vantaggi, vedere [Monitoraggio connessione](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Punti chiave da notare

La migrazione consente di produrre i risultati seguenti:

* Gli agenti locali e le impostazioni del firewall funzionano così come sono. Non sono necessarie modifiche. Gli agenti di Log Analytics installati nelle macchine virtuali di Azure devono essere sostituiti con l Network Watcher [estensione](../virtual-machines/extensions/network-watcher-windows.md).
* I test esistenti vengono mappati Monitoraggio connessione > gruppo di test > test. Selezionando Modifica **,** è possibile visualizzare e modificare le proprietà del nuovo Monitoraggio connessione, scaricare un modello per apportarvi modifiche e inviare il modello tramite Azure Resource Manager.
* Gli agenti inviano dati sia all'area di lavoro Log Analytics che alle metriche.
* Monitoraggio dei dati:
   * **Dati in Log Analytics:** prima della migrazione, i dati rimangono nell'area di lavoro in cui NPM è configurato nella tabella NetworkMonitoring. Dopo la migrazione, i dati passano alla tabella NetworkMonitoring, alla tabella NWConnectionMonitorTestResult e alla tabella NWConnectionMonitorPathResult nella stessa area di lavoro. Dopo aver disabilitato i test in NPM, i dati vengono archiviati solo nella tabella NWConnectionMonitorTestResult e nella tabella NWConnectionMonitorPathResult.
   * **Avvisi, dashboard e integrazioni** basati su log: è necessario modificare manualmente le query in base alla nuova tabella NWConnectionMonitorTestResult e alla tabella NWConnectionMonitorPathResult. Per creare nuovamente gli avvisi nelle metriche, vedere Monitoraggio [della connettività di rete con Monitoraggio connessione](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Per il monitoraggio di ExpressRoute:
    * **Perdita e latenza end-to-end:** Monitoraggio connessione questa funzionalità sarà più semplice rispetto a Monitoraggio prestazioni rete perché gli utenti non devono configurare i circuiti e i peering da monitorare. I circuiti nel percorso verranno individuati automaticamente, i dati saranno disponibili nelle metriche (più velocemente di LA, in cui NPM ha archiviato i risultati). Anche la topologia funzionerà così come è.
    * **Misurazioni della larghezza di** banda: con il lancio delle metriche relative alla larghezza di banda, l'approccio basato sull'analisi dei log di Monitoraggio prestazioni rete non è stato efficace nel monitoraggio della larghezza di banda per i clienti di ExpressRoute. Questa funzionalità non è ora disponibile in Monitoraggio connessione.
    
## <a name="prerequisites"></a>Prerequisiti

* Assicurarsi che la Network Watcher sia abilitata nella sottoscrizione e nell'area dell'area di lavoro Log Analytics. In caso contrario, verrà visualizzato l'errore "Prima di provare a eseguire la migrazione, abilitare l'estensione Network Watcher nella sottoscrizione di selezione e nella posizione dell'area di lavoro di La selezione".
* Nel caso in cui una macchina virtuale di Azure appartenente a un'area o a una sottoscrizione diversa da quella dell'area di lavoro Log Analytics sia usata come endpoint, assicurarsi che la Network Watcher sia abilitata per la sottoscrizione e l'area.   
* Le macchine virtuali di Azure con gli agenti di Log Analytics installati devono essere abilitate con l'Network Watcher predefinita.

## <a name="migrate-the-tests"></a>Eseguire la migrazione dei test

Per eseguire la migrazione dei test Monitoraggio prestazioni rete a Monitoraggio connessione, eseguire le operazioni seguenti:

1. In Network Watcher **selezionare** Monitoraggio connessione e quindi selezionare la scheda Migrate tests from NPM (Esegui la migrazione dei test **da NPM).** 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Eseguire la migrazione dei test Monitoraggio prestazioni rete a Monitoraggio connessione" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Negli elenchi a discesa selezionare la sottoscrizione e l'area di lavoro e quindi selezionare la funzionalità NPM di cui si vuole eseguire la migrazione. 
1. Selezionare **Importa per** eseguire la migrazione dei test.
* Se NPM non è abilitato nell'area di lavoro, verrà visualizzato un errore che indica che non è stata trovata alcuna configurazione NPM valida. 
* Se nella funzionalità scelta nel passaggio 2 non sono presenti test, verrà visualizzato un errore che indica che l'area di lavoro selezionata non ha <feature> la configurazione.
* Se non sono presenti test validi, verrà visualizzato un errore che indica che l'area di lavoro selezionata non ha test validi
* I test possono contenere agenti che non sono più attivi, ma che potrebbero essere stati attivi in passato. Verrà visualizzato un errore che indica che "Pochi test contengono agenti che non sono più attivi. Elenco di agenti inattivi : {0} . Questi agenti possono essere in esecuzione in passato, ma vengono arrestati o non eseguiti più. Abilitare gli agenti ed eseguire la migrazione Monitoraggio connessione. Fare clic su Continua per eseguire la migrazione dei test che non contengono agenti non attivi."

Dopo l'inizio della migrazione, vengono apportate le modifiche seguenti: 
* Viene creata una nuova risorsa di Monitoraggio connessione.
   * Viene creato un monitoraggio delle connessioni per area e sottoscrizione. Per i test con agenti locali, il nuovo nome del monitoraggio delle connessioni è formattato come `<workspaceName>_"workspace_region_name"` . Per i test con gli agenti di Azure, il nuovo nome del monitoraggio delle connessioni è formattato come `<workspaceName>_<Azure_region_name>` .
   * I dati di monitoraggio vengono ora archiviati nella stessa area di lavoro Log Analytics in cui è abilitato NPM, in nuove tabelle denominate tabella NWConnectionMonitorTestResult e tabella NWConnectionMonitorPathResult. 
   * Il nome del test viene portato avanti come nome del gruppo di test. Non viene eseguita la migrazione della descrizione del test.
   * Gli endpoint di origine e di destinazione vengono creati e usati nel nuovo gruppo di test. Per gli agenti locali, gli endpoint sono formattati come `<workspaceName>_<FQDN of on-premises machine>` . Non viene eseguita la migrazione della descrizione dell'agente.
   * La porta di destinazione e l'intervallo di probe vengono spostati in una configurazione di test denominata `TC_<protocol>_<port>` e `TC_<protocol>_<port>_AppThresholds` . Il protocollo viene impostato in base ai valori della porta. Per ICMP, le configurazioni di test sono denominate `TC_<protocol>` come e `TC_<protocol>_AppThresholds` . Le soglie di esito positivo e altre proprietà facoltative, se impostate, vengono migrate. In caso contrario, vengono lasciati vuoti.
   * Se i test di migrazione contengono agenti che non sono in esecuzione, è necessario abilitare gli agenti ed eseguire di nuovo la migrazione.
* NPM non è disabilitato, quindi i test migrati possono continuare a inviare dati alla tabella NetworkMonitoring, alla tabella NWConnectionMonitorTestResult e alla tabella NWConnectionMonitorPathResult. Questo approccio garantisce che le integrazioni e gli avvisi basati su log esistenti non siano interessati.
* Il monitoraggio della connessione appena creato è visibile in Monitoraggio connessione.

Dopo la migrazione, assicurarsi di:
* Disabilitare manualmente i test in NPM. Fino a quando non si eseguono queste operazione, i costi continueranno a essere addebitati. 
* Durante la disabilitazione di NPM, creare nuovamente gli avvisi nelle tabelle NWConnectionMonitorTestResult e NWConnectionMonitorPathResult oppure usare le metriche. 
* Eseguire la migrazione di eventuali integrazioni esterne alle tabelle NWConnectionMonitorTestResult e NWConnectionMonitorPathResult. Esempi di integrazioni esterne sono i dashboard in Power BI e Grafana e le integrazioni con Security Information and Event Management (SIEM).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle Monitoraggio connessione, vedere:
* [Eseguire la migrazione Monitoraggio connessione (versione classica) a Monitoraggio connessione](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Creare Monitoraggio connessione usando il portale di Azure](./connection-monitor-create-using-portal.md)
