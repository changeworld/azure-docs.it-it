---
title: Esercitazione per preparare il portale di Azure e l'ambiente del data center per la distribuzione di Azure Stack Edge Pro | Microsoft Docs
description: La prima esercitazione sulla distribuzione di Azure Stack Edge Pro include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: alkohli
ms.openlocfilehash: 0e0d7808e449a6e4f3e341cd40acdc220e65c0ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068218"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Esercitazione: Preparare la distribuzione di Azure Stack Edge Pro  

Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Stack Edge Pro. Questa esercitazione descrive come preparare il portale di Azure per la distribuzione di una risorsa Azure Stack Edge. 

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="get-started"></a>Introduzione

Per distribuire Azure Stack Edge Pro, fare riferimento alle esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Creare e configurare la risorsa Azure Stack Edge prima di installare un dispositivo fisico Azure Stack Box Edge. |
| 2. |**[Installare Azure Stack Edge Pro](azure-stack-edge-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico Azure Stack Edge Pro.  |
| 3. |**[Connettere, configurare e attivare Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS.  |
| 4. |**[Trasferire i dati con Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Aggiungere condivisioni e connettersi alle condivisioni da SMB o NFS. |
| 5. |**[Trasformare i dati con Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Configurare i moduli di calcolo nel dispositivo per trasformare i dati durante lo spostamento in Azure. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Azure Stack Edge, il dispositivo Azure Stack Edge Pro e la rete del data center.

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per una risorsa Azure Stack Edge. Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Le sottoscrizioni con pagamento in base al consumo non sono supportate.

* Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Azure Stack Edge/Data Box Gateway, hub IoT e Archiviazione di Azure.

  * Per concedere l'accesso di tipo Collaboratore, è necessario essere un **Proprietario** a livello della sottoscrizione. Per concedere l'accesso di tipo Collaboratore a un altro utente, nel portale di Azure passare a **Tutti i servizi** > **Sottoscrizioni** > **Controllo di accesso (IAM)**  >  **+ Aggiungi** > **Aggiungi assegnazione di ruolo**. Per altre informazioni, vedere [Esercitazione: Concedere l'accesso alle risorse di Azure a un utente usando il portale di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Per creare qualsiasi risorsa Azure Stack Edge/Data Box Gateway, è necessario disporre di autorizzazioni di Collaboratore o superiori con ambito a livello di gruppo di risorse. È anche necessario assicurarsi che il provider di risorse `Microsoft.DataBoxEdge` sia registrato. Per informazioni su come registrare un provider di risorse, vedere [Registrare un provider di risorse](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Per creare tutte le risorse dell'hub tutto, assicurarsi che il provider Microsoft. Devices sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
* Si abbia accesso all'API Graph di Azure Active Directory Graph come utente o amministratore. Per altre informazioni, vedere [API Graph di Azure Active Directory](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
* Non si è bloccati da alcun criterio di Azure configurato dall'amministratore di sistema. Per altre informazioni sui criteri, vedere [Avvio rapido: Creare un'assegnazione di criteri per identificare le risorse non conformi](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Per il dispositivo Azure Stack Edge Pro

Prima di distribuire un dispositivo fisico, è necessario:

* Aver esaminato le informazioni sulla sicurezza incluse nel pacchetto di spedizione.
* Avere uno slot 1U disponibile in un rack standard da 19 pollici nel data center per montare in rack il dispositivo.
* Avere accesso a una superficie di lavoro orizzontale, stabile e piana su cui collocare il dispositivo in sicurezza.
* Che sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU rack con un gruppo di continuità (UPS) nell'ambiente in cui si intende configurare il dispositivo.
* Avere accesso a un dispositivo fisico.

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

* La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Azure Stack Edge Pro. Per altre informazioni, vedere [Requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).

* Per le normali condizioni di funzionamento di Azure Stack Edge Pro, siano disponibili:

  * Almeno 10 Mbps di larghezza di banda per il download per assicurarsi che il dispositivo rimanga aggiornato.
  * Almeno 20 Mbps per il caricamento e la larghezza di banda di download per trasferire i file.

## <a name="create-new-resource-for-existing-device"></a>Crea nuova risorsa per dispositivo esistente

Se si è un cliente Azure Stack Edge Pro esistente, usare la procedura seguente per creare una nuova risorsa se è necessario sostituire o reimpostare il dispositivo esistente.

Se sei un nuovo cliente, ti consigliamo di esplorare l'uso di dispositivi Pro-GPU Azure Stack Edge per i tuoi carichi di lavoro. Per altre informazioni, vedere [che cos'è Azure stack Edge Pro con GPU](azure-stack-edge-gpu-overview.md). Per informazioni sull'ordinamento di un Azure Stack Edge Pro con dispositivo GPU, vedere [creare una nuova risorsa per Azure stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Per creare una nuova risorsa di Azure Stack Edge Pro per un dispositivo esistente, seguire questa procedura nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere a:

    - Portale di Azure all'URL [https://portal.azure.com](https://portal.azure.com).
    - Portale di Azure per enti pubblici all'URL [https://portal.azure.us](https://portal.azure.us). Per altri dettagli, vedere l'articolo su come [connettersi ad Azure per enti pubblici con il portale](../azure-government/documentation-government-get-started-connect-with-portal.md).

1. Selezionare **+ Crea una risorsa**. Cercare e selezionare **Azure stack Edge**. Quindi selezionare **Crea**

1. Selezionare la sottoscrizione per il dispositivo Azure Stack Edge Pro e il paese in **cui spedire il dispositivo.**

   ![Selezionare la sottoscrizione e spedire il paese per il dispositivo](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Nell'elenco dei tipi di dispositivo visualizzato selezionare **Azure stack Edge Pro-FPGA**. Quindi scegliere **Seleziona**. 

   Il tipo di dispositivo **Pro-FPGA Azure stack Edge** viene visualizzato solo se si dispone di un dispositivo esistente. Se è necessario ordinare un nuovo dispositivo, passare a [creare una nuova risorsa per la funzionalità Pro-GPU di Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Cercare il servizio Azure Stack Edge](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. Passare alla scheda **Nozioni di base**:

   1. Immettere o selezionare i **dettagli del progetto** seguenti.
    
       |Impostazione  |Valore  |
       |---------|---------|
       |Subscription    |Questo valore viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
       |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).     |

   1. Immettere o selezionare quanto segue in **Dettagli istanza**.

       |Impostazione  |Valore  |
       |---------|---------|
       |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome ha da 2 a 50 caratteri, tra cui lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
       |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|

   1. Selezionare **Rivedi e crea**.

    ![Dettagli del progetto e dell'istanza](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. Nella scheda **revisione e creazione** esaminare i dettagli relativi a **condizioni per l'utilizzo**, **prezzi** e dettagli per la risorsa. Quindi selezionare **Crea**

    ![Esaminare i dettagli della risorsa Azure Stack Edge e le condizioni relative alla privacy](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. La creazione della risorsa richiede alcuni minuti. Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

   ![Andare alla risorsa Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Una volta effettuato l'ordine, Microsoft esamina l'ordine e contatta l'utente (via posta elettronica) con i dettagli sulla spedizione.

![Notifica della verifica dell'ordine di Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Azure Stack Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Azure Stack Edge Pro e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Passare alla risorsa creata e selezionare **Overview (panoramica**). Verrà visualizzata una notifica per indicare che l'ordine è in fase di elaborazione.

    ![Selezionare Panoramica](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Dopo che l'ordine è stato elaborato e il dispositivo viene inviato, la pagina **Panoramica** viene aggiornata. Accettare il nome predefinito di **Azure Key Vault** o immetterne uno nuovo. Selezionare **Genera chiave di attivazione**. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

    ![Ottenere una chiave di attivazione](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * La chiave di attivazione scade tre giorni dopo la generazione.
> * Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge Pro, ad esempio:

> [!div class="checklist"]
>
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare Azure Stack Edge Pro.

> [!div class="nextstepaction"]
> [Installare Azure Stack Edge Pro](./azure-stack-edge-deploy-install.md)