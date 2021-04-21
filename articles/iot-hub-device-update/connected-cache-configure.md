---
title: Configurare Microsoft cache connessa per l'aggiornamento dei dispositivi per hub IoT di Azure | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Panoramica di Microsoft cache connessa per l'aggiornamento dei dispositivi per hub IoT di Azure
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811832"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Configurare Microsoft cache connessa per l'aggiornamento dei dispositivi per hub IoT di Azure

Microsoft cache connessa viene distribuito Azure IoT Edge gateway come Azure IoT Edge modulo. Come altri moduli Azure IoT Edge, le variabili di ambiente di distribuzione dei moduli MCC e le opzioni di creazione del contenitore vengono usate per configurare i moduli cache connessa Microsoft.  Questa sezione definisce le variabili di ambiente e le opzioni di creazione del contenitore necessarie per distribuire correttamente il modulo Microsoft cache connessa per l'uso da parte di Device Update per hub IoT di Azure.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Dettagli sulla distribuzione del modulo Microsoft cache connessa Azure IoT Edge

La denominazione del modulo Microsoft cache connessa è a discrezione dell'amministratore. Non sono presenti altre interazioni tra moduli o servizi che si basano sul nome del modulo per la comunicazione. Inoltre, la relazione padre-figlio dei server Microsoft cache connessa non dipende dal nome del modulo, bensì dall'FQDN o dall'indirizzo IP del gateway Azure IoT Edge configurato come descritto in precedenza.

Le variabili cache connessa Azure IoT Edge di ambiente del modulo Microsoft cache connessa Azure IoT Edge vengono usate per passare al contenitore le informazioni di base sull'identità del modulo e le impostazioni del modulo funzionale.

| Nome variabile                 | Formato del valore                           | Obbligatorio/facoltativo | Funzionalità                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID ID sottoscrizione di Azure             | Obbligatoria          | Si tratta della chiave del cliente, che fornisce sicurezza<br>autenticazione del nodo della cache da Ottimizzazione recapito<br>Servizi<br>Obbligatorio per il funzionamento del modulo. |
| CACHE_NODE_ID                 | GUID ID nodo cache                     | Obbligatoria          | Identifica in modo univoco l'oggetto Microsoft cache connessa<br>da nodo a Ottimizzazione recapito Services.<br>Obbligatorio in ordine<br> per il funzionamento del modulo. |
| CUSTOMER_KEY                  | GUID chiave cliente                     | Obbligatoria          | Si tratta della chiave del cliente, che garantisce sicurezza<br>autenticazione del nodo della cache in Ottimizzazione recapito Services.<br>Obbligatorio per il funzionamento del modulo.|
| STORAGE_ *N* _SIZE_GB           | Dove N è l'unità cache   | Obbligatoria          | Specificare fino a 9 unità per memorizzare nella cache il contenuto e specificare lo spazio massimo in<br>Gigabyte da allocare per il contenuto in ogni unità cache. Esempi:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Il numero dell'unità deve corrispondere ai valori di associazione dell'unità cache specificati<br>nel valore N dell'opzione di creazione del *contenitore* MicrosoftConnectedCache<br>La dimensione minima della cache è 10 GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Facoltativo          | Questo valore può specificare un'istanza upstream connessa a Microsoft<br>Nodo della cache che funge da proxy se cache connessa nodo<br> è disconnesso da Internet. Questa impostazione viene usata per supportare<br> lo scenario IoT annidato.<br>**Nota:** Microsoft cache connessa è in ascolto sulla porta predefinita HTTP 80.|
| UPSTREAM_PROXY                | FQDN/IP:PORTA                           | Facoltativo          | Proxy Internet in uscita.<br>Potrebbe anche trattarsi del proxy della rete perimetrale OT se una rete ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>Nome di dominio completo                        | Facoltativo          | Obbligatorio per supportare i repository di pacchetti personalizzati.<br>I repository possono essere ospitati in locale o su Internet.<br>Non esiste alcun limite al numero di host personalizzati che è possibile configurare.<br><br>Esempi:<br>Name = CACHEABLE_CUSTOM_1_HOST Value = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST Value = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Facoltativo          | Obbligatorio per supportare i repository di pacchetti personalizzati.<br>Questo valore può essere usato come alias e verrà usato dal server di cache per fare riferimento<br>nomi DNS diversi. Ad esempio, il nome host del contenuto del repository packages.foo.com,<br>ma per aree diverse potrebbe essere presente un prefisso aggiuntivo che viene aggiunto al nome host<br>ad esempio westuscdn.packages.foo.com e eastuscdn.packages.foo.com.<br>Impostando l'alias canonico, si garantisce che il contenuto non sia duplicato<br>per il contenuto proveniente dallo stesso host, ma da origini di rete CDN diverse.<br>Il formato del valore canonico non è importante, ma deve essere univoco per l'host.<br>Potrebbe essere più semplice impostare il valore in modo che corrisponda al valore dell'host.<br><br>Esempi basati sugli esempi di host personalizzati precedenti:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL Value = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | true o false                          | Facoltativo          | Consente la visualizzazione del report di riepilogo nella rete locale o in Internet.<br>L'uso di una chiave API (descritta più avanti) è necessario per visualizzare il report di riepilogo se impostato su true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| true o false                          | Facoltativo          | Consente la visualizzazione del report di riepilogo nella rete locale o in Internet senza<br>uso della chiave API da qualsiasi dispositivo nella rete. Usare se non si vuole bloccare l'accesso<br>per visualizzare i dati di riepilogo del server cache tramite il browser. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opzioni di creazione cache connessa Azure IoT Edge del contenitore del modulo Microsoft

Le opzioni di creazione del contenitore per la distribuzione del modulo MCC forniscono il controllo delle impostazioni relative all'archiviazione e alle porte usate dal modulo MCC. Questo è l'elenco delle variabili obbligatorie create dal contenitore usate per distribuire MCC.

### <a name="container-to-host-os-drive-mappings"></a>Contenitore per ospitare i mapping delle unità del sistema operativo

Obbligatorio per eseguire il mapping del percorso di archiviazione del contenitore al percorso di archiviazione nel disco.< è possibile specificare fino a nove posizioni.

>[!Note]
>Il numero dell'unità deve corrispondere ai valori di associazione dell'unità della cache specificati nella variabile di ambiente STORAGE_ *N* _SIZE_GB valore, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Contenitore per ospitare i mapping delle porte TCP

Questa opzione specifica la porta HTTP del computer esterno su cui MCC è in ascolto per le richieste di contenuto. L'hostPort predefinito è la porta 80 e altre porte non sono attualmente supportate perché il client ADU effettua attualmente richieste sulla porta 80. La porta TCP 8081 è la porta interna del contenitore su cui è in ascolto MCC e non può essere modificata.

### <a name="container-service-tcp-port-mappings"></a>Mapping delle porte TCP del servizio contenitore

Il modulo Microsoft cache connessa dispone di un servizio .NET Core, usato dal motore di memorizzazione nella cache per varie funzioni.

>[!Note]
>Per supportare Azure IoT'edge annidato, HostPort non deve essere impostato su 5000 perché il modulo proxy del Registro di sistema è già in ascolto sulla porta host 5000.


Opzioni di creazione del contenitore di esempio

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Report di riepilogo cache connessa Microsoft

Il report di riepilogo è attualmente l'unico modo per un cliente di visualizzare i dati di memorizzazione nella cache per le istanze di Microsoft cache connessa distribuite Azure IoT Edge gateway. Il report viene generato a intervalli di 15 secondi e include statistiche medie per il periodo, nonché statistiche aggregate per la durata del modulo. Le statistiche principali a cui saranno interessati i clienti sono:

* **hitBytes:** somma dei byte recapitati direttamente dalla cache.
* **missBytes:** somma dei byte recapitati che Microsoft cache connessa scaricare dalla rete CDN per visualizzare la cache.
* **eggressBytes:** somma di hitByte e missBytes e rappresenta il totale dei byte recapitati ai client.
* **hitRatioBytes:** rapporto tra hitByte e egressByte.  Se il 100% dei eggressByte recapitati in un periodo fosse uguale agli hitByte, ad esempio, sarebbe 1.


Il report di riepilogo è disponibile in `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` Sostituire \<Azure IoT Edge Gateway IP\> con l'indirizzo IP o il nome host IoT Edge gateway. Per informazioni sulla visibilità di questo report, vedere i dettagli delle variabili di ambiente.