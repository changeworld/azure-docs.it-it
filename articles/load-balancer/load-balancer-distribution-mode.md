---
title: Configurare la modalità di distribuzione di Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come iniziare a configurare la modalità di distribuzione per Azure Load Balancer per supportare l'affinità IP di origine.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 7f2525b89f03e8bc1a2c3166b46c40b4dbb6ff17
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99562008"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurare la modalità di distribuzione per Azure Load Balancer

Azure Load Balancer supporta due modalità di distribuzione per la distribuzione del traffico alle applicazioni:

* Basato su hash
* Affinità dell'IP di origine

Questo articolo illustra come configurare la modalità di distribuzione per il Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Configurare la modalità di distribuzione

---

# <a name="azure-portal"></a>[**Portale di Azure**](#tab/azure-portal)

È possibile modificare la configurazione della modalità di distribuzione modificando la regola di bilanciamento del carico nel portale.

1. Accedere al portale di Azure e individuare il gruppo di risorse contenente il servizio di bilanciamento del carico che si desidera modificare facendo clic su **gruppi di risorse**.
2. Nella schermata di panoramica del servizio di bilanciamento del carico selezionare **regole di bilanciamento del carico** in **Impostazioni**.
3. Nella schermata regole di bilanciamento del carico selezionare la regola di bilanciamento del carico per la quale si desidera modificare la modalità di distribuzione.
4. Nella regola, la modalità di distribuzione viene modificata modificando la casella di riepilogo a discesa della **persistenza della sessione** . 

Sono disponibili le opzioni seguenti: 

* **None (basato su hash)** : specifica che le richieste successive provenienti dallo stesso client possono essere gestite da qualsiasi macchina virtuale.
* **IP client (affinità IP di origine 2-tupla)** : specifica che le richieste successive provenienti dallo stesso indirizzo IP client verranno gestite dalla stessa macchina virtuale.
* **IP e protocollo client (affinità IP di origine 3-tupla)** : specifica che le richieste successive provenienti dalla stessa combinazione di indirizzo IP e protocollo client verranno gestite dalla stessa macchina virtuale.

5. Scegliere la modalità di distribuzione e quindi fare clic su **Salva**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Modificare la persistenza della sessione nella regola del servizio di bilanciamento del carico." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare PowerShell per modificare le impostazioni di distribuzione del servizio di bilanciamento del carico in una regola di bilanciamento del carico esistente. Il comando seguente aggiorna la modalità di distribuzione: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Impostare il valore dell' `LoadDistribution` elemento per la quantità di bilanciamento del carico richiesta. 

Specificare **SourceIP** per il bilanciamento del carico a due Tuple (IP di origine e IP di destinazione). 

Specificare **sourceIPProtocol** per il bilanciamento del carico a tre Tuple (IP di origine, IP di destinazione e tipo di protocollo). 

Specificare **il comportamento predefinito del** bilanciamento del carico con cinque tuple.

---

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Load Balancer](load-balancer-overview.md)
* [Introduzione alla configurazione del bilanciamento del carico con connessione a Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)