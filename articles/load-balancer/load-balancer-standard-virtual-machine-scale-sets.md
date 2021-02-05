---
title: Aggiungere regole per i set di scalabilità di macchine virtuali e Load Balancer Standard di Azure
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Con questo percorso di apprendimento, iniziare a usare Azure Load Balancer Standard e i set di scalabilità di macchine virtuali.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592280"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Aggiungere regole per Azure Load Balancer con i set di scalabilità di macchine virtuali

Quando si lavora con i set di scalabilità di macchine virtuali e Azure Load Balancer, tenere presenti le linee guida seguenti.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Port porting e regole NAT in ingresso

Dopo la creazione del set di scalabilità, la porta back-end non può essere modificata per una regola di bilanciamento del carico usata da un probe di integrità del servizio di bilanciamento del carico. Per modificare la porta, rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali e aggiornando la porta. Quindi, configurare di nuovo il probe di integrità.

Quando si usa il set di scalabilità di macchine virtuali nel pool back-end del servizio di bilanciamento del carico, le regole NAT in ingresso predefinite vengono create automaticamente.
  
## <a name="inbound-nat-pool"></a>Pool NAT in ingresso

Ogni set di scalabilità di macchine virtuali deve avere almeno un pool NAT in ingresso. Un pool NAT in ingresso è una raccolta di regole NAT in ingresso. Un pool NAT in ingresso non può supportare più set di scalabilità di macchine virtuali.

## <a name="load-balancing-rules"></a>Regole di bilanciamento del carico

Quando si usa il set di scalabilità di macchine virtuali nel pool back-end del servizio di bilanciamento del carico, la regola di bilanciamento del carico predefinita viene creata automaticamente.
  
## <a name="outbound-rules"></a>Regole in uscita

Per creare una regola in uscita per un pool back-end a cui fa già riferimento una regola di bilanciamento del carico, selezionare **No** in **Crea regole in uscita implicite** nel portale di Azure quando viene creata la regola di bilanciamento del carico in ingresso.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Screenshot che mostra la creazione di regole di bilanciamento del carico." border="true":::

Usare i metodi seguenti per distribuire un set di scalabilità di macchine virtuali con un'istanza esistente di Load Balancer:

* [Configurare un set di scalabilità di macchine virtuali con un'istanza esistente di Azure Load Balancer usando il portale di Azure](./configure-vm-scale-set-portal.md)
* [Configurare un set di scalabilità di macchine virtuali con un'istanza esistente di Azure Load Balancer usando Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Configurare un set di scalabilità di macchine virtuali con un'istanza esistente di Azure Load Balancer usando l'interfaccia della riga di comando di Azure](./configure-vm-scale-set-cli.md)
* [Aggiornare o eliminare un'istanza esistente di Azure Load Balancer usata da un set di scalabilità di macchine virtuali](./update-load-balancer-with-vm-scale-set.md)
