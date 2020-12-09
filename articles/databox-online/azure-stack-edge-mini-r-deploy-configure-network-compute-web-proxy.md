---
title: Esercitazione per configurare le impostazioni di rete per il dispositivo Azure Stack Edge Mini R nel portale di Azure
description: Esercitazione per la distribuzione di Azure Stack Edge Mini R che illustra come configurare le impostazioni di rete, della rete di calcolo e del proxy Web per il dispositivo fisico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: 915aca5f7400496aacb3c3cf248120dff39d747c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465028"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Esercitazione: Configurare la rete per Azure Stack Edge Mini R

Questa esercitazione illustra come configurare la rete per il dispositivo Azure Stack Edge Mini R con una GPU su scheda usando l'interfaccia utente Web locale.

Per il completamento del processo di connessione sono necessari circa 20 minuti.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Configurare la rete
> * Abilitare la rete di calcolo
> * Configurare il proxy web


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge Mini R, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md).
* Essere connessi all'interfaccia utente Web locale del dispositivo come descritto in [Connettersi ad Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md).


## <a name="configure-network"></a>Configurare la rete

La pagina **Attività iniziali** visualizza le diverse impostazioni necessarie per configurare e registrare il dispositivo fisico nel servizio Azure Stack Edge. 

Seguire questa procedura per configurare la rete per il dispositivo.

1. Nell'interfaccia utente Web locale del dispositivo passare alla pagina **Attività iniziali**. 

2. Se è necessario un aggiornamento zero-day, è possibile eseguirlo qui configurando una porta dati con una connessione cablata. Per maggiori istruzioni su come configurare una connessione cablata per questo dispositivo, vedere [Cablare il dispositivo](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Al termine dell'aggiornamento, si può rimuovere la connessione cablata.

3. Creare i certificati per il Wi-Fi e la catena di firma. Sia i certificati per la catena di firma che quelli per il Wi-Fi devono essere in formato DER con estensione file *cer*. Per istruzioni, vedere [Creare certificati](azure-stack-edge-j-series-manage-certificates.md).

4. Nell'interfaccia utente Web locale passare a **Attività iniziali**. Nel riquadro **Sicurezza** selezionare **Certificati** e quindi selezionare **Configura**. 

    [![Pagina "Certificati" dell'interfaccia utente Web locale](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Selezionare **+ Aggiungi certificato**. 
    
        [![Pagina "Certificati" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Caricare la catena di firma e selezionare **Applica**.

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Ripetere la procedura con il certificato Wi-Fi. 

        ![Pagina "Certificati" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. I nuovi certificati dovrebbero essere visualizzati nella pagina **Certificati**. 
    
        [![Pagina "Certificati" dell'interfaccia utente Web locale - 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Tornare a **Attività iniziali**.

3. Nel riquadro **Rete** selezionare **Configura**.  
    
    Nel dispositivo fisico sono presenti cinque interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, PORT 3 e PORT 4 sono interfacce di rete a 10 Gbps. La quinta porta è la porta Wi-Fi. 

    [![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Selezionare la porta Wi-Fi e configurarne le impostazioni. 
    
    > [!IMPORTANT]
    > È consigliabile configurare un indirizzo IP statico per la porta Wi-Fi.  

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Dopo l'applicazione delle impostazioni della porta Wi-Fi, la pagina **Rete** viene aggiornata.

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Selezionare **Aggiungi un profilo Wi-Fi** e caricare il profilo Wi-Fi. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Un profilo di rete wireless contiene il SSID (nome di rete), il codice password e le informazioni di sicurezza necessari per potersi connettere a una rete wireless. È possibile ottenere il profilo Wi-Fi per il proprio ambiente dall'amministratore di rete.

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Una volta aggiunto il profilo, l'elenco dei profili Wi-Fi viene aggiornato per riflettere il nuovo profilo. Lo **Stato della connessione** del profilo dovrebbe essere **Disconnesso**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Dopo il caricamento del profilo di rete wireless, connettersi al profilo. Selezionare **Connetti profilo WiFi**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Selezionare il profilo Wi-Fi aggiunto nel passaggio precedente e quindi selezionare **Applica**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    Lo **Stato della connessione** dovrebbe venire aggiornato a **Connesso**. La potenza del segnale viene aggiornata per indicare la qualità del segnale. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Per trasferire grandi quantità di dati, è consigliabile usare una connessione cablata invece della rete wireless. 

6. Disconnettere PORT 1 sul dispositivo dal portatile. 

7. Nella configurazione delle impostazioni di rete tenere presenti queste considerazioni:

   - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
   - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
   - È possibile configurare l'interfaccia di rete come IPv4.
   - Il numero di serie di qualsiasi porta corrisponde al numero di serie del nodo. Per un dispositivo serie K viene visualizzato solo un numero di serie.

     >[!NOTE] 
     > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.

Dopo aver configurato e applicato le impostazioni di rete, selezionare **Avanti: Calcolo** per configurare la rete di calcolo.

## <a name="enable-compute-network"></a>Abilitare la rete di calcolo

Seguire questa procedura per abilitare il calcolo e configurare la rete di calcolo. 


1. Nella pagina **Compute** (Calcolo) selezionare un'interfaccia di rete che si vuole abilitare per il calcolo. 

    ![Pagina di calcolo nell'interfaccia utente locale 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. Nella finestra di dialogo **Impostazioni di rete** selezionare **Abilita**. Quando si abilita il calcolo, viene creato un commutatore virtuale nel dispositivo per tale interfaccia di rete. Il commutatore virtuale viene usato per l'infrastruttura di calcolo nel dispositivo. 
    
1. Assegnare gli **indirizzi IP del nodo Kubernetes**. Questi indirizzi IP statici sono per la macchina virtuale di calcolo.  

    Per un dispositivo con *n* nodi, viene fornito un intervallo contiguo di almeno *n+1* indirizzi IPv4 (o più) per la macchina virtuale di calcolo usando gli indirizzi IP di inizio e fine. Dato che Azure Stack Edge è un dispositivo a 1 nodo, vengono forniti almeno 2 indirizzi IPv4 contigui.

    > [!IMPORTANT]
    > Kubernetes in Azure Stack Edge usa la subnet 172.27.0.0/16 per il pod e la subnet 172.28.0.0/16 per il servizio. Assicurarsi che non siano in uso nella rete. Se queste subnet sono già in uso nella rete, è possibile cambiarle eseguendo il cmdlet `Set-HcsKubeClusterNetworkInfo` dall'interfaccia di PowerShell del dispositivo. Per altre informazioni, vedere [Cambiare le subnet del pod e del servizio Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Assegnare gli **indirizzi IP del servizio esterno Kubernetes**. Questi sono anche gli indirizzi IP di bilanciamento del carico. Questi indirizzi IP contigui sono per i servizi che si vogliono esporre all'esterno del cluster Kubernetes e si specifica l'intervallo di indirizzi IP statici a seconda del numero di servizi esposti. 
    
    > [!IMPORTANT]
    > Per accedere ai moduli di calcolo, è consigliabile specificare almeno un indirizzo IP per il servizio Hub di Azure Stack Edge Mini R. È quindi possibile specificare facoltativamente indirizzi IP aggiuntivi per altri moduli di servizi/IoT Edge (1 per servizio/modulo) a cui è necessario accedere dall'esterno del cluster. Gli indirizzi IP del servizio possono essere aggiornati in un secondo momento. 
    
1. Selezionare **Applica**.

    ![Pagina di calcolo nell'interfaccia utente locale 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. L'applicazione della configurazione richiede un paio di minuti e potrebbe essere necessario aggiornare il browser. È possibile vedere che la porta specificata è abilitata per il calcolo. 
 
    ![Pagina di calcolo nell'interfaccia utente locale 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selezionare **Avanti: Proxy Web** per configurare il proxy Web.  

  
## <a name="configure-web-proxy"></a>Configurare il proxy web

Si tratta di una configurazione facoltativa.

> [!IMPORTANT]
> * Se si abilita il calcolo e si usa il modulo IoT Edge nel dispositivo Azure Stack Edge Mini R, è consigliabile impostare l'autenticazione proxy Web su **Nessuna**. L'autenticazione integrata di Windows non è supportata.
>* I file PAC (proxy-auto config) non sono supportati. Un file PAC definisce il modo in cui i Web browser e altri agenti utente possono scegliere automaticamente il server proxy appropriato (metodo di accesso) per recuperare un determinato URL. I proxy che tentano di intercettare e leggere tutto il traffico (quindi firmare nuovamente tutti gli elementi con la propria certificazione) non sono compatibili perché il certificato del proxy non è attendibile. In genere i proxy trasparenti funzionano correttamente con Azure Stack Edge Mini R. I proxy Web non trasparenti non sono supportati.


1. Nella pagina **Impostazioni proxy Web** seguire questa procedura:

    1. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://host-IP address or FQDN:Port number`. Gli URL HTTPS non sono supportati.

    2. In **Autenticazione** selezionare **Nessuna** o **NTLM**. Se si abilita il calcolo e si usa il modulo IoT Edge nel dispositivo Azure Stack Edge Mini R, è consigliabile impostare l'autenticazione proxy Web su **Nessuna**. L'**autenticazione integrata di Windows** non è supportata.

    3. Se si usa l'autenticazione, immettere un nome utente e una password.

    4. Per convalidare e applicare le impostazioni del proxy Web configurate, selezionare **Applica**.
    
   ![Pagina "Impostazioni proxy Web" dell'interfaccia utente Web locale](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Dopo l'applicazione delle impostazioni, selezionare **Avanti: Device** (Workbench IoT: Dispositivo).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Configurare la rete
> * Abilitare la rete di calcolo
> * Configurare il proxy web


Per informazioni su come configurare il dispositivo Azure Stack Edge Mini R, vedere:

> [!div class="nextstepaction"]
> [Configura impostazioni dispositivo](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
