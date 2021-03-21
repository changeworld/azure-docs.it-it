---
title: 'ML Studio (classico): eseguire la migrazione a Azure Machine Learning'
description: Eseguire la migrazione da studio (classico) a Azure Machine Learning per una piattaforma di data science modernizzata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565113"
---
# <a name="migrate-to-azure-machine-learning"></a>Eseguire la migrazione ad Azure Machine Learning

Questo articolo illustra come eseguire la migrazione di risorse di studio (classiche) a Azure Machine Learning. Al momento, per eseguire la migrazione delle risorse, è necessario ricompilare manualmente gli esperimenti.

Azure Machine Learning offre una piattaforma di data science modernizzata che combina approcci senza codice e con Code-First. Per ulteriori informazioni sulle differenze tra studio (classico) e Azure Machine Learning, vedere la sezione relativa alla [valutazione Azure Machine Learning](#step-1-assess-azure-machine-learning) .


## <a name="recommended-approach"></a>Approccio consigliato

Per eseguire la migrazione a Azure Machine Learning, è consigliabile usare l'approccio seguente:

> [!div class="checklist"]
> * Passaggio 1: valutare Azure Machine Learning
> * Passaggio 2: creare un piano di migrazione
> * Passaggio 3: ricompilare esperimenti e servizi Web
> * Passaggio 4: integrare le app client
> * Passaggio 5: pulire le risorse di studio (classiche)


## <a name="step-1-assess-azure-machine-learning"></a>Passaggio 1: valutare Azure Machine Learning
1. Informazioni sulle [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); vantaggi, costi e architettura.

1. Confrontare le funzionalità di Azure Machine Learning e studio (classico).

    >[!NOTE]
    > La funzionalità di **progettazione** di Azure Machine Learning offre un'esperienza di trascinamento della selezione simile a Studio (classica). Tuttavia, Azure Machine Learning fornisce anche [flussi di lavoro Code-First](../concept-model-management-and-deployment.md) affidabili come alternativa. Questa serie di migrazione è incentrata sulla finestra di progettazione, poiché è più simile all'esperienza Studio (classica).

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Verificare che i moduli critici di studio (classico) siano supportati in Azure Machine Learning Designer. Per ulteriori informazioni, vedere la tabella di [mapping dei moduli (classica) e di progettazione](#studio-classic-and-designer-module-mapping) .

4. [Creare un'area di lavoro di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>Passaggio 2: creare un piano di migrazione

1. Identificare i **set di dati**, i **modelli** e i **servizi Web** di studio (classico) di cui si desidera eseguire la migrazione.

1. Determinare l'effetto della migrazione sulla propria azienda.

1. Creare un piano di migrazione.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Passaggio 3: ricompilare esperimenti e servizi Web

1. [Eseguire la migrazione dei set di impostazioni a Azure Machine Learning](migrate-register-dataset.md).
1. Usare la finestra di progettazione per [ricompilare gli esperimenti](migrate-rebuild-experiment.md).
1. Utilizzare la finestra di progettazione per [ridistribuire i servizi Web](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning supporta anche flussi di lavoro Code-First per [set di impostazioni](../how-to-create-register-datasets.md), [Training](../how-to-set-up-training-targets.md)e [distribuzione](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Passaggio 4: integrare le app client

1. Modificare le applicazioni client che richiamano i servizi Web di studio (classico) per usare i nuovi [endpoint Azure Machine Learning](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Passaggio 5: pulire le risorse di studio (classiche)

1. [Pulisci le risorse di studio (classiche)](export-delete-personal-data-dsr.md) per evitare addebiti aggiuntivi. Potrebbe essere necessario mantenere gli asset per il fallback fino a quando non vengono convalidati i carichi di lavoro Azure Machine Learning.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (classico) e modulo di progettazione-mapping

Consultare la tabella seguente per visualizzare i moduli da usare durante la ricompilazione degli esperimenti di studio (classico) nella finestra di progettazione.


> [!IMPORTANT]
> La finestra di progettazione implementa i moduli tramite pacchetti Python Open Source anziché pacchetti C# come studio (classico). A causa di questa differenza, l'output dei moduli della finestra di progettazione può variare leggermente rispetto alle relative controparti Studio (classiche).


|Category|Modulo Studio (classico)|Modulo di progettazione sostitutivo|
|--------------|----------------|--------------------------------------|
|Input e output dei dati|-Immettere manualmente i dati </br> -Esporta dati </br> -Importa dati </br> -Modello con training di carico </br> -Decomprimere i set di impostazioni compressi|-Immettere manualmente i dati </br> -Esporta dati </br> -Importa dati|
|Conversioni tra formati di dati|-Converti in CSV </br> -Converti in DataSet </br> -Converti in ARFF </br> -Converti in SVMLight </br> -Conversione in TSV|-Converti in CSV </br> -Converti in DataSet|
|Trasformazione dei dati-manipolazione|-Aggiungi colonne</br> -Aggiungi righe </br> -Applica trasformazione SQL </br> -Pulizia dei dati mancanti </br> -Converti in valori indicatore </br> -Modifica metadati </br> -Unisci dati </br> -Rimuovi righe duplicate </br> -Selezionare le colonne nel set di dati </br> -Selezione trasformazione colonne </br> -PERCOSse </br> -Raggruppa i valori categorici|-Aggiungi colonne</br> -Aggiungi righe </br> -Applica trasformazione SQL </br> -Pulizia dei dati mancanti </br> -Converti in valori indicatore </br> -Modifica metadati </br> -Unisci dati </br> -Rimuovi righe duplicate </br> -Selezionare le colonne nel set di dati </br> -Selezione trasformazione colonne </br> -PERCOSse|
|Trasformazione dei dati: scalabilità e riduzione |-Ritagliare i valori </br> -Raggruppare i dati in contenitori </br> -Normalizzare i dati </br>-Analisi componenti principali |-Ritagliare i valori </br> -Raggruppare i dati in contenitori </br> -Normalizzare i dati|
|Trasformazione dei dati: campionamento e suddivisione|-Partition ed Sample </br> -Suddividere i dati|-Partition ed Sample </br> -Suddividere i dati|
|Trasformazione dei dati: filtro |-Applica filtro </br> -Filtro FIR </br> -IIR filtro </br> -Filtro mediano </br> -Filtro media mobili </br> -Filtro di soglia </br> -Filtro definito dall'utente||
|Trasformazione dei dati: apprendimento con conteggi |-Trasformazione Conteggio compilazione </br> -Esporta tabella di conteggio </br> -Importa tabella di conteggio </br> -Trasformazione Conteggio merge</br>  -Modificare i parametri della tabella di conteggio||
|Selezione caratteristiche |-Selezione delle funzioni basata su filtro </br> -Analisi discriminante lineare di Fisher  </br> -Importanza della funzionalità di permutazione |-Selezione delle funzioni basata su filtro </br>  -Importanza della funzionalità di permutazione|
| Classificazione modello| -Foresta delle decisioni multiclasse </br> -Jungle decisionale multiclasse  </br> -Regressione logistica multiclasse  </br>-Rete neurale multiclasse  </br>-One-vs-All Multiclass </br>-Two-Class valore medio di perceptron </br>-Two-Class Bayes Point Machine </br>-Two-Class albero delle decisioni con boosting  </br> -Two-Class foresta delle decisioni  </br> -Giungla delle decisioni Two-Class  </br> -Two-Class Locally-Deep SVM </br> -Two-Class regressione logistica  </br> -Two-Class rete neurale </br> -Two-Class macchina a vettori di supporto  | -Foresta delle decisioni multiclasse </br>  -Albero delle decisioni di potenziamento multiclasse  </br> -Regressione logistica multiclasse </br> -Rete neurale multiclasse </br> -One-vs-All Multiclass  </br> -Two-Class valore medio di perceptron  </br> -Two-Class albero delle decisioni con boosting  </br> -Two-Class foresta delle decisioni </br>-Two-Class regressione logistica </br> -Two-Class rete neurale </br>-Two-Class macchina a vettori di supporto  |
| Modello-clustering| -K-means clustering| -K-means clustering|
| Regressione modello| -Regressione lineare Bayes  </br> -Regressione dell'albero delle decisioni con boosting  </br>-Regressione della foresta delle decisioni  </br> -Regressione quantile foresta rapida  </br> -Regressione lineare </br> -Regressione di rete neurale </br> -Regressione ordinale di Poisson regressione| -Regressione dell'albero delle decisioni con boosting  </br>-Regressione della foresta delle decisioni  </br> -Regressione quantile foresta rapida </br> -Regressione lineare  </br> -Regressione di rete neurale </br> -Regressione di Poisson|
| Modello: rilevamento anomalie| -One-Class SVM  </br> -Rilevamento anomalie PCA-Based | -Rilevamento anomalie PCA-Based|
| Machine Learning-valutazione  | -Cross Validate Model  </br>-Valutare il modello  </br>-Valuta la raccomandazione | -Cross Validate Model  </br>-Valutare il modello </br> -Valuta la raccomandazione|
| Machine Learning: eseguire il training| -Sweep clustering  </br> -Training del modello di rilevamento delle anomalie </br>-Modello di clustering di training  </br> -Train conferice Recommender-</br> Eseguire il training del modello  </br>-Ottimizzare gli iperparametri del modello| -Training del modello di rilevamento delle anomalie  </br> -Modello di clustering di training </br> -Train Model-</br> -Eseguire il training del modello PyTorch  </br>-Train SVD Recommender  </br>-Eseguire il training di un'ampia gamma di suggerimenti </br>-Ottimizzare gli iperparametri del modello|
| Machine Learning: Punteggio| -Applica trasformazione  </br>-Assegnare i dati ai cluster  </br>-Punteggio della riassegnazione di fiammifero </br> -Assegnare un punteggio al modello|-Applica trasformazione  </br> -Assegnare i dati ai cluster </br> -Assegnare un punteggio al modello di immagine  </br> -Assegnare un punteggio al modello </br>-Score SVD Recommender </br> -Assegnare un punteggio a un Consiglio più ampio|
| Moduli della libreria OpenCV| -Importa immagini </br>-Classificazione immagini a cascata con training preliminare | |
| Moduli del linguaggio Python| -Eseguire lo script Python| -Eseguire lo script Python  </br> -Creare un modello Python |
| Moduli del linguaggio R  | -Eseguire uno script R  </br> -Creare un modello R| -Eseguire uno script R|
| Funzioni di statistiche | -Applica operazione matematica </br>-Calcolo delle statistiche Elementary  </br>-Calcolo della correlazione lineare  </br>-Evaluate-funzione di probabilità  </br>-Sostituisci valori discreti  </br>-Riepiloga dati  </br>-Ipotesi di test con t-test| -Applica operazione matematica  </br>-Riepiloga dati|
| Analisi del testo| -Rileva lingue  </br>-Estrai frasi chiave da testo  </br>-Estrai le funzionalità di N-Gram dal testo  </br>-Hashing delle funzionalità </br>-Allocazione Dirichlet latente  </br>-Riconoscimento entità denominato </br>-Pre-elabora testo  </br>-Score Vowpal Wabbit modello versione 7-10  </br>-Score Vowpal Wabbit versione 8 </br>-Train Vowpal Wabbit modello versione 7-10  </br>-Train Vowpal Wabbit versione 8 |-Converti parola in vettore </br> -Estrai le funzionalità di N-Gram dal testo </br>-Hashing delle funzionalità  </br>-Allocazione Dirichlet latente </br>-Pre-elabora testo  </br>-Assegnare un punteggio al modello Wabbit di Vowpal </br> -Train Vowpal Wabbit Model|
| Serie temporali| -Rilevamento anomalie della serie temporale | |
| Servizio Web | -Input </br> -Output | -Input </br>  - Output|
| Visione artificiale| | -Applica trasformazione immagine </br> -Converti in directory immagine </br> -Trasformazione immagine init </br> -Directory immagine divisa  </br> -Classificazione delle immagini DenseNet   </br>-Classificazione delle immagini ResNet |

Per ulteriori informazioni sull'utilizzo dei singoli moduli di progettazione, vedere il [riferimento al modulo di progettazione](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>Che cosa accade se manca un modulo della finestra di progettazione?

Azure Machine Learning Designer contiene i moduli più diffusi di studio (classico). Include anche nuovi moduli che sfruttano le tecniche di apprendimento automatico più recenti. 

Se la migrazione è bloccata a causa dei moduli mancanti nella finestra di progettazione, contattare Microsoft [creando un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Migrazione di esempio

La migrazione dell'esperimento seguente evidenzia alcune differenze tra studio (classico) e Azure Machine Learning.

### <a name="datasets"></a>Set di dati

In studio (classico), i **set di impostazioni** sono stati salvati nell'area di lavoro e possono essere usati solo da studio (classico).

![automobile-Price-Classic-DataSet](./media/migrate-overview/studio-classic-dataset.png)

In Azure Machine Learning i **set di impostazioni** sono registrati nell'area di lavoro e possono essere utilizzati in tutti i Azure Machine Learning. Per altre informazioni sui vantaggi dei set di dati Azure Machine Learning, vedere [proteggere l'accesso ai dati](../concept-data.md#reference-data-in-storage-with-datasets).

![automobile-prezzo-AML-DataSet](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pipeline

In studio (classico), gli **esperimenti** contenevano la logica di elaborazione per il lavoro. Sono stati creati esperimenti con i moduli di trascinamento della selezione.


![automobile-prezzo-classico-esperimento](./media/migrate-overview/studio-classic-experiment.png)

In Azure Machine Learning le **pipeline** contengono la logica di elaborazione per il lavoro. È possibile creare pipeline con moduli di trascinamento della selezione o scrivendo codice.

![automobile-prezzo-AML-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Endpoint servizio Web

In studio (classico), l' **API request/Rispondi** è stata usata per la stima in tempo reale. L' **API di esecuzione batch** è stata usata per la stima o la ripetizione del training in batch.

![automobile-Price-Classic-WebService](./media/migrate-overview/studio-classic-web-service.png)

In Azure Machine Learning gli **endpoint in tempo reale** vengono utilizzati per la stima in tempo reale. Gli **endpoint della pipeline** vengono utilizzati per la stima o la ripetizione del training in batch.

![automobile-Price-AML-endpoint](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi i requisiti di alto livello per la migrazione a Azure Machine Learning. Per i passaggi dettagliati, vedere gli altri articoli della serie di migrazione Studio (classica):

1. **Panoramica sulla migrazione**.
1. [Eseguire la migrazione del set di dati](migrate-register-dataset.md).
1. [Ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).
1. [Ricompilare un servizio Web di studio (classico)](migrate-rebuild-web-service.md).
1. [Integrare un servizio web Azure Machine Learning con le app client](migrate-rebuild-integrate-with-client-app.md).
1. [Migrare Execute R script](migrate-execute-r-script.md).






