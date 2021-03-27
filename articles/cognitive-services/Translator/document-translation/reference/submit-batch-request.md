---
title: Traduzione documento-Invia richiesta batch
titleSuffix: Azure Cognitive Services
description: Inviare una richiesta di traduzione documenti al servizio di traduzione documenti.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613028"
---
# <a name="document-translation-submit-batch-request"></a>Traduzione documento: Invia richiesta batch

Usare questa API per inviare una richiesta di conversione bulk (batch) al servizio di traduzione dei documenti. Ogni richiesta può contenere più documenti e deve contenere un contenitore di origine e di destinazione per ogni documento.

Il filtro prefisso e suffisso (se fornito) viene usato per filtrare le cartelle. Il prefisso viene applicato al sottopercorso dopo il nome del contenitore.

La memoria glossari/Translation può essere inclusa nella richiesta e viene applicata dal servizio quando il documento viene convertito.

Se il glossario non è valido o non è raggiungibile durante la conversione, viene indicato un errore nello stato del documento. Se nella destinazione esiste già un file con lo stesso nome, verrà sovrascritto. Il targetUrl per ogni lingua di destinazione deve essere univoco.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Informazioni su come trovare il [nome di dominio personalizzato](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Tutte le richieste API al servizio di traduzione documenti richiedono un endpoint del dominio personalizzato**.
> * Non è possibile usare l'endpoint trovato nelle chiavi di risorsa portale di Azure e nella pagina dell' _endpoint_ né nell'endpoint di conversione globale, `api.cognitive.microsofttranslator.com` per eseguire richieste HTTP alla conversione dei documenti.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta sono le seguenti:

|Intestazioni|Descrizione|
|--- |--- |
|Ocp-Apim-Subscription-Key|Intestazione della richiesta obbligatoria|

## <a name="request-body-batch-submission-request"></a>Corpo della richiesta: richiesta di invio batch

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|input|BatchRequest []|BatchRequest elencate di seguito. Elenco di documenti o cartelle contenenti documenti. Tipi di supporto: "application/json", "text/JSON", "application/* + json".|

### <a name="inputs"></a>Input

Definizione per la richiesta di conversione batch di input.

|Nome|Tipo|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|source|SourceInput[]|Vero|inputs. Source elencati di seguito. Origine dei documenti di input.|
|storageType|StorageInputType[]|Vero|inputs. storageType elencati di seguito. Tipo di archiviazione della stringa di origine dei documenti di input.|
|destinazioni|TargetInput[]|Vero|inputs. target elencati di seguito. Percorso della destinazione per l'output.|

**inputs. Source**

Origine dei documenti di input.

|Nome|Tipo|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falso|DocumentFilter [] riportato di seguito.|
|Filter. prefix|string|Falso|Stringa di prefisso con distinzione tra maiuscole e minuscole per filtrare i documenti nel percorso di origine per la conversione. Ad esempio, quando si usa un URI del BLOB di archiviazione di Azure, usare il prefisso per limitare le sottocartelle per la conversione.|
|Filter. suffisso|string|Falso|Stringa di suffisso con distinzione tra maiuscole e minuscole per filtrare i documenti nel percorso di origine per la conversione. Questa operazione viene usata più spesso per le estensioni di file.|
|Linguaggio|string|Falso|Codice lingua se non è specificato alcun valore, verrà eseguito il rilevamento automatico nel documento.|
|sourceUrl|string|True|Percorso della cartella, del contenitore o del singolo file con i documenti.|
|storageSource|StorageSource|Falso|StorageSource elencate di seguito.|
|storageSource. AzureBlob|string|Falso||

**inputs. storageType**

Tipo di archiviazione della stringa di origine dei documenti di input.

|Nome|Tipo|
|--- |--- |
|file|string|
|folder|string|

**inputs. target**

Destinazione per i documenti tradotti completati.

|Nome|Tipo|Obbligatoria|Descrizione|
|--- |--- |--- |--- |
|category|string|Falso|Categoria/sistema personalizzato per la richiesta di traduzione.|
|glossari|Glossario []|Falso|Glossario riportato di seguito. Elenco di glossario.|
|glossari. Format|string|Falso|Formattare.|
|glossari. glossaryUrl|string|True (se si usano i glossari)|Posizione del glossario. Si userà l'estensione di file per estrarre la formattazione se il parametro di formato non viene fornito. Se la coppia lingua di traduzione non è presente nel glossario, non verrà applicata.|
|glossari. storageSource|StorageSource|Falso|StorageSource sopra elencate.|
|targetUrl|string|True|Percorso della cartella o del contenitore con i documenti.|
|Linguaggio|string|True|Codice lingua di destinazione di due lettere. Vedere l' [elenco dei codici della lingua](../../language-support.md).|
|storageSource|StorageSource []|Falso|StorageSource [] riportato sopra.|
|version|string|Falso|Versione.|

## <a name="example-request"></a>Richiesta di esempio

Di seguito sono riportati alcuni esempi di richieste batch.

**Conversione di tutti i documenti in un contenitore**

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

**Conversione di tutti i documenti in un contenitore applicazione di glossari**

Assicurarsi di aver creato l'URL del glossario & token SAS per il BLOB/documento specifico (non per il contenitore)

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

**Conversione di una cartella specifica in un contenitore**

Assicurarsi di aver specificato il nome della cartella (con distinzione tra maiuscole e minuscole) come prefisso nel filtro, anche se il token SAS è ancora per il contenitore.

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

**Conversione di un documento specifico in un contenitore**

* Assicurarsi di aver specificato "storageType": "file"
* Assicurarsi di aver creato l'URL di origine & token di firma di accesso condiviso per il BLOB/documento specifico (non per il contenitore)
* Assicurarsi di aver specificato il nome del file di destinazione come parte dell'URL di destinazione, anche se il token di firma di accesso condiviso è ancora per il contenitore.
* La richiesta di esempio seguente mostra un singolo documento che viene convertito in due lingue di destinazione

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
|202|Accettato. La richiesta è stata completata e la richiesta batch è stata creata dal servizio. L'intestazione Operation-Location indicherà un URL di stato con l'operazione ID. HeadersOperation-location: String|
|400|Richiesta non valida. Richiesta non valida. Controllare i parametri di input.|
|401|Non autorizzato. Verificare le credenziali.|
|429|La frequenza delle richieste è troppo elevata.|
|500|Errore interno del server.|
|503|Il servizio non è attualmente disponibile.  Riprova più tardi.|
|Altri codici di stato|<ul><li>Numero eccessivo di richieste</li><li>Server temporaneo non disponibile</li></ul>|

## <a name="error-response"></a>Risposta di errore

|Nome|Tipo|Descrizione|
|--- |--- |--- |
|codice|string|Enumerazioni contenenti codici di errore di alto livello. Valori possibili:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorizzata</li></ul>|
|message|string|Ottiene il messaggio di errore di alto livello.|
|innerError|InnerErrorV2|Nuovo formato di errore interno, che è conforme alle linee guida dell'API servizi cognitivi. Contiene le proprietà obbligatorie ErrorCode, Message e optional Properties Target, Details (key value pair), Inner Error (può essere annidato).|
|interna. ErrorCode|string|Ottiene la stringa di errore del codice.|
|innerError. Message|string|Ottiene il messaggio di errore di alto livello.|

## <a name="examples"></a>Esempio

### <a name="example-successful-response"></a>Esempio di risposta riuscita

Le informazioni seguenti vengono restituite in una risposta con esito positivo.

È possibile trovare l'ID processo nell'intestazione della risposta del metodo POST Operation-Location valore dell'URL. L'ultimo parametro dell'URL è l'ID processo dell'operazione (la stringa che segue "/Operation/").

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

Per altre informazioni sull'uso della traduzione dei documenti e della libreria client, seguire la Guida introduttiva.

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](../get-started-with-document-translation.md)
