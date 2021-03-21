---
title: Terminologia e modifiche dell'entità di servizi multimediali V3
description: Questo articolo descrive le differenze terminologiche tra i servizi multimediali di Azure da V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 6f677c8753f09e146d300186e19217568952b417
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705399"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminologia e modifiche di entità tra servizi multimediali V2 e V3

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-2.svg)

Questo articolo descrive le differenze terminologiche tra i servizi multimediali di Azure da V2 a V3.

## <a name="naming-conventions"></a>Convenzioni di denominazione

Esaminare le convenzioni di denominazione applicate alle risorse di servizi multimediali V3. Esaminare anche i [BLOB di denominazione](assets-concept.md#naming)

## <a name="terminology-changes"></a>Modifiche della terminologia

- Un *localizzatore* è ora denominato *localizzatore di streaming*.
- Un *canale* è ora definito *evento Live*.
- Un *programma* è ora denominato *output Live*.
- Un' *attività* è ora denominata *JobOutput*, che fa parte di un processo.

## <a name="entity-changes"></a>Modifiche alle entità
| **V2 (entità)**<!-- row --> | **Entità V3** | **Indicazioni** | **Accessibile a V3** | **Aggiornato da V3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  L'entità `AccessPolicies` non esiste nella V3. | No | No |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Sì | Sì |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Sì | No |
| `AssetFile`<!-- row --> | <!-- empty --> |L'entità `AssetFiles` non esiste nella V3. Sebbene i file (BLOB di archiviazione) caricati siano ancora considerati file.<br/><br/> Usare le API di archiviazione di Azure per enumerare i BLOB in un contenitore. Esistono due modi per applicare una trasformazione ai file con un processo:<br/><br/>File già caricati nello spazio di archiviazione: l'URI include l'ID asset per i processi da eseguire sulle risorse all'interno di un account di archiviazione.<br/><br/>File da caricare durante la trasformazione e il processo di processo: l'asset viene creato nell'archivio, viene restituito un URL di firma di accesso condiviso, i file vengono caricati nella risorsa di archiviazione e quindi la trasformazione viene applicata ai file. | No | No |
| `Channel`<!-- row --> | `LiveEvent` | Gli eventi live sostituiscono i canali dall'API v2. Hanno la maggior parte delle funzionalità e hanno più nuove funzionalità, come le trascrizioni Live, la modalità standby e il supporto per l'inserimento RTMPS. <br/><br/>Visualizza l' [evento live nello streaming live basato sullo scenario](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | No | No |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` non è più un'entità, ora è una proprietà di un localizzatore di streaming.<br/><br/>  In V3, i dati della chiave simmetrica sono associati a `StreamingLocator` (per la crittografia di output) o all'asset stesso (per la crittografia di archiviazione lato client). | Sì | No |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Sì | No |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` sono incluse in `ContentKeyPolicy` . | Sì | No |
| `IngestManifest`<!-- row --> | <!-- empty --> | L'entità `IngestManifests` non esiste nella V3. Il caricamento dei file in V3 comporta l'API di archiviazione di Azure. Gli asset vengono creati per la prima volta e i file vengono caricati nel contenitore di archiviazione associato. Esistono diversi modi per ottenere i dati in un contenitore di archiviazione di Azure che può essere usato in alternativa. `JobInputHttp` fornisce anche un modo per scaricare un input del processo da un URL specificato, se lo si desidera. | No | No |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Esistono diversi modi per ottenere i dati in un contenitore di archiviazione di Azure che può essere usato in alternativa. `JobInputHttp` fornisce anche un modo per scaricare un input del processo da un URL specificato, se lo si desidera. | No | No |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Esistono diversi modi per ottenere i dati in un contenitore di archiviazione di Azure che può essere usato in alternativa. `JobInputHttp` fornisce anche un modo per scaricare un input del processo da un URL specificato, se lo si desidera. | No | No |
| `Job`<!-- row --> | `Job` | Creare un oggetto `Transform` prima di creare un oggetto `Job` . | No | No |
| `JobTemplate`<!-- row --> | `Transform` | Usare invece un oggetto `Transform` . Una trasformazione è un'entità separata da un processo e può essere riutilizzata. | No | No |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Sì | No |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Anziché cercare l'oggetto da `MediaProcessor` usare in base al nome, usare il set di impostazioni desiderato quando si definisce una trasformazione. Il set di impostazioni utilizzato determinerà il processore di contenuti multimediali utilizzato dal sistema di processi. Vedere argomenti sulla codifica in [codifica basata su scenari](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | NA (ReadOnly in v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Le notifiche in V3 vengono gestite tramite griglia di eventi di Azure. Il `NotificationEndpoint` viene sostituito dalla registrazione della sottoscrizione di griglia di eventi che incapsula anche la configurazione per i tipi di notifiche da ricevere, che nella versione V2 è stata gestita dal `JobNotificationSubscription` del processo, dall' `TaskNotificationSubscription` attività e dalla telemetria `ComponentMonitoringSetting` . La telemetria V2 è stata suddivisa tra griglia di eventi di Azure e monitoraggio di Azure per adattarsi ai miglioramenti del più ampio ecosistema di Azure. | No | No |
| `Program`<!-- row --> | `LiveOutput` | Gli output Live ora sostituiscono i programmi nell'API V3.  | No | No |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Gli endpoint di streaming rimangono principalmente uguali. Vengono usati per la creazione dinamica dei pacchetti, la crittografia e il recapito di contenuti HLS e DASH per lo streaming live e on demand, direttamente dall'origine o attraverso la rete CDN. Le nuove funzionalità includono il supporto per ottimizzare l'integrazione e la creazione di grafici di monitoraggio di Azure. |  Sì | Sì |
| `Task`<!-- row --> | `JobOutput` | Sostituito da `JobOutput` (che non è più un'entità separata nell'API).  Vedere argomenti sulla codifica in [codifica basata su scenari](migrate-v-2-v-3-migration-scenario-based-encoding.md). | No | No |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Sostituito da `TransformOutput` (che non è più un'entità separata nell'API). Vedere argomenti sulla codifica in [codifica basata su scenari](migrate-v-2-v-3-migration-scenario-based-encoding.md). | No | No |
| `Inputs`<!-- row --> | `Inputs` | Gli input e gli output sono ora a livello di processo. Vedere argomenti sulla codifica in [codifica basata sullo scenario](migrate-v-2-v-3-migration-scenario-based-encoding.md) | No | No |
| `Outputs`<!-- row --> | `Outputs` | Gli input e gli output sono ora a livello di processo.  In V3, il formato dei metadati è stato modificato da XML a JSON.  Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. Vedere argomenti sulla codifica in [codifica basata sullo scenario](migrate-v-2-v-3-migration-scenario-based-encoding.md) | No | No |


| **Altre modifiche** | **V2**  | **V3** |
|---|---|---|
| **Archiviazione** <!--new row --> |||
| Archiviazione: <!--new row --> | | Gli SDK V3 sono ora disaccoppiati da Storage SDK, che offre un maggiore controllo sulla versione di storage SDK che si vuole usare ed evita i problemi di controllo delle versioni.                      |
| **Encoding** <!--new row --> |||
| Velocità in bit di codifica <!--new row --> | velocità in bit misurate in Kbps, ad esempio: 128 (Kbps)| bit al secondo, ad esempio: 128000 (bit/secondo)|
| Codifica di DRM FairPlay <!--new row --> | In servizi multimediali V2 è possibile specificare il vettore di inizializzazione (IV). | In servizi multimediali V3 non è possibile specificare FairPlay IV.|
| Codificatore Premium <!--new row --> | Codificatore Premium e indicizzatore legacy| Il [codificatore Premium](../previous/media-services-encode-asset.md) e i [processori legacy di analisi media](../previous/legacy-components.md) (Azure Media Services Indexer 2 Preview, visi redactor e così via) non sono accessibili tramite V3. È stato aggiunto il supporto per il mapping del canale audio al codificatore standard.  Vedere [audio nella documentazione di spavalderia per la codifica di servizi multimediali](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Vedere argomenti sulla codifica in [codifica basata sullo scenario](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Trasformazioni e processi** <!--new row -->|||
| Elaborazione basata su processo HTTPS <!--new row --> |<!-- empty -->| Per l'elaborazione di processi basati su file, è possibile usare un URL HTTPS come input. Non è necessario che il contenuto sia già archiviato in Azure, né è necessario creare asset. |
| Modelli ARM per i processi <!--new row --> | I modelli ARM non esistono nella versione V2. | Una trasformazione può essere usata per compilare configurazioni riutilizzabili, per creare modelli di Azure Resource Manager e isolare le impostazioni di elaborazione tra più clienti o tenant. |
| **Eventi Live** <!--new row --> |||
| endpoint di streaming <!--new row --> | Un endpoint di streaming rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione lettore client o a una rete per la distribuzione di contenuti (CDN) per un'ulteriore distribuzione. | Gli endpoint di streaming rimangono principalmente uguali. Vengono usati per la creazione dinamica dei pacchetti, la crittografia e il recapito di contenuti HLS e DASH per lo streaming live e on demand, direttamente dall'origine o attraverso la rete CDN.  Le nuove funzionalità includono il supporto per ottimizzare l'integrazione e la creazione di grafici di monitoraggio di Azure. |
| Canali eventi attivi <!--new row --> | I canali sono responsabili dell'elaborazione di contenuto in streaming live. Un'entità Channel, o canale, fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito al transcodificatore live. Il canale riceve i flussi di input Live dal transcodificatore Live e li rende disponibili per lo streaming tramite uno o più endpoint di streaming. I canali forniscono anche un endpoint di anteprima(URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.| Gli eventi live sostituiscono i canali dall'API v2. Hanno la maggior parte delle funzionalità e hanno più nuove funzionalità, come le trascrizioni Live, la modalità standby e il supporto per l'inserimento RTMPS. |
| Programmi evento Live <!--new row --> | Il programma consente di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale. È possibile specificare il numero di ore per cui si desidera memorizzare il contenuto registrato per il programma impostando la `ArchiveWindowLength` Proprietà. Il valore impostato può essere compreso tra 5 minuti e 25 ore.| Gli output Live ora sostituiscono i programmi nell'API V3. |
| Lunghezza evento Live <!--new row --> |<!-- empty -->| È possibile trasmettere gli eventi Live 24/7 quando si usa servizi multimediali per la transcodifica di un feed di contributo a bitrate singolo in un flusso di output con più velocità in bit.|
| Latenza eventi in tempo reale <!--new row --> |<!-- empty -->| Nuovo supporto per lo streaming live a bassa latenza per gli eventi live. |
| Anteprima evento Live <!--new row --> |<!-- empty -->| L'anteprima degli eventi live supporta la creazione dinamica dei pacchetti e la crittografia dinamica. Questo consente la protezione del contenuto nell'anteprima, nonché la creazione di pacchetti DASH e HLS. |
| Evento Live RTMPS <!--new row --> |<!-- empty-->| Miglioramento del supporto per RTMPS con maggiore stabilità e maggiore supporto del codificatore di origine. |
| Inserimento sicuro RTMPS evento Live <!--new row --> | | Quando si crea un evento Live, si ottengono 4 URL di inserimento. I 4 URL di inserimento sono quasi identici, hanno lo stesso token di flusso `AppId` , ma solo la parte relativa al numero di porta è diversa. Due URL sono primari e due sono di backup per RTMPS.| 
| Trascrizione di eventi Live <!--new row --> |<!-- empty--> | Servizi multimediali di Azure offre video, audio e testo in protocolli diversi. Quando si pubblica il flusso live con MPEG-DASH o HLS/CMAF, insieme a video e audio, il servizio recapita il testo trascritto in TTML compatibile con IMSC 1.1.|
| Modalità standby eventi Live <!--new row --> | Nessuna modalità standby per la versione V2. | La modalità standby è una nuova funzionalità V3 che consente di gestire i pool di eventi attivi. I clienti possono ora avviare un evento Live in modalità stand-by a costi ridotti prima di passare allo stato Running. In questo modo è possibile migliorare l'ora di inizio del canale e ridurre i costi per i pool di lavoro più veloci. |
| Fatturazione eventi in tempo reale <!--new row --> | <!-- empty-->| La fatturazione di eventi live si basa su contatori di canale Live. |
| Output Live <!--new row --> | È stato necessario avviare i programmi dopo la creazione. | Gli output live iniziano al momento della creazione e terminano quando vengono eliminati. |

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]