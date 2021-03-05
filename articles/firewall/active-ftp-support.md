---
title: Supporto FTP attivo del firewall di Azure
description: Per impostazione predefinita, FTP attivo è disabilitato nel firewall di Azure. È possibile abilitarlo usando PowerShell, l'interfaccia della riga di comando e il modello ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 47bced3c3c4f01212af6919d51adc5dfc2e6e1b5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200066"
---
# <a name="azure-firewall-active-ftp-support"></a>Supporto FTP attivo del firewall di Azure

Con FTP attivo, il server FTP avvia la connessione dati alla porta dati client FTP designata. I firewall sulla rete lato client bloccano normalmente una richiesta di connessione esterna a una porta client interna. Per ulteriori informazioni, vedere [FTP attivo rispetto a FTP passivo, una spiegazione definitiva](https://slacksite.com/other/ftp.html).

Per impostazione predefinita, il supporto FTP attivo è disabilitato nel firewall di Azure per la protezione da attacchi di rimbalzo FTP tramite il `PORT` comando FTP. Tuttavia, è possibile abilitare il FTP attivo quando si esegue la distribuzione usando Azure PowerShell, l'interfaccia della riga di comando di Azure o un modello ARM di Azure.

> [!NOTE]
> Attualmente, il protocollo FTP attivo è supportato solo per i firewall distribuiti in una rete virtuale. Il supporto della rete WAN virtuale verrà aggiunto in un secondo momento.

## <a name="azure-powershell"></a>Azure PowerShell

Per eseguire la distribuzione con Azure PowerShell, usare il `AllowActiveFTP` parametro. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un firewall con Allow Active FTP](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per eseguire la distribuzione usando l'interfaccia della riga di comando di Azure, usare il `--allow-active-ftp` parametro. Per ulteriori informazioni, vedere [AZ Network Firewall create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modello di Azure Resource Manager

Per eseguire la distribuzione usando un modello ARM, usare il `AdditionalProperties` campo:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Per ulteriori informazioni, vedere [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un firewall di Azure, vedere [distribuire e configurare il firewall di Azure con Azure PowerShell](deploy-ps.md).