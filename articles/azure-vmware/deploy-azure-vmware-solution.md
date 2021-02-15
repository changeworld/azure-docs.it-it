---
title: Distribuire e configurare la soluzione Azure VMware
description: Informazioni su come usare le informazioni raccolte nella fase di pianificazione per distribuire il cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 12/24/2020
ms.openlocfilehash: 4c6929ca59bae022642082e8382203a10bd41309
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382056"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Distribuire e configurare la soluzione Azure VMware

In questo articolo verranno usate le informazioni raccolte della [sezione di pianificazione](production-ready-deployment-steps.md) per distribuire la soluzione Azure VMware. 

>[!IMPORTANT]
>Se le informazioni non sono ancora state definite, tornare alla [sezione pianificazione](production-ready-deployment-steps.md) prima di continuare.

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Distribuire la soluzione Azure VMware

Usare le informazioni raccolte nell'articolo [Pianificazione della distribuzione della soluzione Azure VMware](production-ready-deployment-steps.md):

>[!NOTE]
>Per distribuire la soluzione Azure VMware, è necessario avere almeno il livello di Collaboratore nella sottoscrizione.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Per una panoramica completa di questo passaggio, guardare il video [Soluzione Azure VMware: Distribuzione](https://www.youtube.com/embed/gng7JjxgayI).

## <a name="create-the-jump-box"></a>Creare una jump box

>[!IMPORTANT]
>Se l'opzione **Rete virtuale** è stata lasciata vuota durante il passaggio di provisioning iniziale nella schermata **Crea un cloud privato**, completare l'esercitazione [Configurare la rete per il cloud privato VMware](tutorial-configure-networking.md) **prima** di continuare con questa sezione.  

Dopo aver distribuito la soluzione VMware di Azure, verrà creata la finestra Jump della rete virtuale che si connette a vCenter e NSX. Dopo la configurazione dei circuiti ExpressRoute e di Copertura globale ExpressRoute, la jump box non è necessaria.  Risulta tuttavia utile per raggiungere vCenter e NSX nella soluzione Azure VMware.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Creare la jump box della soluzione Azure VMware" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Per creare una macchina virtuale (VM) nella rete virtuale [identificata o creata come parte del processo di distribuzione](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution), seguire queste istruzioni: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Connettersi a una rete virtuale con ExpressRoute

>[!IMPORTANT]
>Se una rete virtuale è già stata definita nella schermata di distribuzione in Azure, passare alla sezione successiva.

Se nella fase di distribuzione non è stata definita una rete virtuale e si intende connettere la ExpressRoute della soluzione VMware di Azure a un gateway ExpressRoute esistente, seguire questa procedura.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Verificare le route di rete annunciate

La Jump box si trova nella rete virtuale in cui la soluzione VMware di Azure si connette tramite il circuito ExpressRoute.  In Azure passare all'interfaccia di rete della jump box e [visualizzare le route valide](../virtual-network/manage-route-table.md#view-effective-routes).

Nell'elenco di route valide dovrebbero essere visualizzate le reti create come parte della distribuzione della soluzione Azure VMware. Verranno visualizzate più reti derivate dalla rete [`/22` definita](production-ready-deployment-steps.md#ip-address-segment) durante il [passaggio di distribuzione](#deploy-azure-vmware-solution) disponibile in precedenza in questo articolo.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Verificare le route di rete annunciate dalla soluzione Azure VMware nella Rete virtuale di Azure" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

In questo esempio la rete 10.74.72.0/22 specificata come input durante la distribuzione deriva le reti /24.  Se vengono visualizzati valori analoghi, è possibile connettersi a vCenter nella soluzione Azure VMware.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Connettersi e accedere a vCenter e NSX-T

Accedere alla jump box creata nel passaggio precedente. Una volta effettuato l'accesso, aprire un Web browser e passare a e accedere a vCenter e NSX-T Manager.  

È possibile identificare gli indirizzi IP e le credenziali della console vCenter e NSX-T Manager nel portale di Azure.  Selezionare il cloud privato e quindi nella visualizzazione **Overview** (Panoramica) selezionare **Identity > Default** (Identità > Impostazione predefinita). 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Creare un segmento di rete nella soluzione Azure VMware

Si usa NSX-T Manager per creare nuovi segmenti di rete nell'ambiente della soluzione VMware di Azure.  Sono state definite le reti che si desidera creare nella [sezione relativa alla pianificazione](production-ready-deployment-steps.md).  Se le reti non sono state definite, tornare alla [sezione di pianificazione](production-ready-deployment-steps.md) prima di continuare.

>[!IMPORTANT]
>Assicurarsi che il blocco di indirizzi di rete CIDR definito non si sovrapponga con altri elementi in Azure o negli ambienti locali.  

Per creare un segmento di rete NSX-T nella soluzione Azure VMware, seguire l'esercitazione [Creare un segmento di rete NSX-T nella soluzione Azure VMware](tutorial-nsx-t-network-segment.md).

## <a name="verify-advertised-nsx-t-segment"></a>Verificare un segmento NSX-T annunciato

Tornare al passaggio [Verificare le route di rete annunciate](#verify-network-routes-advertised). Verranno visualizzate altre route nell'elenco che rappresentano i segmenti di rete creati nel passaggio precedente.  

Per le macchine virtuali, verranno assegnati i segmenti creati nel passaggio [creare un segmento di rete in una soluzione VMware di Azure](#create-a-network-segment-on-azure-vmware-solution) .  

Poiché il server DNS è obbligatorio, identificare il server DNS da usare.  

- Se il servizio Copertura globale ExpressRoute è stato configurato, usare il server DNS che verrebbe usato in locale.  
- Se in Azure è disponibile un server DNS, usare tale server.  
- Se tali server non sono disponibili, usare il server DNS usato dalla jump box.

>[!NOTE]
>Questo passaggio consente di identificare il server DNS. Non viene eseguita alcuna operazione di configurazione.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(Facoltativo) Specificare i servizi DHCP per il segmento di rete NSX-T

Se si prevede di usare DHCP nei segmenti NSX-T, continuare con questa sezione. In caso contrario, passare alla sezione [Aggiungere una VM nel segmento di rete NSX-T](#add-a-vm-on-the-nsx-t-network-segment).  

Ora che è stato creato il segmento di rete NSX-T, è possibile creare e gestire il protocollo DHCP nella soluzione Azure VMware in due modi:

* Se si usa NSX-T per ospitare il server DHCP, sarà necessario [creare un server DHCP](manage-dhcp.md#create-a-dhcp-server) e impostare l'[inoltro a tale server](manage-dhcp.md#create-dhcp-relay-service). 
* Se si usa un server DHCP esterno di terze parti nella rete, sarà necessario [creare il servizio di inoltro DHCP](manage-dhcp.md#create-dhcp-relay-service).  Per questa opzione [eseguire solo la configurazione dell'inoltro](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Aggiungere una VM nel segmento di rete NSX-T

Nella soluzione VMware VMware di Azure, distribuire una macchina virtuale e usarla per verificare la connettività dalle reti della soluzione VMware di Azure a:

- Internet
- Reti virtuali di Azure
- In locale.  

Distribuire la VM con la stessa procedura usata in un ambiente vSphere.  Alleghi la macchina virtuale a uno dei segmenti di rete creati in precedenza in NSX-T.  

>[!NOTE]
>Se si configura un server DHCP, si riceverà la configurazione della VM da tale server. Occorre ricordare di configurare l'ambito.  Se si vuole eseguire la configurazione statica, seguire la procedura di configurazione usata normalmente.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testare la connettività del segmento NSX-T

Accedere alla VM creata nel passaggio precedente e verificare la connettività.

1. Effettuare il ping di un indirizzo IP su Internet.
2. In un Web browser passare a un sito Internet.
3. Effettuare il ping della jump box che si trova nella Rete virtuale di Azure.

La soluzione VMware di Azure è ora funzionante ed è stata stabilita la connettività da e verso la rete virtuale di Azure e Internet.

## <a name="next-steps"></a>Passaggi successivi

Nella sezione successiva si connetterà la soluzione VMware di Azure alla rete locale tramite ExpressRoute.
> [!div class="nextstepaction"]
> [Connettere la soluzione Azure VMware con l'ambiente locale](azure-vmware-solution-on-premises.md)
