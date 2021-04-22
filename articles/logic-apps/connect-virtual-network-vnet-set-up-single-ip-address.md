---
title: Configurare un indirizzo IP in uscita pubblico per le ise
description: Informazioni su come configurare un singolo indirizzo IP in uscita pubblico per gli ambienti del servizio di integrazione (ISE) in App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874190"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Configurare un singolo indirizzo IP per uno o più ambienti del servizio di integrazione in App per la logica di Azure

Quando si lavora con App per la logica di Azure, è possibile configurare un ambiente del servizio di integrazione [  (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per l'hosting di app per la logica che devono accedere alle risorse in una [rete virtuale di Azure.](../virtual-network/virtual-networks-overview.md) Quando sono presenti più istanze ISE che devono accedere ad altri endpoint con restrizioni IP, distribuire un [Firewall di Azure](../firewall/overview.md) o [un'appliance](../virtual-network/virtual-networks-overview.md#filter-network-traffic) virtuale di rete nella rete virtuale e instradare il traffico in uscita attraverso tale firewall o appliance virtuale di rete. È quindi possibile fare in modo che tutte le istanze ISE nella rete virtuale usino un singolo indirizzo IP pubblico, statico e prevedibile per comunicare con i sistemi di destinazione desiderati. In questo modo, non è necessario configurare altre aperture del firewall nei sistemi di destinazione per ogni ISE.

Questo argomento illustra come instradare il traffico in uscita attraverso un Firewall di Azure, ma è possibile applicare concetti simili a un'appliance virtuale di rete, ad esempio un firewall di terze parti dal Azure Marketplace. Anche se questo argomento è in particolare relativo alla configurazione per più istanze di ISE, è anche possibile usare questo approccio per un singolo ISE quando lo scenario richiede la limitazione del numero di indirizzi IP che richiedono l'accesso. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale hanno senso per lo scenario. Altre informazioni su [Prezzi del servizio Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Prerequisiti

* Un firewall di Azure eseguito nella stessa rete virtuale dell'ISE. Se non si dispone di un firewall, aggiungere prima di tutto una [subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominata `AzureFirewallSubnet` alla rete virtuale. È quindi possibile [creare e distribuire un firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) nella rete virtuale.

* Tabella di [route di](../virtual-network/manage-route-table.md)Azure . Se non se ne ha una, creare [prima una tabella di route](../virtual-network/manage-route-table.md#create-a-route-table). Per altre informazioni sul routing, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurare la tabella di route

1. Nel [portale di Azure](https://portal.azure.com)selezionare la tabella di route, ad esempio:

   ![Selezionare la tabella di route con la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Per [aggiungere una nuova route,](../virtual-network/manage-route-table.md#create-a-route)nel menu della tabella di route selezionare Route   >  **Aggiungi.**

   ![Aggiungere una route per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Nel riquadro **Aggiungi route** configurare la nuova [route](../virtual-network/manage-route-table.md#create-a-route) con una regola che specifica che tutto il traffico in uscita verso il sistema di destinazione segue questo comportamento:

   * Usa [**l'appliance virtuale**](../virtual-network/virtual-networks-udr-overview.md#user-defined) come tipo di hop successivo.

   * Passa all'indirizzo IP privato per l'istanza del firewall come indirizzo hop successivo.

     Per trovare questo indirizzo IP, nel menu del firewall selezionare **Panoramica,** trovare l'indirizzo in **Indirizzo IP privato,** ad esempio:

     ![Trovare l'indirizzo IP privato del firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Di seguito è riportato un esempio che illustra l'aspetto di una regola di questo tipo:

   ![Configurare la regola per l'indirizzamento del traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome route** | <*unique-route-name*> | Nome univoco per la route nella tabella di route |
   | **Prefisso indirizzo** | <*indirizzo di destinazione*> | Prefisso dell'indirizzo per il sistema di destinazione in cui si vuole che il traffico in uscita sia indirizzato. Assicurarsi di usare la [notazione Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per questo indirizzo. In questo esempio il prefisso dell'indirizzo è per un server SFTP, descritto nella sezione [Configurare la regola di rete](#set-up-network-rule). |
   | **Tipo hop successivo** | **Appliance virtuale** | Tipo [di hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) usato dal traffico in uscita |
   | **Indirizzo hop successivo** | <*firewall-private-IP-address*> | Indirizzo IP privato per il firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Configurare la regola di rete

1. Nel portale di Azure trovare e selezionare il firewall. Nel menu del firewall in **Impostazioni** selezionare **Regole**. Nel riquadro regole selezionare Raccolta regole di rete  >  **Aggiungi raccolta regole di rete**.

   ![Aggiungere la raccolta di regole di rete al firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Nella raccolta aggiungere una regola che consenta il traffico verso il sistema di destinazione.

   Si supponga, ad esempio, di avere un'app per la logica che viene eseguita in un'ise e deve comunicare con un server SFTP. Si crea una raccolta di regole di rete denominata `LogicApp_ISE_SFTP_Outbound` , che contiene una regola di rete denominata `ISE_SFTP_Outbound` . Questa regola consente il traffico dall'indirizzo IP di qualsiasi subnet in cui l'ISE viene eseguito nella rete virtuale al server SFTP di destinazione usando l'indirizzo IP privato del firewall.

   ![Configurare la regola di rete per il firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Proprietà della raccolta di regole di rete**

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*network-rule-collection-name*> | Nome della raccolta di regole di rete |
   | **Priorità** | <*livello di priorità*> | Ordine di priorità da usare per l'esecuzione della raccolta di regole. Per altre informazioni, vedere [Quali sono alcuni concetti Firewall di Azure?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Azione** | **Consentito** | Tipo di azione da eseguire per questa regola |
   |||

   **Proprietà delle regole di rete**

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*network-rule-name*> | Nome della regola di rete |
   | **Protocollo** | <*protocolli di connessione*> | Protocolli di connessione da utilizzare. Ad esempio, se si usano regole del gruppo di sicurezza di rete, selezionare **sia TCP** che **UDP,** non solo **TCP.** |
   | **Indirizzi di origine** | <*ISE-subnet-addresses*> | Indirizzi IP della subnet in cui viene eseguito l'ISE e da dove ha origine il traffico dall'app per la logica |
   | **Indirizzi di destinazione** | <*indirizzo IP di destinazione*> | L'indirizzo IP del sistema di destinazione in cui si vuole che il traffico in uscita sia indirizzato. In questo esempio, questo indirizzo IP è per il server SFTP. |
   | **Porte di destinazione** | <*porte di destinazione*> | Tutte le porte utilizzate dal sistema di destinazione per la comunicazione in ingresso |
   |||

   Per altre informazioni sulle regole di rete, vedere gli articoli seguenti:

   * [Configurare una regola di rete](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logica di elaborazione delle regole del Firewall di Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Domande frequenti su Firewall di Azure](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfaccia della riga di comando di Azure: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
