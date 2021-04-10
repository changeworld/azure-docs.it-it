---
title: Pianificazione della distribuzione della soluzione Azure VMware
description: Questo articolo illustra un flusso di lavoro di distribuzione della soluzione Azure VMware.  Il risultato finale è un ambiente pronto per la creazione e la migrazione di macchine virtuali.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 2ded5d706ab71b3880633cd324fb366d0a1bccbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584636"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Pianificazione della distribuzione della soluzione Azure VMware

Questo articolo illustra il processo di pianificazione per identificare e raccogliere le informazioni che verranno usate durante la distribuzione. Nell'ambito della pianificazione della distribuzione, assicurarsi di documentare le informazioni raccolte per potervi fare riferimento facilmente durante la distribuzione.

La procedura descritta in questa Guida introduttiva fornisce un ambiente di produzione per la creazione di macchine virtuali (VM) e la migrazione. 

>[!IMPORTANT]
>Prima di creare la risorsa della soluzione Azure VMware, seguire l'articolo [Come abilitare la risorsa della soluzione Azure VMware](enable-azure-vmware-solution.md) per inviare un ticket di supporto e ottenere l'allocazione degli host. Dopo la ricezione della richiesta da parte del team di supporto, sono necessari fino a cinque giorni per completare la richiesta e allocare gli host. Se è disponibile un cloud privato della soluzione Azure VMware ed è necessaria l'allocazione di altri host, sarà necessario seguire lo stesso processo. 

## <a name="subscription"></a>Subscription

Identificare la sottoscrizione che si vuole usare per distribuire la soluzione Azure VMware.  È possibile creare una nuova sottoscrizione o riutilizzare una sottoscrizione esistente.

>[!NOTE]
>La sottoscrizione deve essere associata a un Contratto Enterprise Microsoft o a un piano di Azure Cloud Solution Provider. Per altre informazioni, vedere [Come abilitare la risorsa della soluzione Azure VMware](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Resource group

Identificare il gruppo di risorse da usare per la soluzione Azure VMware.  Viene in genere creato un gruppo di risorse specifico per la soluzione Azure VMware, ma è possibile usare un gruppo di risorse esistente.

## <a name="region"></a>Area

Identificare l'area in cui si vuole distribuire la soluzione Azure VMware.  Per altre informazioni, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nome risorsa

Definire il nome della risorsa da usare durante la distribuzione.  Il nome della risorsa è un nome descrittivo usato per il cloud privato della soluzione Azure VMware.

>[!IMPORTANT]
>Il nome deve essere composto da un massimo di 40 caratteri. Se supera questo limite, non sarà possibile creare indirizzi IP pubblici da usare con il cloud privato. 

## <a name="size-hosts"></a>Host dimensioni

Identificare gli host dimensioni da usare durante la distribuzione della soluzione Azure VMware.  Per un elenco completo, vedere la documentazione dei [cloud privati e dei cluster della soluzione Azure VMware](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-clusters-and-hosts"></a>Numero di cluster e host

La prima distribuzione della soluzione VMware di Azure sarà costituita da un cloud privato contenente un singolo cluster. Per la distribuzione, è necessario definire il numero di host che si desidera distribuire nel primo cluster.

>[!NOTE]
>Il numero minimo di host per cluster è tre e il valore massimo è 16. Il numero massimo di cluster per ogni cloud privato è quattro. 

Per altre informazioni, vedere la documentazione dei [cloud privati e dei cluster della soluzione Azure VMware](concepts-private-clouds-clusters.md#clusters).

>[!TIP]
>Se è necessario superare il numero di distribuzione iniziale, è sempre possibile estendere il cluster e aggiungere altri cluster in un secondo momento.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segmento di indirizzi IP per la gestione del cloud privato

Il primo passaggio della pianificazione della distribuzione consiste nel pianificare la segmentazione IP. Per la soluzione VMware di Azure è necessaria una rete CIDR/22. Questo spazio di indirizzi viene suddiviso in segmenti di rete più piccoli (subnet) e usato per i segmenti di gestione delle soluzioni VMware di Azure, incluse le funzionalità vCenter, VMware HCX, NSX-T e vMotion. La visualizzazione seguente evidenzia la posizione in cui verrà usato questo segmento.

Questo blocco di indirizzi di rete CIDR/22 non deve sovrapporsi a un segmento di rete esistente già in locale o in Azure.

**Esempio:** 10.0.0.0/22

Per informazioni dettagliate sul modo in cui la rete CIDR/22 viene suddivisa in base all' [elenco di controllo della pianificazione della rete](tutorial-network-checklist.md#routing-and-subnet-considerations)del cloud privato.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento dell'indirizzo IP per carichi di lavoro di macchine virtuali

Analogamente a qualsiasi ambiente VMware, le macchine virtuali devono connettersi a un segmento di rete. Nella soluzione VMware di Azure sono disponibili due tipi di segmenti, i segmenti estesi L2 (descritti in seguito) e i segmenti di rete NSX-T. Quando si espande la distribuzione di produzione della soluzione VMware di Azure, è spesso presente una combinazione di segmenti estesi L2 da segmenti di rete locali e NSX-T locali. Per pianificare la distribuzione iniziale, nella soluzione VMware di Azure identificare un singolo segmento di rete (rete IP). Questa rete non deve sovrapporsi ad alcun segmento di rete locale o nel resto di Azure e non deve trovarsi entro il segmento di rete/22 definito in precedenza.

Questo segmento di rete viene usato principalmente a scopo di test durante la distribuzione iniziale.

>[!NOTE]
>Questa rete o le reti non saranno necessarie durante la distribuzione. Vengono creati come passaggio di post-distribuzione.
  
**Esempio:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP per carichi di lavoro di macchine virtuali" border="false":::     

## <a name="optional-extend-your-networks"></a>Opzionale Estendi le tue reti

È possibile estendere segmenti di rete da locale alla soluzione Azure VMware. Se si esegue questa operazione, è necessario identificare adesso queste reti.  

Occorre ricordare che:

- Se si prevede di estendere le reti da locale, tali reti devono connettersi a un [commutatore distribuito di vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) nell'ambiente VMware locale.  
- Se la rete o le reti da estendere si trovano in un [commutatore standard di vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), non sarà possibile estenderle.

>[!NOTE]
>Queste reti vengono estese come passaggio finale della configurazione, non durante la distribuzione.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Connetti la rete virtuale di Azure alla soluzione VMware di Azure

Per fornire connettività alla soluzione VMware di Azure, un ExpressRoute viene creato dal cloud privato della soluzione VMware di Azure a un gateway di rete virtuale ExpressRoute.

È possibile usare un gateway di rete virtuale ExpressRoute *nuovo* o *esistente* .

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identificazione - Rete virtuale di Azure da collegare alla soluzione Azure VMware" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Usare un gateway di rete virtuale ExpressRoute esistente

Se si prevede di usare un gateway di rete virtuale ExpressRoute *esistente* , il circuito ExpressRoute della soluzione VMware di Azure viene stabilito come passaggio di post-distribuzione. In questo caso, lasciare vuoto il campo **rete virtuale** .

Come raccomandazione generale, è accettabile usare un gateway di rete virtuale ExpressRoute esistente. Ai fini della pianificazione, prendere nota del gateway di rete virtuale ExpressRoute da usare e quindi continuare con il passaggio successivo.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Creare un nuovo gateway di rete virtuale ExpressRoute

Quando si crea un *nuovo* gateway di rete virtuale ExpressRoute, è possibile usare una rete virtuale di Azure esistente o crearne uno nuovo.  

- Per una rete virtuale di Azure esistente:
   1. Identificare una rete virtuale di Azure in cui non sono presenti gateway di rete virtuale ExpressRoute preesistenti.
   2. Prima della distribuzione, creare un [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) nella rete virtuale di Azure.

- Per una nuova rete virtuale di Azure, è possibile crearla in anticipo o durante la distribuzione. Selezionare il collegamento **Crea nuovo** nell'elenco **rete virtuale** .

Nell'immagine seguente viene mostrata la schermata **creare una distribuzione cloud privata** con il campo **rete virtuale** evidenziato.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Screenshot della schermata di distribuzione della soluzione VMware di Azure con il campo rete virtuale evidenziato.":::

>[!NOTE]
>Eventuali reti virtuali che verranno usate o create possono essere visualizzate dall'ambiente locale e dalla soluzione VMware di Azure, quindi assicurarsi che il segmento IP usato in questa rete virtuale e le subnet non si sovrappongano.

## <a name="vmware-hcx-network-segments"></a>Segmenti di rete di VMware HCX

VMware HCX è una tecnologia inclusa nella soluzione Azure VMware. I casi d'uso principali per VMware HCX sono costituiti da migrazioni dei carichi di lavoro e ripristino di emergenza. Se si prevede di eseguire una di queste operazioni, è consigliabile pianificare ora le risorse di rete.   In caso contrario, è possibile ignorare questo passaggio e procedere al passaggio successivo.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere raccolto e documentato le informazioni necessarie, passare alla sezione successiva per creare il cloud privato della soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Distribuire la soluzione Azure VMware](deploy-azure-vmware-solution.md)
