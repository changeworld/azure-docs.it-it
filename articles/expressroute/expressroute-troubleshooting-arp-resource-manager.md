---
title: 'Azure ExpressRoute: tabelle ARP-risoluzione dei problemi'
description: Questa pagina fornisce istruzioni per ottenere le tabelle ARP (Address Resolution Protocol) per un circuito ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561580"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Recupero di tabelle ARP nel modello di distribuzione Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell - Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell-classico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Questo articolo illustra i passaggi per apprendere le tabelle ARP per il circuito ExpressRoute.

> [!IMPORTANT]
> Questo documento è progettato per aiutare l'utente a rilevare e risolvere i problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Se non si riesce a risolvere il problema tramite la procedura descritta di seguito, è necessario aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (Address Resolution Protocol) e tabelle ARP
ARP (Address Resolution Protocol) è un protocollo di livello 2 definito in [RFC 826](https://tools.ietf.org/html/rfc826). Il protocollo ARP viene usato per mappare l'indirizzo Ethernet (indirizzo MAC) con un indirizzo IP.

La tabella ARP fornisce le informazioni seguenti per le interfacce primarie e secondarie per ogni tipo di peering:

1. Mapping dell'indirizzo IP dell'interfaccia del router locale all'indirizzo MAC
2. Mapping dell'indirizzo IP dell'interfaccia del router di ExpressRoute all'indirizzo MAC
3. Età del mapping

Le tabelle ARP consentono di convalidare la configurazione di livello 2 e risoluzione dei problemi di connettività di base di livello 2. 

Tabella ARP di esempio: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


La sezione seguente fornisce informazioni su come visualizzare le tabelle ARP visualizzate tramite i router perimetrali di ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Prerequisiti per l'apprendimento delle tabelle ARP
Prima di procedere, verificare che le informazioni seguenti siano vere:

* Un circuito ExpressRoute valido configurato con almeno un peering. Il circuito deve essere completamente configurato dal provider di connettività. È necessario che l'utente o il provider di connettività abbia configurato almeno il peering privato di Azure, pubblico di Azure o Microsoft su questo circuito.
* Intervalli di indirizzi IP usati per configurare i peering. Esaminare gli esempi di assegnazione di indirizzi IP nella [pagina requisiti](expressroute-routing.md) per il routing di ExpressRoute per capire come viene eseguito il mapping degli indirizzi IP alle interfacce. È possibile ottenere informazioni sulla configurazione del peering consultando la [pagina sulla configurazione del peering di ExpressRoute](expressroute-howto-routing-arm.md).
* Informazioni dal team di rete/provider di connettività sugli indirizzi MAC delle interfacce usate con questi indirizzi IP.
* È necessario disporre del modulo PowerShell più recente per Azure (versione 1.50 o successiva).

> [!NOTE]
> Se il provider di servizi fornisce il livello 3 e le tabelle ARP sono vuote nel portale o nell'output riportato di seguito, aggiornare la configurazione del circuito usando il pulsante Aggiorna del portale. Questa operazione applicherà la configurazione di routing corretta nel circuito. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Ottenere tabelle ARP per il circuito ExpressRoute
Questa sezione fornisce istruzioni su come visualizzare le tabelle ARP per il peering tramite PowerShell. Prima di procedere all'elaborazione, l'utente o il provider di connettività devono aver configurato il peering. Ogni circuito ha due percorsi (primario e secondario). È possibile controllare la tabella ARP di ogni percorso in modo indipendente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelle ARP per il peering privato di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering privato di Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Di seguito è illustrato un esempio di output per uno dei percorsi

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabelle ARP per il peering pubblico di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering pubblico di Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Di seguito è illustrato un esempio di output per uno dei percorsi

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabelle ARP per il peering di Microsoft
Il cmdlet seguente fornisce le tabelle ARP per il peering di Microsoft

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Di seguito è illustrato un esempio di output per uno dei percorsi

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Come usare queste informazioni
La tabella ARP di un peer può essere usata per determinare la connettività e la configurazione di livello 2 valide. Questa sezione offre una panoramica dell'aspetto delle tabelle ARP in scenari diversi.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabella ARP quando un circuito è in stato operativo (stato previsto)
* La tabella ARP avrà una voce per il lato locale con un indirizzo IP e un indirizzo MAC validi. Lo stesso può essere visualizzato per il lato Microsoft. 
* L'ultimo ottetto dell'indirizzo IP locale sarà sempre un numero dispari.
* L'ultimo ottetto dell'indirizzo IP Microsoft sarà sempre un numero pari.
* Lo stesso indirizzo MAC verrà visualizzato sul lato Microsoft per tutti e tre i peering (primario/secondario). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabella ARP quando il lato locale/provider di connettività presenta problemi
Se si verifica un problema con il provider locale o di connettività, nella tabella ARP viene visualizzato uno dei due elementi seguenti. Si noterà che l'indirizzo MAC locale è incompleto o è possibile visualizzare solo la voce Microsoft nella tabella ARP.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
oppure
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Aprire una richiesta di supporto al provider di connettività per il debug di questi problemi. Se la tabella ARP non dispone di indirizzi IP delle interfacce associate agli indirizzi MAC, esaminare le informazioni seguenti:
> 
> 1. Se il primo indirizzo IP delle /30 subnet assegnate per il collegamento tra il MSEE-PR e il MSEE viene usato nell'interfaccia di MSEE-PR. Azure usa sempre il secondo indirizzo IP per MSEE.
> 2. Verificare se i tag VLAN del cliente (C-Tag) e del servizio (S-Tag) corrispondono nella coppia MSEE-PR e MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabella ARP quando il lato Microsoft presenta problemi
* Se si verificano problemi sul lato Microsoft, non verrà visualizzata una tabella ARP per un peering. 
* Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Specificare che si è riscontrato un problema di connettività di livello 2. 

## <a name="next-steps"></a>Passaggi successivi
* Convalidare le configurazioni di livello 3 per il circuito ExpressRoute.
  * Ottenere il riepilogo della route per determinare lo stato delle sessioni BGP.
  * Ottenere la tabella di route per determinare quali prefissi sono annunciati tra ExpressRoute.
* Convalidare il trasferimento dei dati riesaminando i byte in/out.
* Se si verificano ancora problemi, aprire un ticket di supporto con il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

