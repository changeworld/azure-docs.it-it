---
title: Esempi di Azure PowerShell-ottenere i dettagli del servizio cloud di Azure (supporto esteso)
description: Script di esempio per il recupero di informazioni su una distribuzione di servizi cloud di Azure (supporto esteso)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd272e0c6ce76ff69e3e58417845836fe2f55b39
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745140"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Recuperare informazioni sulle distribuzioni del servizio cloud di Azure (supporto esteso)

In questi esempi viene illustrata la procedura per recuperare informazioni sulle distribuzioni del servizio cloud di Azure (supporto esteso) esistenti.

## <a name="get-all-cloud-services-under-a-resource-group"></a>Ottenere tutti i servizi cloud in un gruppo di risorse

```powershell
Get-AzCloudService -ResourceGroup "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>Ottenere un singolo servizio cloud
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
$cloudService | Format-List
ResourceGroupName                       : ContosOrg
Configuration                           : <?xml version="1.0" encoding="utf-8"?>
                                          <ServiceConfiguration 
                                          xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
                                          </ServiceConfiguration>
ConfigurationUrl                        :
ExtensionProfileExtension               : {RDPExtension}
Id                                      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCS
Location                                : eastus2euap
Name                                    : ContosoCS
NetworkProfileLoadBalancerConfiguration : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
OSProfileSecret                         : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
PackageUrl                              : https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ProvisioningState                       : Succeeded
RoleProfileRole                         : {ContosoFrontEnd, ContosoBackEnd}
StartCloudService                       :
SwappableCloudServiceId                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCSTest
Tag                                     : {
                                            "Owner": "Contos"
                                          }
Type                                    : Microsoft.Compute/cloudServices
UniqueId                                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
UpgradeMode                             : Auto
```

## <a name="get-cloud-service-instance-view"></a>Ottenere la visualizzazione dell'istanza del servizio cloud
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS" -InstanceView | Format-List

RoleInstanceStatusesSummary : {{
                                "code": "ProvisioningState/succeeded",
                                "count": 4
                              }, {
                                "code": "PowerState/started",
                                "count": 4
                              }}
Statuses                    : {{
                                "code": "ProvisioningState/succeeded",
                                "displayStatus": "Provisioning succeeded",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "PowerState/started",
                                "displayStatus": "Started",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "CurrentUpgradeDomain/-1",
                                "displayStatus": "Current Upgrade Domain of Cloud Service is -1.",
                                "level": "Info"
                              }, {
                                "code": "MaxUpgradeDomain/1",
                                "displayStatus": "Max Upgrade Domain of Cloud Service is 1.",
                                "level": "Info"
                              }}
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui servizi cloud di Azure (supporto esteso), vedere [Panoramica di servizi cloud di Azure (supporto esteso)](overview.md).