---
title: Risposta precisa con rilevamento dell'intervallo di risposte-QnA Maker
description: Comprendere la funzionalità di risposta precisa disponibile in QnA Maker gestito.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232189"
---
# <a name="precise-answering"></a>Risposta precisa

La funzionalità di risposta precisa introdotta in QnA Maker Managed (Preview) consente di ottenere la risposta breve precisa dal passaggio di risposta migliore al candidato presente nella Knowledge base per qualsiasi query utente. Questa funzionalità Usa un modello di apprendimento avanzato che in fase di esecuzione, che riconosce lo scopo della query utente e rileva la risposta breve precisa dal passaggio di risposta, se è presente una breve risposta presente come fact nel passaggio di risposta. 

Questa funzionalità è abilitata per impostazione predefinita nel riquadro test, in modo da poter testare le funzionalità specifiche dello scenario. Questa funzionalità è estremamente vantaggiosa per gli sviluppatori di contenuti e per gli utenti finali. A questo punto, gli sviluppatori di contenuti non devono curare manualmente specifiche coppie di QnA per ogni fact presente nella Knowledge base e l'utente finale non deve esaminare l'intero passaggio di risposta restituito dal servizio per trovare il fatto che risponde alla query dell'utente. È possibile recuperare [risposte precise tramite l'API genera risposta](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api).

## <a name="precise-answering-on-qna-maker-portal"></a>Risposta precisa al portale di QnA Maker

Quando si apre il riquadro di test nel portale di QnA Maker, viene visualizzata un'opzione per visualizzare la **risposta breve** nella parte superiore. Questa opzione sarà selezionata per impostazione predefinita. Quando si immette una query nel riquadro di test, viene visualizzata una breve risposta insieme al passaggio di risposta, se è presente una breve risposta presente nel passaggio di risposta.
 
![Riquadro del test abilitato per la modalità gestita](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

È possibile deselezionare l'opzione **Visualizza breve risposta** se si desidera visualizzare solo il passaggio di risposta nel riquadro test. 

Il servizio restituisce anche il Punteggio di confidenza della risposta esatta come **Punteggio di intervallo di risposta** che è possibile controllare selezionando l'opzione **Controlla** presente immediatamente sotto la query nel riquadro test.

![Punteggio intervallo di risposta gestito](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Pubblicazione di un bot QnA Maker

Quando si pubblica un bot, per impostazione predefinita si ottiene la risposta esatta abilitata nell'applicazione, in cui si vedrà una breve risposta insieme al passaggio di risposta. Vedere le informazioni di riferimento sulle API per [generate answer](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) per informazioni su come usare la risposta precisa (denominata AnswerSpan) nella risposta. L'utente ha la possibilità di scegliere altre esperienze aggiornando il modello tramite il servizio app bot. 

## <a name="language-support"></a>Lingue supportate

Attualmente la funzionalità di risposta esatta è supportata solo per l'inglese.
