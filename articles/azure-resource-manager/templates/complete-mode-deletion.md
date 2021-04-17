---
title: Eliminazione in modalità completa
description: Descrive in che modo i tipi di risorsa gestiscono l'eliminazione in modalità completa in modelli di Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576064"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminazione di risorse di Azure per distribuzioni in modalità completa

Questo articolo descrive in che modo i tipi di risorsa gestiscono l'eliminazione quando non si trovano in un modello distribuito in modalità completa.

I tipi di risorse contrassegnati **con Sì** vengono eliminati quando il tipo non è nel modello distribuito in modalità completa.

I tipi di risorsa contrassegnati **con No** non vengono eliminati automaticamente quando non sono presenti nel modello. Tuttavia, vengono eliminati se la risorsa padre viene eliminata. Per una descrizione completa del comportamento, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).

Se si esegue la distribuzione in [più di un](./deploy-to-resource-group.md)gruppo di risorse in un modello, le risorse nel gruppo di risorse specificato nell'operazione di distribuzione sono idonee per l'eliminazione. Le risorse nei gruppi di risorse secondari non vengono eliminate.

Le risorse sono elencate in base allo spazio dei nomi del provider di risorse. Per trovare una corrispondenza tra uno spazio dei nomi del provider di risorse e il nome del servizio di Azure, vedere [Provider di risorse per i servizi di Azure.](../management/azure-services-resource-providers.md)

> [!NOTE]
> Usare sempre [l'operazione di what-if](template-deploy-what-if.md) prima di distribuire un modello in modalità completa. What-if mostra le risorse che verranno create, eliminate o modificate. Usare what-if per evitare di eliminare involontariamente le risorse.

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DomainServices | Sì |
> | DomainServices/oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
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
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | advisorScore | No |
> | configurazioni | No |
> | generateRecommendations | No |
> | metadata | No |
> | raccomandazioni di film | No |
> | suppressions | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | farmBeats | Sì |
> | farmBeats/eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | actionRules | Sì |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Sì |
> | smartDetectorAlertRules | Sì |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deletedServices | No |
> | getDomainOwnershipIdentifier | No |
> | reportFeedback | No |
> | service | Sì |
> | validateServiceName | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | migrateProjects | Sì |
> | migrateProjects/assessments | No |
> | migrateProjects/assessments/assessedApplications | No |
> | migrateProjects/assessments/assessedApplications/machines | No |
> | migrateProjects/assessments/assessedMachines | No |
> | migrateProjects/assessments/assessedMachines/applications | No |
> | migrateProjects/assessments/machinesToAssess | No |
> | migrateProjects/sites | No |
> | migrateProjects/sites/applianceConfigurations | No |
> | migrateProjects/sites/machines | No |
> | osVersions | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | configurationStores | Sì |
> | configurationStores/eventGridFilters | No |
> | configurationStores/keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Spring | Sì |
> | Spring/apps | No |
> | Spring/apps/deployments | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | attestationProviders | Sì |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | dataPolicyManifests | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | locks | No |
> | autorizzazioni | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Sì |
> | roleAssignmentApprovals | No |
> | roleAssignments | No |
> | roleAssignmentScheduleInstances | No |
> | roleAssignmentScheduleRequests | No |
> | roleAssignmentSchedules | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |
> | roleEligibilityScheduleInstances | No |
> | roleEligibilityScheduleRequests | No |
> | roleEligibilitySchedules | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Sì |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | automationAccounts | Sì |
> | automationAccounts/configurations | Sì |
> | automationAccounts/jobs | No |
> | automationAccounts/privateEndpointConnectionProxies | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts/runbooks | Sì |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | privateClouds | Sì |
> | privateClouds/addons | No |
> | privateClouds/authorizations | No |
> | privateClouds/cloudLinks | No |
> | privateClouds/clusters | No |
> | privateClouds/clusters/datastores | No |
> | privateClouds/globalReachConnections | No |
> | privateClouds/hcxEnterpriseSites | No |
> | privateClouds/scriptExecutions | No |
> | privateClouds/scriptPackages | No |
> | privateClouds/scriptPackages/scriptCmdlets | No |
> | privateClouds/workloadNetworks | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No |
> | privateClouds/workloadNetworks/dnsServices | No |
> | privateClouds/workloadNetworks/dnsZones | No |
> | privateClouds/workloadNetworks/gateways | No |
> | privateClouds/workloadNetworks/portMirroringProfiles | No |
> | privateClouds/workloadNetworks/segments | No |
> | privateClouds/workloadNetworks/virtualMachines | No |
> | privateClouds/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | No |
> | ambienti/account | No |
> | ambienti/account/spazi dei nomi | No |
> | ambienti/account/spazi dei nomi/configurazioni | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | b2cDirectories | Sì |
> | b2ctenants | No |
> | guestUsages | Sì |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataControllers | Sì |
> | dataWarehouseInstances | Sì |
> | postgresInstances | Sì |
> | sqlManagedInstances | Sì |
> | sqlServerInstances | Sì |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | autopilotEnvironments | Sì |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Sqlserverregistrations | Sì |
> | sqlServerRegistrations/sqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | catalogs | Sì |
> | catalogs/products | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Sì |
> | linkedSubscriptions | Sì |
> | registrations | Sì |
> | registrations/customerSubscriptions | No |
> | registrations/products | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | galleryImages | Sì |
> | networkInterfaces | Sì |
> | virtualHardDisks | Sì |
> | virtualMachines | Sì |
> | virtualNetworks | Sì |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | bareMetalInstances | Sì |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | batchAccounts | Sì |
> | batchAccounts/certificates | No |
> | batchAccounts/pools | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/agreements | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/customers | No |
> | billingAccounts/billingProfiles/instructions | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |
> | billingAccounts/billingProfiles/invoices/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/reservations | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/elevateRole | No |
> | billingAccounts/billingSubscriptions/invoices | No |
> | billingAccounts/createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/customers | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/customers/policies | No |
> | billingAccounts/customers/products | No |
> | billingAccounts/customers/transactions | No |
> | billingAccounts/customers/transfers | No |
> | billingAccounts/departments | No |
> | billingAccounts/departments/billingPermissions | No |
> | billingAccounts/departments/billingRoleAssignments | No |
> | billingAccounts/departments/billingRoleDefinitions | No |
> | billingAccounts/departments/billingSubscriptions | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts/enrollmentAccounts/billingPermissions | No |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | No |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | No |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | No |
> | billingAccounts/invoices | No |
> | billingAccounts/invoices/transactions | No |
> | billingAccounts/invoices/transactionSummary | No |
> | billingAccounts/invoiceSections | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/elevate | No |
> | billingAccounts/invoiceSections/initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections/productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceSections/products/transfer | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/transactions | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/payableOverage | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/payNow | No |
> | billingAccounts/products | No |
> | billingAccounts/reservations | No |
> | billingAccounts/transactions | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | promozioni | No |
> | transfers | No |
> | transfers/acceptTransfer | No |
> | trasferimenti/rifiutoTrasfero | No |
> | transfers/operationStatus | No |
> | transfers/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mapApis | Sì |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blockchainMembers | Sì |
> | membri di <a0/&gt; membri | Sì |
> | watchers | Sì |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | TokenServices | Sì |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/Gruppi | No |
> | TokenServices/Gruppi/Account | No |
> | TokenServices/TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments/assignmentOperations | No |
> | blueprintAssignments/operations | No |
> | blueprints | No |
> | blueprints/artifacts | No |
> | blueprints/versions | No |
> | blueprints/versions/artifacts | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | botServices | Sì |
> | botServices/channels | No |
> | botServices/connections | No |
> | impostazioni host | No |
> | languages | No |
> | Modelli | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Redis | Sì |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Sì |
> | redisEnterprise/databases | No |
> | RedisEnterprise/privateEndpointConnectionProxies | No |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | No |
> | RedisEnterprise/privateEndpointConnections | No |
> | RedisEnterprise/privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | catalogs | No |
> | commercialReservationOrders | No |
> | exchange | No |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders/calculateRefund | No |
> | reservationOrders/merge | No |
> | reservationOrders/reservations | No |
> | reservationOrders/reservations/revisions | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | reservations | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | siti | Sì |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Sì |
> | edgenodes | No |
> | Profili | Sì |
> | profiles/afdendpoints | Sì |
> | profiles/afdendpoints/routes | No |
> | profiles/customdomains | No |
> | profiles/endpoints | Sì |
> | profiles/endpoints/customdomains | No |
> | profiles/endpoints/origingroups | No |
> | profiles/endpoints/origins | No |
> | profiles/origingroups | No |
> | profiles/origingroups/origins | No |
> | profili/set di regole | No |
> | profili/set di regole/regole | No |
> | profili/segreti | No |
> | profili/criteri di sicurezza | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | certificateOrders | Sì |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | modifiche | No |
> | profile | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Sì |
> | domainNames/capabilities | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames/slots/roles/metricDefinitions | No |
> | domainNames/slots/roles/metrics | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Sì |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrics | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Sì |
> | quotas | No |
> | reservedIps | Sì |
> | virtualNetworks | Sì |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | capabilities | No |
> | disks | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Sì |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metrics | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/services/metrics | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | nodes | Sì |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | plans | Sì |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/privateEndpointConnectionProxies | No |
> | accounts/privateEndpointConnections | No |
> | accounts/privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | availabilitySets | Sì |
> | cloudServices | Sì |
> | cloudServices/networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices/roleInstances | No |
> | cloudServices/roleInstances/networkInterfaces | No |
> | cloudServizi/ruoli | No |
> | diskAccesses | Sì |
> | diskEncryptionSets | Sì |
> | disks | Sì |
> | galleries | Sì |
> | galleries/applications | No |
> | raccolte/applicazioni/versioni | No |
> | galleries/images | Sì |
> | galleries/images/versions | Sì |
> | hostGroups | Sì |
> | hostGroups/hosts | Sì |
> | images | Sì |
> | proximityPlacementGroups | Sì |
> | restorePointCollections | Sì |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Sì |
> | sharedVMExtensions/versions | No |
> | sharedVMImages | Sì |
> | sharedVMImages/versions | No |
> | snapshots | Sì |
> | sshPublicKeys | Sì |
> | virtualMachines | Sì |
> | virtualMachines/extensions | Sì |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/runCommands | Sì |
> | virtualMachineScaleSets | Sì |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Nodi cache | Sì |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | platformAccounts | Sì |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Pool di risorse | Sì |
> | VCenter | Sì |
> | VCenters/InventoryItems | No |
> | VirtualMachines | Sì |
> | VirtualMachines/Extensions | Sì |
> | VirtualMachines/GuestAgents | No |
> | VirtualMachines/HybridIdentityMetadata | No |
> | VirtualMachineTemplates | Sì |
> | VirtualNetworks | Sì |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Saldi | No |
> | Budget | No |
> | Charges | No |
> | CostTags | No |
> | credits | No |
> | eventi | No |
> | Previsioni | No |
> | lots | No |
> | Marketplace | No |
> | Pricesheets | No |
> | products | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | No |
> | Tag | No |
> | tenants | No |
> | Termini | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerGroups | Sì |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | registries | Sì |
> | registries/agentPools | Sì |
> | registries/builds | No |
> | registries/builds/cancel | No |
> | registries/builds/getLogLink | No |
> | registries/buildTasks | Sì |
> | registries/buildTasks/steps | No |
> | registries/connectedRegistries | No |
> | registries/connectedRegistries/deactivate | No |
> | registries/eventGridFilters | No |
> | registries/exportPipelines | No |
> | registries/generateCredentials | No |
> | registries/getBuildSourceUploadUrl | No |
> | registries/GetCredentials | No |
> | registries/importImage | No |
> | registries/importPipelines | No |
> | registries/pipelineRuns | No |
> | registries/privateEndpointConnectionProxies | No |
> | registries/privateEndpointConnectionProxies/validate | No |
> | registries/privateEndpointConnections | No |
> | registries/privateLinkResources | No |
> | registries/queueBuild | No |
> | registries/regenerateCredential | No |
> | registries/regenerateCredentials | No |
> | registries/replications | Sì |
> | registries/runs | No |
> | registries/runs/cancel | No |
> | registries/scheduleRun | No |
> | registries/scopeMaps | No |
> | registries/taskRuns | No |
> | registries/tasks | Sì |
> | registries/tokens | No |
> | registries/updatePolicies | No |
> | registries/webhooks | Sì |
> | registries/webhooks/getCallbackConfig | No |
> | registries/webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | containerServices | Sì |
> | managedClusters | Sì |
> | ManagedClusters/eventGridFilters | No |
> | openShiftManagedClusters | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Avvisi | No |
> | BillingAccounts | No |
> | Budget | No |
> | CloudConnectors | No |
> | Connettori | Sì |
> | costAllocationRules | No |
> | Departments | No |
> | Dimensioni | No |
> | EnrollmentAccounts | No |
> | Esportazioni | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/Alerts | No |
> | ExternalBillingAccounts/Dimensions | No |
> | ExternalBillingAccounts/Forecast | No |
> | ExternalBillingAccounts/Query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/Alerts | No |
> | ExternalSubscriptions/Dimensions | No |
> | ExternalSubscriptions/Forecast | No |
> | ExternalSubscriptions/Query | No |
> | fetchPrices | No |
> | Previsione | No |
> | GenerateDetailedCostReport | No |
> | GenerateReservationDetailsReport | No |
> | Informazioni dettagliate | No |
> | Query | No |
> | register | No |
> | Reportconfigs | No |
> | Report | No |
> | ScheduledActions | No |
> | Impostazioni | No |
> | showbackRules | No |
> | Viste | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DisableLockbox | No |
> | EnableLockbox | No |
> | requests | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | associations | No |
> | resourceProviders | Sì |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | instances | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aree di lavoro | Sì |
> | workspaces/dbWorkspaces | No |
> | aree di lavoro/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | catalogs | Sì |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataFactories | Sì |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | Sì |
> | factories/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/dataLakeStoreAccounts | No |
> | accounts/storageAccounts | No |
> | accounts/storageAccounts/containers | No |
> | accounts/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/eventGridFilters | No |
> | accounts/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DatabaseMigrations | No |
> | services | Sì |
> | services/projects | Sì |
> | SqlMigrationServices | Sì |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | BackupVaults | Sì |
> | ResourceGuards | Sì |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/shares | No |
> | accounts/shares/datasets | No |
> | accounts/shares/invitations | No |
> | accounts/shares/providersharesubscriptions | No |
> | accounts/shares/synchronizationSettings | No |
> | accounts/sharesubscriptions | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No |
> | accounts/sharesubscriptions/datasetmappings | No |
> | accounts/sharesubscriptions/triggers | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | servers | Sì |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers/start | No |
> | servers/stop | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | server flessibili | Sì |
> | servers | Sì |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers/start | No |
> | server/arresto | No |
> | servers/topQueryStatistics | No |
> | server/aggiornamento | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | flexibleServers | Sì |
> | serverGroups | Sì |
> | serverGroupsv2 | Sì |
> | servers | Sì |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/resetQueryPerformanceInsightData | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |
> | serversv2 | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | artifactSources | Sì |
> | rollouts | Sì |
> | serviceTopologies | Sì |
> | serviceTopologies/services | Sì |
> | serviceTopologies/services/serviceUnits | Sì |
> | steps | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationgroups | Sì |
> | applicationgroups/applications | No |
> | applicationgroups/desktops | No |
> | applicationgroups/startmenuitems | No |
> | hostpools | Sì |
> | hostpools/msixpackages | No |
> | hostpools/sessionhosts | No |
> | hostpools/sessionhosts/usersessions | No |
> | hostpools/usersessions | No |
> | scalePlans | Sì |
> | aree di lavoro | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Pool elastici | Sì |
> | ElasticPools/IotHubTenants | Sì |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | Hub IoT | Sì |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Sì |
> | usages | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/instances | Sì |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | pipelines | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | controllers | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labcenters | Sì |
> | labs | Sì |
> | labs/environments | Sì |
> | labs/serviceRunners | Sì |
> | labs/virtualMachines | Sì |
> | schedules | Sì |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | digitalTwinsInstances | Sì |
> | digitalTwinsInstances/endpoints | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cassandraClusters | Sì |
> | databaseAccountNames | No |
> | databaseAccounts | Sì |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | Sì |
> | domains/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/connectors | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Indirizzi | Sì |
> | orderCollections | Sì |
> | orders | Sì |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | domains | Sì |
> | domains/topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Sì |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistrazioni | Sì |
> | partnerTopics | Sì |
> | partnerTopics/eventSubscriptions | No |
> | systemTopics | Sì |
> | systemTopics/eventSubscriptions | No |
> | topics | Sì |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | spazi dei nomi | Sì |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventhubs | No |
> | namespaces/eventhubs/authorizationrules | No |
> | namespaces/eventhubs/consumergroups | No |
> | namespaces/networkrulesets | No |
> | namespaces/privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | experimentWorkspaces | Sì |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | featureConfigurations | No |
> | featureProviderNamespaces | No |
> | featureProviders | No |
> | funzionalità | No |
> | provider | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas/areas/areas | No |
> | myareas/areas/areas/galleryitems | No |
> | myareas/areas/galleryitems | No |
> | myareas/galleryitems | No |
> | register | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | autoManagedAccounts | Sì |
> | autoManagedVmConfigurationProfiles | Sì |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | aggiornamenti software | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hanaInstances | Sì |
> | sapMonitors | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedHSMs | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusterPools | Sì |
> | clusterPools/clusters | Sì |
> | clusters | Sì |
> | clusters/applications | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | healthBots | Sì |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | services | Sì |
> | services/iomtconnectors | No |
> | services/iomtconnectors/connections | No |
> | services/iomtconnectors/mappings | No |
> | services/privateEndpointConnectionProxies | No |
> | services/privateEndpointConnections | No |
> | services/privateLinkResources | No |
> | aree di lavoro | Sì |
> | workspaces/dicomservices | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | machines | Sì |
> | machines/assessPatches | No |
> | machines/extensions | Sì |
> | machines/installPatches | No |
> | machines/privateLinkScopes | No |
> | privateLinkScopes | Sì |
> | privateLinkScopes/privateEndpointConnectionProxies | No |
> | privateLinkScopes/privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dataManagers | Sì |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | devices | Sì |
> | funzioni di rete | Sì |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | Fornitori | No |
> | Vendor/vendorskus | No |
> | Vendor/vendorskus/previewsubscriptions | No |
> | virtualNetworkFunctions | Sì |
> | virtualNetworkFunctionVendors | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | components | Sì |
> | networkScopes | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | jobs | Sì |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Sì |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | defenderSettings | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Grafico | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | deletedVaults | No |
> | hsmPools | Sì |
> | managedHSMs | Sì |
> | insiemi di credenziali | Sì |
> | vaults/accessPolicies | No |
> | vaults/eventGridFilters | No |
> | vaults/keys | No |
> | vaults/keys/versions | No |
> | vaults/secrets | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | connectedClusters | Sì |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Estensioni | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | clusters/attacheddatabaseconfigurations | No |
> | clusters/databases | No |
> | clusters/databases/dataconnections | No |
> | clusters/databases/eventhubconnections | No |
> | clusters/databases/principalassignments | No |
> | clusters/databases/scripts | No |
> | clusters/dataconnections | No |
> | clusters/principalassignments | No |
> | clusters/sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | labaccounts | Sì |
> | labplans | Sì |
> | labs | Sì |
> | users | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hostingEnvironments | Sì |
> | integrationAccounts | Sì |
> | integrationServiceEnvironments | Sì |
> | integrationServiceEnvironments/managedApis | Sì |
> | isolatedEnvironments | Sì |
> | flussi di lavoro | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | commitmentPlans | Sì |
> | webServices | Sì |
> | Aree di lavoro | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | modelinventories | Sì |
> | virtualclusters | Sì |
> | aree di lavoro | Sì |
> | workspaces/batchEndpoints | Sì |
> | workspaces/batchEndpoints/deployments | Sì |
> | workspaces/batchEndpoints/deployments/jobs | No |
> | workspaces/batchEndpoints/jobs | No |
> | workspaces/codes | No |
> | workspaces/codes/versions | No |
> | workspaces/computes | No |
> | aree di lavoro/dati | No |
> | aree di lavoro/archivi dati | No |
> | aree di lavoro/ambienti | No |
> | workspaces/eventGridFilters | No |
> | aree di lavoro/processi | No |
> | workspaces/labelingJobs | No |
> | aree di lavoro/linkedServices | No |
> | aree di lavoro/modelli | No |
> | aree di lavoro/modelli/versioni | No |
> | aree di lavoro/onlineEndpoints | Sì |
> | workspaces/onlineEndpoints/deployments | Sì |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Sì |
> | publicMaintenanceConfigurations | No |
> | updates | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Identità | No |
> | userAssignedIdentities | Sì |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedNetworks | Sì |
> | managedNetworks/managedNetworkGroups | Sì |
> | managedNetworks/managedNetworkPeeringPolicies | Sì |
> | notifica | Sì |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/settings | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/creators | Sì |
> | accounts/eventGridFilters | No |
> | accounts/privateAtlases | Sì |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Mac | No |
> | offers | No |
> | offerTypes | No |
> | offerTypes/publishers | No |
> | offerTypes/publishers/offers | No |
> | offerTypes/publishers/offers/plans | No |
> | offerTypes/publishers/offers/plans/agreements | No |
> | offerTypes/publishers/offers/plans/configs | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/AdminRequestApprovals | No |
> | privateStores/offers | No |
> | privateStores/offers/acknowledgeNotification | No |
> | privateStores/queryNotificationsState | No |
> | privateStores/RequestApprovals | No |
> | privateStores/requestApprovals/query | No |
> | privateStores/requestApprovals/withdrawPlan | No |
> | products | No |
> | publishers | No |
> | publishers/offers | No |
> | publishers/offers/amendments | No |
> | register | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | classicDevServices | Sì |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | agreements | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | mediaservices | Sì |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices/assets/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/graphInstances | No |
> | mediaservices/graphTopologies | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Sì |
> | mediaservices/liveEvents/liveOutputs | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/mediaGraphs | No |
> | mediaservices/privateEndpointConnectionOperations | No |
> | mediaservices/privateEndpointConnectionProxies | No |
> | mediaservices/privateEndpointConnections | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Sì |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transforms | No |
> | mediaservices/transforms/jobs | No |
> | videoAnalyzers | Sì |
> | videoAnalyzers/edgeModules | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appClusters | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | assessmentProjects | Sì |
> | migrateprojects | Sì |
> | moveCollections | Sì |
> | projects | Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sì |
> | objectAnchorsAccounts | Sì |
> | objectUnderstandingAccounts | Sì |
> | remoteRenderingAccounts | Sì |
> | spatialAnchorsAccounts | Sì |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | networks | Sì |
> | networks/sites | Sì |
> | packetCores | Sì |
> | The sims | Sì |
> | sims/simProfiles | Sì |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | netAppAccounts | Sì |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/capacityPools | Sì |
> | netAppAccounts/capacityPools/volumes | Sì |
> | netAppAccounts/capacityPools/volumes/snapshots | No |
> | netAppAccounts/volumeGroups | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationGateways | Sì |
> | applicationGatewayWebApplicationFirewallPolicies | Sì |
> | applicationSecurityGroups | Sì |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Sì |
> | bastionHosts | Sì |
> | bgpServiceCommunities | No |
> | connections | Sì |
> | ddosCustomPolicies | Sì |
> | ddosProtectionPlans | Sì |
> | dnsOperationStatuses | No |
> | dnszones | Sì |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/all | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/recordsets | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Sì |
> | expressRouteCrossConnections | Sì |
> | expressRouteGateways | Sì |
> | expressRoutePorts | Sì |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Sì |
> | frontdoors | Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Sì |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Sì |
> | loadBalancers | Sì |
> | localNetworkGateways | Sì |
> | natGateways | Sì |
> | networkIntentPolicies | Sì |
> | networkInterfaces | Sì |
> | networkProfiles | Sì |
> | networkSecurityGroups | Sì |
> | networkWatchers | Sì |
> | networkWatchers/connectionMonitors | Sì |
> | networkWatchers/flowLogs | Sì |
> | networkWatchers/lenses | Sì |
> | networkWatchers/pingMeshes | Sì |
> | p2sVpnGateways | Sì |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Sì |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/all | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Sì |
> | privateEndpoints | Sì |
> | privateLinkServices | Sì |
> | publicIPAddresses | Sì |
> | publicIPPrefixes | Sì |
> | routeFilters | Sì |
> | routeTables | Sì |
> | serviceEndpointPolicies | Sì |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Sì |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Sì |
> | virtualNetworkGateways | Sì |
> | virtualNetworks | Sì |
> | virtualNetworks/subnets | No |
> | virtualNetworkTaps | Sì |
> | virtualWans | Sì |
> | vpnGateways | Sì |
> | vpnSites | Sì |
> | webApplicationFirewallPolicies | Sì |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | namespaces/notificationHubs | Sì |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | osNamespaces | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | HyperVSites | Sì |
> | ImportSites | Sì |
> | MasterSites | Sì |
> | ServerSites | Sì |
> | VMwareSites | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | querypacks | Sì |
> | storageInsightConfigs | No |
> | aree di lavoro | Sì |
> | workspaces/dataExports | No |
> | aree di lavoro/origini dati | No |
> | aree di lavoro/linkedServices | No |
> | aree di lavoro/linkedStorageAccounts | No |
> | workspaces/metadata | No |
> | workspaces/query | No |
> | workspaces/scopedPrivateLinkProxies | No |
> | workspaces/storageInsightConfigs | No |
> | aree di lavoro/tabelle | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Sì |
> | solutions | Sì |
> | Viste | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Sì |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServizi | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Attestati | No |
> | eventGridFilters | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | remediations | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | Sì |
> | configurazioni tenant | No |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Sì |
> | tenants | Sì |
> | tenant/aree di lavoro | No |
> | workspaceCollections | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | autoScaleVCores | Sì |
> | capacities | Sì |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | enterprisePolicies | Sì |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/customRollouts | No |
> | providerRegistrations/defaultRollouts | No |
> | providerRegistrations/resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | deletedAccounts | No |
> | getDefaultAccount | No |
> | removeDefaultAccount | No |
> | setDefaultAccount | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Aree di lavoro | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | insiemi di credenziali | Sì |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | OpenShiftClusters | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | namespaces/authorizationrules | No |
> | namespaces/hybridconnections | No |
> | namespaces/hybridconnections/authorizationrules | No |
> | namespaces/privateEndpointConnections | No |
> | namespaces/wcfrelays | No |
> | namespaces/wcfrelays/authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | appliances | Sì |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | query | Sì |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | eventi | No |
> | impactedResources | No |
> | metadata | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deployments | No |
> | deployments/operations | No |
> | deploymentScripts | Sì |
> | deploymentScripts/logs | No |
> | collegamenti | No |
> | provider | No |
> | resourceGroups | No |
> | subscriptions | No |
> | templateSpecs | Sì |
> | templateSpecs/versions | Sì |
> | tenants | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | resources | Sì |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Nuvole | Sì |
> | VirtualMachines | Sì |
> | VirtualMachineTemplates | Sì |
> | VirtualNetworks | Sì |
> | server vmm | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | assessments | No |
> | autoDismissAlertsRules | No |
> | automations | Sì |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dell'account di integrazione | No |
> | dataCollectionAgents | No |
> | devices | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | ingestionSettings | No |
> | insights | No |
> | iotAlerts | No |
> | iotAlertTypes | No |
> | iotDefenderSettings | No |
> | iotRecommendations | No |
> | iotRecommendationTypes | No |
> | iotSecuritySolutions | Sì |
> | iotSecuritySolutions/analyticsModels | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | iotSecuritySolutions/iotAlerts | No |
> | iotSecuritySolutions/iotAlertTypes | No |
> | iotSecuritySolutions/iotRecommendations | No |
> | iotSecuritySolutions/iotRecommendationTypes | No |
> | iotSensors | No |
> | iotSites | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | onPremiseIotSensors | No |
> | criteri | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores/secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | Scheda Impostazioni | No |
> | sqlVulnerabilityAssessments | No |
> | sottoassessment | No |
> | attività | No |
> | topologies | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | aggregations | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bookmarks | No |
> | cases | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | Arricchimento | No |
> | entities | No |
> | entityQueries | No |
> | entityQueryTemplates | No |
> | incidents | No |
> | officeConsents | No |
> | Scheda Impostazioni | No |
> | threatIntelligence | No |
> | watchlist | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | consoleServizi | No |
> | serialPorts | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | spazi dei nomi | Sì |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventgridfilters | No |
> | namespaces/networkrulesets | No |
> | namespaces/privateEndpointConnections | No |
> | namespaces/queues | No |
> | namespaces/queues/authorizationrules | No |
> | namespaces/topics | No |
> | namespaces/topics/authorizationrules | No |
> | namespaces/topics/subscriptions | No |
> | namespaces/topics/subscriptions/rules | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | clusters | Sì |
> | clusters/applications | No |
> | containerGroups | Sì |
> | containerGroupSets | Sì |
> | edgeclusters | Sì |
> | edgeclusters/applications | No |
> | managedclusters | Sì |
> | managedclusters/applications | No |
> | managedclusters/applications/services | No |
> | managedclusters/applicationTypes | No |
> | managedclusters/applicationTypes/versions | No |
> | managedclusters/nodetypes | No |
> | networks | Sì |
> | secretstores | Sì |
> | archivi segreti/certificati | No |
> | secretstores/secrets | No |
> | volumes | Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | scala Web | Sì |
> | containerGroups | Sì |
> | gateways | Sì |
> | networks | Sì |
> | chiavi private | Sì |
> | volumes | Sì |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | Linker | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/resourceTypeRegistrations | No |
> | rollouts | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SignalR | Sì |
> | SignalR/eventGridFilters | No |
> | WebPubSub | Sì |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | accounts/accountQuotaPolicies | No |
> | accounts/groupPolicies | No |
> | accounts/jobs | No |
> | accounts/storageContainers | No |
> | images | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | applicationDefinitions | Sì |
> | scala Web | Sì |
> | jitRequests | Sì |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managedInstances | Sì |
> | managedInstances/databases | Sì |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | servers | Sì |
> | servers/administrators | No |
> | servers/communicationLinks | No |
> | servers/databases | Sì |
> | servers/encryptionProtector | No |
> | servers/firewallRules | No |
> | servers/keys | No |
> | servers/restorableDroppedDatabases | No |
> | servers/serviceobjectives | No |
> | servers/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sì |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Sì |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | usages | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | amlFilesystems | Sì |
> | caches | Sì |
> | caches/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | storageSyncServices | Sì |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | managers | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | clusters | Sì |
> | clusters/privateEndpoints | No |
> | streamingjobs | Sì |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership | No |
> | acceptOwnershipStatus | No |
> | alias | No |
> | cancel | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | abilitare | No |
> | criteri | No |
> | ridenominazione | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | subscriptions | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | privateLinkHubs | Sì |
> | aree di lavoro | Sì |
> | workspaces/bigDataPools | Sì |
> | workspaces/operationStatuses | No |
> | workspaces/sqlDatabases | Sì |
> | workspaces/sqlPools | Sì |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | environments | Sì |
> | environments/accessPolicies | No |
> | environments/eventsources | Sì |
> | environments/privateEndpointConnectionProxies | No |
> | environments/privateEndpointConnections | No |
> | environments/privateLinkResources | No |
> | environments/referenceDataSets | Sì |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | stores | Sì |
> | stores/accessPolicies | No |
> | stores/services | No |
> | stores/services/tokens | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | imageTemplates | Sì |
> | imageTemplates/runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | ArcZones | Sì |
> | Pool di risorse | Sì |
> | VCenter | Sì |
> | VCenter/InventoryItems | No |
> | virtualmachines | Sì |
> | VirtualMachineTemplates | Sì |
> | VirtualNetworks | Sì |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sì |
> | dedicatedCloudServices | Sì |
> | virtualMachines | Sì |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | devices | Sì |
> | registeredSubscriptions | No |
> | Fornitori | No |
> | vendor/sku | No |
> | vendor/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Sì |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | account | Sì |
> | plans | Sì |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/apiAcls | No |
> | apiManagementAccounts/apis | No |
> | apiManagementAccounts/apis/apiAcls | No |
> | apiManagementAccounts/apis/connectionAcls | No |
> | apiManagementAccounts/apis/connections | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No |
> | apiManagementAccounts/apis/localizedDefinitions | No |
> | apiManagementAccounts/connectionAcls | No |
> | apiManagementAccounts/connections | No |
> | billingMeters | No |
> | certificates | Sì |
> | connectionGateways | Sì |
> | connections | Sì |
> | customApis | Sì |
> | deletedSites | No |
> | functionAppStacks | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Sì |
> | hostingEnvironments/eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | kubeEnvironments | Sì |
> | publishingUsers | No |
> | raccomandazioni di film | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Sì |
> | serverFarms/eventGridFilters | No |
> | serverFarms/firstPartyApps | No |
> | serverFarms/firstPartyApps/keyVaultSettings | No |
> | siti | Sì |
> | sites/config  | No |
> | sites/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | sites/networkConfig | No |
> | sites/premieraddons | Sì |
> | sites/slots | Sì |
> | sites/slots/eventGridFilters | No |
> | sites/slots/hostNameBindings | No |
> | sites/slots/networkConfig | No |
> | sourceControls | No |
> | staticSites | Sì |
> | validate | No |
> | verifyHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | multipleActivationKeys | Sì |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | DeviceServices | Sì |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | migrationAgents | Sì |
> | carichi di lavoro | Sì |
> | carichi di lavoro/istanze | No |
> | carichi di lavoro/versioni | No |
> | carichi di lavoro/versioni/artefatti | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Eliminazione in modalità completa |
> | ------------- | ----------- |
> | monitors | No |

## <a name="next-steps"></a>Passaggi successivi

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
