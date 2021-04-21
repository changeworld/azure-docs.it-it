---
title: Servizi di Azure che supportano le identità gestite - Azure AD
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771480"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:


### <a name="azure-api-management"></a>Gestione API di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Anteprima | Anteprima | Non disponibile | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per Gestione API di Azure (nelle aree in cui è disponibile):

- [Modello di Azure Resource Manager](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Configurazione app di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | Non disponibile  | ![Disponibile][check] |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Configurazione app di Azure (nelle aree in cui è disponibile):

- [Interfaccia della riga di comando di Azure](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Servizio app di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check] |

Vedere l'elenco seguente per configurare l'identità gestita per il Servizio app di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modello di Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes con abilitazione di Azure Arc

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Azure Arc Kubernetes abilitato supporta [attualmente l'identità assegnata dal sistema.](../../azure-arc/kubernetes/quickstart-connect-cluster.md) Il certificato dell'identità del servizio gestita viene usato da Azure Arc agenti Kubernetes abilitati per la comunicazione con Azure.

### <a name="azure-arc-enabled-servers"></a>Server con abilitazione di Azure Arc

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Tutti Azure Arc server abilitati hanno un'identità assegnata dal sistema. Non è possibile disabilitare o modificare l'identità assegnata dal sistema in un server Azure Arc abilitato. Fare riferimento alle risorse seguenti per altre informazioni su come usare le identità gestite nei Azure Arc abilitati:

- [Eseguire l'autenticazione con le risorse di Azure con server abilitati per Arc](../../azure-arc/servers/managed-identity-authentication.md)
- [Uso di un'identità gestita con server abilitati per Arc](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento al documento seguente per riconfigurare un'identità gestita se la sottoscrizione è stata spostata in un nuovo tenant:
* [Ripristinare un account di gestione automatica interrotto](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprint

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per usare un'identità gestita con [Azure Blueprints](../../governance/blueprints/overview.md):

- [Portale di Azure - assegnazione del progetto](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST - assegnazione del progetto](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-cognitive-services"></a>Servizi cognitivi di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |


### <a name="azure-communication-services"></a>Servizi di comunicazione di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |


### <a name="azure-container-instances"></a>Istanze di Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Linux: Anteprima<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Linux: Anteprima<br>Windows: Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Istanze di Azure Container (nelle aree in cui è disponibile):

- [Interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Attività del Registro Azure Container, nelle aree in cui è disponibile:

- [Interfaccia della riga di comando di Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Esplora dati di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Gemelli digitali di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Gemelli digitali di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Griglia di eventi di Azure

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Anteprima | Non disponibile | Anteprima |
| Assegnata dall'utente | Non disponibile | Non disponibile  | Non disponibile  | Non disponibile |

### <a name="azure-firewall-policy"></a>Firewall di Azure criteri

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile  | Non disponibile  | Non disponibile |

### <a name="azure-functions"></a>Funzioni di Azure

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check]  |

Vedere l'elenco seguente per configurare l'identità gestita per Funzioni di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Modello di Azure Resource Manager](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Hub IoT Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per hub IoT di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Importazione/Esportazione di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile nell'area in cui è disponibile il servizio Importazione/Esportazione di Azure | Anteprima | Disponibile | Disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile | Non disponibile | Non disponibile |


Per altre informazioni, vedere [Usare le identità gestite nel servizio Azure Kubernetes](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Cluster di Azure Log Analytics

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |

Per altre informazioni, vedere [funzionamento dell'identità in Monitoraggio di Azure](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>App per la logica di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |


Vedere l'elenco seguente per configurare l'identità gestita per le App per la logica di Azure (nelle aree in cui è disponibile):

- [Azure portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Modello di Azure Resource Manager](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile | Non disponibile | Non disponibile |

Per altre informazioni, vedere [Usare le identità gestite con Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Criteri di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Criteri di Azure (nelle aree in cui è disponibile):

- [Portale di Azure](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Interfaccia della riga di comando di Azure](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Modelli di Gestione risorse di Azure](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Managed Identity for Service Fabric Applications è](../../service-fabric/concepts-managed-identity.md) disponibile in tutte le aree.

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | Non disponibile | Non disponibile |Non disponibile |

Fare riferimento all'elenco seguente per configurare l'identità gestita per le applicazioni di Service Fabric di Azure in tutte le regioni:

- [Modello di Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |


Per altre informazioni, vedere [How to enable system-assigned managed identity for Azure Spring Cloud application (Come](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)abilitare l'identità gestita assegnata dal sistema per Azure Spring Cloud'applicazione).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | --- | --- | --- | --- |
| Assegnata dal sistema | Disponibile nell'area in cui Azure Stack Edge è disponibile il servizio | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

### <a name="azure-virtual-machine-scale-sets"></a>Set di scalabilità delle macchine virtuali di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |

Vedere l'elenco seguente per configurare l'identità gestita per il set di scalabilità di macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |

Vedere l'elenco seguente per configurare l'identità gestita per le macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Image Builder per macchine virtuali di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | [Disponibile nelle aree supportate](../../virtual-machines/image-builder-overview.md#regions) | Non disponibile | Non disponibile | Non disponibile |

Per informazioni su come configurare l'identità gestita per Image Builder per macchine virtuali di Azure (nelle aree in cui è disponibile), vedere la Image Builder [panoramica.](../../virtual-machines/image-builder-overview.md#permissions)
### <a name="azure-signalr-service"></a>Servizio Azure SignalR

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Anteprima | Anteprima | Non disponibile | Anteprima |
| Assegnata dall'utente | Anteprima | Anteprima | Non disponibile | Anteprima |

Fare riferimento all'elenco seguente per configurare l'identità gestita per Servizio Azure SignalR (nelle aree in cui sono disponibili):

- [Modello di Azure Resource Manager](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Spostamento risorse di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Disponibile nelle aree in cui Spostamento risorse di Azure è disponibile il servizio | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Fare riferimento al documento seguente per usare Spostamento risorse di Azure:

- [Spostamento risorse di Azure](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Fare riferimento all'elenco seguente per configurare l'accesso ad Azure Resource Manager:

- [Assegnare l'accesso tramite il portale di Azure](howto-assign-access-portal.md)
- [Assegnare l'accesso tramite PowerShell](howto-assign-access-powershell.md)
- [Assegnare l'accesso tramite l'interfaccia della riga di comando di Azure](howto-assign-access-CLI.md)
- [Assegnare l'accesso tramite il modello di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponibile][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://management.microsoftazure.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://management.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponibile][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania |  `https://vault.microsoftazure.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://vault.azure.cn` | ![Disponibile][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-sql"></a>SQL di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponibile][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://database.cloudapi.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://database.chinacloudapi.cn/` | ![Disponibile][check] |

### <a name="azure-data-explorer"></a>Esplora dati di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Disponibile][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://<account>.<region>.kusto.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-event-hubs"></a>Hub eventi di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-service-bus"></a>Bus di servizio di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponibile][check] |
| Azure Government |  | ![Disponibile][check] |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |









### <a name="azure-storage-blobs-and-queues"></a>BLOB e code di Archiviazione di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponibile][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponibile][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://*.asazure.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://*.asazure.chinacloudapi.cn` | ![Disponibile][check] |

> [!Note]
> Microsoft Power BI [supporta anche le identità gestite](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Disponibile"
