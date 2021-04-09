---
title: Configurare la preferenza di routing per un indirizzo IP pubblico - Portale di Azure
description: Informazioni su come creare un indirizzo IP pubblico con una preferenza di routing per il traffico Internet
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
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672921"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configurare la preferenza di routing per un indirizzo IP pubblico con il portale di Azure

Questo articolo illustra come configurare le [Preferenze di routing](./routing-preference-overview.md) tramite la rete ISP (opzione **Internet** ) per un indirizzo IP pubblico. Dopo aver creato l'indirizzo IP pubblico, è possibile associarlo alle risorse di Azure seguenti per il traffico Internet in ingresso e in uscita:

* Macchina virtuale
* Set di scalabilità di macchine virtuali
* Servizio Azure Kubernetes
* Servizio di bilanciamento del carico Internet
* Gateway applicazione
* Firewall di Azure

Per impostazione predefinita, la preferenza di routing per l'indirizzo IP pubblico è impostata sulla rete globale Microsoft per tutti i servizi di Azure e può essere associata a qualsiasi servizio di Azure.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Creare un IP pubblico con una preferenza di routing
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**.
3. Nella casella di ricerca digitare *indirizzo IP pubblico*.
3. Nei risultati della ricerca selezionare **Indirizzo IP pubblico**. Quindi, nella pagina **Indirizzo IP pubblico** selezionare **Crea**.
1. Per SKU selezionare **standard**.
1. Per **preferenza routing** selezionare **Internet**.

      ![Creare un indirizzo IP pubblico](./media/routing-preference-portal/public-ip-new.png)
1. Nella sezione **configurazione degli indirizzi IP IPv4** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *RoutingPreferenceResourceGroup*, quindi fare clic su **OK**. |
    | Location | Selezionare **Stati Uniti orientali**.|
    | Zona di disponibilità | Mantieni il valore predefinito con **ridondanza della zona**. |
1. Selezionare **Crea**.

    > [!NOTE]
    > Gli indirizzi IP pubblici vengono creati con un indirizzo IPv4 o IPv6. Tuttavia, attualmente la preferenza di routing supporta solo IPV4.

È possibile associare l'indirizzo IP pubblico creato in precedenza a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Seguire la sezione per l'interfaccia della riga di comando nella pagina dell'esercitazione: [Associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md#azure-cli) per associare l'indirizzo IP pubblico alla VM. È anche possibile associare l'indirizzo IP pubblico creato in precedenza a un'istanza di [Azure Load Balancer](../load-balancer/load-balancer-overview.md), assegnandolo alla configurazione **front-end** del servizio. L'indirizzo IP pubblico viene usato come indirizzo IP virtuale (indirizzo VIP) di bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [indirizzi IP pubblici con preferenza di routing](routing-preference-overview.md).
- [Configurare la preferenza di routing per una VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configurare la preferenza di routing per un indirizzo IP pubblico con PowerShell](routing-preference-powershell.md).
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).