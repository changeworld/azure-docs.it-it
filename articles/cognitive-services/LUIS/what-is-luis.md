---
title: Language Understanding (LUIS) Overview
description: Language Understanding (LUIS) è un servizio API basato sul cloud che applica il Machine Learning al linguaggio naturale delle conversazioni per stimare il significato ed estrarre le informazioni.
keywords: Azure, intelligenza artificiale, ia, elaborazione del linguaggio naturale, nlp, comprensione del linguaggio naturale, nlu, LUIS, IA conversazionale, chatbot di ia, ia nlp, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503771"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) è un servizio di intelligenza artificiale conversazionale basato sul cloud che applica l'intelligenza di Machine Learning al testo in linguaggio naturale della conversazione di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate. LUIS fornisce l'accesso tramite il [portale personalizzato,](https://www.luis.ai) [le API e][endpoint-apis] le [librerie client SDK.](client-libraries-rest-api.md)

Per la prima volta gli utenti, seguire questa procedura per accedere al portale [LUIS](sign-in-luis-portal.md "accedere al portale LUIS") Per iniziare, è possibile provare le app di dominio predefinite di [LUIS](luis-get-started-create-app.md) oppure è possibile compilare [l'app](get-started-portal-build-app.md).

Questa documentazione contiene i tipi di articolo seguenti:  

* [**Le guide introduttive**](luis-get-started-create-app.md) sono istruzioni introduttive che guidano l'utente nell'esecuzione di richieste al servizio.  
* [**Le guide dettagliate**](luis-how-to-start-new-app.md) contengono istruzioni per l'uso del servizio in modi più specifici o personalizzati.  
* [**I**](artificial-intelligence.md) concetti forniscono spiegazioni approfondite delle funzionalità e delle funzionalità del servizio.  
* [**Le esercitazioni**](tutorial-intents-only.md) sono guide più lunghe che illustrano come usare il servizio come componente in soluzioni aziendali più ampie.  

## <a name="what-does-luis-offer"></a>Cosa offre LUIS 

* **Semplicità:** LUIS scarica l'utente dalla necessità di competenze di intelligenza artificiale all'interno o da qualsiasi conoscenza di Machine Learning precedente. Con pochi clic è possibile creare un'applicazione di intelligenza artificiale conversazionale. È possibile compilare l'applicazione personalizzata seguendo una delle guide [introduttive](get-started-portal-build-app.md)oppure è possibile usare una delle [app di dominio](luis-get-started-create-app.md) predefinite.
* **Sicurezza, privacy e conformità:** supportato dall'infrastruttura di Azure, LUIS offre sicurezza, privacy e conformità di livello aziendale. I dati rimangono personali. è possibile eliminare i dati in qualsiasi momento. I dati vengono crittografati mentre sono in archiviazione. Per altre informazioni a questo proposito, vedere [qui](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integrazione:** integrare facilmente l'app LUIS con altri servizi Microsoft [come Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)e il [servizio Voce.](../Speech-Service/quickstarts/intent-recognition.md)


## <a name="luis-scenarios"></a>Scenari LUIS
* [Creare un bot conversazionale](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot)di livello aziendale: questa architettura di riferimento descrive come creare un bot conversazionale di livello aziendale (chatbot) usando azure Bot Framework.
* [Commerce Chatbot:](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot)insieme, il servizio servizio Azure Bot e Language Understanding consente agli sviluppatori di creare interfacce di conversazione per vari scenari, ad esempio servizi bancari, viaggi e intrattenimento.
* [Controllo dei dispositivi IoT](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)con un assistente vocale: creare interfacce di conversazione senza problemi con tutti i dispositivi accessibili da Internet, dal telefono connesso o dai dispositivi in una centrale elettrica connessa.


## <a name="application-development-life-cycle"></a>Ciclo di vita dello sviluppo di applicazioni

![Ciclo di vita dello sviluppo di app LUIS](./media/luis-overview/luis-dev-lifecycle.png "Ciclo di vita dell'applicazione LUIS")

-   **Piano:** identificare gli scenari per cui gli utenti potrebbero usare l'applicazione. Definire le azioni e le informazioni pertinenti che devono essere riconosciute.
-   **Compilazione:** usare la risorsa di creazione per sviluppare l'app. Iniziare definendo [finalità](luis-concept-intent.md) [ed entità](luis-concept-entity-types.md). Aggiungere quindi espressioni di training [per](luis-concept-utterance.md) ogni finalità. 
-   **Test e miglioramento:** iniziare a testare il modello con altre espressioni per avere un'idea del comportamento dell'app ed è possibile decidere se sono necessari miglioramenti. È possibile migliorare l'applicazione seguendo queste [procedure consigliate.](luis-concept-best-practices.md) 
-   **Pubblica:** distribuire l'app per la stima ed eseguire query sull'endpoint usando la risorsa di stima. Altre informazioni sulle risorse di creazione e previsione [sono disponibili qui.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Connect:** connettersi ad altri servizi, ad [esempio Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)e il [servizio Voce.](../Speech-Service/quickstarts/intent-recognition.md) 
-   **Affinare:** [esaminare le espressioni endpoint per](luis-concept-review-endpoint-utterances.md) migliorare l'applicazione con esempi reali

Per altre informazioni sulla pianificazione e la compilazione dell'applicazione, [vedere](luis-how-plan-your-app.md).

## <a name="next-steps"></a>Passaggi successivi

* [Novità](whats-new.md "Novità") del servizio e documentazione
* [Creare un'app LUIS](tutorial-intents-only.md)
* [Informazioni di riferimento sulle API][endpoint-apis]
* [Procedure consigliate](luis-concept-best-practices.md)
* [Risorse per sviluppatori](developer-reference-resource.md "Risorse per sviluppatori") per LUIS.
* [Pianificare l'app](luis-how-plan-your-app.md "Pianificare l'app") con [finalità](luis-concept-intent.md "intents") ed [entità](luis-concept-entity-types.md "entities").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
