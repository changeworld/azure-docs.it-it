---
title: Servizi e categorie supportati nei log delle risorse di monitoraggio di Azure
description: Informazioni di riferimento su monitoraggio di Azure comprendono i servizi e lo schema di eventi supportati per i log delle risorse di Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931229"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorie supportate per i log delle risorse di Azure

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica. Il nome è stato modificato nel 2019 ottobre perché i tipi di log raccolti da monitoraggio di Azure sono stati spostati in modo da includere solo la risorsa di Azure.

I [log delle risorse di monitoraggio di Azure](./platform-logs-overview.md) sono log emessi da servizi di Azure che descrivono il funzionamento di tali servizi o risorse. Tutti i log delle risorse disponibili tramite monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità che consente a ogni servizio di emettere proprietà univoche per gli eventi.

Una combinazione del tipo di risorsa (disponibile nella proprietà `resourceId`) e del `category` identifica in modo univoco uno schema. È disponibile uno schema comune per tutti i log delle risorse con campi specifici del servizio e quindi aggiunti per diverse categorie di log. Per altre informazioni, vedere [schema comune e specifico del servizio per i log delle risorse di Azure]()


## <a name="costs"></a>Costi

L'invio e l'archiviazione di dati in Log Analytics, archiviazione di Azure e/o hub eventi sono i costi associati. Il licenziatario potrà pagare il costo per ottenere i dati in queste posizioni e per mantenerlo.  I log delle risorse sono un tipo di dati che è possibile inviare a questi percorsi. Sono previsti costi aggiuntivi [per esportare alcune categorie di log di risorse](https://azure.microsoft.com/pricing/details/monitor/) in queste posizioni, mentre altre sono prive di costi di esportazione. Le specifiche dei costi di esportazione sono elencate nella tabella seguente.

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa

Di seguito è riportato un elenco dei tipi di log disponibili per ogni tipo di risorsa. 

Alcune categorie possono essere supportate solo per tipi specifici di risorse. Se si ritiene che la risorsa non sia presente, vedere la documentazione specifica della risorsa. Ad esempio, le categorie Microsoft. SQL/Servers/databases non sono disponibili per tutti i tipi di database. Per ulteriori informazioni, vedere [informazioni sulla registrazione diagnostica del database SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Se ancora non è presente, è possibile aprire un commento di GitHub nella parte inferiore di questo articolo.
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Motore|Motore|
|Servizio|Servizio|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|GatewayLogs|Log correlati ad ApiManagement Gateway|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ApplicationConsole|Console applicazione|
|SystemLogs|Log di sistema|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DscNodeStatus|Stato del nodo Dsc|
|JobLogs|Log del processo|
|JobStreams|Flussi del processo|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ServiceLog|Log del servizio|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BlockchainApplication|Applicazione blockchain|
|FabricOrderer|Ordinatore infrastruttura|
|FabricPeer|Peer infrastruttura|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BlockchainApplication|Applicazione blockchain|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|WebApplicationFirewallLogs|Log del firewall di applicazione Web|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AzureCdnAccessLog|Log di accesso della rete CDN di Azure|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Evento del flusso di regole del gruppo di sicurezza di rete|Evento del flusso di regole del gruppo di sicurezza di rete|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Audit|Log di controllo|
|RequestResponse|Log richieste e risposte|
|Trace|Registri di traccia|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ContainerRegistryLoginEvents|Eventi di accesso|
|ContainerRegistryRepositoryEvents|Log RepositoryEvent|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|cluster-autoscaler|Ridimensionamento automatico del cluster Kubernetes|
|kube-apiserver|Server API Kubernetes|
|Kube-audit|Controllo Kubernetes|
|kube-controller-manager|Strumento di gestione del controller Kubernetes|
|kube-scheduler|Utilità di pianificazione Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AuditLogs|Log di controllo per le chiamate MiniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. databricks/area di lavoro

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|account|Account databricks|
|clusters|Cluster databricks|
|dBFS|File system di Databricks|
|instancePools|Pool di istanze|
|jobs|Processi di databricks|
|notebook|Notebook di Databricks|
|chiavi private|Segreti di databricks|
|sqlPermissions|Sqlpermissions di databricks|
|ssh|SSH di databricks|
|area di lavoro|Area di lavoro databricks|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ActivityRuns|Log delle esecuzioni di attività pipeline|
|PipelineRuns|Log delle esecuzioni di pipeline|
|TriggerRuns|Log delle esecuzioni trigger|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Audit|Log di controllo|
|Requests|Log delle richieste|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ReceivedShareSnapshots|Snapshot di condivisione ricevuti|
|SentShareSnapshots|Snapshot di condivisione inviati|
|Condivisioni|Condivisioni|
|ShareSubscriptions|Condividi sottoscrizioni|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|MySqlAuditLogs|Log di controllo di MariaDB|
|MySqlSlowLogs|Log del server MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|MySqlAuditLogs|Log di controllo di MySQL|
|MySqlSlowLogs|Log lenti MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|MySqlAuditLogs|Log di controllo di MySQL|
|MySqlSlowLogs|Log server MySQL|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|
|QueryStoreRuntimeStatistics|Statistiche di runtime Query Store PostgreSQL|
|QueryStoreWaitStatistics|Statistiche attesa Query Store PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Checkpoint|Checkpoint|
|Errore|Errore|
|Gestione|Gestione|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Checkpoint|Checkpoint|
|Connessioni|Connessioni|
|Errore|Errore|
|HostRegistration|HostRegistration|
|Gestione|Gestione|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/Workspaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Checkpoint|Checkpoint|
|Errore|Errore|
|Feed|Feed|
|Gestione|Gestione|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|C2DCommands|Comandi da cloud a dispositivo|
|C2DTwinOperations|Operazioni da cloud a dispositivi gemelli|
|Configurazioni|Configurazioni|
|Connessioni|Connessioni|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Operazioni relative alle identità dei dispositivi|
|DeviceStreams|Flussi del dispositivo (anteprima)|
|DeviceTelemetry|Telemetria dei dispositivi|
|DirectMethods|Metodi diretti|
|DistributedTracing|Traccia distribuita (anteprima)|
|FileUploadOperations|Operazioni di caricamento file|
|JobsOperations|Operazioni dei processi|
|Route|Route|
|TwinQueries|Query dei dispositivi gemelli|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DeviceOperations|Operazioni del dispositivo|
|ServiceOperations|Operazioni di servizio|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DeliveryFailures|Log degli errori di recapito|
|PublishFailures|Pubblica log degli errori|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DeliveryFailures|Log degli errori di recapito|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DeliveryFailures|Log degli errori di recapito|
|PublishFailures|Pubblica log degli errori|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ArchiveLogs|Log di archiviazione|
|AutoScaleLogs|Log di scalabilità automatica|
|CustomerManagedKeyUserLogs|Log delle chiavi gestite dal cliente|
|EventHubVNetConnectionEvent|VNet/IP filtraggio dei log di connessione|
|KafkaCoordinatorLogs|Log coordinatore Kafka|
|KafkaUserErrorLogs|Log degli errori utente Kafka|
|OperationalLogs|Log operativi|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AuditLogs|Log di controllo|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AutoscaleEvaluations|Valutazioni sulla scalabilità automatica|
|AutoscaleScaleActions|Azioni di ridimensionamento per la scalabilità automatica|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AppAvailabilityResults|Risultati della disponibilità|
|AppBrowserTimings|Intervalli del browser|
|AppDependencies|Dependencies|
|AppEvents|Eventi|
|AppExceptions|Eccezioni|
|AppMetrics|Metriche|
|AppPageViews|Visualizzazioni pagina|
|AppPerformanceCounters|Contatori delle prestazioni|
|AppRequests|Requests|
|AppSystemEvents|Eventi di sistema|
|AppTraces|Tracce|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AuditEvent|Log di controllo|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Comando|Comando|
|FailedIngestion|Operazioni di inserimento non riuscite|
|IngestionBatching|Inserimento dati in batch|
|Query|Query|
|SucceededIngestion|Operazioni di inserimento riuscite|
|TableDetails|Dettagli tabella|
|TableUsageStatistics|Statistiche sull'utilizzo delle tabelle|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|KeyDeliveryRequests|Richieste di distribuzione delle chiavi|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|
|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|
|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AzureFirewallApplicationRule|Regola di applicazione di Firewall di Azure|
|AzureFirewallNetworkRule|Regola di rete di Firewall di Azure|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BastionAuditLogs|Log di controllo Bastion|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|PeeringRouteLog|Log delle tabelle di routing di peering|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|FrontdoorAccessLog|Log di accesso a Frontdoor|
|FrontdoorWebApplicationFirewallLog|Log del Web Application Firewall di Frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|
|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|
|NetworkSecurityGroupFlowEvent|Evento del flusso di regole del gruppo di sicurezza di rete|
|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DDoSMitigationFlowLogs|Flusso di log di decisioni di mitigazione DDoS|
|DDoSMitigationReports|Report soluzioni di prevenzione DDoS|
|DDoSProtectionNotifications|Notifiche di protezione DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|ProbeHealthStatusEvents|Evento dei risultati di integrità dei probe di Traffic Manager|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|GatewayDiagnosticLog|Log di diagnostica del gateway|
|IKEDiagnosticLog|Log di diagnostica IKE|
|P2SDiagnosticLog|Log di diagnostica P2S|
|RouteDiagnosticLog|Log di diagnostica della route|
|TunnelDiagnosticLog|Log di diagnostica del tunnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|VMProtectionAlerts|Avvisi di protezione VM|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Motore|Motore|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AddonAzureBackupAlerts|Dati di avviso di backup di Azure addon|
|AddonAzureBackupJobs|Dati del processo di backup di Azure addon|
|AddonAzureBackupPolicy|Dati dei criteri di backup di Azure addon|
|AddonAzureBackupProtectedInstance|Dati dell'istanza protetta del backup di Azure addon|
|AddonAzureBackupStorage|Dati di archiviazione di backup di Azure addon|
|AzureBackupReport|Dati dei report di Backup di Azure|
|AzureSiteRecoveryEvents|Eventi di Azure Site Recovery|
|AzureSiteRecoveryJobs|Processi di Azure Site Recovery|
|AzureSiteRecoveryProtectedDiskDataChurn|Varianza dei dati del disco protetti di Azure Site Recovery|
|AzureSiteRecoveryRecoveryPoints|Punti di ripristino di Azure Site Recovery|
|AzureSiteRecoveryReplicatedItems|Elementi replicati di Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Velocità di caricamento dei dati di replica di Azure Site Recovery|
|AzureSiteRecoveryReplicationStats|Statistiche di replica di Azure Site Recovery|
|CoreAzureBackup|Dati di backup di Azure di base|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|HybridConnectionsEvent|Eventi HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|OperationLogs|Log delle operazioni|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|OperationalLogs|Log operativi|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AllLogs|Log del servizio Azure SignalR.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DevOpsOperationsAudit|Log di controllo delle operazioni di DevOps|
|ResourceUsageStats|Statistiche di utilizzo delle risorse|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Errors|Errors|
|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|
|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AutomaticTuning|Ottimizzazione automatica|
|Blocchi|Blocchi|
|DatabaseWaitStatistics|Statistiche relative alle attese del database|
|Deadlock|Deadlock|
|DevOpsOperationsAudit|Log di controllo delle operazioni di DevOps|
|DmsWorkers|Ruoli di lavoro del servizio di Migrazione del database|
|Errors|Errors|
|ExecRequests|Richieste di esecuzione|
|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|
|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|
|RequestSteps|Procedura per la richiesta|
|SQLInsights|SQL Insights|
|SqlRequests|Richieste SQL|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|
|Timeout|Timeout|
|In attesa|In attesa|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

Costo: a pagamento, come descritto nella sezione log della piattaforma della [pagina dei prezzi di monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/) 

|Category |Nome visualizzato della categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

Costo: a pagamento, come descritto nella sezione log della piattaforma della [pagina dei prezzi di monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/) 

|Category |Nome visualizzato della categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

Costo: a pagamento, come descritto nella sezione log della piattaforma della [pagina dei prezzi di monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Category |Nome visualizzato della categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

Costo: a pagamento, come descritto nella sezione log della piattaforma della [pagina dei prezzi di monitoraggio di Azure.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Category |Nome visualizzato della categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|Creazione|Creazione|
|Esecuzione|Esecuzione|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BuiltinSqlReqsEnded|Richieste pool SQL predefinite terminate|
|GatewayApiRequests|Richieste API del gateway sinapsi|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|
|SynapseRbacOperations|Operazioni di sinapsi RBAC|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. sinapsi/Workspaces/bigDataPools

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|BigDataPoolAppsEnded|Applicazioni pool di Big Data terminate|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. sinapsi/Workspaces/sqlpools

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|DmsWorkers|Ruoli di lavoro del servizio di Migrazione del database|
|ExecRequests|Richieste di esecuzione|
|RequestSteps|Procedura per la richiesta|
|SqlRequests|Richieste SQL|
|SQLSecurityAuditEvents|Evento di controllo della sicurezza SQL|
|In attesa|In attesa|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

Costo: gratuito 

|Category |Nome visualizzato della categoria|
|---|---|
|AppServiceEnvironmentPlatformLogs|Log della piattaforma ambiente del servizio app|


## <a name="microsoftwebsites"></a>microsoft.web/sites

Costo: gratuito 


|Category |Nome visualizzato della categoria|
|---|---|
|AppServiceAppLogs|Log applicazioni del servizio app|
|AppServiceAuditLogs|Accedi ai log di controllo|
|AppServiceConsoleLogs|Log della console del servizio app|
|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|
|AppServiceHTTPLogs|Log HTTP|
|FunctionAppLogs|Log dell'applicazione per le funzioni|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

Costo: gratuito 


|Category |Nome visualizzato della categoria|
|---|---|
|AppServiceAppLogs|Log applicazioni del servizio app|
|AppServiceAuditLogs|Accedi ai log di controllo|
|AppServiceConsoleLogs|Log della console del servizio app|
|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|
|AppServiceHTTPLogs|Log HTTP|
|FunctionAppLogs|Log dell'applicazione per le funzioni|


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log delle risorse](./platform-logs-overview.md)
* [Trasmettere i log delle risorse delle risorse a **Hub eventi**](./resource-logs.md#send-to-azure-event-hubs)
* [Modificare le impostazioni di diagnostica del log delle risorse usando l'API REST di monitoraggio di Azure](/rest/api/monitor/diagnosticsettings)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

