---
title: Associazione di servizi cognitivi a un skillt
titleSuffix: Azure Cognitive Search
description: Informazioni su come alleghi una sottoscrizione all-in-One di servizi cognitivi a una pipeline di arricchimento di intelligenza artificiale in Azure ricerca cognitiva.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577935"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Alleghi una risorsa di servizi cognitivi a un Skills in Azure ricerca cognitiva

Quando si configura una [pipeline di arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) in Azure ricerca cognitiva, è possibile arricchire un numero limitato di documenti gratuitamente. Per carichi di lavoro più grandi e più frequenti, è necessario alleghi una risorsa di servizi cognitivi "All-in-One" fatturabile. Una sottoscrizione "All-in-One" fa riferimento a "Servizi cognitivi" come offerta, anziché singoli servizi, con accesso concesso tramite una singola chiave API.

Una risorsa di servizi cognitivi "All-in-One" determina le [competenze predefinite](cognitive-search-predefined-skills.md) che è possibile includere in un insieme di competenze:

+ [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi delle immagini e il riconoscimento ottico dei caratteri (OCR)
+ [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per il rilevamento della lingua, il riconoscimento delle entità, l'analisi dei sentimenti e l'estrazione di frasi chiave
+ [Traduzione testuale](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Una chiave di servizi cognitivi "All-in-One" è facoltativa in una definizione di competenze. Quando il numero di transazioni giornaliere è inferiore a 20 al giorno, il costo viene assorbito. Tuttavia, quando le transazioni superano tale numero, è necessario disporre di una chiave di risorsa valida per poter continuare l'elaborazione.

La chiave di risorsa "All-in-One" è valida. Internamente, un servizio di ricerca userà la risorsa che si trova nella stessa area fisica, anche se la chiave "All-in-One" è per una risorsa in un'area diversa. La pagina relativa alla [disponibilità del prodotto](https://azure.microsoft.com/global-infrastructure/services/?products=search) Mostra la disponibilità locale side-by-side.

> [!NOTE]
> Se si ometteranno le competenze predefinite in un determinato ambito, non sarà possibile accedere ai servizi cognitivi e non verrà addebitato alcun costo, anche se il skillt specifica una chiave.

## <a name="how-billing-works"></a>Modalità di funzionamento della fatturazione

+ Azure ricerca cognitiva usa la chiave di risorsa Servizi cognitivi fornita in un competenze per la fatturazione per l'arricchimento di immagini e testo. L'esecuzione di competenze fatturabili è il [prezzo con pagamento in base al consumo di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ L'estrazione di immagini è un'operazione di ricerca cognitiva di Azure che si verifica quando i documenti vengono incrinati prima dell'arricchimento. L'estrazione delle immagini è fatturabile. Per i prezzi di estrazione delle immagini, vedere la [pagina dei prezzi di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/).

+ L'estrazione del testo si verifica anche durante la frase di cracking del documento. Non è fatturabile.

+ Le competenze che non chiamano servizi cognitivi, incluse le competenze condizionali, di modellazione, Unione del testo e suddivisione del testo, non sono fatturabili.

## <a name="same-region-requirement"></a>Requisito della stessa area

I servizi cognitivi e ricerca cognitiva devono esistere all'interno della stessa area fisica, come indicato nella pagina relativa alla [disponibilità del prodotto](https://azure.microsoft.com/global-infrastructure/services/?products=search) . La maggior parte delle aree che offrono ricerca cognitiva offrono anche servizi cognitivi.

Se si prova a arricchire l'intelligenza artificiale nell'area che non include entrambi i servizi, verrà visualizzato questo messaggio: "la chiave specificata non è una chiave di tipo CognitiveServices valida per l'area del servizio di ricerca."

> [!NOTE]
> Alcune competenze predefinite sono basate su servizi cognitivi non locali (ad esempio, la capacità di [traduzione del testo](cognitive-search-skill-text-translation.md)). L'uso di una competenza non regionale significa che la richiesta può essere gestita in un'area diversa dall'area ricerca cognitiva di Azure. Per ulteriori informazioni sui servizi non regionali, vedere la pagina relativa al [prodotto in base all'area di servizi cognitivi](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Usare risorse gratuite

È possibile usare un'opzione di elaborazione gratuita limitata per completare l'esercitazione di arricchimento di intelligenza artificiale e gli esercizi di avvio rapido.

Le risorse gratuite (arricchimenti limitati) sono limitate a 20 documenti al giorno, per ogni indicizzatore. È possibile [reimpostare l'indicizzatore](search-howto-run-reset-indexers.md) per reimpostare il contatore.

Se si usa la procedura guidata **Importa dati** per l'arricchimento ai, sono disponibili le opzioni "Connetti Servizi cognitivi" nella pagina **Aggiungi arricchimento ai (facoltativo)** .

![Sezione Servizi cognitivi allargati collegati](./media/cognitive-search-attach-cognitive-services/attach1.png "Sezione Servizi cognitivi allargati collegati")

## <a name="use-billable-resources"></a>Usare risorse fatturabili

Per i carichi di lavoro che creano più di 20 arricchimenti al giorno, assicurarsi di alleghi una risorsa Servizi cognitivi fatturabile. Si consiglia di alleghi sempre una risorsa di servizi cognitivi fatturabile, anche se non si intende mai chiamare API Servizi cognitivi. Il fissaggio di una risorsa sostituisce il limite giornaliero.

Vengono addebitate solo le competenze che chiamano il API Servizi cognitivi. Non vengono addebitate le [competenze personalizzate](cognitive-search-create-custom-skill-example.md)o le competenze come la [fusione del testo](cognitive-search-skill-textmerger.md), la [barra di divisione del testo](cognitive-search-skill-textsplit.md)e lo [shaper](cognitive-search-skill-shaper.md), che non sono basati su API.

Se si usa la procedura guidata di **importazione dei dati** , è possibile configurare una risorsa fatturabile dalla pagina **Aggiungi arricchimento ai (facoltativo)** .

1. Espandere **Connetti Servizi cognitivi** e quindi selezionare **Crea nuova risorsa Servizi cognitivi**. Viene visualizzata una nuova scheda in cui è possibile creare la risorsa:

   ![Creare una risorsa Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Creare una risorsa per Servizi cognitivi")

1. Nell'elenco **location (percorso** ) selezionare la stessa area in cui è presente il servizio di ricerca.

1. Nell'elenco piano **tariffario** selezionare **S0** per ottenere la raccolta All-in-One delle funzionalità di servizi cognitivi, incluse le funzionalità di visione e linguaggio che riportano le competenze predefinite fornite da Azure ricerca cognitiva.

   Per il livello S0, è possibile trovare le tariffe per carichi di lavoro specifici nella [pagina dei prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Nell'elenco **Seleziona offerta** verificare che **Servizi cognitivi** sia selezionato.
   + In funzionalità del **linguaggio** le tariffe per **analisi del testo standard** si applicano all'indicizzazione ai.
   + In funzionalità di **visione** , vengono applicate le tariffe per **visione artificiale S1** .

1. Selezionare **Crea** per eseguire il provisioning della nuova risorsa Servizi cognitivi.

1. Tornare alla scheda precedente. Selezionare **Aggiorna** per visualizzare la risorsa Servizi cognitivi e quindi selezionare la risorsa:

   ![Selezionare la risorsa Servizi cognitivi](./media/cognitive-search-attach-cognitive-services/attach2.png "Selezionare la risorsa Servizi cognitivi")

1. Espandere la sezione **Aggiungi competenze cognitive** per selezionare le competenze cognitive specifiche che si desidera eseguire sui dati. Completare il resto della procedura guidata.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Collegare un set di competenze esistente a una risorsa Servizi cognitivi

Se si ha un set di competenze esistenti, è possibile collegarlo a una risorsa Servizi cognitivi nuova o diversa.

1. Nella pagina di panoramica del servizio di ricerca selezionare **skillsets**:

   ![Scheda skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Scheda skillsets")

1. Selezionare il nome del file di competenze, quindi selezionare una risorsa esistente o crearne una nuova. Selezionare **OK** per confermare le modifiche.

   ![Elenco delle risorse di competenze](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Elenco delle risorse di competenze")

   Tenere presente che l'opzione **gratuito (arricchimenti limitati)** limita la disponibilità di 20 documenti al giorno e che è possibile usare **Crea nuova risorsa Servizi cognitivi** per eseguire il provisioning di una nuova risorsa fatturabile. Se si crea una nuova risorsa, selezionare **Aggiorna** per aggiornare l'elenco delle risorse Servizi cognitivi, quindi selezionare la risorsa.

## <a name="attach-cognitive-services-programmatically"></a>Collegare Servizi cognitivi a livello di codice

Quando si definisce il set di competenze a livello di codice, aggiungere una sezione `cognitiveServices` al set di competenze. In questa sezione, includere la chiave della risorsa Servizi cognitivi che si desidera associare al proprio insieme di competenze. Tenere presente che la risorsa deve trovarsi nella stessa area della risorsa ricerca cognitiva di Azure. Aggiungere anche `@odata.type` e impostarlo su `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Il formato è indicato nel seguente esempio. Si noti la `cognitiveServices` sezione alla fine della definizione.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Esempio: stimare i costi

Per stimare i costi associati all'indicizzazione della ricerca cognitiva, iniziare con un'idea dell'aspetto di un documento medio, in modo da poter eseguire alcuni numeri. Ad esempio, è possibile approssimarsi:

+ 1.000 PDF.
+ Sei pagine ciascuna.
+ Un'immagine per pagina (6.000 immagini).
+ 3.000 caratteri per pagina.

Si supponga di avere una pipeline costituita da cracking di documenti di ogni PDF, estrazione di immagini e testo, riconoscimento ottico dei caratteri (OCR) di immagini e riconoscimento di entità delle organizzazioni.

I prezzi indicati in questo articolo sono ipotetici. Sono usati per illustrare il processo di stima. I costi potrebbero essere inferiori. Per i prezzi effettivi delle transazioni, vedere vedere [prezzi di servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Per l'individuazione di documenti con contenuto di testo e immagini, attualmente l'estrazione di testo è gratuita. Per le immagini 6.000, presumere $1 per ogni 1.000 immagini estratte. Questo è un costo di $6,00 per questo passaggio.

2. Per il riconoscimento ottico dei caratteri di 6.000 immagini in inglese, la competenza cognitiva OCR usa l'algoritmo migliore (DescribeText). Supponendo un costo di 2,50 dollari per l’analisi di 1.000 immagini, il costo raggiunto in questo passaggio sarebbe di 15 dollari.

3. Per l'estrazione di entità, si otterrebbe un totale di tre record di testo per ogni pagina. (ogni record è composto da 1.000 caratteri). Tre record di testo per pagina moltiplicati per 6.000 pagine sono uguali a 18.000 record di testo. Supponendo un prezzo di 2 dollari ogni 1.000 record di testo, in questo passaggio si raggiungerebbe un costo di 36 dollari.

Riunendola, pagherai circa $57,00 per inserire i documenti PDF 1.000 di questo tipo con le competenze descritte.

## <a name="next-steps"></a>Passaggi successivi

+ [Pagina dei prezzi di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Creare un set di competenze (REST)](/rest/api/searchservice/create-skillset)
+ [Come eseguire il mapping dei campi arricchiti](cognitive-search-output-field-mapping.md)