---
title: Informazioni su Servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: I Servizi cognitivi rendono accessibile l'intelligenza artificiale a tutti gli sviluppatori senza richiedere esperienza di Machine Learning e Data Science. È sufficiente eseguire una chiamata API dall'applicazione per aggiungere la possibilità di vedere (ricerca e riconoscimento immagini avanzati), ascoltare, parlare, cercare e prendere decisioni nelle app.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale, riconoscimento cognitivo, funzionalità cognitive
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587613"
---
# <a name="what-are-azure-cognitive-services"></a>Informazioni su Servizi cognitivi di Azure

Servizi cognitivi di Azure sono servizi cloud con API REST ed SDK delle librerie client disponibili per aiutare a integrare l'intelligenza cognitiva nelle applicazioni. È possibile aggiungere funzionalità cognitive nelle applicazioni senza la necessità di competenze di intelligenza artificiale o data science. I Servizi cognitivi di Azure comprendono vari servizi di intelligenza artificiale che consentono di creare soluzioni cognitive in grado di visualizzare, ascoltare, parlare, comprendere e persino prendere decisioni.

## <a name="categories-of-cognitive-services"></a>Categorie di Servizi cognitivi

Il catalogo dei servizi cognitivi che supportano il riconoscimento cognitivo è suddiviso in cinque categorie principali:

* Visione
* Sintesi vocale
* Lingua
* Decisione
* Ricerca

Le sezioni seguenti di questo articolo contengono un elenco dei servizi che fanno parte di questi cinque categorie.

## <a name="vision-apis"></a>API Visione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Visione artificiale](./computer-vision/index.yml "Visione artificiale")|Il servizio Visione artificiale consente di accedere ad algoritmi cognitivi avanzati per l'elaborazione delle immagini e la restituzione di informazioni. Vedere [Visione artificiale di avvio rapido](./computer-vision/quickstarts-sdk/client-library.md) per iniziare a usare il servizio.|
|[Servizio Visione personalizzata](./custom-vision-service/index.yml "Servizio visione artificiale personalizzato")|Il Visione personalizzata consente di compilare, distribuire e migliorare i classificatori di immagini. Un classificatore di immagini è un servizio di intelligenza artificiale che applica etichette alle immagini, in base alle relative caratteristiche visive. |
|[Viso](./face/index.yml "Viso")| Il servizio Viso fornisce l'accesso ad algoritmi viso avanzati consentendo il rilevamento e il riconoscimento degli attributi del viso. Per [iniziare a usare il](./face/quickstarts/client-libraries.md) servizio, vedere Guida introduttiva a Viso.|
|[Riconoscimento modulo](./form-recognizer/index.yml "Riconoscimento modulo")|Il riconoscimento modulo identifica ed estrae le coppie chiave-valore e i dati delle tabelle dai documenti dei moduli, quindi restituisce dati strutturati che includono le relazioni nel file originale. Vedere [riconoscimento modulo introduttiva](./form-recognizer/quickstarts/client-library.md) per iniziare.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer consente di estrarre informazioni dettagliate dai video. Vedere [Video Indexer introduttiva](/azure/media-services/video-indexer/video-indexer-get-started) per iniziare.|

## <a name="speech-apis"></a>Speech API

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Servizio di riconoscimento vocale](./speech-service/index.yml "Servizio Voce")|Il servizio Voce aggiunge funzionalità di riconoscimento vocale alle applicazioni. Il servizio Voce include varie capacità, come la conversione della voce in testo scritto, la sintesi vocale, la traduzione vocale e altre ancora.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>API lingua

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Language Understanding (LUIS)](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) è un servizio di intelligenza artificiale conversazionale basato sul cloud che applica l'intelligenza di Machine Learning al testo in linguaggio naturale della conversazione di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate. [Per iniziare a usare il servizio,](./luis/get-started-portal-build-app.md) vedere l'avvio rapido di LUIS.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker consente di creare una servizio di domande e risposte da contenuto semi-strutturato. [Vedere QnA Maker di avvio](./qnamaker/quickstarts/create-publish-knowledge-base.md) rapido per iniziare a usare il servizio.|
|[Analisi del testo](./text-analytics/index.yml "Text Analytics")| Analisi del testo fornisce l'elaborazione del linguaggio naturale su testo non elaborato per l'analisi del sentiment, l'estrazione delle frasi chiave e il rilevamento della lingua. Vedere [Analisi del testo di avvio rapido](./text-analytics/quickstarts/client-libraries-rest-api.md) per iniziare a usare il servizio.|
|[Translator](./translator/index.yml "Funzione di conversione")|Translator fornisce la traduzione automatica del testo quasi in tempo reale.|
| [Strumento di lettura immersiva](./immersive-reader/index.yml "Strumento di lettura immersiva") | Lo Strumento di lettura immersiva aggiunge capacità di lettura e comprensione dello schermo alle applicazioni. Vedere [strumento di lettura immersiva di avvio](./immersive-reader/quickstarts/client-libraries.md) rapido per iniziare a usare il servizio. |

## <a name="decision-apis"></a>API Decisione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Rilevamento anomalie](./anomaly-detector/index.yml "Rilevamento anomalie") |Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali. Vedere [Rilevamento anomalie di avvio](./anomaly-detector/quickstarts/client-libraries.md) rapido per iniziare a usare il servizio|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator fornisce il monitoraggio per l'individuazione di contenuto potenzialmente offensivo, indesiderato e rischioso. Vedere [Content Moderator introduttiva](./content-moderator/client-libraries.md) per iniziare a usare il servizio.|
|[Advisor metriche](./metrics-advisor/index.yml) (anteprima) | Advisor metriche consente di personalizzare il rilevamento anomalie sui dati di serie temporali con più variabili e offre un portale Web completo per facilitare l'uso del servizio. Vedere [Advisor metriche di avvio rapido](./metrics-advisor/quickstarts/rest-api-and-client-library.md) per iniziare a usare il servizio. |
|[Personalizza esperienze](./personalizer/index.yml "Personalizza esperienze")|Personalizza esperienze consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale. Per [iniziare a usare il servizio,](./personalizer/quickstart-personalizer-sdk.md) vedere Guida introduttiva a Personalizza esperienze.|

## <a name="search-apis"></a>API di ricerca

> [!NOTE]
> [Ricerca cognitiva di Azure](../search/index.yml), pur usando Servizi cognitivi per alcune attività, è una tecnologia di ricerca diversa che supporta altri scenari.

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Ricerca notizie Bing](/azure/cognitive-services/bing-news-search/ "Ricerca notizie Bing")|Ricerca notizie Bing restituisce un elenco di articoli di notizie ritenute pertinenti alla query dell'utente.|
|[Ricerca video Bing](/azure/cognitive-services/Bing-Video-Search/ "Ricerca video Bing")|Ricerca video Bing restituisce un elenco di video ritenuti pertinenti alla query dell'utente.|
|[Ricerca Web Bing](./bing-web-search/index.yml "Ricerca Web Bing")|Ricerca Web Bing restituisce un elenco di risultati di ricerca ritenuti pertinenti alla query dell'utente.|
|[Suggerimenti automatici Bing](/azure/cognitive-services/Bing-Autosuggest "Suggerimenti automatici Bing")|Suggerimenti automatici Bing consente di inviare un termine di query di ricerca parziale a Bing e ottenere un elenco di query.|
|[Ricerca personalizzata Bing](/azure/cognitive-services/bing-custom-search "Ricerca personalizzata Bing")|Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzate per gli argomenti a cui si è interessati.|
|[Ricerca entità Bing](/azure/cognitive-services/bing-entities-search/ "Ricerca entità Bing")|Ricerca entità Bing restituisce informazioni sulle entità ritenute pertinenti alla query dell'utente.|
|[Ricerca immagini Bing](/azure/cognitive-services/bing-image-search "Ricerca immagini Bing")|Ricerca immagini Bing restituisce immagini ritenute pertinenti alla query dell'utente.|
|[Ricerca visiva Bing](/azure/cognitive-services/bing-visual-search "Ricerca visiva Bing")|Ricerca visiva Bing restituisce informazioni dettagliate su un'immagine come immagini somiglianti, fonti per l'acquisto di prodotti individuati nell'immagine e ricerche correlate.|
|[Ricerca attività commerciali locali Bing](/azure/cognitive-services/bing-local-business-search/ "Ricerca attività commerciali locali Bing")| L'API Ricerca attività commerciali locali Bing consente alle applicazioni di trovare informazioni di contatto e sulla posizione di aziende locali in base alle query di ricerca.|
|[Controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/ "Controllo ortografico Bing")|Controllo ortografico Bing consente di eseguire il controllo grammaticale e ortografico.|

## <a name="get-started-with-cognitive-services"></a>Introduzione ai servizi cognitivi

Iniziare creando una risorsa di Servizi cognitivi con le guide di avvio rapido pratiche usando i metodi seguenti:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portale di Azure")
* [Interfaccia della riga di comando di Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "Interfaccia della riga di comando di Azure")
* [Librerie client di Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Modelli di Azure Resource Manager](./create-account-resource-manager-template.md?tabs=portal "Modelli di Azure Resource Manager")

## <a name="using-cognitive-services-in-different-development-environments"></a>Uso di Servizi cognitivi in ambienti di sviluppo diversi

Con Azure e Servizi cognitivi è possibile accedere a diverse opzioni di sviluppo, tra cui:

* Strumenti di automazione e integrazione, ad esempio App per la logica e Power Automate.
* Opzioni di distribuzione come Funzioni di Azure e il Servizio app. 
* Contenitori Docker per Servizi cognitivi per l'accesso sicuro.
* Strumenti come Apache Spark, Azure Databricks, Azure Synapse Analytics e il servizio Azure Kubernetes per gli scenari Big Data. 

Per altre informazioni, vedere [Opzioni di sviluppo per Servizi cognitivi](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Uso sicuro di Servizi cognitivi

Servizi cognitivi di Azure prevede un modello di sicurezza a più livelli, tra cui l'[autenticazione](authentication.md "autenticazione") tramite credenziali di Azure Active Directory, una chiave di risorsa valida e [reti virtuali di Azure](cognitive-services-virtual-networks.md "Reti virtuali di Azure").

## <a name="containers-for-cognitive-services"></a>Contenitori per Servizi cognitivi

 Servizi cognitivi di Azure offre diversi contenitori Docker che consentono di usare le stesse API disponibili in Azure, in locale. L'uso di questi contenitori offre la flessibilità necessaria per avvicinare Servizi cognitivi ai dati per motivi di conformità, sicurezza o altri motivi operativi. Informazioni sui [contenitori di Servizi cognitivi](cognitive-services-container-support.md "Contenitori di Servizi cognitivi").

## <a name="regional-availability"></a>Disponibilità a livello di area

Le API nei Servizi cognitivi sono ospitate in una rete in espansione di centri dati gestiti da Microsoft. È possibile trovare la disponibilità a livello di area per ogni API nell'[elenco delle aree di Azure](https://azure.microsoft.com/regions "Elenco di aree di Azure").

Se si sta cercando un'area non ancora supportata, inviare una richiesta di funzionalità nel [forum di UserVoice](https://cognitive.uservoice.com/ "Forum UserVoice").

## <a name="supported-cultural-languages"></a>Lingue supportate

Servizi cognitivi supporta un'ampia gamma di lingue a livello di servizio. È possibile trovare la disponibilità a livello di lingua per ogni API nell'[elenco delle lingue supportate](language-support.md "elenco delle lingue supportate").

## <a name="certifications-and-compliance"></a>Certificazioni e conformità

I Servizi cognitivi hanno ricevuto certificazioni come CSA STAR, FedRAMP moderato e HIPAA BAA. È possibile [scaricare](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download") le certificazioni per eseguire controlli e verifiche della sicurezza.

Per informazioni sulla privacy e la gestione dei dati, accedere al [Centro protezione](https://servicetrust.microsoft.com/ "Centro protezione").

## <a name="support"></a>Supporto

Servizi cognitivi offre diverse opzioni di supporto che consentono di procedere con la creazione di applicazioni intelligenti. Servizi cognitivi può inoltre contare su una grande community di sviluppatori che possono rispondere a domande specifiche. Per un elenco completo delle opzioni disponibili, vedere [Opzioni di supporto tecnico e assistenza di Servizi cognitivi](cognitive-services-support-options.md "Opzioni di supporto tecnico e assistenza di Servizi cognitivi").

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Servizi cognitivi](cognitive-services-apis-create-account.md "Creare un account di Servizi cognitivi")
* [Novità della documentazione di Servizi cognitivi](whats-new-docs.md "Novità della documentazione di Servizi cognitivi")
* [Pianificare e gestire i costi per Servizi cognitivi](plan-manage-costs.md)
