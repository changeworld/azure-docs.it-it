---
title: Gestione Wi-Fi mini R di Azure Stack Edge
description: Viene descritto come usare la portale di Azure per gestire Wi-Fi sul Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043768"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Usare l'interfaccia utente Web locale per gestire la connettività wireless sul mini R Azure Stack Edge

Questo articolo descrive come gestire la connettività di rete wireless sul dispositivo Mini R Azure Stack Edge. È possibile usare l'interfaccia utente Web locale sul dispositivo Mini R Azure Stack Edge tramite per aggiungere, connettersi ed eliminare Wi-Fi profili.

## <a name="about-wi-fi"></a>Informazioni su Wi-Fi

Il dispositivo Mini R Azure Stack Edge può funzionare sia quando è cablato alla rete che tramite una rete wireless. Il dispositivo dispone di una porta Wi-Fi che deve essere abilitata per consentire al dispositivo di connettersi a una rete wireless. 

Il dispositivo ha cinque porte, la porta 1 tramite la porta 4 e una quinta porta Wi-Fi. Di seguito è riportato un diagramma del piano posteriore di un dispositivo Mini R quando si è connessi a una rete wireless.

![Cablaggio per la rete Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Aggiungi, Connetti a Wi-Fi profilo

Eseguire i passaggi seguenti nell'interfaccia utente locale del dispositivo per aggiungere e connettersi a un profilo di Wi-Fi.

1. Passare alla pagina **Attività iniziali** nell'interfaccia utente Web locale del dispositivo. Nel riquadro **Rete** selezionare **Configura**.  
    
    Nel dispositivo fisico sono presenti cinque interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, PORT 3 e PORT 4 sono interfacce di rete a 10 Gbps. La quinta porta è la porta Wi-Fi. 

    [![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Selezionare la porta Wi-Fi e configurarne le impostazioni. 
    
    > [!IMPORTANT]
    > È consigliabile configurare un indirizzo IP statico per la porta Wi-Fi.  

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Dopo l'applicazione delle impostazioni della porta Wi-Fi, la pagina **Rete** viene aggiornata.

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Selezionare **Aggiungi un profilo Wi-Fi** e caricare il profilo Wi-Fi. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Un profilo di rete wireless contiene il SSID (nome di rete), il codice password e le informazioni di sicurezza necessari per potersi connettere a una rete wireless. È possibile ottenere il profilo Wi-Fi per il proprio ambiente dall'amministratore di rete.

    Per informazioni sulla preparazione dei profili di Wi-Fi, vedere [usare profili Wi-Fi con i dispositivi Mini R Azure stack Edge](azure-stack-edge-mini-r-use-wifi-profiles.md).

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Una volta aggiunto il profilo, l'elenco dei profili Wi-Fi viene aggiornato per riflettere il nuovo profilo. Lo **Stato della connessione** del profilo dovrebbe essere **Disconnesso**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Dopo il caricamento del profilo di rete wireless, connettersi al profilo. Selezionare **Connetti profilo WiFi**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Selezionare il profilo Wi-Fi aggiunto nel passaggio precedente e quindi selezionare **Applica**. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    Lo **Stato della connessione** dovrebbe venire aggiornato a **Connesso**. La potenza del segnale viene aggiornata per indicare la qualità del segnale. 

    ![Impostazioni di rete (Wi-Fi porta) nell'interfaccia utente Web locale - 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Per trasferire grandi quantità di dati, è consigliabile usare una connessione cablata invece della rete wireless. 


## <a name="download-wi-fi-profile"></a>Scarica profilo Wi-Fi

È possibile scaricare un profilo di Wi-Fi usato per la connettività di rete wireless.

1. Nell'interfaccia utente Web locale del dispositivo passare a **configurazione > rete**. 

2. In Wi-Fi impostazioni profilo selezionare **Scarica profilo**. Questa operazione dovrebbe scaricare il profilo Wi-Fi attualmente in uso.


## <a name="delete-wi-fi-profile"></a>Elimina profilo Wi-Fi

È possibile eliminare un profilo di Wi-Fi usato per la connettività di rete wireless.


1. Nell'interfaccia utente Web locale del dispositivo passare a **configurazione > rete**. 

2. In Wi-Fi impostazioni del profilo selezionare **elimina Wi-Fi profilo**.

3. Nel pannello **Elimina profilo Wi-Fi** scegliere il profilo che si vuole eliminare. Selezionare **Applica**.


## <a name="configure-cisco-wi-fi-profile"></a>Configurare il profilo Cisco Wi-Fi

Di seguito sono riportate alcune indicazioni su come gestire e configurare un punto di accesso e un controller Cisco wireless nel dispositivo. 

### <a name="dhcp-bridging-mode"></a>Modalità bridging DHCP

Per usare un controller Cisco wireless per il dispositivo, è necessario abilitare la modalità bridging DHCP (Dynamic Host Configuration Protocol) sul controller LAN wireless (WLC).

Per altre informazioni, vedere [modalità bridging DHCP](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Esempio di configurazione bridging

Per abilitare la funzionalità di bridging DHCP sul controller, è necessario disabilitare la funzionalità proxy DHCP nel controller. Per abilitare il bridging DHCP tramite la riga di comando:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Se il server DHCP non esiste nella stessa rete di livello 2 (L2) del client, la trasmissione deve essere trasmessa al server DHCP nel gateway client usando un helper IP. Questo è un esempio di questa configurazione:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

La funzionalità bridging DHCP è un'impostazione globale, pertanto influiscono su tutte le transazioni DHCP all'interno del controller. È necessario aggiungere le istruzioni dell'helper IP nell'infrastruttura cablata per tutte le reti locali virtuali (VLAN) necessarie nel controller.

### <a name="enable-the-passive-client-for-wlan"></a>Abilitare il client passivo per WLAN

Per abilitare la funzionalità client passivo per la rete WLAN (wireless local area network) in un controller Cisco wireless:

* Per l'interfaccia associata alla rete WLAN è necessario che sia abilitata la codifica VLAN.
* Per la rete WLAN è necessario abilitare la VLAN multicast.
* È necessario abilitare l'GARP per l'invio in WLC.

Per ulteriori informazioni, vedere la pagina relativa alle [informazioni sulla VLAN multicast sull'ottimizzazione multicast](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi con le allocazioni degli indirizzi IP in macchine virtuali in esecuzione su un dispositivo Mini R Azure Stack Edge, è necessario convalidare le impostazioni di configurazione precedenti nell'ambiente di rete.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire Azure stack dispositivo Mini R Edge](azure-stack-edge-mini-r-deploy-prep.md).
