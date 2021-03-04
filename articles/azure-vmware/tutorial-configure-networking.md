---
title: 'Esercitazione: Configurare la rete per il cloud privato VMware in Azure'
description: Informazioni su come creare e configurare la rete necessaria per distribuire il cloud privato in Azure
ms.topic: tutorial
ms.date: 02/23/2021
ms.openlocfilehash: 8871e678ba43aa69eb49c02e386bfb47c6871de7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044979"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Esercitazione: Configurare la rete per il cloud privato VMware in Azure

Un cloud privato della soluzione Azure VMware richiede una rete virtuale di Azure. Poiché la soluzione Azure VMware non supporta un'istanza locale di vCenter, è necessario completare altri passaggi per l'integrazione con l'ambiente locale. È necessaria anche la configurazione di un circuito ExpressRoute e di un gateway di rete virtuale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

## <a name="prerequisites"></a>Prerequisiti 
Una rete virtuale creata in un [cloud privato della soluzione Azure VMware](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare al gruppo di risorse creato nell'[esercitazione sulla creazione di un cloud privato](tutorial-create-private-cloud.md) e selezionare **+ Aggiungi** per definire una nuova risorsa. 

1. Nella casella di testo **Cerca nel Marketplace** digitare **Rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

1. Nella pagina **Rete virtuale** selezionare **Crea** per configurare la rete virtuale per il cloud privato.

1. Nella pagina **Crea rete virtuale** specificare i dettagli per la rete virtuale.

1. Nella scheda **nozioni di base** immettere un nome per la rete virtuale, selezionare l'area appropriata e quindi fare clic su **Avanti: indirizzi IP**.

1. Nella scheda **Indirizzi IP**, in **Spazio indirizzi IPv4**, immettere lo spazio indirizzi creato nell'esercitazione precedente.

   > [!IMPORTANT]
   > È necessario usare uno spazio indirizzi che **non** si sovrappone a quello usato per creare il cloud privato nell'esercitazione precedente.

1. Selezionare **+ Aggiungi subnet**, quindi nella pagina **Aggiungi subnet** assegnare alla subnet un nome e un intervallo di indirizzi appropriato. Al termine selezionare **Aggiungi**.

1. Selezionare **Rivedi e crea**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Selezionare Rivedi e crea." border="true":::

1. Verificare le informazioni e selezionare **Crea**. Al termine della distribuzione, la rete virtuale verrà visualizzata nel gruppo di risorse.

## <a name="create-a-virtual-network-gateway"></a>Creare un gateway di rete virtuale

Dopo aver creato la rete virtuale, si procederà alla creazione di un gateway di rete virtuale.

1. Nel gruppo di risorse selezionare **+ Aggiungi** per aggiungere una nuova risorsa.

1. Nella casella di testo **Cerca nel Marketplace** digitare **Gateway di rete virtuale**. Individuare la risorsa Rete virtuale e selezionarla.

1. Nella pagina **Gateway di rete virtuale** selezionare **Crea**.

1. Nella scheda Generale della pagina **Crea gateway di rete virtuale** specificare i valori per i campi e quindi selezionare **Rivedi e crea**. 

   | Campo | Valore |
   | --- | --- |
   | **Sottoscrizione** | Valore già popolato con la sottoscrizione a cui appartiene il gruppo di risorse. |
   | **Gruppo di risorse** | Valore già popolato con il gruppo di risorse corrente. Il valore dovrebbe essere il gruppo di risorse creato nella sezione precedente. |
   | **Nome** | Immettere un nome univoco per il gateway di rete virtuale. |
   | **Area** | Selezionare la posizione geografica del gateway di rete virtuale. |
   | **Tipo di gateway** | selezionare **ExpressRoute**. |
   | **SKU** | Lasciare il valore predefinito **Standard**. |
   | **Rete virtuale** | Selezionare la rete virtuale creata in precedenza. Se la rete virtuale non è visibile, assicurarsi che l'area del gateway corrisponda all'area della rete virtuale. |
   | **Intervallo di indirizzi subnet del gateway** | Questo valore viene popolato quando si seleziona la rete virtuale. Non modificare il valore predefinito. |
   | **Indirizzo IP pubblico** | Selezionare **Crea nuovo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Specificare valori per i campi e quindi selezionare Rivedi e crea." border="true":::

1. Verificare che i dettagli siano corretti e selezionare **Crea** per avviare la distribuzione del gateway di rete virtuale. 
1. Al termine della distribuzione, passare alla sezione successiva per connettere ExpressRoute al gateway di rete virtuale che contiene il cloud privato della soluzione Azure VMware.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Connettere ExpressRoute al gateway di rete virtuale

Ora che è stato distribuito un gateway di rete virtuale, verrà aggiunta una connessione tra il gateway e il cloud privato della soluzione Azure VMware.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Individuare gli URL per vCenter e NSX Manager

Per accedere a vCenter e NSX Manager, sono necessari gli URL per il client Web vCenter e il sito di gestione NSX-T. 

Passare al cloud privato della soluzione Azure VMware e quindi, in **Gestisci**, selezionare **Identità** per trovare le informazioni necessarie.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Screenshot delle credenziali vCenter e NSX-T e degli URL dei client Web." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway di rete virtuale
> * Connettere il circuito ExpressRoute al gateway
> * Individuare gli URL per vCenter e NSX Manager

Passare all'esercitazione successiva per informazioni su come creare i segmenti di rete NSX-T usati per le VM in vCenter.

> [!div class="nextstepaction"]
> [Creare un segmento di rete NSX-T](tutorial-nsx-t-network-segment.md)