---
title: Configurare la cache connessa Microsoft per l'aggiornamento del dispositivo per l'hub Azure Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Panoramica della cache connessa Microsoft per l'aggiornamento dei dispositivi per l'hub Azure
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663163"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Configurare la cache connessa Microsoft per l'aggiornamento del dispositivo per l'hub Azure

La cache connessa Microsoft viene distribuita in Azure IoT Edge Gateway come modulo di Azure IoT Edge. Analogamente ad altri moduli Azure IoT Edge, le variabili di ambiente di distribuzione del modulo MCC e le opzioni di creazione del contenitore vengono usate per configurare i moduli della cache connessa Microsoft  Questa sezione definisce le variabili di ambiente e le opzioni di creazione del contenitore necessarie per il cliente per distribuire correttamente il modulo Microsoft Connected cache per l'uso da parte dell'aggiornamento del dispositivo per l'hub Azure.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Dettagli sulla distribuzione di Microsoft Connected cache Azure IoT Edge Module

La denominazione del modulo Microsoft Connected cache è a discrezione dell'amministratore. Non sono presenti altre interazioni tra moduli o servizi che si basano sul nome del modulo per la comunicazione. La relazione padre-figlio dei server della cache connessa Microsoft, inoltre, non dipende dal nome del modulo, bensì dal nome di dominio completo o indirizzo IP del gateway Azure IoT Edge che è stato configurato come descritto in precedenza.

Le variabili di ambiente del modulo Microsoft Connected cache Azure IoT Edge vengono usate per passare le informazioni di base sull'identità del modulo e le impostazioni dei moduli funzionali al contenitore.

| Nome variabile                 | Formato del valore                           | Obbligatorio/facoltativo | Funzionalità                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID ID sottoscrizione di Azure             | Necessario          | Si tratta della chiave del cliente, che fornisce sicurezza<br>autenticazione del nodo della cache per l'ottimizzazione del recapito<br>Servizi Obbligatorio per il funzionamento del modulo. |
| CACHE_NODE_ID                 | GUID ID nodo cache                     | Necessario          | Identifica in modo univoco la cache connessa Microsoft<br>da nodo a servizio di ottimizzazione recapito. Obbligatorio nell'ordine<br> per il funzionamento del modulo. |
| CUSTOMER_KEY                  | GUID chiave cliente                     | Necessario          | Si tratta della chiave del cliente, che fornisce sicurezza<br>autenticazione del nodo della cache per i servizi di ottimizzazione recapito.<br>Obbligatorio per il funzionamento del modulo.|
| STORAGE_ *N* _SIZE_GB           | Dove N è il numero di GB necessari   | Necessario          | Specificare fino a nove unità per memorizzare nella cache il contenuto e specificare<br>spazio massimo, in gigabyte, da allocare per il contenuto in ogni unità della cache. Esempi:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Il numero dell'unità deve corrispondere ai valori di binding dell'unità cache specificati<br>nell'opzione di creazione del contenitore MicrosoftConnectedCache *N* value|
| UPSTREAM_HOST                 | FQDN/IP                                | Facoltativo          | Questo valore può specificare un upstream Microsoft connesso<br>Nodo della cache che funge da proxy se il nodo della cache connessa<br> è disconnesso da Internet. Questa impostazione viene utilizzata per supportare<br> scenario di Internet delle cose annidate.<br>**Nota:** La cache connessa Microsoft resta in attesa sulla porta predefinita http 80.|
| UPSTREAM_PROXY                | FQDN/IP: PORTA                           | Facoltativo          | Proxy Internet in uscita.<br>Potrebbe anche essere il proxy di rete perimetrale OT se è presente una rete ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>Nome di dominio completo                        | Facoltativo          | Necessaria per supportare i repository di pacchetti personalizzati.<br>I repository possono essere ospitati localmente o su Internet.<br>Non esiste alcun limite al numero di host personalizzati che è possibile configurare.<br><br>Esempi:<br>Nome = CACHEABLE_CUSTOM_1_HOST valore = packages.foo.com<br> Nome = CACHEABLE_CUSTOM_2_HOST valore = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Facoltativo          | Necessaria per supportare i repository di pacchetti personalizzati.<br>Questo valore può essere usato come alias e verrà usato dal server di cache per fare riferimento a<br>nomi DNS diversi. Ad esempio, il nome host del contenuto del repository può essere packages.foo.com,<br>Tuttavia, per aree diverse potrebbe essere presente un prefisso aggiuntivo aggiunto al nome host<br>come westuscdn.packages.foo.com e eastuscdn.packages.foo.com.<br>Impostando l'alias canonico, si garantisce che il contenuto non sia duplicato<br>per il contenuto proveniente dallo stesso host, ma origini della rete CDN diverse.<br>Il formato del valore canonico non è importante, ma deve essere univoco per l'host.<br>Potrebbe essere più semplice impostare il valore in modo che corrisponda al valore host.<br><br>Esempi basati sugli esempi di host personalizzati precedenti:<br>Nome = CACHEABLE_CUSTOM_1_CANONICAL valore = foopackages<br> Nome = CACHEABLE_CUSTOM_2_CANONICAL valore = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | true o false                          | Facoltativo          | Consente di visualizzare il report di riepilogo sulla rete locale o su Internet.<br>Per visualizzare il report di riepilogo se è impostato su true, è necessario utilizzare una chiave API (discussa più avanti). |
| IS_SUMMARY_ACCESS_UNRESTRICTED| true o false                          | Facoltativo          | Abilita la visualizzazione del report di riepilogo sulla rete locale o su Internet senza<br>uso della chiave API da qualsiasi dispositivo nella rete. Usare se non si vuole bloccare l'accesso<br>per visualizzare i dati di riepilogo del server di cache tramite il browser. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opzioni di creazione del contenitore del modulo Azure IoT Edge della cache connessa Microsoft

Le opzioni di creazione del contenitore per la distribuzione del modulo MCC forniscono il controllo delle impostazioni correlate all'archiviazione e alle porte usate dal modulo MCC. Questo è l'elenco delle variabili di contenitore necessarie usate per distribuire MCC.

### <a name="container-to-host-os-drive-mappings"></a>Da contenitore a host mapping unità del sistema operativo

Obbligatorio per eseguire il mapping del percorso di archiviazione del contenitore al percorso di archiviazione sul disco. è possibile specificare < fino a nove località.

>[!Note]
>Il numero dell'unità deve corrispondere ai valori di binding dell'unità cache specificati nella variabile di ambiente STORAGE_ *N* _SIZE_GB valore, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Contenitore per ospitare i mapping delle porte TCP

Questa opzione specifica la porta http del computer esterno su cui è in ascolto MCC per le richieste di contenuto. Il valore predefinito di Portahost è la porta 80 e altre porte non sono supportate in questo momento perché il client ADU esegue le richieste sulla porta 80 oggi. La porta TCP 8081 è la porta interna del contenitore su cui è in ascolto l'MCC e non può essere modificata.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>Mapping delle porte TCP del servizio contenitore

Il modulo Microsoft Connected cache dispone di un servizio .NET Core, che viene usato dal motore di memorizzazione nella cache per varie funzioni.

>[!Note]
>Per supportare Edge annidati di Azure, Portahost non deve essere impostato su 5000 perché il modulo proxy del registro di sistema è già in ascolto sulla porta host 5000.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Report di riepilogo della cache connessa Microsoft

Il report di riepilogo è attualmente l'unico modo in cui un cliente può visualizzare i dati di caching per le istanze di cache connesse Microsoft distribuite in Azure IoT Edge Gateway. Il report viene generato a intervalli di 15 secondi e include statistiche medie per il periodo, oltre a statistiche aggregate per la durata del modulo. Le statistiche principali a cui i clienti saranno interessati sono:

* **hitBytes** : somma dei byte recapitati provenienti direttamente dalla cache.
* non **sbytes** : questa è la somma dei byte recapitati che Microsoft Connected cache doveva scaricare dalla rete CDN per visualizzare la cache.
* **eggressBytes** : somma di hitBytes e non sbytes ed è il numero totale di byte recapitati ai client.
* **hitRatioBytes** : rapporto tra HitBytes e egressBytes.  Se il 100% dei eggressBytes recapitati in un periodo era uguale al valore di hitBytes, ad esempio è 1.

Il report di riepilogo è disponibile all'indirizzo `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (vedere i dettagli delle variabili di ambiente di seguito per informazioni sulla visibilità di questo report).
