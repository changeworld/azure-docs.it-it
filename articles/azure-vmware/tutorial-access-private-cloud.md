---
title: Esercitazione - Accedere al cloud privato
description: Informazioni su come accedere a un cloud privato della soluzione Azure VMware
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 81296223ab941633a4b0f1316e359a0eb2ff73ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738481"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Accedere a un cloud privato della soluzione Azure VMware

La soluzione Azure VMware non consente di gestire il cloud privato con un'istanza locale di vCenter. Sarà necessario eseguire altre operazioni di configurazione e connessione a un'istanza locale di vCenter tramite una jump box. 

In questa esercitazione si creerà una jump box nel gruppo di risorse creato nell'[esercitazione precedente](tutorial-configure-networking.md) e verrà eseguito l'accesso a vCenter. La jump box è una macchina virtuale Windows nella stessa rete virtuale creata.  Fornisce l'accesso a vCenter e NSX Manager. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows da usare per connettersi a vCenter
> * Accedere a vCenter dalla macchina virtuale

## <a name="create-a-new-windows-virtual-machine"></a>Creare una nuova macchina virtuale Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Connettersi all'istanza locale di vCenter del cloud privato

1. Dalla jump box accedere a vSphere Client con VMware vCenter SSO usando un nome utente amministratore e verificare che l'interfaccia utente venga visualizzata correttamente.

1. Nella portale di Azure selezionare il cloud privato e quindi gestire l'   >  **identità**. 

   Vengono visualizzati gli URL e le credenziali dell'utente per vCenter e NSX-T Manager del cloud privato.

   >[!TIP]
   >Selezionare **genera una nuova password** per generare nuove password vCenter e NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visualizzazione di URL e credenziali di vCenter e NSX Manager del cloud privato." border="true":::

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


