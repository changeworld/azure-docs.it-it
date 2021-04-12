---
title: Proteggere il traffico destinato a endpoint privati nella rete WAN virtuale di Azure
description: Informazioni su come usare le regole di rete e le regole dell'applicazione per proteggere il traffico destinato a endpoint privati nella rete WAN virtuale di Azure
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259354"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Proteggere il traffico destinato a endpoint privati nella rete WAN virtuale di Azure

> [!NOTE]
> Questo articolo si applica solo all'hub virtuale protetto. Se si vuole controllare il traffico destinato agli endpoint privati usando il firewall di Azure in una rete virtuale Hub, vedere [usare il firewall di Azure per controllare il traffico destinato a un endpoint privato](../private-link/inspect-traffic-with-azure-firewall.md).

[Endpoint privato di Azure](../private-link/private-endpoint-overview.md) è il blocco predefinito fondamentale per il [collegamento privato di Azure](../private-link/private-link-overview.md). Gli endpoint privati consentono alle risorse di Azure distribuite in una rete virtuale di comunicare privatamente con risorse di collegamento privato.

Gli endpoint privati consentono alle risorse di accedere al servizio di collegamento privato distribuito in una rete virtuale. L'accesso all'endpoint privato tramite il peering di rete virtuale e le connessioni di rete locali estendono la connettività.

Potrebbe essere necessario filtrare il traffico dai client in locale o in Azure destinati ai servizi esposti tramite endpoint privati in una rete virtuale connessa WAN virtuale. Questo articolo illustra questa attività usando l' [hub virtuale protetto](../firewall-manager/secured-virtual-hub.md) con il [firewall di Azure](../firewall/overview.md) come provider di sicurezza.

Il firewall di Azure filtra il traffico usando uno dei metodi seguenti:

* [FQDN nelle regole di rete](../firewall/fqdn-filtering-network-rules.md) per i protocolli TCP e UDP
* [FQDN nelle regole dell'applicazione](../firewall/features.md#application-fqdn-filtering-rules) per http, HTTPS e MSSQL.
* Indirizzi IP di origine e destinazione, porta e protocollo con [le regole di rete](../firewall/features.md#network-traffic-filtering-rules)

Usare le regole dell'applicazione per le regole di rete per controllare il traffico destinato a endpoint privati.
Un hub virtuale protetto è gestito da Microsoft e non può essere collegato a una [zona DNS privato](../dns/private-dns-privatednszone.md). Questa operazione è necessaria per risolvere un nome di dominio completo della [risorsa di collegamento privato](../private-link/private-endpoint-overview.md#private-link-resource) nell'indirizzo IP dell'endpoint privato corrispondente.

Il filtro FQDN di SQL è supportato solo in modalità [proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). La modalità *proxy* può comportare una latenza maggiore rispetto al *Reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando FQDN nelle regole di rete del firewall.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrare il traffico usando FQDN nelle regole di rete e dell'applicazione

I passaggi seguenti consentono al firewall di Azure di filtrare il traffico usando il nome di dominio completo nelle regole della rete e dell'applicazione:

1. Distribuire una macchina virtuale del server di sicurezza [DNS](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) in una rete virtuale connessa all'hub virtuale protetto e collegata alle zone DNS privato che ospitano i tipi di record a per gli endpoint privati.

2. Configurare [le impostazioni DNS personalizzate](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) in modo che puntino all'indirizzo IP della macchina virtuale del server d'inoltro DNS e abilitare il proxy DNS nei criteri firewall associati al firewall di Azure distribuito nell'hub virtuale protetto.

3. Configurare [server DNS personalizzati](../virtual-network/manage-virtual-network.md#change-dns-servers) per le reti virtuali connesse all'hub virtuale protetto in modo che puntino all'indirizzo IP privato associato al firewall di Azure distribuito nell'hub virtuale protetto.

4. Configurare i server DNS locali per l'invio di query DNS per le zone DNS pubbliche degli endpoint privati all'indirizzo IP privato associato al firewall di Azure distribuito nell'hub virtuale protetto.

5. Configurare una [regola dell'applicazione](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) o una [regola di rete](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) come necessario nei criteri del firewall associati al firewall di Azure distribuito nell'hub virtuale protetto con il *tipo di destinazione* FQDN e il nome FQDN pubblico della risorsa di collegamento privato come *destinazione*.

6. Passare a *Hub virtuali protetti* nel criterio firewall associato al firewall di Azure distribuito nell'hub virtuale protetto e selezionare l'hub virtuale protetto in cui verranno configurati i filtri di traffico destinati agli endpoint privati.

7. Passare a **Configurazione sicurezza**, selezionare **Invia tramite il firewall di Azure** in **traffico privato**.

8. Selezionare **prefissi di traffico privati** per modificare i prefissi CIDR che verranno controllati tramite il firewall di Azure nell'hub virtuale protetto e aggiungere 32 un prefisso per ogni endpoint privato, come indicato di seguito:

   > [!IMPORTANT]
   > Se questi prefissi/32 non sono configurati, il traffico destinato agli endpoint privati ignorerà il firewall di Azure.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configurazione della sicurezza di gestione firewall" border="true":::

Questi passaggi funzionano solo quando i client e gli endpoint privati vengono distribuiti in reti virtuali diverse connesse allo stesso hub virtuale protetto e ai client locali. Se i client e gli endpoint privati vengono distribuiti nella stessa rete virtuale, è necessario creare una UDR con/32 route per gli endpoint privati. Configurare queste route con il **tipo di hop successivo**  impostato su **appliance virtuale** e l' **indirizzo hop successivo** impostato sull'indirizzo IP privato del firewall di Azure distribuito nell'hub virtuale protetto. La **propagazione delle route del gateway** deve essere impostata su **Sì**.

Il diagramma seguente illustra i flussi di traffico dati e DNS per i diversi client per la connessione a un endpoint privato distribuito nella rete WAN virtuale di Azure:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Flussi di traffico" border="true":::

## <a name="troubleshooting"></a>Risoluzione dei problemi

I principali problemi che potrebbero verificarsi quando si tenta di filtrare il traffico destinato a endpoint privati tramite l'hub virtuale protetto sono:

- I client non sono in grado di connettersi agli endpoint privati.

- Il firewall di Azure viene ignorato. Questo sintomo può essere convalidato in assenza di voci di log di regole di rete o di applicazione nel firewall di Azure.

Nella maggior parte dei casi, questi problemi sono causati da uno dei problemi seguenti:

- Risoluzione dei nomi DNS non corretta

- Configurazione di routing non corretta

### <a name="incorrect-dns-name-resolution"></a>Risoluzione dei nomi DNS non corretta

1. Verificare che i server DNS della rete virtuale siano impostati su *personalizzato* e che l'indirizzo IP sia l'indirizzo IP privato del firewall di Azure nell'hub virtuale protetto.

   Interfaccia della riga di comando di Azure:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Verificare che i client nella stessa rete virtuale in cui la macchina virtuale del server di recapito DNS possano risolvere il nome di dominio completo pubblico dell'endpoint privato nell'indirizzo IP privato corrispondente eseguendo una query direttamente sulla macchina virtuale configurata come server di trasmissione DNS.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Controllare le voci di log del firewall di Azure *AzureFirewallDNSProxy* e convalidare la ricezione e la risoluzione delle query DNS dai client.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Verificare che il *proxy DNS* sia stato abilitato e che un server DNS *personalizzato* che punta all'indirizzo IP dell'indirizzo IP della macchina virtuale del server di inoltro DNS sia stato configurato nel criterio firewall associato al firewall di Azure nell'hub virtuale protetto.

   Interfaccia della riga di comando di Azure:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Configurazione di routing non corretta

1. Verificare la *configurazione di sicurezza* nei criteri firewall associati al firewall di Azure distribuito nell'hub virtuale protetto. Assicurarsi che nella colonna **traffico privato** venga visualizzato come **protetto dal firewall di Azure** per tutte le connessioni di rete virtuale e Branch per le quali si vuole filtrare il traffico.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Traffico privato protetto dal firewall di Azure" border="true":::

2. Verificare la **configurazione di sicurezza** nei criteri firewall associati al firewall di Azure distribuito nell'hub virtuale protetto. Assicurarsi che sia presente una voce/32 per ogni indirizzo IP privato dell'endpoint privato di cui si vuole filtrare il traffico in **prefissi di traffico privati**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Configurazione della sicurezza di gestione firewall-prefissi di traffico privato" border="true":::

3. Nell'hub virtuale protetto in WAN virtuale esaminare le route valide per le tabelle di route associate alle connessioni di reti virtuali e Branch per le quali si vuole filtrare il traffico. Verificare che siano presenti/32 voci per ogni indirizzo IP privato dell'endpoint privato per cui si vuole filtrare il traffico.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Route effettive dell'hub virtuale protetto" border="true":::

4. Esaminare le route valide sulle schede di rete collegate alle macchine virtuali distribuite nelle reti virtuali per cui si vuole filtrare il traffico. Verificare che siano presenti/32 voci per ogni indirizzo IP privato dell'endpoint privato per cui si vuole filtrare il traffico.
 
   Interfaccia della riga di comando di Azure:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Esaminare le tabelle di routing dei dispositivi di routing locali. Assicurarsi di conoscere gli spazi di indirizzi delle reti virtuali in cui vengono distribuiti gli endpoint privati.

   La rete WAN virtuale di Azure non annuncia i prefissi configurati in **prefissi di traffico privati** in **configurazione della sicurezza** dei criteri firewall in locale. Si prevede che le voci/32 non verranno visualizzate nelle tabelle di routing dei dispositivi di routing locali.

6. Esaminare i log del firewall di Azure per **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** . Verificare che il traffico destinato agli endpoint privati venga registrato.

   Le voci di log di **AzureFirewallNetworkRule** non includono informazioni di FQDN. Filtrare in base all'indirizzo IP e alla porta quando si esaminano le regole di rete.

   Quando si filtra il traffico destinato a [file di Azure](../storage/files/storage-files-introduction.md) endpoint privati, le voci di log di **AzureFirewallNetworkRule** vengono generate solo quando un client esegue la prima operazione di montaggio o connessione alla condivisione file. Il firewall di Azure non genererà i log per le operazioni [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) per i file nella condivisione file. Ciò è dovuto al fatto che le operazioni CRUD vengono trasferite sul canale TCP persistente aperto quando il client si connette o monta la prima volta nella condivisione file.

   Esempio di query del log delle regole dell'applicazione:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Passaggi successivi

- [Usare il firewall di Azure per controllare il traffico destinato a un endpoint privato](../private-link/inspect-traffic-with-azure-firewall.md)
