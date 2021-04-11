---
title: Esercitazione per configurare le impostazioni di rete per il dispositivo Azure Stack Edge Pro R nel portale di Azure | Microsoft Docs
description: Esercitazione per la distribuzione di Azure Stack Edge Pro R che illustra come configurare le impostazioni di rete, della rete di calcolo e del proxy Web per il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: a1f3966c8794b50f6ec369f1ea86905c4d8aaf3f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059939"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Esercitazione: Configurare la rete per Azure Stack Edge Pro R

Questa esercitazione illustra come configurare la rete per il dispositivo Azure Stack Edge Pro R usando l'interfaccia utente Web locale.

Per il completamento del processo di connessione sono necessari circa 20 minuti.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare la rete
> * Abilitare la rete di calcolo
> * Configurare il proxy web


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Pro R, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-install.md).
* Essere connessi all'interfaccia utente Web locale del dispositivo come descritto in [Connettersi ad Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md).


## <a name="configure-network"></a>Configurare la rete

La pagina **Attività iniziali** visualizza le diverse impostazioni necessarie per configurare e registrare il dispositivo fisico nel servizio Azure Stack Edge. 

Seguire questa procedura per configurare la rete per il dispositivo.

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Attività iniziali**. 

2. Nel riquadro **Rete** selezionare **Configura** per passare alla pagina **Rete**. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Nel dispositivo fisico sono presenti quattro interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, PORT 3 e PORT 4 sono interfacce di rete a 10/25 Gbps. PORT 1 viene automaticamente configurata come porta di sola gestione, mentre le porte tra PORT 2 e PORT 4 sono tutte porte dati. Di seguito è illustrata la pagina **Rete**.
    
    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Per modificare le impostazioni di rete, selezionare una porta e nel riquadro destro visualizzato modificare l'indirizzo IP, la subnet, il gateway, il DNS primario e il DNS secondario. 

    - Se si seleziona la porta 1, è possibile notare che è preconfigurata come statica. 

        ![Impostazioni di rete della porta 1 nell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Se si seleziona la porta 2, 3 o 4, tutte queste porte vengono configurate come DHCP per impostazione predefinita.

        ![Impostazioni di rete della porta 3 nell'interfaccia utente Web locale](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Nella configurazione delle impostazioni di rete tenere presenti queste considerazioni:

   * Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
   * Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
   * È possibile configurare l'interfaccia di rete come IPv4.
   * Il gruppo NIC (Network Interface Card) o l'aggregazione di collegamenti non è supportato con Azure Stack Edge.
   * Il numero di serie di qualsiasi porta corrisponde al numero di serie del nodo.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Dopo aver configurato la rete del dispositivo, la pagina viene aggiornata come illustrato di seguito.

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine dell'attivazione del dispositivo nel servizio e quindi eseguire la modifica. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.


    Dopo aver configurato e applicato le impostazioni di rete, selezionare **Avanti: Calcolo** per configurare la rete di calcolo.

## <a name="enable-compute-network"></a>Abilitare la rete di calcolo

Seguire questa procedura per abilitare il calcolo e configurare la rete di calcolo. 

1. Nella pagina **Compute** (Calcolo) selezionare un'interfaccia di rete che si vuole abilitare per il calcolo. 

    ![Pagina di calcolo nell'interfaccia utente locale 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Nella finestra di dialogo **Impostazioni di rete** selezionare **Abilita**. Quando si abilita il calcolo, viene creato un commutatore virtuale nel dispositivo per tale interfaccia di rete. Il commutatore virtuale viene usato per l'infrastruttura di calcolo nel dispositivo. 
    
1. Assegnare gli **indirizzi IP del nodo Kubernetes**. Questi indirizzi IP statici sono per la macchina virtuale di calcolo.  

    Per un dispositivo con *n* nodi, viene fornito un intervallo contiguo di almeno *n+1* indirizzi IPv4 (o più) per la macchina virtuale di calcolo usando gli indirizzi IP di inizio e fine. Dato che Azure Stack Edge è un dispositivo a 1 nodo, vengono forniti almeno 2 indirizzi IPv4 contigui. Questi indirizzi IP devono essere nella stessa rete in cui è stato abilitato il calcolo e in cui è stato creato il commutatore virtuale.

    > [!IMPORTANT]
    > Kubernetes in Azure Stack Edge usa la subnet 172.27.0.0/16 per il pod e la subnet 172.28.0.0/16 per il servizio. Assicurarsi che non siano in uso nella rete. Se queste subnet sono già in uso nella rete, è possibile cambiarle eseguendo il cmdlet `Set-HcsKubeClusterNetworkInfo` dall'interfaccia di PowerShell del dispositivo. Per altre informazioni, vedere [Cambiare le subnet del pod e del servizio Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Assegnare gli **indirizzi IP del servizio esterno Kubernetes**. Questi sono anche gli indirizzi IP di bilanciamento del carico. Questi indirizzi IP contigui sono per i servizi che si vogliono esporre all'esterno del cluster Kubernetes e si specifica l'intervallo di indirizzi IP statici a seconda del numero di servizi esposti. 
    
    > [!IMPORTANT]
    > Per accedere ai moduli di calcolo, è consigliabile specificare almeno un indirizzo IP per il servizio Hub di Azure Stack Edge Pro R. È quindi possibile specificare facoltativamente indirizzi IP aggiuntivi per altri moduli di servizi/IoT Edge (1 per servizio/modulo) a cui è necessario accedere dall'esterno del cluster. Gli indirizzi IP del servizio possono essere aggiornati in un secondo momento. 
    
1. Selezionare **Applica**.

    ![Pagina di calcolo nell'interfaccia utente locale 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. L'applicazione della configurazione richiede un paio di minuti e potrebbe essere necessario aggiornare il browser. È possibile vedere che la porta specificata è abilitata per il calcolo. 
 
    ![Pagina di calcolo nell'interfaccia utente locale 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selezionare **Avanti: Proxy Web** per configurare il proxy Web.  

  
## <a name="configure-web-proxy"></a>Configurare il proxy web

Si tratta di una configurazione facoltativa.

> [!IMPORTANT]
> * Se si abilita il calcolo e si usa il modulo IoT Edge nel dispositivo Azure Stack Edge Pro R, è consigliabile impostare l'autenticazione proxy Web su **Nessuna**. L'autenticazione integrata di Windows non è supportata.
>* I file PAC (proxy-auto config) non sono supportati. Un file PAC definisce il modo in cui i Web browser e altri agenti utente possono scegliere automaticamente il server proxy appropriato (metodo di accesso) per recuperare un determinato URL. I proxy che tentano di intercettare e leggere tutto il traffico (quindi firmare nuovamente tutti gli elementi con la propria certificazione) non sono compatibili perché il certificato del proxy non è attendibile. In genere i proxy trasparenti funzionano correttamente con Azure Stack Edge Pro R. I proxy Web non trasparenti non sono supportati.


1. Nella pagina **Impostazioni proxy Web** seguire questa procedura:

    1. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://host-IP address or FQDN:Port number`. Gli URL HTTPS non sono supportati.

    2. In **Autenticazione** selezionare **Nessuna** o **NTLM**. Se si abilita il calcolo e si usa il modulo IoT Edge sul dispositivo Azure Stack Edge Pro R, è consigliabile impostare l'autenticazione proxy Web su **Nessuna**. L'**autenticazione integrata di Windows** non è supportata.

    3. Se si usa l'autenticazione, immettere un nome utente e una password.

    4. Per convalidare e applicare le impostazioni del proxy Web configurate, selezionare **Applica**.
    
   ![Pagina "Impostazioni proxy Web" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Dopo l'applicazione delle impostazioni, selezionare **Avanti: Device** (Workbench IoT: Dispositivo).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Configurare la rete
> * Abilitare la rete di calcolo
> * Configurare il proxy web


Per informazioni su come configurare il dispositivo Azure Stack Edge Pro R, vedere:

> [!div class="nextstepaction"]
> [Configura impostazioni dispositivo](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
