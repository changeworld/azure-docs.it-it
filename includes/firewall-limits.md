---
title: includere file
description: File di inclusione
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c4c36c0e099ed7474a5d27f6edcbd4b3ac435f4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030824"
---
| Risorsa | Limite |
| --- | --- |
| Velocità effettiva dei dati |30 Gbps<sup>1</sup> |
|Limiti delle regole|10.000 origini/destinazioni univoche nelle regole di rete|
|Numero massimo di regole DNAT|298 per un singolo indirizzo IP pubblico.<br>Eventuali indirizzi IP pubblici aggiuntivi contribuiscono al numero di porte SNAT disponibili, ma riducono il numero di regole DNAT disponibili. Ad esempio, due indirizzi IP pubblici consentono 297 regole DNAT. Se il protocollo di una regola è configurato sia per TCP che per UDP, viene conteggiato come due regole.|
|Dimensioni minime di AzureFirewallSubnet |/26|
|Intervallo di porte nelle regole di rete e delle applicazioni|1 - 65535|
|Indirizzi IP pubblici|Massimo 250. Nelle regole DNAT è possibile usare tutti gli indirizzi IP pubblici, che contribuiscono alle porte SNAT disponibili.|
|Indirizzi IP in gruppi IP|Massimo 100 gruppi IP per firewall.<br>Massimo 5000 singoli indirizzi IP o prefissi IP per ogni gruppo IP.
|Tabella di route|Per impostazione predefinita, AzureFirewallSubnet prevede una route 0.0.0.0/0 con il valore NextHopType impostato su **Internet**.<br><br>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato su **Internet** per mantenere connettività diretta a Internet. Per impostazione predefinita, Firewall di Azure non supporta il tunneling forzato a una rete locale.<br><br>Tuttavia, se la configurazione richiede il tunneling forzato a una rete locale, Microsoft effettuerà assistenza caso per caso. Contattare il supporto tecnico per poter esaminare il caso. Se il caso viene accettato, la sottoscrizione verrà inserita nell'elenco elementi consentiti e la connettività Internet del firewall verrà mantenuta.|
|Nomi di dominio completo nelle regole di rete|Per prestazioni ottimali non superare più di 1000 nomi di dominio completo in tutte le regole di rete per singolo firewall.|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto di Azure.
