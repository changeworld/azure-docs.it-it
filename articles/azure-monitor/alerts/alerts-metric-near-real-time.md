---
title: Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure
description: Informazioni di riferimento sulle metriche e i log di supporto per gli avvisi delle metriche in Monitoraggio di Azure
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614637"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure

Monitoraggio di Azure supporta ora un [nuovo tipo di avvisi delle metriche](../platform/alerts-overview.md) che presenta vantaggi significativi rispetto agli [avvisi delle metriche classici](./alerts-classic.overview.md). Sono disponibili metriche per un [lungo elenco di servizi di Azure](../platform/metrics-supported.md). Gli avvisi più recenti supportano un sottoinsieme (in espansione) di tipi di risorse. In questo articolo viene illustrato tale sottoinsieme.

È anche possibile usare gli avvisi delle metriche più recenti sui dati di log più diffusi archiviati in un'area di lavoro Log Analytics estratti come metriche. Per altre informazioni, vedere [Avvisi delle metriche per i log](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Supporto per il portale, PowerShell, l'interfaccia della riga di comando, REST
Attualmente, è possibile creare avvisi delle metriche più recenti solo nell'portale di Azure, nell' [API REST](/rest/api/monitor/metricalerts/)o nei [modelli di gestione risorse](./alerts-metric-create-templates.md). Il supporto per la configurazione di nuovi avvisi con PowerShell e l'interfaccia della riga di comando di Azure versione 2.0 e versioni successive sarà presto disponibile.

## <a name="metrics-and-dimensions-supported"></a>Metriche e dimensioni supportate
Gli avvisi delle metriche più recenti supportano l'invio di avvisi per le metriche che usano le dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Tutte le metriche supportate e le dimensioni applicabili possono essere esplorate e visualizzate da [Monitoraggio di Azure - Esplora metriche](../essentials/metrics-charts.md).

Ecco l'elenco completo delle origini delle metriche di monitoraggio di Azure supportate dagli avvisi più recenti:

|Tipo di risorsa  |Dimensioni supportate |Avvisi per più risorse| Metriche disponibili|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Sì | No | |
|Microsoft.ApiManagement/service | Sì | No | [Gestione API](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Sì | No | [Configurazione app](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | Sì | No | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Sì| No | [Account di Automazione](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | No | No | [Soluzione Azure VMware](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Sì | No | [Account Batch](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Sì | Sì | [Cache Redis di Azure](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | No | No | [Servizi cloud classici](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | No | No | [Macchine virtuali classiche](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Sì | No | [Account di archiviazione (versione classica)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Sì | No | [Account di archiviazione (versione classica)-BLOB](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Sì | No | [Account di archiviazione (versione classica)-file](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Sì | No | [Account di archiviazione (versione classica)-code](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Sì | No | [Account di archiviazione (versione classica)-tabelle](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Sì | No | [Servizi cognitivi](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Sì | Sì<sup>1</sup> | [Macchine virtuali](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Sì | No |[Set di scalabilità di macchine virtuali](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Sì| No | [Gruppi di contenitori](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | No | No | [Registri contenitori](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Sì | No | [Cluster gestiti](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sì | Sì | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Sì| No | [Data factory V1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Sì | No | [Data factory V2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Sì | No | [Condivisioni di dati](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | No | No | [DB per MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | No | No |[Database per MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | No | No | [Database per PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | No | No | [DB per PostgreSQL V2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Sì | No | [DB per PostgreSQL (server flessibili)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Sì | No |[Hub IoT](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Sì | No | [Servizi Device provisioning](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Sì | No | [Gemelli digitali](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Sì | No | [Cosmos DB](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Sì | No | [Domini di Griglia di eventi](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Sì | No | [Argomenti del sistema di griglia di eventi](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Sì | No | [Argomenti di Griglia di eventi](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Sì| No | [Cluster di hub eventi](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Sì| No | [Hub eventi](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Sì | No | [Cluster HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Sì | No | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Sì |Sì |[Insiemi di credenziali](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Sì |No |[Cluster Esplora dati](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Sì | No |[Ambienti del servizio di integrazione](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | No | No |[App per la logica](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Sì | No | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | Sì | No | [Mappa account](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | No | No | [Servizi multimediali](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Sì | No | [Endpoint di streaming di servizi multimediali](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Sì | Sì | [Pool di capacità di Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Sì | Sì | [Volumi di Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Sì | No | [Gateway applicazione](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Sì | No | [Firewall](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | No | No | [Zone DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Sì | No |[Circuiti ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Sì | No |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (solo per SKU Standard)| Sì| No | [Bilanciamento del carico](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| No | No | [Gateway NAT](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| No | No | [Endpoint privati](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| No | No | [Servizi di collegamento privato](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | No | No | [Indirizzi IP pubblici](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sì | No | [Profili di gestione traffico](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sì | No | [Aree di lavoro di Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Sì | No | [Peering](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Sì | No | [Servizi di peering](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | No | No | [Capacities](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Sì | No | [Inoltri](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | No | No | [Servizi di ricerca](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Sì | No | [Bus di servizio](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | No | Sì | [Istanze gestite SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | No | Sì | [Database SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | No | Sì | [Pool elastici SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Sì | No | [Account di archiviazione](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Sì| No | [Account di archiviazione-BLOB](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Sì| No | [Account di archiviazione-file](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Sì| No | [Account di archiviazione-code](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Sì| No | [Account di archiviazione-tabelle](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Sì | No | [Cache HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Sì | No | [Servizi di sincronizzazione archiviazione](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Sì | No | [Analisi di flusso](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Sì | No | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. sinapsi/Workspaces/bigDataPools | Sì | No | [Pool di Apache Spark di sinapsi Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. sinapsi/Workspaces/sqlpools | Sì | No | [Pool SQL di sinapsi Analytics](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Sì | No | [Macchine virtuali CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Sì | No | [ambiente del servizio app pool multiruolo](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sì | No | [Pool di lavoro ambiente del servizio app](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sì | No | [Piani di servizio app](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sì | No | [Servizi app e Funzioni](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Sì | No | [Slot del servizio app](../platform/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> non supportato per le metriche di rete della macchina virtuale (rete totale, totale di rete, flussi in ingresso, flussi in uscita, velocità di creazione massima dei flussi in ingresso, velocità di creazione massima dei flussi in uscita) e metriche personalizzate.

## <a name="payload-schema"></a>Schema del payload

> [!NOTE]
> È anche possibile usare lo [schema di avviso comune](./alerts-common-schema.md), che offre il vantaggio di avere un singolo payload di avviso estendibile e unificato per tutti i servizi di avviso in monitoraggio di Azure, per le integrazioni del webhook. [Informazioni sulle definizioni comuni dello schema di avviso.](./alerts-common-schema-definitions.md)


L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi delle metriche più recenti se viene usato un [gruppo di azioni](./action-groups.md) correttamente configurato:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla nuova [esperienza di avvisi](../platform/alerts-overview.md).
* Per altre informazioni, fare riferimento agli [avvisi di log in Azure](./alerts-unified-log.md).
* Informazioni sugli [avvisi in Azure](../platform/alerts-overview.md).