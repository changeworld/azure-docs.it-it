---
title: Supporto per lo trasferimento di risorse di Azure tra aree
description: Elenca i tipi di risorse di Azure che possono essere spostati tra le aree di Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094016"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Supporto per lo trasferimento di risorse di Azure tra aree

Questo articolo conferma se un tipo di risorsa di Azure è supportato per il passaggio a un'altra area di Azure. 

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domainservices | No | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | diagnosticsettings | No |
> | diagnosticsettingscategories | No |
> | privatelinkforazuread | No |
> | tenants |  No |

## <a name="microsoftaddons"></a>Microsoft. Componenti aggiuntivi

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | supportproviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | aadsupportcases | No |
> | addsservices | No | 
> | agents | No | 
> | anonymousapiusers | No |
> | configurazione | No | 
> | log | No | 
> | reports | No | 
> | servicehealthmetrics | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | configurazioni | No | 
> | generaterecommendations | No |
> | metadata | No |
> | raccomandazioni di film | No |
> | suppressions | No | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | actionrules | No | 
> | alerts | No | 
> | alertslist | No | 
> | alertsmetadata | No | 
> | alertssummary | No | 
> | alertssummarylist | No | 
> | smartdetectoralertrules | No | 
> | smartgroups | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | reportfeedback | No |
> | service |  Sì (utilizzo del modello) <br/><br/> [Spostare gestione API tra le aree](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | configurationstores | No | 
> | configurationstores/eventgridfilters | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | spring | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | apiapps | Sì (utilizzo del modello)<br/><br/> [Spostare un'app del servizio app in un'altra area](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | gateways | No | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | attestationproviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | classicadministrators | No | 
> | dataaliases | No | 
> | denyassignments | No | 
> | elevateaccess | No | 
> | findorphanroleassignments | No | 
> | locks | No | 
> | autorizzazioni | No | 
> | policyassignments | No | 
> | policydefinitions | No | 
> | policysetdefinitions | No | 
> | privatelinkassociations | No | 
> | resourcemanagementprivatelinks | No | 
> | roleassignments | No | 
> | roleassignmentsusagemetrics | No | 
> | roledefinitions | No | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | automationaccounts | Sì (utilizzo del modello) <br/><br/> [Uso della replica geografica](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | No | 
> | automationaccounts/runbooks | No | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | Subscription |
> | ------------- | ----------- | 
> | privateclouds | No | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | b2cdirectories | No | 
> | b2ctenants | No | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datacontrollers | No | 
> | hybriddatamanagers | No | 
> | postgresinstances | No | 
> | sqlinstances | No | 
> | sqlmanagedinstances | No |
> | sqlserverinstances | No | 
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | cloudmanifestfiles | No |
> | registrations | No | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | clusters | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | batchaccounts |  Gli account batch non possono essere spostati direttamente da un'area a un'altra, ma è possibile usare un modello per esportare un modello, modificarlo e distribuire il modello nella nuova area. <br/><br/> Informazioni sullo [trasferimento di un account batch tra le aree](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | billingaccounts | No | 
> | billingperiods | No | 
> | billingpermissions | No | 
> | billingproperty | No | 
> | billingroleassignments | No | 
> | billingroledefinitions | No | 
> | departments | No | 
> | enrollmentaccounts | No | 
> | invoices | No | 
> | transfers | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> La rete blockchain non può contenere nodi in aree diverse. 
> | cordamembers | No |
> | watchers | No | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | tokenservices | No |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | blueprintassignments | No | 
> | blueprints | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | redis | No | 
> | redisenterprise | No | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | appliedreservations | No | 
> | calculateexchange | No | 
> | calculateprice | No | 
> | calculatepurchaseprice | No | 
> | catalogs | No | 
> | commercialreservationorders | No | 
> | exchange | No |
> | reservationorders | No | 
> | reservations | No | 
> | resources | No | 
> | validatereservationorder | No | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | edgenodes | No
> | Profili | No | 
> | profiles/endpoints | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | certificateorders | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | capabilities | No | 
> | domainnames | Sì | No |
> | quotas | No | 
> | resourcetypes | No |
> | validatesubscriptionmoveavailability | No | 
> | virtualmachines | No 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | capabilities | No | 
> | expressroutecrossconnections | No | 
> | expressroutecrossconnections/peerings | No | 
> | gatewaysupporteddevices | No | 
> | networksecuritygroups | No |
> | quotas | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | disks | No | 
> | images | No | 
> | osimages | No | 
> | osplatformimages | No | 
> | publicimages | No | 
> | quotas | No | 
> | storageaccounts | Sì |  
> | vmimages | No |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | Operazioni | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 
> | Ricerca cognitiva | Supportato con i passaggi manuali.<br/><br/> Informazioni su [come trasferire il servizio ricerca cognitiva di Azure in un'altra area](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | ratecard | No | 
> | usageaggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | availabilitysets | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare i set di disponibilità. | 
> | diskaccesses | No |
> | diskencryptionsets | No | 
> | disks | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le macchine virtuali di Azure e i dischi correlati. | 
> | galleries | No | 
> | galleries/images | No | 
> | galleries/images/versions | No | 
> | hostgroups | No | 
> | hostgroups/hosts | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/versions | No | 
> | snapshots | No | 
> | sshpublickeys | No |
> | virtualmachines | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le macchine virtuali di Azure. | 
> | virtualmachines/extensions | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | aggregatedcost | No | 
> | balances | No | 
> | budgets | No | 
> | charges | No | 
> | costtags | No | 
> | credits | No | 
> | eventi | No | 
> | forecasts | No | 
> | lots | No | 
> | marketplaces | No | 
> | pricesheets | No | 
> | products | No | 
> | reservationdetails | No | 
> | reservationrecommendationdetails | No | 
> | reservationrecommendations | No | 
> | reservationsummaries | No | 
> | reservationtransactions | No | 
> | tags | No | 
> | tenants | No | 
> | terms | No | 
> | usagedetails | No | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | containergroups | No | 
> | serviceassociationlinks | No |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | registries | No |  
> | registries/agentpools | No | 
> | registries/buildtasks | No |  
> | registries/replications | No | 
> | registries/tasks | No |  
> | registries/webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | containerservices | No |
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | alerts | No | 
> | billingaccounts | No | 
> | budgets | No | 
> | cloudconnectors | No | 
> | dell'account di integrazione | No | 
> | departments | No | 
> | dimensions | No | 
> | enrollmentaccounts | No | 
> | exports | No | 
> | externalbillingaccounts | No | 
> | previsione | No | 
> | query | No | 
> | register | No | 
> | reportconfigs | No | 
> | reports | No | 
> | Scheda Impostazioni | No | 
> | showbackrules | No | 
> | Viste | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | requests | No | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | associations | No |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | jobs | No | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | availableskus | No |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | aree di lavoro | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | catalogs | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | packages | No | 
> | plans | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datafactories | No | 
> | factories | No |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No | 
> | services/projects | No | 
> | slot | No | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | ---------- |
> | backupvaults | No | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../postgresql/howto-move-regions-portal.md)<br/><br/> Se viene eseguito il provisioning del servizio con archiviazione di backup con ridondanza geografica, è possibile usare il ripristino geografico per eseguire il ripristino in altre aree. [Altre informazioni](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | servergroups | No | 
> | servers | È possibile utilizzare una replica di lettura tra aree per spostare un server esistente. [Altre informazioni](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | rollouts | No |  
> | servicetopologies | No | 
> | servicetopologies/services | No |  
> | servicetopologies/services/serviceunits | No | 
> | steps | No | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | applicationgroups | No | 
> | aree di lavoro | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | elasticpools | No. La risorsa non è esposta.
> | elasticpools/iothubtenants | No. La risorsa non è esposta.
> | iothubs | Sì. [Scopri di più](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | controllers | No | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | controllers | No | 
> | Cluster AKS | No<br/><br/> [Altre](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) informazioni sul passaggio a un'altra area.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | labcenters | No | 
> | labs | No | 
> | labs/environments | No |  
> | labs/servicerunners | No | 
> | labs/virtualmachines | No |  
> | schedules | No |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Sì, ricreando le risorse in una nuova area. [Scopri di più](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | databaseaccounts | No | 
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domains | No | 
> | generatessorequest | No | 
> | topleveldomains | No | 
> | validatedomainregistrationinformation | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | domains | No | 
> | eventsubscriptions | No |
> | extensiontopics | No | 
> | partnernamespaces | No | 
> | partnerregistrations | No | 
> | partnertopics | No | 
> | systemtopics | No | 
> | topics | No | 
> | topictypes | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No |  
> | spazi dei nomi | Sì (con modello)<br/><br/> [Spostare uno spazio dei nomi dell'hub eventi in un'altra area](../../event-hubs/move-across-regions.md) | 
> | sku | No |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | experimentworkspaces | No | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | spazi dei nomi | No | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | featureproviders | No | 
> | funzionalità | No | 
> | provider | No | 
> | subscriptionfeatureregistrations | No | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | automanagedaccounts | No | 
> | automanagedvmconfigurationprofiles | No | 
> | guestconfigurationassignments | No | 
> | software | No | 
> | softwareupdateprofile | No | 
> | softwareupdates | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | dedicatedhsms | No | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | machines | No | 
> | machines/extensions | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | devices | No | 
> | vnfs | No | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | components | No | 
> | networkscopes | No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | jobs |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No. [Altre informazioni](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | baseline | No |
> | components |  No |  
> | datacollectionrules | No | 
> | diagnosticsettings | No | 
> | diagnosticsettingscategories | No | 
> | eventcategories | No | 
> | eventtypes | No | 
> | extendeddiagnosticsettings | No | |
> | guestdiagnosticsettings | No | 
> | listmigrationdate | No | 
> | logdefinitions | No | 
> | logprofiles | No | 
> | log | No | No |
> | metricalerts | No | 
> | metricbaselines | No | 
> | metricbatch | No | 
> | metricdefinitions | No | 
> | metricnamespaces | No | 
> | Metriche | No | 
> | migratealertrules | No |
> | migratetonewpricingmodel | No | 
> | myworkbooks | No |
> | notificationgroups | No | 
> | privatelinkscopes | No |
> | rollbacktolegacypricingmodel | No |
> | scheduledqueryrules |  No | 
> | Topologia | No |
> | transazioni | No |
> | vminsightsonboardingstatuses | No |
> | webtests |  No | 
> | test Web/gettestresultfile | No |
> | workbooks |  No |  
> | workbooktemplates | No |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | apptemplates | No | 
> | iotapps | No | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> |  iothub |  Sì (clone Hub) <br/><br/> [Clonare un hub Internet delle cose in un'altra area](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area |
> | ------------- | ----------- | 
> | graph | No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | deletedvaults | No |
> | hsmpools | No | 
> | managedhsms | No |
> | insiemi di credenziali |  No | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | connectedclusters | No | 
> | registeredsubscriptions | No | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | No | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters |  No |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | labaccounts | No | 
> | users | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No, si tratta di un servizio globale.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | integrationserviceenvironments/managedapis | No |
> | isolatedenvironments | No | 
> | flussi di lavoro |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | webservices |  No | 
> | aree di lavoro |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 
> | teamaccounts | No | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | aree di lavoro | No | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | configurationassignments | Sì. [Scopri di più](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Sì. [Scopri di più](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | identità | No | 
> | userassignedidentities | No | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | managednetworks | No | 
> | managednetworks/managednetworkgroups | No |
> | managednetworks/managednetworkpeeringpolicies | No | 
> | notifica | No | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | No | 
> | registrationassignments | No |
> | registrationdefinitions | No | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | getentities | No | 
> | managementgroups | No | 
> | managementgroups/settings | No | 
> | resources | No | 
> | starttenantbackfill | No | 
> | tenantbackfillstatus | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account |  No, Azure Maps è un servizio geospaziale. 
> | accounts/privateatlases | No

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | offers | No | 
> | offertypes | No | 
> | privategalleryitems | No | 
> | privatestoreclient | No | 
> | privatestores | No | 
> | products | No | 
> | publishers | No | 
> | register | No | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | classicdevservices | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | agreements | No | 
> | offertypes | No | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | mediaservices |  No | 
> | mediaservices/liveevents |  No | 
> | mediaservices/streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | movecollections | No
> | projects | No | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | 
> | objectunderstandingaccounts | No | 
> | remoterenderingaccounts | No | 
> | spatialanchorsaccounts | No | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts/capacitypools | No | 
> | netappaccounts/capacitypools/volumes | No | 
> | netappaccounts/capacitypools/volumes/mounttargets | No | 
> | netappaccounts/capacitypools/volumes/snapshots | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | applicationgateways | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | bgpservicecommunities | No |
> | connections |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutegateways | No | 
> | expressrouteserviceproviders | No | 
> | firewallpolicies | No |
> | frontdoors | No | 
> | ipallocations | No |
> | ipgroups | No |
> | loadbalancers | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare i bilanciamenti del carico interno ed esterno. |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkexperimentprofiles | No |
> | networkintentpolicies |  No | 
> | networkinterfaces | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le schede di rete. | 
> | networkprofiles | No | 
> | networksecuritygroups | Sì <br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare i gruppi di sicurezza di rete (NGSs). | 
> | networkwatchers |  No |  
> | networkwatchers/connectionmonitors |  No | 
> | networkwatchers/flowlogs |  No | 
> | networkwatchers/pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones/virtualnetworklinks | No |> | privatednszonesinternal | No |
> | privateendpointredirectmaps | No |
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Sì<br/><br/> Usare [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare gli indirizzi IP pubblici. |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | securitypartnerproviders | No |
> | serviceendpointpolicies |  No | 
> | trafficmanagergeographichierarchies | No | 
> | trafficmanagerprofiles |  No | 
> | trafficmanagerusermetricskeys | No |
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (rete WAN virtuale) | No | 
> | vpnsites (rete WAN virtuale) | No | 
> | vpnsites (rete WAN virtuale) | No |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 
> | namespaces/notificationhubs |  No |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | osnamespaces | No | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | hypervsites | No | 
> | importsites | No | 
> | serversites | No | 
> | vmwaresites | No | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No | 
> | deletedworkspaces | No | 
> | linktargets | No | 
> | storageinsightconfigs | No |
> | aree di lavoro | No |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations |  No | 
> | solutions | No |
> | Viste |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | legacypeerings | No | 
> | peerasns | No | 
> | peeringlocations | No | 
> | peerings | No | 
> | peeringservicecountries | No | 
> | peeringservicelocations | No | 
> | peeringserviceproviders | No | 
> | peeringservices | No | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | policyevents | No | 
> | policystates | No | 
> | policytrackedresources | No | 
> | remediations | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | No | 
> | usersettings | No | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | capacities |  No | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account | No | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | availableaccounts | No | 
> | providerregistrations | No | 
> | rollouts | No | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | aree di lavoro | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | replicationeligibilityresults | No |
> | insiemi di credenziali | No.<br/><br/> Lo stato di un insieme di credenziali di servizi di ripristino per backup di Azure in aree di Azure non è supportato<br/><br/> Negli insiemi di credenziali dei servizi di ripristino per Azure Site Recovery è possibile [disabilitare e ricreare l'](../../site-recovery/move-vaults-across-regions.md) insieme di credenziali nell'area di destinazione. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | openshiftclusters | No | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | query |  No |  
> | resourcechangedetails | No | 
> | resourcechanges | No | 
> | resources | No | 
> | resourceshistory | No | 
> | subscriptionsstatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | childresources | No | 
> | emergingissues | No | 
> | eventi | No | 
> | metadata | No | 
> | Notifiche | No | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area |
> | ------------- | ----------- |
> | deploymentScripts |  Sì<br/><br/>[Spostare le risorse Microsoft. resources in una nuova area](microsoft-resources-move-regions.md) |
> | templateSpecs |  Sì<br/><br/>[Spostare le risorse Microsoft. resources in una nuova area](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web |  No | 
> | saasresources | No | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | resourcehealthmetadata | No |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | No | 
> | advancedthreatprotectionsettings | No | 
> | alerts | No | 
> | allowedconnections | No | 
> | applicationwhitelistings | No | 
> | assessmentmetadata | No | 
> | assessments | No | 
> | autodismissalertsrules | No | 
> | automations | No | 
> | autoprovisioningsettings | No |
> | complianceresults | No | 
> | compliances | No | 
> | datacollectionagents | No | 
> | devicesecuritygroups | No | 
> | discoveredsecuritysolutions | No | 
> | externalsecuritysolutions | No | 
> | informationprotectionpolicies | No | 
> | iotsecuritysolutions | No | 
> | iotsecuritysolutions/analyticsmodels | No | 
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | No | 
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | No | 
> | jitnetworkaccesspolicies | No | 
> | criteri | No | 
> | pricings | No | 
> | regulatorycompliancestandards | No | 
> | regulatorycompliancestandards/regulatorycompliancecontrols | No | 
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | No | 
> | securitycontacts | No | 
> | securitysolutions | No | 
> | securitysolutionsreferencedata | No | 
> | securitystatuses | No | 
> | securitystatusessummaries | No | 
> | servervulnerabilityassessments | No | 
> | Scheda Impostazioni | No | 
> | subassessments | No |
> | attività | No | 
> | topologies | No | 
> | workspacesettings | No | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | aggregations | No | 
> | alertrules | No | 
> | alertruletemplates | No | 
> | automationrules | No |
> | cases | No | 
> | dataconnectors | No | 
> | entities | No | 
> | entityqueries | No |
> | incidents | No | 
> | officeconsents | No | 
> | Scheda Impostazioni | No | 
> | threatintelligence | No | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | consoleservices | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | gateways | No | 
> | nodes | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | spazi dei nomi |  No | 
> | premiummessagingregions | No | 
> | sku | No | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web | No | 
> | clusters |  No |  
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | managedclusters | No |
> | networks | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | scala Web |  No | 
> | containergroups | No | 
> | gateways |  No | 
> | networks |  No | 
> | chiavi private |  No | 
> | volumes |  No |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | rollouts | No | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | hybridusebenefits | No | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | appliances | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | instancepools | No | 
> | locations | No |
> | managedinstances | Sì <br/><br/> [Altre](../../azure-sql/database/move-resources-across-regions.md) informazioni sullo trasferimento di istanze gestite tra le aree. | 
> | managedinstances/databases | Sì | 
> | servers | Sì | 
> | servers/databases | Sì <br/><br/> [Altre](../../azure-sql/database/move-resources-across-regions.md) informazioni sullo stato di trasferimento dei database tra le aree.<br/><br/> [Altre](../../resource-mover/tutorial-move-region-sql.md) informazioni sull'uso di Azure Resource Mover per spostare i database SQL di Azure.  | 
> | servers/elasticpools | Sì <br/><br/> [Altre](../../azure-sql/database/move-resources-across-regions.md) informazioni sullo stato di trasferimento dei pool elastici tra le aree.<br/><br/> [Altre](../../resource-mover/tutorial-move-region-sql.md) informazioni sull'uso di Azure Resource Mover per spostare i pool elastici SQL di Azure.  | 
> | virtualclusters | Sì | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storageaccounts | Sì<br/><br/> [Spostare un account di archiviazione di Azure in un'altra area](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | caches | No | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | managers | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | clusters | No |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | environments | No | 
> | instances | No | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | subscriptions | No | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | services | No | 
> | supporttickets | No | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | aree di lavoro | No | 
> | workspaces/bigdatapools | No | 
> | workspaces/sqlpools | No | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | environments |  No | 
> | environments/eventsources |  No |  
> | environments/referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | stores | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | account |  No | 
> | account/extension |  No | 
> | account/project |  No | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | arczones | No | 
> | resourcepools | No | 
> | vcenters | No | 
> | virtualmachines | No | 
> | virtualmachinetemplates | No | 
> | virtualnetworks | No | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | devices | No | 
> | vnfs | No | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | account | No | 
> | plans | No | 
> | registeredsubscriptions | No |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | availablestacks | No | 
> | billingmeters | No | 
> | certificates | No | 
> | connectiongateways |  No |  
> | connections |  No |  
> | customapis |  No | 
> | deletedsites | No | 
> | deploymentlocations | No | 
> | georegions | No | 
> | hostingenvironments | No | 
> | kubeenvironments | No | 
> | publishingusers | No |
> | raccomandazioni di film | No | 
> | resourcehealthmetadata | No | 
> | runtimes | No | 
> | serverfarms | No |  
> | serverfarms/eventgridfilters | N
> | siti |  No | 
> | sites/premieraddons |  No |  
> | sites/slots |  No |  
> | sourcecontrols | No |
> | staticsites | No | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | multipleactivationkeys | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | carichi di lavoro | No | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Spostamento area | 
> | ------------- | ----------- | 
> | components | No |
> | componentssummary | No | 
> | monitorinstances | No | 
> | monitorinstancessummary | No | 
> | monitors | No | 
## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

[Altre](../../resource-mover/overview.md) informazioni sul servizio Mover di risorse.

