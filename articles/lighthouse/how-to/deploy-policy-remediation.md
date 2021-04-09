---
title: Distribuire un criterio che è possibile correggere
description: Per distribuire i criteri che usano un'attività di monitoraggio e aggiornamento tramite Azure Lighthouse, è necessario creare un'identità gestita nel tenant del cliente.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233883"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata

[Azure Lighthouse](../overview.md) consente ai provider di servizi di creare e modificare le definizioni dei criteri all'interno di una sottoscrizione delegata. Tuttavia, per distribuire i criteri che usano un' [attività di monitoraggio e aggiornamento](../../governance/policy/how-to/remediate-resources.md) , ovvero i criteri con l'effetto [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) o [Modify](../../governance/policy/concepts/effects.md#modify) , è necessario creare un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) nel tenant del cliente. Questa identità gestita può essere usata da Criteri di Azure per distribuire il modello all'interno del criterio. Per abilitare questo scenario sono necessari alcuni passaggi, sia quando si esegue l'onboarding del cliente per la gestione delle risorse delegate di Azure, sia quando si distribuisce il criterio stesso.

> [!TIP]
> Sebbene si faccia riferimento ai provider di servizi e ai clienti in questo argomento, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono utilizzare gli stessi processi.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Creare un utente che può assegnare ruoli a un'identità gestita nel tenant del cliente

Quando si carica un cliente in Azure Lighthouse, si usa un [modello di Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) insieme a un file di parametri per definire le autorizzazioni che concedono l'accesso alle risorse delegate nel tenant del cliente. Ogni autorizzazione specifica un **PrincipalId** che corrisponde a un utente Azure ad, un gruppo o un'entità servizio nel tenant di gestione e un **roleDefinitionId** che corrisponde al [ruolo predefinito di Azure](../../role-based-access-control/built-in-roles.md) che verrà concesso.

Per consentire a un **principalId** di creare un'identità gestita nel tenant del cliente, è necessario impostare il relativo **roleDefinitionId** su **Amministratore accessi utente**. Sebbene questo ruolo non sia generalmente supportato, può essere usato in questo scenario specifico, consentendo agli account utente con questa autorizzazione di assegnare uno o più ruoli predefiniti specifici alle identità gestite. Questi ruoli sono definiti nella proprietà **delegatedRoleDefinitionIds** e possono includere qualsiasi [ruolo predefinito di Azure supportato](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) ad eccezione dell'amministratore o del proprietario dell'accesso utente.

Dopo aver eseguito l'onboarding del cliente, il **principalId** creato in questa autorizzazione potrà assegnare questi ruoli predefiniti alle identità gestite nel tenant del cliente. Tuttavia, nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà assegnata a questo cliente.

L'esempio seguente illustra un **principalId** che avrà il ruolo di Amministratore accesso utenti. Questo utente sarà in grado di assegnare due ruoli predefiniti alle identità gestite nel tenant del cliente: collaboratore e collaboratore Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Distribuire criteri che è possibile correggere

Dopo aver creato l'utente con le autorizzazioni necessarie, come descritto in precedenza, l'utente può distribuire i criteri che usano le attività di correzione nelle sottoscrizioni dei clienti Delegate.

Si immagini, ad esempio, di voler abilitare la diagnostica sulle risorse Azure Key Vault nel tenant del cliente, come illustrato in questo [esempio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Un utente nel tenant di gestione con le autorizzazioni appropriate (come descritto in precedenza) distribuirà un [modello di Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) per abilitare questo scenario.

Si noti che la creazione dell'assegnazione di criteri da usare con una sottoscrizione delegata deve essere eseguita tramite le API, non nel portale di Azure. Quando si esegue questa operazione, è necessario impostare **apiVersion** su **2019-04-01-Preview**, che include la nuova proprietà **delegatedManagedIdentityResourceId**. Questa proprietà consente di includere un'identità gestita che risiede nel tenant del cliente (in una sottoscrizione o in un gruppo di risorse che è stato caricato nel Faro di Azure).

L'esempio seguente illustra un'assegnazione di ruolo con un **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> È disponibile un [esempio simile](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) che dimostra come distribuire un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Criteri di Azure](../../governance/policy/index.yml).
- Informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).
