---
title: Configurare le autorizzazioni per il ripristino di un account Azure Cosmos DB.
description: Informazioni su come isolare e limitare le autorizzazioni di ripristino per l'account di backup continuo a un ruolo specifico o a un'entità. Viene illustrato come assegnare un ruolo predefinito mediante portale di Azure, CLI o definire un ruolo personalizzato.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100648604"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Gestire le autorizzazioni per ripristinare un account di Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB consente di isolare e limitare le autorizzazioni di ripristino per l'account di backup continuo (anteprima) a un ruolo specifico o a un'entità. Il proprietario dell'account può attivare un ripristino e assegnare un ruolo ad altre entità per eseguire l'operazione di ripristino. Queste autorizzazioni possono essere applicate nell'ambito della sottoscrizione o in modo più granulare nell'ambito dell'account di origine, come illustrato nell'immagine seguente:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Elenco dei ruoli necessari per eseguire l'operazione di ripristino." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

L'ambito è un set di risorse con accesso, per altre informazioni sugli ambiti, vedere la documentazione relativa a [RBAC di Azure](../role-based-access-control/scope-overview.md) . In Azure Cosmos DB gli ambiti applicabili sono la sottoscrizione di origine e l'account di database per la maggior parte dei casi d'uso. L'entità che esegue le azioni di ripristino deve disporre delle autorizzazioni di scrittura per il gruppo di risorse di destinazione.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Assegnare i ruoli per il ripristino usando il portale di Azure

Per eseguire un ripristino, un utente o un'entità necessita dell'autorizzazione per il ripristino (ovvero l'autorizzazione di *ripristino/azione* ) e l'autorizzazione per il provisioning di un nuovo account (ovvero l'autorizzazione di *scrittura* ).  Per concedere queste autorizzazioni, il proprietario può assegnare i `CosmosRestoreOperator` `Cosmos DB Operator` ruoli e predefiniti a un'entità.

1. Accedere al [portale di Azure](https://portal.azure.com/)

1. Passare alla sottoscrizione e passare alla scheda **controllo di accesso (IAM)** e selezionare **Aggiungi**  >  **assegnazione ruolo** .

1. Nel riquadro **Aggiungi assegnazione ruolo** selezionare ruolo **CosmosRestoreOperator** per campo **ruolo** . Scegliere **utente, gruppo o entità servizio** per il campo **assegna accesso a** e cercare il nome o l'ID di posta elettronica dell'utente, come illustrato nell'immagine seguente:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Assegnare i ruoli di operatore CosmosRestoreOperator e Cosmos DB." border="true":::

1. Selezionare **Salva** per concedere l'autorizzazione *ripristino/azione* .

1. Ripetere il passaggio 3 con **Cosmos DB ruolo Operatore** per concedere l'autorizzazione di scrittura. Quando si assegna questo ruolo dal portale di Azure, concede l'autorizzazione Restore all'intera sottoscrizione.

## <a name="permission-scopes"></a>Ambiti delle autorizzazioni

|Ambito  |Esempio  |
|---------|---------|
|Subscription | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Risorsa account ripristinabile CosmosDB | /Subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/località/Stati Uniti occidentali/restorableDatabaseAccounts/23e99a35-CD36-4df4-9614-f767a03b9995|

La risorsa dell'account ripristinabile può essere estratta dall'output del `az cosmosdb restorable-database-account list --name <accountname>` comando nell'interfaccia della riga di comando o `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` nel cmdlet in PowerShell. L'attributo Name nell'output rappresenta l'oggetto `instanceID` dell'account ripristinabile. Per altre informazioni, vedere l'articolo [PowerShell](continuous-backup-restore-powershell.md) o l' [interfaccia](continuous-backup-restore-command-line.md) della riga di comando.

## <a name="permissions"></a>Autorizzazioni

Per eseguire le diverse attività relative al ripristino per gli account in modalità di backup continuo, sono necessarie le autorizzazioni seguenti:

|Autorizzazione  |Impatto  |Ambito minimo  |Ambito massimo  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Queste autorizzazioni sono necessarie per la distribuzione del modello ARM per la creazione dell'account ripristinato. Per informazioni sull'impostazione di questo ruolo, vedere l'autorizzazione di esempio [RestorableAction](#custom-restorable-action) di seguito. | Non applicabile | Non applicabile  |
|`Microsoft.DocumentDB/databaseAccounts/write` | Questa autorizzazione è necessaria per ripristinare un account in un gruppo di risorse | Gruppo di risorse in cui viene creato l'account ripristinato. | Sottoscrizione in cui viene creato l'account ripristinato |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Questa autorizzazione è necessaria per l'ambito dell'account del database ripristinabile di origine per consentire l'esecuzione delle azioni di ripristino.  | Risorsa *RestorableDatabaseAccount* che appartiene all'account di origine da ripristinare. Questo valore viene fornito anche dalla `ID` proprietà della risorsa account del database ripristinabile. Un esempio di account ripristinabile è */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>* | Sottoscrizione che contiene l'account di database ripristinabile. Il gruppo di risorse non può essere scelto come ambito.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Questa autorizzazione è necessaria nell'ambito dell'account del database ripristinabile di origine per elencare gli account di database che è possibile ripristinare.  | Risorsa *RestorableDatabaseAccount* che appartiene all'account di origine da ripristinare. Questo valore viene fornito anche dalla `ID` proprietà della risorsa account del database ripristinabile. Un esempio di account ripristinabile è */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Sottoscrizione che contiene l'account di database ripristinabile. Il gruppo di risorse non può essere scelto come ambito.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Questa autorizzazione è necessaria per l'ambito dell'account ripristinabile di origine per consentire la lettura di risorse ripristinabili, ad esempio un elenco di database e contenitori per un account ripristinabile.  | Risorsa *RestorableDatabaseAccount* che appartiene all'account di origine da ripristinare. Questo valore viene fornito anche dalla `ID` proprietà della risorsa account del database ripristinabile. Un esempio di account ripristinabile è */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Sottoscrizione che contiene l'account di database ripristinabile. Il gruppo di risorse non può essere scelto come ambito. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scenari di assegnazione di ruolo CLI di Azure da ripristinare in ambiti diversi

I ruoli con autorizzazione possono essere assegnati a ambiti diversi per ottenere un controllo granulare sugli utenti che possono eseguire l'operazione di ripristino all'interno di una sottoscrizione o di un account specifico.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Assegnare la funzionalità per il ripristino da qualsiasi account ripristinabile in una sottoscrizione

Assegnare il `CosmosRestoreOperator` ruolo predefinito a livello di sottoscrizione

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Assegnare la funzionalità per il ripristino da un account specifico

* Assegnare un'azione di scrittura utente per il gruppo di risorse specifico. Questa azione è necessaria per creare un nuovo account nel gruppo di risorse.

* Assegnare il ruolo predefinito *CosmosRestoreOperator* all'account del database ripristinabile specifico che deve essere ripristinato. Nel comando seguente, l'ambito per *RestorableDatabaseAccount* viene recuperato dalla `ID` proprietà nell'output di (se si usa l'interfaccia della riga di comando `az cosmosdb restorable-database-account` ) o  `Get-AzCosmosDBRestorableDatabaseAccount` (se si usa PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Assegnare la funzionalità per il ripristino da qualsiasi account di origine in un gruppo di risorse.
Questa operazione non è attualmente supportata.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Creazione di un ruolo personalizzato per l'azione di ripristino con CLI

Il proprietario della sottoscrizione può fornire l'autorizzazione per ripristinare qualsiasi altra identità del Azure AD. L'autorizzazione Restore è basata sull'azione: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` e deve essere inclusa nell'autorizzazione Restore. È disponibile un ruolo predefinito denominato *CosmosRestoreOperator* con questo ruolo. È possibile assegnare l'autorizzazione utilizzando questo ruolo predefinito o creare un ruolo personalizzato.

Il RestorableAction riportato di seguito rappresenta un ruolo personalizzato. È necessario creare in modo esplicito questo ruolo. Il modello JSON seguente crea un ruolo personalizzato *RestorableAction* con l'autorizzazione Restore:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Usare quindi il comando di distribuzione del modello seguente per creare un ruolo con l'autorizzazione Restore usando il modello ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)
