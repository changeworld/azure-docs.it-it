---
title: Esercitazione su come configurare i certificati per il dispositivo Azure Stack Edge Mini R nel portale di Azure | Microsoft Docs
description: Esercitazione per la distribuzione di Azure Stack Edge Mini R che illustra come configurare i certificati nel dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: c3a09242b895234c96c64d9e23449d980e47e387
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546738"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Esercitazione: Configurare i certificati, la VPN e la crittografia per Azure Stack Edge Mini R

Questa esercitazione illustra come configurare i certificati, la VPN e la crittografia dei dati inattivi per il dispositivo Azure Stack Edge Mini R usando l'interfaccia utente Web locale.

Il tempo impiegato per questo passaggio può variare in base all'opzione specifica scelta e al modo in cui il flusso del certificato viene stabilito nell'ambiente in uso.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico
> * Configurare la VPN
> * Configurare la crittografia dei dati inattivi

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Mini R, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).

* Se si prevede di usare certificati propri:
    - È necessario che i certificati siano pronti nel formato appropriato, incluso il certificato della catena di firma. Per informazioni dettagliate sul certificato, vedere [Gestire i certificati](azure-stack-edge-gpu-manage-certificates.md)

    - Se il dispositivo è distribuito nel cloud di Azure per enti pubblici o Azure Secret per enti pubblici o Azure Top Secret per enti pubblici e non è distribuito nel cloud pubblico di Azure, è necessario un certificato della catena di firma per attivare il dispositivo. 
    Per informazioni dettagliate sul certificato, vedere [Gestire i certificati](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Configurare i certificati per il dispositivo

1. I certificati possono essere configurati nella pagina **Certificati**. A seconda che sia stato modificato il nome del dispositivo o il dominio DNS nella pagina **Dispositivo**, è possibile scegliere una delle opzioni seguenti per i certificati.

    - Se il nome dispositivo predefinito o il dominio DNS predefinito non è stato modificato nel passaggio precedente e non si vogliono caricare certificati personali, è possibile ignorare questo passaggio e procedere con il passaggio successivo. Il dispositivo ha generato automaticamente certificati autofirmati per iniziare. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Se è stato modificato il nome del dispositivo o il dominio DNS, si noterà che lo stato dei certificati viene visualizzato come **Non valido**. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Selezionare un certificato per visualizzare i dettagli dello stato.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Lo stato del certificato è **Non valido** perché i certificati non riflettono il nome dispositivo e il dominio DNS aggiornati (usati nel nome del soggetto e nel nome alternativo del soggetto). Per attivare correttamente il dispositivo, è possibile caricare certificati di endpoint firmati personali e le catene di firma corrispondenti. È necessario prima di tutto aggiungere la catena di firma e quindi caricare i certificati degli endpoint. Per altre informazioni, vedere [Caricare i certificati personali](#bring-your-own-certificates).


    - Se è stato modificato il nome dispositivo o il dominio DNS e non si caricano i certificati personali, l'**attivazione verrà bloccata**.


#### <a name="bring-your-own-certificates"></a>Caricare i certificati personali

La catena di firma è già stata aggiunta in questo dispositivo in un passaggio precedente. È ora possibile caricare i certificati di endpoint, il certificato del nodo, il certificato dell'interfaccia utente locale e il certificato della VPN. Seguire questa procedura per aggiungere i certificati personali.

1. Per caricare il certificato, nella pagina **Certificati** selezionare **+ Aggiungi certificato**.

    [![Pagina "Certificati" dell'interfaccia utente Web locale - 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. A questo punto si possono caricare altri certificati. Ad esempio, è possibile caricare i certificati di Azure Resource Manager e dell'endpoint di archiviazione BLOB.

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. È anche possibile caricare il certificato dell'interfaccia utente Web locale. Dopo aver caricato questo certificato, verrà richiesto di riavviare il browser e cancellare la cache. Sarà quindi necessario connettersi all'interfaccia utente Web locale del dispositivo.  

    ![Pagina "Certificati" dell'interfaccia utente Web locale - 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. È anche possibile caricare il certificato del nodo.


    ![Pagina "Certificati" dell'interfaccia utente Web locale - 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Infine, è possibile caricare il certificato della VPN.
        
    ![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. In qualsiasi momento, è possibile selezionare un certificato e visualizzare i dettagli per verificare che corrispondano al certificato caricato.

    [![Pagina "Certificati" dell'interfaccia utente Web locale - 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    La pagina Certificati viene aggiornata per riflettere i nuovi certificati aggiunti.

    [![Pagina "Certificati" dell'interfaccia utente Web locale - 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Ad eccezione del cloud pubblico di Azure, i certificati della catena di firma devono essere caricati prima dell'attivazione per tutte le configurazioni cloud (Azure per enti pubblici o hub di Azure Stack).


## <a name="configure-vpn"></a>Configurare la VPN

1. Nel riquadro **Sicurezza** selezionare **Configura** per la VPN. 

    Per configurare la VPN, occorre prima verificare che siano state eseguite tutte le operazioni di configurazione necessarie in Azure. Per informazioni dettagliate, vedere [Configurare la VPN tramite PowerShell per il dispositivo Azure Stack Edge Mini R](azure-stack-edge-placeholder.md). Al termine, è possibile eseguire la configurazione nell'interfaccia utente locale.
    
    1. Nella pagina della VPN selezionare **Configura**.
        ![Configurazione della VPN nell'interfaccia utente locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. Nel pannello **Configura VPN**:

        1. Specificare la rubrica telefonica come input.
        2. Specificare il file JSON dell'intervallo IP del data center di Azure come input. Scaricare questo file da [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519).
        3. Selezionare **eastus** come area.
        4. Selezionare **Applica**.

        ![Configurazione della VPN nell'interfaccia utente locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Configurare gli intervalli di indirizzi IP a cui accedere solo tramite VPN. 
    
        - In **Intervalli di indirizzi IP accessibili solo tramite VPN** selezionare **Configura**.
        - Immettere l'intervallo IPv4 della VNET scelto per la rete virtuale di Azure.
        - Selezionare **Applica**.
    
        ![Configurazione della VPN nell'interfaccia utente locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Il dispositivo è ora pronto per essere crittografato. Configurare la crittografia dei dati inattivi.


## <a name="enable-encryption"></a>Abilitare la crittografia

1. Nel riquadro **Sicurezza** selezionare **Configura** per la crittografia dei dati inattivi. Si tratta di un'impostazione obbligatoria e, finché non viene configurata correttamente, non è possibile attivare il dispositivo. 

    ![Pagina "Crittografia dati inattivi" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    In fase di produzione del dispositivo, dopo la creazione delle immagini dei dispositivi, la crittografia BitLocker a livello di volume è abilitata. Dopo aver ricevuto il dispositivo, è necessario configurare la crittografia dei dati inattivi. Il pool di archiviazione e i relativi volumi vengono ricreati ed è possibile fornire le chiavi BitLocker per abilitare la crittografia dei dati inattivi, creando così un secondo livello di crittografia per i dati inattivi.

1. Nel riquadro **Crittografia dati inattivi** immettere una chiave con codifica Base 64 (AES a 256 bit) di 32 caratteri. Questa configurazione viene eseguita una volta sola e questa chiave viene usata per proteggere la chiave di crittografia effettiva. 

    ![Pagina "Crittografia dati inattivi" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    È anche possibile scegliere di generare automaticamente questa chiave.

    ![Pagina "Crittografia dati inattivi" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Selezionare **Applica**. Questa operazione richiede diversi minuti e il relativo stato viene visualizzato nel riquadro **Sicurezza**.

    ![Pagina "Crittografia dati inattivi" dell'interfaccia utente Web locale - 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Quando lo stato diventa **Completato**, tornare a **Attività iniziali**.

Il dispositivo è ora pronto per essere attivato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare i certificati per il dispositivo fisico
> * Configurare la VPN
> * Configurare la crittografia dei dati inattivi

Per informazioni su come attivare il dispositivo Azure Stack Edge Mini R, vedere:

> [!div class="nextstepaction"]
> [Attivare il dispositivo Azure Stack Edge Mini R](./azure-stack-edge-mini-r-deploy-activate.md)
