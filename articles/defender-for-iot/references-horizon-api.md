---
title: API Horizon
description: Questa guida descrive i metodi Horizon usati comunemente.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/5/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 39770fe7aa7b11cae03304fda8901e81e0f1877a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98208413"
---
# <a name="horizon-api"></a>API Horizon 

Questa guida descrive i metodi Horizon usati comunemente.

### <a name="getting-more-information"></a>Ottenere altre informazioni

Per ulteriori informazioni sull'utilizzo di Horizon e Defender per la piattaforma Internet, vedere le seguenti informazioni:

- Per l'SDK per l'ambiente di sviluppo open Horizon (ODE), contattare il difensore per l'it.
- Per informazioni sul supporto e sulla risoluzione dei problemi, contattare <support@cyberx-labs.com> .

- Per accedere alla guida per l'utente di Defender for Internet dalla console di Defender per le cose, selezionare :::image type="icon" source="media/references-horizon-api/profile.png"::: e quindi fare clic su **Scarica manuale dell'utente**.


## `horizon::protocol::BaseParser`

Abstract per tutti i plug-in. È costituito da due metodi:

- Per l'elaborazione dei filtri plug-in definiti sopra. In questo modo, Horizon sa come comunicare con il parser.
- Per l'elaborazione dei dati effettivi.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

La prima funzione chiamata per il plug-in crea un'istanza del parser per Horizon per riconoscerla e registrarla.

### <a name="parameters"></a>Parametri 

No.

### <a name="return-value"></a>Valore restituito

shared_ptr all'istanza del parser.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Questa funzione verrà chiamata per ogni plug-in registrato sopra. 

Nella maggior parte dei casi, questa operazione sarà vuota. Generare un'eccezione per Horizon per individuare un problema.

### <a name="parameters"></a>Parametri 

- Mappa contenente la struttura di dissect_as, come definito nella config.jsdi un altro plug-in che desidera eseguire la registrazione.

### <a name="return-value"></a>Valore restituito 

Matrice di uint64_t, ovvero la registrazione elaborata in un tipo di uint64_t. Ciò significa che nella mappa sarà presente un elenco di porte, i cui valori saranno i uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Funzione Main. In particolare, la logica del plug-in, ogni volta che un nuovo pacchetto raggiunge il parser. Questa funzione verrà chiamata, tutto ciò che riguarda l'elaborazione dei pacchetti deve essere eseguito qui.

### <a name="considerations"></a>Considerazioni

Il plug-in deve essere thread-safe, perché questa funzione può essere chiamata da thread diversi. Un approccio efficace consiste nel definire tutti gli elementi dello stack.

### <a name="parameters"></a>Parametri

- Unità di controllo dell'SDK responsabile dell'archiviazione dei dati e della creazione di oggetti correlati all'SDK, ad esempio ILayer e campi.
- Helper per la lettura dei dati del pacchetto non elaborato. È già impostato con l'ordine dei byte definito nell'config.js.

### <a name="return-value"></a>Valore restituito 

Risultato dell'elaborazione. Questo può essere *esito positivo*, non *valido* o *integrità*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Contrassegna l'elaborazione come un errore di igiene, ovvero il pacchetto non è riconosciuto dal protocollo corrente e l'orizzonte deve passarlo ad altro parser, se registrato sugli stessi filtri.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Costruttore

### <a name="parameters"></a>Parametri 

- Definisce il codice di errore usato dall'orizzonte per la registrazione, come definito nella config.jssu.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Risultato in formato non corretto, indica che il pacchetto è già stato riconosciuto come protocollo, ma la convalida è stata intervenuta in modo errato (i bit riservati sono on oppure manca un campo).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Costruttore

### <a name="parameters"></a>Parametri  

- Codice di errore, come definito in config.json.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Notifica a Horizon la corretta elaborazione. Al termine dell'operazione, il pacchetto è stato accettato, i dati appartengono a Microsoft e tutti i dati sono stati estratti.

## `horizon::protocol::SuccessResult()`

Costruttore. Creazione di un risultato di base riuscito. Ciò significa che non si conosce la direzione o altri metadati relativi al pacchetto.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Costruttore.

### <a name="parameters"></a>Parametri 

- Direzione del pacchetto, se identificato. I valori possono essere una *richiesta* o una *risposta*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Costruttore.

### <a name="parameters"></a>Parametri

- La direzione del pacchetto, se è stata identificata, può essere *richiesta*, *risposta*.
- Avvisi. Questi eventi non avranno esito negativo, ma l'orizzonte verrà notificato.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Costruttore.

### <a name="parameters"></a>Parametri 

-  Avvisi. Questi eventi non avranno esito negativo, ma l'orizzonte verrà notificato.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Converte un riferimento basato su stringa in un nome di campo, ad esempio function_code, in HorizonID.

### <a name="parameters"></a>Parametri 

- Stringa da convertire.

### <a name="return-value"></a>Valore restituito

- HorizonID creato dalla stringa.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Crea un nuovo livello, quindi Horizon saprà che il plug-in vuole archiviare alcuni dati. Si tratta dell'unità di archiviazione di base da usare.

### <a name="return-value"></a>Valore restituito

Un riferimento a un livello creato, in modo che sia possibile aggiungervi dati.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Ottiene l'oggetto di gestione campi, che è responsabile della creazione di campi su oggetti diversi, ad esempio in ILayer.

### <a name="return-value"></a>Valore restituito

Riferimento al gestore.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Crea un nuovo campo numerico di 64 bit sul livello con l'ID richiesto.

### <a name="parameters"></a>Parametri 

- Livello creato in precedenza.
- HorizonID creato dalla macro **HORIZON_FIELD** .
- Valore non elaborato che si desidera archiviare.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Crea un nuovo campo stringa di sul livello con l'ID richiesto. La memoria verrà spostata, quindi prestare attenzione. Non sarà possibile usare nuovamente questo valore.

### <a name="parameters"></a>Parametri  

- Livello creato in precedenza.
- HorizonID creato dalla macro **HORIZON_FIELD** .
- Valore non elaborato che si desidera archiviare.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Crea un nuovo campo di valore non elaborato (matrice di byte) di nel livello, con l'ID richiesto. La memoria verrà spostata, quindi prestare attenzione, non sarà possibile usare nuovamente questo valore.

### <a name="parameters"></a>Parametri

- Livello creato in precedenza.
- HorizonID creato dalla macro **HORIZON_FIELD** .
- Valore non elaborato che si desidera archiviare.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Crea un campo di valore di matrice (matrice) sul livello del tipo specificato con l'ID richiesto.

### <a name="parameters"></a>Parametri

- Livello creato in precedenza.
- HorizonID creato dalla macro **HORIZON_FIELD** .
- Tipo di valori che verranno archiviati all'interno della matrice.

### <a name="return-value"></a>Valore restituito

Riferimento a una matrice in cui è necessario aggiungere i valori.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Accoda un nuovo valore integer alla matrice creata in precedenza.

### <a name="parameters"></a>Parametri

- Matrice creata in precedenza.
- Valore non elaborato da archiviare nella matrice.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Accoda un nuovo valore stringa alla matrice creata in precedenza. La memoria verrà spostata, quindi prestare attenzione, non sarà possibile usare nuovamente questo valore.

### <a name="parameters"></a>Parametri

- Matrice creata in precedenza.
- Valore non elaborato da archiviare nella matrice.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Accoda un nuovo valore non elaborato alla matrice creata in precedenza. La memoria verrà spostata, quindi prestare attenzione, non sarà possibile usare nuovamente questo valore.

### <a name="parameters"></a>Parametri

- Matrice creata in precedenza.
- Valore non elaborato da archiviare nella matrice.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Verifica che il buffer contenga almeno X byte.

### <a name="parameters"></a>Parametri

Numero di byte che deve esistere.

### <a name="return-value"></a>Valore restituito

True se il buffer contiene almeno X byte. In caso contrario, è `False`.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Legge il valore Uint8 (1 byte) dal buffer in base all'ordine dei byte.

### <a name="return-value"></a>Valore restituito

Valore letto dal buffer.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Legge un valore UInt16 (2 byte), dal buffer, in base all'ordine dei byte.

### <a name="return-value"></a>Valore restituito

Valore letto dal buffer.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Legge il valore UInt32 (4 byte) dal buffer in base all'ordine dei byte.

### <a name="return-value"></a>Valore restituito

Valore letto dal buffer.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Legge il valore UInt64 (8 byte), dal buffer, in base all'ordine dei byte.

### <a name="return-value"></a>Valore restituito

Valore letto dal buffer.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Legge nella memoria preallocata, di una dimensione specificata, i dati vengono effettivamente copiati nell'area di memoria.

### <a name="parameters"></a>Parametri 

- Area di memoria in cui copiare i dati.
- Dimensioni dell'area di memoria. questo parametro definisce anche il numero di byte che verranno copiati.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Legge in una stringa dal buffer.

### <a name="parameters"></a>Parametri 

- Numero di byte da leggere.

### <a name="return-value"></a>Valore restituito

Riferimento all'area di memoria della stringa.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Indica il numero di byte rimanenti nel buffer.

### <a name="return-value"></a>Valore restituito

Dimensioni rimanenti del buffer.

## `void horizon::general::IDataBuffer::skip(size_t)`

Ignora X byte nel buffer.

### <a name="parameters"></a>Parametri

- Numero di byte da ignorare.
