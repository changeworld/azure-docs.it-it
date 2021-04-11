---
title: Esegui la migrazione a monitoraggio connessione da Monitoraggio prestazioni rete
titleSuffix: Azure Network Watcher
description: Informazioni su come eseguire la migrazione a monitoraggio connessione da Monitoraggio prestazioni rete.
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
ms.openlocfilehash: 18d0a24de6f0775fdb35799512f9796a323d353a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045485"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Esegui la migrazione a monitoraggio connessione da Monitoraggio prestazioni rete

> [!IMPORTANT]
> A partire dall'1 luglio 2021, non sarà possibile aggiungere nuovi test in un'area di lavoro esistente o abilitare una nuova area di lavoro con Monitoraggio prestazioni rete. È possibile continuare a usare i test creati prima dell'1 luglio 2021. Per ridurre al minimo le rotture dei servizi nei carichi di lavoro correnti, migrare i test da Monitoraggio prestazioni rete al nuovo monitoraggio della connessione in Azure Network Watcher prima del 29 febbraio 2024.

È possibile eseguire la migrazione dei test da Monitoraggio prestazioni rete (NPM) a un monitoraggio della connessione nuovo e migliorato con un solo clic e senza tempi di inattività. Per ulteriori informazioni sui vantaggi, vedere [Connection Monitor](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Punti chiave da notare

La migrazione contribuisce a produrre i risultati seguenti:

* Gli agenti locali e le impostazioni del firewall funzionano così come sono. Non sono necessarie modifiche. Gli agenti di Log Analytics installati nelle macchine virtuali di Azure devono essere sostituiti con l' [estensione del Network Watcher](../virtual-machines/extensions/network-watcher-windows.md).
* I test esistenti vengono mappati a monitoraggio connessione > gruppo di test > formato di test. Selezionando **modifica**, è possibile visualizzare e modificare le proprietà del nuovo monitoraggio della connessione, scaricare un modello per apportare modifiche e inviare il modello tramite Azure Resource Manager.
* Gli agenti inviano i dati all'area di lavoro Log Analytics e alle metriche.
* Monitoraggio dei dati:
   * **Dati in log Analytics**: prima della migrazione, i dati rimangono nell'area di lavoro in cui NPM è configurato nella tabella NetworkMonitoring. Dopo la migrazione, i dati vengono inseriti nella tabella NetworkMonitoring, nella tabella NWConnectionMonitorTestResult e nella tabella NWConnectionMonitorPathResult nella stessa area di lavoro. Dopo che i test sono stati disabilitati in NPM, i dati vengono archiviati solo nella tabella NWConnectionMonitorTestResult e nella tabella NWConnectionMonitorPathResult.
   * **Avvisi basati su log, dashboard e integrazioni**: è necessario modificare manualmente le query in base alla nuova tabella NWConnectionMonitorTestResult e alla tabella NWConnectionMonitorPathResult. Per ricreare gli avvisi nelle metriche, vedere monitoraggio della [connettività di rete con monitoraggio connessione](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Per il monitoraggio di ExpressRoute:
    * **Perdita e latenza end-to-end**: il monitoraggio della connessione verrà alimentato e sarà più semplice di NPM perché gli utenti non devono configurare i circuiti e i peering da monitorare. I circuiti nel percorso verranno automaticamente individuati, i dati saranno disponibili nelle metriche (più velocemente rispetto alla posizione in cui NPM ha archiviato i risultati). La topologia funzionerà così com'è.
    * **Misurazioni della larghezza di banda**: con l'avvio delle metriche relative alla larghezza di banda, l'approccio basato su log Analytics di NPM non era efficace nel monitoraggio della larghezza di banda per i clienti ExpressRoute. Questa funzionalità non è ora disponibile in monitoraggio connessione.
    
## <a name="prerequisites"></a>Prerequisiti

* Assicurarsi che Network Watcher sia abilitato nella sottoscrizione e nell'area dell'area di lavoro Log Analytics. 
* Se la macchina virtuale di Azure appartenente a un'area o a una sottoscrizione diversa da quella di Log Analytics area di lavoro viene usata come endpoint, assicurarsi che Network Watcher sia abilitato per tale sottoscrizione e area.   
* Le macchine virtuali di Azure con agenti di Log Analytics installati devono essere abilitate con l'estensione Network Watcher.

## <a name="migrate-the-tests"></a>Eseguire la migrazione dei test

Per eseguire la migrazione dei test da Monitoraggio prestazioni rete a monitoraggio connessione, eseguire le operazioni seguenti:

1. In Network Watcher selezionare **monitoraggio connessione**, quindi selezionare la scheda **Esegui la migrazione dei test da NPM** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Esegui la migrazione dei test da Monitoraggio prestazioni rete a monitoraggio connessione" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Negli elenchi a discesa selezionare la sottoscrizione e l'area di lavoro, quindi selezionare la funzionalità NPM di cui si vuole eseguire la migrazione. 
1. Selezionare **Importa** per eseguire la migrazione dei test.

Dopo l'inizio della migrazione, vengono applicate le modifiche seguenti: 
* Viene creata una nuova risorsa di monitoraggio della connessione.
   * Viene creato un monitoraggio della connessione per area e sottoscrizione. Per i test con agenti locali, il nome del nuovo monitoraggio della connessione viene formattato come `<workspaceName>_"workspace_region_name"` . Per i test con agenti di Azure, il nome del nuovo monitoraggio della connessione è formattato come `<workspaceName>_<Azure_region_name>` .
   * I dati di monitoraggio vengono ora archiviati nella stessa area di lavoro Log Analytics in cui è abilitato NPM, in nuove tabelle denominate tabella NWConnectionMonitorTestResult e tabella NWConnectionMonitorPathResult. 
   * Il nome del test viene portato avanti come nome del gruppo di test. La descrizione del test non viene migrata.
   * Gli endpoint di origine e di destinazione vengono creati e usati nel nuovo gruppo di test. Per gli agenti locali, gli endpoint vengono formattati come `<workspaceName>_<FQDN of on-premises machine>` . La descrizione dell'agente non viene migrata.
   * La porta di destinazione e l'intervallo di sondaggio vengono spostati in una configurazione di test denominata `TC_<protocol>_<port>` e `TC_<protocol>_<port>_AppThresholds` . Il protocollo viene impostato in base ai valori di porta. Per ICMP, le configurazioni di test sono denominate come `TC_<protocol>` e `TC_<protocol>_AppThresholds` . Soglie di esito positivo e altre proprietà facoltative se viene eseguita la migrazione di set; in caso contrario, vengono lasciati vuoti.
   * Se i test migranti contengono agenti che non sono in esecuzione, è necessario abilitare gli agenti ed eseguire di nuovo la migrazione.
* NPM non è disabilitato, quindi i test migrati possono continuare a inviare dati alla tabella NetworkMonitoring, alla tabella NWConnectionMonitorTestResult e alla tabella NWConnectionMonitorPathResult. Questo approccio assicura che gli avvisi e le integrazioni esistenti basati su log non siano interessati.
* Il monitoraggio connessione appena creato è visibile in monitoraggio connessione.

Dopo la migrazione, assicurarsi di:
* Disabilitare manualmente i test in NPM. Fino a quando non si esegue questa operazione, si continuerà a essere addebitati. 
* Durante la disabilitazione di NPM, ricreare gli avvisi nelle tabelle NWConnectionMonitorTestResult e NWConnectionMonitorPathResult o usare le metriche. 
* Eseguire la migrazione di tutte le integrazioni esterne nelle tabelle NWConnectionMonitorTestResult e NWConnectionMonitorPathResult. Esempi di integrazioni esterne sono dashboard in Power BI e Grafana e integrazioni con i sistemi di gestione delle informazioni e degli eventi di sicurezza (SIEM).


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su monitoraggio connessione, vedere:
* [Eseguire la migrazione da monitoraggio connessione (versione classica) a monitoraggio connessione](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Creazione di un monitoraggio della connessione tramite il portale di Azure](./connection-monitor-create-using-portal.md)