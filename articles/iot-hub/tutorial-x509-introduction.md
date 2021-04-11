---
title: "Esercitazione: informazioni sulla crittografia e sui certificati X. 509 per l'hub Azure. Microsoft Docs"
description: Esercitazione-comprendere la crittografia e l'infrastruttura a chiave pubblica X. 509 per l'hub Azure
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
- devx-track-azurecli
ms.openlocfilehash: 88f5803e1d4348b79c7675d627a541ff47700dc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630750"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Esercitazione: informazioni sulla crittografia a chiave pubblica e sull'infrastruttura a chiave pubblica X. 509

È possibile usare i certificati X. 509 per autenticare i dispositivi in un hub Azure. Un certificato è un documento digitale che contiene la chiave pubblica del dispositivo e può essere usato per verificare che il dispositivo sia quello che dichiara di essere. I certificati X. 509 e gli elenchi di revoche di certificati (CRL) sono documentati da [RFC 5280](https://tools.ietf.org/html/rfc5280). I certificati sono solo una parte di un'infrastruttura a chiave pubblica (PKI) X. 509. Per comprendere l'infrastruttura a chiave pubblica X. 509, è necessario comprendere gli algoritmi di crittografia, le chiavi crittografiche, i certificati e le autorità di certificazione (CAs):

* Gli **algoritmi** definiscono il modo in cui i dati in testo non crittografato originale vengono trasformati in testo crittografato e viceversa.
* Le **chiavi** sono stringhe di dati casuali o pseudocasuale utilizzate come input per un algoritmo.
* I **certificati** sono documenti digitali che contengono la chiave pubblica di un'entità e consentono di determinare se l'oggetto del certificato è chi o cosa dichiara di essere.
* Le **autorità di certificazione** attestano l'autenticità degli argomenti del certificato.

È possibile acquistare un certificato da un'autorità di certificazione (CA). È anche possibile, per i test e lo sviluppo, oppure se si lavora in un ambiente autonomo, creare una CA radice autofirmata. Se, ad esempio, si è proprietari di uno o più dispositivi e si sta testando l'autenticazione dell'hub Internet, è possibile firmare autonomamente la CA radice e usarla per emettere i certificati del dispositivo. È anche possibile emettere certificati per dispositivi autofirmati. Questa operazione viene illustrata negli articoli successivi.

Prima di illustrare i certificati X. 509 in modo più dettagliato e di usarli per autenticare i dispositivi in un hub Internet delle cose, viene illustrata la crittografia su cui si basano i certificati.

## <a name="cryptography"></a>Crittografia

La crittografia viene usata per proteggere le informazioni e le comunicazioni. Questa operazione viene in genere eseguita usando le tecniche di crittografia per rimescolare il testo normale (testo normale) nel testo crittografato (codificato) e viceversa. Questo processo di rimescolamento è denominato crittografia. Il processo inverso viene definito decrittografia. La crittografia è interessata dagli obiettivi seguenti:

* **Riservatezza**: le informazioni possono essere riconosciute solo dai destinatari desiderati.
* **Integrità**: le informazioni non possono essere modificate nell'archiviazione o in transito.
* **Non ripudio**: il creatore di informazioni non può in seguito negare la creazione.
* **Autenticazione**: il mittente e il destinatario possono confermare l'identità dell'altro.

## <a name="encryption"></a>Crittografia

Il processo di crittografia richiede un algoritmo e una chiave. L'algoritmo definisce la modalità di trasformazione dei dati dal testo non crittografato al testo crittografato e viceversa. Una chiave è una stringa casuale di dati utilizzata come input per l'algoritmo. Tutta la sicurezza del processo è contenuta nella chiave. Pertanto, la chiave deve essere archiviata in modo sicuro. I dettagli degli algoritmi più diffusi, tuttavia, sono disponibili pubblicamente.

Esistono due tipi di crittografia. La crittografia simmetrica utilizza la stessa chiave sia per la crittografia che per la decrittografia. La crittografia asimmetrica USA chiavi diverse ma correlate matematicamente per eseguire la crittografia e la decrittografia.

### <a name="symmetric-encryption"></a>Crittografia simmetrica

La crittografia simmetrica utilizza la stessa chiave per crittografare il testo non crittografato e decrittografare il testo crittografato in testo normale. La lunghezza necessaria della chiave, espressa in numero di bit, è determinata dall'algoritmo. Dopo che la chiave è stata usata per crittografare il testo non crittografato, il messaggio crittografato viene inviato al destinatario che quindi decrittografa il testo crittografato. La chiave simmetrica deve essere trasmessa in modo sicuro al destinatario. L'invio della chiave costituisce il maggior rischio di sicurezza quando si utilizza un algoritmo simmetrico.

![Esempio di crittografia simmetrica](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Crittografia asimmetrica

Se viene utilizzata solo la crittografia simmetrica, il problema è che tutte le parti della comunicazione devono disporre della chiave privata. Tuttavia, è possibile che terze parti non autorizzate possano acquisire la chiave durante la trasmissione agli utenti autorizzati. Per risolvere questo problema, usare invece la crittografia a chiave pubblica o asimmetrica.

In crittografia asimmetrica ogni utente dispone di due chiavi matematicamente correlate, denominate coppia di chiavi. Una chiave è pubblica e l'altra chiave è privata. La coppia di chiavi garantisce che solo il destinatario abbia accesso alla chiave privata necessaria per decrittografare i dati. Nell'illustrazione seguente viene riepilogato il processo di crittografia asimmetrica.

![Esempio di crittografia asimmetrica](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Il destinatario crea una coppia di chiavi pubblica/privata e Invia la chiave pubblica a una CA. La CA costituisce il pacchetto della chiave pubblica in un certificato X. 509.

1. L'entità mittente Ottiene la chiave pubblica del destinatario dalla CA.

1. Il mittente crittografa i dati in testo non crittografato usando un algoritmo di crittografia. La chiave pubblica del destinatario viene utilizzata per eseguire la crittografia.

1. Il mittente trasmette il testo crittografato al destinatario. Non è necessario inviare la chiave perché il destinatario dispone già della chiave privata necessaria per decrittografare il testo crittografato.

1. Il destinatario decrittografa il testo crittografato utilizzando l'algoritmo asimmetrico specificato e la chiave privata.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Combinazione della crittografia simmetrica e asimmetrica

È possibile combinare la crittografia simmetrica e asimmetrica per sfruttare i vantaggi dei loro punti di forza. La crittografia simmetrica è molto più veloce rispetto a asimmetrica ma, a causa della necessità di inviare chiavi private ad altre parti, non è sicura. Per combinare i due tipi insieme, è possibile usare la crittografia simmetrica per convertire il testo non crittografato in testo crittografato. Per scambiare la chiave simmetrica viene utilizzata la crittografia asimmetrica. Questa operazione viene illustrata nella figura seguente.

![Crittografia simmetrica e asimmetrica](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Il mittente recupera la chiave pubblica del destinatario.

1. Il mittente genera una chiave simmetrica e la usa per crittografare i dati originali.

1. Il mittente utilizza la chiave pubblica del destinatario per crittografare la chiave simmetrica.

1. Il mittente trasmette la chiave simmetrica crittografata e il testo crittografato al destinatario previsto.

1. Il destinatario utilizza la chiave privata corrispondente alla chiave pubblica del destinatario per decrittografare la chiave simmetrica del mittente.

1. Il destinatario usa la chiave simmetrica per decrittografare il testo crittografato.

### <a name="asymmetric-signing"></a>Firma asimmetrica

Gli algoritmi asimmetrici possono essere usati per proteggere i dati dalla modifica e dimostrare l'identità dell'autore di dati. Nella figura seguente viene illustrato il modo in cui la firma asimmetrica consente di dimostrare l'identità del mittente.

![Esempio di firma asimmetrica](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Il mittente passa i dati in testo non crittografato tramite un algoritmo di crittografia asimmetrica, usando la chiave privata per la crittografia. Si noti che questo scenario inverte l'uso delle chiavi pubbliche e private descritte nella sezione precedente, la crittografia asimmetrica dettagliata.

1. Il testo crittografato risultante viene inviato al destinatario.

1. Il destinatario Ottiene la chiave pubblica del creatore da una directory.

1. Il destinatario decrittografa il testo crittografato utilizzando la chiave pubblica del creatore. Il testo non crittografato risultante dimostra l'identità dell'originatore perché solo l'originatore ha accesso alla chiave privata che ha inizialmente crittografato il testo originale.

## <a name="signing"></a>per la firma

La firma digitale può essere usata per determinare se i dati sono stati modificati in transito o inattivi. I dati vengono passati tramite un algoritmo hash, una funzione unidirezionale che produce un risultato matematico dal messaggio specificato. Il risultato è un *valore hash*, un *digest del messaggio*, un *digest*, una *firma* o un' *identificazione personale*. Non è possibile invertire un valore hash per ottenere il messaggio originale. Poiché una piccola modifica nel messaggio comporta una modifica significativa nell' *identificazione personale*, il valore hash può essere utilizzato per determinare se un messaggio è stato modificato. Nella figura seguente viene illustrato il modo in cui è possibile utilizzare la crittografia asimmetrica e gli algoritmi hash per verificare che un messaggio non sia stato modificato.

![Esempio di firma](media/tutorial-x509-introduction/signing.png)

1. Il mittente crea un messaggio di testo non crittografato.

1. Il mittente genera un hash per il messaggio di testo non crittografato per creare un digest del messaggio.

1. Il mittente crittografa il digest usando una chiave privata.

1. Il mittente trasmette il messaggio in testo non crittografato e il digest crittografato al destinatario previsto.

1. Il destinatario decrittografa il digest utilizzando la chiave pubblica del mittente.

1. Il destinatario esegue lo stesso algoritmo hash utilizzato dal mittente sul messaggio.

1. Il destinatario confronta la firma risultante con la firma decrittografata. Se i digest sono uguali, il messaggio non è stato modificato durante la trasmissione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui campi che costituiscono un certificato, vedere informazioni sui [certificati di chiave pubblica X. 509](tutorial-x509-certificates.md).

Se si conoscono già i certificati X. 509 e si vogliono generare versioni di test che è possibile usare per l'autenticazione nell'hub Internet delle cose, vedere gli argomenti seguenti:

* [Uso di script Microsoft-Supplied per creare certificati di test](tutorial-x509-scripts.md)
* [Uso di OpenSSL per creare certificati di test](tutorial-x509-openssl.md)
* [Uso di OpenSSL per creare certificati di test Self-Signed](tutorial-x509-self-sign.md)

Se si dispone di un certificato dell'autorità di certificazione (CA) o di un certificato della CA subordinata e si vuole caricarlo nell'hub Internet e dimostrare di essere il proprietario, vedere [dimostrazione del possesso di un certificato della CA](tutorial-x509-prove-possession.md).
