---
title: Visualizzare e configurare la telemetria di protezione DDoS per la protezione DDoS di Azure standard
description: Informazioni su come visualizzare e configurare la telemetria di protezione DDoS per la protezione DDoS di Azure standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0a04c6c58f8bfa5370a6529b81a5a85090413a2a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107534"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Visualizzare e configurare i dati di telemetria della protezione DDoS

Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. I dati di telemetria avanzati vengono esposti tramite monitoraggio di Azure, incluse le metriche dettagliate durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata ulteriormente con [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hub eventi di Azure), OMS log Analytics e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare i dati di telemetria di protezione DDoS
> * Visualizzare i criteri di mitigazione della protezione DDoS
> * Convalidare e testare la telemetria di protezione DDoS

### <a name="metrics"></a>Metriche

> [!NOTE]
> Mentre vengono visualizzate più opzioni per l' **aggregazione** in portale di Azure, per ogni metrica sono supportati solo i tipi di aggregazione elencati nella tabella seguente. Ci scusiamo per questa confusione e stiamo lavorando per risolverlo.

Sono disponibili le [metriche](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) seguenti per la protezione DDoS di Azure standard. Queste metriche possono essere esportate anche tramite le impostazioni di diagnostica (vedere [visualizzare e configurare la registrazione diagnostica DDoS](diagnostic-logging.md)).


| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | Descrizione |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | DDoS byte in ingresso eliminati | Byte al secondo | Massimo | DDoS byte in ingresso eliminati| 
| BytesForwardedDDoS | DDoS byte in ingresso inoltrati | Byte al secondo | Massimo | DDoS byte in ingresso inoltrati |
| BytesInDDoS | DDoS byte in ingresso | Byte al secondo | Massimo | DDoS byte in ingresso |
| DDoSTriggerSYNPackets | Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS) | Conteggio al secondo | Massimo | Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS) |
| DDoSTriggerTCPPackets | Pacchetti TCP in ingresso per attivare la mitigazione DDoS | Conteggio al secondo | Massimo | Pacchetti TCP in ingresso per attivare la mitigazione DDoS |
| DDoSTriggerUDPPackets | Pacchetti UDP in ingresso per attivare la mitigazione DDoS | Conteggio al secondo | Massimo | Pacchetti UDP in ingresso per attivare la mitigazione DDoS |
| IfUnderDDoSAttack | Sotto attacco DDoS o no | Conteggio | Massimo | Sotto attacco DDoS o no |
| PacketsDroppedDDoS | DDoS pacchetti in ingresso eliminati | Conteggio al secondo | Massimo | DDoS pacchetti in ingresso eliminati |
| PacketsForwardedDDoS | DDoS pacchetti in ingresso inoltrati | Conteggio al secondo | Massimo | DDoS pacchetti in ingresso inoltrati |
| PacketsInDDoS | DDoS pacchetti in ingresso | Conteggio al secondo | Massimo | DDoS pacchetti in ingresso |
| TCPBytesDroppedDDoS | DDoS byte TCP in ingresso eliminati | Byte al secondo | Massimo | DDoS byte TCP in ingresso eliminati |
| TCPBytesForwardedDDoS | DDoS byte TCP in ingresso inoltrati | Byte al secondo | Massimo | DDoS byte TCP in ingresso inoltrati |
| TCPBytesInDDoS | DDoS byte TCP in ingresso | Byte al secondo | Massimo | DDoS byte TCP in ingresso |
| TCPPacketsDroppedDDoS | DDoS pacchetti TCP in ingresso eliminati | Conteggio al secondo | Massimo | DDoS pacchetti TCP in ingresso eliminati |
| TCPPacketsForwardedDDoS | DDoS pacchetti TCP in ingresso inoltrati | Conteggio al secondo | Massimo | DDoS pacchetti TCP in ingresso inoltrati |
| TCPPacketsInDDoS | DDoS pacchetti TCP in ingresso | Conteggio al secondo | Massimo | DDoS pacchetti TCP in ingresso |
| UDPBytesDroppedDDoS | DDoS byte UDP in ingresso eliminati | Byte al secondo | Massimo | DDoS byte UDP in ingresso eliminati |
| UDPBytesForwardedDDoS | DDoS byte UDP in ingresso inoltrati | Byte al secondo | Massimo | DDoS byte UDP in ingresso inoltrati |
| UDPBytesInDDoS | DDoS byte UDP in ingresso | Byte al secondo | Massimo | DDoS byte UDP in ingresso |
| UDPPacketsDroppedDDoS | DDoS pacchetti UDP in ingresso eliminati | Conteggio al secondo | Massimo | DDoS pacchetti UDP in ingresso eliminati |
| UDPPacketsForwardedDDoS | DDoS pacchetti UDP in ingresso inoltrati | Conteggio al secondo | Massimo | DDoS pacchetti UDP in ingresso inoltrati |
| UDPPacketsInDDoS | DDoS pacchetti UDP in ingresso | Conteggio al secondo | Massimo | DDoS pacchetti UDP in ingresso |

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md) e la protezione DDoS standard deve essere abilitata in una rete virtuale.
- La protezione DDoS esegue il monitoraggio degli indirizzi IP pubblici assegnati alle risorse all'interno di una rete virtuale. Se non si dispone di risorse con indirizzi IP pubblici nella rete virtuale, è innanzitutto necessario creare una risorsa con un indirizzo IP pubblico. È possibile monitorare l'indirizzo IP pubblico di tutte le risorse distribuite tramite Gestione risorse (non classico) elencate in [rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusi i servizi di bilanciamento del carico di Azure in cui le macchine virtuali di back-end si trovano nella rete virtuale), ad eccezione dei app Azure ambienti del servizio. Per continuare questa esercitazione, è possibile creare rapidamente una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-ddos-protection-telemetry"></a>Visualizzare i dati di telemetria di protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo quando un indirizzo IP pubblico è stato sottoposto a mitigazione. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al piano di protezione DDoS.
2. In **Monitoraggio** selezionare **Metrica**.
3. Selezionare **Ambito**. Selezionare la **sottoscrizione** che contiene l'indirizzo IP pubblico che si vuole registrare, selezionare **indirizzo IP pubblico** per **tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per cui si vogliono registrare le metriche e quindi selezionare **applica**.
4. Selezionare il tipo di **aggregazione** **Max**.

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Dropped tag name** (Nome tag eliminato), ad esempio **Inbound Packets Dropped DDoS** (Pacchetti in ingresso eliminati da DDoS): numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di protezione DDoS.
- **Forwarded tag name** (Nome tag inoltrato), ad esempio **Inbound Packets Forwarded DDoS** (Pacchetti in ingresso inoltrati da DDoS): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **No tag name** (Nessun nome di tag), ad esempio **Inbound Packets DDoS** (Pacchetti in ingresso elaborati da DDoS): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

## <a name="view-ddos-mitigation-policies"></a>Visualizzare i criteri di mitigazione della protezione DDoS

La protezione DDoS standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando i  **pacchetti TCP in ingresso per attivare la mitigazione DDoS** e i **pacchetti UDP in ingresso per attivare le metriche di mitigazione DDoS** con tipo di **aggregazione** come "Max", come illustrato nell'immagine seguente:

![Visualizzare i criteri di mitigazione](./media/manage-ddos-protection/view-mitigation-policies.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basato sull'apprendimento automatico di Azure. La mitigazione DDoS si attiva per l'indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

## <a name="validate-and-test"></a>Convalida e verifica

Per simulare un attacco DDoS per convalidare la telemetria di protezione DDoS, vedere [Validate DDoS Detection](test-through-simulations.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

- Configurare gli avvisi per le metriche di protezione DDoS
- Visualizzare i dati di telemetria di protezione DDoS
- Visualizzare i criteri di mitigazione della protezione DDoS
- Convalidare e testare la telemetria di protezione DDoS

Per informazioni su come configurare i report di mitigazione degli attacchi e i log di flusso, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Visualizzare e configurare la registrazione diagnostica DDoS](diagnostic-logging.md)