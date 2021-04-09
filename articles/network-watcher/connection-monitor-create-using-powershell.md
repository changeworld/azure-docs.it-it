---
title: Creare un monitoraggio della connessione-PowerShell
titleSuffix: Azure Network Watcher
description: Informazioni su come creare un monitoraggio della connessione tramite PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833032"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Creare un monitoraggio della connessione tramite PowerShell

> [!IMPORTANT]
> A partire dall'1 luglio 2021, non sarà possibile aggiungere nuovi test in un'area di lavoro esistente o abilitare una nuova area di lavoro in Monitoraggio prestazioni rete. Non sarà inoltre possibile aggiungere nuovi monitoraggi connessione in monitoraggio connessione (versione classica). È possibile continuare a utilizzare i test e i monitoraggi connessione creati prima dell'1 luglio 2021. Per ridurre al minimo le rotture dei servizi nei carichi di lavoro correnti, [eseguire la migrazione dei test da monitoraggio prestazioni rete ](migrate-to-connection-monitor-from-network-performance-monitor.md) o  [eseguire la migrazione dal monitoraggio della connessione (versione classica)](migrate-to-connection-monitor-from-connection-monitor-classic.md) al nuovo monitoraggio della connessione in Azure Network Watcher prima del 29 febbraio 2024.


Informazioni su come usare la funzionalità di monitoraggio della connessione di Azure Network Watcher per monitorare la comunicazione tra le risorse.


## <a name="before-you-begin"></a>Prima di iniziare

Nei monitoraggi connessione creati con monitoraggio connessione è possibile aggiungere come origini sia computer locali che macchine virtuali di Azure (VM). Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o indirizzo IP.

Un monitoraggio connessione include le seguenti entità:

* **Risorsa di monitoraggio della connessione**: una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà della risorsa di monitoraggio della connessione.
* **Endpoint**: un'origine o una destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono macchine virtuali di Azure, agenti locali, URL e indirizzi IP.
* **Configurazione di test**: una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test**: il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test**: la combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

    ![Diagramma che mostra un monitoraggio della connessione, che definisce la relazione tra i gruppi di test e i test.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Passaggi per la creazione di un monitoraggio della connessione

Usare i comandi seguenti per creare un monitoraggio della connessione tramite PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Descrizione delle proprietà

* **ConnectionMonitorName**: nome della risorsa di monitoraggio della connessione.

* **Sub**: ID sottoscrizione della sottoscrizione in cui si desidera creare un monitoraggio della connessione.

* **NW**: Network Watcher ID di risorsa in cui viene creato un monitoraggio della connessione.

* **Location**: area in cui viene creato un monitoraggio della connessione.

* **Endpoint**
    * **Nome**: nome univoco per ogni endpoint.
    * **ID risorsa**: per gli endpoint di Azure, l'ID risorsa fa riferimento all'id risorsa Azure Resource Manager per le macchine virtuali. Per gli endpoint non di Azure, l'ID risorsa fa riferimento all'ID di risorsa Azure Resource Manager per l'area di lavoro Log Analytics collegata a agenti non Azure.
    * **Address**: applicabile solo se l'ID risorsa non è specificato o se l'ID risorsa è nell'area di lavoro log Analytics. Se usato senza un ID di risorsa, può corrispondere all'URL o all'indirizzo IP di qualsiasi endpoint pubblico. Se usato con un ID di risorsa Log Analytics, si riferisce al nome di dominio completo dell'agente di monitoraggio.
    * **Filtro**: per gli endpoint non di Azure, usare i filtri per selezionare gli agenti di monitoraggio dall'area di lavoro log Analytics nella risorsa di monitoraggio della connessione. Se i filtri non sono impostati, per il monitoraggio è possibile utilizzare tutti gli agenti appartenenti all'area di lavoro Log Analytics.
        * **Tipo**: impostare come **indirizzo dell'agente**.
        * **Address**: impostare come nome di dominio completo dell'agente locale.

* **Gruppi di test**
    * **Nome**: assegnare un nome al gruppo di test.
    * **Origini**: scegliere gli endpoint creati in precedenza. Per gli endpoint di origine basati su Azure è necessario che sia installata l'estensione Azure Network Watcher; per gli endpoint di origine non basati su Azure è necessario che sia installato un agente di Log Analytics di Azure. Per installare un agente per l'origine, vedere [Install Monitoring Agents](./connection-monitor-overview.md#install-monitoring-agents).
    * **Destinazioni**: scegliere gli endpoint creati in precedenza. È possibile monitorare la connettività alle macchine virtuali di Azure o a qualsiasi endpoint (un indirizzo IP pubblico, un URL o un nome di dominio completo) specificandone tali elementi come destinazioni. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, URL di Office 365, URL di Dynamics 365 ed endpoint personalizzati.
    * **Disable**: Disabilita il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test.

* **Configurazioni di test**
    * **Nome**: assegnare un nome alla configurazione di test.
    * **TestFrequencySec**: specificare la frequenza con cui le origini eseguono il ping delle destinazioni sul protocollo e sulla porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Le origini testano la connettività alle destinazioni in base al valore scelto. Se ad esempio si seleziona 30 secondi, le origini controllano la connettività alla destinazione almeno una volta in un periodo di 30 secondi.
    * **Protocollo**: scegliere TCP, ICMP, http o HTTPS. A seconda del protocollo, è inoltre possibile selezionare le seguenti configurazioni specifiche del protocollo:
        * **preferHTTPS**: specificare se usare HTTPS su http.
        * **porta**: specificare la porta di destinazione scelta.
        * **disableTraceRoute**: impedisce alle origini di individuare la topologia e RTT hop per hop. Si applica ai gruppi di test con TCP o ICMP.
        * **Metodo**: selezionare il metodo di richiesta HTTP (Get o post). Questo vale per le configurazioni di test con HTTP.
        * **percorso**: specificare i parametri del percorso da accodare all'URL.
        * **validStatusCodes**: scegliere i codici di stato applicabili. Se il codice di risposta non corrisponde, viene visualizzato un messaggio di diagnostica.
        * **requestHeaders**: specificare le stringhe di intestazione della richiesta personalizzate che passano alla destinazione.
    * **Soglia di esito positivo**: impostare le soglie per i parametri di rete seguenti:
        * **checksFailedPercent**: impostare la percentuale di verifiche che possono avere esito negativo quando le origini controllano la connettività alle destinazioni usando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di verifiche non riuscite potrebbe corrispondere alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo campo rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
        * **roundTripTimeMs**: consente di impostare il tempo necessario per la connessione alla destinazione tramite la configurazione di test, in millisecondi.

## <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi connessione presentano i limiti di scalabilità seguenti:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio connessione: 20

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come analizzare i dati di monitoraggio e impostare gli avvisi](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Informazioni [su come diagnosticare i problemi nella rete](./connection-monitor-overview.md#diagnose-issues-in-your-network).
