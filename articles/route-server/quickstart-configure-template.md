---
title: 'Avvio rapido: Creare un server di route di Azure usando un modello di Azure Resource Manager (modello arm)'
description: Questa guida introduttiva illustra come creare un server di route di Azure usando un Azure Resource Manager di Azure .
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388041"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Guida introduttiva: Creare un server di route di Azure con un modello di Azure Azure Arm

Questa guida introduttiva descrive come usare un modello di Azure Resource Manager (modello ARM) per distribuire un server di route di Azure in una rete virtuale nuova o esistente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-route-server).

In questa guida introduttiva si distribuirà un server di route di Azure in una rete virtuale nuova o esistente. Verrà creata una subnet dedicata `RouteServerSubnet` denominata per ospitare il server di route. Il server di route verrà configurato anche con l'ASN peer e l'IP peer per stabilire un peering BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Nel modello sono state definite più risorse di Azure.

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (due subnet, una denominata `routeserversubnet` )
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (distribuzione del server di route)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (configurazione ASN peer e IP peer)


Per altri modelli correlati a ExpressRoute, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

    Il nome del gruppo di risorse è il nome del progetto seguito da **rg**.

    La distribuzione del modello richiede circa 20 minuti. Al termine, l'output sarà simile al seguente:

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Route Server Resource Manager output di distribuzione di PowerShell.":::

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è il nome del progetto seguito da **rg**.

1. Il gruppo di risorse deve contenere solo la rete virtuale:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Gruppo di risorse di distribuzione del server di route con rete virtuale.":::

1. Passare a https://aka.ms/routeserver.

1. Selezionare il server di route **denominato routeserver** per verificare che la distribuzione sia riuscita.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Screenshot della pagina di panoramica del server di route.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il server di route non sono più necessarie, eliminare il gruppo di risorse. In questo modo vengono rimossi il server di route e tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:

* Route Server
* Rete virtuale
* Subnet

Dopo aver creato il server di route di Azure, continuare ad apprendere come il server di route di Azure interagisce con ExpressRoute e i gateway VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute e VPN di Azure](expressroute-vpn-support.md)
