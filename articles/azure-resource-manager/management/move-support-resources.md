---
title: Supporto per lo spostamento per tipo di risorsa
description: Elenca i tipi di risorse di Azure che possono essere spostati in un nuovo gruppo di risorse, sottoscrizione o area.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: c159b6e5f64f3052a6584034aa58b058b1426b16
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725567"
---
# <a name="move-operation-support-for-resources"></a>Supporto per lo spostamento delle risorse

Questo articolo indica se un tipo di risorsa di Azure supporta l'operazione di spostamento. Vengono inoltre fornite informazioni sulle condizioni speciali da considerare quando si sposta una risorsa.

> [!IMPORTANT]
> Nella maggior parte dei casi, una risorsa figlio non può essere spostata in modo indipendente dalla risorsa padre. Le risorse figlio hanno un tipo di risorsa nel formato `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Ad esempio, `Microsoft.ServiceBus/namespaces/queues` è una risorsa figlio di `Microsoft.ServiceBus/namespaces` . Quando si sposta la risorsa padre, la risorsa figlio viene spostata automaticamente con essa. Se in questo articolo non viene visualizzata una risorsa figlio, è possibile presupporre che sia stata spostata con la risorsa padre. Se la risorsa padre non supporta lo spostamento, la risorsa figlio non può essere spostata.

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
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | No | No |  No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | No | No | No |
> | diagnosticsettingscategories | No | No | No |
> | privatelinkforazuread | Sì | Sì | No |
> | tenants | Sì | Sì | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | No | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | No | No | No |
> | addsservices | No | No | No |
> | agents | No | No | No |
> | anonymousapiusers | No | No | No |
> | configurazione | No | No | No |
> | log | No | No | No |
> | reports | No | No | No |
> | servicehealthmetrics | No | No | No |
> | services | No | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | configurazioni | No | No | No |
> | generaterecommendations | No | No | No |
> | metadata | No | No | No |
> | raccomandazioni di film | No | No | No |
> | suppressions | No | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Sì | Sì | No |
> | alerts | No | No | No |
> | alertslist | No | No | No |
> | alertsmetadata | No | No | No |
> | alertssummary | No | No | No |
> | alertssummarylist | No | No | No |
> | smartdetectoralertrules | Sì | Sì | No |
> | smartgroups | No | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Sì | Sì | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Non è possibile spostare un servizio Gestione API impostato sull'utilizzo delle SKU.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | No | No | No |
> | service | Sì | Sì | Sì (usando il modello) <br/><br/> [Spostare API Management tra aree](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Sì | Sì | No |
> | configurationstores/eventgridfilters | No | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | spring | Sì | Sì | No |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | No | No | Sì (usando il modello)<br/><br/> [Spostare un'app del servizio app in un'altra area](../../app-service/manage-move-across-regions.md) |
> | appidentities | No | No | No |
> | gateways | No | No | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Sì | Sì | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | No | No | No |
> | dataaliases | No | No | No |
> | denyassignments | No | No | No |
> | elevateaccess | No | No | No |
> | findorphanroleassignments | No | No | No |
> | locks | No | No | No |
> | autorizzazioni | No | No | No |
> | policyassignments | No | No | No |
> | policydefinitions | No | No | No |
> | policysetdefinitions | No | No | No |
> | privatelinkassociations | No | No | No |
> | resourcemanagementprivatelinks | No | No | No |
> | roleassignments | No | No | No |
> | roleassignmentsusagemetrics | No | No | No |
> | roledefinitions | No | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> I runbook devono esistere nello stesso gruppo di risorse dell'account di automazione.
>
> Per informazioni, vedere [Spostare l'account Automazione di Azure in un'altra sottoscrizione.](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Sì | Sì | Sì (usando il modello) <br/><br/> [Uso della replica geografica](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configurations | Sì | Sì | No |
> | automationaccounts/runbooks | Sì | Sì | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Sì | Sì | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Sì | Sì | No |
> | b2ctenants | No | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | No | No | No |
> | hybriddatamanagers | No | No | No |
> | postgresinstances | No | No | No |
> | sqlinstances | No | No | No |
> | sqlmanagedinstances | No | No | No |
> | sqlserverinstances | No | No | No |
> | sqlserverregistrations | Sì | Sì | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | No | No | No |
> | registrations | Sì | Sì | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | No | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Sì | Sì | Gli account Batch non possono essere spostati direttamente da un'area a un'altra, ma è possibile usare un modello per esportare un modello, modificarlo e distribuire il modello nella nuova area. <br/><br/> Informazioni sullo [spostamento di un account Batch tra aree](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | No | No | No |
> | billingperiods | No | No | No |
> | billingpermissions | No | No | No |
> | billingproperty | No | No | No |
> | billingroleassignments | No | No | No |
> | billingroledefinitions | No | No | No |
> | departments | No | No | No |
> | enrollmentaccounts | No | No | No |
> | invoices | No | No | No |
> | transfers | No | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | No | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | No | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | No | No | No <br/><br/> La rete blockchain non può avere nodi in aree diverse. |
> | cordamembers | No | No | No |
> | watchers | No | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | No | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | No | No | No |
> | blueprints | No | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Sì | Sì | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Se l'istanza di cache di Azure per Redis è configurata con una rete virtuale, l'istanza non può essere spostata in una sottoscrizione diversa. Vedere [Limitazioni di spostamento della rete](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | Sì | Sì | No |
> | redisenterprise | No | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | No | No | No |
> | calculateexchange | No | No | No |
> | calculateprice | No | No | No |
> | calculatepurchaseprice | No | No | No |
> | catalogs | No | No | No |
> | commercialreservationorders | No | No | No |
> | exchange | No | No | No |
> | reservationorders | No | No | No |
> | reservations | No | No | No |
> | resources | No | No | No |
> | validatereservationorder | No | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | No | No | No |
> | cdnwebapplicationfirewallpolicies | Sì | Sì | No |
> | edgenodes | No | No | No |
> | Profili | Sì | Sì | No |
> | profiles/endpoints | Sì | Sì | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Sì | Sì | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | No | No | No |
> | domainnames | Sì | No | No |
> | quotas | No | No | No |
> | resourcetypes | No | No | No |
> | validatesubscriptionmoveavailability | No | No | No |
> | virtualmachines | Sì | Sì | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | No | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | No | No | No |
> | expressroutecrossconnections | No | No | No |
> | expressroutecrossconnections/peerings | No | No | No |
> | gatewaysupporteddevices | No | No | No |
> | networksecuritygroups | No | No | No |
> | quotas | No | No | No |
> | reservedips | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | disks | No | No | No |
> | images | No | No | No |
> | osimages | No | No | No |
> | osplatformimages | No | No | No |
> | publicimages | No | No | No |
> | quotas | No | No | No |
> | storageaccounts | Sì | No | Sì |
> | vmimages | No | No | No |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento della distribuzione classica](./move-limitations/classic-model-move-limitations.md). Le risorse della distribuzione classica possono essere spostate tra sottoscrizioni con un'operazione specifica di tale scenario.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | Operazioni | No | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Sì | Sì | No |
> | Ricerca cognitiva | **In sospeso** | **In sospeso** | Supportato con passaggi manuali.<br/><br/> Informazioni sullo [spostamento del servizio Ricerca cognitiva di Azure in un'altra area](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | No | No | No |
> | usageaggregates | No | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento di macchine virtuali](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Sì | Sì |  Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare i set di disponibilità. |
> | diskaccesses | No | No | No |
> | diskencryptionsets | No | No | No |
> | disks | Sì | Sì | Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le macchine virtuali di Azure e i dischi correlati. |
> | galleries | No | No | No |
> | galleries/images | No | No | No |
> | galleries/images/versions | No | No | No |
> | hostgroups | No | No | No |
> | hostgroups/hosts | No | No | No |
> | images | Sì | Sì | No |
> | proximityplacementgroups | Sì | Sì | No |
> | restorepointcollections | No | No | No |
> | restorepointcollections/restorepoints | No | No | No |
> | sharedvmextensions | No | No | No |
> | sharedvmimages | No | No | No |
> | sharedvmimages/versions | No | No | No |
> | snapshots | Sì | Sì | No |
> | sshpublickeys | No | No | No |
> | virtualmachines | Sì | Sì | Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le macchine virtuali di Azure. |
> | virtualmachines/extensions | Sì | Sì | No |
> | virtualmachinescalesets | Sì | Sì | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | No | No | No |
> | balances | No | No | No |
> | budgets | No | No | No |
> | charges | No | No | No |
> | costtags | No | No | No |
> | credits | No | No | No |
> | eventi | No | No | No |
> | forecasts | No | No | No |
> | lots | No | No | No |
> | marketplaces | No | No | No |
> | pricesheets | No | No | No |
> | products | No | No | No |
> | reservationdetails | No | No | No |
> | reservationrecommendationdetails | No | No | No |
> | reservationrecommendations | No | No | No |
> | reservationsummaries | No | No | No |
> | reservationtransactions | No | No | No |
> | tags | No | No | No |
> | tenants | No | No | No |
> | terms | No | No | No |
> | usagedetails | No | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | No | No | No |
> | serviceassociationlinks | No | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | registries | Sì | Sì | No |
> | registries/agentpools | Sì | Sì | No |
> | registries/buildtasks | Sì | Sì | No |
> | registries/replications | Sì | Sì | No |
> | registries/tasks | Sì | Sì | No |
> | registries/webhooks | Sì | Sì | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | No | No | No |
> | managedclusters | No | No | No |
> | openshiftmanagedclusters | No | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | scala Web | No | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | No | No | No |
> | billingaccounts | No | No | No |
> | budgets | No | No | No |
> | cloudconnectors | No | No | No |
> | dell'account di integrazione | Sì | Sì | No |
> | departments | No | No | No |
> | dimensions | No | No | No |
> | enrollmentaccounts | No | No | No |
> | exports | No | No | No |
> | externalbillingaccounts | No | No | No |
> | previsione | No | No | No |
> | query | No | No | No |
> | register | No | No | No |
> | reportconfigs | No | No | No |
> | reports | No | No | No |
> | Scheda Impostazioni | No | No | No |
> | showbackrules | No | No | No |
> | Viste | No | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | No | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | requests | No | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | associations | No | No | No |
> | resourceproviders | Sì | Sì | No |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | No | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | No | No | No |
> | databoxedgedevices | No | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aree di lavoro | No | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | catalogs | Sì | Sì | No |
> | datacatalogs | No | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | No | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | packages | No | No | No |
> | plans | No | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Sì | Sì | No |
> | factories | Sì | Sì | No |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | No | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Sì | Sì | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Sì | Sì | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | services/projects | No | No | No |
> | slot | No | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | No | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Sì | Sì | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Sì | Sì | È possibile usare una replica in lettura tra aree per spostare un server esistente. [Altre informazioni](../../postgresql/howto-move-regions-portal.md)<br/><br/> Se viene effettuato il provisioning del servizio con archiviazione di backup con ridondanza geografica, è possibile usare il ripristino geografico per eseguire il ripristino in altre aree. [Altre informazioni](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | server flessibili | No | No | No |
> | servers | Sì | Sì | È possibile usare una replica in lettura tra aree per spostare un server esistente. [Altre informazioni](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | No | No | No |
> | servergroups | No | No | No |
> | servers | Sì | Sì | È possibile usare una replica in lettura tra aree per spostare un server esistente. [Per altre informazioni, vedere](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Sì | Sì | No |
> | singleservers | Sì | Sì | No |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Sì | Sì | No |
> | rollouts | Sì | Sì | No |
> | servicetopologies | Sì | Sì | No |
> | servicetopologies/services | Sì | Sì | No |
> | servicetopologies/services/serviceunits | Sì | Sì | No |
> | steps | Sì | Sì | No |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Sì | Sì | No |
> | hostpools | Sì | Sì | No |
> | aree di lavoro | Sì | Sì | No |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | No | No | No. La risorsa non viene esposta. |
> | elasticpools/iothubtenants | No | No | No. La risorsa non viene esposta. |
> | iothubs | Sì | Sì | Sì. [Scopri di più](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Sì | Sì | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | pipelines | Sì | Sì | No |
> | controllers | **In sospeso** | **In sospeso** | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | controllers | Sì | Sì | No |
> | Cluster del servizio AKS | **In sospeso** | **In sospeso** | No<br/><br/> [Altre informazioni sullo](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) spostamento in un'altra area.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | No | No | No |
> | labs | Sì | No | No |
> | labs/environments | Sì | Sì | No |
> | labs/servicerunners | Sì | Sì | No |
> | labs/virtualmachines | Sì | No | No |
> | schedules | Sì | Sì | No |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | No | No | Sì, ricreando le risorse in una nuova area. [Scopri di più](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | No | No | No |
> | databaseaccounts | Sì | Sì | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | Sì | Sì | No |
> | generatessorequest | No | No | No |
> | topleveldomains | No | No | No |
> | validatedomainregistrationinformation | No | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Sì | Sì | No |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | Sì | Sì | No |
> | eventsubscriptions | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. | No: non può essere spostato in modo indipendente, ma spostato automaticamente con la risorsa sottoscritta. | No |
> | extensiontopics | No | No | No |
> | partnernamespaces | Sì | Sì | No |
> | partnerregistrations | No | No | No |
> | partnertopics | Sì | Sì | No |
> | systemtopics | Sì | Sì | No |
> | topics | Sì | Sì | No |
> | topictypes | No | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Sì | Sì | No |
> | spazi dei nomi | Sì | Sì | Sì (con modello)<br/><br/> [Spostare uno spazio dei nomi dell'hub eventi in un'altra area](../../event-hubs/move-across-regions.md) |
> | sku | No | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | No | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | spazi dei nomi | Sì | Sì | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | No | No | No |
> | funzionalità | No | No | No |
> | provider | No | No | No |
> | subscriptionfeatureregistrations | No | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | No | No | No |
> | automanagedvmconfigurationprofiles | No | No | No |
> | guestconfigurationassignments | No | No | No |
> | software | No | No | No |
> | softwareupdateprofile | No | No | No |
> | softwareupdates | No | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | No | No | No |
> | sapmonitors | No | No | No |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | No | No | No |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> È possibile spostare i cluster HDInsight in una nuova sottoscrizione o in un nuovo gruppo di risorse. Non è tuttavia possibile spostare tra sottoscrizioni le risorse di rete collegate al cluster HDInsight, ad esempio la rete virtuale, l'interfaccia di rete o il servizio di bilanciamento del carico. Non è possibile spostare in un nuovo gruppo di risorse un'interfaccia di rete collegata a una macchina virtuale per il cluster.
>
> Quando si sposta un cluster HDInsight in una nuova sottoscrizione, spostare prima altre risorse, ad esempio l'account di archiviazione. Spostare quindi il cluster HDInsight.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Sì | Sì | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Sì | Sì | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | machines | Sì | Sì | No |
> | machines/extensions | Sì | Sì | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Sì | Sì | No |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | No | No | No |
> | vnfs | No | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | components | No | No | No |
> | networkscopes | No | No | No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | Sì | Sì | No |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | **In sospeso** | **In sospeso** | No. [Altre informazioni](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region) |
> | actiongroups | Sì | Sì | No |
> | activitylogalerts | No | No | No |
> | alertrules | Sì | Sì | No |
> | autoscalesettings | Sì | Sì | No |
> | baseline | No | No | No |
> | components | Sì | Sì | No |
> | datacollectionrules | No | No | No |
> | diagnosticsettings | No | No | No |
> | diagnosticsettingscategories | No | No | No |
> | eventcategories | No | No | No |
> | eventtypes | No | No | No |
> | extendeddiagnosticsettings | No | No | No |
> | guestdiagnosticsettings | No | No | No |
> | listmigrationdate | No | No | No |
> | logdefinitions | No | No | No |
> | logprofiles | No | No | No |
> | log | No | No | No |
> | metricalerts | No | No | No |
> | metricbaselines | No | No | No |
> | metricbatch | No | No | No |
> | metricdefinitions | No | No | No |
> | metricnamespaces | No | No | No |
> | Metriche | No | No | No |
> | migratealertrules | No | No | No |
> | migratetonewpricingmodel | No | No | No |
> | myworkbooks | No | No | No |
> | notificationgroups | No | No | No |
> | privatelinkscopes | No | No | No |
> | rollbacktolegacypricingmodel | No | No | No |
> | scheduledqueryrules | Sì | Sì | No |
> | Topologia | No | No | No |
> | transazioni | No | No | No |
> | vminsightsonboardingstatuses | No | No | No |
> | webtests | Sì | Sì | No |
> | webtests/gettestresultfile | No | No | No |
> | workbooks | Sì | Sì | No |
> | workbooktemplates | Sì | Sì | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | No | No | No |
> | iotapps | Sì | Sì | No |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **In sospeso** | **In sospeso** | Sì (hub clone) <br/><br/> [Clonare un hub IoT in un'altra area](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | graph | Sì | Sì | No |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Gli insiemi di credenziali delle chiavi usati per la crittografia dei dischi non possono essere spostati in un gruppo di risorse nella stessa sottoscrizione o in sottoscrizioni diverse.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | No | No | No |
> | hsmpools | No | No | No |
> | managedhsms | No | No | No |
> | insiemi di credenziali | Sì | Sì | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Sì | Sì | No |
> | registeredsubscriptions | No | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | No | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Sì | Sì | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | No | No | No |
> | users | No | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No, è un servizio globale. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | No | No | No |
> | integrationaccounts | Sì | Sì | No |
> | integrationserviceenvironments | Sì | No | No |
> | integrationserviceenvironments/managedapis | Sì | No | No |
> | isolatedenvironments | No | No | No |
> | flussi di lavoro | Sì | Sì | No |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | No | No | No |
> | webservices | Sì | No | No |
> | aree di lavoro | Sì | Sì | No |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | No | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |
> | teamaccounts | No | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aree di lavoro | No | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | No | No | Sì. [Scopri di più](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Sì | Sì | Sì. [Scopri di più](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | No | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | identità | No | No | No |
> | userassignedidentities | No | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | No | No | No |
> | managednetworks/managednetworkgroups | No | No | No |
> | managednetworks/managednetworkpeeringpolicies | No | No | No |
> | notifica | No | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | No | No | No |
> | registrationassignments | No | No | No |
> | registrationdefinitions | No | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | No | No | No |
> | managementgroups | No | No | No |
> | managementgroups/settings | No | No | No |
> | resources | No | No | No |
> | starttenantbackfill | No | No | No |
> | tenantbackfillstatus | No | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Sì | Sì | No, Mappe di Azure è un servizio geospaziale. |
> | accounts/privateatlases | Sì | Sì | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | offers | No | No | No |
> | offertypes | No | No | No |
> | privategalleryitems | No | No | No |
> | privatestoreclient | No | No | No |
> | privatestores | No | No | No |
> | products | No | No | No |
> | publishers | No | No | No |
> | register | No | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | No | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | agreements | No | No | No |
> | offertypes | No | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Sì | Sì | No |
> | mediaservices/liveevents | Sì | Sì | No |
> | mediaservices/streamingendpoints | Sì | Sì | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | No | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | No | No | No |
> | migrateprojects | No | No | No |
> | movecollections | No | No | No |
> | projects | No | No | No |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | No | No | No |
> | objectunderstandingaccounts | No | No | No |
> | remoterenderingaccounts | Sì | Sì | No |
> | spatialanchorsaccounts | Sì | Sì | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | No | No | No |
> | netappaccounts/capacitypools | No | No | No |
> | netappaccounts/capacitypools/volumes | No | No | No |
> | netappaccounts/capacitypools/volumes/mounttargets | No | No | No |
> | netappaccounts/capacitypools/volumes/snapshots | No | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Vedere [Linee guida di spostamento della rete](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | No | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No | No |
> | applicationsecuritygroups | Sì | Sì | No |
> | azurefirewalls | No | No | No |
> | bastionhosts | No | No | No |
> | bgpservicecommunities | No | No | No |
> | connections | Sì | Sì | No |
> | ddoscustompolicies | Sì | Sì | No |
> | ddosprotectionplans | No | No | No |
> | dnszones | Sì | Sì | No |
> | expressroutecircuits | No | No | No |
> | expressroutegateways | No | No | No |
> | expressrouteserviceproviders | No | No | No |
> | firewallpolicies | Sì | Sì | No |
> | frontdoors | No | No | No |
> | ipallocations | Sì | Sì | No |
> | ipgroups | Sì | Sì | No |
> | loadbalancers | Sì - SKU di base<br> Sì - SKU Standard | Sì - SKU di base<br>No - SKU standard | Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare i servizi di bilanciamento del carico interni ed esterni. |
> | localnetworkgateways | Sì | Sì | No |
> | natgateways | No | No | No |
> | networkexperimentprofiles | No | No | No |
> | networkintentpolicies | Sì | Sì | No |
> | networkinterfaces | Sì | Sì | Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare le schede di interfaccia di rete. |
> | networkprofiles | No | No | No |
> | networksecuritygroups | Sì | Sì | Sì <br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare gruppi di sicurezza di rete (NGS). |
> | networkwatchers | No | No | No |
> | networkwatchers/connectionmonitors | Sì | No | No |
> | networkwatchers/flowlogs | Sì | No | No |
> | networkwatchers/pingmeshes | Sì | No | No |
> | p2svpngateways | No | No | No |
> | privatednszones | Sì | Sì | No |
> | privatednszones/virtualnetworklinks | Sì | Sì | No |
> | privatednszonesinternal | No | No | No |
> | privateendpointredirectmaps | No | No | No |
> | privateendpoints | No | No | No |
> | privatelinkservices | No | No | No |
> | publicipaddresses | Sì - SKU di base<br>Sì - SKU Standard | Sì - SKU di base<br>No - SKU standard | Sì<br/><br/> Usare [Spostamento risorse di Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) per spostare gli indirizzi IP pubblici. |
> | publicipprefixes | Sì | Sì | No |
> | routefilters | No | No | No |
> | routetables | Sì | Sì | No |
> | securitypartnerproviders | Sì | Sì | No |
> | serviceendpointpolicies | Sì | Sì | No |
> | trafficmanagergeographichierarchies | No | No | No |
> | trafficmanagerprofiles | Sì | Sì | No |
> | trafficmanagerprofiles/heatmaps | No | No | No |
> | trafficmanagerusermetricskeys | No | No | No |
> | virtualhubs | No | No | No |
> | virtualnetworkgateways | Sì | Sì | No |
> | virtualnetworks | Sì | Sì | No |
> | virtualnetworktaps | No | No | No |
> | virtualrouters | Sì | Sì | No |
> | virtualwans | No | No |
> | vpngateways (rete WAN virtuale) | No | No | No |
> | vpnserverconfigurations | No | No | No |
> | vpnsites (rete WAN virtuale) | No | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | spazi dei nomi | Sì | Sì | No |
> | namespaces/notificationhubs | Sì | Sì | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Sì | Sì | No |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | No | No | No |
> | importsites | No | No | No |
> | serversites | No | No | No |
> | vmwaresites | No | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Verificare che lo spostamento nella nuova sottoscrizione non comporti il superamento delle [quote di sottoscrizione](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Non è possibile spostare le aree di lavoro con un account di automazione collegato. Prima di iniziare un'operazione di spostamento, assicurarsi di scollegare tutti gli account di automazione.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | No | No | No |
> | deletedworkspaces | No | No | No |
> | linktargets | No | No | No |
> | storageinsightconfigs | No | No | No |
> | aree di lavoro | Sì | Sì | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | No | No | No |
> | managementconfigurations | Sì | Sì | No |
> | solutions | Sì | Sì | No |
> | Viste | Sì | Sì | No |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | No | No | No |
> | peerasns | No | No | No |
> | peeringlocations | No | No | No |
> | peerings | No | No | No |
> | peeringservicecountries | No | No | No |
> | peeringservicelocations | No | No | No |
> | peeringserviceproviders | No | No | No |
> | peeringservices | No | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | No | No | No |
> | policystates | No | No | No |
> | policytrackedresources | No | No | No |
> | remediations | No | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | consoles | No | No | No |
> | dashboards | Sì | Sì | No |
> | usersettings | No | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Sì | Sì | No |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | capacities | Sì | Sì | No |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ---------- |
> | account | No | No | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ---------- |
> | account | **In sospeso** | **In sospeso** | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | No | No | No |
> | providerregistrations | No | No | No |
> | rollouts | No | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aree di lavoro | No | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Vedere [Linee guida di spostamento di Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | No | No | No |
> | insiemi di credenziali | Sì | Sì | No.<br/><br/> Lo spostamento di insiemi di credenziali di Servizi di Backup di Azure tra aree di Azure non è supportato.<br/><br/> In Insiemi di credenziali di Servizi di ripristino per Azure Site Recovery è possibile disabilitare e [ricreare l'insieme di](../../site-recovery/move-vaults-across-regions.md) credenziali nell'area di destinazione. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | No | No | No |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | spazi dei nomi | Sì | Sì | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | query | Sì | Sì | No |
> | resourcechangedetails | No | No | No |
> | resourcechanges | No | No | No |
> | resources | No | No | No |
> | resourceshistory | No | No | No |
> | subscriptionsstatus | No | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | No | No | No |
> | emergingissues | No | No | No |
> | eventi | No | No | No |
> | metadata | No | No | No |
> | Notifiche | No | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | deployments | No | No | No |
> | deploymentscripts | No | No | Sì<br/><br/>[Spostare le risorse Microsoft.Resources in una nuova area](microsoft-resources-move-regions.md) |
> | deploymentscripts/logs | No | No | No |
> | collegamenti | No | No | No |
> | provider | No | No | No |
> | resourcegroups | No | No | No |
> | resources | No | No | No |
> | subscriptions | No | No | No |
> | tags | No | No | No |
> | templatespecs | No | No | Sì<br/><br/>[Spostare le risorse Microsoft.Resources in una nuova area](microsoft-resources-move-regions.md) |
> | templatespecs/versions | No | No | No |
> | tenants | No | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | scala Web | Sì | No | No |
> | saasresources | No | No | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Non è possibile spostare più risorse di ricerca in aree diverse in un'unica operazione. Al contrario, è possibile spostarle con operazioni separate.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | No | No | No |
> | searchservices | Sì | Sì | No |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | No | No | No |
> | advancedthreatprotectionsettings | No | No | No |
> | alerts | No | No | No |
> | allowedconnections | No | No | No |
> | applicationwhitelistings | No | No | No |
> | assessmentmetadata | No | No | No |
> | assessments | No | No | No |
> | autodismissalertsrules | No | No | No |
> | automations | Sì | Sì | No |
> | autoprovisioningsettings | No | No | No |
> | complianceresults | No | No | No |
> | compliances | No | No | No |
> | datacollectionagents | No | No | No |
> | devicesecuritygroups | No | No | No |
> | discoveredsecuritysolutions | No | No | No |
> | externalsecuritysolutions | No | No | No |
> | informationprotectionpolicies | No | No | No |
> | iotsecuritysolutions | Sì | Sì | No |
> | iotsecuritysolutions/analyticsmodels | No | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | No | No | No |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | No | No | No |
> | jitnetworkaccesspolicies | No | No | No |
> | criteri | No | No | No |
> | pricings | No | No | No |
> | regulatorycompliancestandards | No | No | No |
> | regulatorycompliancestandards/regulatorycompliancecontrols | No | No | No |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | No | No | No |
> | securitycontacts | No | No | No |
> | securitysolutions | No | No | No |
> | securitysolutionsreferencedata | No | No | No |
> | securitystatuses | No | No | No |
> | securitystatusessummaries | No | No | No |
> | servervulnerabilityassessments | No | No | No |
> | Scheda Impostazioni | No | No | No |
> | subassessments | No | No | No |
> | attività | No | No | No |
> | topologies | No | No | No |
> | workspacesettings | No | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregations | No | No | No |
> | alertrules | No | No | No |
> | alertruletemplates | No | No | No |
> | regole di automazione | No | No | No |
> | bookmarks | No | No | No |
> | cases | No | No | No |
> | dataconnectors | No | No | No |
> | entities | No | No | No |
> | entityqueries | No | No | No |
> | incidents | No | No | No |
> | officeconsents | No | No | No |
> | Scheda Impostazioni | No | No | No |
> | threatintelligence | No | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | No | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | gateways | No | No | No |
> | nodes | No | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | spazi dei nomi | Sì | Sì | No |
> | premiummessagingregions | No | No | No |
> | sku | No | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | scala Web | No | No | No |
> | clusters | Sì | Sì | No |
> | containergroups | No | No | No |
> | containergroupsets | No | No | No |
> | edgeclusters | No | No | No |
> | managedclusters | No | No | No |
> | networks | No | No | No |
> | secretstores | No | No | No |
> | volumes | No | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | scala Web | Sì | Sì | No |
> | containergroups | No | No | No |
> | gateways | Sì | Sì | No |
> | networks | Sì | Sì | No |
> | chiavi private | Sì | Sì | No |
> | volumes | Sì | Sì | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | No | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Sì | Sì | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | No | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | No | No | No |
> | scala Web | No | No | No |
> | jitrequests | No | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Un database e un server devono trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, quindi, vengono spostati anche tutti i relativi database. Questo comportamento si applica ai database SQL di Azure e Azure Synapse Analytics database.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | No | No | No |
> | locations | Sì | Sì | No |
> | managedinstances | No | No | Sì <br/><br/> [Altre informazioni sullo](../../azure-sql/database/move-resources-across-regions.md) spostamento di istanze gestite tra aree. |
> | managedinstances/databases | No | No | Sì |
> | servers | Sì | Sì |Sì |
> | servers/databases | Sì | Sì | Sì <br/><br/> [Altre informazioni sullo](../../azure-sql/database/move-resources-across-regions.md) spostamento di database tra aree diverse.<br/><br/> [Altre informazioni](../../resource-mover/tutorial-move-region-sql.md) sull'uso Spostamento risorse di Azure per spostare Azure SQL database.  |
> | servers/databases/backuplongtermretentionpolicies | Sì | Sì | No |
> | servers/elasticpools | Sì | Sì | Sì <br/><br/> [Altre informazioni sullo](../../azure-sql/database/move-resources-across-regions.md) spostamento di pool elastici tra aree.<br/><br/> [Altre informazioni](../../resource-mover/tutorial-move-region-sql.md) sull'uso Spostamento risorse di Azure per spostare Azure SQL pool elastici.  |
> | servers/jobaccounts | Sì | Sì | No |
> | servers/jobagents | Sì | Sì | No |
> | virtualclusters | Sì | Sì | Sì |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Sì | Sì | No |
> | sqlvirtualmachines | Sì | Sì | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Sì | Sì | Sì<br/><br/> [Spostare un account Archiviazione di Azure in un'altra area](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | No | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Sì | Sì | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | managers | No | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> I processi di analisi di flusso non possono essere spostati durante l'esecuzione.

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | No | No | No |
> | streamingjobs | Sì | Sì | No |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | No | No | No |
> | instances | No | No | No |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | subscriptions | No | No | No |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | supporttickets | No | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | aree di lavoro | No | No | No |
> | workspaces/bigdatapools | No | No | No |
> | workspaces/sqlpools | No | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | Sì | Sì | No |
> | environments/eventsources | Sì | Sì | No |
> | environments/referencedatasets | Sì | Sì | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | stores | Sì | Sì | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | No | No | No |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Per modificare la sottoscrizione per Azure DevOps, vedere [Modificare la sottoscrizione di Azure usata per la fatturazione](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |
> | account/extension | No | No | No |
> | account/project | No | No | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | No | No | No |
> | resourcepools | No | No | No |
> | vcenters | No | No | No |
> | virtualmachines | No | No | No |
> | virtualmachinetemplates | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | No | No | No |
> | dedicatedcloudservices | No | No | No |
> | virtualmachines | No | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | No | No | No |
> | vnfs | No | No | No |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |
> | plans | No | No | No |
> | registeredsubscriptions | No | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Vedere [Linee guida per lo spostamento del servizio app](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | No | No | No |
> | billingmeters | No | No | No |
> | certificates | No | Sì | No |
> | connectiongateways | Sì | Sì | No |
> | connections | Sì | Sì | No |
> | customapis | Sì | Sì | No |
> | deletedsites | No | No | No |
> | deploymentlocations | No | No | No |
> | georegions | No | No | No |
> | hostingenvironments | No | No | No |
> | kubeenvironments | Sì | Sì | No |
> | publishingusers | No | No | No |
> | raccomandazioni di film | No | No | No |
> | resourcehealthmetadata | No | No | No |
> | runtimes | No | No | No |
> | serverfarms | Sì | Sì | No |
> | serverfarms/eventgridfilters | No | No | No |
> | siti | Sì | Sì | No |
> | sites/premieraddons | Sì | Sì | No |
> | sites/slots | Sì | Sì | No |
> | sourcecontrols | No | No | No |
> | staticsites | No | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | No | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento in un'area |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | No | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | carichi di lavoro | No | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Resource group | Subscription | Spostamento dell'area |
> | ------------- | ----------- | ---------- | ----------- |
> | components | No | No | No |
> | componentssummary | No | No | No |
> | monitorinstances | No | No | No |
> | monitorinstancessummary | No | No | No |
> | monitors | No | No | No |

## <a name="third-party-services"></a>Servizi di terze parti

I servizi di terze parti attualmente non supportano l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

- Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](move-resource-group-and-subscription.md).
- [Altre informazioni](../../resource-mover/overview.md) sul servizio Resource Mover.
- Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
