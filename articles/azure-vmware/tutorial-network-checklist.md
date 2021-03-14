---
title: 'Esercitazione: Elenco di controllo di pianificazione della rete'
description: Informazioni sui requisiti di rete per la connettività di rete e le porte di rete nella soluzione VMware di Azure.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 8cee5fa24aab8bd7fe6a9527f9c8e7cdff997511
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462066"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Elenco di controllo di pianificazione della rete per la soluzione Azure VMware 

La soluzione VMware di Azure offre un ambiente cloud privato VMware accessibile per utenti e applicazioni da ambienti o risorse locali e basati su Azure. La connettività viene fornita tramite servizi di rete come Azure ExpressRoute e le connessioni VPN. Per abilitare i servizi, sono necessari specifici intervalli di indirizzi di rete e porte del firewall. Questo articolo fornisce le informazioni necessarie per configurare la rete in modo che funzioni correttamente con la soluzione VMware di Azure.

In questa esercitazione si vedrà:

> [!div class="checklist"]
> * Considerazioni sulla rete virtuale e sul circuito ExpressRoute
> * Requisiti di routing e subnet
> * Porte di rete necessarie per comunicare con i servizi
> * Considerazioni su DHCP e DNS nella soluzione Azure VMware

## <a name="prerequisite"></a>Prerequisito
Assicurarsi che tutti i gateway, incluso il servizio del provider ExpressRoute, supportino il numero di sistema autonomo (ASN) a 4 byte. La soluzione VMware di Azure usa ASN pubblici a 4 byte per le route pubblicitarie.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Considerazioni sulla rete virtuale e sul circuito ExpressRoute
Quando si crea una connessione di rete virtuale nella sottoscrizione, il circuito ExpressRoute viene stabilito tramite peering, usa una chiave di autorizzazione e un ID peering richiesto nell'portale di Azure. Il peering è una connessione privata, uno-a-uno tra il cloud privato e la rete virtuale.

> [!NOTE] 
> Il circuito ExpressRoute non fa parte di una distribuzione di cloud privato. Il circuito ExpressRoute locale esula dall'ambito di questo documento. Se è necessaria la connettività locale al cloud privato, è possibile usare uno dei circuiti ExpressRoute esistenti o acquistarne uno nel portale di Azure.

Quando si distribuisce un cloud privato, si ricevono gli indirizzi IP per vCenter e NSX-T Manager. Per accedere a tali interfacce di gestione, è necessario creare più risorse nella rete virtuale della sottoscrizione. Nelle esercitazioni sono disponibili le procedure per creare le risorse e stabilire il [peering privato ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md).

La rete logica del cloud privato include NSX-T con il provisioning effettuato. Vengono forniti un gateway di livello 0 e uno di livello 1 con il provisioning effettuato. È possibile creare un segmento e collegarlo al gateway di livello 1 esistente o a uno nuovo definito. I componenti della rete logica NSX-T forniscono connettività da destra a sinistra tra carichi di lavoro e connettività dall'alto in basso a Internet e ai servizi di Azure.

## <a name="routing-and-subnet-considerations"></a>Considerazioni su routing e subnet
Il cloud privato della soluzione VMware di Azure è connesso alla rete virtuale di Azure usando una connessione di Azure ExpressRoute. Questa connessione ad ampia larghezza di banda e a bassa latenza consente di accedere ai servizi in esecuzione nella sottoscrizione di Azure dall'ambiente cloud privato. Il routing è basato su BGP (Border Gateway Protocol), ne viene eseguito automaticamente il provisioning ed è abilitato per impostazione predefinita per ogni distribuzione di cloud privato. 

I cloud privati della soluzione VMware di Azure richiedono almeno un `/22` blocco di indirizzi di rete CIDR per le subnet, illustrato di seguito. Questa rete integra le reti locali disponibili. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali. All'interno di questo blocco di indirizzi, le reti di gestione, provisioning e vMotion vengono automaticamente sottoposte a provisioning.

>[!NOTE]
>Gli intervalli consentiti per il blocco di indirizzi sono inclusi negli spazi indirizzi privati RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), ad eccezione di 172.17.0.0/16.

Esempio di blocco di indirizzi di rete `/22` CIDR: `10.10.0.0/22`

Le subnet:

| Utilizzo di rete             | Subnet | Esempio          |
| ------------------------- | ------ | ---------------- |
| Gestione del cloud privato  | `/26`  | `10.10.0.0/26`   |
| Migrazioni di HCX Mgmt       | `/26`  | `10.10.0.64/26`  |
| Copertura globale riservata     | `/26`  | `10.10.0.128/26` |
| ExpressRoute riservato     | `/27`  | `10.10.0.192/27` |
| Peering ExpressRoute      | `/27`  | `10.10.0.224/27` |
| Gestione di ESXi           | `/25`  | `10.10.1.0/25`   |
| Rete vMotion           | `/25`  | `10.10.1.128/25` |
| Rete di replica       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| Uplink HCX                | `/26`  | `10.10.3.0/26`   |
| Riservato                  | `/26`  | `10.10.3.64/26`  |
| Riservato                  | `/26`  | `10.10.3.128/26` |
| Riservato                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Porte di rete necessarie

| Source (Sorgente) | Destination | Protocollo | Porta | Descrizione  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Server DNS del cloud privato | Server DNS locale | UDP | 53 | Client DNS-inviare le richieste da PC vCenter per qualsiasi query DNS locale (vedere la sezione relativa a DNS di seguito) |  
| Server DNS locale   | Server DNS del cloud privato | UDP | 53 | Client DNS-inviare le richieste da servizi locali a server DNS del cloud privato (controllare la sezione DNS più avanti) |  
| Rete locale  | Server vCenter del cloud privato  | TCP (HTTP)  | 80 | server vCenter richiede la porta 80 per le connessioni HTTP dirette. La porta 80 reindirizza le richieste alla porta HTTPS 443. Questo reindirizzamento è utile se si usa `http://server` anziché `https://server` .  <br><br>WS-Management (richiede anche che la porta 443 sia aperta) <br><br>Se si usa un database Microsoft SQL personalizzato e non il database SQL Server 2008 in bundle sul server vCenter, la porta 80 viene usata dal Reporting Services SQL. Quando si installa server vCenter, il programma di installazione richiede di modificare la porta HTTP per il server vCenter. Modificare la porta HTTP server vCenter in un valore personalizzato per garantire una corretta installazione. Microsoft Internet Information Services (IIS) utilizza anche la porta 80. Vedere conflitto tra server vCenter e IIS per la porta 80. |  
| Rete di gestione del cloud privato | Active Directory locale  | TCP  | 389 | Questa porta deve essere aperta nel computer locale e in tutte le istanze remote di server vCenter. Questa porta è il numero di porta LDAP per i servizi directory per il gruppo di server vCenter. Il sistema di server vCenter deve essere associato alla porta 389, anche se non si aggiunge questa istanza server vCenter a un gruppo in modalità collegata. Se un altro servizio è in esecuzione su questa porta, potrebbe essere preferibile rimuoverlo o modificarne la porta su una porta diversa. È possibile eseguire il servizio LDAP su qualsiasi porta da 1025 a 65535.  Se questa istanza funge da Active Directory Microsoft Windows, modificare il numero di porta da 389 a una porta disponibile da 1025 a 65535. Questa porta è facoltativa, per la configurazione di AD locale come origine di identità nell'istanza di vCenter del cloud privato. |  
| Rete locale  | Server vCenter del cloud privato  | TCP (HTTPS)  | 443 | Questa porta consente di accedere a vCenter da una rete locale. La porta predefinita usata dal sistema di server vCenter per restare in ascolto delle connessioni dal client vSphere. Per consentire al sistema server vCenter di ricevere dati dal client vSphere, aprire la porta 443 nel firewall. Il sistema server vCenter usa inoltre la porta 443 per monitorare il trasferimento dei dati dai client SDK. Questa porta viene usata anche per i servizi seguenti: WS-Management (richiede anche che la porta 80 sia aperta). Accesso del client vSphere a vSphere Update Manager. Connessioni di client di gestione di rete di terze parti al server vCenter. Client di gestione di rete di terze parti che accedono agli host. |  
| Web browser  | Hybrid Cloud Manager  | TCP (HTTPS) | 9443 | Interfaccia di gestione di appliance virtuali per la configurazione di sistema di Hybrid Cloud Manager. |
| Rete di amministrazione  | Hybrid Cloud Manager | SSH | 22 | Accesso SSH come amministratore a Hybrid Cloud Manager. |
| HCM | Gateway cloud | TCP (HTTPS) | 8123 | Invia le istruzioni del servizio di replica basato su host al gateway cloud ibrido. |
| HCM | Gateway cloud | HTTP TCP (HTTPS) | 9443 | Invia le istruzioni di gestione al gateway cloud ibrido locale tramite l'API REST. |
| Gateway cloud | L2C | TCP (HTTPS) | 443 | Invia le istruzioni di gestione dal gateway cloud a L2C quando L2C usa lo stesso percorso del gateway cloud ibrido. |
| Gateway cloud | Host ESXi | TCP | 80.902 | Gestione e distribuzione di OVF. |
| Gateway cloud (locale)| Gateway cloud (remoto) | UDP | 4500 | Obbligatorio per IPSEC<br>   IKEv2 (Internet Key Exchange) per incapsulare i carichi di lavoro per il tunnel bidirezionale. È supportato anche lo standard NAT-T (Network Address Translation-Traversal). |
| Gateway cloud (locale) | Gateway cloud (remoto)  | UDP | 500 | Obbligatorio per IPSEC<br> ISAKMP (Internet Key Exchange) per il tunnel bidirezionale. |
| Rete vCenter locale | Rete di gestione del cloud privato | TCP | 8000 |  vMotion di macchine virtuali da vCenter locale a cloud privato vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Considerazioni su DHCP e sulla risoluzione DNS
Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e le assegnazioni di indirizzi IP. Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta. È possibile configurare una macchina virtuale per fornire questi servizi nell'ambiente cloud privato.  

Usare il servizio DHCP predefinito in NSX o usare un server DHCP locale nel cloud privato invece di instradare la trasmissione del traffico DHCP tramite la rete WAN all'ambiente locale.

Per altri dettagli, vedere l'articolo [fornire i servizi DHCP al segmento di rete NSX-T](deploy-azure-vmware-solution.md#optional-provide-dhcp-services-to-nsx-t-network-segment) .


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato le considerazioni e i requisiti per la distribuzione di un cloud privato della soluzione VMware di Azure. Una volta completata la configurazione corretta della rete, continuare con l'esercitazione successiva per creare il cloud privato della soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Creare un cloud privato della soluzione Azure VMware](tutorial-create-private-cloud.md)
