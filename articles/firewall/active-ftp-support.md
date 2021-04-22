---
title: Firewall di Azure ftp attivo
description: Per impostazione predefinita, FTP attivo è disabilitato Firewall di Azure. È possibile abilitarlo usando PowerShell, l'interfaccia della riga di comando e il modello arm.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864470"
---
# <a name="azure-firewall-active-ftp-support"></a>Firewall di Azure ftp attivo

Con FTP attivo, il server FTP avvia la connessione dati alla porta dati del client FTP designata. I firewall nella rete lato client bloccano in genere una richiesta di connessione esterna a una porta client interna. Per altre informazioni, vedere [Ftp attivo e FTP passivo, spiegazione definitiva](https://slacksite.com/other/ftp.html).

Per impostazione predefinita, il supporto FTP attivo è disabilitato Firewall di Azure per la protezione da attacchi di tipo bounce FTP tramite il comando `PORT` FTP. Tuttavia, è possibile abilitare FTP attivo quando si esegue la distribuzione usando Azure PowerShell, l'interfaccia della riga di comando di Azure o un modello di Azure Arm.

Per supportare FTP in modalità attiva, è necessario aprire le porte TCP seguenti:

- Porta 21 del server FTP da qualsiasi posizione (il client avvia la connessione)
- Porta 21 del server FTP alle porte > 1023 (il server risponde alla porta di controllo del client)
- Porta 20 del server FTP alle porte > 1023 nei client (il server avvia la connessione dati alla porta dati del client)
- Porta 20 del server FTP dalle porte > 1023 nei client (il client invia gli elenchi di controllo di accesso alla porta dati del server)

## <a name="azure-powershell"></a>Azure PowerShell

Per eseguire la distribuzione Azure PowerShell, usare il `AllowActiveFTP` parametro . Per altre informazioni, vedere [Creare un firewall con Consenti FTP attivo](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per eseguire la distribuzione tramite l'interfaccia della riga di comando di Azure, usare il `--allow-active-ftp` parametro . Per altre informazioni, vedere [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Modello di Azure Resource Manager

Per eseguire la distribuzione usando un modello arm, usare il `AdditionalProperties` campo :

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Per altre informazioni, vedere [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un Firewall di Azure, vedere Distribuire e configurare Firewall di Azure [usando Azure PowerShell](deploy-ps.md).