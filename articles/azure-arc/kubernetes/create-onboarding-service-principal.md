---
title: Creare un'entità servizio di onboarding per Azure Arc Enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: "Creare un'entità servizio di onboarding abilitata per Azure Arc "
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: bda088bdae5c866493718db94c9a2da89cada8c9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650347"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Creare un'entità servizio di onboarding per Azure Arc Enabled Kubernetes

## <a name="overview"></a>Panoramica

È possibile connettere i cluster Kubernetes ad Azure ARC usando entità servizio con assegnazioni di ruolo con privilegi limitati. Questa funzionalità è utile nelle pipeline di integrazione continua e distribuzione continua (CI/CD), come le azioni Azure Pipelines e GitHub.

Esaminare i passaggi seguenti per informazioni su come usare le entità servizio per la connessione di cluster Kubernetes ad Azure Arc.

## <a name="create-a-new-service-principal"></a>Creare una nuova entità servizio

Creare una nuova entità servizio con un nome informativo univoco per il tenant Azure Active Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Output:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Assegnare le autorizzazioni

Assegnare il ruolo "cluster Kubernetes-Azure Arc onboarding" all'entità servizio appena creata. Questo ruolo predefinito di Azure con autorizzazioni limitate consente solo all'entità di registrare i cluster in Azure. L'entità con questo ruolo assegnato non può aggiornare, eliminare o modificare altri cluster o risorse all'interno della sottoscrizione.

Considerate le capacità limitate, i clienti possono riutilizzare facilmente questa entità per l'onboarding di più cluster.

È possibile limitare ulteriormente le autorizzazioni passando l'argomento appropriato `--scope` durante l'assegnazione del ruolo. Ciò consente agli amministratori di limitare la registrazione del cluster alla sottoscrizione o all'ambito del gruppo di risorse. Gli scenari seguenti sono supportati dai diversi parametri di `--scope`:

| Risorsa  | Argomento di `scope`| Effetto |
| ------------- | ------------- | ------------- |
| Sottoscrizione | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | L'entità servizio può registrare il cluster in qualsiasi gruppo di risorse in tale sottoscrizione. |
| Gruppo di risorse | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | L'entità servizio può registrare __solo__ cluster nel gruppo di risorse `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Output:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Usare entità servizio con l'interfaccia della riga di comando di Azure

Fare riferimento all'entità servizio appena creata con i comandi seguenti:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Passaggi successivi

* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
