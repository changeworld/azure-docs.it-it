---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467372"
---
Eseguire i passaggi seguenti nell'interfaccia utente Web locale del dispositivo. Questo passaggio richiede circa 15 minuti, incluso il caricamento del file di configurazione VPN (o il file di tag del servizio). 

1. Passare a **Configuration > VPN**. Selezionare **Configura**.

    ![Configura interfaccia utente locale 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Nel pannello **Configura VPN**:

    - Abilitare **Impostazioni VPN**.
    - Specificare un valore in **Segreto condiviso VPN**. Si tratta della chiave condivisa fornita durante la creazione della risorsa di connessione VPN di Azure.
    - Specificare un indirizzo in **Indirizzo IP gateway VPN**. È l'indirizzo IP del gateway di rete locale di Azure.
    - In **Gruppo PFS** selezionare **Nessuno**. 
    - In **Gruppo DH** selezionare **Group2**.
    - In **Metodo di integrità IPSec** selezionare **SHA256**.
    - Per le **costanti di trasformazione crittografia IPSec** selezionare **GCMAES256**.
    - In **Costanti di trasformazione dell'autenticazione IPSec** selezionare **GCMAES256**.
    - In **Metodo di crittografia IKE** selezionare **AES256**.
    - Selezionare **Applica**.

        ![Interfaccia utente locale di Configura VPN - 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Per altre informazioni sugli algoritmi di crittografia supportati, vedere [informazioni sui requisiti crittografici e sui gateway VPN di Azure](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Per caricare il file di configurazione della route VPN, selezionare **Carica**. 

    ![Interfaccia utente locale di Configura VPN - 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Passare al file *JSON* dei tag di servizio scaricato nel sistema locale nel passaggio precedente.
    - Selezionare l'area di Azure associata al dispositivo, alla rete virtuale e ai gateway.
    - Selezionare **Applica**.

        ![Interfaccia utente locale di Configura VPN - 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Il caricamento richiede circa 7-8 minuti sul dispositivo.

4. Per aggiungere route specifiche del client, configurare gli intervalli di indirizzi IP a cui accedere solo tramite VPN. 

    - In **Intervalli di indirizzi IP accessibili solo tramite VPN** selezionare **Configura**.
    - Specificare un intervallo IPv4 valido e selezionare **Aggiungi**. Ripetere la procedura per aggiungere altri intervalli.
    - Selezionare **Applica**.

        ![Interfaccia utente locale di Configura VPN - 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

