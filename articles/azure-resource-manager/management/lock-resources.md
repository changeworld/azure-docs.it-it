---
title: Bloccare le risorse per impedire modifiche
description: Impedire agli utenti di aggiornare o eliminare le risorse di Azure applicando un blocco per tutti gli utenti e i ruoli.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768724"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloccare le risorse per impedire modifiche impreviste

Gli amministratori possono bloccare una sottoscrizione, un gruppo di risorse o una risorsa per impedire ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche. Il blocco esegue l'override di tutte le autorizzazioni che l'utente potrebbe avere.

È possibile impostare il livello di blocco **CanNotDelete** o **ReadOnly**. Nel portale i blocchi sono definiti rispettivamente **Elimina** e **Sola lettura**.

* **CanNotDelete** significa che gli utenti autorizzati possono leggere e modificare una risorsa, ma non eliminarla.
* **ReadOnly** significa che gli utenti autorizzati possono leggere una risorsa, ma non eliminarla o aggiornarla. L'applicazione di questo blocco è simile alla concessione a tutti gli utenti autorizzati solo le autorizzazioni concesse dal ruolo **Lettore**.

## <a name="how-locks-are-applied"></a>Come vengono applicati i blocchi

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere Controllo degli accessi in base [al ruolo di Azure.](../../role-based-access-control/role-assignments-portal.md)

I blocchi di Resource Manager si applicano solo alle operazioni che si verificano nel piano di gestione, costituito da operazioni inviate a `https://management.azure.com`. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Ad esempio, un blocco ReadOnly su un server logico di database SQL impedisce l'eliminazione o la modifica del server. Non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nei database in tale server. Le transazioni di dati sono consentite in quanto tali operazioni non vengono inviate a `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Considerazioni prima di applicare i blocchi

L'applicazione di blocchi può causare risultati imprevisti perché alcune operazioni che non sembrano modificare la risorsa richiedono effettivamente azioni bloccate dal blocco. I blocchi impediranno qualsiasi operazione che richiede una richiesta POST all'API Azure Resource Manager richiesta. Alcuni esempi comuni delle operazioni bloccate dai blocchi sono:

* Un blocco di sola lettura su un **account di archiviazione impedisce** agli utenti di elencare le chiavi dell'account. [L Archiviazione di Azure'operazione List Keys](/rest/api/storagerp/storageaccounts/listkeys) viene gestita tramite una richiesta POST per proteggere l'accesso alle chiavi dell'account, che forniscono l'accesso completo ai dati nell'account di archiviazione. Quando viene configurato un blocco di sola lettura per un account di archiviazione, gli utenti che non hanno le chiavi dell'account devono usare le credenziali Azure AD per accedere ai dati blob o di coda. Un blocco di sola lettura impedisce anche l'assegnazione dei ruoli del controllo degli accessi in base al ruolo di Azure con ambito all'account di archiviazione o a un contenitore di dati (contenitore BLOB o coda).

* Un blocco di non eliminazione su un **account di archiviazione** non impedisce l'eliminazione o la modifica dei dati all'interno di tale account. Questo tipo di blocco protegge solo l'account di archiviazione stesso dall'eliminazione e non protegge i dati di BLOB, code, tabelle o file all'interno di tale account di archiviazione. 

* Un blocco di sola lettura su un **account di archiviazione** non impedisce l'eliminazione o la modifica dei dati all'interno di tale account. Questo tipo di blocco protegge solo l'account di archiviazione stesso dall'eliminazione o dalla modifica e non protegge i dati di BLOB, code, tabelle o file all'interno di tale account di archiviazione. 

* Un blocco di sola lettura applicato a una risorsa **Servizio app** impedisce a Visual Studio Server Explorer di visualizzare i file della risorsa perché questa interazione richiede l'accesso in scrittura.

* Un blocco di sola lettura su un gruppo di **risorse** che contiene un piano di servizio **app** impedisce di aumentare o ridurre [il piano](../../app-service/manage-scale-up.md).

* Un blocco di sola lettura applicato a un **gruppo di risorse** che contiene una **macchina virtuale** impedisce a tutti gli utenti di avviare o riavviare la macchina virtuale. Queste operazioni richiedono una richiesta POST.

* Un blocco cannot-delete su un gruppo **di** risorse impedisce Azure Resource Manager l'eliminazione [automatica delle distribuzioni](../templates/deployment-history-deletions.md) nella cronologia. Se si raggiungono 800 distribuzioni nella cronologia, le distribuzioni avranno esito negativo.

* Un blocco cannot-delete applicato al **​​gruppo di risorse** creato dal **servizio Backup di Azure** causa l'esito negativo dei backup. Il servizio supporta un massimo di 18 punti di ripristino. Quando è bloccato, il servizio di backup non riesce a eseguire la pulizia dei punti di ripristino. Per altre informazioni, vedere [Domande frequenti-Eseguire il backup delle VM di Azure](../../backup/backup-azure-vm-backup-faq.yml).

* Un blocco di sola lettura applicato a una **sottoscrizione** impedisce ad **Azure Advisor** di funzionare correttamente. Advisor non riesce ad archiviare i risultati delle relative query.

## <a name="who-can-create-or-delete-locks"></a>Utenti che possono creare o eliminare blocchi

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.

## <a name="managed-applications-and-locks"></a>Applicazioni gestite e blocchi

Alcuni servizi di Azure, ad esempio Azure Databricks, usano le [applicazioni gestite](../managed-applications/overview.md) per implementare il servizio. In tal caso, il servizio crea due gruppi di risorse. Un gruppo di risorse contiene una panoramica del servizio e non è bloccato. L'altro gruppo di risorse contiene l'infrastruttura del servizio ed è bloccato.

Se si prova a eliminare il gruppo di risorse dell'infrastruttura, viene visualizzato un errore che informa che il gruppo di risorse è bloccato. Se si prova a eliminare il blocco per il gruppo di risorse dell'infrastruttura, viene visualizzato un errore che informa che il blocco non può essere eliminato perché è di proprietà di un'applicazione di sistema.

Eliminare invece il servizio, che elimina anche il gruppo di risorse dell'infrastruttura.

Per le applicazioni gestite selezionare il servizio distribuito.

![Selezionare un servizio](./media/lock-resources/select-service.png)

Si noti che il servizio include un collegamento per un **gruppo di risorse gestito**. Il gruppo di risorse contiene l'infrastruttura ed è bloccato. Non può essere eliminato direttamente.

![Mostrare un gruppo gestito](./media/lock-resources/show-managed-group.png)

Per eliminare tutti gli elementi per il servizio, incluso il gruppo di risorse dell'infrastruttura bloccato, selezionare **Elimina** per il servizio.

![Delete service](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Configurare i blocchi

### <a name="portal"></a>Portale

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>Modello ARM

Quando si usa un modello Azure Resource Manager (modello arm) per distribuire un blocco, è necessario conoscere l'ambito del blocco e l'ambito della distribuzione. Per applicare un blocco nell'ambito della distribuzione, ad esempio il blocco di un gruppo di risorse o di una sottoscrizione, non impostare la proprietà scope. Quando si blocca una risorsa all'interno dell'ambito di distribuzione, impostare la proprietà scope.

Il modello seguente applica un blocco al gruppo di risorse in cui è distribuito. Si noti che non è presente una proprietà di ambito nella risorsa di blocco perché l'ambito del blocco corrisponde all'ambito della distribuzione. Questo modello viene distribuito a livello di gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Per creare un gruppo di risorse e bloccarlo, distribuire il modello seguente a livello di sottoscrizione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Quando si applica un blocco a una **risorsa all'interno** del gruppo di risorse, aggiungere la proprietà scope. Impostare scope sul nome della risorsa da bloccare.

L'esempio seguente illustra un modello che crea un piano di servizio app, un sito Web e un blocco sul sito Web. L'ambito del blocco è impostato sul sito Web.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Per bloccare le risorse distribuite con Azure PowerShell, usare il comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Per ottenere tutti i blocchi nella sottoscrizione, usare:

```azurepowershell-interactive
Get-AzResourceLock
```

Per ottenere tutti i blocchi per una risorsa, usare:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per ottenere tutti i blocchi per un gruppo di risorse, usare:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Per eliminare un blocco per una risorsa, usare:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Per eliminare un blocco per un gruppo di risorse, usare:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per bloccare le risorse distribuite con l'interfaccia della riga di comando di Azure, usare il comando [az lock create](/cli/azure/lock#az_lock_create).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [az lock list](/cli/azure/lock#az_lock_list). Per ottenere tutti i blocchi nella sottoscrizione, usare:

```azurecli
az lock list
```

Per ottenere tutti i blocchi per una risorsa, usare:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Per ottenere tutti i blocchi per un gruppo di risorse, usare:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Per eliminare un blocco per una risorsa, usare:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Per eliminare un blocco per un gruppo di risorse, usare:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>API REST

È possibile bloccare le risorse distribuite tramite l'[API REST per i blocchi di gestione](/rest/api/resources/managementlocks). L'API REST consente di creare ed eliminare i blocchi e recuperare informazioni sui blocchi esistenti.

Per creare un blocco, eseguire:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Lock-name indica il nome che si desidera assegnare al blocco. Per api-version, usare **2016-09-01**.

Nella richiesta includere un oggetto JSON che specifica le proprietà per il blocco.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](tag-resources.md).
* È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Per altre informazioni, vedere [Informazioni su Criteri di Azure](../../governance/policy/overview.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).
