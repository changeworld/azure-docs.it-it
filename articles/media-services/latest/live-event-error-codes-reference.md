---
title: Codici di errore dell'evento live di servizi multimediali di Azure
description: Questo articolo elenca i codici di errore dell'evento Live.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 09859a953b0127733cbf1b0876c1d2ffa6082096
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279477"
---
# <a name="media-services-live-event-error-codes"></a>Codici di errore dell'evento live di servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nelle tabelle seguenti sono elencati i codici di errore dell' [evento Live](live-event-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Quando si esegue la sottoscrizione agli eventi di [griglia di eventi](../../event-grid/index.yml) per un evento Live, è possibile che venga visualizzato uno degli errori seguenti dell'evento [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) .
> [!div class="mx-tdCol2BreakAll"]
>| Errore | Informazioni |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Descrizione | L'URL di inserimento non è corretto. |
>|Soluzione suggerita| APPID è un token GUID nell'URL di inserimento RTMP. Assicurarsi che corrisponda all'URL di inserimento dell'API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Descrizione |L'IP del codificatore non è presente nell'elenco Consenti indirizzi IP configurati |
>| Soluzione suggerita| Verificare che l'indirizzo IP del codificatore sia presente nell'elenco Consenti indirizzi IP. Usare uno strumento online come *whoismyip* o *CIDR Calculator* per impostare il valore corretto.  Verificare che il codificatore possa raggiungere il server prima dell'evento Live effettivo. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Descrizione|Il codificatore RTMP non ha inviato il `setDataFrame` comando. |
>| Soluzione suggerita|La maggior parte dei codificatori commerciali invia i metadati del flusso. Per un codificatore che effettua il push di un inserimento a bitrate singolo, questo potrebbe non essere un problema. Live è in grado di calcolare la velocità in bit in ingresso quando i metadati del flusso risultano mancanti.  Per l'inserimento a bitrate multipli per un canale PassThru o uno scenario con doppio push, è possibile provare ad accodare la stringa di query con ' videodatarate ' è audiodatarate ' nell'URL di inserimento. Il valore approssimativo potrebbe funzionare. L'unità è in kbit. Ad esempio, `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Descrizione|Il codec specificato non è supportato.|
>| Soluzione suggerita| Live ha ricevuto un codec non supportato. Ad esempio, un inserimento RTMP, Live ha ricevuto un codec video non AVC.  Controllare il set di impostazioni del codificatore. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Descrizione |Le informazioni sulla descrizione dei supporti non sono state ricevute prima del recapito dei dati multimediali effettivi. |
>| Soluzione suggerita|Il Live non riceve la descrizione del flusso (intestazione o tag FLV) dal codificatore. Si tratta di una violazione del protocollo. Contattare il fornitore del codificatore. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Descrizione|Il numero di qualità per il tipo audio o video supera il limite massimo consentito. |
>| Soluzione suggerita|Quando la modalità evento Live è codifica live, il codificatore deve eseguire il push di una singola velocità in bit di video e audio.  Si noti che è consentito un push ridondante dalla stessa velocità in bit. Controllare il set di impostazioni del codificatore o l'output per assicurarsi che restituisca un flusso a bitrate singolo. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Descrizione|La velocità in bit totale in ingresso in un evento Live o un servizio canale ha superato il limite massimo consentito. |
>| Soluzione suggerita|Il codificatore ha superato la velocità in bit massima in ingresso. Questo limite aggrega tutti i dati in ingresso dal codificatore che lo contribuisce. Controllare il set di impostazioni del codificatore o di output per ridurre il bitrate. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Descrizione|Il timestamp per FLVTag video o audio non è valido dal codificatore RTMP. |
>| Soluzione suggerita|Deprecato. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Descrizione|I flussi inseriti dal codificatore in ingresso con frequenza dei fotogrammi superano il numero massimo consentito di 30 fps per la codifica di eventi/canali live. |
>| Soluzione suggerita|Controllare il set di impostazioni del codificatore per abbassare la frequenza del frame a 36 FPS. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Descrizione|I flussi inseriti dal codificatore in ingresso hanno superato le risoluzioni consentite seguenti: 1920X1088 per la codifica di eventi/canali live e 4096 x 2160 per gli eventi/canali live pass-through. |
>| Soluzione suggerita|Controllare il set di impostazioni del codificatore per abbassare la risoluzione video in modo che non superi il limite. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Descrizione|L'evento Live ha ricevuto una grande quantità di dati audio contemporaneamente o una grande quantità di dati video senza fotogrammi chiave. Il codificatore è stato disconnesso per offrire la possibilità di riprovare con i dati corretti. |
>| Soluzione suggerita|Verificare che il codificatore invii un fotogramma chiave per ogni intervallo di fotogrammi chiave (GOP).  Abilitare le impostazioni come "velocità in bit costante (CBR)" o "Allinea fotogrammi chiave". In alcuni casi, la reimpostazione del codificatore contribuisce può essere utile. Se non è utile, contattare il fornitore del codificatore. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

È possibile che venga visualizzato uno degli errori seguenti dell'evento [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) .

> [!div class="mx-tdCol2BreakAll"]
>| Errore | Informazioni |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Descrizione|La sessione RTMP è scaduta dopo un periodo di inattività per il limite di tempo consentito. |
>|Soluzione suggerita|Questo problema si verifica in genere quando un codificatore smette di ricevere il feed di input in modo che la sessione diventi inattiva perché non sono presenti dati da inviare. Controllare se lo stato del codificatore o del feed di input è integro. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Descrizione| Il timestamp per il FLVTag video o audio non è valido dal codificatore RTMP. |
>| Soluzione suggerita| Deprecato. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Descrizione|Il codificatore invia i dati troppo velocemente. |
>| Soluzione suggerita|Questo errore si verifica quando il codificatore estrae un ampio set di frammenti in un breve periodo di tempo.  Questo può verificarsi in teoria quando il codificatore non è in grado di effettuare il push dei dati a causa di un problema di rete e il genera dati quando la rete è disponibile. Individuare il motivo del log del codificatore o del registro di sistema. |
>|**Codici di errore sconosciuti** |
>| Descrizione| Questi codici di errore possono variare da errori di memoria a voci duplicate nella mappa di hash. Questo problema può verificarsi quando il codificatore invia un set di frammenti di grandi dimensioni in un breve periodo di tempo.  Questa situazione può verificarsi anche quando il codificatore non è riuscito a eseguire il push dei dati a causa di un problema di rete e quindi invia tutti i frammenti ritardati contemporaneamente quando la rete diventa disponibile. |
>|Soluzione suggerita| Controllare i log del codificatore.|

## <a name="other-error-codes"></a>Altri codici di errore

> [!div class="mx-tdCol2BreakAll"]
>| Errore | Informazioni |Evento rifiutato/disconnesso|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Sì|
>| Descrizione|Si tratta di un errore generale. ||
>|Soluzione suggerita| Nessuna.||
>|**MPI_SYSTEM_MAINTENANCE** ||Sì|
>| Descrizione|Il codificatore è stato interrotto a causa dell'aggiornamento del servizio o della manutenzione del sistema. ||
>|Soluzione suggerita|Verificare che il codificatore consenta la connessione automatica. Consente al codificatore di riconnettersi all'endpoint evento Live ridondante che non è in manutenzione. ||
>|**MPE_BAD_URL_SYNTAX** ||Sì|
>| Descrizione|L'URL di inserimento non è formattato correttamente. ||
>|Soluzione suggerita|Verificare che l'URL di inserimento sia formattato correttamente. Per RTMP, dovrebbe essere `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Sì|
>| Descrizione|Il codificatore ha disconnesso la sessione.  ||
>|Soluzione suggerita|Questo errore non è stato. Disconnessione avviata dal codificatore, inclusa la disconnessione normale. Se si tratta di una disconnessione imprevista, controllare i log del codificatore. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||No|
>| Descrizione|La velocità dei dati in ingresso non corrisponde alla velocità in bit prevista. ||
>|Soluzione suggerita|Si tratta di un avviso che si verifica quando la velocità dei dati in ingresso è troppo lenta o veloce. Controllare il log del codificatore o il registro di sistema.||
>|**MPE_INGEST_DISCONTINUITY** ||No|
>| Descrizione| Sono presenti discontinuty nei dati in arrivo.||
>|Soluzione suggerita| Si tratta di un avviso che indica che il codificatore Elimina i dati a causa di un problema di rete o di una risorsa di sistema. Controllare il log del codificatore o il registro di sistema. Monitorare anche la risorsa di sistema (CPU, memoria o rete). Se la CPU di sistema è troppo elevata, provare a ridurre la velocità in bit o usare l'opzione del codificatore di H/W dalla scheda di grafica del sistema.||

## <a name="see-also"></a>Vedi anche

[Codici di errore dell'endpoint di streaming (Origin)](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: flusso live con servizi multimediali](stream-live-tutorial-with-api.md)
