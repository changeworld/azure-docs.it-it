---
title: Spostare un'Service Fabric Mesh in un'altra area
description: È possibile spostare Service Fabric mesh distribuendo una copia del modello corrente in una nuova area di Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: bce61a00ae1b6b451927b43dbcf19ddb615f79a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861176"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Spostare un'applicazione Service Fabric Mesh in un'altra area di Azure

> [!IMPORTANT]
> L'anteprima Azure Service Fabric Mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric Mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere Azure Service Fabric Mesh [ritiro dell'anteprima.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Questo articolo descrive come spostare l'applicazione Service Fabric Mesh e le relative risorse in un'area di Azure diversa. È possibile spostare le risorse in un'altra area per diversi motivi. Ad esempio, in risposta alle interruzioni, per ottenere funzionalità o servizi disponibili solo in aree specifiche, per soddisfare i requisiti interni di criteri e governance o in risposta ai requisiti di pianificazione della capacità.

 [Service Fabric Mesh non supporta la possibilità](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) di spostare direttamente le risorse tra aree di Azure. Tuttavia, è possibile spostare le risorse indirettamente distribuendo una copia del modello Azure Resource Manager corrente nella nuova area di destinazione e quindi reindirizzando il traffico in ingresso e le dipendenze all'applicazione mesh Service Fabric appena creata.

## <a name="prerequisites"></a>Prerequisiti

* Controller di ingresso (ad esempio [il gateway](../application-gateway/index.yml)applicazione ) da fungere da intermediario per il routing del traffico tra i client e l'applicazione Service Fabric Mesh
* Service Fabric mesh (anteprima) nell'area di Azure di destinazione ( `westus` `eastus` , o `westeurope` )

## <a name="prepare"></a>Preparare

1. Creare uno "snapshot" dello stato corrente dell'applicazione Service Fabric Mesh esportando il modello Azure Resource Manager e i parametri dalla distribuzione più recente. A tale scopo, seguire la procedura descritta in [Esportare il modello dopo la](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) distribuzione usando il portale di Azure. È anche possibile usare l'interfaccia della riga di comando di [Azure,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o [l'API REST.](/rest/api/resources/resourcegroups/exporttemplate)

2. Se applicabile, [esportare altre risorse nello stesso gruppo di risorse per](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) la ridistribuzione nell'area di destinazione.

3. Esaminare (e modificare, se necessario) il modello esportato per assicurarsi che i valori delle proprietà esistenti siano quelli che si vuole usare nell'area di destinazione. La nuova `location` (area di Azure) è un parametro che verrà fornito durante la ridistribuzione.

## <a name="move"></a>Sposta

1. Creare un nuovo gruppo di risorse (o usarne uno esistente) nell'area di destinazione.

2. Con il modello esportato, seguire la procedura descritta in Distribuire risorse [dal modello personalizzato](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) usando il portale di Azure. È anche possibile usare l'interfaccia della riga di comando di [Azure,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)o [l'API REST.](../azure-resource-manager/templates/deploy-rest.md)

3. Per indicazioni sullo spostamento di risorse correlate, ad esempio [Archiviazione di Azure,](../storage/common/storage-account-move.md)vedere le linee guida per i singoli servizi elencati nell'argomento Spostamento di risorse [di Azure tra aree.](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)

## <a name="verify"></a>Verifica

1. Al termine della distribuzione, testare gli endpoint dell'applicazione per verificare la funzionalità dell'applicazione.

2. È anche possibile verificare lo stato dell'applicazione controllando lo stato dell'applicazione ([az mesh app show](/cli/azure/mesh/app#az_mesh_app_show)) ed esaminando i log dell'applicazione e i comandi ([az mesh code-package-log](/cli/azure/mesh/code-package-log)) usando l'interfaccia della riga di comando [di Azure Service Fabric Mesh](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Commit

Dopo aver confermato la funzionalità equivalente dell'applicazione Service Fabric Mesh nell'area di destinazione, configurare il controller di ingresso ( ad [esempio,](../application-gateway/redirect-overview.md)Gateway applicazione ) per reindirizzare il traffico alla nuova applicazione.

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Per completare lo spostamento dell'applicazione Service Fabric Mesh, eliminare l'applicazione [di origine e/o il gruppo di risorse padre.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passaggi successivi

* [Spostare le risorse di Azure tra aree](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Supporto per lo spostamento di risorse di Azure tra aree diverse](../azure-resource-manager/management/move-support-resources.md)
* [Spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Supporto delle operazioni di spostamento per le risorse](../azure-resource-manager/management/move-support-resources.md
)
