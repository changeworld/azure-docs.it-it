---
title: Conformità con Criteri di Azure
description: Assegnare criteri predefiniti in Criteri di Azure per controllare la conformità dei registri di Azure Container
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784174"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Controllare la conformità dei registri contenitori di Azure usando Criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio in Azure che consente di creare, assegnare e gestire i criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda.

Questo articolo presenta i criteri predefiniti per Registro Azure Container. Usare questi criteri per controllare la conformità dei registri nuovi ed esistenti.

Non sono disponibili addebiti per l'Criteri di Azure.

## <a name="built-in-policy-definitions"></a>Definizioni di criteri predefiniti

Le definizioni dei criteri predefiniti seguenti sono specifiche per Registro Azure Container:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Assegnare criteri

* Assegnare i criteri [usando portale di Azure](../governance/policy/assign-policy-portal.md), l'interfaccia della riga di comando di [Azure,](../governance/policy/assign-policy-azurecli.md) [un modello Resource Manager](../governance/policy/assign-policy-template.md)o Criteri di Azure SDK.
* Ambito di un'assegnazione di criteri a un gruppo di risorse, a una sottoscrizione o a un gruppo [di gestione di Azure.](../governance/management-groups/overview.md) Le assegnazioni dei criteri del Registro Contenitori si applicano ai registri contenitori nuovi e esistenti all'interno dell'ambito.
* Abilitare o disabilitare [l'imposizione](../governance/policy/concepts/assignment-structure.md#enforcement-mode) dei criteri in qualsiasi momento.

> [!NOTE]
> Dopo l'assegnazione o l'aggiornamento di un criterio, l'assegnazione deve essere applicata alle risorse nell'ambito definito. Vedere informazioni sui [trigger di valutazione dei criteri](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Verificare la conformità dei criteri

Accedere alle informazioni di conformità generate dalle assegnazioni di criteri usando portale di Azure, gli strumenti da riga di comando di Azure o gli SDK Criteri di Azure sicurezza. Per informazioni dettagliate, vedere [Ottenere i dati di conformità delle risorse di Azure.](../governance/policy/how-to/get-compliance-data.md)

Quando una risorsa non è conforme, i motivi possibili sono molti. Per determinare il motivo o trovare la modifica responsabile, vedere [Determinare la non conformità.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Conformità dei criteri nel portale:

1. Selezionare **Tutti i** servizi e cercare **Criteri.**
1. Selezionare **Conformità.**
1. Usare i filtri per limitare gli stati di conformità o per cercare i criteri.

    ![Conformità dei criteri nel portale](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Selezionare un criterio per esaminare gli eventi e i dettagli di conformità aggregati. Se lo si desidera, selezionare un registro specifico per la conformità delle risorse.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformità dei criteri nell'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure per ottenere i dati di conformità. Ad esempio, usare il [comando az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) nell'interfaccia della riga di comando per ottenere gli ID dei criteri Registro Azure Container criteri applicati:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Output di esempio:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Eseguire quindi [az policy state list](/cli/azure/policy/state#az_policy_state_list) per restituire lo stato di conformità in formato JSON per tutte le risorse con un ID criterio specifico:

```azurecli
az policy state list \
  --resource <policyID>
```

In caso contrario, [eseguire az policy state list](/cli/azure/policy/state#az_policy_state_list) per restituire lo stato di conformità in formato JSON di una risorsa registro specifica, ad esempio *myregistry:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle definizioni Criteri di Azure [e](../governance/policy/concepts/definition-structure.md) sugli [effetti.](../governance/policy/concepts/effects.md)

* Creare una [definizione di criteri personalizzata.](../governance/policy/tutorials/create-custom-policy-definition.md)

* Altre informazioni sulle funzionalità [di governance](../governance/index.yml) in Azure.
