---
title: Esercitazione per preparare il portale di Azure e l'ambiente del data center per la distribuzione di Azure Stack Edge Pro R
description: La prima esercitazione sulla distribuzione di Azure Stack Edge Pro R include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 5e220759a46ad9098f81a9534fa64145adade2b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613125"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Esercitazione: Preparare la distribuzione di Azure Stack Edge Pro R

Questa esercitazione è la prima della serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Stack Edge Pro R. L'esercitazione descrive come preparare il portale di Azure per distribuire una risorsa di Azure Stack Edge. L'esercitazione usa un dispositivo Azure Stack Edge Pro R a un nodo dotato di un gruppo di continuità (UPS).

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

### <a name="get-started"></a>Introduzione

Per distribuire Azure Stack Edge Pro R, fare riferimento alle esercitazioni seguenti nella sequenza indicata.

| Per eseguire questo passaggio | Usare questi documenti |
| --- | --- |
| **Preparazione** |Questi passaggi devono essere completati per preparare la successiva distribuzione. |
| **[Elenco di controllo configurazione della distribuzione](#deployment-configuration-checklist)** |Usare questo elenco di controllo per raccogliere e registrare informazioni prima e durante la distribuzione. |
| **[Prerequisiti di distribuzione](#prerequisites)** |Questi prerequisiti confermano che l'ambiente è pronto per la distribuzione. |
|  | |
|**Esercitazioni sulla distribuzione** |Queste esercitazioni sono necessarie per distribuire il dispositivo Azure Stack Edge Pro R nell'ambiente di produzione. |
|**[1. Preparare il portale di Azure per il dispositivo](azure-stack-edge-pro-r-deploy-prep.md)** |Creare e configurare la risorsa Azure Stack Edge prima di installare un dispositivo fisico Azure Stack Box Edge. |
|**[2. Installare il dispositivo](azure-stack-edge-pro-r-deploy-install.md)**|Controllare e cablare il dispositivo fisico.  |
|**[3. Connettersi al dispositivo](azure-stack-edge-pro-r-deploy-connect.md)** |Dopo aver installato il dispositivo, connettersi all'interfaccia utente Web locale del dispositivo.  |
|**[4. Configurare le impostazioni di rete](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Configurare la rete, incluse la rete di calcolo e le impostazioni del proxy Web per il dispositivo.   |
|**[5. Configurare le impostazioni del dispositivo](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Assegnare un nome di dispositivo e un dominio DNS, configurare il server di aggiornamento e l'ora del dispositivo. |
|**[6. Configurare le impostazioni di sicurezza](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Configurare i certificati, la VPN e la crittografia dei dati inattivi per il dispositivo. Usare i certificati generati dal dispositivo o caricare i certificati personali.   |
|**[7. Attivare il dispositivo](azure-stack-edge-pro-r-deploy-activate.md)** |Usare la chiave di attivazione dal servizio per attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS o per la connessione tramite REST. |
|**[8. Configurare il ruolo di calcolo](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurare il ruolo di calcolo nel dispositivo. Viene creato anche un cluster Kubernetes. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione della distribuzione

Prima di distribuire il dispositivo, è necessario raccogliere informazioni per configurare il software nel dispositivo Azure Stack Edge Pro. La preparazione di alcune di queste informazioni in anticipo consente di semplificare il processo di distribuzione del dispositivo nell'ambiente. Usare l'[elenco di controllo per la configurazione della distribuzione di Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-checklist.md) per prendere nota dei dettagli di configurazione quando si distribuisce il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Azure Stack Edge, il dispositivo Azure Stack Edge e la rete del data center.

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Per il dispositivo Azure Stack Edge

Prima di distribuire un dispositivo fisico, è necessario:

- Avere esaminato le informazioni sulla sicurezza del dispositivo in: [Istruzioni per la sicurezza di Azure Stack Edge Pro R](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Azure Stack Edge. Per altre informazioni, vedere [Requisiti di sistema di Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md).

- Per le normali condizioni di funzionamento del dispositivo siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Azure Stack Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare una risorsa Azure Stack Edge, seguire questa procedura nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere al portale di Azure a questo indirizzo: [https://portal.azure.com](https://portal.azure.com).

2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare e selezionare **Azure Stack Edge/Data Box Gateway**. Selezionare **Crea**. 

3. Selezionare la sottoscrizione che si vuole usare per il dispositivo Azure Stack Edge Pro. Selezionare il paese in cui si vuole spedire il dispositivo fisico. Selezionare **Mostra dispositivi**.

    ![Creare una risorsa 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Selezionare il tipo di dispositivo. In **Azure Stack Edge** selezionare **Azure Stack Edge Pro R** e quindi **Seleziona**. Se si verificano problemi o non è possibile selezionare il tipo di dispositivo, passare alla [risoluzione dei problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

    ![Creare una risorsa 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. A seconda delle esigenze aziendali, è possibile selezionare **Azure Stack Edge Pro R - Nodo singolo** o **Azure Stack Edge Pro R - Nodo singolo con gruppo di continuità**.  

    ![Creare una risorsa 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |Subscription    |I dati della sottoscrizione vengono compilati automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).     |

7. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |Valore  |
    |---------|---------|
    |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|

    ![Creare una risorsa 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Selezionare **Avanti: Indirizzo di spedizione**.

    - Se si ha già un dispositivo, selezionare la casella combinata **Ho un dispositivo Azure Stack Edge Pro R**.

        ![Creare una risorsa 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Se il dispositivo è il nuovo dispositivo che si sta ordinando, immettere il nome del contatto, la società, l'indirizzo per spedire il dispositivo e le informazioni di contatto.

        ![Creare una risorsa 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Selezionare **Avanti: Tag**. Facoltativamente, specificare i tag per classificare le risorse e consolidare la fatturazione. Selezionare **Avanti: Rivedi e crea**.

10. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare la casella combinata **Dichiaro di aver esaminato le informazioni fornite e di accettare le condizioni relative alla privacy**.

    ![Creare una risorsa 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Viene anche visualizzata una notifica che indica che durante la creazione della risorsa verrà abilitata un'identità del servizio gestita che consente di eseguire l'autenticazione ai servizi cloud. Questa identità esiste fintanto che esiste la risorsa.

11. Selezionare **Create** (Crea).

    La creazione della risorsa richiede alcuni minuti. Viene anche creata un'identità del servizio gestita che consente al dispositivo Azure Stack Edge di comunicare con il provider di risorse in Azure.

    Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

    ![Andare alla risorsa Azure Stack Edge Pro](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Una volta effettuato l'ordine, Microsoft esamina l'ordine e contatta l'utente (via posta elettronica) con i dettagli sulla spedizione.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png) - If this is restored, it must go above "After the resource is successfully created." The azure-stack-edge-resource-1.png would seem superfluous in that case.--> 

> [!NOTE]
> Per creare più ordini contemporaneamente o clonare un ordine esistente, è possibile usare gli [script negli esempi di Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Per altre informazioni, vedere il file README.

Se si verificano problemi durante il processo di ordine, vedere [Risolvere i problemi relativi all'ordine](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se necessario, preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Per creare una risorsa Azure Stack Edge, eseguire i comandi seguenti nell'interfaccia della riga di comando di Azure.

1. Creare un gruppo di risorse usando il comando [AZ Group create](/cli/azure/group#az_group_create) oppure usare un gruppo di risorse esistente:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Per creare un dispositivo, usare il comando [AZ databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgePR_Base
   ```

   Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo. L'area archivia solo i metadati per la gestione del dispositivo. I dati effettivi possono essere archiviati in qualsiasi account di archiviazione.

   Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).

1. Per creare un ordine, eseguire il comando [AZ databoxedge Order create](/cli/azure/databoxedge/order#az_databoxedge_order_create) :

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

La creazione della risorsa richiede alcuni minuti. Eseguire il comando [AZ databoxedge Order Show](/cli/azure/databoxedge/order#az_databoxedge_order_show) per vedere l'ordine:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Dopo aver effettuato un ordine, Microsoft esamina l'ordine e contatta l'utente tramite posta elettronica con i dettagli sulla spedizione.

---

## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Azure Stack Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Azure Stack Edge Pro e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata, quindi selezionare **Panoramica**.

2. Nel riquadro a destra specificare un nome per l'istanza di Azure Key Vault o accettare il nome predefinito. La lunghezza del nome dell'insieme di credenziali delle chiavi deve essere compresa tra 3 e 24 caratteri.

   Viene creato un insieme di credenziali delle chiavi per ogni risorsa Azure Stack Edge attivata con il dispositivo. L'insieme di credenziali delle chiavi consente di archiviare e accedere ai segreti, ad esempio la chiave di integrità del canale per il servizio viene archiviata nell'insieme di credenziali delle chiavi.

   Dopo aver specificato un nome dell'insieme di credenziali delle chiavi, selezionare **Genera chiave di attivazione** per creare una chiave di attivazione.

   ![Ottenere una chiave di attivazione](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

   Attendere alcuni minuti mentre vengono creati l'insieme di credenziali delle chiavi e la chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.<!--Verify that the new screen has a copy icon.-->

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
> [Installare Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)
