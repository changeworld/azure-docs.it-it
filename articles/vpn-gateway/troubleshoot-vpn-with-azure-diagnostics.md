---
title: Risolvere i problemi del gateway VPN di Azure tramite log di diagnostica
description: Risolvere i problemi del gateway VPN di Azure tramite log di diagnostica
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 4e65dcd448a2ab2fad635cab12f41d858416becf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726070"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Risolvere i problemi del gateway VPN di Azure tramite log di diagnostica

Questo articolo consente di comprendere i diversi log disponibili per la diagnostica del gateway VPN e come usarli per risolvere efficacemente i problemi del gateway VPN.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

In Azure sono disponibili i log seguenti:

|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Contiene i log di diagnostica per eventi di configurazione del gateway, modifiche primarie ed eventi di manutenzione |
|**TunnelDiagnosticLog** | Contiene gli eventi di modifica dello stato del tunnel. Gli eventi di connessione/disconnessione del tunnel hanno un motivo riepilogativo per la modifica dello stato, se applicabile |
|**RouteDiagnosticLog** | Registra le modifiche alle route statiche e agli eventi BGP che si verificano nel gateway |
|**IKEDiagnosticLog** | Registra gli eventi e i messaggi di controllo IKE nel gateway |
|**P2SDiagnosticLog** | Registra gli eventi e i messaggi di controllo da punto a sito nel gateway |

Si noti che in queste tabelle sono disponibili numerose colonne. In questo articolo vengono presentate solo le più rilevanti per un utilizzo più semplice dei log.

## <a name="set-up-logging"></a><a name="setup"></a>Configurare la registrazione

Per informazioni su come configurare gli eventi del log di diagnostica dal gateway VPN di Azure usando Azure Log Analytics, vedere [configurare gli avvisi sugli eventi del log di diagnostica dal gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Le modifiche alla configurazione vengono controllate nella tabella **GatewayDiagnosticLog** . Si noti che potrebbero essere necessari alcuni minuti prima che le modifiche eseguite vengano riflesse nei log.

Ecco una query di esempio come riferimento.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Questa query su **GatewayDiagnosticLog** mostrerà più colonne.

|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**TimeGenerated** | timestamp di ogni evento, nel fuso orario UTC|
|**NomeOperazione** |evento che si è verificato. Può essere *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*|
|**Messaggio** | Dettagli sull'operazione eseguita ed elenca i risultati con esito positivo o negativo.|

Nell'esempio seguente viene illustrata l'attività registrata quando è stata applicata una nuova configurazione:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Esempio di impostazione dell'operazione del gateway.":::


Si noti che un SetGatewayConfiguration verrà registrato ogni volta che viene modificata una configurazione sia in un gateway VPN che in un gateway di rete locale.
Il riferimento incrociato ai risultati della tabella **GatewayDiagnosticLog** con quelli della tabella **TunnelDiagnosticLog** può essere utile per determinare se un errore di connettività del tunnel è stato avviato allo stesso tempo in cui è stata modificata una configurazione o se è stata svolta una manutenzione. In caso affermativo, abbiamo un ottimo puntatore verso la possibile causa radice.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

La tabella **TunnelDiagnosticLog** è molto utile per esaminare gli Stati di connettività cronologici del tunnel.

Ecco una query di esempio come riferimento.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Questa query su **TunnelDiagnosticLog** mostrerà più colonne.


|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**TimeGenerated** | timestamp di ogni evento, nel fuso orario UTC|
|**NomeOperazione** | evento che si è verificato. Può essere *TunnelConnected* o *TunnelDisconnected*.|
| **Istanza \_ s** | istanza del ruolo Gateway che ha attivato l'evento. Può essere GatewayTenantWorker \_ in \_ 0 o GatewayTenantWorker \_ in 1, \_ ovvero i nomi delle due istanze del gateway.|
| **Risorsa** | indica il nome del gateway VPN. |
| **ResourceGroup** | indica il gruppo di risorse in cui si trova il gateway.|


Output di esempio:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Esempio di evento tunnel connected.":::


**TunnelDiagnosticLog** è molto utile per la risoluzione degli eventi precedenti sulle DISconnessioni VPN impreviste. La sua natura leggera offre la possibilità di analizzare intervalli di tempo di grandi dimensioni in diversi giorni con scarso sforzo.
Solo dopo aver identificato il timestamp di una disconnessione, è possibile passare all'analisi più dettagliata della tabella **IKEdiagnosticLog** per esaminare più a fondo la motivazione delle disconnessioni, che saranno correlate a IPSec.


Alcuni suggerimenti per la risoluzione dei problemi:
- Se viene visualizzato un evento di disconnessione in un'istanza del gateway, seguito da un evento di connessione nell'istanza del gateway **diversa** in pochi secondi, si sta esaminando un failover del gateway. Si tratta in genere di un comportamento previsto a causa della manutenzione in un'istanza del gateway. Per ulteriori informazioni su questo comportamento, vedere [informazioni sulla ridondanza del gateway VPN di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Lo stesso comportamento viene osservato se si esegue intenzionalmente una reimpostazione del gateway sul lato Azure, causando il riavvio dell'istanza del gateway attiva. Per ulteriori informazioni su questo comportamento, vedere [reimpostare un gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic)
- Se viene visualizzato un evento di disconnessione in un'istanza del gateway, seguito da un evento di connessione nella **stessa** istanza del gateway in pochi secondi, è possibile che si verifichi un problema di rete che causa un timeout di DPD o una disconnessione erroneamente inviata dal dispositivo locale.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

La tabella **RouteDiagnosticLog** traccia l'attività per le route modificate in modo statico o le route ricevute tramite BGP.

Ecco una query di esempio come riferimento.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Questa query su **RouteDiagnosticLog** mostrerà più colonne.

|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**TimeGenerated** | timestamp di ogni evento, nel fuso orario UTC|
|**NomeOperazione** | evento che si è verificato. Può essere *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*|
| **Messaggio** | informazioni dettagliate sull'operazione che si sta verificando.|

L'output visualizzerà informazioni utili sui peer BGP connessi/disconnessi e sulle route scambiate.

Esempio:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Esempi di route BGP.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

La tabella **IKEDiagnosticLog** offre la registrazione dettagliata del debug per IKE/IPSec. Questa operazione è molto utile per la risoluzione dei problemi relativi alle disconnessioni o alla mancata connessione di scenari VPN.

Ecco una query di esempio come riferimento.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Questa query su **IKEDiagnosticLog** mostrerà più colonne.


|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**TimeGenerated** | timestamp di ogni evento, nel fuso orario UTC|
| **RemoteIP** | Indirizzo IP del dispositivo VPN locale. Negli scenari reali, è utile filtrare in base all'indirizzo IP del dispositivo locale pertinente ne saranno presenti più di una |
|**LocalIP** | Indirizzo IP del gateway VPN per cui si sta risolvendo la risoluzione dei problemi. Negli scenari reali, è utile filtrare in base all'indirizzo IP del gateway VPN pertinente che ne contiene più di una nella sottoscrizione |
|**Event** | contiene un messaggio di diagnostica utile per la risoluzione dei problemi. In genere iniziano con una parola chiave e fanno riferimento alle azioni eseguite dall' **\[ INVIO \]** del gateway di Azure indica che un evento causato da un pacchetto IPSec inviato dal gateway di Azure **\[ ricevuto \]** indica che un evento in conseguenza di un pacchetto ricevuto dal **\[ locale \]** del dispositivo locale indica un'azione eseguita localmente dal gateway di Azure |


Si noti che le colonne RemoteIP, LocalIP e Event non sono presenti nell'elenco di colonne originale nel database AzureDiagnostics, ma vengono aggiunte alla query analizzando l'output della colonna "message" per semplificare l'analisi.

Suggerimenti per la risoluzione dei problemi:

- Per identificare l'inizio di una negoziazione IPSec, è necessario trovare il messaggio iniziale di SA \_ init. Tale messaggio può essere inviato da entrambi i lati del tunnel. Chiunque invii il primo pacchetto è denominato "Initiator" nella terminologia IPsec, mentre l'altro lato diventa il "risponditore". Il primo messaggio di SA \_ init è sempre quello in cui rCookie = 0.

- Se non è possibile stabilire il tunnel IPsec, Azure continuerà a ritentare ogni pochi secondi. Per questo motivo la risoluzione dei problemi relativi alla VPN è molto utile in IKEdiagnosticLog, in quanto non è necessario attendere un periodo di tempo specifico per riprodurre il problema. Inoltre, l'errore in teoria sarà sempre lo stesso ogni volta che si tenta di eseguire lo zoom avanti di una negoziazione di "campione" non riuscita in qualsiasi momento.

- Il SA \_ init contiene i parametri IPSec che il peer vuole usare per questa negoziazione IPsec. Documento ufficiale   
I [parametri IPSec/IKE predefiniti](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) elencano i parametri IPSec supportati dal gateway di Azure con le impostazioni predefinite


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

L'ultima tabella disponibile per la diagnostica VPN è **P2SDiagnosticLog**. In questo modo viene tracciata l'attività per da punto a sito.

Ecco una query di esempio come riferimento.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Questa query su **P2SDiagnosticLog** mostrerà più colonne.

|***Nome** _ | _ *_Descrizione_** |
|---        | ---               |
|**TimeGenerated** | timestamp di ogni evento, nel fuso orario UTC|
|**NomeOperazione** | evento che si è verificato. Sarà *P2SLogEvent*|
| **Messaggio** | informazioni dettagliate sull'operazione che si sta verificando.|

L'output mostrerà tutte le impostazioni da punto a sito che il gateway ha applicato, oltre ai criteri IPsec sul posto.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Esempio di log P2S.":::

Inoltre, ogni volta che un client si connette tramite IKEv2 o OpenVPN da punto a sito, la tabella registrerà l'attività dei pacchetti, le conversazioni EAP/RADIUS e i risultati di esito positivo/negativo per utente.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Esempio di log EAP.":::

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi per i log delle risorse del tunnel, vedere [configurare gli avvisi nei log delle risorse del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

