---
title: Esercitazione per preparare il portale di Azure e l'ambiente del data center per la distribuzione del dispositivo Azure Stack Edge Mini R | Microsoft Docs
description: La prima esercitazione sulla distribuzione del dispositivo Azure Stack Edge Mini R include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 54ac683dcc2b124c4a6410b2e8449fa7e969ce8c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464908"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Esercitazione: Preparare la distribuzione di Azure Stack Edge Mini R

Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente il dispositivo Azure Stack Edge Mini R. Questa esercitazione descrive come preparare il portale di Azure per la distribuzione di una risorsa Azure Stack Edge.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

### <a name="get-started"></a>Introduzione

Per distribuire Azure Stack Edge Mini R, fare riferimento alle esercitazioni seguenti nella sequenza indicata.

| Passaggio | Descrizione |
| --- | --- |
| **Preparazione** |Questi passaggi devono essere completati per preparare la successiva distribuzione. |
| **[Elenco di controllo configurazione della distribuzione](#deployment-configuration-checklist)** |Usare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| **[Prerequisiti di distribuzione](#prerequisites)** |Questi confermano che l'ambiente è pronto per la distribuzione. |
|  | |
|**Esercitazioni sulla distribuzione** |Queste esercitazioni sono necessarie per distribuire il dispositivo Azure Stack Edge Mini R nell'ambiente di produzione. |
|**[1. Preparare il portale di Azure per il dispositivo](azure-stack-edge-mini-r-deploy-prep.md)** |Creare e configurare la risorsa Azure Stack Edge prima di installare il dispositivo fisico. |
|**[2. Installare il dispositivo](azure-stack-edge-mini-r-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico.  |
|**[3. Connettersi al dispositivo](azure-stack-edge-mini-r-deploy-connect.md)** |Dopo aver installato il dispositivo, connettersi all'interfaccia utente Web locale del dispositivo.  |
|**[4. Configurare le impostazioni di rete](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Configurare la rete, incluse la rete di calcolo e le impostazioni del proxy Web per il dispositivo.   |
|**[5. Configurare le impostazioni del dispositivo](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Assegnare un nome di dispositivo e un dominio DNS, configurare il server di aggiornamento e l'ora del dispositivo. |
|**[6. Configurare le impostazioni di sicurezza](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Configurare i certificati usando i certificati personali, installare la VPN e configurare la crittografia dei dati inattivi per il dispositivo.   |
|**[7. Attivare il dispositivo](azure-stack-edge-mini-r-deploy-activate.md)** |Usare la chiave di attivazione dal servizio per attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS o per la connessione tramite REST. |
|**[8. Configurare il ruolo di calcolo](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurare il ruolo di calcolo nel dispositivo. Verrà creato anche un cluster Kubernetes. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione della distribuzione

Prima di distribuire il dispositivo, è necessario raccogliere informazioni per configurare il software nel dispositivo Azure Stack Edge Mini R. La preparazione di alcune di queste informazioni in anticipo consente di semplificare il processo di distribuzione del dispositivo nell'ambiente. Usare l'[elenco di controllo per la configurazione della distribuzione di Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md) per prendere nota dei dettagli di configurazione quando si distribuisce il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Azure Stack Edge, il dispositivo Azure Stack Edge e la rete del data center.

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Per il dispositivo Azure Stack Edge

Prima di distribuire un dispositivo fisico, è necessario:

- Avere esaminato le informazioni sulla sicurezza del dispositivo in [Istruzioni per la sicurezza di Azure Stack Edge Mini R](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Azure Stack Edge. Per altre informazioni, vedere [Requisiti di sistema di Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Per le normali condizioni di funzionamento di Azure Stack Edge siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Azure Stack Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare la risorsa Azure Stack Edge, completare i passaggi seguenti nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere al portale di Azure a questo indirizzo: [https://portal.azure.com](https://portal.azure.com).


2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare e selezionare **Azure Stack Edge/Data Box Gateway**. Selezionare **Crea**. 

3. Selezionare la sottoscrizione che si vuole usare per il dispositivo Azure Stack Edge Pro. Selezionare il paese in cui si vuole spedire il dispositivo fisico. Selezionare **Mostra dispositivi**.

    ![Creare una risorsa 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Selezionare il tipo di dispositivo. In **Azure Stack Edge** selezionare **Azure Stack Edge Mini R** e quindi **Seleziona**. Se si verificano problemi o non è possibile selezionare il tipo di dispositivo, passare alla [risoluzione dei problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

    [![Creare una risorsa 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


6. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Subscription    |Questo campo viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).     |


7. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |Valore  |
    |---------|---------|
    |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|

    ![Creare una risorsa 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


8. Selezionare **Avanti: Indirizzo di spedizione**.

    - Se si ha già un dispositivo, selezionare la casella combinata **Ho un dispositivo Azure Stack Edge Pro R**.

        ![Creare una risorsa 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

    - Se si sta ordinando un nuovo dispositivo, immettere il nome del contatto, la società, l'indirizzo a cui spedire il dispositivo e le informazioni di contatto.

        ![Creare una risorsa 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

9. Selezionare **Avanti: Tag**. Facoltativamente, specificare i tag per classificare le risorse e consolidare la fatturazione. Selezionare **Avanti: Rivedi e crea**.

10. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare la casella combinata **Dichiaro di aver esaminato le informazioni fornite e di accettare le condizioni relative alla privacy**.

    ![Creare una risorsa 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png) 

    Viene anche visualizzata una notifica che indica che durante la creazione della risorsa verrà abilitata un'identità del servizio gestita che consente di eseguire l'autenticazione ai servizi cloud. Questa identità esiste fintanto che esiste la risorsa.

8. Selezionare **Create** (Crea).

    La creazione della risorsa richiede alcuni minuti. Viene anche creata un'identità del servizio gestita che consente al dispositivo Azure Stack Edge di comunicare con il provider di risorse in Azure.
    
    Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.
    
    ![Andare alla risorsa Azure Stack Edge Pro](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
    Dopo che l'ordine è stato inviato, Microsoft lo verifica e invia quindi all'utente un messaggio di posta elettronica con i dettagli della spedizione.

   Se si verificano problemi durante il processo di ordine, vedere [Risolvere i problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Azure Stack Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Azure Stack Edge Mini R e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata. Selezionare **Panoramica** e quindi **Configurazione dispositivo**.

    ![Selezionare Configurazione dispositivo](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Nel riquadro **Attiva** specificare un nome per l'istanza di Azure Key Vault o accettare il nome predefinito. La lunghezza del nome dell'insieme di credenziali delle chiavi deve essere compresa tra 3 e 24 caratteri. 

    Viene creato un insieme di credenziali delle chiavi per ogni risorsa Azure Stack Edge attivata con il dispositivo. L'insieme di credenziali delle chiavi consente di archiviare e accedere ai segreti, ad esempio la chiave di integrità del canale per il servizio viene archiviata nell'insieme di credenziali delle chiavi. 

    Dopo aver specificato un nome dell'insieme di credenziali delle chiavi, selezionare **Genera chiave** per creare una chiave di attivazione. 

    [![Ottenere una chiave di attivazione](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Attendere alcuni minuti mentre vengono creati l'insieme di credenziali delle chiavi e la chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione.
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare Azure Stack Edge.

> [!div class="nextstepaction"]
> [Installare Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)