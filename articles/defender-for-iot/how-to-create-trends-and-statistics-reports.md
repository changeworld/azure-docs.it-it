---
title: Generare report sulle tendenze e sulle statistiche
description: Ottenere informazioni sull'attività, le statistiche e le tendenze della rete usando Defender per le tendenze e i widget delle statistiche.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522224"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendenze dei sensori e report statistici

## <a name="about-sensor-trends-and-statistics-reports"></a>Informazioni sulle tendenze dei sensori e sui report statistici

È possibile creare grafici di widget e grafici a torta per ottenere informazioni sulle tendenze e le statistiche di rete. I widget possono essere raggruppati in dashboard definiti dall'utente.

> [!NOTE]
> Gli analisti di amministrazione e sicurezza possono creare report sulle tendenze e sulle statistiche.

Il dashboard è costituito da widget che descrivono graficamente i tipi di informazioni seguenti:

- Traffico per porta
- Traffico principale per porta
- Larghezza di banda del canale
- Larghezza di banda totale
- Connessione TCP attiva
- Larghezza di banda superiore per VLAN
- Dispositivi:
  - Nuovi dispositivi
  - Dispositivi occupati
  - Dispositivi per fornitore
  - Dispositivi per sistema operativo
  - Numero di dispositivi per VLAN
  - Dispositivi disconnessi
- Connettività riduzioni per ore
- Avvisi per eventi imprevisti per tipo
- Accesso alla tabella di database
- Widget di dissezione del protocollo
- DELTAV
  - Distribuzione delle operazioni Roc DeltaV
  - Eventi Roc DeltaV per nome
  - DeltaV eventi in base all'ora
- AMS
  - Traffico AMS per porta server
  - Traffico AMS per comando
- Ethernet e indirizzo IP:
  - Traffico di indirizzi IP e Ethernet per servizio CIP
  - Traffico di indirizzi IP e Ethernet per classe CIP
  - Ethernet e traffico indirizzi IP per comando
- OPC
  - Operazioni di gestione principali OPC
  - Operazioni di I/O principali OPC
- S7 Siemens:
  - Traffico S7 per funzione di controllo
  - Traffico S7 per sottofunzione
- VLAN
  - Numero di dispositivi per VLAN
  - Larghezza di banda superiore per VLAN
- 60870-5-104
  - Traffico IEC-60870 per ASDU
- BACNET
  - Servizi BACnet
- DNP3
  - DNP3 il traffico per funzione
- SRTP
  - Traffico SRTP per codice del servizio
  - Errori di SRTP per giorno
- SuiteLink:
  - SuiteLink primi tag con query
  - Comportamento dei tag numerici SuiteLink
- Traffico IEC-60870 per ASDU
- DNP3 il traffico per funzione
- Traffico MMS per servizio
- Traffico Modbus per funzione
- Traffico OPC-UA per servizio

> [!NOTE]
>  Il tempo nei widget è impostato in base al tempo del sensore.

## <a name="create-reports"></a>Creare report

Per visualizzare i dashboard e i widget:

Selezionare **tendenze & statistiche** dal menu laterale.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot di un'indagine.":::

Per impostazione predefinita, i risultati vengono visualizzati per i rilevamenti negli ultimi 7 giorni. È possibile usare gli strumenti di filtro per modificare questo intervallo. Ad esempio, una ricerca di testo gratuita.

## <a name="next-steps"></a>Passaggi successivi

[Creazione di report](how-to-create-risk-assessment-reports.md) 
 di valutazione del rischio [Query](how-to-create-data-mining-queries.md) 
 data mining sensori [Segnalazione di vettori di attacco](how-to-create-attack-vector-reports.md)