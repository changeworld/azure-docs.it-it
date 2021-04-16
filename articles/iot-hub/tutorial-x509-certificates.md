---
title: 'Esercitazione: Informazioni sui certificati a chiave pubblica X.509 per hub IoT di Azure| Microsoft Docs'
description: 'Esercitazione: Informazioni sui certificati a chiave pubblica X.509 per hub IoT di Azure'
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378347"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Esercitazione: Informazioni sui certificati a chiave pubblica X.509

I certificati X.509 sono documenti digitali che rappresentano un utente, un computer, un servizio o un dispositivo. Vengono rilasciati da un'autorità di certificazione (CA), da una CA subordinata o da un'autorità di registrazione e contengono la chiave pubblica del soggetto del certificato. Non contengono la chiave privata del soggetto che deve essere archiviata in modo sicuro. I certificati a chiave pubblica sono documentati [da RFC 5280.](https://tools.ietf.org/html/rfc5280) Sono firmati digitalmente e, in generale, contengono le informazioni seguenti:

* Informazioni sul soggetto del certificato
* Chiave pubblica che corrisponde alla chiave privata dell'oggetto
* Informazioni sulla CA emittente
* Algoritmi di crittografia e/o firma digitale supportati
* Informazioni per determinare lo stato di revoca e validità del certificato

## <a name="certificate-fields"></a>Campi del certificato

Nel corso del tempo sono state presenti tre versioni del certificato. Ogni versione aggiunge campi a quella precedente. La versione 3 è corrente e contiene i campi versione 1 e versione 2 oltre ai campi della versione 3. Nella versione 1 sono stati definiti i campi seguenti:

* **Versione:** valore (1, 2 o 3) che identifica il numero di versione del certificato
* **Numero di serie:** numero univoco per ogni certificato emesso da una CA
* **Algoritmo di firma CA:** nome dell'algoritmo utilizzato dalla CA per firmare il contenuto del certificato
* **Nome autorità di certificazione:** nome distinto (DN) della CA emittente del certificato
* **Periodo di validità:** periodo di tempo per il quale il certificato è considerato valido
* **Nome soggetto:** nome dell'entità rappresentata dal certificato
* **Informazioni sulla chiave pubblica del** soggetto: chiave pubblica di proprietà del soggetto del certificato

Nella versione 2 sono stati aggiunti i campi seguenti contenenti informazioni sull'autorità emittente del certificato. Questi campi vengono tuttavia usati raramente.

* **ID univoco autorità di** certificazione: identificatore univoco per la CA emittente come definito dalla CA
* **ID univoco** del soggetto: identificatore univoco per il soggetto del certificato definito dalla CA emittente

I certificati versione 3 hanno aggiunto le estensioni seguenti:

* **Identificatore di chiave** dell'autorità: può essere uno dei due valori seguenti:
  * Oggetto della CA e numero di serie del certificato ca che ha emesso il certificato
  * Hash della chiave pubblica della CA che ha emesso questo certificato
* **Identificatore della chiave del** soggetto: hash della chiave pubblica del certificato corrente
* **Utilizzo delle chiavi** Definisce il servizio per cui è possibile usare un certificato. Può trattarsi di uno o più dei valori seguenti:
  * **Firma digitale**
  * **Non ripudio**
  * **Crittografia chiave**
  * **Crittografia dei dati**
  * **Contratto chiave**
  * **Firma del certificato della chiave**
  * **Firma CRL**
  * **Solo crittografia**
  * **Solo crittografia**
* **Periodo di utilizzo della chiave privata:** periodo di validità per la parte della chiave privata di una coppia di chiavi
* **Criteri certificato:** criteri usati per convalidare il soggetto del certificato
* **Mapping dei criteri:** esegue il mapping di un criterio in un'organizzazione ai criteri in un'altra
* **Nome alternativo soggetto:** elenco di nomi alternativi per l'oggetto
* **Nome alternativo autorità di certificazione:** elenco di nomi alternativi per la CA emittente
* **Attributo dir soggetto:** attributi da una directory X.500 o LDAP
* **Vincoli di base:** consente al certificato di indicare se viene rilasciato a una CA o a un utente, un computer, un dispositivo o un servizio. Questa estensione include anche un vincolo di lunghezza del percorso che limita il numero di CA subordinate che possono esistere.
* **Vincoli dei nomi:** definisce gli spazi dei nomi consentiti in un certificato emesso dall'autorità di certificazione
* **Vincoli dei** criteri: possono essere usati per impedire i mapping dei criteri tra le CA
* **Utilizzo chiavi esteso:** indica come usare la chiave pubblica di un certificato oltre gli scopi identificati nell'estensione **Utilizzo** chiavi
* **Punti di distribuzione CRL:** contiene uno o più URL in cui è pubblicato l'elenco di revoche di certificati (CRL) di base
* **Inibire anyPolicy:** impedisce  l'uso dell'OID Tutti i criteri di rilascio (2.5.29.32.0) nei certificati della CA subordinata
* **Freshest CRL**: contiene uno o più URL in cui viene pubblicato il delta CRL della CA emittente
* **Accesso alle informazioni dell'autorità:** contiene uno o più URL in cui è pubblicato il certificato della CA emittente
* **Accesso alle informazioni sul** soggetto: contiene informazioni su come recuperare dettagli aggiuntivi per un soggetto del certificato

## <a name="certificate-formats"></a>Formati dei certificati

I certificati possono essere salvati in diversi formati. hub IoT di Azure'autenticazione usa in genere i formati PEM e PFX.

### <a name="binary-certificate"></a>Certificato binario

Contiene un certificato binario in formato non elaborato usando la codifica DER ASN.1.

### <a name="ascii-pem-format"></a>Formato PEM ASCII

Un certificato PEM (estensione pem) contiene un certificato con codifica Base64 che inizia con -----BEGIN CERTIFICATE----- e termina con -----END CERTIFICATE-----. Il formato PEM è molto comune ed è richiesto dall'hub IoT quando si caricano determinati certificati.

### <a name="ascii-pem-key"></a>Chiave ASCII (PEM)

Contiene una chiave DER con codifica Base64 con metadati eventualmente aggiuntivi sull'algoritmo usato per la protezione tramite password.

### <a name="pkcs7-certificate"></a>PKCS #7 certificato

Formato progettato per il trasporto di dati firmati o crittografati. È definito da [RFC 2315](https://tools.ietf.org/html/rfc2315). Può includere l'intera catena di certificati.

### <a name="pkcs8-key"></a>Chiave PKCS#8

Formato per un archivio chiavi privato definito da [RFC 5208.](https://tools.ietf.org/html/rfc5208)

### <a name="pkcs12-key-and-certificate"></a>PKCS #12 chiave e certificato

Formato complesso in grado di archiviare e proteggere una chiave e l'intera catena di certificati. Viene comunemente usato con un'estensione pfx. PKCS #12 è sinonimo del formato PFX.

## <a name="for-more-information"></a>Per ulteriori informazioni

Per altre informazioni, vedere i seguenti argomenti:

* [Guida del layman al gergo del certificato X.509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Comprensione concettuale dei certificati x.509 nel settore IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Passaggi successivi

Per generare certificati di test che è possibile usare per autenticare i dispositivi nell'hub IoT, vedere gli argomenti seguenti:

* [Uso Microsoft-Supplied script per creare certificati di test](tutorial-x509-scripts.md)
* [Uso di OpenSSL per creare certificati di test](tutorial-x509-openssl.md)
* [Uso di OpenSSL per creare Self-Signed certificati di test](tutorial-x509-self-sign.md)

Se si dispone di un certificato dell'autorità di certificazione (CA) o di un certificato della CA subordinata e si vuole caricarlo nell'hub IoT e dimostrarne la proprietà, vedere Dimostrazione del possesso di un certificato [della CA.](tutorial-x509-prove-possession.md)
