---
title: Apprendimento avanzato e Machine Learning
titleSuffix: Azure Machine Learning
description: Scopri in che modo Deep Learning è correlato a Machine Learning e AI. In Azure Machine Learning usare modelli di apprendimento avanzato per il rilevamento di frodi, il rilevamento di oggetti e altro ancora.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 04/12/2021
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: a9e87796c3557f21bb6192c7d7d0c0925d08d786
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311754"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Apprendimento avanzato e Machine Learning in Azure Machine Learning

Questo articolo illustra l'apprendimento avanzato e l'apprendimento automatico e il modo in cui rientrano nella categoria più ampia di intelligenza artificiale. Informazioni sulle soluzioni di apprendimento avanzato che è possibile creare in Azure Machine Learning, ad esempio il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi dei sentimenti e la previsione delle serie temporali.

Per indicazioni sulla scelta degli algoritmi per le soluzioni, vedere il foglio informativo sull' [algoritmo Machine Learning](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Deep Learning, Machine Learning e intelligenza artificiale

![Diagramma delle relazioni: intelligenza artificiale rispetto a Machine Learning e apprendimento avanzato](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Prendere in considerazione le seguenti definizioni per comprendere l'apprendimento avanzato rispetto a Machine Learning e AI:

- **Deep Learning** è un subset di machine learning basato su reti neurali artificiali. Il _processo di apprendimento_ è _profondo_ perché la struttura delle reti neurali artificiali è costituita da più livelli di input, output e nascosto. Ogni livello contiene unità che trasformano i dati di input in informazioni che il livello successivo può usare per una determinata attività predittiva. Grazie a questa struttura, un computer può apprendere tramite la propria elaborazione dei dati.

- **Machine Learning** è un subset di intelligenza artificiale che usa tecniche, ad esempio l'apprendimento avanzato, che consentono ai computer di usare l'esperienza per migliorare le attività. Il _processo di apprendimento_ è basato sui passaggi seguenti:

   1. Inserire i dati in un algoritmo. In questo passaggio è possibile fornire ulteriori informazioni al modello, ad esempio eseguendo l'estrazione delle funzionalità.
   1. Usare questi dati per eseguire il training di un modello.
   1. Testare e distribuire il modello.
   1. Utilizzare il modello distribuito per eseguire un'attività predittiva automatica. In altre parole, chiamare e utilizzare il modello distribuito per ricevere le stime restituite dal modello.

- **Intelligenza artificiale (ai)** è una tecnica che consente ai computer di simulare l'intelligenza umana. Include machine learning. 
 
Grazie alle tecniche di apprendimento automatico e apprendimento avanzato, è possibile creare sistemi di computer e applicazioni che eseguono attività comunemente associate all'intelligence umana. Queste attività includono il riconoscimento delle immagini, il riconoscimento vocale e la traduzione della lingua.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tecniche di apprendimento avanzato rispetto a Machine Learning 

Ora che si ha una panoramica di machine learning rispetto a Deep Learning, verranno confrontate le due tecniche. Nell'apprendimento automatico, è necessario che all'algoritmo venga indicato come eseguire una stima accurata utilizzando altre informazioni, ad esempio eseguendo l'estrazione delle funzionalità. Nell'apprendimento avanzato l'algoritmo può apprendere come eseguire una stima accurata tramite la propria elaborazione dei dati, grazie alla struttura di rete neurale artificiale.

Nella tabella seguente vengono confrontate le due tecniche in modo più dettagliato:

| |Tutti i computer Learning |Solo Deep Learning|
|---|---|---|
|  **Numero di punti dati** | Per eseguire stime è possibile utilizzare piccole quantità di dati. | Per eseguire stime è necessario usare grandi quantità di dati di training. |
|  **Dipendenze hardware** | Può funzionare nei computer di fascia bassa. Non è necessaria una grande quantità di potenza di calcolo. | Dipende da computer di fascia alta. Esegue intrinsecamente un numero elevato di operazioni di moltiplicazione di matrici. Una GPU può ottimizzare in modo efficiente queste operazioni. |
|  **Processo conteggi** | Richiede che le funzionalità siano identificate e create accuratamente dagli utenti. | Impara le funzionalità di alto livello dai dati e crea nuove funzionalità da sola. |
|  **Approccio alla formazione** | Divide il processo di apprendimento in passaggi più piccoli. Combina quindi i risultati di ogni passaggio in un unico output. | Consente di eseguire il processo di apprendimento risolvendo il problema in base a un'end-to-end. |
|  **Tempo di esecuzione** | Richiede tempo relativamente breve per eseguire il training, che varia da pochi secondi ad alcune ore. | Il training richiede in genere molto tempo perché un algoritmo di apprendimento avanzato comporta molti livelli. |
|  **Output** | L'output è in genere un valore numerico, ad esempio un punteggio o una classificazione. | L'output può avere più formati, ad esempio un testo, un punteggio o un suono. |

## <a name="what-is-transfer-learning"></a>Informazioni sull'apprendimento del trasferimento

Il training di modelli di apprendimento avanzato spesso richiede grandi quantità di dati di training, risorse di calcolo di fascia alta (GPU, TPU) e tempi di training più lunghi. Negli scenari in cui non è disponibile alcuna di queste informazioni, è possibile eseguire il collegamento del processo di training usando una tecnica nota come *apprendimento del trasferimento.*

Il trasferimento dell'apprendimento è una tecnica che applica le informazioni ottenute dalla risoluzione di un problema a un problema diverso ma correlato.

A causa della struttura delle reti neurali, il primo set di livelli contiene in genere funzionalità di livello inferiore, mentre il set di livelli finale contiene funzionalità di livello superiore più vicine al dominio in questione. Grazie al riutilizzo dei livelli finali da usare in un nuovo dominio o problema, è possibile ridurre significativamente il tempo, i dati e le risorse di calcolo necessari per eseguire il training del nuovo modello. Se, ad esempio, si dispone già di un modello che riconosce le automobili, è possibile reimpiegare il modello usando Transfer learning per riconoscere anche camion, motocicli e altri tipi di veicoli.

Informazioni su come applicare il trasferimento learning per la classificazione delle immagini usando un framework open source in Azure Machine Learning: eseguire [il training di un modello PyTorch di apprendimento avanzato usando il trasferimento di formazione](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Casi d'uso di Deep Learning

Grazie alla struttura di rete neurale artificiale, l'apprendimento avanzato eccelle nell'identificare i modelli nei dati non strutturati, ad esempio immagini, audio, video e testo. Per questo motivo, l'apprendimento avanzato sta trasformando rapidamente molti settori, tra cui Healthcare, energia, finanza e trasporto. Questi settori ora ripensano i tradizionali processi aziendali. 

Alcune delle applicazioni più comuni per l'apprendimento approfondito sono descritte nei paragrafi seguenti. In Azure Machine Learning, è possibile usare un modello da compilare da un framework open source o compilare il modello usando gli strumenti forniti.

### <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

Il riconoscimento delle entità denominate è un metodo di apprendimento avanzato che accetta una porzione di testo come input e lo trasforma in una classe già specificata. Queste nuove informazioni possono essere un codice postale, una data, un ID prodotto. Le informazioni possono quindi essere archiviate in uno schema strutturato per creare un elenco di indirizzi o fungere da benchmark per un motore di convalida delle identità.

### <a name="object-detection"></a>Rilevamento di oggetti

L'apprendimento avanzato è stato applicato in molti casi d'uso di rilevamento oggetti. Il rilevamento degli oggetti è costituito da due parti: classificazione delle immagini e quindi localizzazione delle immagini. _Classificazione_ immagini identifica gli oggetti dell'immagine, ad esempio automobili o persone. La _localizzazione_ delle immagini fornisce la posizione specifica di questi oggetti. 

Il rilevamento degli oggetti è già in uso in settori quali giochi, vendite al dettaglio, turismo e auto autoguidate.

### <a name="image-caption-generation"></a>Generazione didascalia immagine

Come il riconoscimento delle immagini, nel didascalia delle immagini, per una determinata immagine, il sistema deve generare una didascalia che descrive il contenuto dell'immagine. Quando è possibile rilevare ed etichettare oggetti nelle fotografie, il passaggio successivo consiste nel trasformare tali etichette in frasi descrittive. 

In genere, le applicazioni per la didascalia delle immagini usano reti neurali di convoluzione per identificare gli oggetti in un'immagine e quindi usare una rete neurale ricorrente per trasformare le etichette in frasi coerenti.

### <a name="machine-translation"></a>Traduzione automatica

La traduzione automatica accetta parole o frasi da una lingua e le converte automaticamente in un'altra lingua. La traduzione automatica è rimasta da molto tempo, ma l'apprendimento avanzato raggiunge risultati impressionanti in due aree specifiche: la traduzione automatica del testo (e la traduzione di testo vocale) e la traduzione automatica delle immagini.

Con la trasformazione dei dati appropriata, una rete neurale può comprendere il testo, l'audio e i segnali visivi. La traduzione automatica può essere usata per identificare frammenti di suoni in file audio di dimensioni maggiori e per trascrivere la parola o l'immagine pronunciata come testo.

### <a name="text-analytics"></a>Analisi del testo

L'analisi del testo basata su metodi di apprendimento avanzato comporta l'analisi di grandi quantità di dati di testo (ad esempio, documenti medici o ricevute di spese), il riconoscimento dei modelli e la creazione di informazioni organizzate e concise.

Le aziende usano l'apprendimento avanzato per eseguire analisi del testo per rilevare il trading di insider e la conformità alle normative governative. Un altro esempio comune è la frode delle assicurazioni: l'analisi del testo è stata spesso usata per analizzare grandi quantità di documenti per riconoscere le probabilità che un reclamo assicurativo sia illecito.

## <a name="artificial-neural-networks"></a>Reti neurali artificiali

Le reti neurali artificiali sono costituite da livelli di nodi connessi. I modelli di apprendimento avanzato utilizzano reti neurali con un numero elevato di livelli. 

Le sezioni seguenti illustrano le più diffuse tipologie di reti neurali artificiali.

### <a name="feedforward-neural-network"></a>Rete neurale feedforward

La rete neurale feedforward è il tipo più semplice di rete neurale artificiale. In una rete feedforward, le informazioni si spostano in una sola direzione dal livello di input al livello di output. Feedforward neural network trasforma un input inserendolo in una serie di livelli nascosti. Ogni livello è costituito da un set di neuroni e ogni livello è completamente connesso a tutti i neuroni nel livello precedente. L'ultimo livello completamente connesso, ovvero il livello di output, rappresenta le stime generate.

### <a name="recurrent-neural-network-rnn"></a>Rete neurale ricorrente (RNN)

Le reti neurali ricorrenti sono una rete neurale artificiale ampiamente utilizzata. Queste reti salvano l'output di un livello e lo reinviano al livello di input per consentire la stima del risultato del livello. Le reti neurali ricorrenti hanno ottime capacità di apprendimento. Sono ampiamente usati per attività complesse come la previsione delle serie temporali, la grafia di apprendimento e il riconoscimento del linguaggio.

### <a name="convolutional-neural-network-cnn"></a>Rete neurale di convoluzione (CNN)

Una rete neurale di convoluzione è una rete neurale artificiale particolarmente efficace e presenta un'architettura univoca. I livelli sono organizzati in tre dimensioni: larghezza, altezza e profondità. I neuroni di un livello non si connettono a tutti i neuroni nel livello successivo, ma solo a una piccola area dei neuroni del livello. L'output finale viene ridotto a un singolo vettore di punteggi di probabilità, organizzati lungo la dimensione di profondità. 

Le reti neurali di convoluzione sono state usate in aree quali il riconoscimento video, il riconoscimento delle immagini e i sistemi di raccomandazione.

### <a name="generative-adversarial-network-gan"></a>Rete antagonista generativa (GAN)

Le reti antagoniste generative sono modelli generativi formati per creare contenuti realistici, ad esempio immagini. È costituito da due reti note come generatore e discriminatore. Entrambe le reti vengono sottoposte a training simultaneamente. Durante il training, il generatore utilizza un rumore casuale per creare nuovi dati sintetici che somigliano strettamente ai dati reali. Il discriminatore preleva l'output dal generatore come input e utilizza dati reali per determinare se il contenuto generato è reale o sintetico. Ogni rete è in competizione tra loro. Il generatore sta provando a generare contenuto sintetico non distinguibile dal contenuto reale e il discriminatore sta provando a classificare correttamente gli input come reali o sintetici. L'output viene quindi usato per aggiornare i pesi di entrambe le reti per aiutarli a raggiungere meglio i rispettivi obiettivi.

Le reti antagoniste generative vengono usate per risolvere i problemi, ad esempio la conversione di immagini e la progressione dell'età.

### <a name="transformers"></a>Convertitori

I trasformatori sono un'architettura di modello adatta per la risoluzione di problemi contenenti sequenze come testo o dati di serie temporali. Sono costituiti da [livelli codificatore e decodificatore](https://en.wikipedia.org/wiki/Transformer_(machine_learning_model)#Encoder). Il codificatore accetta un input e ne esegue il mapping a una rappresentazione numerica che contiene informazioni quali context. Il decodificatore usa le informazioni del codificatore per produrre un output, ad esempio il testo tradotto. Ciò che rende le trasformazioni diverse dalle altre architetture che contengono codificatori e decodificatori sono i sottolivelli di attenzione. L'attenzione è l'idea di concentrarsi su parti specifiche di un input in base all'importanza del contesto in relazione ad altri input in una sequenza. Ad esempio, quando si riepiloga un articolo di notizie, non tutte le frasi sono rilevanti per descrivere l'idea principale. Concentrandosi sulle parole chiave in tutto l'articolo, il riepilogo può essere eseguito in una singola frase, ovvero nel titolo.

I trasformatori sono stati usati per risolvere i problemi di elaborazione del linguaggio naturale, ad esempio la traduzione, la generazione di testo, la risposta alle domande e il riepilogo del testo.

Di seguito sono riportate alcune implementazioni di trasformatori ben note:

- Rappresentazioni del codificatore bidirezionale da transformatori (BERT)
- Transformer pre-training generativa 2 (GPT-2)
- Transformer pre-training generatore 3 (GPT-3)

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano altre opzioni per l'uso di modelli di apprendimento avanzato open source in [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Classificare le cifre scritte a mano usando un modello TensorFlow](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Classificare le cifre scritte a mano usando uno strumento di stima TensorFlow e keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Classificare le cifre scritte a mano usando un modello Chainer](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
