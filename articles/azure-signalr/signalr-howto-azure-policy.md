---
title: Conformità con Criteri di Azure
description: Assegnare criteri predefiniti in Criteri di Azure per controllare la conformità delle Servizio Azure SignalR risorse.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784574"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Controllare la conformità Servizio Azure SignalR risorse usando Criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che consente di creare, assegnare e gestire criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda.

Questo articolo presenta i criteri predefiniti (anteprima) per Servizio Azure SignalR. Usare questi criteri per controllare la conformità delle risorse SignalR nuove ed esistenti.

Non sono presenti addebiti per l'Criteri di Azure.

## <a name="built-in-policy-definitions"></a>Definizioni di criteri predefiniti

Le definizioni di criteri predefiniti seguenti sono specifiche per Servizio Azure SignalR:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Assegnare definizioni di criteri

* Assegnare le definizioni dei [criteri portale di Azure](../governance/policy/assign-policy-portal.md), l'interfaccia della riga di comando di [Azure,](../governance/policy/assign-policy-azurecli.md) [un modello Resource Manager](../governance/policy/assign-policy-template.md)o Criteri di Azure SDK.
* Ambito di un'assegnazione di criteri a un gruppo di risorse, a una sottoscrizione o a un gruppo [di gestione di Azure.](../governance/management-groups/overview.md) Le assegnazioni dei criteri di SignalR si applicano alle risorse SignalR nuove e esistenti all'interno dell'ambito.
* Abilitare o disabilitare [l'imposizione](../governance/policy/concepts/assignment-structure.md#enforcement-mode) dei criteri in qualsiasi momento.

> [!NOTE]
> Dopo l'assegnazione o l'aggiornamento di un criterio, l'applicazione dell'assegnazione alle risorse nell'ambito definito richiede tempo. Vedere le informazioni sui [trigger di valutazione dei criteri.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Verificare la conformità ai criteri

Accedere alle informazioni di conformità generate dalle assegnazioni dei criteri usando gli portale di Azure, gli strumenti da riga di comando di Azure o gli CRITERI DI AZURE sdk. Per informazioni dettagliate, vedere [Ottenere i dati di conformità delle risorse di Azure.](../governance/policy/how-to/get-compliance-data.md)

Quando una risorsa non è conforme, i motivi possibili sono molti. Per determinare il motivo o trovare la modifica responsabile, vedere [Determinare la mancata conformità.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Conformità dei criteri nel portale:

1. Selezionare **Tutti i servizi** e cercare **Criteri**.
1. Selezionare **Conformità**.
1. Usare i filtri per limitare gli stati di conformità o per cercare i criteri
   
    [![Conformità dei criteri nel portale ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selezionare un criterio per esaminare i dettagli e gli eventi di conformità aggregati. Se lo si desidera, selezionare un SignalR specifico per la conformità delle risorse.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformità dei criteri nell'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure per ottenere i dati di conformità. Ad esempio, usare il [comando az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) nell'interfaccia della riga di comando per ottenere gli ID dei criteri Servizio Azure SignalR criteri applicati:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Output di esempio:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Eseguire quindi [az policy state list](/cli/azure/policy/state#az_policy_state_list) per restituire lo stato di conformità in formato JSON per tutte le risorse in un gruppo di risorse specifico:

```azurecli
az policy state list --g <resourceGroup>
```

Oppure eseguire [az policy state list](/cli/azure/policy/state#az_policy_state_list) per restituire lo stato di conformità in formato JSON di una risorsa SignalR specifica:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle definizioni [](../governance/policy/concepts/effects.md) Criteri di Azure [effetti](../governance/policy/concepts/definition-structure.md)

* Creare una [definizione di criteri personalizzata](../governance/policy/tutorials/create-custom-policy-definition.md)

* Altre informazioni sulle funzionalità [di governance](../governance/index.yml) in Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/