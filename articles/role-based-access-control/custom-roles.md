---
title: Ruoli personalizzati di Azure - Controllo degli accessi in base al ruolo di Azure
description: Informazioni su come creare ruoli personalizzati di Azure con il controllo degli accessi in base al ruolo di Azure per la gestione degli accessi con granularità fine delle risorse di Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479832"
---
# <a name="azure-custom-roles"></a>Ruoli personalizzati di Azure

> [!IMPORTANT]
> L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se i [ruoli predefiniti](built-in-roles.md) di Azure non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio negli ambiti del gruppo di gestione (solo in anteprima), della sottoscrizione e del gruppo di risorse.

I ruoli personalizzati possono essere condivisi tra sottoscrizioni che considera attendibili lo stesso Azure AD directory. È previsto un limite di **5.000 ruoli** personalizzati per ogni directory. Ad Azure Germania e Azure China (21Vianet), il limite è di 2.000 ruoli personalizzati. I ruoli personalizzati possono essere creati usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="steps-to-create-a-custom-role"></a>Passaggi per la creazione di un ruolo personalizzato

Ecco i passaggi di base per creare un ruolo personalizzato.

1. Determinare le autorizzazioni necessarie.

    Quando si crea un ruolo personalizzato, è necessario conoscere le operazioni disponibili per definire le autorizzazioni. In genere si parte da un ruolo predefinito esistente e lo si modifica in base alle esigenze. Si aggiungeranno le operazioni alle `Actions` proprietà o della definizione del `NotActions` [ruolo](role-definitions.md). Se si dispone di operazioni sui dati, è necessario aggiungerle alle `DataActions` proprietà `NotDataActions` o .

    Per altre informazioni, vedere la sezione successiva [Come determinare le autorizzazioni necessarie.](#how-to-determine-the-permissions-you-need)

1. Decidere come si vuole creare il ruolo personalizzato.

    È possibile creare ruoli personalizzati usando [portale di Azure](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), l'interfaccia della riga di comando di [Azure](custom-roles-cli.md)o [l'API REST](custom-roles-rest.md).

1. Creare il ruolo personalizzato.

    Il modo più semplice è usare il portale di Azure. Per i passaggi su come creare un ruolo personalizzato usando il portale di Azure, vedere Creare o aggiornare ruoli personalizzati di [Azure usando](custom-roles-portal.md)il portale di Azure .

1. Testare il ruolo personalizzato.

    Una volta creato il ruolo personalizzato, è necessario testarlo per verificare che funzioni nel modo previsto. Se occorre apportare delle modifiche, è possibile aggiornare il ruolo personalizzato.

## <a name="how-to-determine-the-permissions-you-need"></a>Come determinare le autorizzazioni necessarie

Azure ha migliaia di autorizzazioni che è possibile includere nel ruolo personalizzato. Ecco alcuni metodi che consentono di determinare le autorizzazioni da aggiungere al ruolo personalizzato:

- Esaminare i [ruoli predefiniti esistenti.](built-in-roles.md)

    È possibile modificare un ruolo esistente o combinare le autorizzazioni usate in più ruoli.

- Elencare i servizi di Azure a cui si vuole concedere l'accesso.

- Determinare [i provider di risorse mappati ai servizi di Azure.](../azure-resource-manager/management/azure-services-resource-providers.md)

    I servizi di Azure espongono le funzionalità e le autorizzazioni tramite [i provider di risorse](../azure-resource-manager/management/overview.md). Ad esempio, il provider di risorse Microsoft.Compute fornisce le risorse della macchina virtuale e il provider di risorse Microsoft.Billing fornisce le risorse di sottoscrizione e fatturazione. Conoscere i provider di risorse consente di limitare e determinare le autorizzazioni necessarie per il ruolo personalizzato.

    Quando si crea un ruolo personalizzato usando il portale di Azure, è anche possibile determinare i provider di risorse cercando parole chiave. Questa funzionalità di ricerca è descritta in [Creare o aggiornare ruoli personalizzati di Azure usando](custom-roles-portal.md#step-4-permissions)portale di Azure .

    ![Riquadro Aggiungi autorizzazioni con il provider di risorse](./media/custom-roles-portal/add-permissions-provider.png)

- Cercare le [autorizzazioni disponibili](resource-provider-operations.md) per trovare le autorizzazioni da includere.

    Quando si crea un ruolo personalizzato usando il portale di Azure, è possibile cercare le autorizzazioni in base alla parola chiave. Ad esempio, è possibile cercare le *autorizzazioni di fatturazione o* *macchina* virtuale. È anche possibile scaricare tutte le autorizzazioni come file CSV e quindi cercare questo file. Questa funzionalità di ricerca è descritta in [Creare o aggiornare ruoli personalizzati di Azure usando](custom-roles-portal.md#step-4-permissions)portale di Azure .

    ![Aggiungere l'elenco di autorizzazioni](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Esempio di ruolo personalizzato

Di seguito viene illustrato l'aspetto di un ruolo personalizzato come visualizzato usando Azure PowerShell in formato JSON. Questo ruolo personalizzato può essere usato per il monitoraggio e il riavvio di macchine virtuali.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Di seguito viene illustrato lo stesso ruolo personalizzato visualizzato con l'interfaccia della riga di comando di Azure.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Al termine della creazione, il ruolo personalizzato viene visualizzato nel portale di Azure con un'icona di risorsa arancione.

![Icona del ruolo personalizzato](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Proprietà del ruolo personalizzato

Nella tabella seguente viene descritto il significato delle proprietà del ruolo personalizzate.

| Proprietà | Obbligatoria | Tipo | Descrizione |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Sì | string | Nome visualizzato del ruolo personalizzato. Mentre una definizione di ruolo è un gruppo di gestione o una risorsa a livello di sottoscrizione, una definizione di ruolo può essere usata in più sottoscrizioni che condividono lo stesso Azure AD directory. Il nome visualizzato deve essere univoco nell'ambito della directory di Azure AD. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 128. |
| `Id`</br>`name` | Sì | string | ID univoco del ruolo personalizzato. Per Azure PowerShell e l'interfaccia della riga di comando di Azure questo ID viene generato automaticamente quando viene creato un nuovo ruolo. |
| `IsCustom`</br>`roleType` | Sì | string | Indica se questo è un ruolo personalizzato. Impostare su `true` o per i ruoli `CustomRole` personalizzati. Impostare su `false` o per i ruoli `BuiltInRole` predefiniti. |
| `Description`</br>`description` | Sì | string | Descrizione del ruolo personalizzato. Può includere lettere, numeri, spazi e caratteri speciali. Il numero massimo di caratteri è 1024. |
| `Actions`</br>`actions` | Sì | String[] | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. Per altre informazioni, vedere [Azioni](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | No | String[] | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `Actions` consentite. Per altre informazioni, vedere [notActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Se si crea un ruolo personalizzato con `DataActions` , tale ruolo non può essere assegnato nell'ambito del gruppo di gestione. Per altre informazioni, vedere [DataActions.](role-definitions.md#dataactions) |
| `NotDataActions`</br>`notDataActions` | No | String[] | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `DataActions` consentite. Per altre informazioni, vedere [NotDataActions.](role-definitions.md#notdataactions) |
| `AssignableScopes`</br>`assignableScopes` | Sì | String[] | Matrice di stringhe che specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. È possibile definire un solo gruppo di gestione in `AssignableScopes` di un ruolo personalizzato. L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima. Per altre informazioni, vedere [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Autorizzazioni con caratteri jolly

`Actions`, `NotActions` , `DataActions` e `NotDataActions` supportano i caratteri jolly ( ) `*` per definire le autorizzazioni. Un carattere jolly ( `*` ) estende un'autorizzazione a tutti gli elementi che corrispondono alla stringa di azione specificata. Si supponga, ad esempio, di voler aggiungere tutte le autorizzazioni correlate a Gestione costi di Azure ed esportazioni. È possibile aggiungere tutte queste stringhe di azione:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Invece di aggiungere tutte queste stringhe, è sufficiente aggiungere una stringa con caratteri jolly. Ad esempio, la stringa con caratteri jolly seguente equivale alle cinque stringhe precedenti. Sono incluse anche eventuali autorizzazioni di esportazione future che potrebbero essere aggiunte.

```
Microsoft.CostManagement/exports/*
```

È anche possibile includere più caratteri jolly in una stringa. Ad esempio, la stringa seguente rappresenta tutte le autorizzazioni di query per Gestione costi.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Chi può creare, eliminare, aggiornare o visualizzare un ruolo personalizzato

Come per i ruoli predefiniti, la proprietà `AssignableScopes` specifica gli ambiti in cui il ruolo personalizzato può essere assegnato. La proprietà `AssignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, aggiornare o visualizzare il ruolo personalizzato.

| Attività | Operazione | Descrizione |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, [Proprietari e](built-in-roles.md#owner) [Amministratori Accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Aggiornare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `AssignableScopes` del ruolo personalizzato possono aggiornare i ruoli personalizzati in tali ambiti. Ad esempio, [Proprietari e](built-in-roles.md#owner) [Amministratori Accesso utenti](built-in-roles.md#user-access-administrator) di gruppi di gestione, sottoscrizioni e gruppi di risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinitions/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono di rendere disponibili i ruoli personalizzati per l'assegnazione. |

## <a name="custom-role-limits"></a>Limiti dei ruoli personalizzati

L'elenco seguente descrive i limiti per i ruoli personalizzati.

- Ogni directory può avere fino a **5000** ruoli personalizzati.
- Azure Germania e Azure China (21Vianet) possono avere fino a 2000 ruoli personalizzati per ogni directory.
- Non è possibile `AssignableScopes` impostare sull'ambito radice ( `"/"` ).
- Non è possibile usare caratteri jolly ( `*` ) in `AssignableScopes` . Questa restrizione con caratteri jolly consente di garantire che un utente non possa ottenere potenzialmente l'accesso a un ambito aggiornando la definizione del ruolo.
- È possibile definire un solo gruppo di gestione in `AssignableScopes` un ruolo personalizzato. L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima.
- I ruoli personalizzati con `DataActions` non possono essere assegnati nell'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione del ruolo.

Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Formati di input e output

Per creare un ruolo personalizzato tramite la riga di comando, in genere si usa JSON per specificare le proprietà desiderate per il ruolo personalizzato. A seconda degli strumenti in uso, i formati di input e output avranno un aspetto leggermente diverso. Questa sezione elenca i formati di input e output a seconda dello strumento.

### <a name="azure-powershell"></a>Azure PowerShell

Per creare un ruolo personalizzato usando Azure PowerShell, è necessario fornire l'input seguente.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Per aggiornare un ruolo personalizzato usando Azure PowerShell, è necessario fornire l'input seguente. Si noti che `Id` la proprietà è stata aggiunta. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Di seguito viene illustrato un esempio dell'output quando si elenca un ruolo personalizzato usando Azure PowerShell e il [comando ConvertTo-Json.](/powershell/module/microsoft.powershell.utility/convertto-json) 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per creare o aggiornare un ruolo personalizzato usando l'interfaccia della riga di comando di Azure, è necessario fornire l'input seguente. Questo formato è lo stesso formato quando si crea un ruolo personalizzato usando Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Di seguito è riportato un esempio dell'output quando si elenca un ruolo personalizzato usando l'interfaccia della riga di comando di Azure.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>API REST

Per creare o aggiornare un ruolo personalizzato usando l'API REST, è necessario fornire l'input seguente. Questo formato è lo stesso che viene generato quando si crea un ruolo personalizzato usando il portale di Azure.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Di seguito è riportato un esempio dell'output quando si elenca un ruolo personalizzato usando l'API REST.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare un ruolo personalizzato di Azure con Azure PowerShell](tutorial-custom-role-powershell.md)
- [Esercitazione: Creare un ruolo personalizzato di Azure con l'interfaccia della riga di comando di Azure](tutorial-custom-role-cli.md)
- [Informazioni su Definizioni di ruolo di Azure](role-definitions.md)
- [Risolvere i problemi relativi al controllo degli accessi in base al ruolo di](troubleshooting.md)
