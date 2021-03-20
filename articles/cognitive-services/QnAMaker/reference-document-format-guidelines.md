---
title: Importare le linee guida per il formato del documento-QnA Maker
description: Usare queste linee guida per importare i documenti per ottenere i risultati migliori per il contenuto.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549542"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Linee guida per il formato di documenti e URL importati

Esaminare queste linee guida per la formattazione per ottenere i risultati migliori per il contenuto.

## <a name="formatting-considerations"></a>Considerazioni di formattazione

Dopo aver importato un file o un URL, QnA Maker converte e archivia il contenuto nel [formato Markdown](https://en.wikipedia.org/wiki/Markdown). Il processo di conversione aggiunge nuove righe nel testo, ad esempio `\n\n` . Una conoscenza del formato Markdown consente di comprendere il contenuto convertito e gestire il contenuto della Knowledge base.

Se si aggiunge o modifica il contenuto direttamente nella Knowledge base, utilizzare la **formattazione Markdown** per creare contenuto RTF o modificare il contenuto del formato Markdown già presente nella risposta. QnA Maker supporta gran parte del formato Markdown per fornire funzionalità di testo avanzate ai contenuti. Tuttavia, l'applicazione client, ad esempio un bot di chat, potrebbe non supportare lo stesso set di formati Markdown. È importante testare la visualizzazione delle risposte dell'applicazione client.

Vedere un elenco completo di [tipi di contenuto ed esempi](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Formattazione di documenti di base

QnA Maker identifica le sezioni e le sottosezioni e le relazioni nel file in base a indizi visivi come:

* Dimensioni carattere
* stile carattere
* numerazione
* colori

> [!NOTE]
> Attualmente non è supportata l'estrazione di immagini da documenti caricati.

### <a name="product-manuals"></a>Manuali di prodotti

Un manuale è in genere materiale di riferimento fornito insieme a un prodotto. Consente all'utente di configurare, usare e gestire un prodotto e di risolverne i problemi. Quando QnA Maker elabora un manuale, estrae i titoli e sottotitoli come domande e il contenuto successivo come risposte. Vedere un esempio [qui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Di seguito è riportato un esempio di un manuale con una pagina di indice e con contenuto gerarchico

 ![Esempio di manuale del prodotto per una knowledge base](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> L'estrazione funziona al meglio con i manuali che hanno un sommario e/o una pagina di indice e una struttura chiara con titoli gerarchici.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochure, linee guida, documenti e altri file

Possono essere elaborati anche molti altri tipi di documento in modo che possano generare coppie di controllo qualità, purché essi dispongano di una struttura e di un layout chiari. Sono inclusi: brochure, linee guida, report, white paper, articoli scientifici, criteri, libri e così via. Vedere un esempio [qui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Di seguito è riportato un esempio di documento semistrutturato, senza un indice:

 ![Documento semistrutturato di Archiviazione Blob di Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento domanda-risposta strutturato

Il formato per documenti domanda-risposta strutturati in file con estensione DOC consiste nell'alternanza di domande e risposte per ogni linea, una domanda per ogni linea seguita dalla relativa risposta nella linea seguente, come illustrato di seguito:

```text
Question1

Answer1

Question2

Answer2
```

Di seguito è riportato un esempio di un documento di Word di domanda-risposta strutturato:

 ![Esempio di documento domanda-risposta strutturato per una knowledge base](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>File *TXT*, *TSV* e *XLS* strutturati

Anche i documenti domanda-risposta sotto forma di file *.txt*, *.tsv* oppure *.xls* strutturati possono essere caricati da QnA Maker per creare o estendere una knowledge base.  Questi file possono essere testo normale, o possono disporre di contenuto in formato RTF o HTML.

| Domanda  | Risposta  | Metadati (1 chiave: 1 valore) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Qualsiasi colonna aggiuntiva nel file di origine viene ignorata.

#### <a name="example-of-structured-excel-file"></a>Esempio di file di Excel strutturato

Di seguito è riportato un esempio di file *.xls* domanda-risposta strutturato, con contenuto HTML:

 ![Esempio di file Excel domanda-risposta strutturato per una knowledge base](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Esempio di domande alternative per una singola risposta in un file di Excel

Di seguito è riportato un esempio di un file QnA *. xls* strutturato, con diverse domande alternative per una singola risposta:

 ![Esempio di domande alternative per una singola risposta in un file di Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Una volta importato il file, la coppia di domande e risposte si trova nella Knowledge base, come illustrato di seguito:

 ![Screenshot delle domande alternative per la singola risposta importata nella Knowledge base](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione

L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file TSV strutturato che contiene informazioni sull'origine dei dati. Queste informazioni consentono a QnA Maker di raggruppare le coppie domanda/risposta e di attribuirle a una specifica origine dati.

| Domanda  | Risposta  | Source (Sorgente)| Metadati (1 chiave: 1 valore) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Formattazione del documento a più turni

* Usare le intestazioni e le sottointestazioni per indicare la gerarchia. Ad esempio, è possibile denotare il QnA padre e H2 per indicare la QnA che deve essere eseguita come richiesta. Utilizzare dimensioni di intestazione ridotte per indicare la gerarchia successiva. Non usare lo stile, il colore o un altro meccanismo per implicare la struttura nel documento, QnA Maker non estrae i prompt a più turni.
* Il primo carattere dell'intestazione deve essere in maiuscolo.
* Non terminare un'intestazione con un punto interrogativo, `?` .

**Documenti di esempio**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Vantaggi di Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Vantaggi di Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>Indirizzo Web di domande frequenti

QnA Maker può supportare tre diversi formati di pagine Web di domande frequenti:

* Pagine semplici di domande frequenti
* Pagina di domande frequenti con collegamenti
* Pagine di domande frequenti con una Home page degli argomenti

### <a name="plain-faq-pages"></a>Pagine semplici di domande frequenti

Si tratta del tipo più comune di pagina di domande frequenti, in cui le risposte seguono immediatamente le domande nella stessa pagina.

Di seguito è riportato un esempio di pagina semplice di domande frequenti:

![Esempio di pagina semplice di domande frequenti per una knowledge base](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Pagina di domande frequenti con collegamenti

In questo tipo di pagina di domande frequenti, le domande sono aggregate e collegate a risposte che si trovano in diverse sezioni della stessa pagina o in pagine differenti.

Di seguito è riportato un esempio di pagina di domande frequenti con collegamenti a sezioni che si trovano nella stessa pagina:

 ![Esempio di pagina di domande frequenti con collegamenti a sezioni per una knowledge base](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Pagina argomenti padre collegamenti a pagine di risposte figlio

Questo tipo di domande frequenti include una pagina di argomenti in cui ogni argomento è collegato a un set di domande e risposte corrispondente in una pagina diversa. QnA Maker esegue la ricerca per indicizzazione di tutte le pagine collegate per estrarre le domande corrispondenti & risposte.

Di seguito è riportato un esempio di una pagina di argomenti con collegamenti a sezioni di domande frequenti in pagine diverse.

 ![Esempio di pagina di domande frequenti con collegamenti diretti per una knowledge base](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>URL di supporto

QnA Maker è in grado di elaborare pagine web di supporto semi-strutturatee, ad esempio articoli web che descrivono come eseguire una determinata attività, come diagnosticare e risolvere un problema specifico e quali sono le procedure consigliate per un determinato processo. Il procedimento di estrazione funziona meglio su contenuti che presentano una struttura chiara con intestazioni gerarchiche.

> [!NOTE]
> L'estrazione per gli articoli di supporto è una nuova funzionalità, nelle sue prime fasi. Funziona al meglio con pagine semplici, ben strutturate e che non contengono intestazioni o piè di pagina complessi.

![QnA Maker supporta l'estrazione da pagine web semi-strutturate che presentano una struttura chiara con intestazioni gerarchiche.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Importazione ed esportazione della Knowledge base

I **file TSV e XLS**, dalle Knowledge base esportate, possono essere usati solo importando i file dalla pagina **Impostazioni** nel portale di QnA Maker. Non possono essere utilizzati come origini dati durante la creazione della Knowledge base o nella funzionalità **+ Aggiungi file** o **Aggiungi URL** nella pagina **Impostazioni** . 

Quando si importa la Knowledge base tramite questi **file TSV e XLS**, le coppie QnA vengono aggiunte all'origine editoriale e non alle origini da cui sono stati estratti i QnAs nella Knowledge base esportata. 


## <a name="next-steps"></a>Passaggi successivi

Vedere un elenco completo di [tipi di contenuto ed esempi](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)
