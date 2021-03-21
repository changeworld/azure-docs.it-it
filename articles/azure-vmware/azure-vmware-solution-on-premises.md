---
title: Connettere la soluzione Azure VMware con l'ambiente locale
description: Informazioni su come connettere la soluzione Azure VMware con l'ambiente locale.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491793"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Connettere la soluzione Azure VMware con l'ambiente locale

In questo articolo si continueranno a usare le [informazioni raccolte durante la pianificazione](production-ready-deployment-steps.md) per connettere la soluzione Azure VMware con l'ambiente locale.

Prima di iniziare, sono necessari due prerequisiti per la connessione della soluzione Azure VMware con l'ambiente locale:

- Un circuito ExpressRoute dall'ambiente locale ad Azure.
- Un blocco di indirizzi di rete CIDR non sovrapposto per ExpressRoute Copertura globale peering, definito come parte della [fase di pianificazione](production-ready-deployment-steps.md).

>[!NOTE]
> È possibile stabilire la connessione tramite VPN, ma questa procedura non rientra nell'ambito di questo documento di avvio rapido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Stabilire una connessione di Copertura globale ExpressRoute

Per stabilire la connettività locale al cloud privato della soluzione Azure VMware usando Copertura globale ExpressRoute, seguire l'esercitazione [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md).

Questa esercitazione genera una connessione, come illustrato nel diagramma.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Copertura globale diagramma della connettività di rete locale." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verificare la connettività alla rete locale

Nel **router perimetrale locale** dovrebbe essere ora possibile verificare la posizione in cui ExpressRoute si connette ai segmenti di rete NSX-T e ai segmenti di gestione della soluzione Azure VMware.

>[!IMPORTANT]
>Ogni utente ha un ambiente diverso e in alcuni casi sarà necessario consentire la propagazione di tali route fino alla rete locale.  

Alcuni ambienti hanno firewall che proteggono il circuito ExpressRoute.  Se non sono presenti firewall e non si verifica l'eliminazione di route, effettuare il ping del server vCenter della soluzione Azure VMware o di una [VM nel segmento NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) dall'ambiente locale. Dalla VM nel segmento NSX-T è inoltre possibile raggiungere le risorse nell'ambiente locale.

## <a name="next-steps"></a>Passaggi successivi

Passare alla sezione successiva per distribuire e configurare VMware HCX

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)