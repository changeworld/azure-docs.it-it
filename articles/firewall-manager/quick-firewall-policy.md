---
title: 'Guida introduttiva: creare un firewall di Azure e un modello di Gestione risorse di criteri firewall'
description: In questa Guida introduttiva si distribuisce un firewall di Azure e un criterio del firewall.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: 9cc263d311bd550a92a0c8f14ab5ce86d72e9ee3
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633632"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Guida introduttiva: creare un firewall di Azure e un criterio firewall-modello ARM

In questa Guida introduttiva si userà un modello di Azure Resource Manager (ARM template) per creare un firewall di Azure e un criterio del firewall. Il criterio firewall ha una regola dell'applicazione che consente le connessioni a `www.microsoft.com` e una regola che consente le connessioni a Windows Update usando il tag FQDN **windowsupdate** . Una regola di rete consente le connessioni UDP a un server di ora in 13.86.101.172.

Inoltre, i gruppi IP vengono usati nelle regole per definire gli indirizzi IP di **origine** .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Per informazioni su gestione firewall di Azure, vedere [che cos'è gestione firewall di Azure?](overview.md).

Per informazioni sul firewall di Azure, vedere [che cos'è il firewall di Azure?](../firewall/overview.md).

Per informazioni sui gruppi IP, vedere [gruppi IP nel firewall di Azure](../firewall/ip-groups.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello consente di creare una rete virtuale Hub, insieme alle risorse necessarie per supportare lo scenario.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un'istanza di Firewall di Azure, una WAN e un hub virtuali, l'infrastruttura di rete e due macchine virtuali.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Nella pagina **Crea un firewall e firewallpolicy con regole e Ipgroups** nel portale digitare o selezionare i valori seguenti:
   - Sottoscrizione: selezionare le sottoscrizioni esistenti.
   - Gruppo di risorse:  selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e quindi **OK**.
   - Area: Scegliere un'area,
   - Nome del firewall: digitare un nome per il firewall.

3. Selezionare **Rivedi e crea** e quindi **Crea**. La distribuzione può richiedere almeno 10 minuti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Al termine della distribuzione, verranno visualizzate le risorse simili seguenti.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Risorse distribuite":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il firewall non sono più necessarie, eliminare il gruppo di risorse. Oltre al firewall verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica dei criteri di Gestione firewall di Azure](policy-overview.md)
