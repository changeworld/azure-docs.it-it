---
title: Esercitazione-comprendere i certificati di chiave pubblica X. 509 per l'hub Azure Microsoft Docs
description: Esercitazione-comprendere i certificati di chiave pubblica X. 509 per l'hub Azure
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
- devx-track-azurecli
ms.openlocfilehash: fada68ba395b959e557542eb8c230561aad84214
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384324"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Esercitazione: informazioni sui certificati di chiave pubblica X. 509

I certificati X. 509 sono documenti digitali che rappresentano un utente, un computer, un servizio o un dispositivo. Vengono rilasciati da un'autorità di certificazione (CA), una CA subordinata o un'autorità di registrazione e contengono la chiave pubblica del soggetto del certificato. Non contengono la chiave privata dell'oggetto che deve essere archiviata in modo sicuro. I certificati di chiave pubblica sono documentati da [RFC 5280](https://tools.ietf.org/html/rfc5280). Sono firmati digitalmente e, in generale, contengono le informazioni seguenti:

* Informazioni sul soggetto del certificato
* Chiave pubblica che corrisponde alla chiave privata dell'oggetto
* Informazioni sulla CA emittente
* Algoritmi di crittografia e/o firma digitale supportati
* Informazioni per determinare lo stato di revoca e di validità del certificato

## <a name="certificate-fields"></a>Campi del certificato

Nel corso del tempo sono state riportate tre versioni del certificato. Ogni versione aggiunge i campi a quello precedente. La versione 3 è aggiornata e contiene i campi versione 1 e versione 2 oltre ai campi versione 3. La versione 1 definisce i campi seguenti:

* **Version**: valore (1, 2 o 3) che identifica il numero di versione del certificato.
* **Numero di serie**: numero univoco per ogni certificato emesso da un'autorità di certificazione
* **Algoritmo di firma CA**: nome dell'algoritmo usato dall'autorità di certificazione per firmare il contenuto del certificato
* **Nome dell'autorità emittente**: il nome distinto (DN) della CA emittente del certificato
* **Periodo di validità**: periodo di tempo per il quale il certificato è considerato valido
* **Nome soggetto**: nome dell'entità rappresentata dal certificato
* **Informazioni sulla chiave pubblica del soggetto**: chiave pubblica posseduta dal soggetto del certificato

Nella versione 2 sono stati aggiunti i campi seguenti contenenti informazioni sull'autorità emittente del certificato. Questi campi vengono tuttavia usati raramente.

* **ID univoco dell'autorità** di certificazione: identificatore univoco per la CA emittente come definito dalla CA
* **ID oggetto univoco**: identificatore univoco per l'oggetto del certificato definito dalla CA emittente

I certificati della versione 3 hanno aggiunto le estensioni seguenti:

* **Identificatore di chiave dell'autorità**: può essere uno dei due valori seguenti:
  * Oggetto della CA e del numero di serie del certificato della CA che ha emesso il certificato
  * Hash della chiave pubblica dell'autorità di certificazione che ha emesso il certificato
* **Identificatore della chiave del soggetto**: hash della chiave pubblica del certificato corrente
* **Utilizzo chiavi** Definisce il servizio per il quale è possibile utilizzare un certificato. Può essere uno o più dei valori seguenti:
  * **Firma digitale**
  * **Non ripudio**
  * **Crittografia chiave**
  * **Crittografia dei dati**
  * **Accordo chiave**
  * **Firma certificato chiave**
  * **Firma CRL**
  * **Solo crittografia**
  * **Solo decrittografia**
* **Periodo di utilizzo della chiave privata**: periodo di validità della parte di chiave privata di una coppia di chiavi
* **Criteri dei certificati**: criteri usati per convalidare il soggetto del certificato
* **Mapping dei criteri**: esegue il mapping di un criterio in un'organizzazione ai criteri in un altro
* **Nome alternativo del soggetto**: elenco di nomi alternativi per l'oggetto
* **Nome alternativo dell'autorità emittente**: elenco di nomi alternativi per la CA emittente
* **Attributo dir oggetto**: attributi da una directory X. 500 o LDAP
* **Vincoli di base**: consente al certificato di designare se è rilasciato a una CA o a un utente, un computer, un dispositivo o un servizio. Questa estensione include anche un vincolo di lunghezza del percorso che limita il numero di CA subordinate che possono esistere.
* **Vincoli di nome**: designa gli spazi dei nomi consentiti in un certificato emesso da un'autorità di certificazione
* **Vincoli dei criteri**: possono essere usati per impedire i mapping dei criteri tra le autorità di certificazione
* **Utilizzo chiavi avanzato**: indica il modo in cui è possibile usare la chiave pubblica di un certificato oltre gli scopi identificati nell'estensione **utilizzo chiavi**
* **Punti di distribuzione CRL**: contiene uno o più URL in cui viene pubblicato l'elenco di revoche di certificati (CRL) di base
* **Inibire anyPolicy**: impedisce l'uso di **tutti i criteri di rilascio** OID (2.5.29.32.0) nei certificati della CA subordinata
* **CRL più recente**: contiene uno o più URL in cui è pubblicato il Delta CRL della CA emittente
* **Accesso alle informazioni sull'autorità**: contiene uno o più URL in cui è pubblicato il certificato della CA emittente
* **Accesso alle informazioni sull'oggetto**: contiene informazioni su come recuperare ulteriori dettagli per un oggetto del certificato

## <a name="certificate-formats"></a>Formati dei certificati

I certificati possono essere salvati in vari formati. L'autenticazione dell'hub Azure Internet usa in genere i formati PEM e PFX.

### <a name="binary-certificate"></a>Certificato binario

Contiene un certificato binario form RAW che utilizza la codifica DER ASN. 1.

### <a name="ascii-pem-format"></a>Formato PEM ASCII

Un certificato PEM (estensione PEM) contiene un certificato con codifica Base64 che inizia con----------BEGIN CERTIFICAte e termina con-----certificato END-----. Il formato PEM è molto comune ed è richiesto dall'hub Internet durante il caricamento di determinati certificati.

### <a name="ascii-pem-key"></a>Chiave ASCII (PEM)

Contiene una chiave DER con codifica Base64 con metadati eventualmente aggiuntivi relativi all'algoritmo utilizzato per la protezione con password.

### <a name="pkcs7-certificate"></a>Certificato PKCS # 7

Formato progettato per il trasporto di dati firmati o crittografati. Viene definito da [RFC 2315](https://tools.ietf.org/html/rfc2315). Può includere l'intera catena di certificati.

### <a name="pkcs8-key"></a>Chiave PKCS # 8

Formato per un archivio chiavi privato definito da [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Chiave e certificato PKCS # 12

Formato complesso che consente di archiviare e proteggere una chiave e l'intera catena di certificati. Viene in genere usato con l'estensione pfx. PKCS # 12 è sinonimo del formato PFX.

## <a name="for-more-information"></a>Per ulteriori informazioni

Per altre informazioni, vedere i seguenti argomenti:

* [La guida del laico al gergo dei certificati X. 509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Comprensione concettuale dei certificati x.509 nel settore IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Passaggi successivi

Se si vuole generare certificati di test che è possibile usare per autenticare i dispositivi nell'hub Internet, vedere gli argomenti seguenti:

* [Uso di script Microsoft-Supplied per creare certificati di test](tutorial-x509-scripts.md)
* [Uso di OpenSSL per creare certificati di test](tutorial-x509-openssl.md)
* [Uso di OpenSSL per creare certificati di test Self-Signed](tutorial-x509-self-sign.md)

Se si dispone di un certificato dell'autorità di certificazione (CA) o di un certificato della CA subordinata e si vuole caricarlo nell'hub Internet e dimostrare di essere il proprietario, vedere [dimostrazione del possesso di un certificato della CA](tutorial-x509-prove-possession.md).
