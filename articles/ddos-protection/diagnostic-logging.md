---
title: Report standard di protezione DDoS di Azure e log di flusso
description: Informazioni su come configurare i report e i log dei flussi.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 7f8e3df927b74cff7e4dc8bf1456600740c07088
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567679"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Visualizzare e configurare la registrazione diagnostica DDoS

Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. I dati di telemetria avanzati vengono esposti tramite monitoraggio di Azure, incluse le metriche dettagliate durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata ulteriormente con [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hub eventi di Azure), OMS log Analytics e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

I log di diagnostica seguenti sono disponibili per la protezione DDoS di Azure standard: 

- **DDoSProtectionNotifications**: le notifiche notificheranno ogni volta che una risorsa IP pubblico è sotto attacco e quando la mitigazione degli attacchi è finita.
- **DDoSMitigationFlowLogs**: i log del flusso di mitigazione degli attacchi consentono di esaminare il traffico eliminato, il traffico trasmesso e altri punti di interesse interessanti durante un attacco DDoS attivo in tempo quasi reale. È possibile inserire il flusso costante di questi dati in Sentinel di Azure o nei sistemi SIEM di terze parti tramite hub eventi per il monitoraggio quasi in tempo reale, intraprendere azioni potenziali e soddisfare le esigenze delle operazioni di difesa.
- **DDoSMitigationReports**: i report di mitigazione degli attacchi usano i dati del protocollo NetFlow aggregati per fornire informazioni dettagliate sull'attacco sulla risorsa. Ogni volta che una risorsa IP pubblica è sotto attacco, la generazione di report verrà avviata contemporaneamente alla mitigazione. Verranno generati un report incrementale ogni 5 minuti e un report post-mitigazione per l'intero periodo della mitigazione. Lo scopo è quello di assicurare che, qualora l'attacco DDoS prosegua per un periodo di tempo più lungo, sia possibile visualizzare lo snapshot più aggiornato del report sulla mitigazione ogni 5 minuti e un riepilogo completo al termine della mitigazione dell'attacco. 
- **AllMetrics**: fornisce tutte le metriche possibili disponibili durante la durata di un attacco DDoS. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare i log di diagnostica DDoS, incluse le notifiche, i report di mitigazione e i log dei flussi di mitigazione. 
> * Abilitare la registrazione diagnostica su tutti gli indirizzi IP pubblici in un ambito definito.
> * Visualizzare i dati di log nelle cartelle di lavoro di.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md) e la protezione DDoS standard deve essere abilitata in una rete virtuale.
- La protezione DDoS esegue il monitoraggio degli indirizzi IP pubblici assegnati alle risorse all'interno di una rete virtuale. Se non si dispone di risorse con indirizzi IP pubblici nella rete virtuale, è innanzitutto necessario creare una risorsa con un indirizzo IP pubblico. È possibile monitorare l'indirizzo IP pubblico di tutte le risorse distribuite tramite Gestione risorse (non classico) elencate in [rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusi i servizi di bilanciamento del carico di Azure in cui le macchine virtuali di back-end si trovano nella rete virtuale), ad eccezione dei app Azure ambienti del servizio. Per continuare questa esercitazione, è possibile creare rapidamente una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).    

## <a name="configure-ddos-diagnostic-logs"></a>Configurare i log di diagnostica DDoS

Se si vuole abilitare automaticamente la registrazione diagnostica su tutti gli indirizzi IP pubblici in un ambiente, ignorare per [abilitare la registrazione diagnostica su tutti gli indirizzi IP pubblici](#enable-diagnostic-logging-on-all-public-ips).

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **Impostazioni**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **indirizzo IP pubblico** per **tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vuole abilitare i log.
6. Selezionare **Aggiungi impostazione di diagnostica**. In **Dettagli categoria** selezionare le opzioni seguenti necessarie, quindi selezionare **Salva**.

    ![Impostazioni di diagnostica DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. In **Dettagli destinazione** selezionare il numero desiderato di opzioni seguenti:

    - **Archivia in un account di archiviazione**: i dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [archiviare i log delle risorse](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [trasmettere i log delle risorse a un hub eventi](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Invia a log Analytics**: scrive i log nel servizio monitoraggio di Azure. Per altre informazioni su questa opzione, vedere [raccogliere i log da usare nei log di monitoraggio di Azure](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Schemi di log

La tabella seguente elenca i nomi dei campi e le descrizioni:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Nome campo | Descrizione |
| --- | --- |
| **TimeGenerated** | Data e ora in formato UTC in cui è stata creata la notifica. |
| **ResourceId** | ID della risorsa dell'indirizzo IP pubblico. |
| **Categoria** | Per le notifiche, questo sarà `DDoSProtectionNotifications` .|
| **ResourceGroup** | Il gruppo di risorse che contiene l'indirizzo IP pubblico e la rete virtuale. |
| **SubscriptionId** | ID sottoscrizione del piano di protezione DDoS. |
| **Risorsa** | Nome dell'indirizzo IP pubblico. |
| **ResourceType** | Questa operazione sarà sempre `PUBLICIPADDRESS` . |
| **NomeOperazione** | Per le notifiche, questo sarà `DDoSProtectionNotifications` .  |
| **Messaggio** | Dettagli dell'attacco. |
| **Tipo** | Tipo di notifica. I valori possibili includono `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Indirizzo IP pubblico. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nome campo | Descrizione |
| --- | --- |
| **TimeGenerated** | Data e ora in formato UTC in cui è stato creato il log di flusso. |
| **ResourceId** | ID della risorsa dell'indirizzo IP pubblico. |
| **Categoria** | Per i log di flusso, questo sarà `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | Il gruppo di risorse che contiene l'indirizzo IP pubblico e la rete virtuale. |
| **SubscriptionId** | ID sottoscrizione del piano di protezione DDoS. |
| **Risorsa** | Nome dell'indirizzo IP pubblico. |
| **ResourceType** | Questa operazione sarà sempre `PUBLICIPADDRESS` . |
| **NomeOperazione** | Per i log di flusso, questo sarà `DDoSMitigationFlowLogs` . |
| **Messaggio** | Dettagli dell'attacco. |
| **SourcePublicIpAddress** | Indirizzo IP pubblico del client che genera il traffico verso l'indirizzo IP pubblico. |
| **SourcePort** | Numero di porta compreso tra 0 e 65535. |
| **DestPublicIpAddress** | Indirizzo IP pubblico. |
| **Porta** | Numero di porta compreso tra 0 e 65535. |
| **Protocollo** | Tipo di protocollo. I valori possibili includono `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nome campo | Descrizione |
| --- | --- |
| **TimeGenerated** | Data e ora in formato UTC in cui è stato creato il report. |
| **ResourceId** | ID della risorsa dell'indirizzo IP pubblico. |
| **Categoria** | Per le notifiche, questo sarà `DDoSProtectionNotifications` .|
| **ResourceGroup** | Il gruppo di risorse che contiene l'indirizzo IP pubblico e la rete virtuale. |
| **SubscriptionId** | ID sottoscrizione del piano di protezione DDoS. |
| **Risorsa** | Nome dell'indirizzo IP pubblico. |
| **ResourceType** | Questa operazione sarà sempre `PUBLICIPADDRESS` . |
| **NomeOperazione** | Per i report di mitigazione, questo sarà `DDoSMitigationReports` . |
| **ReportType** | I valori possibili includono `Incremental` , `PostMitigation` .|
| **MitigationPeriodStart** | Data e ora in formato UTC in cui è stata avviata la mitigazione.  |
| **MitigationPeriodEnd** | Data e ora in formato UTC in cui è terminata la mitigazione. |
| **IPAddress** | Indirizzo IP pubblico. |
| **AttackVectors** |  Suddivisione dei tipi di attacco. Le chiavi includono `TCP SYN flood` , `TCP flood` , `UDP flood` , `UDP reflection` , `Other packet flood` .|
| **TrafficOverview** |  Suddivisione del traffico di attacco. Le chiavi includono `Total packets` ,, `Total packets dropped` `Total TCP packets` , `Total TCP packets dropped` , `Total UDP packets` , `Total UDP packets dropped` , `Total Other packets` , `Total Other packets dropped` . |
| **Protocolli** | Suddivisione dei protocolli necessari. Le chiavi includono `TCP` , `UDP` , `Other` . |
| **DropReasons** | Suddivisione dei motivi per i pacchetti eliminati. Le chiavi includono `Protocol violation invalid TCP syn` ,, `Protocol violation invalid TCP` `Protocol violation invalid UDP` , `UDP reflection` , `TCP rate limit exceeded` , `UDP rate limit exceeded` , `Destination limit exceeded` , `Other packet flood` , `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | Suddivisione dei primi 10 paesi di origine del traffico in ingresso. |
| **TopSourceCountriesForDroppedPackets** | Suddivisione dei primi 10 paesi di origine del traffico di attacco che è/è stato mitigato. |
| **TopSourceASNs** | Suddivisione dei primi 10 numeri del sistema autonomo di origine (ASN) del traffico in ingresso.  |
| **SourceContinents** | Suddivisione dei continenti di origine per il traffico in ingresso. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Abilitare la registrazione diagnostica su tutti gli indirizzi IP pubblici

Questo [modello](https://aka.ms/ddosdiaglogs) crea una definizione di criteri di Azure per abilitare automaticamente la registrazione diagnostica in tutti i registri IP pubblici in un ambito definito.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FPolicy%20-%20DDOS%20Enable%20Diagnostic%20Logging%2FAzure%20Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Visualizzazione dei dati di log nelle cartelle di lavoro

### <a name="azure-sentinel-data-connector"></a>Connettore dati sentinella di Azure

È possibile connettere i log ad Azure Sentinel, visualizzare e analizzare i dati nelle cartelle di lavoro, creare avvisi personalizzati e incorporarli nei processi di analisi. Per connettersi ad Azure Sentinel, vedere [connettersi ad Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Connettore DDoS di Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Cartella di lavoro protezione DDoS di Azure

È possibile usare [questo modello di Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) per distribuire una cartella di lavoro di analisi di attacco. Questa cartella di lavoro consente di visualizzare i dati di attacco in diversi pannelli filtrabili per comprendere facilmente il contenuto. 

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![Cartella di lavoro protezione DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Convalida e verifica

Per simulare un attacco DDoS per la convalida dei log, vedere [Validate DDoS Detection](test-through-simulations.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

- Configurare i log di diagnostica DDoS, incluse le notifiche, i report di mitigazione e i log dei flussi di mitigazione. 
- Abilitare la registrazione diagnostica su tutti gli indirizzi IP pubblici in un ambito definito.
- Visualizzare i dati di log nelle cartelle di lavoro di.

Per informazioni su come configurare gli avvisi di attacco, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Visualizzare e configurare gli avvisi di protezione DDoS](alerts.md)
