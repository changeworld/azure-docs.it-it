---
title: Codici di errore client per l'aggiornamento del dispositivo per l'hub Azure Microsoft Docs
description: Questo documento fornisce una tabella di codici di errore client per diversi componenti di aggiornamento del dispositivo.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200346"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Aggiornamento del dispositivo per i codici di errore dell'hub

Questo documento fornisce una tabella di codici di errore per diversi componenti di aggiornamento del dispositivo. Questo deve essere utilizzato come riferimento per gli utenti che desiderano provare ad analizzare i propri codici di errore per diagnosticare e risolvere i problemi.

Sono disponibili due componenti principali sul lato client che possono generare codici di errore: l'agente di aggiornamento del dispositivo e l'agente di ottimizzazione recapito.

## <a name="device-update-agent"></a>Agente di aggiornamento dispositivo

### <a name="resultcode-and-extendedresultcode"></a>ResultCode e ExtendedResultCode

L'aggiornamento del dispositivo per i report dell'interfaccia PnP principali dell'hub `ResultCode` e `ExtendedResultCode` , che può essere usato per diagnosticare gli errori. [Altre](device-update-plug-and-play.md) informazioni sull'interfaccia PnP di aggiornamento del dispositivo.

#### <a name="resultcode"></a>ResultCode

`ResultCode` è un codice di stato generale e segue la convenzione del codice di stato http.
[Altre](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) informazioni sui codici di stato http.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` è un numero intero con informazioni sull'errore codificate.

È probabile che venga visualizzato `ExtendedResultCode` come intero con segno nell'interfaccia PnP. Per decodificare `ExtendedResultCode` , convertire l'intero con segno in esadecimale senza segno. Vengono usati solo i primi 4 byte di `ExtendedResultCode` e sono nel formato in `F` `FFFFFFF` cui il primo bocconcino è il **codice della struttura** e il resto dei bit è il codice di **errore**.

**Codici di struttura**

| Codice di struttura     | Descrizione  |
|-------------------|--------------|
| D                 | Errore generato da DO SDK|
| E                 | Il codice di errore è errno |


Ad esempio:

`ExtendedResultCode` è `-536870781`

La rappresentazione esadecimale senza segno di `-536870781` è `FFFFFFFF E0000083` .

| Ignora    | Codice di struttura  | Codice di errore   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` in Hex è `131` in decimale, che rappresenta il valore errno per `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Agente di ottimizzazione recapito
La tabella seguente elenca i codici di errore relativi al componente di ottimizzazione recapito del client di aggiornamento del dispositivo. Il componente DO è responsabile del download del contenuto di aggiornamento nel dispositivo Internet delle cose.

Per ottenere il codice di errore è possibile esaminare le eccezioni generate in risposta a una chiamata API. Tutti i codici di errore possono essere identificati dal prefisso 0x80D0.

| Codice di errore  | Errore stringa                       | Tipo                 | Descrizione |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n/d                  | Ottimizzazione recapito non è in grado di fornire il servizio |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Scarica processo         | Il download di un file non è stato rilevato entro il periodo definito |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Scarica processo         | SetProperty () o GetProperty () chiamato con un ID di proprietà sconosciuto |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Scarica processo         | Impossibile chiamare SetProperty () su una proprietà di sola lettura |
| 0x80D02013L | DO_E_INVALID_STATE                 | Scarica processo         | L'azione richiesta non è consentita nello stato del processo corrente. È possibile che il processo sia stato annullato o completato. Si trova ora in uno stato di sola lettura. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Scarica processo         | Non è possibile avviare un download perché non è stato specificato alcun sink di download (file locale o interfaccia di flusso) |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | Interfaccia IDODownload| Il download è stato avviato senza fornire un URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Condizioni transitorie | Il download è stato sospeso a causa di una perdita di connettività di rete |

## <a name="device-update-content-service"></a>Servizio contenuto aggiornamento dispositivo
La tabella seguente elenca i codici di errore relativi al componente del servizio contenuto del servizio di aggiornamento del dispositivo. Il componente del servizio contenuto è responsabile della gestione dell'importazione del contenuto degli aggiornamenti.

| Codice di errore                    | Errore stringa                                                               | Passaggi successivi                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Esiste già un aggiornamento con la stessa identità.                              | Assicurarsi di importare un aggiornamento che non è già stato importato in questa istanza dell'aggiornamento del dispositivo per l'hub Internet. |
| "DuplicateContentImport"      | Contenuto identico importato simultaneamente più volte.                  | Uguale a UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Errore durante l'elaborazione del manifesto di importazione.                                          | Per informazioni corrette sulla formattazione del manifesto di importazione, vedere [importare i concetti](./import-concepts.md) e importare la documentazione dell' [aggiornamento](./import-update.md) . |
| "CannotDownload"              | Impossibile scaricare il manifesto di importazione.                                           | Verificare che l'URL per il file manifesto di importazione sia ancora valido. |
| "CannotParse"                 | Impossibile analizzare il manifesto di importazione.                                              | Verificare l'accuratezza del manifesto di importazione rispetto allo schema definito nella documentazione sull' [importazione dell'aggiornamento](./import-update.md) . |
| "UnsupportedVersion"          | La versione dello schema del manifesto di importazione non è supportata.                           | Assicurarsi che il manifesto di importazione usi lo schema più recente definito nella documentazione sull' [importazione dell'aggiornamento](./import-update.md) . |
| "UpdateLimitExceeded"         | Errore durante l'importazione dell'aggiornamento a causa del superamento del limite.                              | È stato raggiunto un limite per il numero di provider, nomi o versioni diversi consentiti nell'istanza dell'aggiornamento del dispositivo per l'hub Internet. Eliminare alcuni aggiornamenti dall'istanza e riprovare. |
| "UpdateProvider"              | Impossibile importare un nuovo provider di aggiornamenti.                                       | È stato raggiunto un limite per il numero di __provider__ diversi consentiti nell'istanza dell'aggiornamento del dispositivo per l'hub Internet. Eliminare alcuni aggiornamenti dall'istanza e riprovare. |
| "UpdateName"                  | Impossibile importare un nuovo nome di aggiornamento per il provider specificato.                | È stato raggiunto un limite per il numero di __nomi__ diversi consentiti in un provider nell'istanza dell'aggiornamento del dispositivo per l'hub Internet. Eliminare alcuni aggiornamenti dall'istanza e riprovare. |
| UpdateVersion               | Impossibile importare una nuova versione di aggiornamento per il provider e il nome specificati.    | È stato raggiunto un limite per il numero di __versioni__ diverse consentite con un provider e un nome nell'istanza dell'aggiornamento del dispositivo per l'hub Internet. Eliminare alcuni aggiornamenti con tale nome dall'istanza e riprovare. |
| "UpdateProviderCompatibility" | Impossibile importare un provider di aggiornamento aggiuntivo con la compatibilità specificata. | Quando si definiscono le proprietà di compatibilità del produttore del dispositivo e del modello di dispositivo in un manifesto di importazione, tenere presente che l'aggiornamento del dispositivo per l'hub delle cose supporta una combinazione di nome e provider singolo per un produttore o un modello specifico. Ciò significa che se si tenta di utilizzare le stesse proprietà di compatibilità del produttore o del modello con più di una combinazione di provider/nome, vengono visualizzati questi errori. Per risolvere questo problema, assicurarsi che tutti gli aggiornamenti per un determinato dispositivo (come definito dal produttore/modello) usino lo stesso provider e lo stesso nome. Sebbene non sia obbligatorio, è consigliabile fare in modo che il provider corrisponda al produttore e al nome identico a quello del modello, solo per semplicità. |
| "UpdateNameCompatibility"     | Impossibile importare un nome di aggiornamento aggiuntivo con la compatibilità specificata.     | Uguale a UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Impossibile importare una versione di aggiornamento aggiuntiva con la compatibilità specificata.  | Uguale a UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Errore durante l'elaborazione del file di origine.                                              |                                    |
| "ContentFileCannotDownload"   | Impossibile scaricare il file di origine.                                               | Verificare che l'URL per i file di aggiornamento sia ancora valido. |

**[Passaggio successivo: risoluzione dei problemi relativi all'aggiornamento del dispositivo](.\troubleshoot-device-update.md)**
