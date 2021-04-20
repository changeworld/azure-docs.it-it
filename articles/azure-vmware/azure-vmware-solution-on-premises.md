---
title: Connettere la soluzione Azure VMware con l'ambiente locale
description: Informazioni su come connettere la soluzione Azure VMware con l'ambiente locale.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725620"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Connettere la soluzione Azure VMware con l'ambiente locale

In questo articolo si continueranno a usare le [informazioni raccolte durante la pianificazione](production-ready-deployment-steps.md) per connettere la soluzione Azure VMware con l'ambiente locale.

Prima di iniziare, è necessario avere un circuito ExpressRoute dall'ambiente locale ad Azure.


>[!NOTE]
> È possibile stabilire la connessione tramite VPN, ma questa procedura non rientra nell'ambito di questo documento di avvio rapido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Stabilire una connessione di Copertura globale ExpressRoute

Per stabilire la connettività locale al cloud privato della soluzione Azure VMware usando Copertura globale ExpressRoute, seguire l'esercitazione [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md).

Questa esercitazione determina una connessione, come illustrato nel diagramma.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Copertura globale di connettività di rete locale." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verificare la connettività alla rete locale

Nel **router perimetrale locale** dovrebbe essere ora possibile verificare la posizione in cui ExpressRoute si connette ai segmenti di rete NSX-T e ai segmenti di gestione della soluzione Azure VMware.

>[!IMPORTANT]
>Ogni utente ha un ambiente diverso e in alcuni casi sarà necessario consentire la propagazione di tali route fino alla rete locale.  

Alcuni ambienti hanno firewall che proteggono il circuito ExpressRoute.  Se non sono presenti firewall e non si verifica l'eliminazione di route, effettuare il ping del server vCenter della soluzione Azure VMware o di una [VM nel segmento NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) dall'ambiente locale. Dalla VM nel segmento NSX-T è inoltre possibile raggiungere le risorse nell'ambiente locale.

## <a name="next-steps"></a>Passaggi successivi

Passare alla sezione successiva per distribuire e configurare VMware HCX

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)