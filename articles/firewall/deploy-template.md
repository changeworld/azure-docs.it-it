---
title: "Avvio rapido: Creare un'istanza di Firewall di Azure con zone di disponibilità - Modello di Resource Manager"
description: In questo argomento di avvio rapido si usa un modello per distribuire Azure Firewall. La rete virtuale ha una rete virtuale con tre subnet. Sono state distribuite due macchine virtuali Windows Server, un jumpbox e un server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 478f3454a728871040cdbbf9f817394cffe6b82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660254"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>Avvio rapido: Distribuire Firewall di Azure con zone di disponibilità - Modello di Resource Manager

In questa guida di avvio rapido si usa un modello di Azure Resource Manager per distribuire un'istanza di Firewall di Azure in tre zone di disponibilità.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Il modello crea un ambiente di rete di test con un firewall. La rete ha una rete virtuale (VNet) con tre subnet: *AzureFirewallSubnet*, *ServersSubnet*, e *JumpboxSubnet*. Ciascuna delle subnet *ServersSubnet* e *JumpboxSubnet* dispone di una macchina singola virtuale Windows Server a due core.

Il firewall si trova nella subnet *AzureFirewallSubnet* e include una raccolta di regole dell'applicazione con una singola regola che consente l'accesso a `www.microsoft.com`.

Una route definita dall'utente punta al traffico di rete dalla subnet *ServersSubnet* attraverso il firewall, in cui vengono applicate le regole del firewall.

Per altre informazioni su Firewall di Azure, vedere [Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea un'istanza di Firewall di Azure con zone di disponibilità, unitamente alle risorse necessarie per supportare Firewall di Azure.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un'istanza di Firewall di Azure, l'infrastruttura di rete e due macchine virtuali.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. Nella pagina **Create a sandbox setup of Azure Firewall with Zones** (Crea una configurazione sandbox di Firewall di Azure con zone) del portale digitare o selezionare i valori seguenti:
   - **Gruppo di risorse**: selezionare **Crea nuovo**, digitare un nome per il gruppo di risorse e selezionare **OK**. 
   - **Nome della rete virtuale**: Digitare un nome per la nuova rete virtuale.
   - **Nome utente amministratore**: Digitare un nome utente per l'account utente amministratore.
   - **Password amministratore**: Digitare una password dell'amministratore.

3. Leggere le condizioni, selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista**. La distribuzione può richiedere almeno 10 minuti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Esplorare le risorse create con il firewall.

Per altre informazioni sulla sintassi JSON e sulle proprietà di un firewall in un modello, vedere [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il firewall e tutte le relative risorse eseguendo il comando `Remove-AzResourceGroup` di PowerShell. Per rimuovere un gruppo di risorse denominato *MyResourceGroup*, eseguire:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Non rimuovere il gruppo di risorse e il firewall se si prevede di continuare con l'esercitazione sul monitoraggio del firewall. 

## <a name="next-steps"></a>Passaggi successivi

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./firewall-diagnostics.md)