---
title: Proprietà dei metadati del contenuto
titleSuffix: Azure Cognitive Search
description: Le proprietà dei metadati dei documenti possono fornire contenuti ai campi in un indice di ricerca o informazioni che informano il comportamento di indicizzazione in fase di esecuzione. Questo articolo elenca le proprietà dei metadati supportate in Azure ricerca cognitiva.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668419"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Proprietà dei metadati del contenuto usate in Azure ricerca cognitiva

SharePoint Online e l'archiviazione BLOB di Azure possono contenere vari contenuti e molti di questi tipi di contenuto hanno proprietà dei metadati che possono essere utili per indicizzare. Così come è possibile creare campi di ricerca per proprietà BLOB standard **`metadata_storage_name`** , ad esempio, è possibile creare campi per le proprietà dei metadati specifiche del formato di un documento.

## <a name="supported-document-formats"></a>Formati di documento supportati

Ricerca cognitiva supporta l'indicizzazione BLOB e l'indicizzazione dei documenti di SharePoint Online per i formati di documento seguenti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Proprietà in base al formato del documento

La tabella seguente riepiloga l'elaborazione eseguita per ogni formato di documento e descrive le proprietà dei metadati estratte da un indicizzatore BLOB e dall'indicizzatore di SharePoint Online.

| Formato documento/tipo di contenuto | Metadati estratti | Dettagli elaborazione |
| --- | --- | --- |
| HTML (testo/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rimozione del markup HTML ed estrazione del testo |
| PDF (applicazione/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati (escluse le immagini) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| XML di WORD (Application/vnd. ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Rimozione del markup XML ed estrazione del testo |
| WORD 2003 XML (Application/vnd. ms-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Rimozione del markup XML ed estrazione del testo |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| XLSM (Application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| PPTM (Application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Estrae il testo, incluso il testo estratto dagli allegati. `metadata_message_to_email`, `metadata_message_cc_email` e `metadata_message_bcc_email` sono raccolte di stringhe, il resto dei campi è costituito da stringhe.|
| ODT (Application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| ODS (Application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| ODP (Application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Estrazione del testo, inclusi i documenti incorporati |
| ZIP (application/zip) |`metadata_content_type` |Estrazione del testo da tutti i documenti nell'archivio |
| GZ (applicazione/gzip) |`metadata_content_type` |Estrazione del testo da tutti i documenti nell'archivio |
| EPUB (applicazione/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Estrazione del testo da tutti i documenti nell'archivio |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Rimozione del markup XML ed estrazione del testo |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Estrazione del testo<br/>NOTA: per conoscere i dettagli su come estrarre più campi documento da un BLOB JSON, vedere [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md) |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Estrazione del testo, inclusi gli allegati |
| RTF (application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Estrazione del testo|
| Testo normale (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Estrazione del testo|

## <a name="see-also"></a>Vedi anche

* [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
* [Informazioni sui BLOB con intelligenza artificiale](search-blob-ai-integration.md)
* [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)
* [Indicizzazione di SharePoint Online](search-howto-index-sharepoint-online.md)