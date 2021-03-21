---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730648"
---
Connettersi alla VM Windows usando il Remote Desktop Protocol (RDP) tramite l'indirizzo IP passato durante la creazione della macchina virtuale.

1. Sul client aprire RDP. 
1. Passare a **Start**, quindi digitare **mstsc**.
1. Nel riquadro **Connessione desktop remoto** , immettere l'indirizzo IP della macchina virtuale e le credenziali di accesso usate nel file dei parametri del modello di macchina virtuale e quindi selezionare **Connetti**.

   ![Screenshot del riquadro Connessione Desktop remoto per la connessione tramite RDP alla macchina virtuale Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Potrebbe essere necessario approvare la connessione a un computer non attendibile. 

A questo punto è stato effettuato l'accesso alla macchina virtuale in esecuzione nell'appliance. 
