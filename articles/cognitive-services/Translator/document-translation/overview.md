---
title: Che cos'è la traduzione di documenti?
description: Panoramica del servizio e del processo di traduzione dei documenti batch basati sul cloud.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 255de7d6739691c479e1ae98e37bc6d3e01a6710
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100643632"
---
# <a name="what-is-document-translation-preview"></a>Che cos'è la traduzione di documenti (anteprima)?

La traduzione dei documenti è una funzionalità basata sul cloud del servizio di [conversione di Azure](../translator-info-overview.md) ed è parte della famiglia di servizi cognitivi di Azure delle API REST. L'API traduzione documenti converte i documenti da e verso più di 70 lingue, conservando al tempo stesso il formato dei dati e la struttura del documento.

## <a name="document-translation-key-features"></a>Funzionalità chiave di traduzione documenti

| Funzionalità | Descrizione |
| ---------| -------------|
| **Traduci file di grandi dimensioni**| Tradurre i documenti interi in modo asincrono.|
|**Tradurre numerosi file**|Convertire più file da e verso più di 70 lingue.|
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
|SCHEDA/valori delimitati da tabulazioni|. TSV/. Tab| un file di dati non elaborati delimitati da tabulazioni utilizzato dai programmi di fogli di calcolo.|
|Testo|.txt| Documento di testo non formattato.|
|Scambio di memoria di traduzione|. TMX|Uno standard XML aperto usato per lo scambio di dati di memoria di traduzione (TM) creati dalle applicazioni di localizzazione e di traduzione tramite computer.|

## <a name="supported-glossary-formats"></a>Formati di glossario supportati

I seguenti tipi di file di glossario sono supportati dalla conversione dei documenti:

| Tipo file| Estensione file|Descrizione|
|---|---|--|
|Formato del file di interscambio di localizzazione|XLF. , XLIFF| Formato di documento parallelo, esportazione di sistemi di memoria di traduzione. Le lingue usate vengono definite all'interno del file.|
|SCHEDA/valori delimitati da tabulazioni|. TSV/. Tab| un file di dati non elaborati delimitati da tabulazioni utilizzato dai programmi di fogli di calcolo.|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](get-started-with-document-translation.md)
>
>