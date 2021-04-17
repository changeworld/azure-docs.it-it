---
title: Usare Servizi cognitivi di Azure contenitori locali
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i contenitori Docker per usare Servizi cognitivi in locale.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: locale, Docker, contenitore, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588225"
---
# <a name="azure-cognitive-services-containers"></a>Contenitori di Servizi cognitivi di Azure

Servizi cognitivi di Azure offre diversi [contenitori Docker](https://www.docker.com/what-container) che consentono di usare le stesse API disponibili in Azure, in locale. L'uso di questi contenitori offre la flessibilità necessaria per avvicinare Servizi cognitivi ai dati per motivi di conformità, sicurezza o altri motivi operativi. Il supporto dei contenitori è attualmente disponibile per un subset di Servizi cognitivi di Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio, incluse le relative dipendenze e configurazione, viene compresso come immagine del contenitore. L'immagine del contenitore può essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine. Tali istanze possono essere rimosse quando non sono più necessarie.

## <a name="features-and-benefits"></a>Funzionalità e vantaggi

- **Infrastruttura non modificabile:** consente ai team DevOps di sfruttare un set coerente e affidabile di parametri di sistema noti, pur essendo in grado di adattarsi alle modifiche. I contenitori offrono la flessibilità necessaria per eseguire pivot all'interno di un ecosistema prevedibile ed evitare la deriva della configurazione.
- **Controllo sui dati:** scegliere dove i dati vengono elaborati da Servizi cognitivi. Questo può essere essenziale se non è possibile inviare dati al cloud, ma è necessario accedere a API Servizi cognitivi. È supportata la coerenza in ambienti ibridi, tra i dati e le funzionalità di gestione, identità e sicurezza.
- **Controllo degli aggiornamenti dei modelli:** flessibilità nel controllo delle versioni e nell'aggiornamento dei modelli distribuiti nelle soluzioni.
- **Architettura portabile:** consente la creazione di un'architettura di applicazione portabile che può essere distribuita in Azure, in locale e nei dispositivi perimetrali. I contenitori possono essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml), in [Istanze di Azure Container](../container-instances/index.yml) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure-stack/operator). Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuire Kubernetes in Azure Stack).
- **Velocità effettiva elevata/bassa latenza:** offrire ai clienti la possibilità di scalare per una velocità effettiva elevata e requisiti di bassa latenza consentendo a Servizi cognitivi di essere eseguiti fisicamente vicino alla logica e ai dati dell'applicazione. I contenitori non raggiungono il limite di transazioni al secondo e possono offrire scalabilità sia orizzontale che verticale per gestire la domanda, se si forniscono le risorse hardware necessarie.
- **Scalabilità:** con la sempre maggiore diffusione del software di containerizzazione e orchestrazione dei contenitori, ad esempio Kubernetes; La scalabilità è all'avanguardia dei miglioramenti tecnologici. Sulla base di un cluster scalabile, lo sviluppo di applicazioni si adatta alla disponibilità elevata.

## <a name="containers-in-azure-cognitive-services"></a>Contenitori in Servizi cognitivi di Azure

Servizi cognitivi di Azure contenitori forniscono il set seguente di contenitori Docker, ognuno dei quali contiene un subset di funzionalità dei servizi in Servizi cognitivi di Azure. Le istruzioni e i percorsi delle immagini sono disponibili nelle tabelle seguenti. È disponibile anche [un elenco di](containers/container-image-tags.md) immagini del contenitore.

### <a name="decision-containers"></a>Contenitori di decisioni

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [Rilevamento anomalie][ad-containers] | **Rilevamento anomalie** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | L'API Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali con l'apprendimento automatico. | Disponibile a livello generale |

### <a name="language-containers"></a>Contenitori per la lingua

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([immagine](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Carica un modello Language Understanding sottoposto a training o pubblicato, noto anche come app LUIS, in un contenitore Docker e fornisce l'accesso alle stime di query dagli endpoint dell'API del contenitore. È possibile raccogliere i log di query dal contenitore e caricarli nel [portale LUIS](https://www.luis.ai) per migliorare l'accuratezza delle stime dell'app. | Disponibile a livello generale |
| [Analisi del testo][ta-containers-keyphrase] | **Estrazione frasi chiave** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Estrae le frasi chiave per identificare i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". | Anteprima |
| [Analisi del testo][ta-containers-language] |  **Testo Rilevamento lingua** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Per un massimo di 120 lingue, rileva la lingua in cui è scritto il testo di input e crea un report relativo a un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. | Disponibile a livello generale |
| [Analisi del testo][ta-containers-sentiment] | **Analisi del sentiment v3** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analizza testo non elaborato per indicazioni su una valutazione positiva o negativa. Questa versione dell'analisi del sentiment restituisce le etichette del sentiment (ad esempio *positive* o *negative)* per ogni documento e frase al suo interno. |  Disponibile a livello generale |
| [Analisi del testo][ta-containers-health] |  **Analisi del testo per la sanità** | Estrarre ed etichettare le informazioni cliniche da testo non strutturato. | Anteprima con gate. [Richiedere l'accesso][request-access]a . |

### <a name="speech-containers"></a>Contenitori per la voce

> [!NOTE]
> Per usare i contenitori voce, è necessario completare un [modulo di richiesta online.](https://aka.ms/csgate)

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|
| [API servizio Voce][sp-containers-stt] |  **Riconoscimento vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Esegue la trascrizione del parlato continuo in tempo reale in testo. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-cstt] | **Riconoscimento vocale personalizzato da testo a testo** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Trascrive il parlato continuo in tempo reale in testo usando un modello personalizzato. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-tts] | **Sintesi vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converte il testo scritto in un audio che suona naturale. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-ctts] | **Sintesi vocale personalizzata** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Converte il testo in sintesi vocale naturale usando un modello personalizzato. | Anteprima recisa |
| [API servizio Voce][sp-containers-ntts] | **Sintesi vocale neurale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Converte il testo in parlato dal suono naturale usando la tecnologia di rete neurale profonda, consentendo una sintesi vocale più naturale. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-lid] | **Rilevamento della lingua del riconoscimento vocale** [(immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Determina la lingua dell'audio parlato. | Anteprima con controllo |

### <a name="vision-containers"></a>Contenitori per la visione

> [!WARNING]
> L'11 giugno 2020 Microsoft ha annunciato che non venderà la tecnologia di riconoscimento facciale ai dipartimenti di polizia degli Stati Uniti fino a quando non verranno adottate norme rigorose, che tengano conto dei diritti umani. Di conseguenza, i clienti non possono usare le funzionalità di riconoscimento facciale o le funzionalità incluse in Servizi di Azure, ad esempio Viso o Video Indexer, se il cliente è un dipartimento di polizia degli Stati Uniti o consente l'uso di tali servizi a tale dipartimento.

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [Visione artificiale][cv-containers] | **Lettura OCR** [(immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Il contenitore Read OCR consente di estrarre testo stampato e scritto a mano da immagini e documenti con supporto per i formati di file JPEG, PNG, BMP, PDF e TIFF. Per altre informazioni, vedere la documentazione [dell'API Read.](./computer-vision/overview-ocr.md) | Anteprima con controllo. [Richiedere l'accesso a][request-access]. |
| [Analisi spaziale][spa-containers] | **Analisi spaziale** [(immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analizza i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone, il loro movimento e le interazioni con gli oggetti in ambienti fisici. | Anteprima con controllo. [Richiedere l'accesso a][request-access]. |
| [Viso][fa-containers] | **Viso** | Rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche facciali con previsioni basate su computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure può confrontare i visi con un database per verificare se esistono già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise. | Non disponibile |
| [Riconoscimento modulo][fr-containers] | **Riconoscimento modulo** | Form Understanding applica la tecnologia di Machine Learning per identificare ed estrarre coppie chiave-valore e tabelle dai moduli. | Non disponibile | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Inoltre, alcuni contenitori sono supportati nell'offerta di risorse [multi-servizio di Servizi cognitivi.](cognitive-services-apis-create-account.md) È possibile creare una singola risorsa All-In-One di Servizi cognitivi e usare la stessa chiave di fatturazione nei servizi supportati per i servizi seguenti:

* Visione artificiale
* Viso
* LUIS
* Analisi del testo

## <a name="prerequisites"></a>Prerequisiti

Per usare i contenitori di Servizi cognitivi di Azure, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato localmente. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml) o in [Istanze di Azure Container](../container-instances/index.yml).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro contenitori di Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro contenitori di Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

Singoli contenitori possono prevedere anche requisiti specifici, tra cui quelli relativi a server e allocazione di memoria.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Esempi per gli sviluppatori

Gli esempi per gli sviluppatori sono disponibili nel [repository GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [ricette dei contenitori](containers/container-reuse-recipe.md) che è possibile usare con Servizi cognitivi.

Installare ed esplorare le funzionalità fornite dai contenitori in Servizi cognitivi di Azure:

* [Rilevamento anomalie contenitori][ad-containers]
* [Visione artificiale contenitori][cv-containers]
* [Contenitori di viso][fa-containers]
* [riconoscimento modulo contenitori][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [Contenitori dell'API del servizio Voce][sp-containers]
* [Analisi del testo contenitori][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://aka.ms/csgate
