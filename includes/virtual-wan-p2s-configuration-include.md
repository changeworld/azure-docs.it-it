---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048259"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Passare a **tutte le risorse** e selezionare la rete WAN virtuale creata, quindi selezionare **configurazioni VPN utente** dal menu a sinistra.
1. Nella pagina **configurazioni VPN utente** selezionare **+ Crea configurazione VPN utente** nella parte superiore della pagina per aprire la pagina di configurazione per la creazione di una **nuova VPN utente** .

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Screenshot della pagina configurazioni VPN utente.":::

1. Nella scheda informazioni di **base** , in **Dettagli istanza**, immettere il **nome** che si vuole assegnare alla configurazione VPN.
1. Per **tipo di tunnel**, dall'elenco a discesa, selezionare il tipo di tunnel desiderato. Le opzioni relative al tipo di tunnel sono: **IKEV2 VPN**, **OpenVPN**, OpenVPN **e IKEv2**.
1. Usare i passaggi seguenti che corrispondono al tipo di tunnel selezionato. Una volta specificati tutti i valori, fare clic su **Verifica + crea** e quindi su **Crea** per creare la configurazione.

   **VPN IKEv2**

   * **Requisiti:** Quando si seleziona il tipo di tunnel **IKEv2** , viene visualizzato un messaggio che indica di selezionare un metodo di autenticazione. Per IKEv2, è possibile specificare un solo metodo di autenticazione. È possibile scegliere il certificato di Azure, Azure Active Directory o l'autenticazione basata su RADIUS.

   * **Parametri personalizzati IPSec:** Per personalizzare i parametri per la fase 1 e la fase 2 di IKE, impostare l'opzione IPsec su **Custom** e selezionare i valori dei parametri. Per ulteriori informazioni sui parametri personalizzabili, vedere l'articolo relativo a [IPsec personalizzato](../articles/virtual-wan/point-to-site-ipsec.md) .

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Screenshot del passaggio IPsec a Custom.":::

   * **Autenticazione:** Passare al meccanismo di autenticazione che si vuole usare facendo clic su **Avanti** nella parte inferiore della pagina per passare al metodo di autenticazione oppure fare clic sulla scheda appropriata nella parte superiore della pagina. Impostare l'opzione su **Sì** per selezionare il metodo.

     In questo esempio è selezionata l'autenticazione RADIUS. Per l'autenticazione basata su RADIUS, è possibile specificare un indirizzo IP del server RADIUS secondario e un segreto server.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Screenshot di IKE.":::

   **OpenVPN**

   * **Requisiti:** Quando si seleziona il tipo di tunnel **OpenVPN** , viene visualizzato un messaggio che indica di selezionare un meccanismo di autenticazione. Se si seleziona OpenVPN come tipo di tunnel, è possibile specificare più metodi di autenticazione. È possibile scegliere qualsiasi subset di certificati di Azure, Azure Active Directory o l'autenticazione basata su RADIUS. Per l'autenticazione basata su RADIUS, è possibile specificare un indirizzo IP del server RADIUS secondario e un segreto server.

   * **Autenticazione:** Passare al metodo di autenticazione che si vuole usare facendo clic su **Avanti** nella parte inferiore della pagina per passare al metodo di autenticazione oppure fare clic sulla scheda appropriata nella parte superiore della pagina.
   Per ogni metodo che si desidera selezionare, impostare l'opzione su **Sì** e immettere i valori appropriati.

     In questo esempio Azure Active Directory è selezionata.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Screenshot della pagina OpenVPN.":::
