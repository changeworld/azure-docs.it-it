---
title: Configurare i componenti di rete NSX nella soluzione VMware di Azure
description: Informazioni su come usare la console della soluzione VMware di Azure per configurare i segmenti di rete NSX-T.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716989"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Configurare i componenti di rete NSX nella soluzione VMware di Azure

Per impostazione predefinita, una soluzione VMware di Azure cloud privata viene fornita con NSX-T come rete definita dal software (SDDC). Viene eseguito il pre-provisioning con un gateway NSX-T di livello 0 in modalità attivo/attivo e un gateway NSX-T di primo livello predefinito in modalità attivo/standby.  Questi gateway consentono di connettere i segmenti (commutatori logici) e forniscono la connettività East-West e North-South. 

Dopo la distribuzione del cloud privato della soluzione VMware di Azure, è possibile configurare gli oggetti NSX-T necessari dalla console della soluzione VMware di Azure in **rete del carico di lavoro**.  La console presenta la visualizzazione semplificata delle operazioni di NSX-T che un amministratore VMware necessita quotidianamente e destinate agli utenti che non hanno familiarità con NSX-T.  

Sono disponibili quattro opzioni per configurare i componenti NSX-T nella console della soluzione VMware di Azure:
- **Segmenti** : creare segmenti che vengono visualizzati in NSX-T Manager e vCenter.
- **DHCP** : consente di creare un server DHCP o un inoltro DHCP se si prevede di utilizzare DHCP.
- **Mirroring delle porte** : consente di creare il mirroring delle porte per risolvere i problemi di rete.
- **DNS** : consente di creare un server d'inoltre DNS per inviare richieste DNS a un server DNS designato per la risoluzione.  

>[!NOTE]
>Sarà comunque possibile accedere alla console NSX-T Manager, in cui è possibile usare le impostazioni avanzate indicate e altre funzionalità di NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Screenshot che illustra quattro opzioni nella console della soluzione VMware di Azure per la configurazione di NSX-T.":::

## <a name="prerequisites"></a>Prerequisiti
Le macchine virtuali create o migrate nel cloud privato della soluzione VMware di Azure devono essere collegate a un segmento. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Creare un segmento NSX-T nella portale di Azure
È possibile creare e configurare un segmento NSX-T dalla console della soluzione VMware di Azure nella portale di Azure.  Questi segmenti sono connessi al gateway di livello 1 predefinito e i carichi di lavoro in questi segmenti ottengono East-West e North-South la connettività. Una volta creato, il segmento viene visualizzato in NSX-T Manager e vCenter.

>[!NOTE]
>Se si prevede di usare DHCP, è necessario [configurare un server DHCP o un inoltro DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) prima di poter creare e configurare un segmento NSX-T.

1. Nel cloud privato della soluzione VMware di Azure, in **rete del carico di lavoro** selezionare **segmenti**  >  **Aggiungi**. Specificare i dettagli per il nuovo segmento logico e selezionare **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Screenshot che illustra come aggiungere un nuovo segmento.":::
   
   - **Nome segmento** : nome del commutire logico visibile in vCenter.
   - **Gateway subnet** : indirizzo IP del gateway per la subnet del Commuter logico con un subnet mask. Le VM sono collegate a un Commuter logico e tutte le VM che si connettono a questo Commuter appartengono alla stessa subnet.  Inoltre, tutte le macchine virtuali collegate a questo segmento logico devono contenere un indirizzo IP dello stesso segmento.
   - **DHCP** (facoltativo): intervalli DHCP per un segmento logico. Un [server DHCP o un inoltro DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) deve essere configurato per l'utilizzo di DHCP sui segmenti.  
   - **Gateway connesso**  -  *Selezionato per impostazione predefinita e di sola lettura.*  Gateway di primo livello e tipo di informazioni sul segmento. 
      - **T1** : nome del gateway di primo livello in NSX-T Manager. Un cloud privato della soluzione VMware di Azure è dotato di un gateway NSX-T di livello 0 in modalità attivo/attivo e di un gateway NSX-T di primo livello predefinito in modalità attivo/standby.  I segmenti creati tramite la console della soluzione VMware di Azure si connettono solo al gateway di primo livello predefinito e i carichi di lavoro di questi segmenti ottengono East-West e North-South la connettività. È possibile creare più gateway di primo livello tramite NSX-T Manager. I gateway di primo livello creati dalla console NSX-T Manager non sono visibili nella console della soluzione VMware di Azure. 
      - **Tipo** : segmento overlay supportato dalla soluzione VMware di Azure.

   Il segmento è ora visibile nella console della soluzione VMware di Azure, in NSX-T Manger e in vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Creare un server DHCP o un inoltro DHCP nell'portale di Azure
È possibile creare un server DHCP o un inoltro direttamente dalla console della soluzione VMware di Azure nella portale di Azure. Il server DHCP o il relè si connette al gateway di primo livello, che viene creato quando si distribuisce la soluzione VMware di Azure. Tutti i segmenti in cui sono stati assegnati gli intervalli DHCP faranno parte di questo DHCP.  Dopo aver creato un server DHCP o un inoltro DHCP, è necessario definire una subnet o un intervallo a livello di segmento per utilizzarlo.

1. Nel cloud privato della soluzione VMware di Azure, in **rete del carico di lavoro**, selezionare **DHCP**  >  **Aggiungi**.

2. Selezionare **server DHCP** o **inoltro DHCP** , quindi specificare un nome per il server o l'inoltro e tre indirizzi IP. 

   >[!NOTE]
   >Per l'inoltro DHCP, per una configurazione corretta è necessario un solo indirizzo IP.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Screenshot che illustra come aggiungere un server DHCP o un inoltro DHCP nelle soluzioni VMware di Azure.":::

4. Completare la configurazione DHCP [fornendo gli intervalli DHCP nei segmenti logici](#create-an-nsx-t-segment-in-the-azure-portal) e quindi fare clic su **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Configurare il mirroring delle porte nel portale di Azure
È possibile configurare il mirroring delle porte per monitorare il traffico di rete che prevede l'invio di una copia di ogni pacchetto da una porta di commutazione di rete a un'altra. Questa opzione inserisce un analizzatore di protocollo sulla porta che riceve i dati con mirroring. Analizza il traffico da un'origine, una macchina virtuale o un gruppo di macchine virtuali e quindi inviato a una destinazione definita. 

Per configurare il mirroring delle porte nella console della soluzione VMware di Azure, è necessario:

* [Passaggio 1. Creare macchine virtuali di origine e di destinazione o gruppi di VM](#step-1-create-source-and-destination-vms-or-vm-groups) : il gruppo di origine dispone di una singola macchina virtuale o di più macchine virtuali in cui viene sottoposto a mirroring.

* [Passaggio 2. Creare un profilo di mirroring delle porte](#step-2-create-a-port-mirroring-profile) : definire la direzione del traffico per i gruppi di macchine virtuali di origine e di destinazione.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Passaggio 1. Creare macchine virtuali di origine e di destinazione o gruppi di VM

In questo passaggio verranno creati un gruppo di VM di origine e un gruppo di macchine virtuali di destinazione.

1. Nel cloud privato della soluzione VMware di Azure, in **rete del carico di lavoro** selezionare gruppi di macchine virtuali per il **mirroring delle porte**  >    >  **Aggiungi**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Screenshot che illustra come creare un gruppo di macchine virtuali per il mirroring delle porte.":::

1. Specificare un nome per il nuovo gruppo di macchine virtuali, selezionare le macchine virtuali desiderate dall'elenco e quindi **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Screenshot che mostra l'elenco di macchine virtuali da aggiungere al gruppo di VM.":::

1. Ripetere questi passaggi per creare il gruppo di macchine virtuali di destinazione.

### <a name="step-2-create-a-port-mirroring-profile"></a>Passaggio 2: Creazione di un profilo di mirroring delle porte

In questo passaggio verrà definito un profilo per la direzione del traffico dei gruppi di macchine virtuali di origine e di destinazione.

>[!NOTE]
>Verificare che siano stati creati entrambi i gruppi di macchine virtuali di origine e di destinazione.

1. Selezionare il **mirroring delle porte**  >  **Aggiungi** e quindi specificare:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Screenshot che mostra le informazioni necessarie per il profilo di mirroring delle porte.":::

   - Nome di **mirroring delle porte** : nome descrittivo per il profilo.
   - **Direction** : selezionare da ingresso, uscita o bidirezionale.
   - **Origine** : selezionare il gruppo di VM di origine.
   - **Destinazione** : selezionare il gruppo di macchine virtuali di destinazione.
   - **Descrizione** : immettere una descrizione per il mirroring delle porte.

1. Selezionare **OK** per completare il profilo. 

   Il profilo e i gruppi di macchine virtuali sono visibili nella console della soluzione VMware di Azure.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configurare un server d'inoltre DNS nel portale di Azure
Si configurerà un server di destinazione DNS in cui richieste DNS specifiche vengono inviate a un server DNS designato per la risoluzione.  Un server d'invio DNS è associato a una **zona DNS predefinita** e a un massimo di tre **zone FQDN**.

>[!TIP]
>È anche possibile usare la [console NSX-T Manager per configurare un server d'utilità DNS](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Per configurare un server d'installazione DNS nella console della soluzione VMware di Azure, è necessario:

* [Passaggio 1. Configurare una zona DNS e un'area di FQDN predefinite](#step-1-configure-a-default-dns-zone-and-fqdn-zone) : quando viene ricevuta una query DNS, un server di un server d'un server di un server di un server di un server di dominio esegue il confronto con i nomi di dominio nella zona DNS 

* [Passaggio 2. Configurare il servizio DNS](#step-2-configure-dns-service) : verrà configurato il servizio server di trasmissione DNS.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Passaggio 1. Configurare una zona DNS e una zona FQDN predefinite
Verrà configurata una zona DNS predefinita e una zona FQDN per inviare query DNS al server upstream.  Quando viene ricevuta una query DNS, il server d'invio DNS confronta il nome di dominio nella query con i nomi di dominio delle zone DNS di FQDN. Se viene trovata una corrispondenza, la query viene trasmessa ai server DNS specificati nella zona DNS FQDN. Se non viene trovata alcuna corrispondenza, la query viene trasmessa ai server DNS specificati nella zona DNS predefinita.

>[!NOTE]
>Prima di configurare una zona FQDN, è necessario definire una zona DNS predefinita. 

1. Nel cloud privato della soluzione VMware di Azure, in **rete del carico di lavoro**, selezionare   >  **zone DNS** DNS  >  **Aggiungi**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Screenshot che illustra come aggiungere zone DNS e un servizio DNS.":::

1. Selezionare la **zona DNS predefinita** e specificare:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Screenshot che illustra come aggiungere una zona DNS predefinita.":::

   1. Nome per la zona DNS.

   1. Fino a tre indirizzi IP del server DNS nel formato **8.8.8.8**.

1. Selezionare l' **area FQDN** e specificare:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Screenshot che illustra come aggiungere una zona FQDN. ":::

   1. Nome per la zona DNS.

   1. Dominio FQDN.

   1. Fino a tre indirizzi IP del server DNS nel formato **8.8.8.8**.

1. Selezionare **OK** per completare l'aggiunta della zona DNS e del servizio DNS predefiniti.

### <a name="step-2-configure-dns-service"></a>Passaggio 2: Configurare il servizio DNS

1. Selezionare la scheda **servizio DNS** e selezionare **Aggiungi**. Specificare i dettagli e fare clic su **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Screenshot che mostra le informazioni necessarie per il servizio DNS.":::

   >[!TIP]
   >Il **gateway di primo livello** è selezionato per impostazione predefinita e riflette il gateway creato durante la distribuzione della soluzione VMware di Azure.

   Il servizio DNS è stato aggiunto correttamente.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Screenshot che indica che il servizio DNS è stato aggiunto correttamente.":::

