---
title: 'Esercitazione: Dimensionare un cloud privato'
description: In questa esercitazione si usa il portale di Azure per dimensionare un cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 2129a3f5d04311883369b7b708689a13f07ec118
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463611"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Dimensionare un cloud privato della soluzione Azure VMware

Per sfruttare al meglio l'esperienza del cloud privato della soluzione Azure VMware, dimensionare i cluster e gli host in base alle esigenze dei carichi di lavoro pianificati. È possibile ridimensionare i cluster e gli host in un cloud privato, se necessario per il carico di lavoro dell'applicazione. Le limitazioni relative alle prestazioni e alla disponibilità di servizi specifici dovranno essere risolte caso per caso. I limiti relativi a cluster e host sono descritti nell'[articolo sui concetti del cloud privato](concepts-private-clouds-clusters.md).

In questa esercitazione verrà usato il portale di Azure per:

> [!div class="checklist"]
> * Aggiungere un cluster a un cloud privato esistente
> * Aggiungere host a un cluster esistente

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario un cloud privato esistente. Se non è stato creato un cloud privato, usare l'esercitazione [Creare un cloud privato](tutorial-create-private-cloud.md) per crearne uno. 

## <a name="add-a-new-cluster"></a>Aggiungere un nuovo cluster

1. Nella pagina Panoramica di un cloud privato esistente, in **Gestisci**, selezionare **Scale private cloud** (Ridimensiona cloud privato). Selezionare quindi **+ Aggiungi cluster**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Selezionare Aggiungi cluster" border="true":::

1. Nella pagina **Aggiungi cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Nella pagina Aggiungi cluster usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare Salva." border="true":::

   Verrà avviata la distribuzione del nuovo cluster.

## <a name="scale-a-cluster"></a>Ridimensionare un cluster 

1. Nella pagina Panoramica di un cloud privato esistente selezionare **Scale private cloud** (Ridimensiona cloud privato) e selezionare l'icona della matita per modificare il cluster.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Selezionare Scale private cloud in Panoramica" border="true":::

1. Nella pagina **Modifica cluster** usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare **Salva**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Nella pagina Modifica cluster usare il dispositivo di scorrimento per selezionare il numero di host. Selezionare Salva." border="true":::

   Viene avviata l'aggiunta di host al cluster.

## <a name="next-steps"></a>Passaggi successivi

Se necessario, [creare un altro cloud privato](tutorial-create-private-cloud.md) della soluzione Azure VMware rispettando gli stessi prerequisiti di rete e i limiti di cluster e host.

<!-- LINKS - external-->

<!-- LINKS - internal -->
