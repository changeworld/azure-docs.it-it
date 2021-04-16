---
title: Configurare la modalità di distribuzione di Azure Load Balancer
titleSuffix: Azure Load Balancer
description: In questo articolo viene illustrata la configurazione della modalità di distribuzione per Azure Load Balancer per supportare l'affinità IP di origine.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483621"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurare la modalità di distribuzione per Azure Load Balancer

Azure Load Balancer supporta due modalità di distribuzione per la distribuzione del traffico alle applicazioni:

* Basato su hash
* Affinità dell'IP di origine

Questo articolo illustra come configurare la modalità di distribuzione per l'Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Configurare la modalità di distribuzione

---

# <a name="azure-portal"></a>[**Portale di Azure**](#tab/azure-portal)

È possibile modificare la configurazione della modalità di distribuzione modificando la regola di bilanciamento del carico nel portale.

1. Accedere al portale di Azure e individuare il gruppo di risorse contenente il servizio di bilanciamento del carico che si vuole modificare facendo clic su **Gruppi di risorse**.
2. Nella schermata di panoramica del servizio di bilanciamento del carico selezionare **Regole di bilanciamento del carico** in **Impostazioni**.
3. Nella schermata regole di bilanciamento del carico selezionare la regola di bilanciamento del carico per cui si vuole modificare la modalità di distribuzione.
4. Nella regola la modalità di distribuzione viene modificata modificando la casella di riepilogo **a** discesa Persistenza sessione. 

Sono disponibili le opzioni seguenti: 

* **Nessuno (basato su hash):** specifica che le richieste successive dello stesso client possono essere gestite da qualsiasi macchina virtuale.
* **IP client (affinità IP** di origine a due tuple): specifica che le richieste successive dallo stesso indirizzo IP client verranno gestite dalla stessa macchina virtuale.
* IP client e protocollo (affinità IP di origine a tre **tuple):** specifica che le richieste successive dalla stessa combinazione di indirizzo IP client e protocollo verranno gestite dalla stessa macchina virtuale.

5. Scegliere la modalità di distribuzione e quindi selezionare **Salva**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Modificare la persistenza della sessione nella regola di bilanciamento del carico." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare PowerShell per modificare le impostazioni di distribuzione del servizio di bilanciamento del carico in una regola di bilanciamento del carico esistente. Il comando seguente aggiorna la modalità di distribuzione: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Impostare il valore dell'elemento `LoadDistribution` per il tipo di bilanciamento del carico richiesto. 

* Specificare **SourceIP per** il bilanciamento del carico a due tuple (IP di origine e ip di destinazione). 

* Specificare **SourceIPProtocol per** il bilanciamento del carico a tre tuple (IP di origine, IP di destinazione e tipo di protocollo). 

* Specificare **Default per** il comportamento predefinito del bilanciamento del carico a cinque tuple.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Usare l'interfaccia della riga di comando di Azure per modificare le impostazioni di distribuzione del servizio di bilanciamento del carico in una regola di bilanciamento del carico esistente.  Il comando seguente aggiorna la modalità di distribuzione:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Impostare il valore di `--load-distribution` per il tipo di bilanciamento del carico richiesto.

* Specificare **SourceIP per** il bilanciamento del carico a due tuple (IP di origine e ip di destinazione). 

* Specificare **SourceIPProtocol per** il bilanciamento del carico a tre tuple (IP di origine, IP di destinazione e tipo di protocollo). 

* Specificare **Default per** il comportamento predefinito del bilanciamento del carico a cinque tuple.

Per altre informazioni sul comando usato in questo articolo, vedere [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Load Balancer](load-balancer-overview.md)
* [Introduzione alla configurazione del bilanciamento del carico con connessione a Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
