---
title: Trasmettere i dati di monitoraggio di Azure all'hub eventi e ai partner esterni
description: Informazioni su come trasmettere i dati di monitoraggio di Azure a un hub eventi per ottenere i dati in uno strumento di analisi o informazioni di sicurezza e gestione degli eventi partner.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768742"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Trasmettere i dati di monitoraggio di Azure a un hub eventi o a un partner esterno

Monitoraggio di Azure offre una soluzione completa di monitoraggio dello stack per applicazioni e servizi in Azure, in altri cloud e in locale. Oltre a usare Monitoraggio di Azure per analizzare i dati e usarli per diversi scenari di monitoraggio, potrebbe essere necessario inviarli ad altri strumenti di monitoraggio nell'ambiente in uso. Nella maggior parte dei casi, il metodo più efficace per trasmettere i dati di monitoraggio a strumenti esterni è [l'uso di Hub eventi di Azure](../../event-hubs/index.yml). Questo articolo fornisce una breve descrizione su come eseguire questa operazione e quindi elenca alcuni dei partner a cui è possibile inviare i dati. Alcuni hanno una speciale integrazione con Monitoraggio di Azure e possono essere ospitati in Azure.  

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Prima di configurare il flusso per qualsiasi origine dati, è necessario creare uno spazio dei [nomi di Hub eventi e un hub eventi.](../../event-hubs/event-hubs-create.md) Lo spazio dei nomi e l'hub eventi sono la destinazione di tutti i dati di monitoraggio. Uno spazio dei nomi di Hub eventi è un raggruppamento logico di hub eventi che condividono gli stessi criteri di accesso, così come un account di archiviazione contiene al suo interno singoli BLOB. Considerare i dettagli seguenti sullo spazio dei nomi di Hub eventi e sugli hub eventi che si usano per lo streaming dei dati di monitoraggio:

* Il numero di unità elaborate consente di aumentare la scala della velocità effettiva per gli hub eventi. In genere è necessaria una sola unità di velocità effettiva. Se è necessario aumentare le prestazioni con l'aumento dell'utilizzo dei log, è possibile aumentare manualmente il numero di unità elaborate per lo spazio dei nomi o abilitare l'aumento automatico.
* Il numero di partizioni consente di parallelizzare il consumo tra molti consumer. Una singola partizione può supportare fino a 20 MBps o circa 20.000 messaggi al secondo. A seconda dello strumento che utilizza i dati, può essere supportato o meno il consumo di dati di più partizioni. Se non si è certi del numero di partizioni da impostare, è ragionevole iniziare con quattro partizioni.
* La conservazione dei messaggi nell'hub eventi è impostata su almeno 7 giorni. Se lo strumento utilizzato rimane in stato di insoddi per più di un giorno, lo strumento potrà riprendere dal punto in cui è stato arrestato per gli eventi di un massimo di 7 giorni.
* È consigliabile usare il gruppo di consumer predefinito per l'hub eventi. Non è necessario creare altri gruppi di consumer o usare un gruppo di consumer distinto, a meno che non siano previsti due diversi strumenti che utilizzano gli stessi dati dello stesso hub eventi.
* Per il log attività di Azure, si seleziona uno spazio dei nomi di Hub eventi e Monitoraggio di Azure crea un hub eventi all'interno di tale spazio dei nomi denominato _insights-logs-operational-logs._ Per altri tipi di log, è possibile scegliere un hub eventi esistente o Monitoraggio di Azure creare un hub eventi per categoria di log.
* Le porte in uscita 5671 e 5672 devono in genere essere aperte nel computer o nella rete virtuale che utilizza i dati dall'hub eventi.

## <a name="monitoring-data-available"></a>Dati di monitoraggio disponibili
[Origini dei dati di monitoraggio Monitoraggio di Azure](../agents/data-sources.md) descrive i diversi livelli di dati per le applicazioni di Azure e i tipi di dati di monitoraggio disponibili per ognuno. La tabella seguente elenca ognuno di questi livelli e una descrizione del modo in cui i dati possono essere trasmessi a un hub eventi. Per altri dettagli, seguire i collegamenti forniti.

| Livello | Data | Metodo |
|:---|:---|:---|
| [Tenant di Azure](../agents/data-sources.md#azure-tenant) | Log di controllo di Azure Active Directory | Configurare un'impostazione di diagnostica del tenant nel tenant di AAD. Per  [informazioni dettagliate, vedere Esercitazione: Trasmettere Azure Active Directory log in un hub eventi](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) di Azure. |
| [Sottoscrizione di Azure](../agents/data-sources.md#azure-subscription) | Azure Activity Log | Creare un profilo di log per esportare gli eventi del log attività in Hub eventi.  Per [informazioni dettagliate,](../essentials/resource-logs.md#send-to-azure-event-hubs) vedere Eseguire lo streaming Hub eventi di Azure log della piattaforma Azure. |
| [Risorse di Azure](../agents/data-sources.md#azure-resources) | Metriche della piattaforma<br> Log risorse |Entrambi i tipi di dati vengono inviati a un hub eventi tramite un'impostazione di diagnostica delle risorse. Per [informazioni dettagliate, vedere Trasmettere i log delle risorse](../essentials/resource-logs.md#send-to-azure-event-hubs) di Azure a un hub eventi. |
| [Sistema operativo (guest)](../agents/data-sources.md#operating-system-guest) | Macchine virtuali di Azure | Installare [l'Diagnostica di Azure in](../agents/diagnostics-extension-overview.md) macchine virtuali Windows e Linux in Azure. Per [informazioni dettagliate](../agents/diagnostics-extension-stream-event-hubs.md) sulle macchine virtuali [Linux,](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) vedere Streaming Diagnostica di Azure dati nel percorso critico usando Hub eventi e Usare l'estensione di diagnostica Linux per monitorare metriche e log. |
| [Codice dell'applicazione](../agents/data-sources.md#application-code) | Application Insights | Application Insights non fornisce un metodo diretto per trasmettere i dati agli hub eventi. È possibile [configurare l'esportazione](../app/export-telemetry.md) continua dei dati Application Insights in un account di archiviazione e quindi usare un'app per la logica per inviare i dati a un hub eventi, come descritto in Streaming manuale con App per la [logica](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manuale con App per la logica
Per i dati che non è possibile trasmettere direttamente a un hub eventi, è possibile [](../../connectors/connectors-create-api-azureblobstorage.md#add-action) scrivere in Archiviazione di Azure e quindi usare un'app per la logica attivata dal tempo che estrae i dati dall'archivio BLOB ed estrae i dati come messaggio all'hub [eventi](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Strumenti per i partner con Monitoraggio di Azure integrazione

Il routing dei dati di monitoraggio a un hub eventi con Monitoraggio di Azure consente di integrarsi facilmente con gli strumenti di monitoraggio e SIEM esterni. Di seguito sono riportati alcuni esempi di strumenti Monitoraggio di Azure di integrazione:

| Strumento | Ospitato in Azure | Descrizione |
|:---|:---| :---|
|  IBM QRadar | No | Microsoft Azure DSM e Microsoft Azure Event Hub Protocol sono scaricabili dal [sito Web del supporto IBM](https://www.ibm.com/support). Per altre informazioni sull'integrazione con Azure, vedere [QRadar DSM configuration](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar). |
| Splunk | No | [Microsoft Azure Add-On per Splunk](https://splunkbase.splunk.com/app/3757/) è un progetto open source disponibile in Splunkbase. <br><br> Se non è possibile installare un componente aggiuntivo nell'istanza di Splunk, se ad esempio si usa un proxy o si esegue su Splunk Cloud, è possibile inoltrare questi eventi all'agente di raccolta eventi HTTP Splunk usando la funzione di Azure per [Splunk,](https://github.com/Microsoft/AzureFunctionforSplunkVS)attivata da nuovi messaggi nell'hub eventi. |
| sumologic | No | Le istruzioni per configurare SumoLogic per l'utilizzo dei dati da un hub eventi sono disponibili in Raccogliere log per l'app di controllo di [Azure da Hub eventi.](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) |
| ArcSight | No | Il connettore intelligente dell'hub eventi di Azure ArcSight è disponibile come parte della [raccolta di connettori intelligenti ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Server Syslog | No | Se si vuole trasmettere Monitoraggio di Azure dati direttamente a un server syslog, è possibile usare una soluzione [basata su una funzione di Azure.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm | No| Le istruzioni per configurare LogRhythm per raccogliere i log da un hub eventi sono disponibili [qui.](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/) 
|Logz.io | Sì | Per altre informazioni, vedere Introduzione al monitoraggio e alla registrazione usando [Logz.io per le app Java in esecuzione in Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Potrebbero essere disponibili anche altri partner. Per un elenco più completo di tutti i Monitoraggio di Azure partner e delle relative funzionalità, vedere Monitoraggio di Azure [integrazioni dei partner.](../partners.md)

## <a name="next-steps"></a>Passaggi successivi
* [Archiviare il log attività in un account di archiviazione](./activity-log.md#legacy-collection-methods)
* [Leggere la panoramica del log attività di Azure](../essentials/platform-logs-overview.md)
* [Configurare un avviso in base a un evento del log attività](../alerts/alerts-log-webhook.md)
