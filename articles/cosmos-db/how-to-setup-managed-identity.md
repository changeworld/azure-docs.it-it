---
title: Configurare identità gestite con Azure AD per l'account Azure Cosmos DB
description: Informazioni su come configurare le identità gestite con Azure Active Directory per l'account Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231498"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Configurare identità gestite con Azure Active Directory per l'account Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Questo articolo illustra come creare un'identità gestita per gli account Azure Cosmos DB.

> [!NOTE]
> Solo le identità gestite assegnate dal sistema sono attualmente supportate da Azure Cosmos DB.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Per informazioni sui tipi di identità gestiti, vedere [tipi di identità gestiti](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Per configurare le identità gestite, l'account deve avere il ruolo di [collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistema

### <a name="using-an-azure-resource-manager-arm-template"></a>Uso di un modello di Azure Resource Manager (ARM)

> [!IMPORTANT]
> Assicurarsi di usare un `apiVersion` valore di `2021-03-15` o versione successiva quando si utilizzano identità gestite.

Per abilitare un'identità assegnata dal sistema a un account Azure Cosmos DB nuovo o esistente, includere la seguente proprietà nella definizione della risorsa:

```json
"identity": {
    "type": "SystemAssigned"
}
```

La `resources` sezione del modello ARM dovrebbe essere simile alla seguente:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Dopo la creazione o l'aggiornamento dell'account Azure Cosmos DB, verrà visualizzata la proprietà seguente:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Scopri di più sulle [chiavi gestite dal cliente in Azure Cosmos DB](how-to-setup-cmk.md)
