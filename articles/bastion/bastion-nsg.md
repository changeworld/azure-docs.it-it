---
title: Uso di macchine virtuali e gruppi in Azure Bastion
description: È possibile usare i gruppi di sicurezza di rete con Azure Bastion. Informazioni sulle subnet richieste per questa configurazione.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700219"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Uso di NSG Access e Azure Bastion

Quando si lavora con Azure Bastion, è possibile usare i gruppi di sicurezza di rete (gruppi). Per ulteriori informazioni, vedere [gruppi di sicurezza](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

In questo diagramma:

* L'host Bastion viene distribuito nella rete virtuale.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente passa alla macchina virtuale di Azure in RDP/SSH.
* Connettere l'integrazione: sessione RDP/SSH con singolo clic all'interno del browser
* Non è richiesto alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Gruppi di sicurezza di rete

Questa sezione illustra il traffico di rete tra l'utente e il Bastione di Azure e per le macchine virtuali di destinazione nella rete virtuale:

> [!IMPORTANT]
> Se si sceglie di usare un NSG con la risorsa Bastion di Azure, è **necessario** creare tutte le seguenti regole di traffico in ingresso e in uscita. Se si omette una qualsiasi delle regole seguenti nel NSG, la risorsa di Azure Bastion non riceverà gli aggiornamenti necessari in futuro e quindi aprirà la risorsa alle vulnerabilità di sicurezza future.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion viene distribuito in modo specifico a ***AzureBastionSubnet***.

* **Traffico in ingresso:**

   * **Traffico in ingresso da Internet pubblico:** Azure Bastion creerà un IP pubblico che richiede la porta 443 abilitata nell'indirizzo IP pubblico per il traffico in ingresso. NON è necessario aprire la porta 3389/22 nella AzureBastionSubnet.
   * **Traffico in ingresso dal piano di controllo Bastion di Azure:** Per la connettività del piano di controllo, abilitare la porta 443 in ingresso dal tag del servizio **GatewayManager** . In questo modo, il piano di controllo, ovvero Gestione Gateway, sarà in grado di comunicare con Azure Bastion.
   * **Traffico in ingresso dal piano dati Bastion di Azure:** Per la comunicazione del piano dati tra i componenti sottostanti di Azure Bastion, abilitare le porte 8080, 5701 in ingresso dal tag del servizio **virtualnetwork** al tag del servizio **virtualnetwork** . Questo consente ai componenti di Azure Bastion di comunicare tra loro.
   * **Traffico in ingresso da Azure Load Balancer:** Per i probe di integrità, abilitare la porta 443 in ingresso dal tag del servizio **AzureLoadBalancer** . Questo consente Azure Load Balancer di rilevare la connettività


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Screenshot mostra le regole di sicurezza in ingresso per la connettività di Azure Bastion.":::

* **Traffico in uscita:**

   * **Traffico in uscita per le macchine virtuali di destinazione:** Azure Bastion raggiungerà le VM di destinazione tramite un indirizzo IP privato. Il gruppi deve consentire il traffico in uscita ad altre subnet VM di destinazione per la porta 3389 e 22.
   * **Traffico in uscita verso il piano dati Bastion di Azure:** Per la comunicazione del piano dati tra i componenti sottostanti di Azure Bastion, abilitare le porte 8080, 5701 in uscita dal tag del servizio **virtualnetwork** al tag del servizio **virtualnetwork** . Questo consente ai componenti di Azure Bastion di comunicare tra loro.
   * **Traffico in uscita verso altri endpoint pubblici in Azure:** Azure Bastion deve essere in grado di connettersi a diversi endpoint pubblici in Azure, ad esempio per archiviare i log di diagnostica e i log di misurazione. Per questo motivo, Azure Bastion deve essere in uscita da 443 al tag del servizio **AzureCloud** .
   * **Traffico in uscita verso Internet:** Azure Bastion deve essere in grado di comunicare con Internet per la convalida della sessione e del certificato. Per questo motivo, è consigliabile abilitare la porta 80 in uscita verso **Internet.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Screenshot mostra le regole di sicurezza in uscita per la connettività di Azure Bastion.":::

### <a name="target-vm-subnet"></a>Subnet VM di destinazione
Si tratta della subnet che contiene la macchina virtuale di destinazione a cui si desidera connettersi tramite RDP/SSH.

   * **Traffico in ingresso da Azure Bastion:** Azure Bastion raggiungerà la macchina virtuale di destinazione tramite un indirizzo IP privato. Le porte RDP/SSH (rispettivamente le porte 3389/22) devono essere aperte sul lato della macchina virtuale di destinazione su un indirizzo IP privato. Come procedura consigliata, è possibile aggiungere l'intervallo di indirizzi IP della subnet di Azure Bastion in questa regola per consentire solo a Bastion di aprire queste porte nelle VM di destinazione nella subnet VM di destinazione.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere le [domande frequenti](bastion-faq.md).
