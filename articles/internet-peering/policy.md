---
title: Criteri di peering Microsoft
titleSuffix: Azure
description: Criteri di peering Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592330"
---
# <a name="peering-policy"></a>Criteri di peering
Microsoft mantiene un criterio di peering selettivo progettato per garantire la migliore esperienza possibile per i clienti supportata dagli standard e dalle procedure consigliate del settore, scalabilità per la domanda futura e posizionamento strategico del peering. Di conseguenza, Microsoft si riserva il diritto di rendere le eccezioni ai criteri ritenute necessarie. I requisiti generali di Microsoft della rete sono illustrati nelle sezioni riportate di seguito. Applicabili sia al peering diretto che alle richieste di peering di Exchange. 

## <a name="technical-requirements"></a>Requisiti tecnici

* Una rete completamente ridondante con capacità sufficiente per lo scambio di traffico senza congestione.
* Il peer avrà un numero di sistema autonomo (ASN) instradabile pubblicamente.
* Sono supportati sia IPv4 che IPv6 e Microsoft prevede di stabilire sessioni di entrambi i tipi in ogni località di peering.
* MD5 non è supportato.
* **Dettagli ASN:**

    * Microsoft gestisce AS8075 insieme ai seguenti ASN: AS8068, AS8069, AS12076. Per un elenco completo di ASN con peering AS8075, fare riferimento a SET MICROSOFT.
    * Il peering di tutte le parti con Microsoft non accetta le route da AS12076 (Express Route) in qualsiasi circostanza e deve filtrare AS12076 in tutti i peer.

* **Criteri di routing:**
    * Il peer avrà almeno un instradabile pubblicamente/24.
    * Microsoft sovrascriverà i discriminatori a più uscite ricevuti (MED).
    * Microsoft preferisce ricevere i tag della community BGP dai peer per indicare l'origine della route.
    * Si consiglia ai peer di impostare un prefisso max di 2000 (IPv4) e 500 (IPv6) route nelle sessioni di peering con Microsoft.
    * A meno che non sia specificato in anticipo, i peer dovrebbero annunciare Route coerenti in tutte le posizioni in cui sono peer con Microsoft.
    * In generale, le sessioni di peering con AS8075 annuncieranno tutte le route AS-MICROSOFT. Microsoft può annunciare alcune specifiche internazionali.
    * Nessuna delle due parti stabilirà una route statica, una route dell'ultima risorsa o altrimenti invierà traffico all'altra parte per una route non annunciata tramite BGP.
    * Il peer è necessario per registrare le route in un database di Internet routing Registry (IRR) pubblico, allo scopo di filtrare, e manterrà le informazioni aggiornate.      
    * I peer sono conformi agli standard del settore MANRS per la sicurezza delle route.  A sua esclusiva discrezione, Microsoft può scegliere: 1. non stabilire il peering con le aziende che non hanno Route firmate e registrate. 2.) per rimuovere le route RPKI non valide; 3.) non accettare route da peer stabiliti che non sono registrati e firmati. 

## <a name="operational-requirements"></a>Requisiti operativi
* Un servizio di rete Operations Center (NOC) completamente personale, in grado di supportare la risoluzione di tutti i problemi tecnici e di prestazioni, le violazioni della sicurezza, gli attacchi Denial of Service o qualsiasi altro abuso originato all'interno del peer o dei clienti.
* Si prevede che i peer abbiano un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com) , incluso un messaggio di posta elettronica NOC 24x7 dal dominio aziendale e dal numero di telefono. Queste informazioni vengono usate per convalidare i dettagli del peer, ad esempio le informazioni relative ai NOC, le informazioni di contatto tecnico e la loro presenza presso le strutture di peering e così via. Gli account personali di Yahoo, Gmail e Hotmail non sono accettati.

## <a name="physical-connection-requirements"></a>Requisiti di connessione fisica
* Le località in cui è possibile connettersi con Microsoft per il peering diretto o il peering di Exchange sono elencate in [PeeringDB](https://www.peeringdb.com/net/694)

* **Peering di interscambio:**
    * I peer dovrebbero avere almeno una connessione da 10 GB a Exchange.
    * Si prevede che i peer aggiornino le porte quando il picco di utilizzo supera il 50%.
    * Microsoft incoraggia i peer a mantenere una connettività diversificata a Exchange per supportare scenari di failover.

* **Peering diretto:**
    * L'interconnessione deve essere basata su Fiber a modalità singola con l'ottica 100 Gbps.
    * Microsoft stabilirà il peering diretto solo con provider di servizi di rete o ISP.
    * Si prevede che i peer aggiornino le porte quando il picco di utilizzo supera il 50% e mantengono diverse capacità in ogni metro, in un'unica posizione o in diverse posizioni in una metropolitana.
    * Ogni peering diretto è costituito da due connessioni a due router Microsoft Edge dai router del peer che si trovano in Edge del peer. Microsoft richiede sessioni BGP doppie tra queste connessioni. Il peer può scegliere di non distribuire dispositivi ridondanti alla fine.


## <a name="traffic-requirements"></a>Requisiti di traffico

* Il peering sul peering di Exchange deve avere una lunghezza minima di 500 MB di traffico e inferiore a 2 GB. Per il traffico che supera i 2 GB è necessario stabilire il peering diretto.
* Microsoft richiede almeno 2 GB per il peering diretto. Ogni percorso di peering accettato reciprocamente deve supportare il failover che garantisce che il peering rimanga localizzato durante uno scenario di failover. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui passaggi per configurare il peering diretto con Microsoft, seguire la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md).
* Per informazioni sui passaggi per configurare il peering di Exchange con Microsoft, seguire la [procedura dettagliata del peering di Exchange](walkthrough-exchange-all.md).
