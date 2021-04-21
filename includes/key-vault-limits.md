---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753300"
---
Azure Key Vault servizio supporta due tipi di risorse: insiemi di credenziali e HMS gestiti. Le due sezioni seguenti descrivono rispettivamente i limiti del servizio per ognuna di esse.

### <a name="resource-type-vault"></a>Tipo di risorsa: insieme di credenziali

Questa sezione descrive i limiti del servizio per il tipo di risorsa `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transazioni chiave (numero massimo di transazioni consentite entro 10 secondi, per ogni insieme di credenziali e ogni area<sup>1</sup>):

|Tipo di chiave|Chiave del modulo di protezione hardware<br>Chiave CREATE|Chiave del modulo di protezione hardware<br>Tutte le altre transazioni|Chiave software<br>Chiave CREATE|Chiave software<br>Tutte le altre transazioni|
|:---|---:|---:|---:|---:|
|RSA a 2.048 bit|5|1\.000|10|2.000|
|RSA a 3.072 bit|5|250|10|500|
|RSA a 4.096 bit|5|125|10|250|
|ECC P-256|5|1\.000|10|2.000|
|ECC P-384|5|1\.000|10|2.000|
|ECC P-521|5|1\.000|10|2.000|
|ECC SECP256K1|5|1\.000|10|2.000|
||||||

> [!NOTE]
> Nella tabella precedente si è visto ce per chiavi software RSA a 2.048 bit sono consentite 2.000 transazioni GET ogni 10 secondi. Per le chiavi del modulo di protezione hardware RSA a 2.048 bit sono consentite 1.000 transazioni GET ogni 10 secondi.
>
> Le soglie di limitazione delle richieste sono ponderate e l'imposizione viene applicata alla somma. Ad esempio, come illustrato nella tabella precedente, quando si eseguono operazioni GET su chiavi del modulo di protezione hardware RSA, usare chiavi a 4.096 bit risulta otto volte più oneroso rispetto a usare chiavi a 2.048 bit, in quanto 1.000/125 = 8.
>
> In un intervallo specificato di 10 secondi un client Azure Key Vault può eseguire *una sola* delle operazioni seguenti prima che venga restituito un codice di stato HTTP di limitazione delle richieste `429`:
> - 2\.000 transazioni GET con chiave software RSA a 2.048 bit
> - 1\.000 transazioni GET con chiave del modulo di protezione hardware RSA a 2.048 bit
> - 125 transazioni GET con chiave del modulo di protezione hardware RSA a 4.096 bit
> - 124 transazioni GET con chiave del modulo di protezione hardware RSA a 4.096 bit e 8 transazioni GET con chiave del modulo di protezione hardware RSA a 2.048 bit

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segreti, chiavi di account di archiviazione gestiti e transazioni dell'insieme di credenziali:

| Tipo di transazioni | Numero massimo di transazioni consentite entro 10 secondi, per ogni archivio e ogni area<sup>1</sup> |
| --- | --- |
| Tutte le transazioni |2.000 |

Per informazioni su come gestire la limitazione delle richieste in caso di superamento dei limiti, vedere [Guida alla limitazione delle richieste per Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> Il limite a livello di sottoscrizione per tutti i tipi di transazione corrisponde a un valore pari a cinque volte quello del limite a livello di insieme di credenziali delle chiavi. Ad esempio, le transazioni diverse da HSM hanno un limite di 5.000 transazioni ogni 10 secondi per sottoscrizione.

#### <a name="backup-keys-secrets-certificates"></a>Chiavi di backup, segreti, certificati

Quando si esegue il backup di un oggetto dell'insieme di credenziali delle chiavi, come un segreto, una chiave o un certificato, l'operazione di backup scarica l'oggetto come BLOB crittografato. Questo BLOB non può essere decrittografato all'esterno di Azure. Per ottenere dati utilizzabili da questo BLOB, è necessario ripristinare il BLOB in un insieme di credenziali delle chiavi all'interno della stessa sottoscrizione di Azure e della stessa area geografica di Azure

| Tipo di transazioni | Numero massimo di versioni degli oggetti dell'insieme di credenziali delle chiavi consentite |
| --- | --- |
| Eseguire il backup di una singola chiave, segreto, certificatofiicate |500 |

> [!NOTE]
> Se si tenta di eseguire il backup di una chiave, di un segreto o di un oggetto certificato con un numero di versioni superiore al limite specificato, verrà generato un errore. Non è possibile eliminare le versioni precedenti di una chiave, un segreto o un certificato. 

#### <a name="azure-private-link-integration"></a>Integrazione del collegamento privato di Azure

> [!NOTE]
> Il numero di insiemi di credenziali delle chiavi con endpoint privati abilitati per ogni sottoscrizione è un limite regolabile. Il limite indicato di seguito è il limite predefinito. Se si vuole richiedere un aumento del limite per il servizio, creare una richiesta di supporto che verrà valutata caso per caso.

| Risorsa | Limite |
| -------- | -----:|
| Endpoint privati per insieme di credenziali delle chiavi | 64 |
| Insiemi di credenziali delle chiavi con endpoint privati per sottoscrizione | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Tipo di risorsa: HSM gestito (anteprima)

Questa sezione descrive i limiti del servizio per il tipo di risorsa `managed HSM` .

#### <a name="object-limits"></a>Limiti di oggetto

|Elemento|Limiti|
|----|------:|
Numero di istanze di HSM per sottoscrizione per area|1 (durante l'anteprima)
Numero di chiavi per pool HSM|5000
Numero di versioni per chiave|100
Numero di definizioni di ruolo personalizzate per modulo di protezione HSM|50
Numero di assegnazioni di ruolo nell'ambito HSM|50
Numero di assegnazioni di ruolo nell'ambito di ogni singola chiave|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limiti delle transazioni per le operazioni amministrative (numero di operazioni al secondo per ogni istanza di HSM)
|Operazione |Numero di operazioni al secondo|
|----|------:|
Tutte le operazioni di controllo degli accessi in base al ruolo<br/>(include tutte le operazioni CRUD per le definizioni di ruolo e le assegnazioni di ruolo)|5
Backup/ripristino HSM completo<br/>(è supportata una sola operazione di backup o ripristino simultanea per ogni istanza di HSM)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limiti delle transazioni per le operazioni di crittografia (numero di operazioni al secondo per ogni istanza di HSM)

- Ogni istanza di HSM gestito costituisce 3 partizioni HSM con carico bilanciato. I limiti di velocità effettiva sono una funzione della capacità hardware sottostante allocata per ogni partizione. Le tabelle seguenti mostrano la velocità effettiva massima con almeno una partizione disponibile. La velocità effettiva può essere fino a 3 volte superiore se sono disponibili tutte e 3 le partizioni.
- I limiti di velocità effettiva notati presuppongono che venga usata una singola chiave per ottenere la velocità effettiva massima. Ad esempio, se viene usata una singola chiave RSA-2048, la velocità effettiva massima sarà di 1100 operazioni di firma. Se si usano 1100 chiavi diverse con 1 transazione al secondo, non saranno in grado di ottenere la stessa velocità effettiva.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni con chiave RSA (numero di operazioni al secondo per ogni istanza di HSM)

|Operazione|2048 bit|3072 bit|4096 bit|
|--|--:|--:|--:|
Crea chiave|1| 1| 1
Elimina chiave (eliminazione soft)|10|10|10 
Chiave di ripulitura|10|10|10 
Chiave di backup|10|10|10 
Ripristina chiave|10|10|10 
Ottenere informazioni sulla chiave|1100|1100|1100
Encrypt|10000|10000|6000
Decrypt|1100|360|160
Wrap|10000|10000|6000
Unwrap|1100|360|160
Sign|1100|360|160
Verifica|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni chiave EC (numero di operazioni al secondo per ogni istanza HSM)

Questa tabella descrive il numero di operazioni al secondo per ogni tipo di curva.

|Operazione|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Crea chiave| 1| 1| 1| 1
Elimina chiave (eliminazione soft)|10|10|10|10
Chiave di ripulitura|10|10|10|10
Chiave di backup|10|10|10|10
Ripristina chiave|10|10|10|10
Ottenere informazioni sulla chiave|1100|1100|1100|1100
Sign|260|260|165|56
Verifica|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni chiave AES (numero di operazioni al secondo per ogni istanza HSM)
- Le operazioni di crittografia e decrittografia presuppongono una dimensione del pacchetto di 4 KB.
- I limiti di velocità effettiva per Crittografa/Decrittografa si applicano agli algoritmi AES-CBC e AES-GCM.
- I limiti di velocità effettiva per Wrap/Unwrap si applicano all'algoritmo AES-KW.

|Operazione|128 bit|192 bit|256 bit|
|--|--:|--:|--:|
Crea chiave|1| 1| 1
Elimina chiave (eliminazione soft)|10|10|10
Ripulire la chiave|10|10|10
Chiave di backup|10|10|10
Ripristina chiave|10|10|10
Ottenere informazioni sulla chiave|1100|1100|1100
Encrypt|8000|8000 |8000 
Decrypt|8000|8000|8000
Wrap|9000|9000|9000
Unwrap|9000|9000|9000

