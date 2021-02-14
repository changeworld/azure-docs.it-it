---
title: Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione ConstrainedAllocationFailed durante la distribuzione di un servizio cloud in Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521069"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud in Azure

In questo articolo verranno risolti gli errori di allocazione in cui i servizi cloud di Azure non possono essere distribuiti a causa dei vincoli.

Microsoft Azure alloca quando si è:

- Aggiornamento delle istanze di servizi cloud

- Aggiunta di nuove istanze del ruolo Web o di lavoro

- Distribuzione di istanze in un servizio cloud

Occasionalmente è possibile che si verifichino errori durante queste operazioni anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

In portale di Azure passare al servizio cloud e nella barra laterale selezionare *log operazioni (versione classica)* per visualizzare i log.

Quando si esaminano i log del servizio cloud, viene visualizzata la seguente eccezione:

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|ConstrainedAllocationFailed     |L'operazione di Azure ' `{Operation ID}` ' non è riuscita con codice COMPUTE. ConstrainedAllocationFailed. Dettagli: allocazione non riuscita. non è possibile soddisfare i vincoli nella richiesta. La nuova distribuzione richiesta del servizio è legata a un gruppo di affinità o ha come destinazione una rete virtuale, oppure è presente una distribuzione esistente in questo servizio ospitato. Una di queste condizioni vincola la nuova distribuzione a risorse Azure specifiche. Riprovare più tardi o provare a ridurre le dimensioni della macchina virtuale o il numero di istanze del ruolo. In alternativa, se possibile, rimuovere i vincoli sopra indicati o provare a distribuire in un'area diversa.|

## <a name="cause"></a>Causa

Si è verificato un problema di capacità con l'area o il cluster in cui si esegue la distribuzione. Si verifica quando lo SKU di risorsa selezionato non è disponibile per il percorso specificato.

> [!NOTE]
> Quando viene distribuito il primo nodo di un servizio cloud, questo viene *aggiunto* a un pool di risorse. Un pool di risorse può essere un cluster singolo o un gruppo di cluster.
>
> Nel tempo, le risorse in questo pool di risorse potrebbero essere completamente utilizzate. Se un servizio cloud esegue una richiesta di allocazione per risorse aggiuntive quando nel pool di risorse aggiunto sono disponibili risorse insufficienti, la richiesta comporterà un [errore di allocazione](cloud-services-allocation-failures.md).

## <a name="solution"></a>Soluzione

In questo scenario, è necessario selezionare un'area o uno SKU diverso per distribuire il servizio cloud in. Prima di distribuire o aggiornare il servizio cloud, è possibile determinare quali SKU sono disponibili in un'area o in una zona di disponibilità. Seguire i processi dell'interfaccia della riga di comando di [Azure](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)o [API REST](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Elencare SKU nell'area usando l'interfaccia della riga di comando

È possibile usare il comando [AZ VM list-SKUS](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Usare il `--location` parametro per filtrare l'output in base alla posizione in uso.
- Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni.
- Per ulteriori informazioni, vedere la Guida [risoluzione dell'errore per SKU non disponibile](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Ad esempio:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Risultati di esempio:** ![ Output dell'interfaccia della riga di comando di Azure dell'esecuzione del comando ' AZ VM list-SKUS--location southcentralus--size Standard_F--output Table ', che Mostra gli SKU disponibili.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Elencare gli SKU nell'area usando PowerShell

È possibile usare il comando [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtrare i risultati in base all'area.
- Per questo comando, è necessaria la versione più recente di PowerShell.
- Per ulteriori informazioni, vedere la Guida [risoluzione dell'errore per SKU non disponibile](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Ad esempio:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Altri comandi utili:**

Filtrare le posizioni che contengono le dimensioni (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrare tutti i percorsi che contengono le dimensioni (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Elencare gli SKU nell'area usando l'API REST

È possibile usare l'operazione [SKU di risorsa-elenco](https://docs.microsoft.com/rest/api/compute/resourceskus/list) . Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori soluzioni di errore di allocazione e per comprendere meglio il modo in cui vengono generate:

> [!div class="nextstepaction"]
> [Errori di allocazione (servizi cloud)](cloud-services-allocation-failures.md)

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e stack overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto tecnico, nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) selezionare *Supporto*.
