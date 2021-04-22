---
title: Avviare la traduzione
titleSuffix: Azure Cognitive Services
description: Avviare una richiesta di traduzione di documenti con il servizio traduzione documenti.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 820b5f39192fffa0ec54b44c6016965599d85a8c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863696"
---
# <a name="start-translation"></a>Avviare la traduzione

Usare questa API per avviare una richiesta di traduzione in blocco (batch) con il servizio traduzione documenti. Ogni richiesta può contenere più documenti e deve contenere un contenitore di origine e di destinazione per ogni documento.

Il filtro di prefisso e suffisso (se specificato) viene usato per filtrare le cartelle. Il prefisso viene applicato al sottopercorso dopo il nome del contenitore.

I glossari/la memoria di traduzione possono essere inclusi nella richiesta e vengono applicati dal servizio quando il documento viene tradotto.

Se il glossario non è valido o non è raggiungibile durante la traduzione, viene indicato un errore nello stato del documento. Se nella destinazione esiste già un file con lo stesso nome, verrà sovrascritto. TargetUrl per ogni lingua di destinazione deve essere univoco.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio Traduzione documenti richiedono un endpoint di dominio personalizzato.**
> * Non è possibile usare l'endpoint presente nella pagina delle chiavi e _dell'endpoint_ della risorsa portale di Azure, né l'endpoint del traduttore globale, per effettuare richieste HTTP alla `api.cognitive.microsofttranslator.com` traduzione dei documenti.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="request-body-batch-submission-request"></a>Corpo della richiesta: richiesta di invio batch

|Nome|Type|Descrizione|
|--- |--- |--- |
|input|BatchRequest[]|BatchRequest elencato di seguito. Elenco di input di documenti o cartelle contenenti documenti. Tipi di supporti: "application/json", "text/json", "application/*+json".|

### <a name="inputs"></a>Input

Definizione per la richiesta di conversione batch di input.

|Nome|Type|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|source|SourceInput[]|Vero|inputs.source elencato di seguito. Origine dei documenti di input.|
|storageType|StorageInputType[]|Vero|inputs.storageType elencato di seguito. Tipo di archiviazione della stringa di origine dei documenti di input.|
|destinazioni|TargetInput[]|Vero|inputs.target elencato di seguito. Posizione della destinazione per l'output.|

**inputs.source**

Origine dei documenti di input.

|Nome|Type|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falso|DocumentFilter[] elencato di seguito.|
|filter.prefix|string|Falso|Stringa di prefisso con distinzione tra maiuscole e minuscole per filtrare i documenti nel percorso di origine per la traduzione. Ad esempio, quando si usa un URI del BLOB di archiviazione di Azure, usare il prefisso per limitare le sottocartelle per la traduzione.|
|filter.suffix|string|Falso|Stringa di suffisso con distinzione tra maiuscole e minuscole per filtrare i documenti nel percorso di origine per la traduzione. Questa opzione viene spesso utilizzata per le estensioni di file.|
|Linguaggio|string|Falso|Codice lingua Se non ne viene specificato nessuno, verrà eseguito il rilevamento automatico sul documento.|
|sourceUrl|string|True|Percorso della cartella o del contenitore o del singolo file con i documenti.|
|storageSource|StorageSource|Falso|StorageSource elencato di seguito.|
|storageSource.AzureBlob|string|Falso||

**inputs.storageType**

Tipo di archiviazione della stringa di origine dei documenti di input.

|Nome|Type|
|--- |--- |
|file|string|
|folder|string|

**inputs.target**

Destinazione per i documenti tradotti completati.

|Nome|Type|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|category|string|Falso|Categoria/sistema personalizzato per la richiesta di traduzione.|
|Glossari|Glossario[]|Falso|Glossario elencato di seguito. Elenco di glossario.|
|glossaries.format|string|Falso|Formattare.|
|glossaries.glossaryUrl|string|True (se si usano glossari)|Posizione del glossario. Si userà l'estensione di file per estrarre la formattazione se non viene specificato il parametro format. Se la coppia di lingue di traduzione non è presente nel glossario, non verrà applicata.|
|glossaries.storageSource|StorageSource|Falso|StorageSource elencato in precedenza.|
|targetUrl|string|True|Percorso della cartella/contenitore con i documenti.|
|Linguaggio|string|True|Codice lingua di destinazione di due lettere. Vedere [l'elenco dei codici di lingua](../../language-support.md).|
|storageSource|StorageSource []|Falso|StorageSource [] elencato in precedenza.|
|version|string|Falso|Versione.|

## <a name="example-request"></a>Richiesta di esempio

Di seguito sono riportati esempi di richieste batch.

**Traduzione di tutti i documenti in un contenitore**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Traduzione di tutti i documenti in un contenitore applicando glossari**

Assicurarsi di aver creato l'URL del glossario & token di firma di accesso condiviso per il BLOB/documento specifico (non per il contenitore)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Traduzione di una cartella specifica in un contenitore**

Assicurarsi di aver specificato il nome della cartella (con distinzione tra maiuscole e minuscole) come prefisso nel filtro, anche se il token di firma di accesso condiviso è ancora per il contenitore.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Traduzione di un documento specifico in un contenitore**

* Assicurarsi di aver specificato "storageType": "File"
* Assicurarsi di aver creato l'URL di & token di firma di accesso condiviso per il BLOB/documento specifico (non per il contenitore)
* Assicurarsi di aver specificato il nome file di destinazione come parte dell'URL di destinazione, anche se il token di firma di accesso condiviso è ancora per il contenitore.
* La richiesta di esempio seguente mostra un singolo documento tradotto in due lingue di destinazione

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta.

|Codice di stato|Descrizione|
|--- |--- |
|202|Accettato. La richiesta ha esito positivo e la richiesta batch viene creata dal servizio. L'intestazione Operation-Location un URL di stato con l'operazione ID.HeadersOperation-Location: stringa|
|400|Richiesta non valida. Richiesta non valida. Controllare i parametri di input.|
|401|Non autorizzato. Controllare le credenziali.|
|429|La frequenza delle richieste è troppo elevata.|
|500|Errore interno del server.|
|503|Il servizio non è attualmente disponibile.  Riprova più tardi.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="error-response"></a>Risposta di errore

|Nome|Type|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene un messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, conforme alle linee guida dell'API Servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, message e optional properties target, details(key value pair), inner error (questo può essere annidato).|
|Interno. Errorcode|string|Ottiene la stringa di errore del codice.|
|innerError.message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta con esito positivo

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

È possibile trovare l'ID processo nell'intestazione di risposta del metodo POST Operation-Location VALORE DELL'URL. L'ultimo parametro dell'URL è l'ID processo dell'operazione (la stringa che segue "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Risposta di errore di esempio

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Seguire la guida introduttiva per altre informazioni sull'uso della traduzione dei documenti e della libreria client.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione di documenti](../get-started-with-document-translation.md)
