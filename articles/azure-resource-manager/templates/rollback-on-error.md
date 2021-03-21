---
title: Eseguire il rollback in errore alla distribuzione riuscita
description: Consente di specificare che è necessario eseguire il rollback di una distribuzione non riuscita a una distribuzione riuscita.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492213"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback in merito a errori di distribuzione riuscita

Quando una distribuzione non riesce, è possibile ridistribuire automaticamente una distribuzione precedente con esito positivo dalla cronologia della distribuzione. Questa funzionalità è utile se si ha uno stato valido noto per la distribuzione dell'infrastruttura e si vuole ripristinare questo stato. È possibile specificare una particolare distribuzione precedente o l'ultima distribuzione riuscita.

> [!IMPORTANT]
> Questa funzionalità consente di eseguire il rollback di una distribuzione non riuscita ridistribuendo una distribuzione precedente. Questo risultato può essere diverso da quello previsto per l'annullamento della distribuzione non riuscita. Assicurarsi di comprendere il modo in cui la distribuzione precedente viene ridistribuita.

## <a name="considerations-for-redeploying"></a>Considerazioni sulla ridistribuzione

Prima di usare questa funzionalità, prendere in considerazione questi dettagli sul modo in cui viene gestita la ridistribuzione:

- La distribuzione precedente viene eseguita usando la [modalità completa](./deployment-modes.md#complete-mode), anche se è stata usata la [modalità incrementale](./deployment-modes.md#incremental-mode) durante la distribuzione precedente. La ridistribuzione in modalità completa potrebbe produrre risultati imprevisti quando la distribuzione precedente usava incrementale. La modalità completa indica che tutte le risorse non incluse nella distribuzione precedente vengono eliminate. Specificare una distribuzione precedente che rappresenta tutte le risorse e i relativi Stati che si desidera includere nel gruppo di risorse. Per altre informazioni, vedere [modalità di distribuzione](./deployment-modes.md).
- La ridistribuzione viene eseguita esattamente come è stata eseguita in precedenza con gli stessi parametri. Non è possibile modificare i parametri.
- La ridistribuzione influisce solo sulle risorse. le modifiche apportate ai dati non sono interessate.
- Questa funzionalità può essere usata solo con le distribuzioni di gruppi di risorse. Non supporta le distribuzioni a livello di sottoscrizione, gruppo di gestione o tenant. Per altre informazioni sulla distribuzione a livello di sottoscrizione, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](./deploy-to-subscription.md).
- È possibile usare questa opzione solo con le distribuzioni a livello di radice. Le distribuzioni di un modello annidato non sono disponibili per la ridistribuzione.

Per usare questa opzione, le distribuzioni devono avere nomi univoci nella cronologia di distribuzione. Si tratta solo di nomi univoci che possono essere identificati da una distribuzione specifica. Se non si hanno nomi univoci, una distribuzione non riuscita potrebbe sovrascrivere una distribuzione corretta nella cronologia.

Se si specifica una distribuzione precedente che non esiste nella cronologia di distribuzione, il rollback restituisce un errore.

## <a name="powershell"></a>PowerShell

Per eseguire nuovamente l'ultima distribuzione con esito positivo, aggiungere il parametro `-RollbackToLastDeployment` come contrassegno.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Per eseguire nuovamente una distribuzione specifica, usare il parametro `-RollBackDeploymentName` e specificare il nome della distribuzione. La distribuzione specificata deve aver avuto esito positivo.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per eseguire nuovamente l'ultima distribuzione con esito positivo, aggiungere il parametro `--rollback-on-error` come contrassegno.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Per eseguire nuovamente una distribuzione specifica, usare il parametro `--rollback-on-error` e specificare il nome della distribuzione. La distribuzione specificata deve aver avuto esito positivo.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Per eseguire nuovamente l'ultima distribuzione con esito positivo se la distribuzione corrente non riesce, usare:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Per eseguire nuovamente una distribuzione specifica se la distribuzione corrente non riesce, usare:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

La distribuzione specificata deve aver avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle modalità complete e incrementali, vedere [Azure Resource Manager modalità di distribuzione](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
