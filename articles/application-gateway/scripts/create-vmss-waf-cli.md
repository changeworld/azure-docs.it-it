---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Limitare il traffico Web | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un gateway applicazione con un web application firewall e un set di scalabilità di macchine virtuali che usa regole OWASP per limitare il traffico.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e9201f41c9552b6a60f9ccd8eacda60ac46f89eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99591636"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Limitare il traffico Web con l'interfaccia della riga di comando di Azure

Questo script crea un gateway applicazione con un web application firewall che usa un set di scalabilità di macchine virtuali per i server back-end. Il web application firewall consente di limitare il traffico Web in base alle regole OWASP. Dopo aver eseguito lo script, è possibile testare il gateway applicazione tramite il relativo indirizzo IP pubblico.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Crea una rete virtuale. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Crea una subnet in una rete virtuale. |
| [az network public-ip create](/cli/azure/network/public-ip) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [az network application-gateway create](/cli/azure/network/application-gateway) | Creare un gateway applicazione. |
| [az vmss create](/cli/azure/vmss#az-vmss-create) | Crea un set di scalabilità di macchine virtuali. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea un account di archiviazione. |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | Crea un account di archiviazione. |
| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) | Ottiene l'indirizzo IP pubblico del gateway applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per il gateway applicazione sono reperibili nella [documentazione sul gateway applicazione di Azure](../cli-samples.md).
