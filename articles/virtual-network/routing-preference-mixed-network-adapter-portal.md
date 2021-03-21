---
title: Configurare entrambe le opzioni di preferenza di routing per una macchina virtuale-portale di Azure
description: Informazioni su come abilitare entrambe le opzioni per le preferenze di routing
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679659"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Configurare entrambe le opzioni di preferenza di routing per una macchina virtuale

Questo articolo illustra come configurare entrambe le opzioni di [preferenza di routing](./routing-preference-overview.md) (Internet e rete globale Microsoft) per una macchina virtuale (VM). Questa operazione viene eseguita usando due interfacce di rete virtuale, un'interfaccia di rete con un indirizzo IP pubblico instradato tramite la rete globale Microsoft e l'altra con un indirizzo IP pubblico instradato tramite una rete ISP.

## <a name="prerequisites"></a>Prerequisiti

Creare una macchina virtuale con un indirizzo IP pubblico seguendo le istruzioni descritte in [creare una macchina virtuale con un indirizzo IP pubblico statico usando il portale di Azure](./virtual-network-deploy-static-pip-arm-portal.md) usando il portale di Azure. La scelta di preferenza di routing predefinita per l'indirizzo IP pubblico è tramite la **rete globale Microsoft**. Una volta creata una macchina virtuale con un indirizzo IP pubblico, aggiungere un secondo indirizzo IP pubblico alla macchina virtuale che instrada il traffico attraverso la rete ISP di transito con la preferenza di routing configurata come **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Creare un indirizzo IP pubblico con una scelta preferenziale per il routing Internet
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca digitare *indirizzo IP pubblico*.
4. Nei risultati della ricerca selezionare **Indirizzo IP pubblico**. Quindi, nella pagina **Indirizzo IP pubblico** selezionare **Crea**.
5. Nelle opzioni **Preferenza di routing** selezionare **Internet**.

      ![Creare un indirizzo IP pubblico](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Gli indirizzi IP pubblici vengono creati con un indirizzo IPv4 o IPv6. Tuttavia, attualmente la preferenza di routing supporta solo IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Creare un'interfaccia di rete e associare l'indirizzo IP pubblico

1. Creare un' [interfaccia di rete](./routing-preference-overview.md) usando il portale di Azure con le impostazioni predefinite. 
1. [Associare l'indirizzo IP pubblico all'interfaccia di rete](./associate-public-ip-address-vm.md) creata nella sezione precedente. Potrebbero essere necessari alcuni secondi per la visualizzazione di un indirizzo IP. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP, come illustrato:

    ![Associare l'indirizzo IP pubblico](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Collegare l'interfaccia di rete alla macchina virtuale

1. [Collegare l'interfaccia di rete creata nella sezione precedente alla macchina virtuale](./virtual-network-network-interface-vm.md).
2. È ora possibile visualizzare due interfacce di rete virtuale associate alla macchina virtuale, una con un indirizzo IP pubblico instradato tramite la rete globale Microsoft e l'altra indirizzata tramite una rete ISP come indicato:  ![ collegare un'interfaccia di rete a una VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [indirizzi IP pubblici con preferenza di routing](routing-preference-overview.md).
- [Configurare la preferenza di routing per una VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configurare la preferenza di routing per un indirizzo IP pubblico con PowerShell](routing-preference-powershell.md).
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).