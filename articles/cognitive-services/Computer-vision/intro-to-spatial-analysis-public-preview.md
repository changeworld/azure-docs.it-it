---
title: Che cos'è l'analisi spaziale?
titleSuffix: Azure Cognitive Services
description: In questo documento vengono illustrati i concetti e le funzionalità di base di un contenitore di analisi spaziale Visione artificiale.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284543"
---
# <a name="what-is-spatial-analysis"></a>Che cos'è l'analisi spaziale?

Il servizio di analisi spaziale consente alle organizzazioni di massimizzare il valore dei propri spazi fisici comprendendo i movimenti e la presenza di persone all'interno di una determinata area. Consente di inserire video da telecamere TVCC o di sorveglianza, eseguire operazioni di intelligenza artificiale per estrarre informazioni dettagliate dai flussi video e generare eventi che verranno usati da altri sistemi. Con l'input da un flusso della fotocamera, un'operazione di intelligenza artificiale può eseguire operazioni come il conteggio del numero di persone che entrano in uno spazio o la misurazione della conformità con le linee guida per la maschera e l'allontanamento

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Funzione

Le principali operazioni di analisi spaziale sono tutte basate su una pipeline che inserisce video, rileva le persone nel video, tiene traccia delle persone mentre si spostano nel tempo e genera eventi quando gli utenti interagiscono con le aree di interesse.

## <a name="spatial-analysis-features"></a>Funzionalità di analisi spaziale

| Funzionalità | Definizione |
|------|------------|
| **Rilevamento persone** | Questo componente risponde alla domanda "dove sono le persone in questa immagine?" Trova le persone in un'immagine e passa un rettangolo di delimitazione che indica la posizione di ogni persona al componente di rilevamento persone. |
| **Rilevamento persone** | Questo componente connette i rilevamenti degli utenti nel tempo mentre gli utenti si spostano davanti a una fotocamera. Usa la logica temporale sul modo in cui le persone si spostano in genere e le informazioni di base sull'aspetto generale delle persone. Non tiene traccia delle persone tra più fotocamere. Se una persona esce dal campo di visualizzazione per più di circa un minuto e quindi immette nuovamente la visualizzazione della fotocamera, il sistema lo percepirà come una nuova persona. Il rilevamento utenti non identifica in modo univoco gli utenti nelle fotocamere. Non usa il riconoscimento facciale o il rilevamento dell'andamento. |
| **Rilevamento maschera viso** | Questo componente rileva la posizione della faccia di una persona nel campo di visualizzazione della fotocamera e identifica la presenza di una maschera faccia. L'operazione di intelligenza artificiale analizza le immagini dal video; Quando viene rilevata una faccia, il servizio fornisce un rettangolo di delimitazione intorno alla faccia. Utilizzando le funzionalità di rilevamento oggetti, viene identificata la presenza di maschere facciali all'interno del rettangolo di delimitazione. Il rilevamento viso maschera non implica la distinzione di una faccia da un'altra faccia, la stima o la classificazione degli attributi facciali o l'esecuzione del riconoscimento facciale. |
| **Area di interesse** | Si tratta di una zona o di una linea definita dall'utente nel fotogramma video di input. Quando una persona interagisce con questa area nel video, il sistema genera un evento. Per l'operazione PersonCrossingLine, ad esempio, viene definita una riga nel video. Quando un utente incrocia la riga, viene generato un evento. |
| **Event** | Un evento è l'output primario dell'analisi spaziale. Ogni operazione genera un evento specifico periodicamente, ad esempio una volta al minuto, oppure ogni volta che si verifica un trigger specifico. L'evento include informazioni su ciò che si è verificato nel video di input, ma non include immagini o video. L'operazione PeopleCount, ad esempio, può generare un evento contenente il conteggio aggiornato ogni volta che il conteggio delle persone cambia (trigger) o ogni minuto (periodicamente). |

## <a name="get-started"></a>Introduzione

### <a name="public-preview-gating"></a>Controllo anteprima pubblica

Per garantire che l'analisi spaziale venga utilizzata per gli scenari per cui è stata progettata, questa tecnologia viene messa a disposizione dei clienti tramite un processo dell'applicazione. Per ottenere l'accesso all'analisi spaziale, è necessario iniziare compilando il modulo di assunzione online. [Avviare l'applicazione qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

L'accesso alla versione di anteprima pubblica dell'analisi spaziale è soggetto alla esclusiva discrezione di Microsoft, in base ai criteri di idoneità, al processo di vagliatura e alla disponibilità per supportare un numero limitato di clienti durante questa anteprima gestita. Nella versione di anteprima pubblica, i clienti che hanno una relazione significativa con Microsoft sono interessati a collaborare con Microsoft nei casi d'uso consigliati e a scenari aggiuntivi che rispettano i responsabili degli impegni di intelligenza artificiale.

### <a name="follow-a-quickstart"></a>Segui una guida introduttiva

Quando viene concesso l'accesso all'analisi spaziale, seguire la [Guida introduttiva](spatial-analysis-container.md) per configurare il contenitore e iniziare l'analisi del video.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilizzo responsabile della tecnologia di analisi spaziale

Per informazioni sull'utilizzo responsabile della tecnologia di analisi spaziale, vedere la [Nota sulla trasparenza](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Le note sulla trasparenza di Microsoft sono destinate a comprendere il funzionamento della tecnologia di intelligenza artificiale, le scelte che i proprietari del sistema possono influenzare sulle prestazioni e sul comportamento del sistema e sull'importanza di considerare l'intero sistema, tra cui la tecnologia, le persone e l'ambiente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: contenitore di analisi spaziale](spatial-analysis-container.md)'''''''''''