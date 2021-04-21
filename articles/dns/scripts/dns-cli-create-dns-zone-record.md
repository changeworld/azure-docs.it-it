---
title: Creare una zona DNS e un record per un nome di dominio - Interfaccia della riga di comando di Azure - DNS di Azure
description: Questo esempio di interfaccia della riga di comando di Azure mostra come creare una zona DNS e un record per un nome di dominio
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5692c7a81d34ec9005c1c4675c71d63e697c5f47
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783702"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Esempio di interfaccia della riga di comando di Azure: creare una zona DNS e un record

Questo esempio di interfaccia della riga di comando di Azure crea una zona DNS e un record per un nome di dominio. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la zona DNS e tutte le risorse correlate.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create) | Crea una zona DNS di Azure. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Aggiunge un record *A* a una zona DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Elencare tutti i set di record *A* in una zona DNS. |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).