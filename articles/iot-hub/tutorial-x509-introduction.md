---
title: 'Esercitazione: Informazioni sui certificati di crittografia e X.509 per hub IoT di Azure | Microsoft Docs'
description: "Esercitazione: Informazioni sulla crittografia e sull'infrastruttura a chiave pubblica X.509 per hub IoT di Azure"
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378381"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Esercitazione: Informazioni sulla crittografia a chiave pubblica e sull'infrastruttura a chiave pubblica X.509

È possibile usare i certificati X.509 per autenticare i dispositivi in un hub IoT di Azure. Un certificato è un documento digitale che contiene la chiave pubblica del dispositivo e può essere usato per verificare che il dispositivo sia quello che dichiara di essere. I certificati X.509 e gli elenchi di revoche di certificati (CRL) sono documentati da [RFC 5280.](https://tools.ietf.org/html/rfc5280) I certificati sono solo una parte di un'infrastruttura a chiave pubblica (PKI) X.509. Per comprendere l'infrastruttura AKI X.509, è necessario comprendere algoritmi di crittografia, chiavi crittografiche, certificati e autorità di certificazione (CA):

* **Gli algoritmi** definiscono il modo in cui i dati originali del testo non crittografato vengono trasformati in testo crittografato e vengono nuovamente trasformati in testo non crittografato.
* **Le** chiavi sono stringhe di dati casuali o pseudocasuali usate come input per un algoritmo.
* **I** certificati sono documenti digitali che contengono la chiave pubblica di un'entità e consentono di determinare se il soggetto del certificato è chi o quale attesta di essere.
* **Le Autorità di** certificazione attestano l'autenticità dei soggetti del certificato.

È possibile acquistare un certificato da un'autorità di certificazione (CA). È anche possibile, per il test e lo sviluppo o se si lavora in un ambiente autonomo, creare una CA radice autofirmata. Se, ad esempio, si è proprietari di uno o più dispositivi e si sta testando l'autenticazione dell'hub IoT, è possibile autofirmare la CA radice e usarla per rilasciare i certificati del dispositivo. È anche possibile rilasciare certificati di dispositivo autofirmati. Questo argomento viene illustrato negli articoli successivi.

Prima di illustrare in modo più dettagliato i certificati X.509 e usarli per autenticare i dispositivi in un hub IoT, viene illustrata la crittografia su cui si basano i certificati.

## <a name="cryptography"></a>Crittografia

La crittografia viene usata per proteggere le informazioni e le comunicazioni. Questa operazione viene in genere eseguita usando tecniche crittografiche per eseguire lo scramble di testo non crittografato (testo normale) in testo crittografato (testo codificato) e di nuovo. Questo processo di scrambling è detto crittografia. Il processo inverso è denominato decrittografia. La crittografia riguarda gli obiettivi seguenti:

* **Riservatezza:** le informazioni possono essere comprese solo dai destinatari.
* **Integrità:** le informazioni non possono essere modificate nell'archiviazione o in transito.
* **Non ripudio:** l'autore delle informazioni non può negare tale creazione in un secondo momento.
* **Autenticazione:** il mittente e il destinatario possono confermare l'identità reciproca.

## <a name="encryption"></a>Crittografia

Il processo di crittografia richiede un algoritmo e una chiave. L'algoritmo definisce il modo in cui i dati vengono trasformati da testo non crittografato in testo crittografato e di nuovo in testo non crittografato. Una chiave è una stringa casuale di dati utilizzata come input per l'algoritmo. Tutta la sicurezza del processo è contenuta nella chiave. Pertanto, la chiave deve essere archiviata in modo sicuro. I dettagli degli algoritmi più diffusi, tuttavia, sono disponibili pubblicamente.

Esistono due tipi di crittografia. La crittografia simmetrica usa la stessa chiave sia per la crittografia che per la decrittografia. La crittografia asimmetrica usa chiavi diverse ma matematicamente correlate per eseguire la crittografia e la decrittografia.

### <a name="symmetric-encryption"></a>Crittografia simmetrica

La crittografia simmetrica usa la stessa chiave per crittografare il testo non crittografato in testo crittografato e decrittografare il testo crittografato in testo non crittografato. La lunghezza necessaria della chiave, espressa in numero di bit, è determinata dall'algoritmo . Dopo che la chiave è stata usata per crittografare il testo non crittografato, il messaggio crittografato viene inviato al destinatario che quindi decrittografa il testo crittografato. La chiave simmetrica deve essere trasmessa in modo sicuro al destinatario. L'invio della chiave è il rischio di sicurezza maggiore quando si usa un algoritmo simmetrico.

![Esempio di crittografia simmetrica](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Crittografia asimmetrica

Se si usa solo la crittografia simmetrica, il problema è che tutte le parti della comunicazione devono avere la chiave privata. Tuttavia, è possibile che terze parti non autorizzate possano acquisire la chiave durante la trasmissione agli utenti autorizzati. Per risolvere questo problema, usare invece la crittografia a chiave asimmetrica o pubblica.

Nella crittografia asimmetrica ogni utente ha due chiavi correlate matematicamente denominate coppia di chiavi. Una chiave è pubblica e l'altra è privata. La coppia di chiavi garantisce che solo il destinatario abbia accesso alla chiave privata necessaria per decrittografare i dati. Nella figura seguente viene riepilogato il processo di crittografia asimmetrica.

![Esempio di crittografia asimmetrica](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Il destinatario crea una coppia di chiavi pubblica-privata e invia la chiave pubblica a una CA. La CA imballa la chiave pubblica in un certificato X.509.

1. L'entità mittente ottiene la chiave pubblica del destinatario dalla CA.

1. Il mittente crittografa i dati di testo non crittografato usando un algoritmo di crittografia. La chiave pubblica del destinatario viene usata per eseguire la crittografia.

1. Il mittente trasmette il testo crittografato al destinatario. Non è necessario inviare la chiave perché il destinatario ha già la chiave privata necessaria per decrittografare il testo crittografato.

1. Il destinatario decrittografa il testo crittografato usando l'algoritmo asimmetrico e la chiave privata specificati.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Combinazione della crittografia simmetrica e asimmetrica

La crittografia simmetrica e asimmetrica può essere combinata per sfruttarne i vantaggi relativi. La crittografia simmetrica è molto più veloce rispetto a quella asimmetrica, ma, a causa della necessità di inviare chiavi private ad altre parti, non è così sicura. Per combinare i due tipi, è possibile usare la crittografia simmetrica per convertire il testo non crittografato in testo crittografato. La crittografia asimmetrica viene utilizzata per scambiare la chiave simmetrica. Questa operazione è illustrata nel diagramma seguente.

![Crittografia simmetrica e assymetrica](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Il mittente recupera la chiave pubblica del destinatario.

1. Il mittente genera una chiave simmetrica e la usa per crittografare i dati originali.

1. Il mittente usa la chiave pubblica del destinatario per crittografare la chiave simmetrica.

1. Il mittente trasmette la chiave simmetrica crittografata e il testo crittografato al destinatario previsto.

1. Il destinatario usa la chiave privata corrispondente alla chiave pubblica del destinatario per decrittografare la chiave simmetrica del mittente.

1. Il destinatario usa la chiave simmetrica per decrittografare il testo crittografato.

### <a name="asymmetric-signing"></a>Firma asimmetrica

Gli algoritmi asimmetrici possono essere usati per proteggere i dati dalla modifica e dimostrare l'identità dell'autore dei dati. La figura seguente mostra in che modo la firma asimmetrica consente di dimostrare l'identità del mittente.

![Esempio di firma asimmetrica](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Il mittente passa i dati di testo non crittografato tramite un algoritmo di crittografia asimmetrico, usando la chiave privata per la crittografia. Si noti che questo scenario inverte l'uso delle chiavi private e pubbliche descritte nella sezione precedente che illustra la crittografia asimmetrica dettagliata.

1. Il testo crittografato risultante viene inviato al destinatario.

1. Il destinatario ottiene la chiave pubblica dell'origine da una directory.

1. Il destinatario decrittografa il testo crittografato usando la chiave pubblica dell'autore. Il testo non crittografato risultante dimostra l'identità dell'autore perché solo l'autore ha accesso alla chiave privata che inizialmente ha crittografato il testo originale.

## <a name="signing"></a>per la firma

La firma digitale può essere usata per determinare se i dati sono stati modificati in transito o in pausa. I dati vengono passati tramite un algoritmo hash, una funzione unidiretiva che produce un risultato matematico dal messaggio specificato. Il risultato è denominato valore *hash,* *digest del messaggio,* *digest,* *firma* o *identificazione personale.* Non è possibile invertire un valore hash per ottenere il messaggio originale. Poiché una piccola modifica del messaggio comporta una modifica significativa dell'identificazione *personale,* il valore hash può essere usato per determinare se un messaggio è stato modificato. Nella figura seguente viene illustrato come usare la crittografia asimmetrica e gli algoritmi hash per verificare che un messaggio non sia stato modificato.

![Esempio di firma](media/tutorial-x509-introduction/signing.png)

1. Il mittente crea un messaggio di testo non crittografato.

1. Il mittente esegue l'hashing del messaggio in testo non crittografato per creare un digest del messaggio.

1. Il mittente crittografa il digest usando una chiave privata.

1. Il mittente trasmette il messaggio di testo non crittografato e il digest crittografato al destinatario previsto.

1. Il destinatario decrittografa il digest usando la chiave pubblica del mittente.

1. Il destinatario esegue lo stesso algoritmo hash usato dal mittente sul messaggio.

1. Il destinatario confronta la firma risultante con la firma decrittografata. Se i digest sono uguali, il messaggio non è stato modificato durante la trasmissione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui campi che costituiscono un certificato, vedere Informazioni sui certificati a [chiave pubblica X.509](tutorial-x509-certificates.md).

Se si conoscono già molto i certificati X.509 e si vogliono generare versioni di test che è possibile usare per l'autenticazione nell'hub IoT, vedere gli argomenti seguenti:

* [Uso Microsoft-Supplied script per creare certificati di test](tutorial-x509-scripts.md)
* [Uso di OpenSSL per creare certificati di test](tutorial-x509-openssl.md)
* [Uso di OpenSSL per creare Self-Signed certificati di test](tutorial-x509-self-sign.md)

Se si dispone di un certificato dell'autorità di certificazione (CA) o di un certificato della CA subordinata e si vuole caricarlo nell'hub IoT e dimostrarne la proprietà, vedere Dimostrazione del possesso di un certificato [della CA.](tutorial-x509-prove-possession.md)
