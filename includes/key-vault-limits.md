---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: d934d40cad5f4eec929cfd273b6e30ea291e48d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010958"
---
Azure Key Vault servizio supporta due tipi di risorse: insiemi di credenziali e HSM gestiti. Nelle due sezioni seguenti vengono descritti rispettivamente i limiti del servizio per ciascuno di essi.

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

#### <a name="azure-private-link-integration"></a>Integrazione del collegamento privato di Azure

> [!NOTE]
> Il numero di insiemi di credenziali delle chiavi con endpoint privati abilitati per ogni sottoscrizione è un limite regolabile. Il limite indicato di seguito è il limite predefinito. Se si vuole richiedere un aumento del limite per il servizio, inviare un messaggio di posta elettronica a akv-privatelink@microsoft.com. Queste richieste vengono approvate caso per caso.

| Risorsa | Limite |
| -------- | -----:|
| Endpoint privati per insieme di credenziali delle chiavi | 64 |
| Insiemi di credenziali delle chiavi con endpoint privati per sottoscrizione | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Tipo di risorsa: HSM gestito (anteprima)

Questa sezione descrive i limiti del servizio per il tipo di risorsa `managed HSM` .

#### <a name="object-limits"></a>Limiti di oggetto

|Elemento|Limiti|
|----|------:|
Numero di istanze HSM per sottoscrizione per area|1 (durante l'anteprima)
Numero di chiavi per ogni pool di moduli di protezione hardware|5000
Numero di versioni per chiave|100
Numero di definizioni di ruolo personalizzate per HSM|50
Numero di assegnazioni di ruolo nell'ambito del modulo di protezione hardware|50
Numero di assegnazioni di ruolo in ogni singolo ambito della chiave|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limiti delle transazioni per le operazioni amministrative (numero di operazioni al secondo per ogni istanza del modulo di protezione hardware)
|Operazione |Numero di operazioni al secondo|
|----|------:|
Tutte le operazioni RBAC<br/>(include tutte le operazioni CRUD per le definizioni di ruolo e le assegnazioni di ruolo)|5
Backup/ripristino HSM completo<br/>(è supportata solo un'operazione di backup o ripristino simultanea per ogni istanza HSM)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limiti delle transazioni per le operazioni di crittografia (numero di operazioni al secondo per ogni istanza del modulo di protezione hardware)

- Ogni istanza del modulo di protezione hardware gestito costituisce 3 partizioni HSM con bilanciamento del carico. I limiti di velocità effettiva sono una funzione della capacità hardware sottostante allocata per ogni partizione. Le tabelle seguenti mostrano la velocità effettiva massima con almeno una partizione disponibile. Se tutte e tre le partizioni sono disponibili, è possibile che la velocità effettiva sia massima di 3 volte superiore.
- I limiti di velocità effettiva indicati presuppongono che venga usata una sola chiave per ottenere la velocità effettiva massima. Se, ad esempio, viene usata una singola chiave RSA-2048, la velocità effettiva massima sarà di 1100 operazioni di firma. Se si utilizzano 1100 chiavi diverse con 1 transazione al secondo, non sarà possibile ottenere la stessa velocità effettiva.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni con chiavi RSA (numero di operazioni al secondo per ogni istanza del modulo di protezione hardware)

|Operazione|2048 bit|3072 bit|4096 bit|
|--|--:|--:|--:|
Crea chiave|1| 1| 1
Elimina chiave (eliminazione temporanea)|10|10|10 
Elimina chiave|10|10|10 
Chiave di backup|10|10|10 
Chiave di ripristino|10|10|10 
Ottenere informazioni sulla chiave|1100|1100|1100
Encrypt|10000|10000|6000
Decrypt|1100|360|160
Wrap|10000|10000|6000
Unwrap|1100|360|160
Sign|1100|360|160
Verifica|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni chiave EC (numero di operazioni al secondo per ogni istanza del modulo di protezione hardware)

Questa tabella descrive il numero di operazioni al secondo per ogni tipo di curva.

|Operazione|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Crea chiave| 1| 1| 1| 1
Elimina chiave (eliminazione temporanea)|10|10|10|10
Elimina chiave|10|10|10|10
Chiave di backup|10|10|10|10
Chiave di ripristino|10|10|10|10
Ottenere informazioni sulla chiave|1100|1100|1100|1100
Sign|260|260|165|56
Verifica|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operazioni di chiave AES (numero di operazioni al secondo per ogni istanza del modulo di protezione hardware)
- Le operazioni di crittografia e decrittografia presuppongono una dimensione del pacchetto 4KB.
- I limiti di velocità effettiva per crittografia/decrittografia si applicano agli algoritmi AES-CBC e AES-GCM.
- I limiti di velocità effettiva per l'incapsulamento/Unwrap si applicano all'algoritmo AES-KW.

|Operazione|128 bit|192 bit|256 bit|
|--|--:|--:|--:|
Crea chiave|1| 1| 1
Elimina chiave (eliminazione temporanea)|10|10|10
Elimina chiave|10|10|10
Chiave di backup|10|10|10
Chiave di ripristino|10|10|10
Ottenere informazioni sulla chiave|1100|1100|1100
Encrypt|8000|8000 |8000 
Decrypt|8000|8000|8000
Wrap|9000|9000|9000
Unwrap|9000|9000|9000

