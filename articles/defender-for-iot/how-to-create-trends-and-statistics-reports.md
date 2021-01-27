---
title: Generare report sulle tendenze e sulle statistiche
description: Ottenere informazioni sull'attività, le statistiche e le tendenze della rete usando Defender per le tendenze e i widget delle statistiche.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811665"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendenze dei sensori e report statistici

## <a name="about-sensor-trends-and-statistics-reports"></a>Informazioni sulle tendenze dei sensori e sui report statistici

È possibile creare grafici di widget e grafici a torta per ottenere informazioni sulle tendenze e le statistiche di rete. I widget possono essere raggruppati in dashboard definiti dall'utente.

> [!NOTE]
> Gli analisti di amministrazione e sicurezza possono creare report sulle tendenze e sulle statistiche.

Il dashboard è costituito da widget che descrivono graficamente i tipi di informazioni seguenti:

- Traffico per porta
- Larghezza di banda del canale
- Larghezza di banda totale
- Connessione TCP attiva
- Dispositivi:
  - Nuovi dispositivi
  - Dispositivi occupati
  - Dispositivi per fornitore
  - Dispositivi per sistema operativo
  - Dispositivi disconnessi
- Riduzione della connettività per ore
- Avvisi per eventi imprevisti per tipo
- Accesso alla tabella di database
- Widget di dissezione del protocollo
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

## <a name="see-also"></a>Vedi anche

[Creazione di report](how-to-create-risk-assessment-reports.md) 
 di valutazione del rischio [Query](how-to-create-data-mining-queries.md) 
 data mining sensori [Segnalazione di vettori di attacco](how-to-create-attack-vector-reports.md)