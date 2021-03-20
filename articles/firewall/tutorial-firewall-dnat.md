---
title: 'Esercitazione: Filtrare il traffico Internet in ingresso con DNAT di Firewall di Azure tramite il portale'
description: Questa esercitazione mostra come distribuire e configurare DNAT di Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741906"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Esercitazione: Filtrare il traffico Internet in ingresso con DNAT di Firewall di Azure tramite il portale di Azure

È possibile configurare la modalità DNAT (Destination Network Address Translation) di Firewall di Azure per convertire e filtrare il traffico Internet in ingresso nelle subnet. Quando si configura la modalità DNAT, l'azione di raccolta delle regole NAT è impostata su **Dnat**. Ogni regola nella raccolta regole NAT può quindi essere usata per convertire la porta e l'indirizzo IP pubblico del firewall in un indirizzo IP privato e una porta. Le regole DNAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. Per motivi di sicurezza, l'approccio consigliato consiste nell'aggiungere un'origine Internet specifica per consentire l'accesso DNAT alla rete ed evitare l'uso di caratteri jolly. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Testare il firewall

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.



## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nella home page del portale di Azure selezionare **Gruppi di risorse** e quindi selezionare **Aggiungi**.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Nome del gruppo di risorse** digitare **RG-DNAT-Test**.
5. Per **area** selezionare un'area. Tutte le altre risorse create devono trovarsi nella stessa area.
6. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea).

## <a name="set-up-the-network-environment"></a>Configurare l'ambiente di rete

Per questa esercitazione vengono create due reti virtuali con peering:

- **VN-Hub**: in questa rete virtuale si trova il firewall.
- **VN-Spoke**: in questa rete virtuale si trova il server del carico di lavoro.

Creare innanzitutto le reti virtuali e quindi eseguire il peering.

### <a name="create-the-hub-vnet"></a>Creare la rete virtuale dell'hub

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Reti virtuali**.
3. Selezionare **Aggiungi**.
7. Per **gruppo di risorse** selezionare **RG-DNAT-test**.
1. In **Nome** digitare **VN-Hub**.
1. Per **area** selezionare la stessa area usata in precedenza.
1. Selezionare **Avanti: Indirizzi IP**.
1. Per **spazio indirizzi IPv4** accettare il valore predefinito **10.0.0.0/16**.
1. In **nome subnet** selezionare predefinito.
1. Modificare il **nome della subnet** e digitare **AzureFirewallSubnet**.

     Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
     > [!NOTE]
     > La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Per **intervallo di indirizzi subnet** Digitare **10.0.1.0/26**.
11. Selezionare **Salva**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

### <a name="create-a-spoke-vnet"></a>Creare un rete virtuale spoke

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Reti virtuali**.
3. Selezionare **Aggiungi**.
1. Per **gruppo di risorse** selezionare **RG-DNAT-test**.
1. In **Nome** digitare **VN-Spoke**.
1. Per **area** selezionare la stessa area usata in precedenza.
1. Selezionare **Avanti: Indirizzi IP**.
1. Per **spazio indirizzi IPv4**, modificare il valore predefinito e digitare **192.168.0.0/16**.
1. Selezionare **Aggiungi subnet**.
1. Per il **nome della subnet** digitare **sn-workload**.
10. Per **Intervallo di indirizzi subnet**, digitare **192.168.1.0/24**.
11. Selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

### <a name="peer-the-vnets"></a>Eseguire il peering delle reti virtuali

Ora è il momento di eseguire il peering delle due reti virtuali.

1. Selezionare la rete virtuale **VN-Hub**.
2. In **Impostazioni** selezionare **Peer**.
3. Selezionare **Aggiungi**.
4. In **questa rete virtuale**, per il **nome del collegamento di peering**, digitare **peer-HubSpoke**.
5. In **rete virtuale remota**, per **nome collegamento peering**, digitare **peer-SpokeHub**. 
1. Selezionare **VN-Spoke** per la rete virtuale.
1. Accettare tutte le altre impostazioni predefinite, quindi selezionare **Aggiungi**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale del carico di lavoro e inserirla nella subnet **SN-Workload**.

1. Nel menu del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.

**Nozioni di base**

1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **gruppo di risorse** selezionare **RG-DNAT-test**.
1. In **Nome macchina virtuale** digitare **Srv-Workload**.
1. In **Area** selezionare la stessa località usata in precedenza.
1. Immettere un nome utente e la password.
1. Selezionare **Avanti: Dischi**.

**Dischi**
1. Selezionare **Avanti: Rete**.

**Rete**

1. In **Rete virtuale** selezionare **VN-Spoke**.
2. In **Subnet** selezionare **SN-Workload**.
3. In **IP pubblico** selezionare **Nessuno**.
4. In **Porte in ingresso pubbliche** selezionare **Nessuna**. 
2. Lasciare le altre impostazioni predefinite e selezionare **Avanti: gestione**.

**Gestione**

1. Per **diagnostica di avvio** selezionare **Disabilita**.
1. Selezionare **Rivedi e crea**.

**Rivedi e crea**

Esaminare il riepilogo e quindi selezionare **Crea**. Questa operazione richiederà qualche minuto.

Al termine della distribuzione, prendere nota dell'indirizzo IP privato della macchina virtuale. Sarà necessario più avanti per la configurazione del firewall. Selezionare il nome della macchina virtuale e in **Impostazioni** selezionare **Rete** per trovare l'indirizzo IP privato.

## <a name="deploy-the-firewall"></a>Distribuire il firewall

1. Dalla home page del portale selezionare **Crea una risorsa**.
1. Cercare **Firewall**, quindi selezionare **Firewall**.
1. Selezionare **Crea**. 
1. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

   |Impostazione  |Valore  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |Selezionare **RG-DNAT-test** |
   |Nome     |**FW-DNAT-test**|
   |Region     |Selezionare la stessa località usata in precedenza|
   |Gestione del firewall|**Usare le regole del firewall (classiche) per gestire questo firewall**|
   |Scegliere una rete virtuale     |**Usa esistente**: VN-Hub|
   |Indirizzo IP pubblico     |**Aggiungere nuovo**, nome: **FW-PIP**.|

5. Accettare le altre impostazioni predefinite, quindi selezionare **Verifica + crea**.
6. Controllare il riepilogo e quindi selezionare **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
7. Al termine della distribuzione, passare al gruppo di risorse **RG-DNAT-Test** e selezionare il firewall **FW-DNAT-test**.
8. Prendere nota degli indirizzi IP pubblici e privati del firewall. Verranno usati in un secondo momento quando si crea la route predefinita e la regola NAT.

## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **SN-Workload** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Tabelle route**.
3. Selezionare **Aggiungi**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **gruppo di risorse** selezionare **RG-DNAT-test**.
1. In **Area** selezionare la stessa area usata in precedenza.
1. In **Nome** digitare **RT-FWroute**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.
1. Selezionare **Vai alla risorsa**.
1. Selezionare **Subnet** e quindi **Associa**.
1. In **Rete virtuale** selezionare **VN-Spoke**.
1. In **Subnet** selezionare **SN-Workload**.
1. Selezionare **OK**.
1. Selezionare **Route** e quindi **Aggiungi**.
1. In **Nome route** immettere **FW-DG**.
1. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
1. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Selezionare **OK**.

## <a name="configure-a-nat-rule"></a>Configurare una regola NAT

1. Aprire il gruppo di risorse **RG-DNAT-test** e selezionare il firewall **FW-DNAT-test** . 
2. Nella pagina **FW-DNAT-test** , in **Impostazioni**, selezionare **regole (classico)**. 
3. Selezionare **Aggiungi raccolta regole NAT**. 
4. Per **Nome** digitare **RC-DNAT-01**. 
5. In **Priorità** immettere **200**. 
6. In **Regole** per **Nome** digitare **RL-01**.
7. In **Protocollo** selezionare **TCP**.
1. In **Tipo di origine** selezionare **Indirizzo IP**.
1. Per **source** Digitare *. 
1. Per **indirizzi di destinazione**, digitare l'indirizzo IP pubblico del firewall. 
1. Per **Porte di destinazione** digitare **3389**. 
1. Per **Indirizzo convertito** digitare l'indirizzo IP privato per la macchina virtuale Srv-Workload. 
1. Per **Porta tradotta** digitare **3389**. 
1. Selezionare **Aggiungi**. Questa operazione richiederà qualche minuto.

## <a name="test-the-firewall"></a>Testare il firewall

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall. Dovrebbe essere stabilita una connessione alla macchina virtuale **Srv-Workload**.
2. Chiudere il desktop remoto.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **RG-DNAT-Test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Testare il firewall

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./firewall-diagnostics.md)
