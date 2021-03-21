---
title: Creare e abilitare un piano di protezione DDoS di Azure usando un modello di Azure Resource Manager (modello ARM).
description: Informazioni su come creare e abilitare un piano di protezione DDoS di Azure usando un modello di Azure Resource Manager (modello ARM).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99092501"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Guida introduttiva: creare uno standard di protezione DDoS di Azure con il modello ARM

Questa Guida introduttiva descrive come usare un modello di Azure Resource Manager (modello ARM) per creare un piano di protezione DDoS (Distributed Denial of Service) e una rete virtuale (VNet), quindi Abilita il piano di protezione per il VNet. Un piano di protezione DDoS di Azure standard definisce un set di reti virtuali con protezione DDoS abilitata tra le sottoscrizioni. È possibile configurare un piano di protezione DDoS standard per l'organizzazione e collegare reti virtuali da più sottoscrizioni allo stesso piano.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Il modello definisce due risorse:

- [Microsoft. Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Distribuire il modello

In questo esempio, il modello crea un nuovo gruppo di risorse, un piano di protezione DDoS e un VNet.

1. Per accedere ad Azure e aprire il modello, selezionare il pulsante **Distribuisci in Azure** .

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Immettere i valori per creare un nuovo gruppo di risorse, il piano di protezione DDoS e il nome VNet.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Modello di avvio rapido DDoS.":::

    - **Subscription (sottoscrizione**): nome della sottoscrizione di Azure in cui verranno distribuite le risorse.
    - **Gruppo di risorse**: selezionare un gruppo di risorse esistente o creare un nuovo gruppo di risorse.
    - **Region (area**): area in cui viene distribuito il gruppo di risorse, ad esempio Stati Uniti orientali.
    - **Nome del piano di protezione DDoS**: il nome di per il nuovo piano di protezione DDoS.
    - **Nome rete virtuale**: crea un nome per il nuovo VNet.
    - **Location**: funzione che usa la stessa area del gruppo di risorse per la distribuzione delle risorse.
    - **Prefisso indirizzo VNET**: usare il valore predefinito o immettere l'indirizzo vnet.
    - **Prefisso subnet**: usare il valore predefinito o immettere la subnet VNet.
    - **Piano di protezione DDoS abilitato**: l'impostazione predefinita prevede `true` l'abilitazione del piano di protezione DDoS.

1. Selezionare **Rivedi e crea**.
1. Verificare che la convalida del modello sia stata superata e selezionare **Crea** per avviare la distribuzione.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Per copiare l'interfaccia della riga di comando di Azure o Azure PowerShell comando, selezionare il pulsante **copia** . Il pulsante **prova** consente di aprire Azure cloud Shell per eseguire il comando.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

L'output Mostra le nuove risorse.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, è possibile eliminare le risorse. Il comando elimina il gruppo di risorse e tutte le risorse in esso contenute.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e configurare i dati di telemetria per il piano di protezione DDoS, continuare con le esercitazioni.

> [!div class="nextstepaction"]
> [Visualizzare e configurare i dati di telemetria della protezione DDoS](telemetry.md)
