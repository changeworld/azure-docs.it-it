---
title: Che cos'è la traduzione di documenti?
description: Panoramica del servizio e del processo di traduzione dei documenti batch basati sul cloud.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968170"
---
# <a name="what-is-document-translation-preview"></a>Che cos'è la traduzione di documenti (anteprima)?

La traduzione dei documenti è una funzionalità basata sul cloud del servizio di [conversione di Azure](../translator-info-overview.md) ed è parte della famiglia di servizi cognitivi di Azure delle API REST. L'API traduzione documenti converte i documenti da e verso 90 lingue e dialetti, conservando al tempo stesso la struttura del documento e il formato dei dati.

Questa documentazione contiene i tipi di articolo seguenti:  

* Nelle [**guide introduttive**](get-started-with-document-translation.md) vengono fornite istruzioni introduttive per l'esecuzione di richieste al servizio.
* Le [**guide alle procedure**](create-sas-tokens.md) contengono istruzioni per l'uso della funzionalità in modi più specifici o personalizzati.  

## <a name="document-translation-key-features"></a>Funzionalità chiave di traduzione documenti

| Funzionalità | Descrizione |
| ---------| -------------|
| **Traduci file di grandi dimensioni**| Tradurre i documenti interi in modo asincrono.|
|**Tradurre numerosi file**|Tradurre più file da e verso 90 lingue e dialetti.|
|**Mantieni presentazione file di origine**| Tradurre i file conservando il layout e il formato originali.|
|**Applicare la traduzione personalizzata**| Tradurre i documenti usando modelli di traduzione generali e [personalizzati](../customization.md#custom-translator) .|
|**Applicare glossari personalizzati**|Tradurre i documenti usando glossari personalizzati.|

## <a name="how-to-get-started"></a>Come iniziare

Nella Guida dettagliata verrà illustrato come iniziare rapidamente a utilizzare il convertitore di documenti. Per iniziare, è necessario un [account Azure](https://azure.microsoft.com/free/cognitive-services/)attivo.  Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Operazioni preliminari](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formati di documento supportati

I tipi di file di documento seguenti sono supportati dalla conversione dei documenti:

| Tipo file| Estensione file|Descrizione|
|---|---|--|
|Adobe PDF|pdf|Formato di documento portatile Adobe Acrobat|
|HTML|.html|Linguaggio di markup Hyper-Text.|
|Formato del file di interscambio di localizzazione|XLF. , XLIFF| Formato di documento parallelo, esportazione di sistemi di memoria di traduzione. Le lingue usate vengono definite all'interno del file.|
|Microsoft Excel|xlsx|Un file di foglio di calcolo per l'analisi dei dati e la documentazione.|
|Microsoft Outlook|msg|Un messaggio di posta elettronica creato o salvato in Microsoft Outlook.|
|Microsoft PowerPoint|pptx| Un file di presentazione usato per visualizzare il contenuto in un formato di presentazione.|
|Microsoft Word|docx| File di documento di testo.|
|SCHEDA/valori delimitati da tabulazioni|. TSV/. Tab| Un file di dati non elaborati delimitati da tabulazioni utilizzato dai programmi di fogli di calcolo.|
|Testo|.txt| Documento di testo non formattato.|

## <a name="supported-glossary-formats"></a>Formati di glossario supportati

I seguenti tipi di file di glossario sono supportati dalla conversione dei documenti:

| Tipo file| Estensione file|Descrizione|
|---|---|--|
|Formato del file di interscambio di localizzazione|XLF. , XLIFF| Formato di documento parallelo, esportazione di sistemi di memoria di traduzione. Le lingue usate vengono definite all'interno del file.|
|SCHEDA/valori delimitati da tabulazioni|. TSV/. Tab| Un file di dati non elaborati delimitati da tabulazioni utilizzato dai programmi di fogli di calcolo.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](get-started-with-document-translation.md)
