---
title: Esercitazione su come configurare i certificati per il dispositivo Azure Stack Edge Pro R nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Azure Stack Edge Pro R e configurare i certificati nel dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059973"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Esercitazione: Configurare i certificati per il dispositivo Azure Stack Edge Pro R

Questa esercitazione illustra come configurare i certificati per il dispositivo Azure Stack Edge Pro R usando l'interfaccia utente Web locale.

Il tempo impiegato per questo passaggio può variare in base all'opzione specifica scelta e al modo in cui il flusso del certificato viene stabilito nell'ambiente in uso.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico
> * Configurare la VPN
> * Configurare la crittografia dei dati inattivi

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Pro R, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).
* Se si prevede di usare certificati propri:
    - È necessario che i certificati siano pronti nel formato appropriato, incluso il certificato della catena di firma. Per informazioni dettagliate sul certificato, vedere [Gestire i certificati](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Configurare i certificati per il dispositivo

1. I certificati possono essere configurati nella pagina **Certificati**. A seconda che sia stato modificato il nome del dispositivo o il dominio DNS nella pagina **Dispositivo**, è possibile scegliere una delle opzioni seguenti per i certificati.

    - Se il nome dispositivo o il dominio DNS non è stato modificato nel passaggio precedente, è possibile ignorare questo passaggio e procedere con il passaggio successivo. Il dispositivo ha generato automaticamente certificati autofirmati per iniziare. 

    - Se è stato modificato il nome del dispositivo o il dominio DNS, si noterà che lo stato dei certificati viene visualizzato come **Non valido**. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Selezionare un certificato per visualizzare i dettagli dello stato.

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Il motivo è che i certificati non riflettono il nome del dispositivo e il dominio DNS aggiornati (usati nel nome del soggetto e nel nome alternativo del soggetto). Per attivare correttamente il dispositivo, è possibile caricare certificati di endpoint firmati personali e le catene di firma corrispondenti. È necessario prima di tutto aggiungere la catena di firma e quindi caricare i certificati degli endpoint. Per altre informazioni, vedere [Caricare i certificati personali nel dispositivo Azure Stack Edge Pro R](#bring-your-own-certificates).

    - Se è stato modificato il nome dispositivo o il dominio DNS e non si caricano i certificati personali, l'**attivazione verrà bloccata**.

    
#### <a name="bring-your-own-certificates"></a>Caricare i certificati personali

Seguire questa procedura per aggiungere i certificati personali, inclusa la catena di firma.

1. Per caricare il certificato, nella pagina **Certificati** selezionare **+ Aggiungi certificato**.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Caricare prima la catena di firma e selezionare **Validate & add** (Convalida e aggiungi).

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. A questo punto è possibile caricare altri certificati. Ad esempio, è possibile caricare i certificati di Azure Resource Manager e dell'endpoint di archiviazione BLOB.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    È anche possibile caricare il certificato dell'interfaccia utente Web locale. Dopo aver caricato questo certificato, verrà richiesto di riavviare il browser e cancellare la cache. Sarà quindi necessario connettersi all'interfaccia utente Web locale del dispositivo.  

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    È anche possibile caricare il certificato del nodo.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Infine è possibile caricare il certificato della VPN.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    In qualsiasi momento, è possibile selezionare un certificato e visualizzare i dettagli per verificare che corrispondano al certificato caricato.

    La pagina Certificati viene aggiornata per riflettere i nuovi certificati aggiunti.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Ad eccezione del cloud pubblico di Azure, i certificati della catena di firma devono essere caricati prima dell'attivazione per tutte le configurazioni del cloud (Azure per enti pubblici o Azure Stack).

1. Selezionare **< Torna alle Attività iniziali**.

## <a name="configure-vpn"></a>Configurare la VPN

1. Nel riquadro **Sicurezza** selezionare **Configura** per la VPN.

    ![Pagina "VPN" dell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Per configurare la VPN, occorre prima verificare che siano state eseguite tutte le operazioni di configurazione necessarie in Azure. Per informazioni dettagliate, vedere [Configurare i prerequisiti](azure-stack-edge-placeholder.md) e [Configurare le risorse di Azure per la VPN](azure-stack-edge-placeholder.md). Al termine, è possibile eseguire la configurazione nell'interfaccia utente locale.
    
    1. Nella pagina della VPN selezionare **Configura**.
    2. Nel pannello **Configura VPN**:

    - Abilitare **Impostazioni VPN**.
    - Specificare un valore in **Segreto condiviso VPN**. Si tratta della chiave condivisa specificata durante la creazione dell'oggetto connessione VPN di Azure.
    - Specificare un indirizzo in **Indirizzo IP gateway VPN**. È l'indirizzo IP del gateway di rete locale di Azure.
    - In **Gruppo PFS** selezionare **Nessuno**. 
    - In **Gruppo DH** selezionare **Group2**.
    - In **Metodo di integrità IPSec** selezionare **SHA256**.
    - In **Costanti di trasformazione di crittografia IPSec** selezionare **GCMAES256**.
    - In **Costanti di trasformazione dell'autenticazione IPSec** selezionare **GCMAES256**.
    - In **Metodo di crittografia IKE** selezionare **AES256**.
    - Selezionare **Applica**.

        ![Interfaccia utente locale di Configura VPN - 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Per caricare il file di configurazione della route VPN, selezionare **Carica**. 
    
        ![Interfaccia utente locale di Configura VPN - 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Passare al file *JSON* di configurazione della VPN scaricato nel sistema locale nel passaggio precedente.
        - Selezionare l'area di Azure associata al dispositivo, alla rete virtuale e ai gateway.
        - Selezionare **Applica**.
    
            ![Interfaccia utente locale di Configura VPN - 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Per aggiungere route specifiche del client, configurare gli intervalli di indirizzi IP a cui accedere solo tramite VPN. 
    
        - In **Intervalli di indirizzi IP accessibili solo tramite VPN** selezionare **Configura**.
        - Specificare un intervallo IPv4 valido e selezionare **Aggiungi**. Ripetere la procedura per aggiungere altri intervalli.
        - Selezionare **Applica**.
    
            ![Interfaccia utente locale di Configura VPN - 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Selezionare **< Torna alle Attività iniziali**.

## <a name="configure-encryption-at-rest"></a>Configurare la crittografia dei dati inattivi

1. Nel riquadro **Sicurezza** selezionare **Configura** per la crittografia dei dati inattivi. Si tratta di un'impostazione obbligatoria e, finché non viene configurata correttamente, non è possibile attivare il dispositivo. 

    In fase di produzione del dispositivo, dopo la creazione delle immagini dei dispositivi, la crittografia BitLocker a livello di volume è abilitata. Dopo aver ricevuto il dispositivo, è necessario configurare la crittografia dei dati inattivi. Il pool di archiviazione e i relativi volumi vengono ricreati ed è possibile fornire le chiavi BitLocker per abilitare la crittografia dei dati inattivi, creando così un secondo livello di crittografia per i dati inattivi.

1. Nel riquadro **Crittografia dati inattivi** specificare una chiave con codifica Base 64 di 32 caratteri. Questa configurazione viene eseguita una volta sola e questa chiave viene usata per proteggere la chiave di crittografia effettiva. È anche possibile scegliere di generare automaticamente questa chiave o immetterne una.

    ![Riquadro "Crittografia dati inattivi" dell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    La chiave viene salvata in un file nella pagina **Dettagli cloud** dopo l'attivazione del dispositivo.

1. Selezionare **Applica**. Questa operazione richiede diversi minuti e il relativo stato viene visualizzato nel riquadro **Sicurezza**.

    ![Pagina "Crittografia dati inattivi" dell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Quando lo stato diventa **Completato**, selezionare **< Torna alle Attività iniziali**.

Il dispositivo è ora pronto per essere attivato. 


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico
> * Configurare la VPN
> * Configurare la crittografia dei dati inattivi

Per informazioni su come attivare il dispositivo Azure Stack Edge Pro R, vedere:

> [!div class="nextstepaction"]
> [Attivare il dispositivo Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-activate.md)
