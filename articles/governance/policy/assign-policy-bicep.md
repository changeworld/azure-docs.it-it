---
title: 'Guida introduttiva: nuova assegnazione di criteri con il file bicipite (anteprima)'
description: In questa Guida introduttiva si userà un file bicipite (anteprima) per creare un'assegnazione di criteri per identificare le risorse non conformi.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223979"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Guida introduttiva: creare un'assegnazione di criteri per identificare le risorse non conformi usando un file bicipite

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse.
Questa Guida introduttiva illustra il processo di uso di un file [bicipite (anteprima)](https://github.com/Azure/bicep) compilato in un modello di Azure Resource Manager (modello ARM) per creare un'assegnazione di criteri per identificare le macchine virtuali che non usano Managed Disks. Alla fine di questo processo, verranno identificate le macchine virtuali che non usano dischi gestiti e che sono quindi _non conformi_ all'assegnazione di criteri.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello viene visualizzato nel portale di Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Pulsante per distribuire il modello di Resource Manager per l'assegnazione di criteri di Azure ad Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Prerequisiti 

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Versione bicipite `0.3` o successiva installata. Se non si dispone ancora dell'interfaccia della riga di comando bicipite o è necessario aggiornare, vedere [installare bicipite (anteprima)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Esaminare il file bicipite

In questa Guida introduttiva si crea un'assegnazione di criteri e si assegna una definizione dei criteri predefinita denominata _Controlla macchine virtuali che non usano Managed disks_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Per un elenco parziale di tutti i criteri predefiniti disponibili, vedere [Esempi di Criteri di Azure](./samples/index.md).

Creare il file bicipite seguente come `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

La risorsa definita nel file è la seguente:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Distribuire il modello

> [!NOTE]
> Il servizio Criteri di Azure è gratuito. Per altre informazioni, vedere [Panoramica di Criteri di Azure](./overview.md).

Dopo aver installato l'interfaccia della riga di comando bicipite e creato il file, è possibile distribuire il file bicipite con:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Alcune risorse aggiuntive:

- Per altri modelli di esempio, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Per informazioni di riferimento sul modello, vedere [Informazioni di riferimento sui modelli di Azure](/azure/templates/microsoft.authorization/allversions).
- Per informazioni su come sviluppare modelli di Resource Manager, vedere la [documentazione di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Per informazioni sulla distribuzione a livello di sottoscrizione, vedere [Creare gruppi di risorse e risorse a livello di sottoscrizione](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Selezionare **Assegnazioni** a sinistra nella pagina. Individuare l'assegnazione dei criteri _Audit VMs that do not use managed disks_ (Controllare le macchine virtuali che non usano Managed Disks) creata.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Screenshot dei dettagli di conformità nella pagina Conformità dei criteri." border="false":::

Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Non-compliant resources** (Risorse non conformi).

Per altre informazioni, vedere [Funzionamento della conformità](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere l'assegnazione creata, eseguire la procedura seguente:

1. Selezionare **Conformità** (o **Assegnazioni**) a sinistra della pagina Criteri di Azure e individuare l'assegnazione dei criteri _Audit VMs that do not use managed disks_ (Controllare le macchine virtuali che non usano Managed Disks) creata.

1. Fare clic con il pulsante destro del mouse sull'assegnazione di criteri _Controlla macchine virtuali che non usano dischi gestiti_ e scegliere **Elimina assegnazione**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Screenshot dell'uso del menu di scelta rapida per eliminare un'assegnazione dalla pagina Conformità." border="false":::

1. Eliminare il file `assignment.bicep`.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata assegnata una definizione di criteri predefinita a un ambito ed è stato valutato il relativo report di conformità. La definizione dei criteri garantisce che tutte le risorse nell'ambito siano conformi e identifica quelle che non lo sono.

Per altre informazioni sull'assegnazione di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)