---
title: Servizi e categorie supportati nei log delle risorse di monitoraggio di Azure
description: Informazioni di riferimento su monitoraggio di Azure comprendono i servizi e lo schema di eventi supportati per i log delle risorse di Azure.
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: a4ab4a2e425b752198223da5efd1b07466ab83d1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166939"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorie supportate per i log delle risorse di Azure

> [!NOTE]
> I log delle risorse erano noti in precedenza come log di diagnostica. Il nome è stato modificato nel 2019 ottobre perché i tipi di log raccolti da monitoraggio di Azure sono stati spostati in modo da includere solo la risorsa di Azure.

I [log delle risorse di monitoraggio di Azure](../essentials/platform-logs-overview.md) sono log emessi da servizi di Azure che descrivono il funzionamento di tali servizi o risorse. Tutti i log delle risorse disponibili tramite monitoraggio di Azure condividono uno schema di primo livello comune, con la flessibilità che consente a ogni servizio di emettere proprietà univoche per gli eventi.

Una combinazione del tipo di risorsa (disponibile nella proprietà `resourceId`) e del `category` identifica in modo univoco uno schema. È disponibile uno schema comune per tutti i log delle risorse con campi specifici del servizio e quindi aggiunti per diverse categorie di log. Per altre informazioni, vedere [schema comune e specifico del servizio per i log delle risorse di Azure]()


## <a name="costs"></a>Costi

L'invio e l'archiviazione di dati in Log Analytics, archiviazione di Azure e/o hub eventi sono i costi associati. Il licenziatario potrà pagare il costo per ottenere i dati in queste posizioni e per mantenerlo.  I log delle risorse sono un tipo di dati che è possibile inviare a questi percorsi. 

Sono previsti costi aggiuntivi per esportare alcune categorie di log di risorse in questi percorsi. I registri con i costi di esportazione sono elencati nella tabella seguente. Per altre informazioni sui prezzi, vedere la sezione log della piattaforma nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Categorie di log supportate per tipo di risorsa

Di seguito è riportato un elenco dei tipi di log disponibili per ogni tipo di risorsa. 

Alcune categorie possono essere supportate solo per tipi specifici di risorse. Se si ritiene che la risorsa non sia presente, vedere la documentazione specifica della risorsa. Ad esempio, le categorie Microsoft. SQL/Servers/databases non sono disponibili per tutti i tipi di database. Per ulteriori informazioni, vedere [informazioni sulla registrazione diagnostica del database SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Se si ritiene che manchi qualcosa, è possibile aprire un commento di GitHub nella parte inferiore di questo articolo.

## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/DomainServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Signin|Signin|Sì|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Motore|Motore|No|
|Servizio|Servizio|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|GatewayLogs|Log correlati ad ApiManagement Gateway|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|HttpRequest|Richieste HTTP|Sì|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ApplicationConsole|Console applicazione|No|
|SystemLogs|Log di sistema|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuditEvent|Categoria di log del messaggio AuditEvent.|No|
|ERR|Categoria log messaggi di errore.|No|
|INF|Categoria di log messaggi informativi.|No|
|Avv|Categoria di log del messaggio di avviso.|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DscNodeStatus|Stato del nodo Dsc|No|
|JobLogs|Log del processo|No|
|JobStreams|Flussi del processo|No|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft. AutonomousDevelopmentPlatform/accounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Audit|Sì|
|Operativo|Operativo|Sì|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ServiceLog|Log del servizio|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BlockchainApplication|Applicazione blockchain|No|
|FabricOrderer|Ordinatore infrastruttura|No|
|FabricPeer|Peer infrastruttura|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BlockchainApplication|Applicazione blockchain|No|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BotRequest|Richieste dai canali al bot|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|WebApplicationFirewallLogs|Log del firewall di applicazione Web|No|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AzureCdnAccessLog|Log di accesso della rete CDN di Azure|No|
|FrontDoorAccessLog|Log di accesso FrontDoor|Sì|
|FrontDoorHealthProbeLog|Log del probe di integrità FrontDoor|Sì|
|FrontDoorWebApplicationFirewallLog|Log WebApplicationFirewall FrontDoor|Sì|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|CoreAnalytics|Ottiene le metriche dell'endpoint, ad esempio la larghezza di banda, i dati in uscita e così via|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Evento del flusso di regole del gruppo di sicurezza di rete|Evento del flusso di regole del gruppo di sicurezza di rete|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Log di controllo|No|
|RequestResponse|Log richieste e risposte|No|
|Trace|Registri di traccia|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuthOperational|Log di autenticazione operativa|Sì|
|ChatOperational|Log di chat operativi|No|
|SMSOperational|Log SMS operativi|No|
|Utilizzo|Record di utilizzo|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ContainerRegistryLoginEvents|Eventi di accesso|No|
|ContainerRegistryRepositoryEvents|Log RepositoryEvent|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|cluster-autoscaler|Ridimensionamento automatico del cluster Kubernetes|No|
|guard|guard|No|
|kube-apiserver|Server API Kubernetes|No|
|Kube-audit|Controllo Kubernetes|No|
|Kube-audit-admin|Log di amministrazione di controllo di Kubernetes|No|
|kube-controller-manager|Strumento di gestione del controller Kubernetes|No|
|kube-scheduler|Utilità di pianificazione Kubernetes|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuditLogs|Log di controllo per le chiamate MiniRP|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/istanze

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Eventi di controllo|No|
|Operativo|Eventi operativi|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. databricks/area di lavoro

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|account|Account databricks|No|
|clusters|Cluster databricks|No|
|dBFS|File system di Databricks|No|
|instancePools|Pool di istanze|No|
|jobs|Processi di databricks|No|
|notebook|Notebook di Databricks|No|
|chiavi private|Segreti di databricks|No|
|sqlPermissions|Sqlpermissions di databricks|No|
|ssh|SSH di databricks|No|
|area di lavoro|Area di lavoro databricks|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. datacollaboration/Workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|CollaborationAudit|Controllo collaborazione|Sì|
|Dataasset|Asset di dati|No|
|Pipelines|Pipelines|No|
|Proposte|Proposte|No|
|Script|Script|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ActivityRuns|Log delle esecuzioni di attività pipeline|No|
|PipelineRuns|Log delle esecuzioni di pipeline|No|
|SandboxActivityRuns|Log delle esecuzioni di attività sandbox|Sì|
|SandboxPipelineRuns|Log delle esecuzioni della pipeline sandbox|Sì|
|SSISIntegrationRuntimeLogs|Log di runtime di integrazione SSIS|No|
|SSISPackageEventMessageContext|Contesto del messaggio di evento del pacchetto SSIS|No|
|SSISPackageEventMessages|Messaggi di evento del pacchetto SSIS|No|
|SSISPackageExecutableStatistics|Statistiche eseguibili pacchetto SSIS|No|
|SSISPackageExecutionComponentPhases|Fasi del componente di esecuzione del pacchetto SSIS|No|
|SSISPackageExecutionDataStatistics|Statistiche sui dati del pacchetto SSIS exeution|No|
|TriggerRuns|Log delle esecuzioni trigger|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Log di controllo|No|
|Requests|Log delle richieste|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Log di controllo|No|
|Requests|Log delle richieste|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ReceivedShareSnapshots|Snapshot di condivisione ricevuti|No|
|SentShareSnapshots|Snapshot di condivisione inviati|No|
|Condivisioni|Condivisioni|No|
|ShareSubscriptions|Condividi sottoscrizioni|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|MySqlAuditLogs|Log di controllo di MariaDB|No|
|MySqlSlowLogs|Log del server MariaDB|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|MySqlAuditLogs|Log di controllo di MySQL|No|
|MySqlSlowLogs|Log lenti MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|MySqlAuditLogs|Log di controllo di MySQL|No|
|MySqlSlowLogs|Log server MySQL|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|No|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|Sì|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|No|
|QueryStoreRuntimeStatistics|Statistiche di runtime Query Store PostgreSQL|No|
|QueryStoreWaitStatistics|Statistiche attesa Query Store PostgreSQL|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|PostgreSQLLogs|Log del server PostgreSQL|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Errore|Errore|No|
|Gestione|Gestione|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|Checkpoint|Checkpoint|No|
|Connessioni|Connessioni|No|
|Errore|Errore|No|
|HostRegistration|HostRegistration|No|
|Gestione|Gestione|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/Workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Errore|Errore|No|
|Feed|Feed|No|
|Gestione|Gestione|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|C2DCommands|Comandi da cloud a dispositivo|No|
|C2DTwinOperations|Operazioni da cloud a dispositivi gemelli|No|
|Configurazioni|Configurazioni|No|
|Connessioni|Connessioni|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operazioni relative alle identità dei dispositivi|No|
|DeviceStreams|Flussi del dispositivo (anteprima)|No|
|DeviceTelemetry|Telemetria dei dispositivi|No|
|DirectMethods|Metodi diretti|No|
|DistributedTracing|Traccia distribuita (anteprima)|No|
|FileUploadOperations|Operazioni di caricamento file|No|
|JobsOperations|Operazioni dei processi|No|
|Route|Route|No|
|TwinQueries|Query dei dispositivi gemelli|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|C2DCommands|Comandi da cloud a dispositivo|No|
|C2DTwinOperations|Operazioni da cloud a dispositivi gemelli|No|
|Configurazioni|Configurazioni|No|
|Connessioni|Connessioni|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operazioni relative alle identità dei dispositivi|No|
|DeviceStreams|Flussi del dispositivo (anteprima)|No|
|DeviceTelemetry|Telemetria dei dispositivi|No|
|DirectMethods|Metodi diretti|No|
|DistributedTracing|Traccia distribuita (anteprima)|No|
|FileUploadOperations|Operazioni di caricamento file|No|
|JobsOperations|Operazioni dei processi|No|
|Route|Route|No|
|TwinQueries|Query dei dispositivi gemelli|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeviceOperations|Operazioni del dispositivo|No|
|ServiceOperations|Operazioni di servizio|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeliveryFailures|Log degli errori di recapito|No|
|PublishFailures|Pubblica log degli errori|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeliveryFailures|Log degli errori di recapito|No|
|PublishFailures|Pubblica log degli errori|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeliveryFailures|Log degli errori di recapito|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeliveryFailures|Log degli errori di recapito|No|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DeliveryFailures|Log degli errori di recapito|No|
|PublishFailures|Pubblica log degli errori|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ArchiveLogs|Log di archiviazione|No|
|AutoScaleLogs|Log di scalabilità automatica|No|
|CustomerManagedKeyUserLogs|Log delle chiavi gestite dal cliente|No|
|EventHubVNetConnectionEvent|VNet/IP filtraggio dei log di connessione|No|
|KafkaCoordinatorLogs|Log coordinatore Kafka|No|
|KafkaUserErrorLogs|Log degli errori utente Kafka|No|
|OperationalLogs|Log operativi|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. Experimentation/experimentWorkspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Richiesta|Richiesta|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuditLogs|Log di controllo|No|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AutoscaleEvaluations|Valutazioni sulla scalabilità automatica|No|
|AutoscaleScaleActions|Azioni di ridimensionamento per la scalabilità automatica|No|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AppAvailabilityResults|Risultati della disponibilità|No|
|AppBrowserTimings|Intervalli del browser|No|
|AppDependencies|Dependencies|No|
|AppEvents|Eventi|No|
|AppExceptions|Eccezioni|No|
|AppMetrics|Metriche|No|
|AppPageViews|Visualizzazioni pagina|No|
|AppPerformanceCounters|Contatori delle prestazioni|No|
|AppRequests|Requests|No|
|AppSystemEvents|Eventi di sistema|No|
|AppTraces|Tracce|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. managedhsms

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuditEvent|Evento di controllo|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AuditEvent|Log di controllo|No|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Comando|Comando|No|
|FailedIngestion|Operazioni di inserimento non riuscite|No|
|IngestionBatching|Inserimento dati in batch|No|
|Query|Query|No|
|SucceededIngestion|Operazioni di inserimento riuscite|No|
|TableDetails|Dettagli tabella|No|
|TableUsageStatistics|Statistiche sull'utilizzo delle tabelle|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|IntegrationAccountTrackingEvents|Eventi di rilevamento degli account di integrazione|No|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|WorkflowRuntime|Eventi di diagnostica del runtime del flusso di lavoro|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|KeyDeliveryRequests|Richieste di distribuzione delle chiavi|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ApplicationGatewayAccessLog|Log di accesso del gateway applicazione|No|
|ApplicationGatewayFirewallLog|Log del firewall del gateway applicazione|No|
|ApplicationGatewayPerformanceLog|Log delle prestazioni del gateway applicazione|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AzureFirewallApplicationRule|Regola di applicazione di Firewall di Azure|No|
|AzureFirewallDnsProxy|Proxy DNS del firewall di Azure|No|
|AzureFirewallNetworkRule|Regola di rete di Firewall di Azure|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BastionAuditLogs|Log di controllo Bastion|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|PeeringRouteLog|Log delle tabelle di routing di peering|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|FrontdoorAccessLog|Log di accesso a Frontdoor|No|
|FrontdoorWebApplicationFirewallLog|Log del Web Application Firewall di Frontdoor|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|LoadBalancerAlertEvent|Eventi di avviso del servizio di bilanciamento del carico|No|
|LoadBalancerProbeHealthStatus|Stato di integrità dei probe del servizio di bilanciamento del carico|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|NetworkSecurityGroupEvent|Event del gruppo di sicurezza di rete|No|
|NetworkSecurityGroupFlowEvent|Evento del flusso di regole del gruppo di sicurezza di rete|No|
|NetworkSecurityGroupRuleCounter|Contatore di regole del gruppo di sicurezza di rete|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|GatewayDiagnosticLog|Log di diagnostica del gateway|No|
|IKEDiagnosticLog|Log di diagnostica IKE|No|
|P2SDiagnosticLog|Log di diagnostica P2S|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DDoSMitigationFlowLogs|Flusso di log di decisioni di mitigazione DDoS|No|
|DDoSMitigationReports|Report soluzioni di prevenzione DDoS|No|
|DDoSProtectionNotifications|Notifiche di protezione DDoS|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ProbeHealthStatusEvents|Evento dei risultati di integrità dei probe di Traffic Manager|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|GatewayDiagnosticLog|Log di diagnostica del gateway|No|
|IKEDiagnosticLog|Log di diagnostica IKE|No|
|P2SDiagnosticLog|Log di diagnostica P2S|No|
|RouteDiagnosticLog|Log di diagnostica della route|No|
|TunnelDiagnosticLog|Log di diagnostica del tunnel|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|VMProtectionAlerts|Avvisi di protezione VM|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|GatewayDiagnosticLog|Log di diagnostica del gateway|No|
|IKEDiagnosticLog|Log di diagnostica IKE|No|
|RouteDiagnosticLog|Log di diagnostica della route|No|
|TunnelDiagnosticLog|Log di diagnostica del tunnel|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|OperationalLogs|Log operativi|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Audit|Log di controllo|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. Power bi/tenant

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Motore|Motore|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. Power bi/tenant/aree di lavoro

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Motore|Motore|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Motore|Motore|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. competenza/account

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AddonAzureBackupAlerts|Dati di avviso di backup di Azure addon|No|
|AddonAzureBackupJobs|Dati del processo di backup di Azure addon|No|
|AddonAzureBackupPolicy|Dati dei criteri di backup di Azure addon|No|
|AddonAzureBackupProtectedInstance|Dati dell'istanza protetta del backup di Azure addon|No|
|AddonAzureBackupStorage|Dati di archiviazione di backup di Azure addon|No|
|AzureBackupReport|Dati dei report di Backup di Azure|No|
|AzureSiteRecoveryEvents|Eventi di Azure Site Recovery|No|
|AzureSiteRecoveryJobs|Processi di Azure Site Recovery|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Varianza dei dati del disco protetti di Azure Site Recovery|No|
|AzureSiteRecoveryRecoveryPoints|Punti di ripristino di Azure Site Recovery|No|
|AzureSiteRecoveryReplicatedItems|Elementi replicati di Azure Site Recovery|No|
|AzureSiteRecoveryReplicationDataUploadRate|Velocità di caricamento dei dati di replica di Azure Site Recovery|No|
|AzureSiteRecoveryReplicationStats|Statistiche di replica di Azure Site Recovery|No|
|CoreAzureBackup|Dati di backup di Azure di base|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|HybridConnectionsEvent|Eventi HybridConnections|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|OperationLogs|Log delle operazioni|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|OperationalLogs|Log operativi|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AllLogs|Log del servizio Azure SignalR.|No|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AllLogs|Log del servizio PubSub Web di Azure.|Sì|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DevOpsOperationsAudit|Log di controllo delle operazioni di DevOps|No|
|ResourceUsageStats|Statistiche di utilizzo delle risorse|No|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Errors|Errors|No|
|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|No|
|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|No|
|SQLInsights|SQL Insights|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AutomaticTuning|Ottimizzazione automatica|No|
|Blocchi|Blocchi|No|
|DatabaseWaitStatistics|Statistiche relative alle attese del database|No|
|Deadlock|Deadlock|No|
|DevOpsOperationsAudit|Log di controllo delle operazioni di DevOps|No|
|DmsWorkers|Ruoli di lavoro del servizio di Migrazione del database|No|
|Errors|Errors|No|
|ExecRequests|Richieste di esecuzione|No|
|QueryStoreRuntimeStatistics|Statistiche di runtime di Query Store|No|
|QueryStoreWaitStatistics|Statistiche relative alle attese di Query Store|No|
|RequestSteps|Procedura per la richiesta|No|
|SQLInsights|SQL Insights|No|
|SqlRequests|Richieste SQL|No|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|No|
|Timeout|Timeout|No|
|In attesa|In attesa|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|StorageDelete|StorageDelete|Sì|
|StorageRead|StorageRead|Sì|
|StorageWrite|StorageWrite|Sì|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|StorageDelete|StorageDelete|Sì|
|StorageRead|StorageRead|Sì|
|StorageWrite|StorageWrite|Sì|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|StorageDelete|StorageDelete|Sì|
|StorageRead|StorageRead|Sì|
|StorageWrite|StorageWrite|Sì|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|StorageDelete|StorageDelete|Sì|
|StorageRead|StorageRead|Sì|
|StorageWrite|StorageWrite|Sì|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Creazione|Creazione|No|
|Esecuzione|Esecuzione|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BuiltinSqlReqsEnded|Richieste pool SQL predefinite terminate|No|
|GatewayApiRequests|Richieste API del gateway sinapsi|No|
|IntegrationActivityRuns|Esecuzioni attività di integrazione|Sì|
|IntegrationPipelineRuns|Esecuzioni pipeline di integrazione|Sì|
|IntegrationTriggerRuns|Esecuzioni del trigger di integrazione|Sì|
|SQLSecurityAuditEvents|Evento di controllo di sicurezza SQL|No|
|SynapseRbacOperations|Operazioni di sinapsi RBAC|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. sinapsi/Workspaces/bigDataPools

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|BigDataPoolAppsEnded|Applicazioni pool di Big Data terminate|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. sinapsi/Workspaces/sqlpools

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|DmsWorkers|Ruoli di lavoro del servizio di Migrazione del database|No|
|ExecRequests|Richieste di esecuzione|No|
|RequestSteps|Procedura per la richiesta|No|
|SqlRequests|Richieste SQL|No|
|SQLSecurityAuditEvents|Evento di controllo della sicurezza SQL|No|
|In attesa|In attesa|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Dati in ingresso|Dati in ingresso|No|
|Gestione|Gestione|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|Dati in ingresso|Dati in ingresso|No|
|Gestione|Gestione|No|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Log della piattaforma ambiente del servizio app|No|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Report log di controllo antivirus|No|
|AppServiceAppLogs|Log applicazioni del servizio app|No|
|AppServiceAuditLogs|Accedi ai log di controllo|No|
|AppServiceConsoleLogs|Log della console del servizio app|No|
|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|No|
|AppServiceHTTPLogs|Log HTTP|No|
|AppServiceIPSecAuditLogs|Log di controllo di IPSecurity|No|
|AppServicePlatformLogs|Log della piattaforma del servizio app|No|
|FunctionAppLogs|Log dell'applicazione per le funzioni|No|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Category|Nome visualizzato della categoria|Costi per l'esportazione|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Report log di controllo antivirus|No|
|AppServiceAppLogs|Log applicazioni del servizio app|No|
|AppServiceAuditLogs|Accedi ai log di controllo|No|
|AppServiceConsoleLogs|Log della console del servizio app|No|
|AppServiceFileAuditLogs|Log di controllo delle modifiche al contenuto del sito|No|
|AppServiceHTTPLogs|Log HTTP|No|
|AppServiceIPSecAuditLogs|Log di controllo di IPSecurity|No|
|AppServicePlatformLogs|Log della piattaforma del servizio app|No|
|FunctionAppLogs|Log dell'applicazione per le funzioni|No|


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log delle risorse](../essentials/platform-logs-overview.md)
* [Trasmettere i log delle risorse delle risorse a **Hub eventi**](./resource-logs.md#send-to-azure-event-hubs)
* [Modificare le impostazioni di diagnostica del log delle risorse usando l'API REST di monitoraggio di Azure](/rest/api/monitor/diagnosticsettings)
* [Analizzare i log di Archiviazione di Azure con Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

