---
title: Generare report sulle tendenze e sulle statistiche
description: Ottenere informazioni sull'attività, le statistiche e le tendenze della rete usando Defender per le tendenze e i widget delle statistiche.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706480"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendenze dei sensori e report statistici

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

Per impostazione predefinita, i risultati vengono visualizzati per i rilevamenti negli ultimi sette giorni. È possibile usare gli strumenti di filtro per modificare questo intervallo. Ad esempio, una ricerca di testo gratuita.

## <a name="create-a-dashboard"></a>Creare un dashboard

Creare un nuovo dashboard selezionando il menu a discesa **Dashboard** . È possibile creare e aggiungere tutti i widget a un dashboard.

È possibile creare dashboard personalizzati usando le opzioni seguenti:

- Aggiungere un widget al dashboard

- Eliminare un widget dal dashboard

- Modificare il filtro di un widget

- Ridimensionare un widget

- Modificare il percorso di un widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Modificare il percorso di un widget.":::

Per creare un dashboard personalizzato:

1. Selezionare **tendenze e statistiche** dal pannello di sinistra.

1. Selezionare il menu a discesa **Seleziona dashboard** e selezionare il pulsante **crea dashboard** .

1. Immettere un nome significativo per il nuovo dashboard e selezionare **Crea**.

1. Selezionare **Aggiungi widget** nella parte superiore sinistra della pagina.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selezionare il widget dall'archivio widget.":::

1. I widget di **sicurezza** e **operativi** sono disponibili nella parte superiore destra della finestra. Scegliere tra diverse categorie e protocolli. Viene visualizzata una breve descrizione con un grafico in miniatura con ogni widget. Utilizzare la barra di scorrimento per visualizzare tutti i widget disponibili.

1. Selezionare un widget usando il pulsante **fare clic per aggiungere** . Il widget verrà visualizzato immediatamente nel dashboard.

Per eliminare un dashboard:

1. Selezionare il nome del dashboard dal menu a discesa.

1. Selezionare l'icona **Elimina** e quindi fare clic su **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Selezionare l'icona Elimina per eliminare il dashboard.":::

Per modificare il nome di un dashboard:

1. Selezionare il nome del dashboard dal menu a discesa.

1. Selezionare l'icona **modifica** .
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Selezionare l'icona di modifica per modificare il nome del dashboard.":::

1. Immettere un nuovo nome per il dashboard e selezionare **Salva**.
 
Per impostare il dashboard predefinito:

1. Selezionare il nome del dashboard dal menu a discesa.

1. Selezionare l'icona a **stella** per selezionare il dashboard da impostare come dashboard predefinito.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Selezionare l'icona a stella per scegliere il dashboard predefinito."::: 

Per modificare il filtro dei dati in un widget:

1. Selezionare l'icona del **filtro** .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Selezionare l'icona del filtro per impostare i parametri per il widget.":::

1. Modificare i parametri richiesti.

1. Selezionare **OK**.

Per eliminare un widget:

- Selezionare l' :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: icona.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Selezionare la X per eliminare il widget.":::

## <a name="creating-widgets"></a>Creazione di widget 

L'archivio widget consente di selezionare i widget per categoria e protocollo. È possibile visualizzare i widget di **sicurezza** o **operativi** disponibili selezionando i widget.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selezionare il widget dall'archivio widget.":::

Ogni widget contiene informazioni specifiche sul traffico di sistema, statistiche di rete, statistiche del protocollo, dispositivo e informazioni sugli avvisi. Quando non sono presenti dati per un widget, viene visualizzato un messaggio.

È possibile rimuovere una sezione dalla torta, in un grafico a torta, per vedere più chiaramente l'importanza relativa delle sezioni rimanenti. Per eseguire questa operazione, selezionare il nome della sezione nella legenda nella parte inferiore della schermata.

Le sezioni seguenti presentano esempi di casi d'uso per alcuni widget.

### <a name="busy-devices-widget"></a>Widget dispositivi occupati

Questo widget elenca i primi cinque dispositivi più attivi. In modalità di **modifica** è possibile filtrare in base ai protocolli noti.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Visualizzazione del widget dispositivo occupato.":::

### <a name="total-bandwidth-widget"></a>Widget larghezza di banda totale

Questo widget tiene traccia della larghezza di banda in Mbps (megabit al secondo). La larghezza di banda è indicata sull'asse y, con la data visualizzata sull'asse x. La modalità di **modifica** consente di filtrare i risultati in base a client, server, porta server o subnet. Quando si passa il cursore sul grafico, viene visualizzata una descrizione comando.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Visualizzazione del widget della larghezza di banda totale.":::

### <a name="channels-bandwidth-widget"></a>Widget larghezza di banda canali

Questo widget mostra i primi cinque canali di traffico. È possibile filtrare in base all'indirizzo e impostare il numero di risultati presentati. Selezionare la freccia rivolta verso il basso per visualizzare altri canali.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Visualizzazione del widget della larghezza di banda dei canali.":::

### <a name="traffic-by-port-widget"></a>Traffico per widget porta

Questo widget Visualizza il traffico in base alla porta, indicato da un grafico a torta con ogni porta designata con un colore diverso. La quantità di traffico in ogni porta è proporzionale alle dimensioni della parte della torta.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Visualizzazione del traffico in base al widget di porta.":::

### <a name="new-devices-widget"></a>Widget nuovi dispositivi

In questo widget viene visualizzato il grafico a barre nuovi dispositivi, che indica il numero di nuovi dispositivi individuati in una data specifica.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Visualizzazione del widget nuovi dispositivi.":::

### <a name="protocol-dissection-widgets"></a>Widget di dissezione del protocollo

In questo widget viene visualizzato un grafico a torta che fornisce un'occhiata al traffico per protocollo, dissecato da codici di funzione e servizi. La dimensione di ogni sezione della torta è proporzionale alla quantità di traffico rispetto alle altre sezioni.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Visualizzazione del widget di dissezione del protocollo.":::

### <a name="active-tcp-connections-widget"></a>Widget connessioni TCP attive

In questo widget viene visualizzato un grafico che mostra il numero di connessioni TCP attive nel sistema.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Visualizzazione del widget connessioni TCP attive.":::

### <a name="incident-by-type-widget"></a>Widget evento imprevisto per tipo

In questo widget viene visualizzato un grafico a torta che mostra il numero di eventi imprevisti per tipo. Il numero di avvisi generati da ogni motore in un periodo di tempo predefinito.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Visualizzazione dell'evento imprevisto per tipo widget.":::

## <a name="devices-by-vendor-widget"></a>Widget dispositivi per fornitore

Questo widget visualizza un grafico a torta che mostra il numero di dispositivi per fornitore. Il numero di dispositivi per un fornitore specifico è proporzionale alle dimensioni della parte fornitore del dispositivo del disco rispetto ad altri fornitori di dispositivi.

## <a name="number-of-devices-per-vlan-widget"></a>Numero di dispositivi per ogni widget VLAN

Questo widget visualizza un grafico a torta che mostra il numero di dispositivi individuati per VLAN. La dimensione di ogni sezione della torta è proporzionale al numero di dispositivi individuati rispetto alle altre sezioni.

Ogni VLAN viene visualizzata con il tag VLAN assegnato dal sensore o dal nome aggiunto manualmente.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Visualizzazione del widget numero di dispositivi.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Larghezza di banda superiore per widget VLAN

Questo widget Visualizza l'utilizzo della larghezza di banda per VLAN. Per impostazione predefinita, il widget mostra cinque VLAN con l'utilizzo più elevato della larghezza di banda.

È possibile filtrare i dati in base al periodo presentato nel widget. Selezionare la freccia rivolta verso il basso per visualizzare altri risultati.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Visualizzazione della larghezza di banda superiore per il widget VLAN.":::

## <a name="system-report"></a>Report di sistema

Per scaricare il report di sistema: 

1. Selezionare **tendenze & statistiche** dal menu laterale.

1. Selezionare **report di sistema** nell'angolo superiore destro. Il report verrà scaricato automaticamente.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Selezionare il pulsante del rapporto di sistema per scaricare una copia del report di sistema.":::

Il report di sistema è un file PDF contenente tutti i dati nel sistema:

  - Dispositivi

  - Avvisi

  - Informazioni sui criteri di rete

## <a name="devices-in-a-system-report"></a>Dispositivi in un report di sistema 

Il report di sistema Visualizza un elenco di tutti i dispositivi e le relative informazioni. Ad esempio, digitare, nome e protocolli utilizzati. Il report di sistema Mostra anche un elenco di dispositivi per fornitore.

## <a name="alerts-in-system-report"></a>Avvisi nel report di sistema 

Il report di sistema Mostra un elenco di tutti gli avvisi con le relative informazioni, ad esempio la data e la gravità.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Visualizzazione degli avvisi nei report di sistema.":::

## <a name="network-information-in-system-report"></a>Informazioni di rete nel report di sistema 

Il report di sistema Mostra in dettaglio la linea di base di rete. Ad esempio, il codice della funzione DNP3 e le porte aperte per connessione.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Visualizzazione della funzione DNP3 dal report di sistema.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Visualizzazione del report sulla porta aperta per connessione.":::

## <a name="see-also"></a>Vedi anche

[Creazione di report](how-to-create-risk-assessment-reports.md) 
 di valutazione del rischio [Query](how-to-create-data-mining-queries.md) 
 data mining sensori [Segnalazione di vettori di attacco](how-to-create-attack-vector-reports.md)
