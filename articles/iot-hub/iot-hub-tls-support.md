---
title: Supporto TLS per l'hub IoT di Azure
description: Informazioni sull'uso delle connessioni TLS sicure per i dispositivi e i servizi che comunicano con l'hub Internet
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: d36a7917693aef9063ade473759f2f451d3a677f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234019"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Supporto di Transport Layer Security (TLS) nell'hub Internet

L'hub IoT usa il protocollo Transport Layer Security (TLS) per la protezione delle connessioni dai dispositivi e dai servizi IoT. Attualmente sono supportate tre versioni del protocollo TLS, ovvero le versioni 1.0, 1.1 e 1.2.

Le versioni 1.0 e 1.1 di TLS sono considerate legacy e ne è prevista la deprecazione. Per altre informazioni, vedere [Deprecazione di TLS 1.0 e 1.1 nell'hub IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). Per evitare problemi futuri, usare TLS 1,2 come unica versione di TLS quando ci si connette all'hub Internet.

## <a name="iot-hubs-server-tls-certificate"></a>Certificato TLS del server dell'hub Internet

Durante un handshake TLS, l'hub Internet presenta certificati server con chiave RSA per la connessione dei client. La radice è la CA radice Cybertrust Baltimore. Di recente è stata implementata una modifica al certificato del server TLS, in modo che venga ora emesso dalle nuove autorità di certificazione intermedie (ICA). Per altre informazioni, vedere [aggiornamento del certificato TLS dell'hub](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)Internet.

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Certificato TLS server crittografia a curva ellittica (ECC) (anteprima)

Il certificato TLS dell'hub ECC è disponibile per l'anteprima pubblica. Pur offrendo una sicurezza simile ai certificati RSA, la convalida dei certificati ECC (con pacchetti di crittografia solo per l'ECC) USA fino al 40% di calcolo, memoria e larghezza di banda inferiori. Questi risparmi sono importanti per i dispositivi Internet a causa dei loro profili e memoria più piccoli e per supportare i casi d'uso negli ambienti con larghezza di banda di rete limitata. 

Per visualizzare in anteprima il certificato del server ECC dell'hub Internet:

1. [Creare un nuovo hub Internet con la modalità di anteprima in](iot-hub-preview-mode.md).
1. [Configurare il client](#tls-configuration-for-sdk-and-iot-edge) in modo da includere *solo* i pacchetti di crittografia ECDSA ed *escludere* quelli RSA. Questi sono i pacchetti di crittografia supportati per l'anteprima pubblica del certificato ECC:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Connettere il client all'hub di anteprima.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Imposizione di TLS 1,2 disponibile in aree selezionate

Per una maggiore sicurezza, configurare gli hub Internet per consentire *solo* le connessioni client che usano TLS versione 1,2 e per applicare l'uso di pacchetti di [crittografia](#cipher-suites). Questa funzionalità è supportata solo nelle aree geografiche seguenti:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2
* US Gov Arizona
* US Gov Virginia (il supporto di TLS 1.0/1.1 non è disponibile in questa area-l'imposizione di TLS 1,2 deve essere abilitata o la creazione dell'hub Internet non riesce)

Per abilitare l'imposizione di TLS 1,2, seguire i passaggi descritti in [creare un hub Internet in portale di Azure](iot-hub-create-through-portal.md), eccetto

- Scegliere un' **area** da uno nell'elenco precedente.
- In **Management-> Advanced-> Transport Layer Security (TLS)-> versione minima di TLS** selezionare **1,2**. Questa impostazione viene visualizzata solo per l'hub Internet delle cose creato nell'area supportata.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="Screenshot che illustra come attivare l'imposizione di TLS 1,2 durante la creazione dell'hub":::

Per usare il modello ARM per la creazione, effettuare il provisioning di un nuovo hub Internet in una delle aree supportate e impostare la `minTlsVersion` proprietà su `1.2` nella specifica della risorsa:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

La risorsa dell'hub IoT creata con questa configurazione rifiuterà i client dei dispositivi e dei servizi che tentano di connettersi usando le versioni 1.0 e 1.1 di TLS. Analogamente, l'handshake TLS verrà rifiutato se il `ClientHello` messaggio non elenca alcuna [crittografia consigliata](#cipher-suites).

> [!NOTE]
> La proprietà `minTlsVersion` è di sola lettura e non è possibile modificarla in seguito alla creazione della risorsa dell'hub IoT. È pertanto essenziale testare e convalidare anticipatamente e in modo adeguato la compatibilità di *tutti* i dispositivi e i servizi IoT con TLS 1.2 e con le [crittografie consigliate](#cipher-suites).
> 
> Al momento del failover, la proprietà `minTlsVersion` dell'hub IoT rimarrà valida nell'area abbinata in seguito al failover.

## <a name="cipher-suites"></a>Pacchetti di crittografia

Gli hub Internet delle cose configurati per accettare solo TLS 1,2 imporrà anche l'uso dei seguenti pacchetti di crittografia consigliati:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Per gli hub Internet non configurati per l'imposizione di TLS 1,2, TLS 1,2 funziona comunque con i pacchetti di crittografia seguenti:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Un client può suggerire un elenco di pacchetti di crittografia più elevati da usare durante `ClientHello` . Tuttavia, alcune di esse potrebbero non essere supportate dall'hub Internet (ad esempio, `ECDHE-ECDSA-AES256-GCM-SHA384` ). In questo caso, l'hub Internet tenterà di seguire le preferenze del client, ma alla fine negozierà il pacchetto di crittografia con `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Configurazione TLS per SDK e IoT Edge

Usare i collegamenti seguenti per configurare TLS 1.2 e le crittografie consentite negli SDK del client dell'hub IoT.

| Linguaggio | Versioni che supportano TLS 1.2 | Documentazione |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 o versioni successive            | [Collegamento](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versione 2.0.0 o successive             | [Collegamento](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versione 1.21.4 o successive            | [Collegamento](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versione 1.19.0 o successive            | [Collegamento](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versione 1.12.2 o successive            | [Collegamento](https://aka.ms/Tls_Node_SDK_IoT) |

È possibile configurare l'uso di TLS 1.2 da parte dei dispositivi IoT Edge durante la comunicazione con l'hub IoT. A tale scopo, vedere la [pagina della documentazione di IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Autenticazione del dispositivo

Al termine di un handshake TLS riuscito, l'hub Internet può autenticare un dispositivo usando una chiave simmetrica o un certificato X. 509. Per l'autenticazione basata su certificati, può trattarsi di qualsiasi certificato X. 509, incluso ECC. L'hub Internet per la convalida del certificato viene convalidato in base all'identificazione personale o all'autorità di certificazione (CA) fornita. Per altre informazioni, vedere [certificati X. 509 supportati](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Negoziazione lunghezza massima del frammento TLS (anteprima)

L'hub Internet delle cose supporta inoltre la negoziazione della lunghezza massima del frammento TLS, che talvolta è nota come negoziazione della dimensione del frame TLS. Questa funzionalità è disponibile in anteprima pubblica. 

Utilizzare questa funzionalità per specificare la lunghezza massima del frammento di testo non crittografato a un valore inferiore a 2 ^ 14 byte predefiniti. Una volta negoziati, l'hub e il client iniziano a frammentare i messaggi per assicurarsi che tutti i frammenti siano inferiori alla lunghezza negoziata. Questo comportamento è utile per il calcolo o i dispositivi con vincoli di memoria. Per altre informazioni, vedere la [specifica dell'estensione TLS ufficiale](https://tools.ietf.org/html/rfc6066#section-4).

Il supporto ufficiale di SDK per questa funzionalità di anteprima pubblica non è ancora disponibile. Per iniziare

1. [Creare un nuovo hub Internet con la modalità di anteprima in](iot-hub-preview-mode.md).
1. Quando si usa OpenSSL, chiamare [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) per specificare le dimensioni del frammento.
1. Connettere il client all'hub di anteprima.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla sicurezza e sul controllo di accesso dell'hub Internet, vedere [controllare l'accesso all'hub](iot-hub-devguide-security.md)Internet.
- Per altre informazioni sull'uso del certificato X509 per l'autenticazione del dispositivo, vedere [autenticazione del dispositivo con certificati della CA X. 509](iot-hub-x509ca-overview.md)
