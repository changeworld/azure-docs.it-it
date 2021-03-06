---
title: Dati personali
description: Informazioni su come gestire i dati personali associati alle operazioni di Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 9087d3e46f38aab3de7774ea341ebd9cbc2d7d1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785956"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gestire i dati personali associati a Azure Resource Manager

Per evitare di esporre informazioni riservate, eliminare eventuali informazioni personali fornite in distribuzioni, gruppi di risorse o tag. In Azure Resource Manager è possibile eseguire operazioni che consentono di gestire i dati personali eventualmente forniti in distribuzioni, gruppi di risorse o tag.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Eliminare i dati personali nella cronologia di distribuzione

Per le distribuzioni, Resource Manager conserva i valori dei parametri e i messaggi di stato nella cronologia di distribuzione. Questi valori sono salvati in modo permanente finché non si elimina la distribuzione dalla cronologia. Per vedere se sono stati forniti dati personali in questi valori, elencare le distribuzioni. Se si trovano dati personali, eliminare le distribuzioni dalla cronologia.

Per elencare le **distribuzioni** nella cronologia, usare:

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

Per eliminare le **distribuzioni** dalla cronologia, usare:

* [Elimina](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Eliminare i dati personali nei nomi dei gruppi di risorse

Il nome del gruppo di risorse è salvato in modo permanente finché non si elimina il gruppo di risorse. Per vedere se sono stati forniti dati personali nei nomi, elencare i gruppi di risorse. Se si trovano dati personali, [spostare le risorse](move-resource-group-and-subscription.md) in un nuovo gruppo di risorse ed eliminare il gruppo di risorse con i dati personali nel nome.

Per elencare i **gruppi di risorse**, usare:

* [Elenco](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az_group_list)

Per eliminare i **gruppi di risorse**, usare:

* [Elimina](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az_group_delete)

## <a name="delete-personal-data-in-tags"></a>Eliminare i dati personali nei tag

I nomi e i valori dei tag sono salvati in modo permanente finché non si elimina o si modifica il tag. Per vedere se sono stati forniti dati personali nei tag, elencare i tag. Se si trovano dati personali, eliminare i tag.

Per elencare i **tag**, usare:

* [Elenco](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az_tag_list)

Per eliminare i **tag**, usare:

* [Elimina](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az_tag_delete)

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica di Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](overview.md).