---
title: Esercitazione - Accedere al cloud privato
description: Informazioni su come accedere a un cloud privato della soluzione Azure VMware
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494394"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Accedere a un cloud privato della soluzione Azure VMware

La soluzione Azure VMware non consente di gestire il cloud privato con un'istanza locale di vCenter. È necessario connettersi all'istanza di vCenter della soluzione VMware di Azure tramite una finestra di salto. 

In questa esercitazione si creerà una casella di salto nel gruppo di risorse creato nell' [esercitazione precedente](tutorial-configure-networking.md) e si accederà alla soluzione VMware di Azure per vCenter. Questa jump box è una macchina virtuale (VM) Windows nella stessa rete virtuale creata.  Consente di accedere a vCenter e a gestione NSX. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows per accedere alla soluzione VMware di Azure vCenter
> * Accedere a vCenter da questa macchina virtuale

## <a name="create-a-new-windows-virtual-machine"></a>Creare una nuova macchina virtuale Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Connettersi all'istanza locale di vCenter del cloud privato

1. Nella casella di salto accedere a vSphere client con VMware vCenter SSO usando un nome utente amministratore cloud e verificare che l'interfaccia utente venga visualizzata correttamente.

1. Nella portale di Azure selezionare il cloud privato e quindi **gestire** l'  >  **identità**. 

   Vengono visualizzati gli URL e le credenziali dell'utente per vCenter e NSX-T Manager del cloud privato.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visualizzazione di URL e credenziali di vCenter e NSX Manager del cloud privato." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Passare alla macchina virtuale creata nel passaggio precedente e connettersi. 

   Se è necessaria assistenza per la connessione alla VM, vedere [Connettersi a una macchina virtuale](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) per altri dettagli.

1. Nella VM Windows aprire un browser e passare agli URL di vCenter e NSX-T Manager in due schede. 

1. Nella scheda vCenter immettere le credenziali utente `cloudadmin@vmcp.local` del passaggio precedente.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Accedere a vCenter del cloud privato." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portale di vCenter." border="true":::

1. Nella seconda scheda del browser accedere a NSX-T Manager.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Nella seconda scheda del browser accedere a NSX-T Manager." border="true":::



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows da usare per connettersi a vCenter
> * Accedere a vCenter dalla macchina virtuale

Continuare con l'esercitazione successiva per informazioni su come creare una rete virtuale per configurare la gestione locale per i cluster del cloud privato.

> [!div class="nextstepaction"]
> [Creare una rete virtuale](tutorial-configure-networking.md)


